---
title: "TailwindCSS 세팅(2) - Tailwind Color Custom"
author: saemii
categories:
  - TailwindCSS
tags:
  - [Next.js, TailwindCSS]
date: 2024-02-26
last_modified_at: 2024-02-26
pin: true
---

## 📌시작하며

TailwindCSS 세팅(1)편에서 plugin이나 그 외 세팅을 마무리 했으니 이번엔 tailwind 컬러를 커스텀 해보고자 한다. 사실 거창해보이지만 별 건 없다🤭

> 이 글은 아래와 같이 이어집니다.
>
> - [TailwindCSS 세팅(1)](https://saemii-24.github.io/posts/tailwindcss-1/)
> - [TailwindCSS 세팅(2)](https://saemii-24.github.io/posts/tailwindcss-2/)
> - [TailwindCSS 세팅(3)](https://saemii-24.github.io/posts/tailwindcss-3/)
> - [TailwindCSS 세팅(4)](https://saemii-24.github.io/posts/tailwindcss-4/)

## ✅ 필요한 color 추가하기

tailwind 에서 기본으로 제공하는 color도 충분히 예쁘지만, 내 프로젝트에는 맞지 않을 수 있다. 이럴 땐 color 값을 추가해주면 된다. 나는 `music` 이란 이름으로 컬러값들을 추가해주었다. 직접 적용할 때는 `bg-music-100` 이런식으로 적용하면 된다.

```typescript
import type { Config } from "tailwindcss"

const config: Config = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
    colors: {
      music: { 👈
        100: "#E9F6FC",
        //여기에 더 추가해주면 된다.
      },
    },
  },
}
export default config
```

## ✅ 기본 color 함께 사용하기

그런데 위의 내용까지만 하고 끝내면 문제가 발생한다. 나는 gray나 black계열은 그대로 사용할 생각이었는데, 저렇게 color를 작성하면 기존의 color값들은 사용이 불가능해진다.
하지만 친절한 tailwind는 기존 color 값도 사용할 수 있도록 제시해준다.

```typescript
import colors from "tailwindcss/colors"  👈

const config: Config = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    colors: {  👈
      //기존값들을 불러와준다.
      transparent: "transparent",
      current: "currentColor",
      black: colors.black,
      white: colors.white,
      gray: colors.gray,
      emerald: colors.emerald,
      indigo: colors.indigo,
      yellow: colors.yellow,
      music: {
        100: "#E9F6FC",
        //이 부분은 커스텀
      },
    },
  },
}
export default config
```

## 📩마무리

다음엔 자주 사용하는 css 내용을 정리해보고자 한다!

## 🗂️참고 사이트

- <https://tailwindcss.com/docs/customizing-colors>
