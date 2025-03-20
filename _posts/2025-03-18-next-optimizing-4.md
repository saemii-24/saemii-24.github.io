---
title: "Nextjs-Optimizing(4) - Static Assets(번역)"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-03-18
last_modified_at: 2025-03-18
pin: true
---

## 📌시작하며

> 15.2.2 공식문서 기반으로 작성

계속해서 Static Assets에 관한 내용을 알아보자.

## 1️⃣ Static Assets in `public`

Next.js는 이미지 같은 static 파일을 root의 public이라는 폴더 아래에 제공할 수 있다. 그러면, 다른 컴포넌트에서 기본 URL(/)로 public 내부의 파일을 참조할 수 있게 된다.

예를 들어 `public/avatars/me.png` 파일은 `/avatars/me.png` 경로로 작성할 수 있다.

```typescript
import Image from "next/image"

export function Avatar({ id, alt }) {
  return <Image src={`/avatars/${id}.png`} alt={alt} width="64" height="64" />
}

export function AvatarOfMe() {
  return <Avatar id="me" alt="A portrait of me" />
}
```

### 🧡Caching

Next.js는 변경될 수 있으므로 public 폴더의 assets을 안전하게 캐시할 수 없습니다. 적용되는 기본 캐싱 헤더는 아래와 같다.

```
Cache-Control: public, max-age=0
```

### 💛Robots, Favicons, and others

`robots.txt`, `favicon.ico` 등과 같은 static metadata 파일의 경우 app 폴더 내부의 특수 메타데이터 파일을 사용해야 한다.

> **✨Good to know**
>
> - 디렉터리의 이름은 `public`이어야 한다. 이 이름은 변경할 수 없으며, static assets을 제공하는데 사용되는 유일한 디렉토리다.
> - 빌드 시점에 `public` 디렉토리에 있는 에셋만 Next.js에서 제공된다. 요청 시점에 추가된 파일은 사용할 수 없다. 따라서 영구적안 파일 저장소에는 Vercel Blob과 같은 타사 서비스를 사용하는 것이 좋다.

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/optimizing/static-assets>
