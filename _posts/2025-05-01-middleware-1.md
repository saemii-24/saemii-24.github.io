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

### 📌 실행 경로 정의하기

미들웨어가 실행될 경로를 정의하는 방법은 **두 가지**가 있다.

1. **커스텀 매처(config 사용)**

   - 특정 경로에 대해 미들웨어가 실행되도록 설정할 수 있습니다.

2. **조건문 사용**
   - 미들웨어 내에서 조건문을 사용하여 경로에 따라 실행을 제어할 수 있습니다.
