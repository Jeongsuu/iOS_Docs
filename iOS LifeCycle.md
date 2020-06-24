# iOS App Life Cycle

Application Life Cycle : 앱이 실행되고 동작하며 종료하기까지의 일련의 과정

App State : 생명 주기 동안의 5가지 상태

![https://images.velog.io/images/cskim/post/53950351-2edd-4134-87fa-88c27935ba1a/image.png](https://images.velog.io/images/cskim/post/53950351-2edd-4134-87fa-88c27935ba1a/image.png)

- Not Running : 앱이 아직 실행되지 않았거나 시스템에 의해 완전히 종료된 상태
- Inactive : 앱이 실행 중이지만 아직 이벤트를 받지 않은 상태
- Active : 앱이 실행 중이면서 이벤트를 받고 있는 상태
- Background : 앱이 다른 앱으로 전환되거나 홈화면으로 돌아갔을 때에도 동작을 하고 있는 상태
- Suspended : 앱이 백그라운드 상태에서 추가적인 작업을 하지 않으면 들어가는 상태, 앱을 다시 실행해도 빠르게 실행할 수 있도록 메모리에만 올라가있음, 메모리가 부족하게 되면 iOS는 Suspended 상태의 앱들을 종료시켜 메모리를 확보

## Application 실행과정

App 실행 → main에서 UIApplicationMain() 호출 → UIApplication 객체 생성 (app의 main run loop 관리) → Info.plist의 값들을 바탕으로 필요한 데이터, 객체 로드 → AppDelegate 객체를 생성하고 UIApplication 객체와 연결 → 실행에 필요한 기타 코드 구현 → application(_:didFinishLaunchingWithOptions:) 메소드 호출

## Process Life Cycle

- application(_:willFinishLaunchingWithOptions:) : 앱에 필요한 주요 객체들을 생성하고 run loop를 생성하는 등 앱의 실행 준비가 끝나기 직전에 호출
- application(_:didFinishLaunchingWithOptions:) : 앱 실행을 위한 모든 준비가 끝난 후 사용자에게 화면이 보여지기 직전에 호출, 최종 초기화 코드 작성
- applicationWillTerminate(_:) : 앱이 종료되기 직전에 호출, 사용자에 의해 종료될 때만 호출

## UI Life Cycle

- applicationWillEnterForeground(_:) : 앱이 백그라운드 또는 Not-running 상태에서 Forground로 들어가기 직전에 호출
- applicationDidBecomeActive(_:) : 앱이 액티브 상태가 된 직후 호출
- applicationWillResignActive(_:) : 앱이 액티브 상태에서 In-active 상태로 진입하기 직전에 호출, 앱이 조용한 상태로 변환될 때의 작업 진행
- applicationDidEnterBackground(_:) : 앱이 백그라운드 상태로 진입한 직후 호출, Suspended 상태가 되기 전 데이터를 저장하거나 공유 자원을 해제하는 등 종료되기 전 필요한 작업 진행

## View Controller 생명 주기

![https://user-images.githubusercontent.com/39197978/61628981-aac03e00-acbe-11e9-9f8a-51ce0654d005.png](https://user-images.githubusercontent.com/39197978/61628981-aac03e00-acbe-11e9-9f8a-51ce0654d005.png)

- viewDidLoad() : loadView() 직후에 호출되는 콜백 메소드, 뷰가 메모리에 올라간 직후 호출
- viewWillAppear() : 뷰 컨트롤러의 root view가 로드된 이후 window의 뷰 계층으로 더해지기 직 전에 호출
- viewDidAppear() : window의 root view가 뷰 계층으로 더해진 직 후 호출
- viewWillDisappear() : window의 root view가 뷰 계층에서 제거되기 직 전 호출
- viewDidDisappear() : window의 root view가 뷰  계층에서 제거된 직 후 호출