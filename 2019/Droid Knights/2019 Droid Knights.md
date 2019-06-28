## 소개
2019년 코엑스 e홀에서 열린 Droid Knights에서 여러 세션들을 보고 들은 것을 메모한 문서입니다.

## RxJava 구독과 구독 제거 메커니즘

* [발표영상](https://www.youtube.com/watch?v=rKP2XL8YxMo)

* [발표자료](https://drive.google.com/file/d/1LPKPZaKB_mKTK1T9QRi3svRpxxAQ8oir/view)

* RxJava를 사용할 때 좀 더 구독과 구독 제거를 잘할 수 있도록 하는 발표

* 구독이란 Observable과 Observer를 연결하는 것

* 구독제거는 Observable과 Observer의 연결을 끊는 것인데 이벤트 스트림을 중지하거나 무한 이벤트 스트림로 인한 메모리 누수 방지를 위해 하는 것입니다.

* 간단하게 Disposable이란 인터페이스가 있는데 우리는 이 것을 편리하게 사용하고 있고 굳이 이 친구를 수정할 필요는 없습니다.

* onNext, onError, onComplete가 대표적으로 있고 onSubscribe 함수가 있는데 사실 별로 사용하지 ㅇ낳는다.

* subscribe 함수는 결국 Disposable의 subscribe 함수를 사용하고 있다고 합니다.

* Disposable의 subsribe 함수 안에는 onNext이런 친구들 안에 LambdaObservable이 있다. 우리는 결국 이 친구를 계속 사용합니다.

* 구독은 연산자가 연결된 후에 발생합니다. 이 것을 연산자 체인이라고 하는데 연산자 체인은 Observable 반환으로 이어집니다.

* 나한테 주는 Observable은 upStream이고 내가 받는 Observable은 downStream입니다.

* 연산자 체인으로 인해 구독을 한다면 마지막 연산자 친구가 구독이 되는 메커니즘입니다.

* LamdbaObserver를 쓰는 이유는 실제로 람다를 쓰기 때문에 편하고 Disposable을 더 해준다고 합니다.

* 추상적인 Observer는 Observer만 구현하는데 나머지 실제 구현 Observer는 Diposable를 구현을 해준줍니다.

* RxJava에서 예외처리를 할 때 onError로 넘어가는데 이 onError는 LambdaObserver에 구현되어 있습니다.

* 결국 우리가 쓰는 것은 LambdaObserver 밖에 없다고 합니다.

* onNext에서 기존 Java처럼 try-catch를 사용하여 예외처리는 불가능하다고 합니다.

* LambdaObserver는 예외처리를 onNext에서 에러가 발생시 upStream은 dispose하고 onError를 실행하는 순서라고 합니다.

* onError에서 lazySet(DisposableHelper.DIPOSED)라는 친구를 쓰면 true가 나올 때가 있고 false가 나올때가 있는데 ture가 나올 때는 구독이 이미 제거 되었다는 뜻으로 둑이 구독 제거를 또 해주지 않아도 됩니다.

* dispose를 해야할 때는 interval와 같은 새로운 스트림이 생기는 경우 이 스트림은 전체 이벤트가 종료되어도 계속 실행되서 메모리 누수를 발생시킵니다. 이럴 때는 구독 제거를 시켜주어야 합니다.

* 불필요한 경우는 fromArray와 같은 친구를 쓰고 onComplete가 났음에도 불구하고 구독을 해지하는 경우입니다.

* 주로 사용하는 compositeDisposable 입니다. Api를 쓴다고 가정했을 때 갈아 엎고 Api를 쓰고 싶다면 clear() 함수를 사용하고 dispose() 함수를 함부로 사용하면 안됩니다.

* 근데 CompositeDisposable를 쓰게 되면 Callback Hell의 문제가 발생합니다. 이런 문제를 해결하기 위해서는 하나의 흐름을 만든 다음 마지막에 subsribe를 한번만 사용하는게 좋습니다.

* Disposable로 구독 여부를 체크를 할 수 있다고 합니다.

* SerialDisposable 이 친구는 못들었다 ㅠㅠ

* **정리**: RxJava 총정리 느낌 이었다. 좀 다르긴 하지만 Observable이 어떤 관계를 이루고 있는지 알기 쉬웠다.
* **느낀점**: 작년이었으면 진짜 하나도 이해 못했을 RxJava 발표를 이번 발표를 이해했다는 것을 느끼고 나니까 1년동안 내가 많이 성장했구나라는 생각이 들었다.

## 지금까지 이런 간단한 Logic 처리는 없었다. 이것은 Rx인가, UI 이벤트인가? 네, RxBinding 입니다.

* [발표영상](https://www.youtube.com/watch?v=uyUK65AUQOI)

* [발표자료](https://drive.google.com/file/d/1Hf3AGbSrEUA67Hr6hU_5c0vik8mZok3Z/view)

* RxBinding이란 Android UI의 다양한 이벤트들을 Observable로 바꿔주는 라이브러리입니다.

* RxBinding을 사용하는 이유는 직관성, 선언성, 확장성, 복합성, 변화성 모두 5가지가 있습니다.

* 더 정확히 말하자면 이벤트들이 다양해지고 로직이 복잡해져서 늘어난 코드들을 줄이거나 간단하게 하기 위해 사용합니다.

* RxBinding Kotlin을 그레이들에 추가하면 kotlin android extension을 사용할 수 있습니다.

* 만약 onClick 이벤트가 일어나면 onNext를 하나씩 넘겨줍니다.

* 복잡한 로직을 리액티브를 사용하지 않고 변수를 사용하여 처리하는 경우 로직은 복잡해지고 유지보수가 어려운 코드가 됩니다.

* View의 text가 바뀌었을 때 textChanges를 사용하여 텍스트 변경 로직을 쉽게 처리할 수 있습니다.

* 데이터가 바뀔 때마다 Callback을 사용하여 데이터를 처리하는 것보다 stream으로 바꾸어 stream을 조합하여 사용하는 것이 더 좋습니다.

* RecyclerAdapterDataChangeObservable를 쓸 때는 ViewHolder가 여러개여서 viewHolder가 View에 업데이트를 시켜야하는 경우입니다.

* 이럴 때는 AdapterData를 View가 구독하고 ViewHolder는 AdapterData의 데이터만 업데이트 시켜주면 됩니다.

* RecyclerAdapterDataChangeObservable은 custom이 가능하여 notifyChanged 같은 이벤트가 발생했을 때도 알림을 보낼 수 있습니다.

* Presenter에서 데이터의 변화를 구독한 뒤 View에 알림을 보내는 것이 실 적용 사례라고 합니다.

* RxBinding을 이용하면 코드를 함수형 프로그래밍 관점에서 보게 되고 유지보수가 쉽고 콜백지옥에서 탈 출할 수 있다고 합니다.

* **정리**: RxBinding은 복잡한 로직을 처리할 수 있다고 합니다.
* **느낀점**: RxJava로 리액티브 프로그래밍 공부한 뒤 들으니까 흐름은 이해할 수 있었는데 뭔가 70프로 정도 이해한 것 같은 느낌이다.

## Clean Architecture (in Android) Revised

* [발표영상](https://www.youtube.com/watch?v=5eOYb0Yvqh0)

* [발표자료](https://speakerdeck.com/sunghyunzz/clean-architecture-in-android-revised)

* 2017년 Clean Architecture (in Android) 발표를 다시 새롭게 바로잡는 발표입니다.

* Application이 실행하고자하는 가장 핵심적인 기능을 Use Case라고 합니다. 그리고 Application 앞의 붙는 단어는 플랫폼의 이름일 가능성이 높다.

* 그렇다면 내 Use Case 코드가 플랫폼에 종속적인가? 라고 의문을 던질 수 있다고 합니다. 하지만 엉클 밥은 아키텍쳐는 개념적인 것이지 프레임워크에 종속적인 것은 아니다라고 합니다.

* 그러므로 소프트웨어의 아키텍쳐는 Use Case를 중심으로 이루어져야 합니다.

* Use Case란 소프트웨어가 가장 핵심적인 친구로 이 중심에는 Entity라는 친구가 있는데 이 친구는 비즈니스 룰을 정의하는 친구입니다.

* 엉클 밥은 플랫폼과 Use Case를 느슨하게 결합시켜주는 Interface Adapter를 만들자고 제안합니다. 이런 Interface를 통해 외부세계와 데이터를 주고 받을 수 있습니다.

* 클린 아키텍쳐의 핵심은 비즈니스 로직을 바뀔 수 있는 것들로부터 독립시키자 입니다. 무슨 말이냐면 Use Case를 바꿔야 하지 않음에도 불구하고 Use Case를 바꿔야 되면 안된다는 것입니다.

* 그 원인은 클린 아키텍쳐에서 말하는 의존성 규칙을 지키지 않기 때문에 발생합니다.

* 클린 아키텍쳐의 의존성 규칙은 무엇이냐면 의존성은 Interface를 통해서 의존성을 얻어야 한다는 것입니다. 반드시 원 안의 의존성을 주입을 받을 수 있는 것이죠, 예를 들어 Presenter Interface를 통해서 Use Case를 구현해야합니다.

* 발표자님이 이게 완벽한 클린 아키텍쳐가 아닐 수도 있다고 말씀 하셨습니다. 예제는 깃허브에 sunghyunzz/android-clean-architecture-example에 들어가면 있다고 합니다.

* 클린 아키텍쳐에서의 View는 우리가 아는 MVP의 View와 같은 것이 아니라 가상의 프로그램의 로직이라고 할 수 있습니다.

* 그래서 TodosView와 같은 인터페이스를 만들어서 의존성을 주입할 때 Android 프레임 워크에 의존하지 않는다고 합니다.

* 그리고 View는 todo를 핸들링하는 역할을 합니다.

* Use Case가 하는 일은 todo들을 가져와서 todoService로 처리하는 것입니다.

* Use Case에서 Entity를 사용해야하는데 이런 Entity는 data class로 구현합니다. 그리고 Entity는 TodoGateway를 통해 가져옵니다.

* 이 과정에서 Presenter에서 처리를 해야하는데 이 Presenter에서는 ViewModel을 자체적으로 생성하는 주체가 됩니다.

* 외부 환경에 독립적이고 프레임워크에 독립적이지 않게 되어 테스트하기가 더 쉬워집니다.

* 발표자님계서 클린 아키텍쳐에 대한 오해를 해결해주셨습니다.
    * 클린 아키텍쳐와 MVP, MVVM은 비교할 수가 없습니다.
    * 클린 아키텍쳐는 진짜 클린합니다. 단지 생성하는 코드의 양은 늘어나기는 합니다. 하지만 코드의 가독성이 올라가기 때문에 클린합니다.
    * 클린 아키텍쳐는 현대적입니다. 다만 구현체가 구린거지 정신은 아니라고 합니다.
    * 클린 아키텍쳐는 DDD가 아닙니다. 클린 아키텍쳐에서 DDD는 전혀 언급되지 않았습니다.

* 작견 발표바로잡기
    * Entity는 비즈니스 오브젝트입니다.
    * Domain가 아니라 Entity Gateway 패턴을 쓰는게 더 바람직한 것 같습니다
    * Presentation 레이어에서 View라는 개념에 대해서 전혀 언급하지 않았습니다. (이 부분이 중요한 듯 합니다)

* **정리**: 2017년 발표는 잘못되었고 그 발표를 다시 바로잡는 발표였다.
* **느낀점**: 진짜 엄청 어려웠다. 원래 알고 있던 개념하고는 사뭇달랐다. 그래서 위 발표처럼 엉클 밥의 정신에 맞게 플랫폼 종속성에서 자유로운 코드를 구현해야할지 원래 본질과는 조금 다르게 생각 되는 Android에서 구현하기 비교적 쉬운 Clean Architecture 코드를 구현해할 지 모르겠다.

## 오프라인과 앱 서비스

* [발표자료](https://speakerdeck.com/cyber7eyes/opeuraingwa-aebseobiseu-deuroideu-naiceu-2019)

* 첫 번째 방법은 예외 처리입니다.

* 에러가 발생하면 cache의 데이터를 가져옵니다. 이 과정에서 Clean Architecture의 Data Manager를 많이 건드립니다.

* 두 번째 방법은 오프라인 모드입니다. 데이터 사용을 임의로 제한하여 오프라인 데이터로만 서비스를 제공하는 방법입니다.

* 로컬 DB에 데이터를 담아서 업데이트합니다.

* 세 번째 방법은 오프라인 우선 모드입니다. 오프라인 우선 모드는 로컬 DB에 동기화하여 굳이 네트워크 작업을 하지 않아도 할 수 있는 방법입니다.

* 이 방법은 Updater라는 클래스를 사용하여 REST API에서 받아온 데이터를 Local DB에 저장한 후 Local Data를 가져옵니다.
    * 온라인 오프라인 데이터에 대한 인터페이스 제공
    * 온라인 오프라인 간의 변경 사항을 저장
    * 온라인 오프라인의 stream을 Observer를 사용

* Updater에서 너무 오래된 로컬 데이터는 UI에 표시합니다.

* 오프라인 데이터 처리
     * 마지막 동기화 시간 기록
     * 민감한 데이터는 유효시간 설정
     * 유효시간이 지난 데이터 표시시 UI에 표시합니다.

* **정리**: 클린 아키텍쳐 안에 있는 Data Layer에 Updater클래스를 만들어서 오프라인 우선순위를 두는 방식이었다.
* **느낀점**: DMS에 넣어도 좋을 것 같다.

## Large-Scale App을 위한 아키텍쳐 총정리

* [발표영상](https://www.youtube.com/watch?v=0LL7FCWTxoo)

* [발표자료](https://speakerdeck.com/saryong/android-architecture-congjeongri)

* Model: State + 비즈니스 로직 + 데이터 저장소 (비즈니스 로직)

* 클린 아키텍쳐: MVC에서 Model을 분해하여 Domain Layer+ Data Layer

* 안드로이드에서 Presentation 로직에 집중하는 이유는 가장 복잡한 비즈니스 로직은 서버에서 구현하기 때문입니다. 그리고 비동기적인 상태의 관리와 라이프사이클 연동이 문제이기 때문입니다.

* 테스트 가능성이 중요한 이유는 테스트 가능성이 떨어지는 코드는 중요 라이브러리 변경, 기반 구조 변경에 대응하기가 어렵습니다.

* 재사용성이 중요한 이유는 재사용성이 떨어지는 구조는 기능 변경/ 확장성이 떨어진다 -> 코드 가독성이 떨어지기 때문입니다.

* MVVM/Redux
    * View는 context와 관련된 것만 처리해야함 MVP보다 훨씬 제한적임.
    * 기존 Activity/Fragment의 형태를 깨뜨리지 않으면서 재사용성/생산성이 높은 아키텍쳐 구현이 가능하기 때문에 구글은 리액티브를 하지 않으면 구현하기 힘든 ViewModel을 지원하는 것 같다고 하셨습다.

* Redux가 나온 문제 상황: 모바일 앱에서 가장 큰 난제 중의 하나는 복잡한 상태들을 어떻게 깔끔하고 유지보수성 높게 구현할 것인가의 문제입니다.

* MVI란? Flux에 View처리 등 모바일 관련 내용을 추가해서 부르는 이름. 안드로이드에서 Flux와 같은 용어라고 봐도 무관합니다.

* Redux의 장점은 Functional한 구조 덕분에 유닛 테스트를 작성하는 것이 편하고 라이프사이클의 영향을 어느 정도 배제할 수 있는 등 여러 장점이 있습니다. 단점은 코드량이 증가하고 학습비용이 높습니다.

* 나중에 Redux가 코루틴과 함께 사용될 것 같습니다.

* 이번 년도 트렌드는 멀티모듈이라고 생각하십니다.

* 모듈은 어떻게 나누는가? Presentation/ Domain/ Data Layer로 구현
    * 하지만 Application에서는 불가능하므로 di를 통해 Domain에 의존성을 주입하도록 합니다.
    * 화면별, 기능별로 수평 분리를 합니다.
    * DIP와 DI를 통해서 모듈을 만들어 의존성을 주입합니다.

* **정리**: MVC부터 Clean Architecture, Redux 까지 알아보았다.
* **느낀점**: 정말 많은 내용이 한시간에 축약된 발표 들었고 MVI와 Redux, Flux에 대해서 호기심이 생겼다.

## PWA 품은 안드로이드 웹뷰 성능 개선기

* [발표자료](https://speakerdeck.com/tinyjin/pwa-pumeun-andeuroideu-webbyu-seongneung-gaeseongi)

* PWA 캐시를 하면 웹뷰를 쓸 때보다 훨씬 빠르다고 합니다.
* 구글에서 WorkBox 라이브러리로 PWA를 쉽게 구현할 수 있습니다.
* 이미 웹뷰가 적극적으로 적용된 앱이라면 부담없이 적용할 수 있을 것입니다.

* **정리**: 웹뷰를 사용할 경우 PWA를 활용하여 개발을 하라고 권장했다.
* **느낀점**: 웹뷰를 적극적으로 사용해본적은 없지만 사용하게 된다면 한번 적용해보고 싶어졌다.

## Dagger vs Koin 어떻게 쓰는 건가요??

* [발표영상](https://www.youtube.com/watch?v=mbntrG0LKtE)

* [발표자료](https://speakerdeck.com/hyunji92/how-to-use-dagger2-and-koin)

* DI란? 의존성이 크다라는 건 두 클래스의 결합도가 깊다는 것을 뜻합니다.

* Dagger2는 어노테이션을 사용하여 빌드시간은 길어지지만 런타임 중에는 에러가 없어집니다.

* 송직히 Dagger2는 제가 어느정도 정리해 둔 것이 있어서 굳이 적지는 않겠습니다.

* Koin은 Service Locator Pattern의 집합체입니다.

* Service Locaotr Pattern이란 필요에 따라 리턴해주는 패턴입니다.

* DSL을 사용하기 때문에 어노테이션을 사용하지 않고 실용적인 의존성 주입 라이브러리가 생겨났습니다.

* Koin은 항상 지연 초기화를 함.

* 예제 주소: https://gitHub.com/hyunji92/FindNewUsers

* 의존성 사용으로 얻는 이점
    * 추상화를 통해 정의된 객체들의 상호작용에 의해 동적인 흐름 가능합니다.
    * 런타임에 바인딩되어 의존성은 외부에서 주입되어 의존도를 낮추고 재상용 가능합니다.
    * 주입된 코드의 기능을 바꾸고 싶을 때, 많은 코드를 바꿀 필요가 없습니다.
    * 생성된 인스턴스의 스코프, 인스턴트의 생성과 생명주기를 DI 프레임워크에서 알아서 해줍니다.

* **정리**: 그냥 Dagger2와 Koin의 개념과 사용법에 대해서 알아보았다.
* **느낀점**: 솔직히 의존성 사용으로 얻는 장점에 대해서는 모호했는데 발표를 듣고 명확해졌다.

## Advanced MVP

* [발표영상](https://www.youtube.com/watch?v=ooMT69xOiKI)

* [발표자료](https://speakerdeck.com/gorita/advanced-mvp-refactoring-mvp)

* View는 passive하게 작성하여 UnitTest가 쉽도록 작성해야 합니다.

* View를 passive하게 작성하기 시작하면 Logic은 Presenter로 넘어 가기 때문에 Presenter의 코드가 비대해집니다.

* 이를 해결할 수 있는 방법은 Model에 비즈니스 도메인 로직을 넘기는 것입니다.

* Model은 하나의 레이어로 도메인/비즈니스 로직을 담당합니다. data를 가져올 수 있는 repository와 비즈니스로직과 도메인 로직을 담당하는 Use Case로 이루어져있습니다.

* 이럴 때 Repository 패턴에 Use Case에 있어야 하는 로직이 있게 됩니다. 이럴 경우 Unit Test가 어려워집니다. 이를 확실히 분리해야 합니다.

* Design Pattern에 대해 발표자님은 방법보다 패턴을 써야 하는 목적에 생각해야 합니다고 하셨습니다.

* **정리**: 정말 MVP를 어떻게 잘다루는지에 대해서 말하는 깔끔한 발표였다.
* **느낀점**: 개발자가 의문을 던져야 할 why에 대해서 생각해볼 수 있는 발표였다.
