---
title: "에러 핸들링 - global-error.tsx"
author: saemii
categories:
tags:
  - [Next.js, Error]
date: 2025-02-12
last_modified_at: 2025-02-12
pin: true
---

## 📌시작하며

1월에 기회가 있어 교육박람회를 다녀왔고, 가장 관심있게 보았던 것은 디지털 교과서다. 사실 출판사들마다 UI는 거의 비슷했는데, 가장 기억에 남는 출판사의 교과서를 떠올려보면, 사용자에게 로딩 컴포넌트나 토스트 메세지가 적절히 표시되어 사용자 배려가 돋보였다.

내가 봤던 출품작 중에서는 시연 중 네트워크 오류로 인해 제대로 작동을 하지 않은 경우가 있었는데, 그때 따로 에러 안내 문구가 없다보니 시연 하시는 분들도 왜 예상대로 작동하지 않는지 잠시 당황하시는 모습이 있었다. 그때 에러가 발생하더라도, 적절한 안내문구 제공이 중요한 일이란걸 새삼 깨닫게 되었다.

그래서 이번엔 next.js의 기능에 포함된 에러 핸들링에 대해 알아보고, 프로젝트에도 적용해보려고 한다.

## ✅global-error.tsx

next.js에서는 error.tsx를 사용해 에러 핸들링이 가능한데, root layout의 에러를 잡아내긴 어렵다. 이를 위해, next.js에서는 `global-error.tsx`라는 파일을 제공한다.
이때 위치는 app 폴더 바로 아래 root layout과 나란히 두면 된다.

먼저 root layout에서 error를 발생시킬 Wrapper 컴포넌트를 하나 만들어 준다.

```typescript
"use client"
import { useState } from "react"

interface WrapperProps {
  children: React.ReactNode
}

export const ErrorWrapper = ({ children }: WrapperProps) => {
  const [error, setError] = useState<boolean>(false)
  if (error) throw new Error("에러가 발생했습니다!")

  return (
    <div className="flex flex-col rounded-lg mt-8 relative p-4 border border-gray-300">
      <div className="absolute top-0 left-4 -translate-y-1/2">
        <button
          title="Simulate an error"
          className="bg-red-950 text-white cursor-pointer rounded px-10 py-2 leading-none font-semibold text-sm hover:bg-red-900 transition"
          onClick={() => setError(true)}
        >
          에러 강제 발생
        </button>
      </div>
      {children}
    </div>
  )
}
```

이제 이 wrapper가 `layout.tsx`에서 작동하도록, 감싸준다.

```typescript
import "./globals.css"
import Provider from "./Provider"
import { ErrorWrapper } from "./error-wrapper"
import { Noto_Sans_KR } from "next/font/google"

const noto = Noto_Sans_KR({ subsets: ["latin"] })

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ko" className={noto.className}>
      <body className="mx-auto min-h-screen w-[360px] border border-x-gray-200 ">
        <ErrorWrapper>
          <Provider>{children}</Provider>
        </ErrorWrapper>
      </body>
    </html>
  )
}
```

이제 에러가 발생했을 때 렌더링 하는 페이지 즉, `global-error.tsx`파일만 만들어 주면 된다!

```typescript
"use client" // 에러 바운더리는 반드시 Client Component여야 한다.

export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    // global-error는 반드시 html과 body 태그를 포함해야 한다.
    <html>
      <body>
        <div className="w-screen h-screen flex items-center justify-center">
          <h2>에러가 발생했습니다!!</h2>
          <button
            className="bg-cyan-800 text-white px-10 py-2 cursor-pointer hover:bg-cyan-800/70"
            onClick={
              // 세그먼트를 다시 렌더링하여 복구를 시도한다.
              () => reset()
            }
          >
            다시 시도하기
          </button>
        </div>
      </body>
    </html>
  )
}
```

- 이때 `global-error.tsx`는 아예 html 파일을 대체하기 때문에 `html` 태그와 `body` 태그를 꼭 포함해서 작성해야 한다.
- 반드시 Client Component로 작성해야 된다.
- 예시에 적힌 props는 다른 상위 컴포넌트에서 내려줄 필요 없이 바로 받아와 `reset()`을 통해 `refresh` 해줄 수 있다.

가장 중요한 내용은, **해당 error는 dev모드에서 확인이 불가능하다**는 것이다!!

반드시, `npm run build`를 하고 `npm run start`를 통해 :star:**production 모드**:star:에서 에러를 발생시켜야 미리 만들어 둔 `global-error.tsx`가 렌더링 된다는 것을 잊지 말자.

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/routing/error-handling>
- <https://www.youtube.com/watch?v=ywUDMEVR3Mg>
