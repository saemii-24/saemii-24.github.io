---
title: "params에 따라 response 값이 달라질 때"
author: saemii-24
categories:
  - Next.js, React
tags:
  - [Next.js]
date: 2026-01-31
last_modified_at: 2026-01-31
pin: true
---

타입 지옥에 빠져 쓰는 글...
예를 들어 하나의 엔드포인트가 있고, 어떤 데이터를 받을지 요청 파라미터로 선택할 수 있는 구조의 query를 작성한다고 해보자.

그럼 당연히 파라미터 값에 따라 response도 달라지게 된다.

이걸 고정 Response type로 설계하는 순간 타입 지옥에 빠지게 된다 _(눈물로 쓰여진 글)_

이를 해결하기 위해서는 `요청 타입 > 응답 타입`을 연결해주는 타입 맵이 필요하다.

```typescript
type FuncNameTypeMap = {
  furniture: Furniture
  mail: Mail
  cutlery: Cutlery
}
```

그 다음, 요청 파라미터를 제네릭으로 설계해준다. 이렇게 작성하면 실제 사용 시 `useFuncName({ types: ['furniture', 'cutlery'] })` 여기서 T는 'furniture' | 'cutlery' 로 추론된다.

```typescript
type FuncNameParams<T extends keyof FuncNameTypeMap> = {
  enabled?: boolean
  types: T[]
}
```

참고로 `T extends keyof FuncNameTypeMap`은 'furniture' | 'mail' | 'cutlery' 의 범위 안에서만 T가 정해질 수 있도록 한정해 준다.

이제 Response Type을 지정해주자.

```typescript
type FuncNameResponse<T extends keyof FuncNameTypeMap> = {
  time: {
    requestTime: number
    responseTime: number
  }
  data: {
    [K in T]: FuncNameTypeMap[K][]
  }
}
```

⭐ 여기서 `[K in T]`는 무엇을 의미할까?

[K in T]는 TypeScript의 mapped type 문법으로, 의미는 아래와 같다.

> T에 포함된 값들 객체의 key로 사용한다.

즉, T가 다음과 같다면

```typescript
T = "furniture" | "cutlery"
```

TypeScript는 내부적으로 다음과 같은 객체 구조를 만들게 된다.

```typescript
{
  furniture: ...
  cutlery: ...
}
```

실제 호출 할 떄는 다음과 같이 작성해준다.

```typescript
const result = useFuncName({
  types: ["furniture", "cutlery"],
})
```

즉 호출 할 때 다
