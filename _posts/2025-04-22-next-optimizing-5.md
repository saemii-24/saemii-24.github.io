---
title: "Nextjs-Optimizing(5) - Image caching"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-04-22
last_modified_at: 2025-04-22
pin: true
---

# 📌 시작하며

이전 Nextjs-Optimizing(4) 포스팅에서 static assets에 대해 살펴보았다.  
사실 그때는 "그렇군! 좋아! 🤗" 하고 넘어갔는데, 나중에 이걸 실제로 사용할 일이 생기니 약간의 시행착오를 겪게 되어, 이를 해결했던 과정을 정리해보고자 한다.



# 🖼️ Image Caching

## 💥 문제 상황

나는 public 폴더 내의 static한 이미지를 자주 사용했는데, 해당 이미지는 바뀔 확률이 거의 없는 이미지였다. 그런데 이 이미지를 렌더링할 때마다 네트워크 요청을 보내는 것은 너무 비효율적으로 느껴져, "이걸 어떻게 처리하지?" 고민하다가, 이전 포스팅과 함께 Next.js 공식문서를 다시 훑어보며 이미지 캐싱 적용을 시도하게 되었다.



## 🎇 캐시란?

먼저, 캐시(Cache) 가 무엇인지 간단히 짚고 넘어가자.  
캐시는 브라우저가 웹사이트의 이미지, 글꼴, HTML, CSS, JS 등을 임시로 저장해두는 공간이다.

웹사이트에 처음 방문하면 브라우저는 페이지를 구성하기 위한 다양한 리소스를 다운로드한다.  
이때 브라우저는 다음 번 방문 시 같은 파일을 다시 요청하지 않기 위해, 먼저 캐시에 해당 리소스가 있는지 확인한다.  

즉, 캐시에 해당 데이터가 있다면 브라우저는 다시 다운로드하지 않고 바로 불러오기 때문에 로딩 시간이 단축된다.



## 🎁 Next.js에서 Image Cache 하기

Next.js 공식 문서를 보면, Next.js는 `immutable assets`에 대해 아래와 같은 `Cache-Control` 헤더를 자동으로 설정한다고 설명한다.  

이걸 보고는 "어? 그러면 따로 설정 안 해도 되는 거 아냐?" 라고 생각할 수 있지만, 사실은 "어떻게 이미지를 가져오는가"에 따라 적용 여부가 달라진다.



## 💛 참고 사항

Next.js는 `next/image` 컴포넌트를 이용해 static image를 가져올 경우, `Cache-Control: public, max-age=31536000, immutable`을 자동 설정한다.  

이런 경우에는 파일명에 SHA 해시가 포함되기 때문에, 파일 내용이 바뀌면 이름도 바뀌게 되고, 오래 캐시해도 문제 없이 안정적으로 유지할 수 있다.

하지만 `/public/images/profile.svg` 와 같은 직접 경로 기반의 정적 이미지 접근 (`<img src="/images/xxx.svg" />`)에는 자동으로 `Cache-Control`이 적용되지 않기 때문에 이 경우에는 직접 헤더를 설정해줘야 한다.



### 🥠 예제

Next.js에서는 `next.config.js`에서 `headers()` 설정을 통해 특정 경로에 대한 커스텀 HTTP 헤더를 직접 지정할 수 있다.  


```js
module.exports = {
  async headers() {
    return [
      {
        source: '/about',
        headers: [
          {
            key: 'x-custom-header',
            value: 'my custom header value',
          },
          {
            key: 'x-another-custom-header',
            value: 'my other custom header value',
          },
        ],
      },
    ];
  },
};
```
## 😫 문제 상황
그런데 위처럼 캐시를 적용하고자 했을 때 의도치 않은 문제가 발생했다.

나는 `/public` 폴더 바로 아래에 모든 static assets(image)을 보관하고 있었기 때문에, public 폴더 `/`의 모든 내용에 캐시 헤더를 적용하려고 다음과 같이 설정했었다.

```javascript
async headers() {
  return [
    {
      source: '/:path*',
      headers: [
        {
          key: 'Cache-Control',
          value: 'public, max-age=31536000, immutable',
        },
      ],
    },
  ];
}
```
그런데 이 설정을 적용하니... 이미지가 아예 로딩되지 않고, 로그인도 작동하지 않는 심각한 문제가 발생했다.
알고 보니 이 설정은 말 그대로 모든 경로`/`에 매칭되기 때문에, 정적 파일뿐 아니라 페이지 라우트나 API까지 모두 캐시되어 의도치 않은 상황이 발생한 것이었다.


### 🤗 해결 방법
이를 해결하기 위해 `/public/images` 폴더를 따로 만들어 이미지들을 옮긴 뒤 `source`를 아래와 같이 작성해주었다.

```javascript
async headers() {
  return [
    {
      source: '/images/:path*',
      headers: [
        {
          key: 'Cache-Control',
          value: 'public, max-age=31536000, immutable',
        },
      ],
    },
  ];
}
```
## ✅ 결과
이제 `/public/images/profile.svg` 파일을 network 탭에서 확인했을 때, 작성했던 내용 그대로 표시되는 것을 확인할 수 있었다.

>응답 코드: 200 OK
>
>헤더: Cache-Control: public, max-age=31536000, immutable


## 🗂️참고 사이트

- <https://nextjs.org/docs/app/api-reference/config/next-config-js/headers>
- <https://developer.mozilla.org/ko/docs/Web/HTTP/Reference/Headers/Cache-Control>
- <https://zapier.com/blog/what-is-a-cache/>