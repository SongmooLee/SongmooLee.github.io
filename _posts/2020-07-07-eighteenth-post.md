---
title: "스터디 14주차"
date: 2020-07-07
categories: Study
---

### temp

[오레오 뷰링크](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/oreo-security-release/core/java/android/view/View.java)
4725번째 줄

### 진행상황

![1](https://blogfiles.pstatic.net/MjAyMDA3MDhfMjI3/MDAxNTk0MjAyNzU0MjE3.grLdYFBweVW8bmW_pfG4rLF6L7Oloqzv9ObEi2_TTAMg.532BIBJ0fi0Qzzem4tTyd8WDbWgMr2z-G6BNeXQPOWEg.PNG.goonta96/11.PNG)

![2](https://blogfiles.pstatic.net/MjAyMDA3MDhfMjE2/MDAxNTk0MjAyOTIxMzM4.gKnSy4Rokfl6OavFlnN9ctyRcH3mEEEcG-u0GcLHuCEg.UxgZ8seZaO9KAjfyjXIwhRrrYyvAFTNe7ptLisxbXewg.PNG.goonta96/13.PNG)

성능개선이 가능해보이는 코드 (전 게시물 마지막의 뷰링크)를 복사해서 작동을 하도록 구현을 함. 총 86개의 case문과 많은 변수들을 확인 가능하다. 실행시간을 확인 가능.

![3](https://blogfiles.pstatic.net/MjAyMDA3MDhfNDIg/MDAxNTk0MjAyODU4NDY3.JTNpr7T1sBvoHf7avYjqql979onuafct70YsZoWd2J4g.d_bdEYndai07rv1R5IxfpmbmRll5sUc5Lz4QSIeg3kAg.PNG.goonta96/12.PNG)

기존 코드와 비슷하게 다른 패키지에서 상수를 가져오록 했음

![4](https://blogfiles.pstatic.net/MjAyMDA3MDhfMTMy/MDAxNTk0MjAzMTMxOTc3.WQwt3GJoa-PZGHDI8JJ77P-1KJ24RcPEMBIwKjVYCvgg.iWiz2DO3GulJDxVe7tFGj1FvVbdpTgbtUy5HD2py5sMg.PNG.goonta96/14.PNG)

문제는 Enummap구현이 이런 많은 switchcase문에 적용여부의 적합성 여부를 잘 모르겠다.
Enummap의 예시들을 보니 String key들에게 다른 특정 value들을 부여하는 식으로 쓰임.
애초의 기존코드는 integer로 switchcase문이 구성되있고 다른 성능향상 여부가 있는지를 모르겠다.