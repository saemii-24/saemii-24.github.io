---
title: "파이썬 기본 문법 - 클래스 상속과 데코레이터"
author: saemii
categories:
  - JavaScript
tags:
  - [Python]
date: 2024-07-28
last_modified_at: 2024-07-28
pin: true
---

## 📌시작하며

신입 개발자로 일하기 시작하며 업무에 필요한 스택을 틈틈히 공부하고 있다.😌 파이썬도 같은 흐름으로, 백엔드 코드를 읽거나 소통할 때 필요한 파이썬 공부도 시작했다.

파이썬 언어는 인간이 읽기 쉬운 고급언어로 배우기도 쉽다고 한다. 아직 걸음마 단계라 과연 그런지는 차차 공부해봐야 알겠지만😅

일단 기본 문법은 예제도 쳐보고 한 번은 훑어봐서, 자주 사용 되는 문법 위주로 정리해보고자 한다.

## ✅클래스

클래스는 객체 지향 프로그래밍의 핵심으로, 클래스 안에 데이터와 기능을 함께 작성해 하나로 묶을 수 있다.

### ➡️클래스 정의

클래스는 아래와 같이 `class`키워드를 사용해 정의한다.
아래는 Dog란 class를 정의했다.

```python
class Dog:
```

### ➡️생성자

객체가 생성될 때 자동으로 호출되는 특수 메서드로 객체의 초기 상태를 설정할 수 있다. `__init__`을 사용해 정의하면 된다.

```python
class Dog:
  def __init__(self, name):
    self.name= name
```

여기서 self는 메서드가 호출된 객체(즉 생성된 인스턴스)를 가리키는 역할을 한다.

```python
class Dog:
  def __init__(self, name):
    self.name= name

# Dog 클래스를 이용한 dog1이란 인스턴스를 생성
dog1 = Dog("해피")
```

이때 dog1이 Dog를 부르고, 이 상황에서 self는 dog1이 되는 것이다. 이를 바탕으로 self에 dog1을 넣어보자. `dog1.name = name`이 되고, `Dog("해피")`로 name에 "해피"를 넣어서 생성했기 때문에, `dog1.name = "해피"`가 되는 것이다.

### ➡️메서드

아래에서 두가지 메서드를 정의했다. 이름을 받아 문장을 return하는 bark와 지정된 문장이 return되는 cute다. 이때 cute의 경우 self를 직접적으로 이용하지는 않지만 사이에 self를 넣어야 함에 유의한다.

```python
class Dog:
    def __init__(self, name):
        self.name = name  # 인스턴스 변수 설정

    def bark(self):
        return f"우리 {self.name}(은/는) 왕왕 짖어요"
    def cute(self):
        return "우리 강아지는 귀여워요"

dog1 = Dog("해피")
print(dog1.bark())  # 출력: 우리 해피(은/는) 왕왕 짖어요
print(dog1.cute())  # 출력: 우리 강아지는 귀여워요
```

## ✅클래스 상속

클래스 상속이란 기존 클래스를 기반으로 새로운 클래스를 생성할 수 있게 해주는 기능으로, 이를 통해 기존 클래스의 속성, 메서드를 재사용하고 확장하거나 수정할 수 있게 한다.

먼저 아래와 같이 공통 class를 정의해주었다.

```python
class Animal:
  def __init__(self, name):
    self.name = name
    self.age = 5

  def speak(self):
    return "이렇게 말해요"
```

이제 Animal 클래스를 상속해보자.
상속은 `class 지금 만드는 클래스(상속할 클래스):` 로작성한다.

```python
class Cat(Animal):
    def speak(self):
        return f"고양이 {self.name}(은/는) 야옹야옹"
```

이 상황에서 speak 메서드는 Cat 클래스에서 **오버라이드(재정의)** 되었다.

- **오버라이딩:** 기본 클래스에서 정의된 메서드를 파생 클래스에서 다시 정의하여 새로운 동작을 구현하는 것

### ➡️부모 클래스 접근하기

위에서 정의한 부모 믈래스를 다시 살펴보자.

```python
class Animal:
  def __init__(self, name):
    self.name = name
    self.age = 5

  def speak(self):
    return "이렇게 말해요"
```

만약 이때 하위 클래스에서는 `self.age = 5` 가 아니라 `self.age = 7` 로 바꾸고 싶다면 어떻게할까?

이와 같이 부모 클래스의 `__init__`메서드에 접근할 때는 `super` 키워드를 사용하면 된다.

```python
class Animal:
    def __init__(self, name):
        self.name = name
        self.age = 5

class Cat(Animal):
    def __init__(self, name):
        super().__init__(name)  # 부모 클래스의 __init__ 호출
        self.age = 7  # 나이를 7로 변경

animal = Animal("동물")
print(f"{animal.name}은 {animal.age}살이에요")  # 출력: 동물은 5살이에요

cat = Cat("쿠키")
print(f"{cat.name}는 {cat.age}살이에요")  # 출력: 쿠키는 7살이에요

```

위와 같이, `super()`는 현재 클래스의 부모 클래스를 참조하고, 해당 부모 클래스의 메서드나 속성에 접근할 수 있는 방법을 제공하는 키워드다.

`super().__init__(name)` 로 Animal의 name을

- <https://docs.python.org/ko/3/tutorial/classes.html>
- <https://www.tutorialsteacher.com/python/python-class>
