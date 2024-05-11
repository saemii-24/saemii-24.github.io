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

### ➡️sizes를 사용하는 방법

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

### ➡️sizes를 사용하는 방법 (aespect ratio)

반응형 이미지의 올바른 종횡비를 설정하려면, `width`와 `height`에 적절한 너비와 높이를 제공해야 한다.

```javascript
import Image from "next/image"

export default function Page({ photoUrl }) {
  return (
    <Image
      src={photoUrl}
      alt="Picture of the author"
      sizes="100vw"
      style={{
        width: "100%",
        height: "auto",
      }}
      width={500}
      height={300}
    />
  )
}
```

### ➡️fill을 사용하는 방법

fill은 boolean 값을 사용한다! 부모 요소를 채우도록 이미지를 늘리며, 이미지의 너비와 높이를 알 수 없을 때 사용하면 된다.

이때 **이미지 요소**가 자동으로 `position:absolute`가 지정되므로, **부모요소**는 반드시 `position: "relative", position: "fixed", 또는 position: "absolute"` 스타일이 지정되어야 한다.

1. **비율 유지하고 letterbox형식으로 채우기**: `object-fit: "contain"` 추가 설정

2. **이미지를 전체 컨테이너에 비율 유지하며 채우기** : `object-fit: "cover"` 추가 설정, 부모 요소에 `overflow: "hidden"` 스타일이 지정

```javascript
import Image from "next/image"

export default function Page({ photoUrl }) {
  return (
    <div style={{ position: "relative", width: "300px", height: "500px" }}>
      <Image
        src={photoUrl}
        alt="Picture of the author"
        sizes="300px"
        fill
        style={{
          objectFit: "contain",
        }}
      />
    </div>
  )
}
```

## ✅sizes란?

sizes는 미디어 쿼리와 비슷한데, 다양한 브레이크포인트에서 이미지의 사이즈를 지정한다. 따라서, sizes의 값은 fill을 사용할 때, 반응형 크기 이미지의 성능에 큰 영향을 미친다.

sizes 속성은 이미지 성능과 관련해 두 가지의 주요 목적을 제공한다.

- **첫째:** 브라우저가 이미지를 선택할 때, 페이지에서 이미지의 크기를 정확히 알지 못하므로 뷰포트와 동일한 크기 혹은 더 큰 이미지를 선택한다. 즉, fill 속성이 있는 이미지에 sizes 값을 지정하지 않으면 기본값으로 100vw(전체 화면 크기)가 사용된다. 이때 sizes 속성을 사용하면 브라우저에게 이미지가 뷰포트(전체 화면) 보다 작을 수 있다는 것을 알려줄 수 있다.

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

### ➡️Tailwind breakpoint

sizes 작성에 참고하기 위해, 작업할 때 사용했던 break point를 정리하면 다음과 같다.
| class | Media Query |
|-------------|----------------------------------|
| sm | @media (min-width: 640px) |
| md | @media (min-width: 768px) |
| lg | @media (min-width: 1024px) |
| xl | @media (min-width: 1280px) |
| 2xl | @media (min-width: 1536px) |

## ✅srcset

평소에 srcset 속성을 사용하지 않아서, Next/Image 사용후 검사도구로 확인하며, 이건 뭐지? 하고 당황했었는데😅 sizes와 함께 반응형 이미지를 최적화 하는 방법이다.

srcset 은 브라우저에서 선택할 수 있는 이미지 세트와 각 이미지의 크기를 정의한다! 쉽게 이해해보자면, <u>디바이스의 픽셀 밀도와 뷰포트의 크기를 고려</u>해 가장 적절한 이미지를 선택할 수 있도록 여러 선택지를 제공하는 것이다.

예를 들어보자.

```javascript
<img src="image-800w.jpg" alt="예시 이미지" />
```

일반적으로 사용하는 img 태그이다. 이 경우, 뷰포트 크기에 상관없이 `'image-800w'` 라는 이미지가 사용된다.

```javascript
<img
  srcset="image-480w.jpg 480w, image-800w.jpg 800w"
  sizes="(max-width: 600px) 480px,
         800px"
  src="image-800w.jpg"
  alt="예시 이미지"
/>
```

하지만 다음과 같이 사용 될 경우,

- 뷰포트의 너비가 480px 미만이면 image-480w.jpg가 선택된다.
- 뷰포트의 너비가 480px 이상이고, 800px 미만이면 image-800w.jpg가 선택된다.
- 뷰포트의 너비가 800px 이상이라면 브라우저는 해당 픽셀 크기에 가장 가까운 이미지를 선택한다.

📌w는 픽셀 단위의 이미지의 너비를 나타낸다.

## ✅반응형 작업시 추가적인 속성

### ➡️DeviceSizes

만약 사용자의 디바이스 사이즈들을 알고 있다면, 이 속성을 사용해 기기의 breakpoint를 지정할 수 있다. 이 너비는 Next/Image 컴포넌트가 사용자의 기기에 맞는 이미지가 표시되도록 할 때 사용된다. 기본값은 다음과 같다.

```javascript
module.exports = {
  images: {
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
  },
}
```

### ➡️ImageSizes

Next/Image를 사용해본 사람이라면, srcset 속성을 따로 지정하지 않아도 Next/Image가 알아서 이 속성을 적용하는 것을 알 수 있다!

만약 srcset의 속성값을 조정하고 싶으면, `next.config.js` 에서 수정할 수 있다. 기본값은 다음과 같다.

```javascript
module.exports = {
  images: {
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
}
```

### ➡️DeviceSizes & ImageSizes의 연관성

두 내용을 종합해서 이해해보자면 다음과 같다.

1. **deviceSizes**: 기기의 크기를 나타내는 배열. 이때 '기기의 크기'는 사용자의 화면 크기를 고려해 일반적인 디바이스 너비를 지정한다. 디바이스의 너비는 이미지의 최적화된 버전을 선택하는 데 사용된다.

2. **imageSizes**: 이미지의 크기를 나타내는 배열. 뷰포트의 크기에 따라 이미지를 선택하는 데 사용되며, sizes props가 제공된 이미지에만 사용된다. sizes 속성은 이미지의 크기가 뷰포트의 전체 크기보다 작을 때 사용된다.

> sizes 속성은 이미지가 삽입된 요소의 크기나 뷰포트의 크기에 따라 이미지의 크기를 조절하는 데 사용되기 때문에, 이미지의 실제 크기가 뷰포트의 전체 크기보다 작을 때에만 sizes 속성이 의미가 있다.

이 두 개의 값들은 함께 이미지를 적절하게 선택하고 최적화 할 수 있도록 도와준다.

## ✅format

Next/Image의 기본Image Optimization API는 요청의 Accept 헤더를 통해 브라우저가 지원하는 이미지 포맷을 자동으로 감지한다.
`next.config.js`에서 아래와 같이 사용할 format들의 배열을 지정할 수 있는데, 첫 번째 일치 항목부터 브라우저가 사용 가능한 이미지를 사용하게 된다.

```javascript
module.exports = {
  images: {
    formats: ["image/webp"],
  },
}
```

다음과 같이 avif 파일을 사용할 수 있다.

> ⦁ AVIF는 일반적으로 인코딩하는 데 20% 더 많은 시간이 걸리지만, WebP와 비교하여 20% 더 작게 압축되므로, 이미지가 요청될 때 처음은 느릴 수 있지만, 이후 캐시된 요청은 더 빠를 수 있다.
> ⦁ Next.js 앞에 프록시/CDN을 사용하여 자체 호스팅하는 경우 프록시를 구성하여 Accept 헤더를 전달해야 한다.

```javascript
module.exports = {
  images: {
    formats: ["image/avif", "image/webp"],
  },
}
```

## 🗂️참고 사이트

- https://nextjs.org/docs/pages/api-reference/components/image
- https://developer.mozilla.org/ko/docs/Web/HTML/Element/img#sizes
- https://developer.mozilla.org/ko/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images
