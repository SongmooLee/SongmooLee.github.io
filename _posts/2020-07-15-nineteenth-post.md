---
title: "스터디 15주차"
date: 2020-07-15
categories: Study
---

### temp

![그림1](https://blogfiles.pstatic.net/MjAyMDA3MTVfNDcg/MDAxNTk0ODA1NjYwMTUy.IFZjm8pbcyVEeE0KJM-2D2YxxyQ3Rghj14MD8x18kI8g.SEB_y70deJ9kLJ3yYBtlUkVPNLeyDs5Wc8HO8Pcmzfgg.PNG.goonta96/image.png)

![그림2](https://blogfiles.pstatic.net/MjAyMDA3MTVfOSAg/MDAxNTk0ODA1Njk3NjY5.4jUqbDrk-XLYBm-KRCRxT_QxxL3jn4e3Cncb3OxLbtUg.2kuu36_f_goEgxYVXCgEwohEWdivHoJnE-TbWhpMr5Eg.PNG.goonta96/image.png)

![그림3](https://blogfiles.pstatic.net/MjAyMDA3MTVfODcg/MDAxNTk0ODA1NzMyOTM5._zPMnbArsAX8kMsKRlRNVCEyDbRolqtunm4swSgg1UIg.dsa0US_OaOBkNbp4rqm3pLuSZEqPV4xQbuowTsKlAB8g.PNG.goonta96/image.png)

![그림4](https://blogfiles.pstatic.net/MjAyMDA3MTVfMjk5/MDAxNTk0ODA1NzcxMjQ2.AtZSuYNtJH8wsaFipOWvEdTtCZfLkjq7CSxT_p5KJ58g.6IDDw3hus-wFcG5pEooipn0EAWNbtRSlULMCpbzZ5Rog.PNG.goonta96/image.png)

![그림5](https://blogfiles.pstatic.net/MjAyMDA3MTVfMTA2/MDAxNTk0ODA2NDIxMDkw.1SSxAGXl9ydsJ3MrgrJ1dV_rQPKpXlb-3g4TxgzGxb0g.-zS03x3EaqURrTwDDQJbWUA-gbs8mzx1NUpc2si68x8g.PNG.goonta96/image.png)

더 안좋은 결과가 나온것 같다.. if문을 건드려야 겠다.

기존에 찾았던 다른 if문 같은 것들을 개선할 필요가 있을것 같다.

javap -c 로 바이트코드를 봤지만 무슨 방법으로 봐야될지를 모르겠다. 의논필요

#### 기존에 찾았던것들

frameworks/base/core/java/android/view/WindowManager.java 의 2895 줄 --> if else 구문은 없으나 한번 비교해볼 필요성은 있을듯, 호출횟수 보기

frameworks/base/core/java/android/view/Window.java의 781줄 --> if else는 많으나 비교적 조금 짧음, 직접 짜서 분석해볼 필요성은 있을듯, 호출횟수 보기

아니면 다른 팀원들이 지적했던 곳 다시 확인하기. ex) LayoutParams