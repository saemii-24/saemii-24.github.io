---
title: "에러 핸들링 - 파일 업로드와 CORS"
author: saemii-24
categories:
  - HTML, React
tags:
  - [HTML]
date: 2026-02-13
last_modified_at: 2026-02-13
pin: true
---

# Presigned URL 업로드 중 CORS 에러 발생

회사에서 사용 중인 스토리지에 presigned URL로 파일 업로드를 구현하던 중 이상한 현상을 겪었다.🙄

## 🔍 증상

1. fetch로 업로드 수행
2. Network 탭에서 200 OK
3. 파일은 실제로 스토리지에 정상 업로드됨
4. fetch는 res.ok 전에 실패로 떨어짐
5. Network 탭에서 요청이 빨간색으로 표시됨
6. 프론트에서 에러로 판단.

즉, **스토리지에 파일을 넣는다** 라는 목적 자체는 수행했으나 프론트에서 실패로 인식하였고,
이에 따라 해당 flag값을 다시 백엔드로 보내는 과정이 실패하면서 전체 로직에도 문제가 생겼다.😫

## 🍊 원인 파악하기

원인을 찾고자 네트워크 탭을 살펴 보는 중 특이한 지점을 발견했다.
분명히 200OK가 뜨는데 빨갛게 X 처리가 되어있고 콘솔에 CORS error가 뜬 것이었다!

다시 한 번 Response headers를 확인해보니 다음과 같은 응답이 있었다.

```
access-control-allow-origin: https://test.domain.com
access-control-allow-origin: *
```

다음과 같이 allow-origin이 두 개가 들어오던 것.

CORS 오류인 만큼 애당 값이 문제이지 않을까 찾아보니 실제로 CORS 스펙상
Access-Control-Allow-Origin은 **하나의 값만 허용**하거나 **\* 하나만 가능** 하였고, 이와 같이 여러 개 존재하면 브라우저가 잘못된 응답으로 판단하는 것이었다.

## 🌟 구조 파악하기

현재 우리 구조는 다음과 같았다.

```
브라우저 > Nginx > 스토리지
```

1. 스토리지에서 CORS 설정으로 Access-Control-Allow-Origin 추가
2. Nginx에서도 add_header Access-Control-Allow-Origin \*; 설정

결과적으로 응답에 헤더가 2번 추가되는 상황이었던 것이었다!

그래서 Nginx에서 관리하던 해당 부분을 제거하고 스토리지에서만 CORS를 추가하도록 했다.

이후 다시 시도했더니 정상적으로 파일 업로드가 진행되었다!
