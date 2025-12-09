---
title: "any와 unknown 타입에 대해서"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript, Next.js]
date: 2025-12-09
last_modified_at: 2025-12-09
pin: true
---

# 🍊any와 unknown

개발을 하던 중 다음과 같은 오류를 만났다.

> unknown에 할당할 수 없습니다.

해당 값의 타입을 any로 지정해둔 상태로 unknown 타입 변수에 넣으려고 하니 오류가 발생한 것이었다.
사실 unknown이나 any나 둘 다 모든 타입을 받을 수 있는 거 아닌가? 왜 오류가 발생했지? 라는 의문이 들었고,
한 번 의식하고 보니 이름부터 다른 타입인데 당연한 건가? 싶으면서도,
정확히 어떤 차이가 있는지 궁금해졌다.

## 💛any

any는 일종의 타입스크립트 **탈출**구문이다.
즉, 코드 일부를 자바스크립처럼 동작하고 싶을 때, 타입의 제한을 받고 싶지 않을 때 any를 사용할 수 있다.

즉, any를 사용하게 되면 타입스크립트는 더 이상 해당 코드의 오류를 잡아내지 못한다.
예를 들어 보자.

```typescript
const data: any = { fruit: "banana" }

console.log(data.fish)
```

data에는 fish라는 key가 없으므로 에러가 발생해야 옳지만 any 타입을 주었기 때문에 타입스크립트는 더이상
타입을 검사하지 않게 되고, 자연스럽게 해당 `console.log` 에서도 에러를 잡아내지 못한다.

## 💚unknown

unknown은 TypeScript 3.0부터 도입된 top type(최상위 타입)으로 기존 any를 대체한다.
핵심은 다음과 같다.

1. 무엇이든 `unknown` 타입에 할당할 수 있다.

```typescript
let u: unknown

u = 123 // ⭕
u = "hi" // ⭕
u = {} // ⭕
```

2. 하지만 `unknown`은 아무 타입에나 할당할 수 없다.

```typescript
let u: unknown

let a: any = u // ⭕
let b: unknown = u // ⭕
let c: number = u // ❌
```

unknown 변수에는 안전한 타입 `unknown`, `any`만 넣을 수 있다.

- <https://www.typescriptlang.org/ko/play/?#example/any>
- <https://www.typescriptlang.org/ko/play/?#example/unknown-and-never>
- <https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type>
