---
title: "스터디 1주차 - 2"
date: 2020-03-08
categories: Study
---

## 안드로이드 개발환경 구축

- 필요한 요소들

 - VirtualBox: 윈도우 운영체제에서 안드로이드 플랫폼 소스 코드를 빌드하기 위한 리눅스를 설치하는데 사용하는 가상머신.

 - Ubutu: 안드로이드 소스 코드를 빌드하기 위해 사용하는 리눅스 운영체제.

 - Git: 안드로이드 소스 코드 관리에 사용되는 분산 버전 관리 시스템.

 - Repo: 안드로이드 소스 코드를 손쉽게 다운로드 하기 위해 사용한 파이썬 스크립트.

 - Eclipse: 안드로이드 애플리케이션 개발 및 디버깅에 사용한 개발 도구.

 - Android SDK: 안드로이드 애플리케이션 개발에 사용하는 프로그램 및 라이브러리 모음.

 - ADT Plugin for Eclipse: 안드로이드 애플리케이션 개발 작업을 돕는 유용한 이클립스 플러그인.

 - devlopment/ide/eclipse/.classpath: 안드로이드 애플리케이션 프레임워크 자바 소스 코드 및 컴파일 된 클래스 파일 경로를 담고 있는 설정 파일.

- - -

## init 프로세스

### 1) init 프로세스의 실행 과정

- start_kernel() -> init_post -> run_init_process() -> init 프로세스 실행.

- init_post() 함수에서 execute_command 에 등록된 프로세스 파일의 경로를 가지고 run_init_process() 함수를 호출하여 execve() 시스템 콜 호출.

- init 프로세스 정상 실행 위해서는 커널의 부팅 옵션 init = /init.

### 2) init 프로세스의 소스 코드 분석

init 프로세스의 기능

- init.rc 파일 분석하여 실행
 - init.rc 파일은 안드로이드 부팅 시 시스템의 환경 설정과 실행할 프로세스를 기술해 놓은 파일 --> init 프로세스가 이를 통해 액션 리스트, 서비스 리스트 생성. 안드로이드의 플랫폼의 소스코드에서 살펴볼 수 있다

- 자식 프로세스의 종료 처리

- 애플리케이션이 디바이스 드라이버에 접근할 때 사용하기 위한 디바이스 노드 파일 생성

- 시스템 동작에 필요한 환경 변수 저장하는 프로퍼티 서비스 제공

- 시그널 핸들러 등록 후 부팅에 필요한 디렉터리 생성 및 마운트

- 디바이스 노드가 위치하는 /dev 디렉터리 생성 후 open_devnull_stdio() 함수로 실행 로그 출력을 위한 장치 생성 혹은 log_init() 함수로 출력 장치 생성
 - open_devnull_stdion()함수는 null 이라는 디바이스 노드 파일 생성, 표준 입력/출력/에러출력 모두 null 장치로 리다이렉션한다

- parse_config_file() 함수로 init.rc 파일 파싱

- device_list() 함수로 정적 디바이스 노드 생성

- property_init() 함수로 공유 메모리에 프로퍼티 영역 생성 및 초기화

### 3) init.rc 파일 분석 및 실행

#### 1. 액션 리스트

키워드 'on'

- ' on init ' : 시스템 환경변수 등록 (루트 파일 시스템 내의 명령어 사용 위한 실행경로, 컴파일 시 필요한 라이브러리 경로), 부팅 시 필요한 디렉터리 생성, 특정 파일에 대한 퍼미션 지정, 시스템 동작과 관련된 디렉터리 마운트(/system, /data) -> 마운트 후 루트 파일 시스템 완성
 - 루트 파일 시스템 : shell 유틸리티, system 디렉터리, data 디렉터리

- ' on boot ' : 애플리케이션 종료조건 설정 (Out Of Memory (메모리 부족 시 애플리케이션 종료 역할)의 Adjustment Value 지정), 애플리케이션 구동에 필요한 디렉터리 및 파일 퍼미션 설정

- `'on property:<name>=<value>'`

#### 2. 서비스 리스트 키워드 'service'. 부팅시 실행하는 프로세스를 기술

실행하는 프로세스: 일회성 프로그램, 데몬 프로세스 (백그라운드로 구동되면서 애플리케이션이라 시스템 운용에 관여)
형태: `service '실행할 프로세스 이름' '프로세스 경로' \n '프로세스 실행 관련 옵션'`

#### 3. init.rc 파싱 코드 분석

parse_config_file() 함수는 인자로 전달된 파일을 읽어 연속적인 문자열로 생성 및 열 파싱 // read_file() 과 parse_file() 로 나뉨

- read_file() = 메모리 확보, 시작주소 반환

- parse_file() = 인자로 전달된 파일의 끝까지 파일 각 라인 파싱. 액션 리스트와 서비스 리스트 생성

- ( read_file() 로 파일을 읽어와 parse_file()로 각 문자열을 파싱함)

lookup_keyword() = ( parse_file() 안에서 사용되는 함수 )

- 각 라인의 첫 단어에 해당하는 keyword_list 구조체 배열에서 배열의 번호 반환

- keyword_list 구조체 배열은 KEYWORD 리스트에 의해 만들어지고, 각 리스트는 KEYWORD 매크로를 사용하여 생성

- KEYWORD 매크로 예시: COMMAND(init 프로세스가 실행하는 명령어들. 실행함수와 매핑), SECTION(액션 리스트와 서비스 리스트 구분)

parse_new_section() = 매크로에 의해 구분된 명령어(on, service)를 액션 리스트나 서비스 리스트에 등록, 이 함수를 수행하고 나면 액션 리스트와 서비스 리스트 완성

#### 4. 액션 리스트 및 서비스 리스트 실행

액션 리스트 drain_action_queue() 함수로 실행

서비스 리스트 do_class_start() 함수로 실행




출처 : [https://ddageung2.github.io/jekyll/update/second-post/](http://https://ddageung2.github.io/jekyll/update/second-post/)