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

## 👩‍💻 예제

```typescript
import { NextResponse } from "next/server"
import type { NextRequest } from "next/server"

// 함수 내부에 `await`을 사용하는 경우 `async`로 표시할 수 있다.
export function middleware(request: NextRequest) {
  return NextResponse.redirect(new URL("/home", request.url))
}

export const config = {
  matcher: "/about/:path*",
}
```

### 💬 request

예제에서 middleware 함수에 매개변수로 받는 `request: NextRequest`가 정확히 무엇을 의미하는 것일까? NextRequest에 대한 [공식 문서](https://nextjs.org/docs/app/api-reference/functions/next-request) 를 살펴보면,

NextRequest는 브라우저의 기본 **Request 객체를 확장**한 것으로, Next.js Middleware에서 요청을 더 쉽게 다룰 수 있게 도와주는 역할을 한다.

이 객체의 주요 값들을 살펴보자.

#### 🍪 cookies

##### 🔸 set(name, value)

요청에 쿠키를 설정한다.

```typescript
request.cookies.set("show-banner", "false")
// → Set-Cookie: show-banner=false; path=/...
```

##### 🔸 get(name)

해당 이름의 쿠키 값을 가져온다.

```typescript
const cookie = request.cookies.get("show-banner")
// → { name: 'show-banner', value: 'false', Path: '/...' }
```

##### 🔸 getAll(name?)

특정 이름의 모든 쿠키를 배열로 반환한다.
만약 인자를 생략하면 모든 쿠키를 반환한다.

```typescript
request.cookies.getAll("experiments")
request.cookies.getAll()
```

##### 🔸 has(name)

쿠키 존재 여부를 확인한다.

```typescript
request.cookies.has("experiments") // → true or false
```

##### 🔸 delete(name)

요청에서 해당 쿠키를 삭제한다.

```typescript
request.cookies.delete("experiments") // → true or false
```

##### 🔸 clear()

모든 Set-Cookie 헤더를 제거하여, 쿠키 전체를 초기화한다.

```typescript
request.cookies.clear()
```

#### 🌐 nextUrl

`request.nextUrl`은 URL 객체를 확장한 것으로, Next.js에 특화된 정보를 포함한다.

| 속성           | 설명                                      |
| -------------- | ----------------------------------------- |
| `pathname`     | 요청 경로 (ex:`/home`)                    |
| `searchParams` | 쿼리 파라미터 객체 (ex: `?name=lee`)      |
| `basePath`     | 앱의 base path (Next.js 설정에 따라 다름) |
| `buildId`      | 빌드 식별자 (옵션)                        |

```typescript
request.nextUrl.pathname // "/home"
request.nextUrl.searchParams // URLSearchParams { name: 'lee' }
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

beforeFiles와 afterFiles가 정확히 무엇을 의미하는 것일까? 🤔 궁금해서 찾아보니 공식 문서 [rewrites](https://nextjs.org/docs/app/api-reference/config/next-config-js/rewrites) 부분에서 자세히 다루고 있었다.

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
          source: "/some-page",
          destination: "/somewhere-else",
          has: [{ type: "query", key: "overrideMe" }],
        },
      ],
      afterFiles: [
        // afterFiles에 작성한 경우 pages/public 파일들을 먼저 확인한 후,
        // 동적 라우트(dynamically matched routes) 이전에 실행된다.
        {
          source: "/non-existent",
          destination: "/somewhere-else",
        },
      ],
      fallback: [
        // 이 rewrites는 pages/public 파일과 동적 라우트를
        // 모두 확인한 이후, 404 페이지가 렌더링되기 전에 실행된다. (즉 모든 라우팅 시도가 실패했을 때 마지막으로 실행됨)
        {
          source: "/:path*",
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
  matcher: "/about/:path*",
}
```

### 2️⃣ 여러 경로 지정하기

```typescript
export const config = {
  matcher: ["/about/:path*", "/dashboard/:path*"],
}
```

### 3️⃣ 정규식 사용하기

matcher의 경우 정규식을 지원하므로, 고급 조건을 작성할 수 있다.

사실 `matcher`를 사용하는 예제를 보면 어떤 경우는 작성된 특정 경로에만 middleware를 **적용**하고,
또 어떤 경우는 작성된 특정 경로를 **제외**하고 나머지에 적용하는 방식이라 헷갈리곤 했는데..

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
    "/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)",
  ],
}
```

### 4️⃣ 조건부로 적용하기

만약 요청 헤더나 쿠키 값에 따라 middleware를 조건부로 실행해야 한다면 `has`와 `missing`을 이용하면 된다. 이 두가지는 필요에 따라 각각 혹은 함께 작성할 수 있다.

```typescript
export const config = {
  /*
   * 다음으로 시작하는 경로는 제외:
   * - api(API 경로)
   * - _next/static (정적 파일)
   * - _next/image(이미지 최적화 파일)
   * - favicon.ico, sitemap.xml, robots.txt (메타데이터 파일)
   */
  matcher: [
    {
      source:
        "/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)",
      missing: [
        { type: "header", key: "next-router-prefetch" },
        { type: "header", key: "purpose", value: "prefetch" },
      ],
    },

    {
      source:
        "/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)",
      has: [
        { type: "header", key: "next-router-prefetch" },
        { type: "header", key: "purpose", value: "prefetch" },
      ],
    },

    {
      source:
        "/((?!api|_next/static|_next/image|favicon.ico|sitemap.xml|robots.txt).*)",
      has: [{ type: "header", key: "x-present" }],
      missing: [{ type: "header", key: "x-missing", value: "prefetch" }],
    },
  ],
}
```

### 🤗 matcher 사용 방식 정리

1. 반드시 `/`로 시작해야 한다.
2. `parameter`를 포함할 수 있다.

- `/about/:path`는 `/about/a` 및 `/about/b`와 매칭되지만 `/about/a/c`는 매칭되지 않는다.

3. 이름이 지정된 `parameter`에는 **modifier**를 붙일 수 있다. 예를 들어, `/about/:path*`는 `/about/a/b/c`와 매칭된다.

- `*` → 0개 이상
- `?` → 0개 또는 1개
- `+` → 1개 이상

4. 괄호로 묶인 정규식을 사용할 수 있다
   - `/about/(.*)`와 `/about/:path*`는 같다.

## ☎️ NextResponse

`NextResponse` API는 아래와 같은 기능을 제공한다.

- 요청을 다른 URL로 `redirect`
- 특정 URL을 표시하여 응답을 `rewrite`
- `API Routes`, `getServerSideProps`, `rewrite` 대상에 대해 요청 헤더 설정
- 응답 쿠키 설정
- 응답 헤더 설정

### 💌 응답 생성하기

Middleware에서 직접 응답을 반환하려면, `Response` 또는 `NextResponse` 인스턴스를 반환하면 된다.

```typescript
// middleware.ts
import type { NextRequest } from "next/server"
import { isAuthenticated } from "@lib/auth"

// 이 미들웨어는 /api/로 시작하는 경로에만 적용됨
export const config = {
  matcher: "/api/:function*",
}

export function middleware(request: NextRequest) {
  // 인증 함수를 호출하여 요청을 검사
  if (!isAuthenticated(request)) {
    // 인증 실패 시 JSON 에러 메시지를 반환
    return Response.json(
      //💛 Response 인스턴스를 반환
      { success: false, message: "authentication failed" },
      { status: 401 }
    )
  }
}
```

## 🔨 Headers 세팅하기

HTTP 헤더를 사용하면 클라이언트와 서버가 요청 또는 응답 메시지와 함께 **추가 정보를 전달**할 수 있으며, 아래 예제와 같이 커스텀 헤더를 사용할 수도 있다.

**커스텀 헤더**란 개발자가 직접 정의한 헤더로, 서비스 내부에서만 사용하는 특수 목적의 정보 전달을 위해 사용한다. 이를 이용해 API 테스트, 디버깅, 실험 환경 분기 등에 활용할 수 있다.

```typescript
import { NextResponse } from "next/server"
import type { NextRequest } from "next/server"

export function middleware(request: NextRequest) {
  // 요청 헤더를 복제하고, 새로운 헤더 `x-hello-from-middleware1`을 추가
  const requestHeaders = new Headers(request.headers)
  requestHeaders.set("x-hello-from-middleware1", "hello")

  // NextResponse.next를 사용할 때도 요청 헤더를 설정할 수 있음
  const response = NextResponse.next({
    request: {
      // 새롭게 설정된 요청 헤더들
      headers: requestHeaders,
    },
  })

  // 응답 헤더에 `x-hello-from-middleware2`라는 새로운 헤더를 추가
  response.headers.set("x-hello-from-middleware2", "hello")
  return response
}
```

## 🎫 CORS

미들웨어에서 CORS 헤더를 설정하여 단순 요청 또는 `preflighted` 요청을 포함한 교차 출처 요청을 허용할 수 있다.

```typescript
import { NextRequest, NextResponse } from "next/server"

// 허용할 Origin 목록을 설정
const allowedOrigins = ["https://acme.com", "https://my-app.org"]

// CORS 옵션 정의
const corsOptions = {
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
  "Access-Control-Allow-Headers": "Content-Type, Authorization",
}

export function middleware(request: NextRequest) {
  // 요청의 Origin 헤더 가져오기
  const origin = request.headers.get("origin") ?? ""
  // Origin이 허용된 목록에 있는지 확인
  const isAllowedOrigin = allowedOrigins.includes(origin)

  // Preflight 요청인지 확인 (OPTIONS 메서드 여부)
  const isPreflight = request.method === "OPTIONS"

  if (isPreflight) {
    // Preflight 요청일 경우, 해당 헤더를 포함한 응답 반환
    const preflightHeaders = {
      ...(isAllowedOrigin && { "Access-Control-Allow-Origin": origin }),
      ...corsOptions,
    }
    return NextResponse.json({}, { headers: preflightHeaders })
  }

  // 일반 요청 처리
  const response = NextResponse.next()

  // 허용된 Origin이면 응답 헤더에 설정
  if (isAllowedOrigin) {
    response.headers.set("Access-Control-Allow-Origin", origin)
  }

  // 응답 헤더에 CORS 옵션 설정
  Object.entries(corsOptions).forEach(([key, value]) => {
    response.headers.set(key, value)
  })

  return response
}

// 이 미들웨어는 /api/ 이하 경로에만 적용됨
export const config = {
  matcher: "/api/:path*",
}
```

### 👓 CORS 보충 설명

**CORS**는 브라우저 보안 정책인 '동일 출처 정책(Same-Origin Policy)'을 우회할 수 있도록 해주는 표준 메커니즘이다.

여기서 말하는 출처는 다음과 같은 3가지 요소로 구성된다. (ex: `http//example.com:8000`)

- 프로토콜 (ex: `http`)
- 도메인 (ex: `example.com`)
- 포트 번호 (ex: `8000`)

그렇다면 CORS가 왜 필요한 걸까?

기본적으로 브라우저는 보안을 위해 다른 출처의 리소스 요청을 차단한다. 하지만, 실제 서비스에서 FE와 BE가 다른 도메인에 있을 수 있고, 외부 API를 사용하는 경우도 많으므로 이런 **다른 출처의 요청을 허용할 방법**이 필요하다.

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/routing/middleware>
- <https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/CORS>
