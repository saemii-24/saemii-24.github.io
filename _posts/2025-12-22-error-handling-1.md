# Maximum call 탈출기

바야흐로 `video` 태그를 이용해 자체 비디오 플레이어를 만들던 평화로운 어느날, 배포를 하고 살펴보니 갑자기 maximum call이 나오며 페이지가 로딩이 안되는 상황이 발생했다. 😱
마감 일정은 앞당겨오지, 해당 컴포넌트는 이미 많은 로직이 작성되어 있지 원인은 파악 되었지만 이걸 대체할 방법은 또 모르겠는..
그런 상황을 겪다가 결국 해결을 하게 되어서 그 탈출기를 작성하고자 한다.

파악한 문제점은 다음과 같았다.

일단 기존 컴포넌트 구조는 다음과 같다.

[비디오 태그 / 사용자 카드1]

사용자 카드에는 시간값이 적혀있는 카드 리스트로, 사용자가 카드를 클릭하는 경우 비디오 태그의 currentTime이 그 시간으로 이동해야 했으며, 현재 어떤 카드가 재생되고 있는지가 보여져야 했으므로 나는 두가지의 state를 두었다.

1. currentTime
2. videoIdx

즉, 최초 코드를 작성할 때 나의 관점에서는 currentTime이 변동 될때도 videoIdx를 관찰해야 했고 videoIdx가 변경되어도 currentTime이 변경되어야 했던 것이다.
그래서 이 두가지를 작성하다 보니 currentTime 변경 <-> videoIdx 변경이 왔다갔다 하면서 maximum call로 빠지게 된 것이었다.

그래서 일단 videoIdx를 이용해 currentTime을 처리하는 useEffect를 주석처리하고 문제를 해결해보기로 했다.

1. DOM을 직접 관리해보자.
   사실 react 원칙에 어긋나는 방안이긴 하다. 변명을 좀 해보자면 그떄는 useEffect를 사용하면 계속 maximum call이 나기 때문에 이를 이용하지 않고 video를 카드의 startTime에 맞추기 위한 고육지책이라고 생각했다(...)
   그런데 문제는 이렇게 하니, 영상이 재생될때는 해당 영상의 시간값과 연결되어있는 slider가 적절히 변경되지만 videoTag를 멈춘 상태에서 이동하고자 하면 (렌더링이 일어나지 않으니 당연히도..) 아주 가만~히 있는다는 것이었다.
   UI에 큰 문제가 아닐 수 없다(...) 😑
   이 방안을 고수하려면 더 문제가 될 것 같아 일단 해당 해결방안은 폐기했다.

2. ref를 활용할까? 그런데 VideoPlayer 컴포넌트와 Card component가 너무 멀다!
   그 다음 생각해본건 ref를 이용해 video 태그에 접근하는 것이었다. 문제는 실제 비디오 플레이어와 카드 컴포넌트가 너무 멀어 ref를 동시에 관리해주는게 너무 어려웠다는 것이었다.

3. 아예 역할을 철저히 분리하라!
   마지막 채택된 방안은 역할을 아주 철저히 분리하는 것이었다. 원인 자체가 비디오 태그의 시간값을 조정하는 trigger들이 너무 많아 나도 모르게 의존성 배열에 maximum call을 부를 원인들을 유발하고 있었던 것이기에, react에서 중요시 하는 단방향 데이터 흐름을 따라 한 곳에서만 비디오의 영상 시간값을 조정하기로 한 것이다.

```typescript
useEffect(() => {
  if (
    videoRef.current &&
    Math.abs(videoRef.current.currentTime - currentTime) > 0.1
  ) {
    videoRef.current.currentTime = currentTime
  }
}, [currentTime])
```

이 코드는 기존에 문제를 일으켰던 코드로, currentTime 변화마다 useEffect에서 비디오의 실제 시간을 강제로 설정했었다.
