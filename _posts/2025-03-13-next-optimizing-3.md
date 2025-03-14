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

\+ 단어의 뜻이 명확하지 않을 때가 있어 아래 용어 설명을 추가했다!

# ✅배포 체크리스트

Next.js 애플리케이션을 프로덕션으로 전환하기 전에, **사용자 경험, 성능 및 보안을 위해** 구현해야 할 최적화 패턴이 있다.

---

## 1️⃣ 자동 최적화

아래는 Next.js가 기본적으로 제공하는 최적화 목록으로, 별도로 활성화할 필요가 없다.

🔹 **Server Components**
Next.js는 기본적으로 서버 컴포넌트를 사용한다. 서버 컴포넌트는 서버에서 실행되며 클라이언트에서 렌더링하기 위해 자바스크립트가 필요하지 않기 때문에, 클라이언트 측 자바스크립트 **번들 크기에 영향을 미치지 않는다.**

🔹 **Code Splitting**
서버 컴포넌트를 사용하면 route segments에 따라 자동 code splitting이 가능하다. 필요한 경우에는 클라이언트 컴포넌트, 타사 라이브러리를 lazy loading 할 수도 있다.

**🔹 Prefetching**
사용자 뷰포트에 새로운 경로로 가는 Link가 들어오면, Next.js는 백그라운드에서 해당 경로를 prefetcing 한다. 따라서, 사용자가 새로운 경로의 탐색이 즉각적으로 이루어지게 된다. 필요한 경우 프리패칭을 해제할 수 있다.

**🔹 Static Rendering**
Next.js는 서버에서 빌드 타임에 서버 및 클라이언트 컴포넌트를 정적으로 렌더링하고, 렌더링된 결과를 캐시하여 애플리케이션의 성능을 개선한다. 필요한 경우 특정 경로에 대해 동적 렌더링을 선택할 수 있다.

**🔹 Caching**
Next.js는 데이터 요청, 서버 및 클라이언트 컴포넌트의 렌더링 결과, static assets 등을 캐시하여 서버, 데이터베이스 및 백엔드 서비스에 대한 네트워크 요청 수를 줄인다. 필요한 경우 캐싱을 해제할 수 았다.

이런 기본 값은 애플리케이션 성능을 개선하고 각 네트워크 요청 시 전송되는 데이터의 양과 비용을 줄이는 것을 목표로 한다.

## 2️⃣ 개발 시 진행할 내용

애플리케이션을 구축할 때 최상의 성능과 사용자 경험을 보장하기 위해 아래의 기능을 사용하는 것이 좋다.

### ❤ Routing and Rendering

**🔹 Layouts**
레아이웃을 사용해 페이지 간 UI를 공유하고 페이지 이동시 부분 렌더링을 가능하게 한다.

**🔹 Link 컴포넌트**
클라이언트 측에서 페이지를 이동하게 하거나 prefetcing을 위해 <Link> 컴포넌트를 사용한다.

**🔹 Error Handling**
사용자 지정 오류 페이지를 생성하여 포괄적인 오류, 404 오류를 우아하게 처리하도록 한다.

**🔹 Composition Patterns**
서버, 클라이언트 컴포넌트에 대한 권장하는 구성 패턴을 따르고, 클라이언트 측의 JS 번들이 불필요하게 증가하지 않도록 한다.

**🔹 Dynamic APIs**
cookie 및 searchParams 속성과 같은 동적 API는 전체 경로를 동적 렌더링으로 설정함에 주의한다.(만약 루트 레이아웃에서 사용할 경우 애플리케이션이 전체적으로 동적 렌더링이 된다.)
본인이 동적 API를 의도적으로 사용했음을 확인하고, `<Suspense>`로 감싼다.

> **✨Good to know**
> Partial Prerendering (실험 기능)을 사용하면 전체 경로를 동적 렌더링으로 선택하지 않고, 경로의 일부를 동적으로 렌더링할 수 있다.

### 🧡 Data fetching and caching

### 💛 UI and accessibility

### 💚 Security

### 💙 Metadata and SEO

### 💜 Type safety

## 3️⃣ 프로덕션에 들어가기 전

## 4️⃣ 배포 후

어디에 애플리케이션을 배포했는지에 따라 애플리케이션 성능을 모니터링하고 개선하는데 도움이 될 수 있는 추가적인 도구나 통합 기능에 접근할 수 있다.

## 👀 추가 용어 설명

🔸**Segment**
URL 경로의 특정 부분을 의미한다. 예를 들어보자.

- `https://example.com/products/123` 이란 경로가 주어졌을 때 `products`와 `123`이 각각 하나의 segment 라고 할 수 있다.
- app router에서는 app 폴더 아래 url을 구성하기 위해 만든 폴더 하나가 경로의 segment가 된다.

🔸[Partial Rendering](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering)
Parial Rendering은 변경되는 route segments만 client에서 re-render 되고, 공유 segments는 유지되는 것을 의미한다. 예를 들어보자.

1. `/dashboard/settings`와 `/dashboard/analytics`간 내비게이션을 시도한다.
2. 이 과정에서 `/settings`는 unmount 되고, `/analytics`는 새 상태로 마운트 된다.
3. 이 떄, `/dashboard` 의 레이아웃은 그대로 유지 된다.
4. 1~3번 과정은 `/blog/[slug]/page`와 같은 동적 segment 에서도 동일하게 발생한다.

🔸[Prefetcing](https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#2-prefetching)

- Prefetcing은 사용자가 경로를 방문하기 전 백그라운데에서 경로를 미리 로드한다.
- Next.js 에서 경로를 Prefetcing하는 방법은 아래 두 가지가 있다.
  - **`<Link>` 컴포넌트 사용하기**
    - route가 사용자의 뷰포트에 표시될 때 자동으로 prefetcing 된다. (처음 들어왔을 때 / 스크롤 해서 뷰포트에 표시될 때 등)
  - **`router.prefetch()` 사용하기**
    - `useRouter` 훅을 사용해 프로그래밍 방식으로 경로를 미리 가져올 수 있다.

> **✨Good to know**
>
> - Prefetcing은 development에서는 불가능하고, production일 때만 가능함에 주의한다!
> - `<Link>` 컴포넌트의 더 자세할 설명은 [이쪽](https://nextjs.org/docs/app/api-reference/components/link)에서 확인한다.

🔸[Static Rendering](https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering#what-is-static-rendering)

- Static Rendering(정적 렌더링)에서는 date Fetching과 rendering이 서버에서 빌드 타임 또는 [data revalidating](https://nextjs.org/docs/app/building-your-application/data-fetching/fetching)(데이터 재검증)시 일어난다.
- 사용자가 애플리케이션을 방문할 때 마다 캐시된 결과가 제공된다.
- **장점**은 다음과 같다.

  - **더 빠른 웹사이트**
    - 미리 렌더링 된 콘텐츠를 Vercel과 같은 플랫폼에 배포하면, 캐싱하여 전 세계에 배포할 수 있어 전 세계 사용자가 웹사이트의 콘텐츠에 더 빠르고 안정적으로 접근할 수 있다.
  - **서버 부하 감소**
    - 콘텐츠가 캐시되므로, 각각의 사용자들 요청에 일일히 콘텐츠를 동적으로 생성할 필요가 없어 컴퓨팅 비용을 절감할 수 있다.
  - **SEO**
    - 미리 렌더링 된 콘텐츠는 페이지가 로들 될 때 이미 콘텐츠를 사용할 수 있어, 검색 엔진 크롤러가 색인을 생성하기 더 쉬워 검색 엔진 순위 향상으로 이어질 수 있다.

- 정적 렌더링은 데이터가 없거나, 사용자들이 동일한 데이터를 공유하고 있는 UI (블로그, 상품 페이지 등)에 유용하다.
- 정기적으로 업데이트되거나, 개인화 된 데이터가 있는 대시보드에는 적합하지 않다.

🔸[Dynamic Rendering](https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering#what-is-dynamic-rendering)

- Dynmaic Rendering(동적 렌더링)에서는 사용자의 요청이 발생할 때 (사용자가 페이지를 방문할 때) 서버에서 렌더링 된다.
- **장점**은 다음과 같다.
  - **실시간 데이터**
    - 동적 렌더링을 사용하면 실시간으로, 혹은 자주 업데이트 되는 데이터를 표시할 수 있어 데이터가 자주 변경되는 애플리케이션에 사용하기 좋다,.
  - **사용자별 콘텐츠**
    - 대시보드나 사용자 프로필과 같은 개인화 된 콘텐츠를 제공하고, 사용자 상호 작용에 따라 데이터를 업데이트 하기 더 쉬워진다.
  - **요청 시점에서의 정보**
    - 동적 렌더링을 사용하면 쿠키나 URL 검색 매개변수와 같이 요청 시점에만 알 수 있는 정보에 액세스 할 수 있다.

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/building-your-application/deploying/production-checklist>
- <https://nextjs.org/docs/app/building-your-application/routing/linking-and-navigating#4-partial-rendering>
- <https://nextjs.org/learn/dashboard-app/static-and-dynamic-rendering#what-is-static-rendering>
