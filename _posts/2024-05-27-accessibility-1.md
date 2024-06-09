---
title: "웹 접근성(1) - Accessibility 점수 개선하기"
author: saemii
categories:
  - Optimize
tags:
  - [Optimize, Next.js, Project]
date: 2024-05-27
last_modified_at: 2024-05-27
pin: true
---

## 📌시작하며

프로젝트 최적화 작업을 마무리하고.. 이번엔 접근성(Accessibility) 점수를 개선해보고자 한다. 😎 개인적으로 접근성 하면 생각나는 말이 있다.

내가 한창 수업이나 과제에 아이패드를 활용하던 중에 '손쉬운 사용'기능이 있다는걸 알게되었고, 사용법을 찾아보던 중 한 유튜브 영상을 접하게되었는데, 그때 출연자가 이 기능을 설명하면서

> 기술의 발전은 사용자 모두를 고려하지 않으면 결국 누군가에겐 차별이 된다.

라고 말씀하셨는데(시간이 좀 지나다 보니 정확한 문장이 아닐 수도 있지만...)그 때도, 그리고 지금도 많은 생각을 하게 하는 문장이 아닌가 싶다.

프론트엔드 개발자의 필수 덕목 중 하나가 '사용자 친화적으로 개발한다.' 인데, 이 말에도 반드시 내포되어야 하는건 <u>'모든' 사용자를 고려해야 한다</u> 라는 것이다.

어쩌면 저 말이 개발자가 되고 싶다는 생각에 밑거름이 되어주지 않았을까 하는 생각도 든다.

어려서부터 컴퓨터로 그림을 그리거나, 글을 쓰거나, 만들거나, 내가 상상했던 무언가를 꺼내어 표현하는 것을 좋아했던 나는, 내가 만드는 무언가가 누군가에게 '도움이 될 수 있는', 즉 어떠한 '가치'를 담고 싶었다.

그리고 프론트엔드 개발자는 다양한 사용자를 고려하여, 모든 사람들이 무언가의 기술(서비스)을 충분히 사용할 수 있도록 이끄는 사람이자, 내가 생각하는 가치를 표현할 수 있는 일이라고 생각했고, 지금도 그러한 일을 할 수 있는 사람이 되기 위해 노력중이다.

쓰다보니 말이 길어졌는데, 어쨌든! 이렇게 글로 생각을 정리하다보니 웹 접근성에 대해 소홀히 했던 것에 대해 반성하는 한 편, 프론트엔드 개발자가 되고 싶었던 이유를 다시 한 번 생각하게 되었다.😎

그럼 서론은 여기서 끝내고, 다시 원래의 목적으로 돌아와, 접근성 점수를 개선해보자!🎉

## ✅aria 속성

> - Buttons do not have an accessible name
> - Links do not have a discernible name

두 가지 경고문을 해결해보자!

### ➡️aria 속성 종류

ARIA 속성이란 **Accessible Rich Internet Applications**의 약자로, 웹 콘텐츠와 웹 애플리케이션의 접근성을 높이기 위한 역할을 한다.

ARIA는 HTML을 보충해 공식문서의 설명에 따르면, aria 속성을 이용했던 많은 부분이 <u>HTML5에서 시멘틱 태그 등으로 통합되었기 때문에 그것을 우선시 하라</u>고 안내하고 있다!

### ➡️aria 속성 종류

aria 속성은 여러가지가 있지만 주요 속성들만 정리해보았다.

**📌aria-label**<br/>
요소에 레이블을 제공해 스크린 리더가 해당 요소의 목적을 설명하게 한다.

```html
<button aria-label="닫기">X</button>
```

위에 서술한 에러들도 aria-label을 이용해 해결했다.

**📌aria-labelledby**<br/>
다른 요소의 텍스트를 사용해 현재 요소에 레이블을 지정한다.

```html
<div id="dialog" role="dialog" aria-labelledby="레이블 지정">
  <h2 id="레이블 지정">Dialog의 제목</h2>
  <p>Dialog의 내용</p>
</div>
```

이 예제에서는 `<div>` 요소의 레이블을 `<h2>` 요소의 텍스트로 지정하고 있다.

다이얼로그는 팝업 창이나 대화상자를 말하는데 사용자가 어떤 작업을 수행하는 도중 다이얼로그 창이 열릴 경우, 시각적으로는 `h2`로 제목이 바로 보이지만, 보조 기술을 사용하는 사용자의 경우 이 다이얼로그가 무엇을 뜻하는지 정확히 알기 어렵기 때문에, 이 다이얼로그가 무엇인지 알기 위해 제목이 필요하다!

📌**aria-describedby**<br/>
요소에 대한 설명을 제공하는 다른 요소를 지정한다.

```html
<input type="text" aria-describedby="설명요소 지정" />

<p id="설명요소 지정">Enter your full name.</p>
```

이 속성을 사용하면 스크린 리더가 지정된 설명 요소의 내용을 읽어주어 사용자가 현재 요소의 목적이나 사용 방법을 더 잘 이해할 수 있다.

📌**aria-expanded**<br/>
요소가 확장되었는지 축소되었는지 나타낸다. (아래 예제 참조)

📌**aria-controls**<br/>
현재 요소에 의해 제어되는 다른 요소를 지정한다.

```html
<button aria-expanded="false" aria-controls="메뉴">메뉴</button>

<ul id="메뉴" hidden>
  <li>1번</li>
  <li>2번</li>
</ul>
```

aria-expanded 속성은 boolean 값을 가진다.

- aria-expanded="true": 요소가 확장된 상태
- aria-expanded="false": 요소가 축소된 상태

이 예제에서는 aria-controls를 통해 `button`이 `ul` 태그를 컨트롤 하고 있음을 나타내며, aria-expanded를 통해 현재 이 요소가 축소되어 있음을 나타낸다.

만약, 사용자가 button을 클릭해 이 ul태그를 열고 닫는다면 보조기술을 사용하는 사용자가 현재 요소가 확장/축소 된 상황을 알 수 있도록 **aria-expaneded 값도 적절히 대응**되게 해주어야 함에 주의한다.

📌**aria-hidden**<br/>
아이콘이나 이미지와 같이 디자인을 위한 콘텐츠로, 굳이 보조 기술에서 요소를 읽을 필요가 없는 경우 보조 기술이 이 콘텐츠에 접근하는 것을 막는다.

```html
<div aria-hidden="true">
  이 요소는 보조기술에서 숨겨지고, 시각적으로는 숨겨지지 않는다.
</div>
```

`aria-hidden="true"`는 포커스를 받을 수 있는 요소에 사용하지 않고, 포커스 가능한 요소의 부모 또는 조상 요소에 추가하지 않는다!

## ✅role 속성

aria roles은 콘텐츠에 **의미**를 제공해 스크린 리더나 기타 도구가 해당 유형에 부합하는 방식으로 상호 작용을 표시하고, 지원할 수 있게 해준다!

기본적으로 HTML의 시멘틱 태그는 각각의 역할이 있다. (`input type="radio"`등...) 하지만 `div`나 `span`같은 비시멘틱 요소는 역할이 없다. 이때 role 속성이 이러한 태그들에게 <u>의미를 제공</u>하여 보조 기술 사용자들이 해당 엘리먼트를 이해하는데 도움을 준다.

role을 지정할 때는 element안에 `role="role type"`을 작성해주면 된다.

주의할점은 모든 요소에 이런 role을 사용할 필요가 없다는 거다. 이미 HTML5에서는 많은 시멘틱 태그를 가지고 있기 때문에, 처음부터 시멘틱 마크업을 잘 하면 굳이 이런 role을 사용하지 않아도 충분히 의미를 전달 할 수 있다. (MDN 사이트에서도 시멘틱 태그를 활용할 것을 권장한다.)

즉 아래에서는 시멘틱 태그로 대체하기 어려운 role을 위주로 정리한다.

#### 💟문서 구조 역할

📌**toolbar**<br/>
드롭다운 메뉴와 같은 도구 모음으로, 3개 이상의 컨트롤을 그룹화하는 데만 사용한다!

```html
<div role="toolbar" aria-label="언어를 선택하는 툴바">
  <button>한국어</button>
  <button>English</button>
  <button>日本語</button>
</div>
```

📌**tooltip**<br/>
툴팁은 요소가 포커스 or 호버 되는 경우 해당 요소에 대한 컨텍스트 정보를 제공하는 요소로, 레이블이 없는 콘텐츠에 접근할 경우, 도구 사용 팁을 제공하는 일종의 텍스트 풍선이다.

```html
<button aria-describedby="tooltip-btn">마우스를 올리세요</button>
<div role="tooltip" id="tooltip-btn">추가 설명을 나타냅니다.</div>
```

📌**feed**<br/>
feed는 동적으로 스크롤 가능한 문서 목록으로, 예를 들어 뉴스 피드, Facebook, Instagram과 같은 소셜 미디어 피드 또는 전자상거래 페이지의 관련된 제품 목록 등이 있다.

문서 수를 알고 있는 경우 문서 자체에 `aria-setsize`를 설정하지만, 총 개수가 매우 많거나 무한이거나 자주 변경되는 경우에는 `aria-setsize="-1"`을 설정해 피드 크기를 알 수 없음을 표시하면 된다.

```html
<section role="feed" aria-busy="false">
  <article aria-posinset="427" aria-setsize="-1">…</article>
  <article aria-posinset="428" aria-setsize="-1">…</article>
  <article aria-posinset="429" aria-setsize="-1">…</article>
</section>
```

📌**math**<br/>
콘텐츠가 수학적 표현을 나타내는 것을 알려준다. 만약 수학 식을 표현하는 이미지를 사용해야 한다면 role="math"와 함께 이 표현식을 음석으로 설명할 수 있는 레이블을 지정해주어야 한다.

```html
<div role="math" aria-label="a^{2} + b^{2} = c^{2}">
  a<sup>2</sup> + b<sup>2</sup> = c<sup>2</sup>
</div>
```

📌**presentation/none**<br/>
보조기술이 특정 웹 element를 무시하도록 하는 설정이다. 이 역할은 element가 레이아웃 목적으로만 사용되고, 접근성 기술에 의해 읽히지 않도록 할 때 사용된다.

이때 이 role이 적용된 자식 요소도 접근성 트리에서 제거 되므로 중요한 콘텐츠가 포함 된 경우 사용에 유의한다.

```html
<span role="none">스크린 리더는 이 span 내용을 읽지 않는다.</span>
```

📌**note**<br/>
주석이나 부가 설명등 중요한 추가 정보를 제공하는데 사용된다. 스크린 리더는 이 요소르 읽을 때 중요한 정보임을 사용자에게 알려준다.

중요한 정보임을 알리는 만큼, 무분별하게 사용하지 않도록 한다.

```html
<p role="note" class="hilitebox">중요한 정보가 들어간다.</p>
```

<!-- #### 💟위젯 역할

📌**scrollbar**<br/>
📌**searchbox**<br/>
📌**seperator**<br/>
📌**slider**<br/>
📌**spinbutton**<br/>
📌**switch**<br/>
📌**tab**<br/>
📌**tabpanel**<br/>
📌**treeitem**<br/>
📌**combobox**<br/>
📌**menu**<br/>
📌**menubar**<br/>
📌**tablist**<br/>
📌**tree**<br/>
📌**treegrid**<br/> -->

#### 💟위젯 역할

## ✅ul태그와 li태그 순서와 형식지키기

> - Lists do not contain only `<li>` elements and script supporting elements (`<script>` and `<template>`).
> - List items (`<li>`) are not contained within `<ul>`, `<ol>` or `<menu>` parent elements.

header와 footer를 만들때 사용했언 `ul` `li`태그에서 다음과 같은 경고가 발생했다!

이 문제는 마크업 실수에서 온다....😅 해결법은 간단하다.

1. `<ul>` 또는 `<ol>`태그의 바로 첫 자식 요소에는 `<li>`태그만 사용한다. (`<div>`요소 등 사용 하지 않음)
2. `<li>` 안에는 다른 HTML 요소들을 포함할 수 있다. 예를 들어, `<li>` 안에 `<img>` 태그, `<a>` 태그 등 사용도 가능!

이 문제가 발생한 이유는 `<ul>` 태그 바로 아래 자식 요소에 `<div>` 요소를 사용해서였다.. 이 부분을 수정하니 간단하게 해당 오류가 해결되었다.

## ✅명도 대비

> Background and foreground colors do not have a sufficient contrast ratio.

이 경고는 배경과 텍스트의 명도 대비가 적절한 수준 이상으로 이루어지지 않았기 때문이다.

색약, 색맹, 저시력 사용자는 명암 대비가 낮은 경우 텍스트와 배경을 구별하여 읽기 어렵기 때문에, 충분한 명암 대비를 주어야 한다.

### ➡️WCAG 2.1 지침

> WCAG 2.1(Web Content Accessibility Guidelines 2.1) 웹 콘텐츠 접근성 가이드라인

WCAG 2.1은 다음과 같은 기준을 제시한다.

| 수준       | 설명                                                                     |
| ---------- | ------------------------------------------------------------------------ |
| A (최소)   | 모든 웹사이트에 필수적인 기준                                            |
| AA (중간)  | 대부분의 접근성 전문가가 권장하는 수준                                   |
| AAA (최고) | 더 엄격한 요구사항을 충족해야 함. <br/>모든 콘텐츠 적용이 어려울 수 있음 |

1. 텍스트와 배경 간의 최소 대비 비율은 **4.5:1**이어야 한다.
2. 텍스트가 18pt 또는 Bold 14pt 이상일 경우 **3:1**의 명도 대비가 허용된다.

## 🗂️참고 사이트

- <https://dequeuniversity.com/rules/axe/4.9/color-contrast>
- <https://accessibleweb.com/color-contrast-checker/>
- <https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA/Attributes/aria-label>
- <https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA>
- <https://w3c.github.io/using-aria/#aria-states-and-properties-aria-attributes>
- <https://accessibility.naver.com/acc/guide_04>
- <https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles>
