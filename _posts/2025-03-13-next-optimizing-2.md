---
title: "Nextjs-Optimizing(2) - Package Bundling"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-03-13
last_modified_at: 2025-03-13
pin: true
---

## 📌시작하며

> 15.2.2 공식문서 기반으로 작성

아직 작업이 완전히 끝난 것은 아니지만, 공식문서를 보면서 꾸준히 Next.js의 최적화 기법에 대해서 관심을 가지고 있다!

특정 `<Image>` 컴포넌트나, `<Scripts>` 같은 경우에는 직접 예제를 만들어 보면서 학습하는 중이라 블로그에 정리하는건 조금 시간이 걸릴 것 같아 Package Bundling 부분부터 살펴보고자 한다.

## 1️⃣Analyzing JavaScript bundles

`@next/bundle-analyzer`는 애플리케이션 번들 크기를 관리하는데 도움을 주는 Next.js의 플러그인이다. 이 플러그인을 통해 각 패키지의 크기와 종속성을 시각적으로 확인할 수 있고, 이 정보를 기반으로 아래와 같은 최적화를 진행할 수 있다.

- 큰 사이즈 dependencies(종속성) 제거
- 코드 분할
- lazy load(지연 로드)

### 🧡설치하기

```bash
npm i @next/bundle-analyzer
# or
yarn add @next/bundle-analyzer
# or
pnpm add @next/bundle-analyzer
```

설치가 완료되면 `next.config.js`에 다음과 같이 작성한다.

```typescript
/** @type {import('next').NextConfig} */
const nextConfig = {}

const withBundleAnalyzer = require("@next/bundle-analyzer")({
  enabled: process.env.ANALYZE === "true",
})

module.exports = withBundleAnalyzer(nextConfig)
```

\+ 공식문서에서는 `CommonJS(.cjs)` 방식으로 작성되어 있으나, 나는` ES Module` 방식인 `next.config.mjs`를 사용하고 있어 오류가 발생했다. 따라서 아래와 같이 작성해주었다.

```typescript
/** @type {import('next').NextConfig} */
import bundleAnalyzer from "next-bundle-analyzer"

const withBundleAnalyzer = bundleAnalyzer({
  enabled: process.env.ANALYZE === "true",
})
```

> ✨**알아두기**
> .cjs → CommonJS 방식 (require, module.exports)
> .mjs → ES 모듈 방식 (import, export default)

### 💛보고서 생성하기

```bash
ANALYZE=true npm run build
# or
ANALYZE=true yarn build
# or
ANALYZE=true pnpm build
```

명령어를 실행해주면, 내가 만든 애플리케이션의 트리맵 차트를 확인할 수 있다!
파일 크기와 구조를 한눈에 파악할 수 있어, 내가 어떤 파일을 줄일 수 있는지 파악하는데 도움을 준다.😎

<hr/>

## 2️⃣Optimizing package imports

아이콘 라이브러리와 같은 일부 패키지들은 수백 개의 모듈을 내보낼 수 있어 개발 혹은 프로덕션에서 성능 문제를 일으킬 수 있다.

이런 패키지를 import 하는 방식을 최적화 하기 위해서 `optimizePackageImports` 옵션을 사용할 수 있다.
이 옵션을 사용하면 실제로 사용하는 모듈만 로드하게 된다.

```typescript
/** @type {import('next').NextConfig} */
const nextConfig = {
  experimental: {
    optimizePackageImports: ["icon-library"],
  },
}

module.exports = nextConfig
```

> ✨**알아두기**
> Next.js는 일부 라이브러리를 자동으로 최적화하기때문에 해당 옵션을 사용하지 않아도 되는 경우가 있다. 그 목록은 [이쪽](https://nextjs.org/docs/app/api-reference/config/next-config-js/optimizePackageImports)을 참조한다.

<hr/>

## 3️⃣Opting specific packages out of bundling

서버 컴포넌트와 라우트 핸들러 내부에서 가져온 패키지는 Next.js에 의해 자동으로 번들로 제공된다. 만약 이를 원치 않는다면, `serverExternalPackages` 옵션을 사용해 서버측에서만 사용하는 패키지를 클라이언트 번들에서 제외하도록 해 최적화 할 수 있다.

```typescript
/** @type {import('next').NextConfig} */
const nextConfig = {
  serverExternalPackages: ["package-name"], // 'package-name'을 번들에서 제외
}

module.exports = nextConfig
```

## 📚배운점

중간점검으로 현재 업무에서 작업하는 프로젝트에 해당 옵션을 적용해서 어떤 라이브러리가 사용되고, 어느 정도의 사이즈를 차지하는지 확인해보았다!

생각보다 사이즈가 큰 것이 몇개 보였는데, 많은 컴포넌트에서 필수적으로 사용하는 기능이 아니었기 때문에 해당 부분은 내가 직접 구현하는 것으로 리팩토링하면서 사이즈를 많이 줄일 수 있었다.

Next.js 공식문서에서 얘기하는 것 처럼 어떤 라이브러리를 도입할 때, 혹은 작업 중에 내가 만든 컴포넌트가 어느정도 사이즈를 차지하고 이게 내 프로젝트에 어느 정도의 영향을 미칠 수 있는지를 판단하며 작업하면 훨씬 쾌적한 결과물이 될 수 있으리라 생각한다.😊

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/optimizing/package-bundling>
- <https://nextjs.org/docs/app/building-your-application/deploying/production-checklist>
