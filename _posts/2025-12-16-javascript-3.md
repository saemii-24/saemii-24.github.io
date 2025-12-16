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

## 💁‍♀️ 마무리

지금까지 낯설지만 편리한 새로운 syntax들에 대해 알아보았다. 재미이쓴
그렇다고 여기 정리된 모든 기능을 다 쓸 수 있는 것은 아니다. 최신 문법일 수록 브라우저 호환성을 확인하고 작성해야 함에 유의하자.

## 🎁참고 사이트

- <https://www.w3schools.com/js/js_versions.asp>
