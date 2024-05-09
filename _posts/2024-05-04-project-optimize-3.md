---
title: "최적화 적용(3) - 메모이제이션"
author: saemii
categories:
  - Optimize
tags:
  - [Optimize, Next.js, Project]
date: 2024-05-09
last_modified_at: 2024-05-09
pin: true
---

## 📌시작하며

프로젝트에서 React.memo, useMemo, useCallback을 사용해서 메모를 통한 최적화 방식을 적용했다. 어떤 것들에 적용했는지 정리하고자 포스팅한다.😎

## ✅React.memo

React.memo는 컴포넌트가 받는 props가 변경되지 않는 한, 해당 컴포넌트가 재랜더링되는 것을 방지한다.

React Dev Tools를 이용해 state가 변경되었을 때 어떤 컴포넌트들이 재 렌더링 되는지 확인하고, 불필요하게 재 렌더링 되는 경우 memo 처리 해주었다.

예를 들어 루트 페이지의 음악 카드들의 경우 Tanstack Query로 받은 Data를 map 메소드를 이용해 각각의 musicData를 props로 전달받는데 Data가 변경되지 않는 경우 굳이 재렌더링 될 필요가 없으나, 지속적으로 재 렌더링 되는 것을 확인하였고, 이를 막고자 React.memo를 사용하였다.

여담이지만 컴포넌트 만들 때 이번 프로젝트에서는 `export default function 컴포넌트`와 같은 함수 선언식으로 작성했는데, memo를 쓸 때는 `const 컴포넌트 = () => {}`와 같은 함수 표현식을 사용한 다음, 마지막에 `export default memo(컴포넌트)` 와 같이 작성해야 해서 처음부터 표현식으로 작성할 걸 그랬나? 생각했다.😅

```javascript
const MusicCard = ({ musicData }: { musicData: SupabaseType }) => {
  const {
    //구조분해할당
  } = musicData

  const lan = useRecoilValue(languageMode)
  const route = useRouter()

  //현재 보고 있는 버전 설정
  const [selectLang, setSelectLang] =
    useState < LangType > (kotitle ? "ko" : "jp")

  return (
    <div className="mt-20 w-full overflow-hidden rounded-lg bg-white shadow-sm">
      <Link
        href={`/musicpt/${musicData.id}`}
        className="block aspect-[8/5] w-full cursor-pointer overflow-hidden "
      >
        <div
          className={cn("relative aspect-square w-full", {
            hidden: selectLang === "jp",
            block: selectLang === "ko",
          })}
        >
          {selectLang === "ko" &&
            (kothumbnail ? (
              <Image
                src={kothumbnail}
                fill={true}
                sizes="(max-width: 768px) 100vw, (max-width: 1023px) 704px, 400px"
                alt={"음악"}
                loading="lazy"
                placeholder="blur"
                blurDataURL={kothumbnail}
              />
            ) : (
              <DefaultImage />
            ))}
        </div>
        <div
          className={cn("relative aspect-square w-full", {
            hidden: selectLang === "ko",
            block: selectLang === "jp",
          })}
        >
          {selectLang === "jp" &&
            (jpthumbnail ? (
              <Image
                src={jpthumbnail}
                fill={true}
                sizes="(max-width: 768px) 100vw, (max-width: 1023px) 704px, 400px"
                alt={"음악"}
                loading="lazy"
                placeholder="blur"
                blurDataURL={jpthumbnail}
              />
            ) : (
              <DefaultImage />
            ))}
        </div>
      </Link>
      <div className="h-[150px] p-5">
        {/* 카드 윗 줄 */}
        <div className="flex items-center">
          <p className="font-medium text-black">
            {selectLang === "ko" ? kosinger : jpsinger}
          </p>
          <div className="relative ml-auto ">
            <LangButton
              setSelectLang={setSelectLang}
              kolyrics={kolyrics}
              jplyrics={jplyrics}
            />
          </div>
        </div>
        {/* 카드 제목 */}
        <h1 className="mt-2 text-2xl font-medium text-black">
          {selectLang === "ko" ? kotitle : jptitle}
        </h1>
      </div>
      {/* 작성자 좋아요 */}
      <div className="flex border-t border-music-basicgray px-5 py-3">
        <div className="flex items-center gap-1">
          <div>
            <MdOutlineUpdate className="text-lg text-black" />
          </div>
          <div className="text-sm text-black ">{date.slice(0, 10)}</div>
        </div>
        <div className="ml-auto flex items-center gap-1 text-sm">
          <div>
            <LikeCount music={musicData} />
          </div>
        </div>
      </div>
    </div>
  )
}

export default memo(MusicCard)
```

## ✅useMemo

useMemo는 주어진 **함수를 호출**하고 **그 결과를 메모**한다.

titleInfo는 lan 값의 변화에 따라 title과 description이 달라지는데, 다른 값들이 변경할 때마다 다시 렌더링 되어서, 아래와 같이 useMemo를 통해 값을 메모하고, 동일한 값일 경우 굳이 재렌더링 되지않게 만들어주었다.

```javascript
const titleInfo = useMemo(() => {
  return {
    title: lan["edit-music-title"],
    description: lan["edit-music-description"],
  }
}, [lan])
```

## ✅useCallback

useCallback은 주로 함수를 prop으로 넘길 때 사용되고, 함수가 의존하는 값이 변경되었을 때만 새로운 함수가 생성된다.

setTitle은 lan값의 변화에 따라 setThisTitle이란 상태를 업데이트 하는 새로운 함수를 실행하는 것이므로, useCallback을이용해 메모화 해주었다.

```javascript
const setTitle = useCallback(
  (lyricsVer: string) => {
    switch (lyricsVer) {
      case "koVer":
        setThisTitle(lan["music-title-korean"])
        break
      case "jpVer":
        setThisTitle(lan["music-title-japanese"])
        break
      case "koCompare":
        setThisTitle(lan["music-title-jpver-korean"])
        break
      case "jpCompare":
        setThisTitle(lan["music-title-kover-japanese"])
        break
      default:
        setThisTitle(lan["music-title-korean"])
    }
  },
  [lan]
)
```
