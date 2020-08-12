---
title: "스터디 19주차"
date: 2020-08-12
categories: Study
---

### concat()

![1](https://blogfiles.pstatic.net/MjAyMDA4MTJfMTcw/MDAxNTk3MjMwOTk3Mzc2.2ts6MV6QX5Jb6wQb6czto3DgBcU6NCUMkPa8OUD9-KUg.V3HmZpKI9WAxmlHfxvd4BKMc0_2rUfvv3ouScuX10QYg.JPEG.goonta96/1.JPG)

String 기본연산자 + 를 이용한 문자열 연결 필드들 발견

![2](https://blogfiles.pstatic.net/MjAyMDA4MTJfMjYy/MDAxNTk3MjMwOTk3Mzc2.GwDheisQefVOF1zUBfbfaZHSGF2JTrlJOeRWCNxtj9gg.24uAd6RKHfUQ_7Fk3EYUPT8Stb921DkibImz-lqLJ7kg.JPEG.goonta96/0.JPG)

임포트 되는 필드들의 위치를 찾아 복붙을 했다

![3](https://blogfiles.pstatic.net/MjAyMDA4MTJfNTcg/MDAxNTk3MjMwOTk3NDEx.fVx-2zx2L0P0S02zcpvQEBdh8MAxBlwmWBmEMXkX7Wsg.5SdSMZx9rEl8cmzarBTNcHnJdnICB2EyVON0KcT4H5Eg.JPEG.goonta96/2.JPG)

String 기본연산자 +를 쓴 경우를 백만번씩 열번 돌렸을 때 걸린 시간

![4](https://blogfiles.pstatic.net/MjAyMDA4MTJfMTQ4/MDAxNTk3MjMwOTk3NDIx.LaCcg5pgfku-5fH5gcqoOTft2wzQYO5ywdFmkJUjYd4g.wXHqKIDELy4F98uplXZzcuxK40KuDUqOcbvPvG5Rl1cg.JPEG.goonta96/3.JPG)

StringBuilder나 StringBuffer 메소드는 main메소드안에 없어서 사용불가

concat메소드를 이용하여 바꾼 후 백만번씩 열번 돌렸을 때 시간

몇번을 돌려도 concat이 더 좋게 나온다.

다른 파일에 String 기본연산자인 +가 많이 존재하는 곳 찾아보기


