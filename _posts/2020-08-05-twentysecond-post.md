---
title: "스터디 18주차"
date: 2020-08-05
categories: Study
---

### copyFrom

![1](https://blogfiles.pstatic.net/MjAyMDA4MDVfMTkg/MDAxNTk2NjI3ODc3MzAw.b2_Vdu_t7j2VinLWtsi0-04WPqNts8O2uEgMlsRF8-Eg.I-Q1aetBvPBzDH_Z-k3o7jybwUPO1oIs_D3_MRu-Ai8g.JPEG.goonta96/01.JPG)

수정된 copyFrom 메소드, implements Cloneable 포함

![2](https://blogfiles.pstatic.net/MjAyMDA4MDVfNTcg/MDAxNTk2NjI3ODc3MzAx.8q7xLk5s77iovc_VwvmjbeDAV16gUhOS5Mi8h4Qlx8og.mnE4XmCY_7I4rlTnrSoKRXXx7BVOH45LVWF7u6ox7HMg.JPEG.goonta96/02.JPG)

수정한 copyFrom이 쓰이는 다른 클래스의 수정 예시 1

### Math

Math가 많이 쓰이는 파일 중 호출이 자주되는 메소드를 찾아서 Math.max나 min대신 삼항연산자와 혼합해서 쓴것으로 구현하여 시간 측정을 함

![3](https://blogfiles.pstatic.net/MjAyMDA4MDVfMzAw/MDAxNTk2NjI3ODc3MzIz.Y5jmV0DymOGEup9lRbwMEwOYhwv5-1XeCfsZJGAOMlsg.GvM4dI6X_e2rIca0-TOXWcH-oc131L5fiCi20zkUhwUg.JPEG.goonta96/11.JPG)

Math만

![4](https://blogfiles.pstatic.net/MjAyMDA4MDVfNDQg/MDAxNTk2NjI3ODc3MzQz.PSAW4Gv5Jstu28SsUL6mxQthS8kX5Ko5ZgVd8CucNdcg.T8Uyu3esFFIxXkfIpg-OXshgNtQ6XsvkD6DvA2EsKF4g.JPEG.goonta96/12.JPG)

삼항연산자만

![5](https://blogfiles.pstatic.net/MjAyMDA4MDVfMzAw/MDAxNTk2NjI3ODc3MzE4._S6mj-AE6sB8oqq3JN54k6vQ7xwYTlgC5WPxv3-KCbAg.I5G4ZORDYZcTR9uUBbFleRqXbFkNQLZyeYaoP6h21tMg.JPEG.goonta96/13.JPG)

혼합 후 사용

![6](https://blogfiles.pstatic.net/MjAyMDA4MDVfMTAy/MDAxNTk2NjI3ODc3NTI0.k7cv7JKtP2TDHUcEK8XkZ1hQrOAR1zbw9xKCOgEukccg.CnEMB9Y7Eipms8qjzXZyOtog_d1A8cOaWi3YFncuqeQg.JPEG.goonta96/14.JPG)

천만번씩 10세트씩 돌려봤다.

![7](https://blogfiles.pstatic.net/MjAyMDA4MDVfMTI4/MDAxNTk2NjI3ODc3MjU4.k3IVsTkxYqS476dwXErHemprERLlBCHTux4dfwTDjF0g.t6u9OkIOTDMYRUwtSFDcFBJMKuIMr47rqmYxNLcLGZAg.JPEG.goonta96/10.JPG)

몇번을 해도 Math클래스만 쓴것이 제일 빠름...

### 많은 import

![8](https://blogfiles.pstatic.net/MjAyMDA4MDVfMTE0/MDAxNTk2NjI3ODc3NTc2.3__G3DS_eVoRxOU8aJUPL_vlmsGvCdpsEVUPw32lmnQg.oVPf8uFwzU1rdrxm_2rg3ogKFCo-IxBecMuRdS1Ex7Eg.JPEG.goonta96/20.JPG)

대부분 boolean이며 대부분은 WindowManagerService.java에서 많이 import 되어 쓰인다.

![9](https://blogfiles.pstatic.net/MjAyMDA4MDVfMjE1/MDAxNTk2NjI3ODc3Njcx.ORuQQEh-AtGDwnsvNTXaok2DiM549JpC_wOl-Ug7axkg.ynhuGcCdX9HqCjuFDiNKEhiyUxfPB48xn3Po7YN3lLMg.JPEG.goonta96/21.JPG)

몇 개의 변수를 제외하고 몇번 안쓰이는데 이런것들을 false로 그대로 다 바꿔쓴다면 성능 향상이 있을까?


