---
title: "TailwindCSS (5) - Next.js에 darkmode 적용하기"
author: saemii
categories:
  - TailwindCSS
tags:
  - [Next.js, TailwindCSS]
date: 2024-07-29
last_modified_at: 2024-07-29
pin: true
---

## 📌시작하며

## ✅tailwind-merge

tailwind-merge는 스타일 충돌 없이 tailwind css의 클래스를 병합해주는 역할을 한다.
쉽게 말해서, 기본 스타일이 bg-red-500일때, bg-green-500을 props로 내려줘서 덮어씌울 수 있다고 생각하면 된다.

설치하고 시작해보자.

```bash
npm i tailwind-merge
```

현재 page.tsx에서 `Button`컴포넌트를 불러와 사용하고자 한다.
`Button` 컴포넌트의 기본 색상은 bg-red-500이지만,
bg-green-500으로 변경하고 싶을때, 이 내용을 단순히 props로 내려줘서 덮어씌우고자 시도하면
tailwind가 적절히 적용되지 않는 것을 알 수 있다.

```typescript
import Button from "@/components/Button"
import React from "react"

const page = () => {
  return (
    <>
      <Button className="bg-green-900 hover:bg-red-900">Save</Button>
    </>
  )
}

export default page
```

이제 Button 컴포넌트에 twMerge를 사용해서 스타일 충돌없이 적용해보자.
`twMerge(default 속성, className의 props)` 와 같이 적어준다.

```typescript
const Button = ({ children, className, ...props }) => {
  return (
    <button
      className={twMerge(
        "mx-3 bg-blue-900 px-4 py-2 text-white hover:bg-blue-100 hover:text-blue-900",
        className
      )}
      {...props}
    >
      {children}
    </button>
  )
}
```

- 여기서 props는

## ✅cva

```typescript
const buttonVariants = cva("rounded-md font-medium text-white", {
  variants: {
    variant: {
      primary: "bg-sky-500",
      secondary: "bg-slate-500",
    },
    size: {
      sm: "text-sm px-4 py-2"
      md: "text-base px-6 py-4",
    },
  },
  defaultVariants:{
    variant:"primary",
    size:"md",
  }
})

const Button = ({ children, className, variant, size, ...props }) => {
  return (
    <Button
      className={buttonVariants({variant, size, className})}
      {...props}
    >
      {children}
    </Button>
  )
}
```

## ✅clsx

```typescript
const buttonVariants = cva("rounded-md font-medium text-white", {
  variants: {
    variant: {
      primary: "bg-sky-500",
      secondary: "bg-slate-500",
    },
    size: {
      sm: "text-sm px-4 py-2"
      md: "text-base px-6 py-4",
    },
  },
  defaultVariants:{
    variant:"primary",
    size:"md",
  }
})

const Button = ({ children, className, variant, size, pending, ...props }) => {
  return (
    <Button
      className={twMerge(
        clsx(buttonVariants({variant, size, className}),{
          "bg-slate-900": pending
        })
      )}
      {...props}
    >
      {children}
    </Button>
  )
}
```

## ✅라이브러리 통합하기

lib폴더 안에 utils.ts 파일을 만들어 라이브러리를 간략하게 쓸 수 있도록 통합해보자.

```typescript
import clsx, { type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

이제 `twMerge(clsx ~` 이 부분을 `cn(~`으로 축약해서 쓸 수 있다!

```typescript
const Button = ({ children, className, variant, size, pending, ...props }) => {
  return (
    <Button
      className={cn(buttonVariants({ variant, size, className }), {
        "bg-slate-900": pending,
      })}
      {...props}
    >
      {children}
    </Button>
  )
}
```

## 🗂️참고 사이트

- <https://www.youtube.com/watch?v=Ix_gYhfB_9Q>
- <https://www.npmjs.com/package/tailwind-merge>
- <https://www.npmjs.com/package/clsx>
- <https://cva.style/docs>
