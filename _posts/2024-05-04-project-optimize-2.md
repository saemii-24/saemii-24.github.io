---
title: "최적화 적용(2) - Next Image"
author: saemii
categories:
  - Optimize
tags:
  - [Optimize, Next.js, Project]
date: 2024-05-04
last_modified_at: 2024-05-04
pin: true
---

## 📌시작하며

**(작성중~)**
사실 지금 프로젝트에서 이미지 최적화를 어떻게 하면 좋을지 고민이 많았다.(물론 지금도...)🤔 일단 이미지가 사용되는 전체적인 흐름은 다음과 같다.

<div class="mermaid" markdown="0" >
  graph LR;
  A(사용자 웹 접속, 데이터 요청) --> B(데이터 응답);
  B --> C(Next Image의 src 속성으로 사용);
</div>

사용자가 업로드한 이미지는 supabase의 storage에 업로드 되고, 이 '주소'값을 table에 저장해두었다가, src속성에 활용된다.
배포 후에 프로젝트의 lighthouse 검사를 하니 LCP 최적화가 필요하다고 하여, 고민에 빠지게 되었다.

> 지금 상황에서 어떤 최적화를 할 수 있을까?🤔

일단 프로젝트 내에서 사용되는 대부분의 이미지는 로컬환경에 저장된 이미지가 아닌, 사용자가 업로드 한 이미지에 관련되어 있기 때문에, 어떻게 최적화 해야할지 고민되었고 아래와 같은 몇가지 방법을 떠올렸다.

1. Next/Image를 좀 더 잘 활용할 수 없을까?
2. 사용자가 업로드한 이미지 파일을 경량 파일 형식인 WebP 파일로 변환 할 수 있을까?
3. 사용자가 업로드한 이미지 파일의 비율을 유지한채 적정한 크기로 저장해서 storage에 저장하면 어떨까?

2번 3번은 방법을 검색해보니 canvas API를 사용하면 된다고 하는데 아직 이 부분은 추가학습이 필요하니, 당장 할 수 있는 1번부터 차근차근 진행해보고자 한다.

## ✅next/Image의 Props

| Prop              | Example                                  | Type            | Status     |
| ----------------- | ---------------------------------------- | --------------- | ---------- |
| src               | `src="/profile.png"`                     | String          | Required   |
| width             | `width={500}`                            | Integer (px)    | Required   |
| height            | `height={500}`                           | Integer (px)    | Required   |
| alt               | `alt="Picture of the author"`            | String          | Required   |
| loader            | `loader={imageLoader}`                   | Function        | -          |
| fill              | `fill={true}`                            | Boolean         | -          |
| sizes             | `sizes="(max-width: 768px) 100vw, 33vw"` | String          | -          |
| quality           | `quality={80}`                           | Integer (1-100) | -          |
| priority          | `priority={true}`                        | Boolean         | -          |
| placeholder       | `placeholder="blur"`                     | String          | -          |
| style             | `style={{objectFit: "contain"}}`         | Object          | -          |
| onLoadingComplete | `onLoadingComplete={img => done())}`     | Function        | Deprecated |
| onLoad            | `onLoad={event => done())}`              | Function        | -          |
| onError           | `onError(event => fail())`               | Function        | -          |
| loading           | `loading="lazy"`                         | String          | -          |
| blurDataURL       | `blurDataURL="data:image/jpeg..."`       | String          | -          |
| overrideSrc       | `overrideSrc="/seo.png"`                 | String          | -          |

### ➡️Required Props

이미지 컴포넌트에는 `src`, `width`, `height`, `alt` 속성이 **반드시 필요**하다!

```javascript
import Image from "next/image"

export default function Page() {
  return (
    <div>
      <Image
        src="/profile.png"
        width={500}
        height={500}
        alt="Picture of the author"
      />
    </div>
  )
}
```

## ✅반응형 작업

반응형 작업 하면서, 반드시 필요한 width값과 height 값을 어떻게 지정해야 할지 난감했는데, 공식문서에서 아래와 같은 방법을 제시했다.

### ➡️sizes 사용

```javascript
import Image from "next/image"
import me from "../photos/me.jpg"

export default function Author() {
  return (
    <Image
      src={me}
      alt="Picture of the author"
      sizes="100vw"
      style={{
        width: "100%",
        height: "auto",
      }}
    />
  )
}
```

#### 💟sizes란?

sizes는 미디어 쿼리와 비슷한데, 다양한 브레이크포인트에서 이미지의 사이즈를 지정한다. 따라서, sizes의 값은 fill을 사용할 때, 반응형 크기 이미지의 성능에 큰 영향을 미친다.

sizes 속성은 이미지 성능과 관련해 두 가지의 주요 목적을 제공한다.

- **첫째:** 브라우저가 이미지를 선택할 때, 페이지에서 이미지의 크기를 정확히 알지 못하므로 뷰포트와 동일한 크기 혹은 더 큰 이미지를 선택한다. 즉, fill 속성이 있는 이미지에 sizes 값을 지정하지 않으면 기본값으로 100vw(전체 화면 너비)가 사용된다. 이때 sizes 속성을 사용하면 브라우저에게 이미지가 뷰포트(전체 화면) 보다 작을 수 있다는 것을 알려줄 수 있다.

- **둘째:** sizes 속성은 자동으로 생성된 srcset의 동작을 변경한다. sizes 값이 없는 경우에는 고정 크기 이미지에 적합한 작은 srcset들이 생성된다(1x/2x 등). 하지만 sizes가 정의된 경우, 반응형 이미지에 적합한 srcset이 생성된다.(640w/750w 등). sizes 속성에 뷰포트 너비의 백분율을 나타내는 값(예: 50vw)이 포함된 경우, srcset은 필요 없는 값이 포함되지 않게된다.

```javascript
import Image from "next/image"

export default function Page() {
  return (
    <div className="grid-element">
      <Image
        fill
        src="/example.png"
        sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
      />
    </div>
  )
}
```

이 예제에서 sizes를 살펴보자. 해석은 **미디어 쿼리 처럼 해석**하면 된다!

- `(max-width: 768px) 100vw`: 뷰포트의 최대 너비가 768px 이하인 경우, 이미지 크기는 뷰포트의 100%가 된다.
- `(max-width: 1200px) 50vw`: 뷰포트의 최대 너비가 768px를 초과하고 1200px 이하인 경우, 이미지의 크기는 뷰포트의 절반(50%)이 된다.
- `33vw`: 뷰포트의 최대 너비가 1200px를 초과하는 경우, 이미지의 크기는 뷰포트의 약 1/3(33%)이 된다.

만약 sizes를 사용하지 않으면 기본적으로 100vw가 되므로, 1200px 이상일때도 100vw의 이미지가 사용되어, 불필요한 대용량 이미지 파일을 다운로드해 페이지 로딩 속도를 느리게 할 수 있어 사용성을 떨어뜨릴 수 있다.

#### 💟srcset

#### 💟imageSizes (srcset)

이때 함께 보면 좋은 것을

## 🗂️참고 사이트

- https://nextjs.org/docs/pages/api-reference/components/image
- https://developer.mozilla.org/ko/docs/Web/HTML/Element/img#sizes
