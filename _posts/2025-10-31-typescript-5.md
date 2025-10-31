---
title: "Query 상태에 따른 컴포넌트를 깔끔하게 정리해보자"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript, Next.js, TailwindCSS]
date: 2025-10-31
last_modified_at: 2025-10-31
pin: true
---

# 📌 Query 상태 그리고 컴포넌트

나는 useQuery를 이용해 be와 통신해서 각 상황별로 컴포넌트를 렌더링하고 있다.
예를 들어, data를 불러오는 경우 `<Loading/>` 컴포넌트, 빈 경우에는 `<Empty/>`, 에러인 경우는 `<Error/>`를 표시한다.

그런데 최근에 작업한 내용에는 동일한 컴포넌트를 기반으로 한 페이지가 여럿 있었는데, 각 컴포넌트 별로 이것을 작성하는 건 상당히 비효율적이란 생각이 들었다.

문제는 동일한 컴포넌트지만, api도 다르고 데이터가 전부 다르다는 건데.. 🤔
타입스크립트를 사용하기 때문에 타입을 어떻게 맞추면 좋을지 고민이 되었고, 이를 해결하기 위해 제네릭을 사용하였다.

이번 글은 이 내용을 정리하는 글이다.

## 👀 제네릭을 사용해보자!

### ✅ 기본 정리

사실 나는 `<Component<T>>` 형태의 타입을 지정하는 컴포넌트가 생소했다.
이게 어떤 의미인지 부터 살펴보자.

```typescript
function Component<T>(value: T): T {
  return value
}
```

먼저 함수는 다음과 같이 제네릭을 쓸 수 있다. 이 식에는 T가 3번 사용되었는데, 순서대로 해석해보자면 다음과 같다.

1. `<T>`

- 이 함수가 제네릭 함수임을 ㄴ타낸다.
- 타입 매개변수 T를 선언해, 어떤 타입이든 받을 수 있음을 나타낸다.

2. `(value:T)`

   - 1번에서 선언한 타입 변수를 사용해 매개변수의 타입을 지정해준 것이다.
   - 즉 이 함수는 T 타입의 값을 인자로 받게 된다.

3. `:T`
   - 함수의 반환 타입을 T로 지정하겠다는 뜻이다.

실제 사용은 다음과 같다.

```typescript
const str = Component<string>("Hello TypeScript")
```

여기서 T는 string으로 지정되며, `value:T`에 따라 매개변수도 `string`이 되고, 반환값도 `string`이 된다.

### ✅ object 사용해보기

기존과 동일하게 아래와 같은 함수를 만든 다음, 객체 타입을 사용해보자.

```typescript
function Component<T>(value: T): T {
  return value
}
```

```typescript
type User = {
  name: string
  age: number
}

const user = Component<User>({ name: "신짱구", age: 5 })
```

- <https://www.typescriptlang.org/ko/docs/handbook/2/generics.html>
