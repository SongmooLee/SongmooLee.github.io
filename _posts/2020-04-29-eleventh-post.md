---
title: "스터디 7주차-2"
date: 2020-04-29
categories: Study
---

## 바인더 코드 분석

### 바인더 드라이버 소스 구조

<br>.drivers/staging/android/binder.c 파일이 실제 바인더 드라이버 파일.

```
static const struct file_operations binder_fops = {
.owner = THIS_MODULE,
.poll = binder_poll,
.unlocked_ioctl = binder_ioctl,
.mmap = binder_mmap,
.open = binder_open,
.flush = binder_flush,
.release = binder_release,
};


static struct miscdevice binder_miscdev = {
.minor = MISC_DYNAMIC_MINOR,
.name = "binder",
.fops = &binder_fops
};

binder_init() {
 ...
 ret = misc_register(&binder_miscdev);
}
```

위 코드는 실제 바인더 드라이버의 초기화 과정이다. 즉, "/dev/binder" 는 miscdevice 로서 초기화되고, binder_fops의 함수들이 통신에서 사용된다.

<br>또한 binder 는 아래와 같은 proc 파일을 통해 개발자에게 디버깅용 정보를 제공한다.

```
/proc/binder/state
/proc/binder/stats
/proc/binder/transactions
/proc/binder/transactions_log
```

prelinked 방식 (프로그램 컴파일 시 링크 단계에서 배치될 코드와 데이터 미리 정해두는 것. 어디에 배치될지) 에 의해서 mmap 을 별도의 메모리 영역에 배치할 수 있다.

실제 바인더 드라이버를 open 하고, 바인더 드라이버를 사용하여 통신하는 서비스 프레임워크(서비스 매니저)의 코드는 frameworks/base/cmds/servicemanager/binder.c 에 존재한다.
_ _ _

<br>
```
void binder_loop(struct binder_state *bs, binder_handler func)
{
    int res;
    struct binder_write_read bwr;
    uint32_t readbuf[32];
    bwr.write_size = 0;
    bwr.write_consumed = 0;
    bwr.write_buffer = 0;
    readbuf[0] = BC_ENTER_LOOPER;
    binder_write(bs, readbuf, sizeof(uint32_t));
    for (;;) {
        bwr.read_size = sizeof(readbuf);
        bwr.read_consumed = 0;
        bwr.read_buffer = (uintptr_t) readbuf;
        res = ioctl(bs->fd, BINDER_WRITE_READ, &bwr);
        if (res < 0) {
            ALOGE("binder_loop: ioctl failed (%s)\n", strerror(errno));
            break;
        }
        res = binder_parse(bs, 0, (uintptr_t) readbuf, bwr.read_consumed, func);
        if (res == 0) {
            ALOGE("binder_loop: unexpected reply?!\n");
            break;
        }
        if (res < 0) {
            ALOGE("binder_loop: io error %d %s\n", res, strerror(errno));
            break;
        }
    }
}
```

<br>실제 service_manager.c (frameworks/native/cmds/servicemanager 내 존재) 파일을 보면 main 함수에서 binder를 위한 메모리를 할당한 다음 selinux 가 활성화 되어 있으면 관련 확인 작업을 한 뒤 바로 binder_loop 함수를 호출하는데 그 함수 내부에는 무한루프를 돌면서 지속적으로 ioctl을 보내는 루틴을 확인할 수 있다. ioctl 명령이 있는것으로 보아 여기서 커널에 존재하는 binder driver와 통신을 하고 있다는 것을 알 수 있다.

//oss 바인더 부분 연관해서 다시보고, 원격서비스의 바인더처리 및 서비스 매니저의 바인더 관련 동작 코드 부분 다시 보기.

출처: https://lastyouth.tistory.com/19