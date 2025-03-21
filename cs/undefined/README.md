---
description: 소프트웨어를 설계하는 과정에서 자주 발생하는 문제를 해결하기 위한 설계 방식
---

# 디자인 패턴

디자인 패턴이라는 용어는 원래 **건축학**에서 시작되었습니다.&#x20;

건축가 크리스토퍼 알렉산더(Christopher Alexander) 는 건축 및 도시 설계에서 반복적으로 나타나는 문제를 해결하기 위해 패턴 랭귀지(Pattern Language)라는 개념을 제안했습니다. 그는 “[A Pattern Language: Towns, Buildings, Construction](https://www.amazon.com/Pattern-Language-Buildings-Construction-Environmental/dp/0195019199)” (1977) 책에서 **253개의 디자인 패턴**을 정리했으며, 이 패턴들은 도시, 건축, 실내 공간 등에 적용될 수 있는 재사용 가능한 설계 솔루션으로 정의되고 있습니다.

<details>

<summary>크리스토퍼 알렉산더가 제안한 대표적인 건축 디자인 패턴에는 어떤 것이 있을까?</summary>

### 1️⃣ **Small Public Squares (작은 공공 광장)**

사람들이 광장에 모이기를 원하지만, 너무 넓은 공간은 위압감을 형성

💡 크기가 적절한 작은 광장을 배치하여 사람들이 자연스럽게 모이도록 유도

### 2️⃣ **Street Cafe (거리 카페)**

도시에서 사람들이 휴식을 취하고 교류할 수 있는 공간이 부족

💡 보행자가 쉽게 접근할 수 있는 길가에 카페를 배치하여 자연스러운 사회적 공간 형성

### 3️⃣ **Natural Light in Rooms (자연 채광을 고려한 방 배치)**

인공 조명만으로는 실내 공간이 답답하고 불편

💡 창문을 적절한 방향으로 배치하여 자연광이 최대한 들어오도록 설계

### **4️⃣ Entrance Transition (출입구의 전환 공간)**

건물 내부와 외부의 단절로 인해 갑작스러운 환경 변화로 불편

💡 입구에 포치(Porch)나 작은 로비를 두어 공간적 완충 역할을 하도록 설계

### **5️⃣ Hierarchy of Open Spaces (공간의 위계 구조)**

건축 공간이 개방적이거나 닫힌 정도가 무질서할 경우, 사람들에게 불안감을 형성

💡 거대한 개방 공간 대신 작은 개인 공간부터 공공 공간까지 위계적으로 정리하여 자연스럽게 연결.

### **6️⃣ Rooms Shaped for People (사람을 고려한 방 형태)**

직사각형 방이 표준이지만, 일부 활동이나 가구 배치에 적합하지 않을 가능성

💡 방을 정형화된 사각형이 아니라, 인간의 활동 패턴에 맞게 곡선이나 다각형 등으로 변형 가능하도록 설계

</details>

{% embed url="https://youtu.be/SJ9s4kr5K6w?si=KgWyGhwHS1_t4eJM" %}

크리스토퍼 알렉산더의 건축 디자인 패턴은 공간을 더욱 인간 중심적으로 설계할 수 있는 지표가 되었습니다.

***

이후 점차 **컴퓨터 공학**에서도 널리 사용되기 시작하며 **GoF**(Gang of Four: 4명의 소프트웨어 공학자들)이 _<_[_Design Patterns: Elements of Reusable Object-Oriented Software_](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612)_>_ (한글 번역: [“디자인 패턴: 재사용 가능한 객체 지향 소프트웨어의 요소”](https://m.yes24.com/Goods/Detail/17525598)) 이라는 책을 통해 **23가지 디자인 패턴**을 정리했습니다.

## 건축학 그리고 소프트웨어 공학에서 말하는 디자인 패턴의 공통점

1. 패턴은 단순한 반복이 아니라, **맥락(Context)에 맞는** 최적의 해결책을 제공해야 합니다.
2. 좋은 디자인 패턴은 재사용 가능하고, 가독성이 뛰어나며, 유지보수가 용이해야 합니다.
3. **작은 요소들이 모여서 더 크고 유기적인 시스템을 형성**해야 합니다.

<details>

<summary>소프트웨어 공학에서 디자인 패턴으로 해결할 수 있는 문제에는 어떤게 있을까?</summary>

### 1️⃣ **복잡한 객체 생성 문제**

소프트웨어 개발 과정에서는 종종 복잡한 설정이나 초기화가 필요한 객체를 생성해야 하는 경우가 있습니다. 이 상황에서 **Factory Method** 패턴을 활용할 수 있습니다.&#x20;

예를 들어, 다양한 DB 드라이버에 따라 서로 다른 연결 객체를 생성해야 할 때, 팩토리 메서드가 객체 생성의 세부 사항을 클라이언트 코드에서 추상화하여 코드의 변경에 대한 민감성을 줄여줍니다. 이를 통해 유지보수성을 높이고, 새로운 데이터베이스 타입 추가 시에도 기존 코드를 수정할 필요가 없어 유연성이 향상됩니다.

### 2️⃣ **상태 변경에 따른 여러 객체의 자동 업데이트 문제**

애플리케이션에서 어떤 객체의 상태가 변경될 때, 그 변화에 여러 객체들이 즉시 반응해야 하는 경우가 있습니다. 이러한 경우 **Observer 패턴**을 활용될 수 있습니다.&#x20;

실제 사례로는 이메일 클라이언트에서 새로운 메일이 도착했을 때, 이를 모든 열린 창이나 알림 시스템이 자동으로 업데이트 받도록 할 수 있습니다. 옵저버 패턴을 통해 각 객체 간의 관계를 느슨하게 유지하면서, 변화에 대한 대응이 자동적으로 이루어질 수 있습니다.

### 3️⃣ **행위의 런타임 교체 필요성 문제**

특정 기능이나 작업을 다양한 방식으로 실행해야 할 상황에서 **Strategy 패턴**을 적용할 수 있습니다.&#x20;

예를 들면, 결제 처리 시스템에서는 사용자가 선택하는 결제 방식에 따라 다른 결제 처리 로직을 적용해야 할 수 있습니다. 전략 패턴을 통해 각 결제 방법을 전략으로 캡슐화하고, 런타임에 적절한 전략을 쉽게 교체할 수 있습니다. 코드의 모듈화를 돕고, 새로운 결제 방법을 추가할 때 기존 코드에 미치는 영향을 최소화합니다.

### 4️⃣ **전역 상태 관리 문제**

어플리케이션 전반에서 특정 자원이나 상태를 일관되게 관리해야 할 때, **Singleton 패턴**이 유용합니다.&#x20;

예를 들어, 애플리케이션 설정을 전역적으로 관리하는 경우, 싱글톤 패턴을 통해 설정 객체를 단일 인스턴스로 유지할 수 있습니다. 이렇게 하면 모든 모듈에서 동일한 인스턴스에 접근할 수 있게 해주어 데이터 일관성을 보장하지만, 객체의 전역 접근으로 인한 결합도 증가 및 테스트의 어려움이라는 단점도 고려해야 합니다.

</details>

## 디자인 패턴의 주요 목적

1. 코드 재사용성 증가 → 반복적인 구조를 피하고 효율적인 설계를 유도하기
2. 유지보수 용이 → 변경이 발생하더라도 최소한의 수정으로 해결하기
3. 소프트웨어 확장성 증가 → 새로운 기능 추가 쉽게 하기
4. 설계의 표준화 → 팀원 간의 협업 시 일관된 코드 스타일 유지하기

## 디자인 패턴의 3가지 분류

{% stepper %}
{% step %}
### 생성(Creational) 패턴 → 객체 생성과 관련

* 객체를 효율적으로 생성하고 관리하는 방법을 제공합니다.
* 객체 생성 과정에서 중복을 줄이고, 유연성을 높입니다.

<table><thead><tr><th width="270">디자인 패턴 이름</th><th>설명</th></tr></thead><tbody><tr><td><a href="undefined.md">Singleton (싱글톤)</a></td><td>하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴</td></tr><tr><td><a href="../design-pattern/factory-method.md">Factory Method (팩토리 메서드)</a></td><td>객체 생성 주체를 하위클래스로 위임하는 패턴</td></tr><tr><td><a href="../design-pattern/factory-method.md#factory-method-abstract-factory">Abstract Factory (추상 팩토리)</a></td><td>관련된 객체들을 생성할 수 있도록 하는 팩토리들의 집합</td></tr><tr><td>Builder (빌더)</td><td>복잡한 객체 생성을 단계적으로 진행할 수 있도록 하는 패턴</td></tr><tr><td>Prototype (프로토타입)</td><td>기존 객체를 복사하여 새로운 객체를 생성하는 패턴</td></tr></tbody></table>


{% endstep %}

{% step %}
### 구조(Structural) 패턴 → 객체와 클래스의 구조 관련

* 객체 간의 관계를 쉽게 정의하고 유지보수하기 위한 패턴
* 클래스 간의 결합도를 줄이고, 효율적인 상속 및 조합을 유도

<table><thead><tr><th width="266">디자인 패턴 이름</th><th>설명</th></tr></thead><tbody><tr><td>Adapter (어댑터)</td><td>인터페이스가 다른 객체들을 연결해주는 패턴</td></tr><tr><td>Bridge (브릿지)</td><td>구현부를 추상화하여 독립적으로 확장할 수 있도록 하는 패턴</td></tr><tr><td>Composite (컴포지트) </td><td>객체를 트리 구조로 만들어 단일 객체와 그룹을 동일하게 다룸</td></tr><tr><td>Decorator (데코레이터)</td><td>객체에 동적으로 기능을 추가하는 패턴</td></tr><tr><td>Facade (파사드)</td><td>복잡한 서브시스템을 단순한 인터페이스로 제공하는 패턴</td></tr><tr><td>Proxy (프록시)</td><td>접근을 제어하기 위해 대리 객체를 사용하는 패턴</td></tr></tbody></table>


{% endstep %}

{% step %}
### 행위(Behavioral) 패턴 → 객체 간의 상호작용과 책임 분배

* 객체 간의 커뮤니케이션을 효과적으로 처리하는 패턴
* 코드의 응집도를 높이고, 결합도를 낮추는 데 도움

<table><thead><tr><th width="273">디자인 패턴 이름</th><th>설명</th></tr></thead><tbody><tr><td>Chain of Responsibility (책임 연쇄)</td><td>요청을 여러 개의 처리 객체가 연속적으로 처리하는 패턴</td></tr><tr><td>Command (커맨드)</td><td>요청을 캡슐화하여 실행 취소 기능을 제공하는 패턴</td></tr><tr><td>Interpreter (인터프리터) </td><td>특정 언어의 문법을 분석하고 실행하는 패턴</td></tr><tr><td>Iterator (이터레이터)</td><td>컬렉션 요소들을 순차적으로 탐색하는 패턴</td></tr><tr><td>Mediator (미디에이터)</td><td>객체 간의 직접적인 통신을 방지하고 중재자를 통해 조정하는 패턴</td></tr><tr><td>Memento (메멘토)</td><td>객체 상태를 저장하고 복원할 수 있는 패턴</td></tr><tr><td><a href="broken-reference">Observer (옵저버)</a></td><td>객체 상태 변화 시, 관련 객체들에 자동으로 알림을 보내는 패턴</td></tr><tr><td>State (스테이트) </td><td>객체의 상태에 따라 다른 행동을 수행하는 패턴</td></tr><tr><td><a href="broken-reference">Strategy (전략)</a></td><td>알고리즘을 캡슐화하여 동적으로 변경할 수 있도록 하는 패턴</td></tr><tr><td>Template Method (템플릿 메서드)</td><td>알고리즘의 구조를 정의하고, 세부 사항은 서브클래스에서 구현하는 패턴</td></tr><tr><td>Visitor (비지터)</td><td>객체 구조와 별개로 연산을 추가할 수 있도록 하는 패턴</td></tr></tbody></table>
{% endstep %}
{% endstepper %}

지금부터 하나씩 흔히 쓰이는 디자인 패턴들을 살펴보겠습니다.&#x20;



#### **참고 자료**

{% embed url="https://refactoring.guru/design-patterns/what-is-pattern" %}
