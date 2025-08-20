React Native는 앱에서 사용할 수 있는 여러 내장 코어 컴포넌트를 제공하는데, 먼저 자주사용하게 되는 Core Component부터 살펴보고자 한다.

공식문서에서 설명하는 코어 컴포넌트는 다음과 같다.

- Basic Components (기본 컴포넌트)
- User Interface (사용자 인터페이스)
- List Views (리스트 뷰)
- Android-specific (Android 전용)
- iOS-specific (iOS 전용)
- Others (기타)

# 📱 React Native 기본 컴포넌트 정리

## 🟦 Basic Components

대부분의 앱에서 한 개 이상 사용하게 되는 기본 컴포넌트이다.

- **View**
  UI를 구성하기 위한 가장 기본적인 컴포넌트이다.

- **Text**
  텍스트를 표시하는 컴포넌트이다.

- **Image**
  이미지를 표시하는 컴포넌트이다.

- **TextInput**
  키보드를 통해 텍스트를 입력받는 컴포넌트이다.

- **Pressable**
  자식 요소에 다양한 터치(press) 상호작용 단계를 감지하는 래퍼 컴포넌트이다.

- **ScrollView**
  여러 컴포넌트와 뷰를 담을 수 있는 스크롤 가능한 컨테이너이다.

- **StyleSheet**
  CSS 스타일시트와 유사한 추상화 레이어를 제공하는 컴포넌트이다.

## 🟩 User Interface

플랫폼에 상관없이 공통적으로 렌더링되는 사용자 인터페이스 컨트롤이다.

- **Button**
  모든 플랫폼에서 보기 좋게 렌더링되는 기본 버튼 컴포넌트이다.

- **Switch**
  불리언 입력을 렌더링하는 컴포넌트이다.

## 🟨 List Views

ScrollView와 달리, 현재 화면에 보이는 요소만 렌더링하여 긴 리스트를 효율적으로 표시할 수 있다.

- **FlatList**
  성능 좋은 스크롤 가능한 리스트를 렌더링하는 컴포넌트이다.

- **SectionList**
  FlatList와 유사하나, 섹션 단위로 리스트를 표시할 수 있는 컴포넌트이다.

## 🤖 Android Components & APIs

일반적으로 사용되는 Android 클래스의 래퍼를 제공하는 컴포넌트이다.

- **BackHandler**
  하드웨어 뒤로가기 버튼 입력을 감지하는 컴포넌트이다.

- **DrawerLayoutAndroid**
  Android에서 DrawerLayout을 렌더링하는 컴포넌트이다.

- **PermissionsAndroid**
  Android M에서 도입된 권한 모델에 접근할 수 있게 해주는 API이다.

- **ToastAndroid**
  Android 토스트 알림을 생성하는 컴포넌트이다.

## 🍎 iOS Components & APIs

일반적으로 사용되는 iOS UIKit 클래스의 래퍼를 제공하는 컴포넌트이다.

- **ActionSheetIOS**
  iOS 액션 시트나 공유 시트를 표시하는 API이다.

## 💻 Others

특정 상황에서 유용하게 사용할 수 있는 기타 컴포넌트이다.

- **ActivityIndicator**
  원형 로딩 인디케이터를 표시하는 컴포넌트이다.

- **Alert**
  지정된 제목과 메시지를 가진 알림 다이얼로그를 띄우는 컴포넌트이다.

- **Animated**
  강력하고 유연한 애니메이션을 쉽게 생성하고 유지할 수 있는 라이브러리이다.

- **Dimensions**
  기기 화면 크기를 가져올 수 있는 인터페이스이다.

- **KeyboardAvoidingView**
  가상 키보드가 나타날 때 자동으로 뷰를 피하게 해주는 컴포넌트이다.

- **Linking**
  앱 링크를 수신하거나 외부로 열 수 있는 일반적인 인터페이스이다.

- **Modal**
  상위 뷰 위에 콘텐츠를 표시하는 간단한 방법을 제공하는 컴포넌트이다.

- **PixelRatio**
  기기의 픽셀 밀도에 접근할 수 있는 API이다.

- **RefreshControl**
  ScrollView 내부에서 당겨서 새로고침 기능을 제공하는 컴포넌트이다.

- **StatusBar**
  앱의 상태 바를 제어할 수 있는 컴포넌트이다.
