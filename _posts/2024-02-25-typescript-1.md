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

> (제네릭을 사용하시면서) 이 부분은 조금 어려울 수 있어요.

라고 말씀하셨는데.. 그 말씀 그대로였다. 선생님께서 작성하시는 문법이 타입스크립트 제네릭인 건 알고 있지만 그걸 내가 자유자재로 쓸 수 있는 가는 아무래도 다른 문제다 보니... 😥 그래서, 이번엔 확실하게! 자주 사용해왔던 것과 추가로 공부한 타입스크립트 내용을 정리하고자 한다.

> 이 글은 아래와 같이 이어집니다.
>
> - [Typescript(1)](https://saemii-24.github.io/posts/typescript-1/)
> - [Typescript(2)](https://saemii-24.github.io/posts/typescript-2/)

## ✅typescript 사용하기

먼저 typescript를 react나 next에서 사용할 때는 어떻게 해야할까?

1. cra를 사용할 경우

```bash
npx create-react-app [프로젝트 명] --template typescript
```

2. vite를 사용할 경우

```bash
yarn create vite [프로젝트 명] --template react-ts
```

3. next를 사용할 경우

```bash
npx create-next-app@latest
```

next.js는 설치 당시에 typescript 사용여부를 물어보기 때문에 사용한다고 답해주면 된다.

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

## ✅Type Aliases

위에서 살펴본 것 처럼 정의할 때 필요한 type을 할당할 수도 있지만 재사용성이 떨어진다는 문제가 있다. 이를 해결하기 위해 타입을 정의하고 이름을 붙일 수 있는 기능이 존재한다.

```typescript
type User = { name: string; age: number }

let shin: User = { name: "짱구", age: 5 }
let han: User = { name: "유리", age: 5 }
```

### ➡️ | (union)

| (union)을 이용해 여러 타입 중 한 가지를 나타낼 수 있다. 아래 예제에서 `User`는 `string`, `number` 값 중 하나를 가진다는 뜻이며, `Obj` 는 `name: string`인 객체이거나 `age:number` 값을 가지는 객체라는 뜻이다.

```typescript
type User = string | number
type Obj = { name: string } | { age: number }

let user1:User = "짱구"⭕ //string 값 가능
let user2:User = 5⭕ //number 값 가능
let user3:User = false❌ //boolean 값 불가능

let user4:Obj = { name: "짱구" }⭕
let user5:Obj = { age: 5 }⭕
let user6:Obj = { isMarried: false }❌
//isMarried가 key 값인 객체 타입을 가지고 있지 않음. 불가능
```

### ➡️ & (intersection)

& (intersection)을 이용해 여러 타입을 가지는 새로운 타입을 만들 수 있다.

```typescript
type Name = { name: string }
type Age = { age: number }
type User = Name & Age

let user1: User = {⭕
  name: "짱구",
  age: 5,
}
let user2: User = {❌ //age 누락
  name: "짱구",
}
let user3: User = {❌//name 누락
  age: 5,
}
```

## ✅interface

interface는 주로 객체의 타입을 지정할 때 사용한다. `type` 키워드를 쓸 때와 차이점은 `=` 없이 바로 `{}` 안에 타입을 작성한다는 것이다!

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

### ➡️interface extends

interface는 extends 구문을 이용해 기존에 정의된 타입을 상속받고, 확장할 수 있다!

```typescript
interface Name {
  name: string
}

interface User extends Name { 👈
  age: number
  isMarried: boolean
}

let shin: User = {
  name: "짱구",
  age: 5,
  isMarried: false,
}
```

### ➡️? (optional)

? (optional) 키워드를 사용해 선택적으로 사용되는 속성을 만들 수 있다. 예를 들어 여러 객체를 만드는데 각각의 객체에 name, age를 모두 가지고 있거나 한 가지만 가진다고 생각해보자.

name,age를 모두가지고 있는 타입, name 타입, age 타입을 하나씩 지정하기엔 너무 번거롭다.🤔 이를 해결할 수 있는 방법인 것이다.

```typescript
interface User {
  name: string
  age?: number👈 // age는 선택 속성이다!
}

let shin: User = { name: "짱구" }⭕
let han: User = { name: "유리", age: 5 }⭕
let lee: User = { age: 5 }❌ //name은 필수다.
```

## ✅Enum

Enum은 '열거형' 으로 자바스크립트에는 존재하지않지만, 타입스크립트에서 제공하는 기능이다. Enum을 이용해 특정 집합 값들을 정의한다고 볼 수 있다.

```typescript
enum User {
  JjangGu,
  Yuri,
}

let member: User = User.JJangGu⭕
let member: User = User.Yuri⭕
```

즉 아래의 경우, `Hun`은 enum 키워드를 통한 집합에 정의되어 있지 않으므로, 에러가 발생한다.

```typescript
let member: User = User.Hun❌
```

## ✅타입 방어

## ✅타입 단언

기본적으로 타입스크립트는 '타입 추론'에 의해 타입을 결정할 수 있지만 개발자가 해당 데이터에 대한 타입을 단언할 수 있다. 이 때 단언은 확실한 상황에서 사용하는 것에 주의한다.

타입 단어는 `as`를 사용하면 된다.

예를 들어 아래와 같이 `getElementById`를 사용해 값을 불러온다고 가정하자. 타입스크립트는 `getElementById`로 불러올 경우 타입을 `HTMLElement | null`로 추론한다. 하지만 개발자는 해당 객체의 타입이 `HTMLCanvasElement`임을 알고 있다.

```typescript
const myCanvas = document.getElementById("main_canvas")
```

이 경우 as키워드를 사용한 **타입 단언**으로 타입스크립트에게 명시적으로 타입을 정해줄 수 있는 것이다.

```typescript
const myCanvas = document.getElementById("main_canvas") as HTMLCanvasElement
```

## ✅! (non-Null Assertion)

! (non-Null Assertion)은 의미 그대로, null이 아님을 단언 하는 방법이다.
typescript에서 컴파일 옵션인 strictNullChecks를 켜면 값이 null이거나 정의되지 않은 경우, 해당 값에 메서드나 속성을 사용하기 전에 해당 값을 테스트해야 한다.

아래 예제에서 `x`는 optional로 설정되었다. 즉, x의 타입은 number 또는 null일 수 있다는 것이다.

```typescript
function liveDangerously(x?: number | null) {
  // 아래에서 에러가 발생하지 않는다.
  console.log(x!.toFixed())
}
```

따라서, `console.log(x.toFixed())`를 시도하면, typescript는

> x가 null일수도 있겠는데?

라고 생각하며 에러를 발생시킨다. 그러나 개발자 입장에서, x가 null이 아님을 확신한다면, 함수 내부에서 `x!.toFixed()`처럼 x가 null일 가능성이 없다고 단언해줄 수 있고, 에러가 발생하지 않게 된다.

## ✅d.ts

d.ts 파일은 '선언 파일(Declaration file)' 이라고 한다. 최근에 등장하는 대부분의 라이브러리는 typescript를 사용할 수 있도록 Type을 제공하지만, 오래 된 라이브러리에서는 제공하지 않을 수 있다.🤔

이를 해결하기 위해 위해, `d.ts` 파일을 이용해 라이브러리의 타입 정보를 지정해줄 수 있다.

## 📩마무리

자주 사용하는 타입들을, 이렇게 정리하면서 한 번 더 이해할 수 있었던 것 같다!

제네릭과 유틸리티 타입이 남았는데, 글이 길어질 것 같아서 다음 포스트에서 이어서 정리하고자 한다.😎

## 🗂️참고 사이트

-<https://www.typescriptlang.org/ko/docs/handbook/declaration-files/dts-from-js.html>
