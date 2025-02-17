---

title: "에러 핸들링 - Parallel Routes"
author: saemii
categories: 
tags: - [Next.js, Error]
date: 2025-02-17
last_modified_at: 2025-02-17
pin: true

---

## 📌시작하며

계속해서 next.js의 Parallel Routes를 사용할 때는 어떻게 `error.tsx`를 보여주면 좋을지 정리해보자.

## ✅Parallel Routes란?

공식문서에서는 Parallel Routes를 이렇게 설명한다.

> Parallel Routes를 사용하면 동일한 레이아웃 내에서 하나 이상의 페이지를 동시에 또는 조건부로 렌더링할 수 있다. 대시보드나 소셜 사이트의 피드와 같이 앱의 동적인 섹션을 구성하는데 유용하다.

이 설명만 읽으면 <i>동일한 레이아웃 내에서 페이지를 렌더링 한다</i>고?🤔 하며 이해가 조금 어려울 수 있는데, 대시보드를 생각해보자!

특정 웹 사이트의 사용자의 체류시간이나 행동에 관련한 대시보드를 만든다면,

1. 사용자의 체류시간 통계
2. 사용자가 좋아하는 글 TOP 5
3. 사용자가 자주 접속하는 시간

등을 그래프로 나타낼 수 있고, 이 그래프 하나하나를 컴포넌트로 만들 수 있을 것이다.

즉, 이런 컴포넌트를 app 폴더 안에서 페이지 처럼 관리하면서 동시, 조건부로 렌더링 할 수 있는 것이다.

## ✅슬롯

Parallel Routes는 slots을 사용해 정의하면 된다.

- 명명 규칙은 `@이름`이다.
- slots는 자동으로 layout.tsx에 전달해 병렬로 렌더링한다.
- slots는 URL 구조에 영향을 미치지 않는다.

## ✅폴더 구조

그럼 이제 `dashboard`라는 페이지를 만들어보자. 이 안에는 병렬로 렌더링 하고 싶은 두 개의 컴포넌트를 slots으로 사용하기 위해 `@article`과 `@users` 두개의 폴더를 만들어 주었다.

그 안에는 각각 로딩 화면을 사용하기 위한 `loading.tsx`, 에러 발생시 보여줄 `error.tsx`와 기본 화면을 위한 `page.tsx`를 만들어 주었다.

```
dashboard/
├── layout.tsx
├── page.tsx
├── @article/
│   ├── loading.tsx
│   ├── page.tsx
│   └── error.tsx
├── @users/
│   ├── loading.tsx
│   ├── page.tsx
│   └── error.tsx
```

## ✅slots의 page.tsx

서버 컴포넌트 상황에서, `loading.tsx` 컴포넌트를 띄우기 위해, 미리 만들어 둔 renderWait 함수를 사용해 3초정도 기다린 후 return 되도록 해주었다.

```typescript
//page.tsx
import Trigger from "../_components/trigger"
import renderWait from "../lib"

export default async function Users() {
  await renderWait(3000)

  return (
    <div className="w-full h-20 flex-col bg-pink-50 items-center justify-center flex">
      유저 정보가 표시됩니다.
      <Trigger />
    </div>
  )
}
```

안에서 불러오는 Trigger는 error를 발생하는 버튼 트리거다.

```typescript
"use client"
import { useEffect, useState } from "react"

export default function Trigger() {
  const [error, setError] = useState<Error | null>(null)

  const makeError = () => {
    setError(new Error("미상의 에러"))
  }

  useEffect(() => {
    if (error) throw error
  }, [error])

  return (
    <button
      className="h-10 px-10 rounded-lg bg-blue-500 text-white cursor-pointer"
      onClick={makeError}
    >
      에러 발생 트리거
    </button>
  )
}
```

## ✅slots의 error.tsx, loading.tsx

각 상황에 렌더링 되어야할 파일도 만들어 주었다.

```typescript
"use client"

export default function ErroBoundary({
  error,
  reset,
}: {
  error: Error
  reset: () => void
}) {
  return (
    <div className="w-full h-20 flex-col bg-gray-900 items-center justify-center flex">
      <h2 className="text-white">기사 로딩 중 에러가 발생했습니다.</h2>

      <button
        className="h-10 px-10 rounded-lg bg-red-500 text-white cursor-pointer"
        onClick={() => reset()}
      >
        재시도
      </button>
    </div>
  )
}
```

```typescript
export default function Loading() {
  return (
    <div className="w-full h-20 bg-gray-200 items-center justify-center flex">
      기사 정보 로딩중...
    </div>
  )
}
```

## ✅dashboard 폴더의 layout.tsx

이렇게 만든 slots는 부모의 `layout.tsx`에 불러와 레이아웃을 잡아주면 된다!

```typescript
import { ReactNode } from "react"

export default function Layout({
  children,
  article, //@으로 되어있는 것들은 따로 import 안해도 됨
  users,
}: {
  children: ReactNode
  article: ReactNode
  users: ReactNode
}) {
  return (
    <>
      <nav className="text-2xl font-semibold mt-3">대시보드</nav>
      {children}
      {users}
      {article}
    </>
  )
}
```

위에는 `@article` 과 관련된 파일만 작성해두었는데, `@users` 도 똑같이 작성해주었다.
이제 렌더링 해보면 각 컴포넌트(페이지) 별로 특정 상황에 `loading.tsx`또는 `error.tsx`가 렌더링 되는 것을 알 수 있다.

## 🌷마무리 하며...

사실 이렇게 구현하는건 굳이 해당 라우팅 방식을 사용하지 않고 컴포넌트 내부에서 useEffect 등을 이용해 구현할 수 있는 내용이기도 하다.🤗 하지만 이렇게 구성하면 훨씬 구조화 되어있고 한 파일의 코드양을 줄일 수 있어 깔끔하게 관리할 수 있다는 장점도 있다!

개인적으로 한 파일당 작성되는 코드가 되도록 깔끔하게 유지되는게 좋아서, 앞으로 프로젝트에서는 자주 활용하고자 한다.

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/routing/parallel-routes>
- <https://www.youtube.com/watch?v=8I5-OTNOni0>
- <https://www.youtube.com/watch?v=wi8kF8UniUI>
