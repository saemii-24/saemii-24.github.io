---
title: "Typescript(1)"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript, Language]
date: 2024-02-25
last_modified_at: 2024-02-25
pin: true
---

## 📌시작하며

Next.js + Typescript 인강을 듣던 중 선생님께서

> TypeScript에 익숙하지 않으면 조금 어려울 수 있어요.

라고 말씀하셨는데.. 그 말씀 그대로였다. 선생님께서 작성하시는 문법이 타입스크립트 제네릭인 건 알고 있지만 그걸 내가 자유자재로 쓸 수 있는 가는 아무래도 다른 문제다 보니... 😥 그래서, 이번엔 확실하게! 자주 사용해왔던 것과 추가로 공부한 타입스크립트 내용을 정리하고자 한다.

## ✅원시타입

기본적인 JS의 원시타입은 다음과 같이 타입을 정의한다.

```typescript
let name: string = "짱구"
let age: number = 5
let isMarried: boolean = false
let isNull: null = null
let isUndefined: undefined = undefined
```

## ✅배열과 객체

배열 정의 시 위에서 살펴본 타입과 함께 `[]` 기호를 작성해주면 된다. 해당 타입으로 이루어진 배열임을 나타낸다.

```typescript
let name: string[] = ["짱구", "유리", " 맹구"]
let age: number[] = [5, 4, 6]
```

만약 배열안의 타입이 여러개라면 `(타입1|타입2)[]`과 같이 작성한다.

```typescript
let group: (string | number | boolean)[] = ["짱구", "유리", 5, " 맹구", false]
```

## ✅함수

함수에서 지정해주어야 하는 타입은 크게 두 가지로 볼 수 있다. 매개변수의 타입과, 함수가 반환하는 값의 타입이다.

### ➡️반환 값이 있을 때

반환값(return 값)이 있을 경우 return 하는 값의 type을 작성해주면 된다.

```typescript
function hello(a: number, b: number): number {
  return a + b
}
```

### ➡️반환 값이 없을 때

반환값(return 값)이 없는 경우 type은 void로 작성해주면 된다.

```typescript
function hello(name: string, age: number): void {
  console.log("내 이름은" + name + "나이는" + age + "입니다")
}
```

## ✅interface

위에서 살펴 본 것 처럼 적으면 한 가지 문제점이 발생한다. 여러 컴포넌트에서 동일한 타입을 쓴 다면 이것을 계속 해당 파일마다 만들어야 된다면 너무 번거롭기 때문이다.
이를 해결하기 위해 interface와 Type을 사용해 작성할 수 있다.

interface는 주로 객체의 타입을 지정할 때 사용한다.

```typescript
interface User {
  name: string
  age: number
  isMarried: boolean
}

//사용시
const shin: User = {
  name: "짱구",
  age: 5,
  isMarried: false,
}
```

## ✅Type

```typescript
let name: string = "짱구"
let age: number = 5
let isMarry: boolean = false
```

## ✅제네릭

## ✅타입 방어

## ✅타입 단언

기본적으로 타입스크립트는 '타입 추론'에 의해 타입을 결정할 수 있지만 개발자가 해당 데이터에 대한 타입을 단언할 수 있다. 이 때 단언은 확실한 상황에서 사용하는 것에 주의한다.

타입 단어는 `as`를 사용하면 된다.

```typescript
let str: any =
```

## ✅? 와 ! 사용

아마 타입스크립트로 작성된 코드를 보다 `?`나 `!`를 만났을 수도 있다. 이 두 가지의 의미는 다음과 같다.

## ✅d.ts

d.ts 파일은

## ✅react에서 활용
