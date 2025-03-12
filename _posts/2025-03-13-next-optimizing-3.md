---
title: "Nextjs-Optimizing(3) - Production Checklist(번역)"
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

Next.js 공식문서를 읽을 때마다 느끼는 거지만 정말 사람은 아는만큼 보이고, 아는만큼 쓰는 것 같다.. 😅 특히 이렇게 습득할 수 있는 정보가 많을 때는 내가 처해있는 상황에 어떤 정보가 가장 필요한지 구별해내는 것이 정말 어렵지만 꼭 필요한 일이기도 하다.

서론이 길었는데, 최근 Optimizing 관련 문서를 읽다가 타고 타고.. 들어가다 보니 만난 너무 좋은 [챕터](https://nextjs.org/docs/app/building-your-application/deploying/production-checklist)가 있어서, 내가 읽기 좋게 번역해보려고 한다.. ~~(의역/오역 당연히!! 있을 수 있음)~~

# ✅배포 체크리스트

Next.js 애플리케이션을 프로덕션으로 전환하기 전에, **사용자 경험, 성능 및 보안을 위해** 구현해야 할 최적화 패턴이 있다.

---

## 1️⃣자동 최적화

아래는 Next.js가 기본적으로 제공하는 최적화 목록으로, 별도로 활성화할 필요가 없다.

🔹 [Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components)
Next.js는 기본적으로 서버 컴포넌트를 사용한다. 서버 컴포넌트는 서버에서 실행되며 클라이언트에서 렌더링하기 위해 자바스크립트가 필요하지 않기 때문에, 클라이언트 측 자바스크립트 **번들 크기에 영향을 미치지 않는다.**

물론, 상호 작용에 필요한 경우 Client Component를 사용할 수 있다.

이런 기본적인 최적화들은 애플리케이션의 퍼포먼스를 향상시키고, 각 네트워크 요청에서 전송되는 데이터의 비용과 양을 줄이는 것을 목표로 하고 있다.

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/deploying/production-checklist>
