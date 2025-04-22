---
title: "Nextjs-Optimizing(5) - Image caching"
author: saemii
categories:
  - Nextjs
tags:
  - [Nextjs]
date: 2025-04-22
last_modified_at: 2025-04-22
pin: true
---

# 📌시작하며

이전 Nextjs-Optimizing(4) 포스팅에서 static assets에 대해 살펴보았다. 사실 그 때는 그렇군! 좋아! 🤗 하고 넘어갔는데, 나중에 이걸 실제로 사용할 때가 되니 약간의 시행착오를 겪어서 해당 내용을 정리해보고자 한다.

# 🖼️ Image Caching

## 💥 문제 상황

실무에서 특정 이미지를 사용하는데, 해당 이미지는 거의 바뀌지 않을 이미지이고 자주 사용되는 이미지였다. 그런데 이 이미지를 렌더링 될때마다 요청하는건 너무 낭비같아서 이를 어떻게 처리할까 하다가 이 전 포스팅들을 통해 Next js 공식문서들을 쭉 훑어봤던 것들이 기억났고, 이에 이미지 캐싱을 시도하게 되었다.

## 🎇 캐시

## 🎁 Next js에서 Image Cache 하기

Next js 공식 문서에서 Next.js에서 어떻게 Cache를 처리하면 되는지 설명하고 있는데 이를 살펴보자.

## 💛 참고 사항

그런데 Next js 공식문서를 보다보면 Next.js는 immutable assets에 대해 아래와 같은 Cache-Control 헤더를 **자동으로 설정**한다는 말이 있다. 이걸 보고 어? 그러면 굳이 할필요가 없나? 하고 생각했는데, 같은 파일이어도 이를 어떻게 import해서 사용하냐에 따라 달라진다.

예시를 살펴보자.

## 😫 문제 상황

사실 public 폴더 바로 아래에 모든 static assets(image)를 보관하고 있었기에, `/:path*`와 같은 경로를 사용해서 모든 요청에 캐시 헤더를 적용하려고 했다.

```javascript
async headers() {
		return [
			{
				source: '/:path*',
				headers: [
					{
						key: 'Cache-Control',
						value: 'public, max-age=31536000, immutable',
					},
				],
			},
		];
	},
```

그런데 이 설정을 적용하니 이미지가 아예 로딩되지 않고 제대로 로그인도 되지 않는 큰 문제가 발생했다... 알고보니 이는 모든 경로와 매칭되어, 그 모든 것이 캐시되어버렸기 때문이었다.

따라서, 내가 원하는 **이미지**에만 캐시를 적용하기 위해서는 따로 이미지를 위한 폴더가 필요해서, 따로 public 폴더 아래에 `images` 폴더를 만들어 주어 이미지를 옮겨 주었고, 다시 source를 작성하였다.

```javascript
async headers() {
		return [
			{
	      source: "/images/:path*",
				headers: [
					{
						key: 'Cache-Control',
						value: 'public, max-age=31536000, immutable',
					},
				],
			},
		];
	},
```

## 🗂️참고 사이트

- <https://nextjs.org/docs/app/api-reference/config/next-config-js/headers>
- <https://developer.mozilla.org/ko/docs/Web/HTTP/Reference/Headers/Cache-Control>
-
