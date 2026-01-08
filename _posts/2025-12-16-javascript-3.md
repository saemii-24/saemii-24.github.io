---
title: "알아두면 좋은 JS 문법들"
author: saemii-24
categories:
  - Javascript
tags:
  - [Javascript]
date: 2025-12-16
last_modified_at: 2025-12-16
pin: true
---

# 🍊 새로운 JS 문법들

매년 ECMA에서는 JavaScript의 새로운 문법들이 소개된다.
그중에서도 ES2016은 가장 잘 알려진 버전으로, 현재까지 널리 사용되는 많은 문법들이 이 때 등장했다.

물론, JavaScript는 이후로도 꾸준히 업데이트되고 있는데, 연말을 맞아 지식을 정리하고 업데이트하는 겸, 실무에서 유용하게 활용할 수 있을 만한 문법들을 정리해보고자 한다.

## 🔸at()

많은 프로그래밍 언어들은 `[-1]` 처럼 음수 인덱스를 통해 끝에서부터 배열에 접근할 수 있지만, Javascript는 `[]`가 배열과 객체 둘 다에 사용되므로, 키가 `-1`인 property를 가리키게 된다.

이러한 불편함을 해소하기 위해 ES2022에서 at()메서드가 도입되었다!

사용법도 간단하다.

```javascript
const fruits = ["Banana", "Orange", "Apple", "Mango"]
let fruit = fruits.at(2) //Apple
let negativeFruit = fruits.at(-1) //Mango
```

만약 범위를 벗어날 경우 `undefined` 를 반환한다.

at은 또한 string에도 적용 가능하다.

```javascript
"Hello".at(2) // 'l'
"Hello".at(-1) // 'o'
```

## 💖 Promise

이번엔 API를 다루면서 많이 보게 된 프로미스 관련 내용을 알아보자.

api 호출할 때 사용하는 fetch의 경우 이미 Promise를 만들어서 return하기 때문에 직접적으로 Promise를 코드에 명시하는 일이 많지는 않아짔만,
쵝느에 하나의 파이프라인 안에서 여러개의 프로미스를 다룰일이 생기다 보니 익숙해지는 편이 좋겠다는 생각을 했다.

**Promise**란 지금은 결과가 없지만 나중에 결과가 생길 일을 약속하는 객체로,

1. 성공 (resolve(value))
2. 실패 (reject(error))
   결과는 위와 같은 두 가지 만이 존재한다.

### 프로미스 생성하기

프로미스는 아래와 같이 생성할 수 있다. 이 함수의 경우 성공할 경우 resolve를, 실패할 경우 reject를 내뱉으며, 비동기 작업을 시작시키기 위해 콜백을 받는다.

```javascript
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("foo")
  }, 300)
})

promise1.then((value) => {
  console.log(value)
})
```

```javascript
const promise1 = Promise.resolve(3)
const promise2 = 42
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, "foo")
})

Promise.all([promise1, promise2, promise3]).then((values) => {
  console.log(values)
})
```

## Promise.all()

여러 Promise를 **동시**에 실행하고, 하나라도 실패하면 즉시 **전체를 실패**로 만든다

```javascript
Promise.all([p1, p2, p3])
```

- p1 ✅
- p2 ❌ ← 여기서 바로 **전체** 실패
- p3 ⏳ (결과 기다리지 않음)

```javascript
Promise.all([fetch("/a"), fetch("/b"), fetch("/c")])
  .then(([a, b, c]) => {
    // 전부 성공했을 때만 실행
  })
  .catch((err) => {
    // 하나라도 실패하면 전체 실패로 간주. catch가 실행 됨
  })
```

## Promise.allSettled()

모든 Promise가 끝날 때까지 기다리고, 성공/실패 결과를 전부 알려준다

```javascript
Promise.allSettled([p1, p2, p3])
```

다음과 같이 실행되었을 때 만약 P2가 실패했다면, 아래와 같은 값을 반환한다.

```javascript
;[
  { status: "fulfilled", value: "결과값" },
  { status: "rejected", reason: "에러" },
  { status: "fulfilled", value: "결과값" },
]
```

```javascript
const results = await Promise.allSettled([
  fetch("/a"),
  fetch("/b"),
  fetch("/c"),
])

results.forEach((r) => {
  if (r.status === "fulfilled") {
    console.log("성공", r.value)
  } else {
    console.log("실패", r.reason)
  }
})
```

> ⭐ 이러한 특징을 사용해 여러 api가 엮여있는 파일 업로드 + 각 status를 알리는 UX를 구성할 수 있었다.

## 🎁참고 사이트

- <https://www.w3schools.com/js/js_versions.asp>
- <https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/all>
