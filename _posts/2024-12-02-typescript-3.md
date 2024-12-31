---
title: "Typescript(3) - Polymorphic Components"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript]
date: 2024-12-02
last_modified_at: 2024-12-02
pin: true
---

## 📌시작하며

> 프로그램 언어의 [다형성](<https://ko.wikipedia.org/wiki/%EB%8B%A4%ED%98%95%EC%84%B1_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)>)이란, 프로그램 언어의 각 요소들이 다양한 자료형에 속하는 것이 허가되는 성질을 가리킨다. (위키백과)

어쩌다 들어간 웹사이트에서 Polymorphic Components 관련된 글을 보게 되었다.
Polymorphic(다형성) 이란 단어 자체가 낯설어서, 이게 뭐지? 하고 읽어보던 와중에, 내가 예전에 봤던 인강 선생님이 사용하시던 방식인걸 깨달았다. 심지어 지금 내가 필요로 했던 내용이란것도!

역시 사람은 망각의 동물(...) 🥹이라고 그때도 <i>와~ 좋은 방법이구나!</i> 하고 노션에 정리해두었는데 그 이후로 사용하질 않아서 까맣게 잊어버리고 말았다.. 🙃

지금 업무에 적용하기도 좋은 내용이라, 내용을 꼼꼼히 읽어보았는데, 전체적인 내용 자체는 어렵지 않지만 의외의 복병은 Typescript를 해석하는 것이었다.

그래서 이번 포스팅도 Typescript 카테고리에 넣어두었는데, 이번 기회에 제네릭 개념을 다시 한 번 정리해보면서, Polymorphic Components를 만들어보자.

## ✅Polymorphic Components의 필요성

현재 내가 담당하는 업무 중에, url을 가진 data를 fetch해와서, url인 경우 `<Link>` 태그를 만들고, 없다면 `<div>` 태그를 이용해야 하는 경우가 있다.

둘의 CSS는 같아야 하기 때문에 오로지 HTML 태그를 위해서 아래와 같이 삼항연산자를 써주어야 했다.

```javascript
{
  data.url ? <Link href={data.url}>{data}</Link> : <div>{data}</div>
}
```

해당 예제에서는 간단하게 썼지만 실제 업무에서는 tailwind를 사용하고 있기 때문에 해당 각 element가 굉장히 길어지고, 또 CSS를 바꾸려면 두 태그를 모두 바꿔주어야 하는 것이 상당히 귀찮았다.🤔

이때 Polymorphic Components가 등장한다.
위에서 다형성이 무엇인지 써놓았는데, Polymorphic Components란 간단히 말해 **props로 해당 태그가 무엇인지 지정** 해주고, 내가 지정한 HTML tag 속성을 내려서 필요에 따라 Link로도, div로도 쓸 수 있는 컴포넌트를 말한다.

이것을 이용하면 tailwind class는 한 번만 작성하고, props 내려주는 것을 바꾸어, 필요에 따라 `<Link>`로도, `<div>`로도 만들 수 있게 된다.

## ✅Polymorphic Components 전체 코드

이 코드는 Polymorphic Components를 만드는 코드다.

```typescript
import React from "react"

type PolymorphicProps<C extends React.ElementType> = {
  as?: C
  children: React.ReactNode
} & React.ComponentPropsWithoutRef<C>

const Components = <C extends React.ElementType = "div">({
  as,
  children,
  ...props
}: PolymorphicProps<C>) => {
  const Component = as || "div"
  return <Component {...props}>{children}</Component>
}
```

실제 사용할 때는 다음과 같이 사용할 수 있다.

```typescript
//as로 a 태그를 만들어보자!
<Components as="a" href="http://test.com" />
//실제 렌더링은 이렇게..
<a href="http://example.com"></a>
```

```typescript
//as로 div태그를 만들어보자!
<Components as="div" />
//실제 렌더링은 이렇게..
<div></div>
```

즉 위에서 보았던 예시를 아래와 같이 적을 수 있다.

```typescript
<Components
  as={data.url ? "a" : "div"}
  {...(data.url ? { href: data.url } : {})}
>
  {data.content}
</Components>
```

## ✅Polymorphic Components의 Typescript 해석하기

Polymorphic Components를 만들 때는, typescript를 적극 활용해 보다 안전한 컴포넌트를 만들 수 있다.

사용자가 잘못해서 'divv' 를 내려주거나, button 태그인데 href를 내려주려고 시도하는 것을 막아주기 때문이다.

그럼 먼저 위에서 부터 차례대로 해석해보자.

```typescript
type PolymorphicProps<C extends React.ElementType> = {
  as?: C
  children: React.ReactNode
} & React.ComponentPropsWithoutRef<C>
```

먼저 `type PolymorphicProps`은 PolymorphicProps란 이름의 type을 지정해준다는 뜻이다.
이때 `<C extends React.ElementType>` 은 C라는 사용할 때 결정되는 어떠한 타입(제네릭)을 `React.ElementType`으로 extends(한정한다) 라는 의미이다.

사실 extends의 뜻이 '확장하다' 라는 의미가 있어, 나는 저 부분을 <u>C라는 어떠한 타입에 '더해서' React.ElementType도 가질 수 있다</u> 라고 **오해** 했었다.

하지만 이 extends의 의미를 직역하려 하지 말고, **'한정한다/제약한다'** 라는 말로 해석해보면 훨씬 자연스럽게 이 내용을 이해할 수 있다!

계속해보면, 이제 extends를 이용해 타입이 한정된 C는 React.ElementType인

1. HTML 태그 이름 ('div', 'span', 'a' 등)
2. React 컴포넌트 타입 (MyComponent, MySpan 등)

의 타입을 가질 수 있게 된다.

## 🗂️참고 사이트

- <https://www.freecodecamp.org/news/build-strongly-typed-polymorphic-components-with-react-and-typescript/>
