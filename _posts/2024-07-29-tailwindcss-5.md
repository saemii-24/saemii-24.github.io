---
title: "TailwindCSS (5) - Tailwind와 사용하기 좋은 라이브러리"
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

next.js를 사용하면서 tailwind를 사용하는 일이 많아졌다. 이번 프로젝트에서도 tailwind를 적용하기로 했는데 편리한 라이브러리가 있어 정리해보고자 한다.

## ✅tailwind-merge

tailwind-merge는 스타일 충돌 없이 tailwind css의 클래스를 병합해주는 역할을 한다.
예를 들어 기본 스타일이 bg-red-500이지만 특정 컴포넌트에서 bg-green-500을 덮어씌우는 것이다.
굳이 라이브러리를 쓰지 않아도 되지 않을까 싶지만, 단순히 className을 props를 내려줘 작성하는 것으로는
적용되지 않는다는 문제점이 있고 꽤 번거로워 진다.😅

이런 상황에서, tailwind-merge를 통해 간단하게 여러 Tailwind CSS 클래스를 병합하거나 중복된 클래스를 제거할 수 있다.

설치하고 시작해보자.

```bash
npm i tailwind-merge
```

실제 사용은 아래와 같다. 만들고 있는 Button 컴포넌트에 twMerge를 사용해서 기본적으로 적용될 클래스를 적어준 다음,
className은 props로 받아주어 함께 적어주면, 사용자가 내려준 className에 따라 스타일이 적절히 적용된다.

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

## ✅cva

UI라이브러리를 사용해보았다면 `variant = 'success'`와 같이 내려주는 것만으로 다양한 색의 버튼이 렌더링 되는 것을 경험해보았을 거다.
cva를 이용해 비슷한 기능을 구현할 수 있는데, 미리 특정 컴포넌트의 여러 가지 상태나 스타일을 정의해두고 props로 내려주는 것만으로 컴포넌트에 다양한 스타일을 적용할 수 있다. 😎

```bash
npm i class-variance-authority
```

설치한 후에 상단에 적절히 변수를 만들어 주고, cva를 사용해준다.
먼저 무조건 적용해야 하는 style를 적어주고, 각각 props로 내려줄 값을 작성하면 된다.

아래 에제에서는 variant와 size 두가지를 각각 세분화해 작성해주었다.

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

실제 사용할 때는 아래와 같이 미리 작성해둔 variant와 size를 넣어 렌더링 해주면 다양한 형태의 버튼을 만들 수 있다.

```typescript
<Button variant="primary" size="md">
  연습용 버튼
</Button>
```

## ✅clsx

clsx는 조건에 따라 css를 주기 좋은 라이브러리다!

```bash
npm install --save clsx
```

아래 예제는 위에서 살펴본 twMerge와 cva가 함께 작성된 예제인데, clsx로 작성된 부분은 `"bg-slate-900" : pending` 으로, 사용자가 제시한 조건 pending이 true인 경우에만 bg-slate-900이 적용된다.

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

지금까지 살펴본 라이브러리를 간단한 구문으로 사용할 수 있도록 정리해보자.
먼저 lib폴더 안에 utils.ts 파일을 만들어 아래와 같이 twMerge와 clsx를 함께 작성할 수 있도록 하자.

```typescript
import clsx, { type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

이제 `twMerge(clsx ~` 부분을 `cn(~`으로 축약해서 쓸 수 있다! cva의 경우 기존 작성방식과 동일하게 상단에 변수를 선언하고 각 그룹별 적절히 클래스를 나눈 후 아래와 같이 작성해주면 된다.

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
