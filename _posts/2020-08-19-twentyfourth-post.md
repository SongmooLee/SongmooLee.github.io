---
title: "스터디 20주차"
date: 2020-08-19
categories: Study
---

### deep copy

객체는 따로 implements Cloneable 하고, clone 메소드를 오버라이드 하지 않는 이상 클론(deep copy)이 안돼는것(deep copy)을 확인하고 배움

결국 클론이 쓰이는 객체마다 찾아가서 해당 클래스에 implements하고 override 함.

Arrays.copyof로 객체를 복사해봤지만 shallow copy가 되는것을 객체의 toString()으로 확인함.

근데 꼭 deepcopy가 필요하는가의 의문. 그 displayinfo안의 메소드들도 override되는지도 확인이 좀 필요할듯

![1](https://blogfiles.pstatic.net/MjAyMDA4MTlfMTcw/MDAxNTk3ODM3MTYxNzc4.ijMzIClFl7pRO5bDWrfWykOy46Siw5oX4Cp3Xv-A7Psg.KBzikAdn3i66yIN9W9t4oYxEtd3rCaHlmYpypnWWvrsg.PNG.goonta96/image.png)

객체를 deepcopy하는 방법

![2](https://blogfiles.pstatic.net/MjAyMDA4MTlfMjQ3/MDAxNTk3ODM3MjIyOTcx.j3KvNWlxdKoo6P7j30aF9m-50hIC2EWnjujIRVMTlaYg.ILJCIpQmjRz0w_14PoSUFOajgz3qbSw0R9G73zb_B6cg.PNG.goonta96/image.png)

이렇게 객체를 복사하는 코드를 작성하니까 성능이 급나빠진다.

![3](https://blogfiles.pstatic.net/MjAyMDA4MTlfNzYg/MDAxNTk3ODM3NDA4NjMw.LRROAYG6S3FtubsItrKIkeeSzzb9Y-uaU3OFZbKBmdog.HcUh0koArSqX5mbHyWZZyg7XJVH-SlaUWc9lbggVnKsg.PNG.goonta96/image.png)

![4](https://blogfiles.pstatic.net/MjAyMDA4MTlfMjQg/MDAxNTk3ODM3NTUyNDg0.fq4fZP-bRONlXbSmZUm3DPhQFvvqXxYequpECfsHg9Ig.07X6SwGln3ETO84fWusKpilsSa-ejpJB08zVH4gy6YIg.PNG.goonta96/image.png)

![5](https://blogfiles.pstatic.net/MjAyMDA4MTlfODkg/MDAxNTk3ODM4MTE2MTAw.lmcDLVLdncQxjmfLWuG6DZFGI08mbCENWtM3HLMNTNYg.BnSjU8JM40OMhZKG7RGZw-ivF7danar7JA8-Rqw_KXYg.PNG.goonta96/image.png)

리포트를 작성중이다.
작성하고 있는 리포트 일부


//string concat 쓰이는 부분 찾고 논문 쓰고 displayinfo 자체를 shallowcopy가 가능한지 확인하기, final class?