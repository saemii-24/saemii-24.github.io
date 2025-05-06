---
title: "Middleware - 공식문서 번역하면서 이해해보기"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-05-01
last_modified_at: 2025-05-01
pin: true
---

# ✍ 미들웨어가 필요하다!

웹사이트를 만들다 보면 다음과 같은 경우를 처리해야 하는 상황을 만날 수 있다.👀

1. 사용자가 로그인 했는데 로그인 페이지에 시도하는 경우
2. 사용자가 로그인 하지 않았음에도 보호되는 주소에 접근하려는 경우
3. 사용자에게 권한이 없음에도 사용자가 해당 주소에 접근하려는 경우

업무에서는 next.js와 next-auth를 주로 사용하고 있는 만큼 `useSession`를 활용해 **client side**에서 `useEffect`로 처리해도 가능하지만, client side이다 보니 한번은 렌더링이 되고 그 다음 액션이 이루어져야 하므로 사용자는 화면이 깜빡인다고 느낄 수 있다. 이를 방지하고자 Middleware를 활용하고 있었는데, 최근에 다시 한 번 Middleware 파일을 작업할 일이 있어, 겸사겸사 정리해보고자 한다.

내용은 [Next.js 공식문서](https://nextjs.org/docs/app/building-your-application/routing/middleware)의 15.3.1 app router를 기반으로 한다.

# 🤔 미들웨어란 무엇일까?

미들웨어를 사용하면 서버에 보내는 요청이 완료되기 전에 코드를 실행할 수 있어 아래와 같은 상황에 유용하게 사용된다.

- 요청의 일부 값을 읽은 후 빠르게 redirect할 때
- A/B 테스트 또는 실험에 따라 다른 페이지로 rewrite할 때
- 전체 페이지 또는 일부 페이지의 헤더를 수정할 때

다만, 아래와 같은 상항에는 적합하지 않다.

- 느린 데이터 fetching
- 세션 관리

## 💌 Convention

app router에서는 app과 나란히 두면 된다.
나는 현재 `src/app` 경로를 사용하고 있기 때문에 `src/middleware.ts`로 위치시켰다.

```
src/
├── app/
├── middleware.ts
```

## 💥 경로 맞추기

미들웨어는 기본적으로 프로젝트의 모든 경로에서 실행되는 만큼, 특정 경로에서만 미들웨어를 실행하거나 제외하고 싶다면 matcher를 사용해야 된다. ~~그렇지 않다면 무한 루프 에러를 만나볼 수 있다~~

아래는 미들웨어가 실행되는 순서다.

1. `next.config.js`의 `header`
2. `next.config.js`의 `redirects`
3. 미들웨어 (`rewrites`, `redirects` 등)
4. `beforeFiles` `next.config.js`의 `rewrites`
5. 파일 시스템 경로 (`public/`, `_next/static/`, `pages/`, `app/` 등)
6. `afterFiles` `next.config.js`의 `rewrites`
7. Dynamic Routes (`/blog/[slug]`)
8. `fallback` `next.config.js`의 `rewrites`

### 📌beforeFiles와 afterFiles

beforeFiles와 afterFiles가 정확히 무엇을 의미하는 것일까? 🤔 궁금해서 찾아보니 공식문서 [rewrites](https://nextjs.org/docs/app/api-reference/config/next-config-js/rewrites) 부분에서 자세히 다루고 있었다.

rewrites는 URL은 유지하면서 다른 페이지나 리소스로 routing 할 수 있게 해주는 기능이다.

> ✨ 예시
> 클라이언트가 주소창에 A라는 주소를 입력하더라도, rewrites 기능을 이용해 'A' 라고 표시는 되지만 실제로 'B' 경로에 맞는 기능을 처리한다.

이 기능을 활용하기 위한 `rewrites()` 함수에서 단순한 배열이 아닌 객체 형태로 `beforeFiles`, `afterFiles`, `fallback`을 반환할 때 라우팅 순서를 정교하게 제어할 수 있다.

```javascript
module.exports = {
  async rewrites() {
    return {
      beforeFiles: [
        // beforeFiles에 작성한 경우 headers/redirects 이후에 체크되며,
        // _next/public 파일을 포함한 모든 파일 이전에 실행된다.
        // 즉, 기존 페이지 파일들을 덮어쓸 수 있다.
        {
          source: '/some-page',
          destination: '/somewhere-else',
          has: [{ type: 'query', key: 'overrideMe' }],
        },
      ],
      afterFiles: [
        // afterFiles에 작성한 경우 pages/public 파일들을 먼저 확인한 후,
        // 동적 라우트(dynamically matched routes) 이전에 실행된다.
        {
          source: '/non-existent',
          destination: '/somewhere-else',
        },
      ],
      fallback: [
        // 이 rewrites는 pages/public 파일과 동적 라우트를
        // 모두 확인한 이후, 404 페이지가 렌더링되기 전에 실행된다. (즉 모든 라우팅 시도가 실패했을 때 마지막으로 실행됨)
        {
          source: '/:path*',
          destination: `https://my-old-site.com/:path*`,
        },
      ],
    }
  },
}

```


### 📌 실행 경로 정의하기

미들웨어가 실행될 경로를 정의하는 방법은 **두 가지**가 있다.

1. **커스텀 매처(config 사용)**

   - 특정 경로에 대해 미들웨어가 실행되도록 설정할 수 있다.

2. **조건문 사용**
   - 미들웨어 내에서 조건문을 사용하여 경로에 따라 실행을 제어할 수 있다.

## ✔️ matcher
matcher는 특정 경로에서만 middleware가 작동하도록 필터링할 수 있게 해주는 설정이다.

### 1️⃣ 단일 경로 지정하기

```typescript
export const config = {
  matcher: '/about/:path*',
}
```
### 2️⃣ 여러 경로 지정하기
```typescript
export const config = {
  matcher: ['/about/:path*', '/dashboard/:path*'],
}
```
### 3️⃣ 정규식 사용하기
matcher의 경우 정규식을 지원하므로, 고급 조건을 작성할 수 있다.

사실 `matcher`를 사용하는 예제를 보면 어떤 경우는 작성된 특정 경로에만 middleware를 **적용**하고,
또 어떤 경우는 작성된 특정 경로를 **제외**하고 나머지에 적용하는 방식이라 헷갈리곤 했는데.. 🫠

정리하자면 이렇다.

- matcher에 명시한 경로에만 적용되는 방식 → 1️⃣, 2️⃣ 처럼 사용
- 특정 경로만 제외하고 싶을 때 → 3️⃣ (정규식) 처럼 사용


```typescript
export const config = {
  matcher: [
    /*
     * 다음으로 시작하는 요청 경로는 제외:
     * - api (API 라우트)
     * - _next/static (정적 파일)
     * - _next/image (이미지 최적화 파일)
     * - favicon.ico, sitemap.xml, robots.txt (메타데이터)
     */
    '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
  ],
}
```
### 4️⃣ 조건부로 적용하기
만약 요청 헤더나 쿠키 값에 따라 middleware를 조건부로 실행해야 한다면 `has`와 `missing`을 이용하면 된다. 이 두가지는 필요에 따라 각각 혹은 함께 작성할 수 있다.

```typescript
export const config = {
  /*
     * 다음으로 시작하는 경로는 제외외:
     * - api(API 경로)
     * - _next/static (정적 파일)
     * - _next/image(이미지 최적화 파일)
     * - favicon.ico, sitemap.xml, robots.txt (메타데이터 파일)
     */
  matcher: [
    {
      source:
        '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
      missing: [
        { type: 'header', key: 'next-router-prefetch' },
        { type: 'header', key: 'purpose', value: 'prefetch' },
      ],
    },

    {
      source:
        '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
      has: [
        { type: 'header', key: 'next-router-prefetch' },
        { type: 'header', key: 'purpose', value: 'prefetch' },
      ],
    },

    {
      source:
        '/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)',
      has: [{ type: 'header', key: 'x-present' }],
      missing: [{ type: 'header', key: 'x-missing', value: 'prefetch' }],
    },
  ],
}
```

### 🤗 matcher 사용 방식 정리
1. 반드시 `/`로 시작해야 한다. 
2. `parameter`를 포함할 수 있다. 
  - `/about/:path`는 `/about/a` 및 `/about/b`와 매칭되지만 `/about/a/c`는 매칭되지지 않는다.
3. 이름이 지정된 `parameter`에는 **수식자(modifier)**를 붙일 수 있다. 예를 들어, `/about/:path*`는 `/about/a/b/c`와 매칭된다. 
  - `*` → 0개 이상
  - `?` → 0개 또는 1개
  - `+` → 1개 이상

4. 괄호로 묶인 정규식을 사용할 수 있다 `/about/(.*)`와 `/about/:path*`는 같다.