---
title: "파이썬 기본 문법 - 람다 함수"
author: saemii
categories:
  - JavaScript
tags:
  - [Python]
date: 2024-08-12
last_modified_at: 2024-08-12
pin: true
---

## 📌시작하며

최근 파이썬의 탄탄한(?) 기초를 다지고, 앞으로 사용하게 될 파이썬에 대비하고자 인프런 강의를 듣고 있다.
수업에서 안내하는 예제도 공부해야 하지만, 내가 잘 모르거나 이해가 잘 안되는 부분은 따로 찾아보면서 학습하는게 좋으니, 블로그에 추가로 정리를 하고자 한다. 🤗

## ✅람다 함수

람다 함수는 Python에서 익명 함수를 정의할 때 사용하는 구문으로, `lambda` 키워드를 사용해 정의할 수 있다.

기본 문법은 다음과 같다.
한국어로 번역해보면, `lambda(키워드) 함수에 전달 될 인자: 계산된 값을 표현할 표현식`이다.

```python
lambda arguments: expression
```

### ➡️사칙연산

간단하게 숫자를 더해보자.
위에 작성된 것 처럼, lambda(키워드) 를 먼저 작성한 뒤, 함수에 전달 될 인자 `x, y`를 작성하고, `x + y` 를 작성해준다.

```python
add = lambda x, y: x + y
print(add(1, 2))  # 출력: 3
```

이를 응용해 다음과 같은 결과값을 얻을 수 있다.

```python
result = lambda x, y: x * y + 3
print(result(1, 2))  # 출력: 5
```

## ✅list comprehension

리스트 컴프리헨션은 새로운 리스트를 간단히 만들 수 있는 방법이다.
구문은 다음과 같다.

```python
newlist = [expression for item in iterable if condition == True]
```

좀 복잡한데 이걸 풀어서 한국어로 써보자면,

- newlist = 새로운 리스트
- expression = 리스트의 각 항목에 적용할 표현식
- item = iterable의 각 항목
- iterable = iterable 객체 (반복할 원본 리스트 등)
- condition = 조건 (조건이 True일 떄 실행된다.)

### ➡️예제

```python
numbers = [1, 3, 5, 7, 9]

# 리스트 컴프리헨션을 사용해 각 요소에 2를 곱해 새로운 리스트를 생성한다.
double = [x * 2 for x in numbers]

print(double)  # 출력: [2, 6, 10, 14, 18]
```

하나씩 끊어서 이해해보면 `double`이라는 새로운 리스트를 만드는데,
각각의 항목에 `x * 2` 2를 곱할 것이며, x는 numbers(기존 리스트) 각각의 항목에 해당함을 알 수 있다.

현재는 모든 항목에 2를 곱하기 때문에 조건이 생략되었지만 아래와 같이 조건을 추가할수도 있다.

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]

# 홀수인 요소를 선택해 각 요소에 2로 곱한 새로운 리스트를 생성한다.
oddDouble = [x * 2 for x in numbers if x % 2 != 0]

print(oddDouble)  # 출력: [2, 6, 10, 14, 18]
```

여기서는 **홀수** 라는 `x % 2 != 0` 조건이 추가되었다.

### ➡️list comprehensio과 lambda

리스트 컴프리헨션과 람다 함수를 함께 사용하면 리스트에서 원하는 요소를 간단하게 변경할 수 있다.

```python
numbers = range(11)  # 0부터 10까지의 숫자를 생성하는 이터러블 객체

# 조건에 따라 각 요소를 다르게 변형하여 새로운 리스트 생성
changeNumbers = [
    (lambda x: x + 10 if x % 3 == 0 else (x - 20 if x % 5 == 0 else x))(x)
    for x in numbers
]

print(changeNumbers)  # 출력: [0, 1, 2, 13, 4, -15, 6, 7, 8, -11, 10]
```

위의 예제를 이해하기 위해서는 python **삼항연산자**를 함께 이해해야 된다.
(자바스크립트랑 순서가 달라서 혼동하기 쉽다 주의하자😅)

```python
#조건이 True이면 값1을, False이면 값2를 반환함
값1 if 조건 else 값2
```

이제 위에 예제를 이해하기 위한 사전준비는 모두 마쳤다! 끊어서 생각해보자.
`(x - 20 if x % 5 == 0 else x)` 만약 x가 5의 배수라면 `x - 20` 그렇지 않다면 x를 반환한다.
`x + 10 if x % 3 == 0 else x` 만약 x가 3듸 배수라면 `x + 10` 그렇지 않다면 x를 반환한다.

## 🗂️참고 사이트

- <https://www.w3schools.com/python/python_lambda.asp>
- <https://www.w3schools.com/python/python_lists_comprehension.asp>
