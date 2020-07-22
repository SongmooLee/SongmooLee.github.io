---
title: "스터디 16주차"
date: 2020-07-21
categories: Study
---

### temp

전의 displayinfo.java [링크](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/view/DisplayInfo.java) 에서 발견했던 boolean equals메소드와 copyfrom 메소드를 수정할 예정

#### equals

먼저 equals 메소드는 자주 틀릴 수 있는 필드나, 자료형의 비교 빠르기 등을 개선하면 될듯함.

[참조링크](https://stackoverflow.com/questions/23559526/performance-many-if-statements-vs-long-logical-expression)

[참조링크2](https://stackoverflow.com/questions/34428952/if-with-multiple-conditions-evaluation-in-java/34429054)

#### copyfrom

![1](https://blogfiles.pstatic.net/MjAyMDA3MjFfMjky/MDAxNTk1MzMyNDMwNDA1.6bTIRYNBJrxDLz24V7uQOcfzQ5t0gy_-p62UvfMUsWgg.AloQPVglG3kFBmzyVLUBBDn_jQi5hBEuFzleKJG_VDQg.JPEG.goonta96/3.JPG)

![2](https://blogfiles.pstatic.net/MjAyMDA3MjFfMjky/MDAxNTk1MzMyNDMwNDQ0.--Zsp2AwwyYKtfYDuMbc5lJU9dXvNvC9u6pGvYvEAfMg.5_mE6Hv5SWAXDBeAWlrPssabfAcWlKk_4Pu9lAqogPMg.JPEG.goonta96/1.JPG)

![3](https://blogfiles.pstatic.net/MjAyMDA3MjFfMjEw/MDAxNTk1MzMyNDMwNDA1.1eaewk3giSwU8XH1yeRvJTBr2szzJowLvKcrR65niB8g.96W_x2qqT-i-_VPC4Jeg2vuS1g8G6fkdOpQM2Z62QQMg.JPEG.goonta96/2.JPG)

호출되는 형식을 확인

![35](https://blogfiles.pstatic.net/MjAyMDA3MjJfMjQ1/MDAxNTk1NDE0OTcyMjA2.wBUwsgcFL3QaWFd5kDpQPpx3f_wUzpPL3tEBQD4LCmEg.wgEunj8fHDWMAALZuyNzLRmeu_Kw8hPDComUVqukvxog.JPEG.goonta96/%EC%97%AC%EB%9F%AC%ED%98%B8%EC%B6%9C%ED%99%95%EC%9D%B8.JPG)

호출되는 곳 확인가능

![4](https://blogfiles.pstatic.net/MjAyMDA3MjFfNzYg/MDAxNTk1MzMyNDMwNTIw.phowVFnxxo6_a7Bv-3HZPwv4gKLwX9thck5S5wMXe3Ig.-rYkDdg7L0A6uFkXWieJeZfEgSU1PpL9DkCGqsMwHPIg.JPEG.goonta96/11.JPG)

diplayinfo.java의 필드들 만을 그대로 (copyfrom 메소드에 있는 변수들 모두) 옮겨적음
Display 관련 변수는 일단 주석처리 했다.

이후에 aosp의 displayinfo.java에 있는 메소드들을 똑같이 복사하여, 최대한 비슷하게 구현하도록 노력하기 위해, (그래도 주석처리된게 몇 개 있다) 성능비교를 시작함

![5](https://blogfiles.pstatic.net/MjAyMDA3MjFfMjQg/MDAxNTk1MzMyNDMwNTIw.DeIj6pgdLjhoJkwkqoATHvIN5UfhR7IbgkoSRy2LlA8g.mMlb5yKaTCp2m9MHSiQn2dFOyOCv1NOTLzf4Z1H3oqIg.JPEG.goonta96/22.JPG)

![6](https://blogfiles.pstatic.net/MjAyMDA3MjFfMTM5/MDAxNTk1MzMyNTU0OTQz.TFCmdOTWqzbqcdROjX7V5XKwhmeoXT1i_km8P9znzq8g.h1--Z4HpuabUlgqE5yOMbyHWnXs71ETo9YskEBzIXHog.JPEG.goonta96/33.JPG)

clone()과 copyfrom()을 비교하기 위한 테스트 클래스이다.

![7](https://blogfiles.pstatic.net/MjAyMDA3MjFfMTI4/MDAxNTk1MzMyODE0ODI5.3zu75v2tsJ-JZnGrxRSDE2RejV6ERytaPAY57M6aY4wg.Qtfs_OO0g4ddBYd6UiSFkJibx-IDk8aUbxYBd_lwUnog.JPEG.goonta96/44.JPG)

각 clonetest 객체 t1, t2의 변수들에 임의로 대입시킴.

![8](https://blogfiles.pstatic.net/MjAyMDA3MjFfMjE4/MDAxNTk1MzMyOTM3ODgx.Po5-2nKg84djOD0IrzXKPjrf_PcSbvTlGY6ewOO8VFMg.gIgQSUIo4qf1PlAJwRnO0E7fGzUlvzw0YfULB_FfGUUg.JPEG.goonta96/55.JPG)

시간 비교를 했다.

![9](https://blogfiles.pstatic.net/MjAyMDA3MjJfNjYg/MDAxNTk1NDE1MzQ2NTE3.k_CjBsD8D0KPClKERK0eSJsk_9Q_PCg9LW12NEIpRAMg.83QLmgwkfPpoqi7kcq9T5pIM3mQFBfSUh6DC7ErCOk4g.JPEG.goonta96/15%EB%B2%88%EC%A7%84%ED%96%89.JPG)

15번실행을 하였다

clone메소드가 성능이 우세하게 나왔다
문제는 실상황 아니면 주석처리를 한 메소드나 필드가 영향을 미칠 수 있다
빌드를 해보면 결과가 달라지나?

나중에 wm 서버쪽에서 다른 성능의심구문을 찾아보자.