---
title: "Lighthouse에 대해 알아보자"
author: saemii
categories:
  - Performance
tags:
  - [Performance, Web]
date: 2024-02-18
last_modified_at: 2024-02-18
pin: true
---

## 📌시작하며

웹 애플리케이션에서 성능 측정은 어떻게 할까?
이번 글에서는 내 나름대로 성능지표와 Lighthouse에 대해 정리하고, 활용해볼 수 있는 자료를 만들어보고자 한다.

## ✨Lighthouse

> 이 내용은 [Chrome for Developers](https://developer.chrome.com/docs/lighthouse/overview?hl=ko) 를 참고해 작성한다.

Lighthouse는 웹페이지 품질을 개선하기 위한 오픈소스 자동화 도구다.
성능, 접근성, 프로그레시브 웹 앱, 검색엔진 최적화 등을 한 눈에 살펴볼 수 있다.
Lighthouse를 확인할 수 있는 방법에는 여러가지가 있는데, 개인적으로 F12 개발자 도구에서 확인하는 방법과 Chrome 확장 프로그램을 이용하는 것이 가장 편리했다.

## 📝Lighthouse 검사 결과

Lighthouse 분석을 마치면 수치화 되어 나타내는 지표는 다음과 같다.

- **Performance**👈
- Accessibility
- Best Practices
- SEO
- PWA

## 💻Performance

이 중에서 접근성은 다른 포스트에서 다루기로 하고, Performance를 집중적으로 살펴보자!

이 점수는 Lighthouse가 측정하는 점수의 가중치 평균을 이용한다. 즉 가중치가 높은 측정항목이 전체 실적 점수에 더 큰 영향을 미치는 것이다. 가중치는 다음과 같다.

> 24년 3월 12일에 INP가 추가될 예정이므로, 해당 내용도 함께 작성한다.

<table>
  <tr>
    <th>감사</th>
    <th>가중치</th>
  </tr>
  <tr>
    <td>
    First Contentful Paint<br/>
    콘텐츠가 포함된 첫 페인트</td>
    <td>10%</td>
  </tr>
  <tr>
    <td>Speed Index<br/>속도 색인</td>
    <td>10%</td>
  </tr>
  <tr>
    <td>Largest Contentful Paint<br/>최대 콘텐츠 렌더링 시간</td>
    <td>25%</td>
  </tr>
  <tr>
    <td>Total Blocking Time<br/>총 차단 시간</td>
    <td>30%</td>
  </tr>
  <tr>
    <td>Cumulative Layout Shift<br/>레이아웃 변경 횟수</td>
    <td>25%</td>
  </tr>
</table>

### ✅FCP 콘텐츠가 포함된 첫 페인트

FCP(First Contentful Paint)는 사용자가 페이지로 이동한 후 브라우저에서 첫 번째 DOM 콘텐츠를 렌더링하는 데 걸리는 시간을 측정한다. 이 시간이 1.8초 이하면 'Good' 으로 판단한다. 3.0초가 넘어가면 'POOR'로 판단한다.

<table>
  <tr>
    <th>FCP 시간(초)</th>
    <th>색 구분</th>
  </tr>
  <tr>
    <td>0~1.8</td>
    <td>녹색(빠름)</td>
  </tr>
  <tr>
    <td>1.8~3</td>
    <td>주황색 (보통)</td>
  </tr>
  <tr>
    <td>3 초과</td>
    <td>빨간색 (느림)</td>
  </tr>
</table>

#### FCP 개선 방법

- 렌더링 차단 리소스 제거
- CSS 축소
- 사용하지 않는 CSS 삭제
- 사용하지 않는 자바스크립트 삭제하기
- 필수 출처에 미리 연결
- 서버 응답 시간 (TTFB) 단축
- 여러 페이지 리디렉션 피하기
- 주요 요청 미리 로드
- 방대한 네트워크 페이로드 피하기
- 효율적인 캐시 정책을 사용하여 정적 애셋 제공
- 과도한 DOM 크기 피하기
- 중요한 요청 체이닝 피하기
- 웹폰트가 로드되는 동안 텍스트가 계속 표시되도록 하기
- 요청 수를 낮게 유지하고 전송 크기를 작게 유지

> 각 항목별 자세한 사항은 [이 곳](https://web.dev/articles/fcp?hl=ko)을 참조한다.

### ✅Speed Index 속도색인

속도 지수는 페이지 로딩 중 콘텐츠가 시각적으로 얼마나 빨리 표시되는지를 측정한다. Lighthouse는 먼저 브라우저에서 페이지가 로딩되는 동영상을 캡처하여 프레임 사이의 시각적 진행 상황을 계산한다. 그 다음 Speedline Node.js 모듈을 사용하여 속도 지수 점수를 생성한다.

<table>
  <tr>
    <th>Speed Index
(in seconds)</th>
    <th>색 구분</th>
  </tr>
  <tr>
    <td>0~3.4</td>
    <td>녹색(빠름)</td>
  </tr>
  <tr>
    <td>3.4~5.8</td>
    <td>주황색 (보통)</td>
  </tr>
  <tr>
    <td>5.8 초과</td>
    <td>빨간색 (느림)</td>
  </tr>
</table>

#### Speed Index 개선 방법

- 메인 스레드 작업 최소화
- 자바스크립트 실행 시간 단축
- 웹폰트 로드 중 텍스트가 계속 표시되도록 보장

> Speed Index 개선 방법의 자세한 사항은 [이 곳](https://developer.chrome.com/docs/lighthouse/performance/speed-index)을 참조한다.

### ✅Total Blocking Time 총 차단 시간

TBT는 마우스 클릭, 화면 탭 또는 키보드 누름과 같은 사용자 입력에 대해 페이지가 응답하지 못하도록 차단된 총 시간을 측정한다. 이 합계는 첫 번째 Contentful Paint와 Time to Interactive 사이의 모든 긴 작업의 차단 부분을 더해 계산된다.
50밀리초 이상 실행되는 모든 작업은 긴 작업이므로, 50밀리초 이후의 시간이 차단 부분이다. 예를 들어 Lighthouse가 70밀리초 길이의 작업을 감지하면 차단 부분은 20밀리초가 된다.

<table>
  <tr>
    <th>TBT time
(in milliseconds)</th>
    <th>색 구분</th>
  </tr>
  <tr>
    <td>0~200</td>
    <td>녹색(빠름)</td>
  </tr>
  <tr>
    <td>200~600</td>
    <td>주황색 (보통)</td>
  </tr>
  <tr>
    <td>600 초과</td>
    <td>빨간색 (느림)</td>
  </tr>
</table>

#### Total Blocking Time 개선 방법

TBT를 해결하기 위해서는 긴 작업의 근본 원인을 진단해야 한다. 일반적으로 작업이 오래 걸리는 이유는 불필요한 자바스크립트 로딩, 구문 분석 또는 실행 등이다. 이를 해결하기 위해 다음과 같은 방법을 실행할 수 있다.

- 코드 분할
- 사용하지 않는 코드 제거

> Total Blocking Time 개선 방법의 자세한 사항은 [이 곳](https://web.dev/articles/long-tasks-devtools?hl=ko#what_is_causing_my_long_tasks)을 참조한다.

### ✅Largest Contentful Paint 최대 콘텐츠 렌더링 시간

LCP는 뷰포트에서 가장 큰 콘텐츠 요소가 화면에 렌더링되는 시점을 측정한다. 이는 페이지의 주요 콘텐츠가 사용자에게 표시되는 시점을 대략적으로 나타내는 것이다.

<table>
  <tr>
    <th>LCP time
(in seconds)</th>
    <th>색 구분</th>
  </tr>
  <tr>
    <td>0~2.5</td>
    <td>녹색(빠름)</td>
  </tr>
  <tr>
    <td>2.5~4</td>
    <td>주황색 (보통)</td>
  </tr>
  <tr>
    <td>4 초과</td>
    <td>빨간색 (느림)</td>
  </tr>
</table>

#### Largest Contentful Paint 개선 방법

Largest Contentful Paint API에 지정된 대로 최대 Contentful Paint에 고려되는 요소 유형은 다음과 같다. 아래내용을 고려하여 LCP를 개선할 수 있다. 예를들어, LCP가 이미지라면, 이미지를 불러오는 타이밍을 4단계로 나눌 수 있는데, 어떤 단계가 가장 오래 걸리는지 파악하고, 최적화 하면 된다.

LCP와 관련 된 요소는 다음과 같다.

- `<img>` 요소
- `<svg>` 요소 내의 `<image>` 요소
- 포스터 이미지가 있는 `<video>` 요소 (포스터 이미지 로드 시간이 사용됨)
- url() 함수를 사용하여 로드된 배경 이미지가 있는 요소(CSS 그라데이션과 반대)
- 텍스트 노드 또는 다른 인라인 수준 텍스트 요소 하위 요소를 포함하는 블록 수준 요소
- `<video>` 요소 자동재생을 위해 페인트된 첫 번째 프레임
  애니메이션 GIF와 같은 애니메이션 이미지 형식의 첫 번째 프레임

> Largest Contentful Paint 개선 방법의 자세한 사항은 [이 곳](https://web.dev/articles/lcp?hl=ko#what-is-lcp)을 참조한다.

### ✅Cumulative Layout Shift 레이아웃 변경 횟수

레이아웃 변경 횟수(CLS)는 사용자가 <u>의도치 않은</u> 레이아웃 이동을 경험하는 빈도를 정량화하고, 시각적 안정성을 측정하는 데 중요한 사용자 중심 지표다. CLS가 낮을수록 페이지가 쾌적하다는 것을 의미한다.

(의도치 않은 레이아웃 변경이란, 특정 버튼을 누르려고 했는데 갑자기 레이아웃이 변경되어 의도치 않은 광고를 클릭하거나 하는 상황을 말한다.)

<table>
  <tr>
    <th>CLS</th>
    <th>색 구분</th>
  </tr>
  <tr>
    <td>0~0.1</td>
    <td>좋음</td>
  </tr>
  <tr>
    <td>0.1~0.25</td>
    <td>개선 필요</td>
  </tr>
  <tr>
    <td>0.25 초과</td>
    <td>나쁨</td>
  </tr>
</table>

#### Cumulative Layout Shift 개선 방법

먼저, 잘못된 CLS의 가장 일반적인 원인은 다음과 같다. 이러한 레이아웃 변경의 원인을 파악하고, 개선하는 것이 중요하다.

- 크기가 없는 이미지
- 크기가 없는 광고, 삽입, iframe
- 크기 없이 광고, 삽입, iframe과 같이 동적으로 삽입된 콘텐츠

> Cumulative Layout Shift 개선 방법의 자세한 사항은 [이 곳](https://web.dev/articles/optimize-cls?hl=ko)을 참조한다.

### ✅Interaction to Next Paint 다음 페인트와의 상호작용

Interaction to Next Paint는 2024년 3월 12일에 추가 예정인 측정항목으로, 사용자가 페이지를 방문하는 전체 기간에 발생하는 모든 클릭, 탭, 키보드 상호작용의 지연 시간을 관찰하여 사용자 상호작용에 대한 페이지의 전반적인 응답성을 평가하는 항목이다.

여기서 말하는 **상호작용**은 대개 자바스크립트이지만 브라우저는 체크박스, 라디오 버튼, CSS에서 제공하는 컨트롤과 같이 JavaScript로 구동되지 않는 컨트롤을 통해 상호작용을 제공하기도 한다. 이때 INFP는 다음과 같은 상호작용만을 관찰한다.

- 마우스로 클릭
- 터치스크린이 있는 디바이스를 탭하기
- 물리적 키보드 또는 화면 키보드 누르기기

INP 점수를 좋다 나쁘다로 구분하기는 어렵지만, 일반적으로는 다음과 같다.

<table>
  <tr>
    <th>INP (in milliseconds)</th>
    <th>색 구분</th>
  </tr>
  <tr>
    <td>0~200</td>
    <td>좋음</td>
  </tr>
  <tr>
    <td>200~500</td>
    <td>개선 필요</td>
  </tr>
  <tr>
    <td>500 초과</td>
    <td>나쁨</td>
  </tr>
</table>

#### FID와의 차이점

INP가 모든 페이지 상호작용을 고려하는 반면, FID는 첫 번째 상호작용만 고려한다.

FID는 페이지와의 첫 번째 상호작용에서 입력 지연이 거의 없다면 페이지가 좋은 첫인상을 남겼다고 할 수 있고,

INP는 첫인상 그 이상으로, 모든 상호 작용을 샘플링하여 응답성을 종합적으로 평가할 수 있어 INP는 FID보다 전반적인 응답성에 대한 지표가 된다.

#### Interaction to Next Paint 개선 방법

- 느린 상호작용의 필드를 찾아 개선한다.
- **레이아웃 스레싱**(layout thrashing)에 주의한다
  - 레이아웃 스레싱: 웹 페이지가 렌더링 될 때 브라우저가 반복적으로 레이아웃을 계산하고, 화면을 다시 그리는 과정에서 발생한다. 사용자가 브라우저 창 크기를 조정하거나 스크롤을 할 때, 자주 발생 한다.
- DOM 크기를 최소화 한다.
- 자바스크립트를 사용하여 HTML을 렌더링할 때 성능 비용에 주의한다.

> Interaction to Next Paint 개선 방법의 자세한 사항은 [이 곳](https://web.dev/articles/optimize-inp?hl=ko)을 참조한다.

## 📩마무리

Lighthouse 측정에 재미가(?) 붙어서 그동안 진행한 몇 개의 프로젝트를 측정해보니 생각보다 높게 나온 경우도, 생각보다 낮게 나온 경우도 있었다.

이렇게 수치로 어떤 것이 부족한지 알려주니 개발자 입장에서 어떤 것을 고치면 좋을지 쉽게 알 수 있어 편리한 기능이 아닐 수 없다.
정말 개발자들은 어떤 불편함이 있으면 그 불편함을 해결하기 위해 보완 기술을 계속해서 만들어 내는 사람들이구나 싶다다.
이제 작업 후 Lighthouse를 이용해 웹 성능 최적화도 신경쓰면서 작업해보자!

## 🗂️참고 사이트

- <https://developer.chrome.com/docs/lighthouse>
- <https://web.dev/explore/learn-core-web-vitals?hl=ko>
