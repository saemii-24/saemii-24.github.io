---
title: "TailwindCSS 세팅(1) - ESlint Plugin"
author: saemii
categories:
  - TailwindCSS
tags:
  - [Next.js, TailwindCSS, ESlint]
date: 2024-02-26
last_modified_at: 2024-02-26
pin: true
---

## 📌시작하며

Next.js에서 TailwindCSS 이용해 스타일링을 하기로 결정하고, 필요한 플러그인을 설치했다. 하지만 예상대로 작동하지 않거나 오류가 발생하는 경우가 있었다. TailwindCSS는 종종 사용할 것 같으니 이번 기회에 어떤 오류가 있는지 정리하고, 나름의 세팅 과정을 정리해보고자 한다.

> 이 글은 아래와 같이 이어집니다.
>
> - [TailwindCSS 세팅(1)](https://saemii-24.github.io/posts/tailwindcss-1/)
> - [TailwindCSS 세팅(2)](https://saemii-24.github.io/posts/tailwindcss-2/)
> - [TailwindCSS 세팅(3)](https://saemii-24.github.io/posts/tailwindcss-3/)
> - [TailwindCSS 세팅(4)](https://saemii-24.github.io/posts/tailwindcss-4/)

## ✅ 특정 폴더 하위 파일에서 TailwindCSS 작동 안할 때

next13에서 app 폴더를 사용해 component 폴더를 만들었으나 component 폴더에서 아무리 tailwind 클래스를 작성해도 제대로 작동하지 않았다.

```
├── app/
│   └── page.tsx
│
└── component/
    └── header.tsx
```

알고 보니 `tailwind.config.ts` 파일에 적힌 경로가 `component`가 아니라 `components`여서 발생한 문제였다. 둘 중 하나의 이름을 바꿔주면 된다. 나는 `components`로 폴더 이름을 변경했다.

```typescript
import type { Config } from "tailwindcss"

const config: Config = {
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  //생략
}
```

## ✅ eslint가 제대로 작동하는지 확인하기

next.js를 설치할 때 자동으로 package.json에 적혀 있는 `"lint": "next lint"` 를 활용해 확인하면 된다. 작성한 tailwind가 order 규칙에 맞지 않거나, 알 수 없는 className인 경우 warning이 발생함을 확인할 수 있다.

```bash
npm run lint
```

혹은 VS code의 `출력 > eslint` 를 확인해 제대로 작동하고 있는지 확인하면 된다.

## ✅ Failed to load config "prettier" to extend from

그런데 위에서 작동하는지 확인하기 위해 `npm run lint`를 작동하자 다음과 같은 오류가 발생했다.

```bash
Failed to load config "prettier" to extend from.
```

해당 오류는 필요한 eslint-prettier 플러그인이 빠져있어서 발생한 것이었다. 추가적으로 필요한 내용을 설치하자.

```bash
yarn add --dev prettier eslint-config-prettier eslint-plugin-prettier
```

## ✅ eslint-plugin-tailwindcss

오피셜로 제공하는 `prettier-plugin-tailwindcss`보다 더 많은 규칙을 제공하는 유용한 plugin이다.
class 정렬, 병합, 임의 값 사용 금지, 동일 속성 덮어쓰기 금지 등을 제공한다.

```bash
yarn add --dev eslint-plugin-tailwindcss
```

설치 해 준다음, `eslintrc.json`에 다음과 같이 작성해 extends와 rules를 추가해준다.

```json
{
  "extends": [
    "next/core-web-vitals",
    "prettier",
    "plugin:tailwindcss/recommended" 👈
  ],
  "rules": { 👈
    "tailwindcss/classnames-order": "warn",
    "tailwindcss/enforces-negative-arbitrary-values": "warn",
    "tailwindcss/enforces-shorthand": "warn",
    "tailwindcss/no-custom-classname": "warn",
    "tailwindcss/no-arbitrary-value:": "warn",
    "tailwindcss/no-contradicting-classname": "warn"
  }
}
```

## ✅ 저장하면 eslint 규칙 적용되게 하기

위의 오류를 해결하고 다시 `npm run lint`를 해보면 문제가 있는 className을 잘 잡아냈으나, 저장할 때 eslint plugin이 자동으로 정렬되지 않았는데, 이 문제는 VS code의 `settings.json`에 아래와 같이 적어 해결했다.

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll": true
  }
}
```

## ✅ tailwindCSS

## 📩마무리

기본 세팅을 이렇게 마무래핬다. 이제 tailwind 커스텀을 시작해보자

## 🗂️참고 사이트

- <https://www.npmjs.com/package/eslint-plugin-tailwindcss?activeTab=readme>
