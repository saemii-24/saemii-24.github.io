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

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/optimizing/package-bundling>
- <https://nextjs.org/docs/app/building-your-application/deploying/production-checklist>
