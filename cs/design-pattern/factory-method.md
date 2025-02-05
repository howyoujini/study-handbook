---
description: 객체 생성의 책임을 하위 클래스에 위임하는 패턴
---

# 팩토리 메서드 (Factory Method)

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://refactoring.guru/ko/design-patterns/factory-method">https://refactoring.guru/ko/design-patterns/factory-method</a></p></figcaption></figure>

팩토리 메소드 패턴(Factory Method Pattern)은 객체 **생성을 캡슐화하여, 하위 클래스에서** 객체의 **생성 방식을 결정하도록 하는 디자인 패턴**입니다. 즉, 객체를 직접 생성하는 것이 아니라, **객체 생성을 담당하는 메소드를 통해 객체를 반환**하도록 합니다. 이 패턴을 활용하면 클라이언트 코드가 특정 클래스의 인스턴스를 직접 생성하지 않고, 추상화된 인터페이스를 통해 객체를 생성할 수 있습니다.



### 팩토리 패턴과 팩토리 메서드 패턴은 어떻게 다를까?

둘 다 객체 생성을 캡슐화하는 디자인 패턴이지만, 그 방식과 의도에서 차이가 있습니다.

<details>

<summary>팩토리 패턴 (Factory Pattern)</summary>

객체 생성을 직접 수행하는 것이 아니라, 객체 생성을 담당하는 별도의 클래스(팩토리 클래스)를 두고, 이를 통해 객체를 생성하는 방식입니다. 즉, **객체 생성 로직을 클라이언트 코드에서 분리**하여 관리하는 것입니다.&#x20;

주로 **객체 생성 방식이 고정**되어 있고, 여러 서브클래스를 관리할 때 유용합니다.&#x20;

PizzaFactory를 통해 **다양한 피자를 생성하는 경우**를 살펴보겠습니다.

{% code lineNumbers="true" fullWidth="false" %}
```javascript
class Pizza {
  constructor(name) {
    this.name = name;
  }

  bake() {
    console.log(`${this.name} 피자를 굽습니다.`);
  }
}

class PizzaFactory {
  static createPizza(type) {
    switch (type) {
      case "cheese":
        return new Pizza("치즈");
      case "pepperoni":
        return new Pizza("페퍼로니");
      default:
        return new Pizza("기본");
    }
  }
}

// 사용
const pizza = PizzaFactory.createPizza("cheese");
pizza.bake(); // 치즈 피자를 굽습니다.
```
{% endcode %}

</details>

<details>

<summary>팩토리 메서드 패턴 (Factory Method Pattern)</summary>

팩토리 메서드는 객체 **생성을 위한 인터페이스(추상 메서드)**&#xB97C; 정의하고, **하위 클래스가 이를 구현**하여 객체를 생성하도록 하는 방식입니다. 즉, 객체 생성을 하위 클래스에서 결정하도록 유도하여, **개방-폐쇄 원칙(OCP)**&#xC744; 따르게 합니다. 이렇게 되면 **확장 가능성이 높아 새로운 객체 타입을 추가하기 쉽습**니다.&#x20;

PizzaStore가 `createPizza()`를 제공하고, 서브클래스에서 이를 구현합니다.

```javascript
class Pizza {
  constructor(name) {
    this.name = name;
  }

  bake() {
    console.log(`${this.name} 피자를 굽습니다.`);
  }
}

class PizzaStore {
  orderPizza(type) {
    const pizza = this.createPizza(type);
    pizza.bake();
    return pizza;
  }

  // 팩토리 메서드 (하위클래스에서 구현해야 함)
  createPizza(type) {
    throw new Error("createPizza() 메서드를 구현해야 합니다.");
  }
}

class ItalianPizzaStore extends PizzaStore {
  createPizza(type) {
    if (type === "cheese") return new Pizza("이탈리안 치즈");
    if (type === "pepperoni") return new Pizza("이탈리안 페퍼로니");
    return new Pizza("이탈리안 기본");
  }
}

// 사용
const italianStore = new ItalianPizzaStore();
italianStore.orderPizza("cheese"); // 이탈리안 치즈 피자를 굽습니다.
```

</details>

<table><thead><tr><th width="141">구분</th><th>팩토리 패턴 (Factory Pattern)</th><th>팩토리 메서드 패턴 (Factory Method Pattern)</th></tr></thead><tbody><tr><td>객체 생성 책임</td><td>하나의 팩토리 클래스가 생성 책임을 가짐</td><td>서브클래스에서 객체 생성 로직을 결정</td></tr><tr><td>유연성</td><td>새로운 객체 유형 추가 시 기존 팩토리 클래스 수정 필요</td><td>새로운 서브클래스를 추가하여 확장 가능 (OCP 준수)</td></tr><tr><td>설계 방식 </td><td>단순한 정적 메서드 또는 별도의 팩토리 클래스를 사용</td><td>상속을 활용해 객체 생성 방식을 변경</td></tr><tr><td>사용 예</td><td>단순한 객체 생성을 중앙 집중화할 때</td><td>다양한 객체 생성을 서브클래스에 위임할 때</td></tr></tbody></table>



## 주요 특징 <a href="#features" id="features"></a>

1. **객체 생성 로직을 캡슐화** → 객체 생성을 직접 하지 않고, **팩토리 메소드를 통해** 생성
2. **유연한 확장성** → 새로운 객체 타입이 추가되더라도 기존 코드 수정 없이 확장 가능
3. **결합도 낮추기** → 클라이언트 코드가 구체적인 클래스를 몰라도 객체를 생성할 수 있도록 유도

## 언제 활용할 수 있을까?

싱글톤 패턴에 비해 팩토리 메서드 패턴을 언제 활용할 수 있을지 이해하는 것이 어려웠습니다. 하지만 **비슷한 객체를 반복적으로(공장처럼) 생성해야 할 경우**에 팩토리 메서드 패턴 사용을 고려해본다고 생각하니 이해가 쉬워졌습니다. 또 개발자가 컴파일 단계에서 어떤 객체를 생성해야할 지 모르고, **런타임 단계에서 동적으로 생성해야 할 때**도 사용할 수 있습니다.

<details>

<summary>플랫폼(Windows, MacOS) 환경에 따라 다른 버튼을 생성해하는 경우</summary>

OS마다 UI 컴포넌트가 다르기 때문에 Button을 직접 생성하면 OS별 분기 처리가 필요합니다. 이 경우 팩토리 메소드 패턴을 활용하면 OS별 버튼을 쉽게 추가할 수 있습니다. 자바스크립트의 `class`를 활용하여 버튼 인터페이스를 만들어보겠습니다.&#x20;

```javascript
// 1. 버튼 인터페이스 정의
class Button {
  render() {
    throw new Error("render() 메소드를 구현해야 합니다.");
  }
}

// 2. 각 OS에 맞는 버튼 클래스 구현
class WindowsButton extends Button {
  render() {
    console.log("Windows 스타일의 버튼 렌더링");
  }
}

class MacOSButton extends Button {
  render() {
    console.log("MacOS 스타일의 버튼 렌더링");
  }
}

// 3. 팩토리 메소드가 있는 창 클래스
class Dialog {
  createButton() {
    throw new Error("createButton() 메소드를 구현해야 합니다.");
  }

  render() {
    const button = this.createButton();
    button.render();
  }
}

// 4. OS에 맞는 팩토리 구현
class WindowsDialog extends Dialog {
  createButton() {
    return new WindowsButton();
  }
}

class MacOSDialog extends Dialog {
  createButton() {
    return new MacOSButton();
  }
}

// 5. 클라이언트 코드
function application(OS) {
  let dialog;

  if (OS === "Windows") {
    dialog = new WindowsDialog();
  } else if (OS === "MacOS") {
    dialog = new MacOSDialog();
  } else {
    throw new Error("지원되지 않는 OS");
  }

  dialog.render();
}

// 사용 예시
application("Windows"); // Windows 스타일의 버튼 렌더링
application("MacOS");   // MacOS 스타일의 버튼 렌더링
```

OS에 따라 적절한 버튼을 생성하여 렌더링할 수 있게 되었습니다. 새로운 OS 타입이 추가될 경우, 기존 코드를 수정할 필요 없이 새로운 클래스만 추가하면 됩니다.&#x20;

</details>

<details>

<summary>다양한 데이터베이스(MySQL, PostgreSQL)를 지원해하는 경우</summary>

어떤 애플리케이션이 다양한 데이터베이스를 지원해야 할 때, 팩토리 메소드 패턴을 사용하면 코드의 변경 없이 쉽게 확장할 수 있습니다.&#x20;

```javascript
// 1. 데이터베이스 인터페이스 정의
class Database {
  connect() {
    throw new Error("connect() 메소드를 구현해야 합니다.");
  }
}

// 2. 특정 데이터베이스 연결 클래스 구현
class MySQLDatabase extends Database {
  connect() {
    console.log("MySQL 데이터베이스에 연결됨");
  }
}

class PostgreSQLDatabase extends Database {
  connect() {
    console.log("PostgreSQL 데이터베이스에 연결됨");
  }
}

// 3. 팩토리 메소드 패턴 적용
class DatabaseFactory {
  createDatabase() {
    throw new Error("createDatabase() 메소드를 구현해야 합니다.");
  }
}

class MySQLDatabaseFactory extends DatabaseFactory {
  createDatabase() {
    return new MySQLDatabase();
  }
}

class PostgreSQLDatabaseFactory extends DatabaseFactory {
  createDatabase() {
    return new PostgreSQLDatabase();
  }
}

// 4. 클라이언트 코드
function getDatabaseConnection(type) {
  let factory;

  if (type === "MySQL") {
    factory = new MySQLDatabaseFactory();
  } else if (type === "PostgreSQL") {
    factory = new PostgreSQLDatabaseFactory();
  } else {
    throw new Error("지원되지 않는 데이터베이스 타입");
  }

  const database = factory.createDatabase();
  database.connect();
}

// 사용 예시
getDatabaseConnection("MySQL"); // MySQL 데이터베이스에 연결됨
getDatabaseConnection("PostgreSQL"); // PostgreSQL 데이터베이스에 연결됨
```

새로운 데이터베이스 유형을 추가하려면, 새로운 `DatabaseFactory`와 Database 클래스를 만들기만 하면 됩니다. 클라이언트 코드(`getDatabaseConnection`)는 데이터베이스 연결 방식이 변경되어도 영향을 받지 않게 됩니다.&#x20;

</details>





## 구현 방법

팩토리 메소드 패턴을 구현하는 방법은 여러 가지가 있습니다. 대표적인 3가지 방법을 살펴보겠습니다.

### 1. Factory 클래스 기반 switch 문&#x20;

<pre class="language-javascript"><code class="lang-javascript"><strong>class Product {
</strong>  constructor(name) {
    this.name = name;
  }

  use() {
    console.log(`${this.name} 제품을 사용합니다.`);
  }
}

<strong>class ProductFactory {
</strong>  static createProduct(type) {
    switch (type) {
      case "A":
        return new Product("상품 A");
      case "B":
        return new Product("상품 B");
      default:
        throw new Error("존재하지 않는 상품 타입입니다.");
    }
  }
}

const productA = ProductFactory.createProduct("A");
const productB = ProductFactory.createProduct("B");

productA.use(); // "상품 A 제품을 사용합니다."
productB.use(); // "상품 B 제품을 사용합니다."
</code></pre>

`ProductFactory`가 객체 생성을 담당하며, 클라이언트는 `createProduct()` 메소드를 통해 제품을 생성합니다. 새로운 제품이 추가될 경우, 팩토리 클래스의 **`switch` 문을 수정**해야 합니다. 이는 **OCP(개방-폐쇄 원칙)에 위배**될 수 있으므로, 이를 해결하기 위한 다른 방식을 살펴보겠습니다.&#x20;

### 2. 인터페이스 기반&#x20;

```javascript
class Product {
  use() {
    throw new Error("use() 메서드는 서브클래스에서 구현해야 합니다.");
  }
}

class ConcreteProductA extends Product {
  use() {
    console.log("상품 A 사용 중...");
  }
}

class ConcreteProductB extends Product {
  use() {
    console.log("상품 B 사용 중...");
  }
}

class ProductFactory {
  static createProduct(type) {
    if (type === "A") return new ConcreteProductA();
    if (type === "B") return new ConcreteProductB();
    throw new Error("잘못된 상품 타입입니다.");
  }
}

const productA = ProductFactory.createProduct("A");
const productB = ProductFactory.createProduct("B");

productA.use(); // "상품 A 사용 중..."
productB.use(); // "상품 B 사용 중..."
```

`ConcreteProductA`, `ConcreteProductB`를 통해 제품 클래스를 확장하여 **`switch` 문을 제거**했습니다. 새로운 제품을 추가할 때, `ProductFactory`의 **내부 로직을 수정하지 않고 새로운 클래스를 추가**하면 됩니다. (확장성 증가, OCP 원칙 준수)

### 3. 추상 팩토리 패턴과의 결합

팩토리 메소드 패턴은 종종 **추상 팩토리 패턴(Abstract Factory Pattern)**&#xACFC; 함께 사용됩니다.

```javascript
class Product {
  constructor(name) {
    this.name = name;
  }

  use() {
    console.log(`${this.name} 제품을 사용합니다.`);
  }
}

class AbstractFactory {
  createProduct() {
    throw new Error("createProduct() 메서드는 서브클래스에서 구현해야 합니다.");
  }
}

class ProductFactoryA extends AbstractFactory {
  createProduct() {
    return new Product("상품 A");
  }
}

class ProductFactoryB extends AbstractFactory {
  createProduct() {
    return new Product("상품 B");
  }
}

const factoryA = new ProductFactoryA();
const factoryB = new ProductFactoryB();

const productA = factoryA.createProduct();
const productB = factoryB.createProduct();

productA.use(); // "상품 A 제품을 사용합니다."
productB.use(); // "상품 B 제품을 사용합니다."
```

팩토리 메소드를 추상 클래스로 정의하여 **각 팩토리마다 독립적인 생성 방식 유지**할 수 있습니다. 클라이언트 코드가 특정 팩토리 인스턴스를 선택하여 사용할 수 있도록 유도합니다.&#x20;



## 팩토리 메소드 패턴의 장점

1. **객체 생성 로직을 분리하여 코드 가독성 향상**
2. **클라이언트 코드의 결합도 낮추기 (Loose Coupling)**
3. **새로운 객체 타입을 쉽게 추가하여 확장성 증가**



## 주의점

* 클래스 수 증가: 각 제품마다 새로운 클래스를 생성해야 하므로 코드가 길어질 수 있음
* 단순한 객체 생성에는 오버헤드가 발생할 수 있음 (단순한 객체는 new 키워드 사용이 더 적합)



팩토리 메소드 패턴은 객체 생성 로직을 캡슐화하고, 유연한 확장을 가능하게 합니다. 다양한 프레임워크와 라이브러리에서 널리 사용되므로, 실제 프로젝트에서 적절히 활용하면 보다 유지보수성이 높은 코드를 작성할 수 있습니다. 여러분은 어떤 사례에서 팩토리 메소드 패턴을 활용해 보셨나요? 😊

#### **참고 자료**

{% embed url="https://refactoring.guru/ko/design-patterns/factory-method" %}

{% embed url="https://youtu.be/ejXUhFKcbIU?si=sHVreG5FawjZYBF_" %}
