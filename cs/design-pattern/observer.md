---
description: 객체의 상태 변화를 감지하고 등록된 옵저버들에게 자동으로 변화를 알리는 패턴
---

# 옵저버 (Observer)

<figure><img src="../../.gitbook/assets/observer-2x.png" alt=""><figcaption></figcaption></figure>



watch?

listen?



객체와 주체를 따로 두지 않는 경우도 있음

twitter



js 로 구현하게 되면,&#x20;

어떠한 대상의 기본적인 동작의 작업을 가로챌 수 있는 객체 : 프록시 객체

target 프록시 할 대상

handler target 동작을 ㄷ가로채고어떠한 동작을 할것인지 설정되어 있는 함수

new Proxy();&#x20;

특정 속성에 접근할 때 그 부분을 가로채서 어떠한 로직을 강제할 수 있는 것



옵저버 패턴 어케 구현?

프롣시 객체를 써서?

프록시 서버를 설명하고 사용 사례에 대해 설명해봐라

MVC 패턴을 설명하고 MVVM 과 어떤 차이가 있는지 설명하라

