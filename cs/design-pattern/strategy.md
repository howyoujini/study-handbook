---
description: 객체의 행위를 변경할 때, 알고리즘을 캡슐화하여 컨텍스트 내부에서 동적으로 교체할 수 있도록 만드는 패턴
---

# 전략 (Strategy)

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p><a href="https://refactoring.guru/ko/design-patterns/strategy">https://refactoring.guru/ko/design-patterns/strategy</a></p></figcaption></figure>

전략 패턴(Strategy Pattern)은 **객체의 행위를 변경할 때, 알고리즘을 캡슐화하여 컨텍스트 내부에서 동적으로 교체할 수 있도록 만드는 패턴**입니다. 즉, 객체가 수행하는 기능(행위)을 미리 정의된 여러 알고리즘 중 하나로 변경할 수 있도록 설계하는 방식입니다.

이 패턴을 활용하면 **동일한 문제를 해결하는** 다양한 알고리즘을 독립적으로 구현할 수 있으며, 런타임에서 원하는 전략을 선택하여 실행할 수 있습니다. 이는 코드의 유지보수성을 높이고, 확장성을 극대화하는 데 유용합니다.



## 전략 패턴의 필요성

어떤 애플리케이션이 다양한 방식으로 동작할 수 있도록 하려면, 다음과 같은 문제를 해결해야 합니다.

* **조건문 남발**: `if-else`나 `switch` 문을 통해 여러 가지 동작을 구현하면 코드가 복잡해지고 유지보수성이 떨어집니다.
* **유연하지 않은 설계**: 객체의 행위를 변경하려면 **기존 코드에 직접적인 수정**이 필요하며, 이는 **OCP(개방-폐쇄 원칙) 위반으로** 이어질 수 있습니다.
* **중복 코드 발생**: 서로 다른 방식으로 동작하는 코드가 반복적으로 등장하면 **유지보수가 어려워**집니다.

전략 패턴을 사용하면 위 문제를 해결할 수 있으며, 객체가 다양한 동작을 필요로 하는 경우에도 코드의 재사용성을 극대화할 수 있습니다.



## 전략 패턴과 템플릿 메소드 패턴의 차이점

전략 패턴은 **행위를 동적으로 변경할 수 있도록 하는 패턴**이며, 각 알고리즘을 별도의 클래스로 분리하여 필요에 따라 선택적으로 사용할 수 있습니다.

반면, 템플릿 메소드 패턴(Template Method Pattern)은 **기본적인 알고리즘 구조를 정의하고, 일부 세부 사항을 하위 클래스에서 구현하도록 하는 패턴**입니다. 즉, 전략 패턴은 실행 중에 알고리즘을 바꿀 수 있는 반면, 템플릿 메소드 패턴은 상속을 통해 일부 동작을 변경하는 구조입니다.



### 전략 패턴 예제

전략 패턴을 활용하여 **소셜 로그인 시스템**을 설계해보겠습니다. 사용자는 Google, Kakao, GitHub 등의 로그인 방법을 선택할 수 있으며, 각 로그인 방식은 전략 패턴을 이용해 독립적으로 구현됩니다.

#### 1. 소셜 로그인 인터페이스 정의

```typescript
interface SocialLoginStrategy {
  login(userInfo: string): void;
}
```

#### 2. 각 소셜 로그인 클래스 구현

```typescript
class GoogleLogin implements SocialLoginStrategy {
  login(userInfo: string): void {
    console.log(`${userInfo} 님이 Google로 로그인했습니다.`);
  }
}

class KakaoLogin implements SocialLoginStrategy {
  login(userInfo: string): void {
    console.log(`${userInfo} 님이 Kakao로 로그인했습니다.`);
  }
}

class GitHubLogin implements SocialLoginStrategy {
  login(userInfo: string): void {
    console.log(`${userInfo} 님이 GitHub로 로그인했습니다.`);
  }
}
```

#### 3. 컨텍스트 클래스 (로그인 관리)

```typescript
class LoginContext {
  private strategy: SocialLoginStrategy;

  constructor(strategy: SocialLoginStrategy) {
    this.strategy = strategy;
  }

  setStrategy(strategy: SocialLoginStrategy): void {
    this.strategy = strategy;
  }

  executeLogin(userInfo: string): void {
    this.strategy.login(userInfo);
  }
}
```

#### 4. 전략 패턴 적용 예시

```typescript
const loginContext = new LoginContext(new GoogleLogin());
loginContext.executeLogin("Ella");  // "Ella 님이 Google로 로그인했습니다."

loginContext.setStrategy(new KakaoLogin());
loginContext.executeLogin("Ella");  // "Ella 님이 Kakao로 로그인했습니다."
```

전략 패턴을 활용하면 객체의 행위를 동적으로 변경할 수 있어 유지보수성과 확장성이 향상됩니다. 위 예제처럼 **로그인 방식뿐만 아니라 검색 필터링, 결제 방식, 추천 시스템** 등 다양한 곳에서 활용할 수 있습니다.



### 전략 패턴의 장점

* **코드의 유연성 증가** → 알고리즘을 런타임에 변경 가능&#x20;
* **캡슐화된 알고리즘** → 각 전략이 독립적으로 구현되므로 유지보수가 용이&#x20;
* **결합도 감소** → 컨텍스트와 알고리즘이 분리되어 모듈화됨

### 전략 패턴의 단점

* **클래스 수 증가** → 새로운 전략이 추가될 때마다 별도의 클래스를 만들어야 함&#x20;
* **설정 과정 필요** → 전략을 동적으로 변경하려면 추가적인 설정 코드가 필요함

### 언제 활용할 수 있을까?

* **동일한 기능을 수행하지만 알고리즘이 여러 개 존재하는 경우** (예: 정렬 알고리즘, 인증 방식)
* **런타임에서 알고리즘을 변경해야 하는 경우** (예: 소셜 로그인, 결제 방식 선택)
* **상속보다 객체 조합을 활용하여 유연성을 높이고 싶은 경우**
* **동일한 작업을 수행하지만, 여러 조건에 따라 실행 방식을 변경해야 하는 경우** (예: AI 추천 시스템에서 추천 알고리즘을 다르게 적용)
* **애플리케이션의 기능 확장이 빈번히 발생하는 경우** (예: 게임에서 무기, 이동 방식 등의 변경)



전략 패턴은 실무에서 **UI, 인증 시스템, 검색 필터링, 결제 시스템 등** 다양한 곳에서 활용됩니다. 여러분은 어떤 사례에서 전략 패턴을 활용해 보셨나요? 😊

***
