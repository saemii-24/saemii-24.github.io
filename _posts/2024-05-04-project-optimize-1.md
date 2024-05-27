---
title: "최적화 적용(1) - 폰트 최적화"
author: saemii
categories:
  - Optimize
tags:
  - [Optimize, React, Next.js, Project]
date: 2024-05-04
last_modified_at: 2024-05-04
pin: true
---

## 📌시작하며

지난 포스팅에서 계속 최적화 방식에 대해 알아봤는데 이제 직접 프로젝트에 적용해보려고 한다.😎 일단 가장 간단해보이는(?) 폰트 최적화 부터 시작하기로 했다.

현재 사용하고 있는 폰트 파일을 봤더니 생각 이상으로 용량이 엄청나다!😅 (약 12mb...) 나는 두 가지 언어에 관한 폰트를 사용하고 있기 때문에 용량이 무려 2배!🥲 폰트를 불러오는데도 시간이 꽤 소요되는 것을 확인할 수 있었다.

그럼 이제 폰트 최적화를 시작해보자.🥳

## ✅ttf -> woff 변경하기

현재 사용하고 있던 폰트 확장자는 ttf였다! 😅 일단 경량 파일인 woff 파일을 만들어서, @font-face로 woff 파일을 사용할 수 있는 브라우저의 경우 woff를 사용할 수 있도록 변경하기로 했다. (자세한 내용은 아래에 서술한다.)

## ✅subset 만들기

### ➡️변환 프로그램

서브셋 만드는 방법에서 대표격(?)으로 자주 보았던 프로그램은
**[서브셋 폰트 메이커](https://opentype.jp/subsetfontmk.htm)** 였다.<br/>
제작한 폰트는 ttf이기 때문에 woff와 woff2로 변경해주면 더 좋은데 이건
**[woff 컨버터](https://opentype.jp/woffconv.htm)** 를 이용하면 된다. (ttf 파일을 넣으면 woff, woff2 파일 모두로 변환해준다!)

설치한 다음 PC에서 해당 프로그램이 어디에 설치되어있는지 모른다면 PC 검색기능을 사용해서 일본어로 검색해주면 된다.

**서브셋 폰트 메이커**: サブセットフォントメーカー<br/>
**woff 컨버터**: WOFF コンバータ

(공부한 일본어를 여기에 써먹을 줄은 몰랐는데 왠지 뿌듯🥰)

### ➡️필요한 파일

사실 위의 방법으로 가지고 있던 font 파일을 이용해 subset을 만들고 프로젝트에 적용했더니, 굵기가 모두 light정도로만 표시되는 문제점이 있었다. 따라서, font 굵기를 각각의 파일을 이용해 관리하기로 결정했다.

프로젝트에서 사용한 tailwind의 font 굵기는 normal, medium, semibold, bold, extrabold, black 이고 font-weight 값으로 환산하면, 다음과 같다.

| class          | font-weight |
| -------------- | ----------- |
| font-normal    | 400         |
| font-medium    | 500         |
| font-semibold  | 600         |
| font-bold      | 700         |
| font-extrabold | 800         |
| font-black     | 900         |

### ➡️서브셋을 만들기 위한 글자 모음집

프로젝트에 적용 된 폰트 unicode-range의 경우 한글은 한글만, 나머지의 경우 일본어 폰트를 적용하게 지정해두었다. (일본어의 경우 히라가나, 가타카나, 한자 등 지정할 범위가 많아서 번거로웠기 때문)

따라서 한글은 자주 쓰는 한글 글자만 subset으로 지정해도 되었고, 일본어의 경우 알파벳, 상용 일본어 한자를 포함한 문자, 숫자, 특수문자 등을 모두 포함해야 할 필요가 있었다.

따라서 한글폰트 → [KS X 1001의 완성형 한글 2350자](https://ko.wikipedia.org/wiki/KS_X_1001)<br/>
일본어폰트 → [상용 일본어 한자, 히라가나, 가타카나, 숫자, 알파벳, 자주쓰는 특수문자](https://qiita.com/flatsato/items/2872aa863c20818982c8)

를 이용해 서브셋 파일을 제작했다.

## ✅font-face 적용

font-face는 위에서 알아본 것과 같이 400~900대의 폰트를 지정해주었다.

```css
@font-face {
  font-family: "noto";
  font-weight: 400;
  src: url("../../public/font/NotoSansKR-Regular-Subset.woff2") format("woff2"),
    url("../../public/font/NotoSansKR-Regular-Subset.woff") format("woff"),
    url("../../public/font/NotoSansKR-Regular-Subset.ttf") format("truetype");
  unicode-range: U+AC00-D7A3;
}
@font-face {
  font-family: "noto";
  font-weight: 500;
  src: url("../../public/font/NotoSansKR-Medium-Subset.woff2") format("woff2"), url("../../public/font/NotoSansKR-Medium-Subset.woff")
      format("woff"),
    url("../../public/font/NotoSansKR-Medium-Subset.ttf") format("truetype");
  unicode-range: U+AC00-D7A3;
}
@font-face {
  font-family: "noto";
  font-weight: 600;
  src: url("../../public/font/NotoSansKR-SemiBold-Subset.woff2") format("woff2"),
    url("../../public/font/NotoSansKR-SemiBold-Subset.woff") format("woff"),
    url("../../public/font/NotoSansKR-SemiBold-Subset.ttf") format("truetype");
  unicode-range: U+AC00-D7A3;
}
@font-face {
  font-family: "noto";
  font-weight: 700;
  src: url("../../public/font/NotoSansKR-Bold-Subset.woff2") format("woff2"), url("../../public/font/NotoSansKR-Bold-Subset.woff")
      format("woff"),
    url("../../public/font/NotoSansKR-Bold-Subset.ttf") format("truetype");
  unicode-range: U+AC00-D7A3;
}
@font-face {
  font-family: "noto";
  font-weight: 800;
  src: url("../../public/font/NotoSansKR-ExtraBold-Subset.woff2") format("woff2"),
    url("../../public/font/NotoSansKR-ExtraBold-Subset.woff") format("woff"),
    url("../../public/font/NotoSansKR-ExtraBold-Subset.ttf") format("truetype");
  unicode-range: U+AC00-D7A3;
}
@font-face {
  font-family: "noto";
  font-weight: 900;
  src: url("../../public/font/NotoSansKR-Black-Subset.woff2") format("woff2"), url("../../public/font/NotoSansKR-Black-Subset.woff")
      format("woff"),
    url("../../public/font/NotoSansKR-Black-Subset.ttf") format("truetype");
  unicode-range: U+AC00-D7A3;
}
```

## ✅결과

최종적으로, 유저가 첫페이지 진입시 다운로드 되는 파일은

> 한글 폰트 - Regular, Medium, Extraboold
> 일본어 폰트 - Regular, Medium, Extrabold, Black

이렇게 7가지이고, woff2일 경우, 한글폰트는 140KB이하, 일본어 폰트는 한 파일당 500KB 이하로 줄일 수 있었다. 최초에 사용한 파일의 경우 모든 폰트 굵기 + 모든 단어가 포함된 파일이라 한 파일당 12mb 이상이었는데, 서브셋과 경량화 된 파일을 이용해 용량을 훨씬 줄일 수 있었다!🥹<br/>
속도노 눈에띄게 개선되었고, lighthouse 점수도 15점 가량 개선되었다.. (물론 아직도 갈 길이 멀었다...🥲)

## 🗂️참고 사이트

- <https://ko.wikipedia.org/wiki/KS_X_1001>
- <https://qiita.com/flatsato/items/2872aa863c20818982c8>
