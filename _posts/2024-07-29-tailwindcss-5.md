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

```typescript
const Button = ({ children, ...props }) => {
  return (
    <Button
      className={twMerge("rounded-md border py-3 px-6 bg-red-500")}
      {...props}
    >
      {children}
    </Button>
  )
}
```

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
