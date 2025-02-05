---
description: 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴
---

# 싱글톤 (Singleton)

<figure><img src="../../.gitbook/assets/singleton-2x.png" alt=""><figcaption><p><a href="https://refactoring.guru/ko/design-patterns/singleton">https://refactoring.guru/ko/design-patterns/singleton</a></p></figcaption></figure>

싱글톤 패턴(Singleton Pattern)은 특정 클래스의 인스턴스를 **오직 하나만 생성하도록 보장**하고, 그 인스턴스를 **어디서든** 접근할 수 있도록 제공하는 디자인 패턴입니다. 이 패턴은 객체 생성을 제한하고, 동일한 객체를 여러 곳에서 공유해야 할 때, 즉 객체 생성이 비싼 로직일 때 싱글톤 패턴을 주로 활용합니다.&#x20;



## 주요 특징 <a href="#features" id="features"></a>

1\. 단 하나의 인스턴스만 존재 → 새로운 인스턴스가 **생성되지 않도록** 하기

2\. 글로벌 접근 가능 → 인스턴스를 **어디서든 접근 가능**하도록 하기

3\. 클래스 내부에서만 객체 생성 가능 → **외부에서 `new` 키워드를 사용하더라도 단 하나의 동일 인스턴스 유지하기**



## 언제 활용할 수 있을까? <a href="#use-cases" id="use-cases"></a>

어디에서 어떻게 활용되는지 미리 알면 더 재밌게 싱글톤 패턴을 공부할 수 있겠죠? 😉

<details>

<summary><strong>데이터베이스 연결 (DB Connection)</strong></summary>

🚨 매번 새로운 DB 연결을 생성하면 성능 저하

애플리케이션이 데이터베이스와 통신할 때 매번 새로운 연결을 생성하면 엄청난 비용 발생합니다. DB 서버는 연결(Connection) 개수에 제한이 있으며, 불필요한 연결이 많아지면 서버가 다운될 수도 있습니다.

**✅ 싱글톤 패턴을 사용하면?**

* 하나의 DB 연결을 공유하여 여러 요청이 동일한 연결을 재사용
* DB 연결 수를 최적화하여 서버 부하를 줄이고 성능을 향상

**🔍 싱글톤 DB Connection 구현 예제 (Node.js)**

```javascript
const mysql = require("mysql2");

class Database {
  static instance;
  
  constructor() {
    if (!Database.instance) {
      this.connection = mysql.createConnection({
        host: "localhost",
        user: "root",
        password: "password",
        database: "mydb",
      });
      Database.instance = this;
    }
    
    return Database.instance;
  }

  getConnection() {
    return this.connection;
  }
}

const db1 = new Database();
const db2 = new Database();
console.log(db1.getConnection() === db2.getConnection()); // true (같은 DB 연결 사용)
```

</details>

<details>

<summary><strong>앱 설정 관리 (</strong>Configuration <strong>Manager)</strong></summary>

🚨 여러 곳에서 앱 설정을 사용할 때 **일관성 문제** 발생

환경 설정(Config)이 여러 곳에서 관리되면 설정 값이 변경될 때 **일관성이 깨질 위험**이 있습니다. 설정이 각 모듈에서 개별적으로 관리되면 코드가 복잡해지고 유지보수가 어려워지겠죠.

**✅ 싱글톤 패턴을 사용하면?**

* 앱의 설정 값을 하나의 인스턴스로 유지하여 어디서든 일관된 값을 사용가능
* 설정 변경 시 전역적으로 즉시 반영 가능

**🔍 싱글톤 Config Manager 구현 예제**

```javascript
class Config {
  static instance;
  
  constructor() {
    if (!Config.instance) {
      this.settings = { theme: "dark", language: "en" };
      Config.instance = this;
    }
    return Config.instance;
  }

  getConfig() {
    return this.settings;
  }

  updateConfig(newConfig) {
    this.settings = { ...this.settings, ...newConfig };
  }
}

const config1 = new Config();
config1.updateConfig({ theme: "light" });

const config2 = new Config();
console.log(config2.getConfig()); // { theme: "light", language: "en" } (같은 객체 공유)
```

</details>

<details>

<summary><strong>로깅 시스템 (Logger)</strong></summary>

🚨 로그 객체를 매번 새로 생성하면 성능 문제가 발생

로그를 남길 때마다 새로운 Logger 객체를 생성하면 **메모리 사용량 증가** 및 **불필요한** **객체 생성 비용 발생**합니. 특히 파일 I/O 또는 네트워크 I/O 기반 로그 시스템에서는 새로운 연결이 생성되면 성능 저하가 심각할 수 있습니다.

**✅ 싱글톤 패턴을 사용하면?**

* 하나의 Logger 인스턴스를 유지하여 모든 로그 요청이 동일한 객체를 사용하도록 가능
* 불필요한 객체 생성을 방지하고, 메모리 사용량을 최소화 가능
* 단일 인스턴스로 파일 및 네트워크 연결을 공유하여 I/O 성능을 최적화

**🔍 싱글톤 Logger 구현 예제 (JavaScript)**

```javascript
class Logger {
  static instance;
  
  constructor() {
    if (!Logger.instance) {
      this.logs = [];
      Logger.instance = this;
    }
    return Logger.instance;
  }

  log(message) {
    this.logs.push(message);
    console.log(`[LOG]: ${message}`);
  }

  getLogs() {
    return this.logs;
  }
}

const logger1 = new Logger();
const logger2 = new Logger();
const logger3 = new Logger()
logger1.log("Only one Logger!");

console.log(logger1 === logger2); // true (같은 객체 공유)
console.log(logger1 === logger3); // true 
console.log(logger1.logs); // ["Only one Logger!"]
console.log(logger2.logs); // ["Only one Logger!"]
console.log(logger3.logs); // ["Only one Logger!"]
```

</details>

<details>

<summary><strong>전역 상태 관리 (State Manager)</strong></summary>

🚨 여러 컴포넌트가 상태를 공유할 때 **일관성이 깨질 위험**

상태를 개별적으로 관리하면 각 컴포넌트가 서로 다른 상태를 가질 수 있습니다. 그래서 Redux나 Zustand 같은  전역 상태 관리 라이브러리는 싱글톤 패턴을 기반으로 동작하여 일관된 상태를 유지할 수 있도록 도와줍니다.

✅ **싱글톤 패턴을 사용하면?**

* 하나의 상태 저장소(Store)를 전역에서 관리하여 일관된 상태 유지
* 여러 컴포넌트가 같은 상태를 공유하며, 상태 변경이 모든 곳에 즉시 반영

🔍 **Redux Store를 싱글톤으로 활용하는 예제**

```javascript
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";

const store = configureStore({
  reducer: { counter: counterReducer },
});

export default store; // 싱글톤 Store 인스턴스를 export
```

</details>

## 구현 방법 <a href="#implementation" id="implementation"></a>

싱글톤 패턴을 구현하는 방식은 여러 가지가 있습니다. 가장 많이 사용되는 3가지 방법을 살펴보겠습니다.&#x20;

### 1. static instance <a href="#static-instance" id="static-instance"></a>

```javascript
class Singleton {
  static instance; // 단 하나의 인스턴스를 저장할 변수

  constructor() {
    if (Singleton.instance) { // 인스턴스가 이미 있다면,
      return Singleton.instance; // 기존 인스턴스를 반환
    }
    Singleton.instance = this; // 새로운 인스턴스를 저장
  }

  sayHello() {
    console.log("Hello from Singleton!");
  }
}

const obj1 = new Singleton();
const obj2 = new Singleton();

console.log(obj1 === obj2); // true (같은 객체를 공유)
obj1.sayHello(); // "Hello from Singleton!"
```

`Singleton` 클래스는 `instance`라는 **정적 변수**를 가집니다. `new`를 사용해 새로운 객체를 생성하면, 생성자 함수가 기존에 `instance`가 존재하면 새로운 객체를 만들지 않고 기존 객체를 반환하도록 합니다. 그리고 `instance`가 없다면 새로운 객체를 생성하게 됩니다. 따라서 여러 번 `new Singleton()`을 호출해도 **항상 같은 객체를 참조**하게 됩니다.

### 2. getInstance() <a href="#get-instance" id="get-instance"></a>

생성자를 `private`으로 두고, `getInstance()` 메서드로 접근하는 방식을 더 많이 권장하고 있습니다. 왜냐하면 `static instance` 변수를 사용해 싱글톤 패턴을 구현하면, 클라이언트 코드가 직접 `instance` 변수에 접근할 위험이 있기 때문입니다.&#x20;

```javascript
class Singleton {
  static instance = null;

  constructor() {
    if (Singleton.instance) {
      throw new Error("Use Singleton.getInstance() instead of new!");
    }
    Singleton.instance = this;
  }

  static getInstance() {
    if (!Singleton.instance) {
      Singleton.instance = new Singleton();
    }
    return Singleton.instance;
  }

  sayHello() {
    console.log("Hello from Singleton!");
  }
}

const obj1 = Singleton.getInstance();
const obj2 = Singleton.getInstance();

console.log(obj1 === obj2); // true (항상 같은 인스턴스 반환)
```

`getInstance()`를 통해서만 인스턴스를 생성할 수 있도록 제한합니다. 즉, `new Singleton()`을 직접 호출하면 에러가 발생되도록 하여 **`new` 키워드를 사용하는 것을 방지**합니다. `Singleton.getInstance()`를 호출하면 항상 동일한 인스턴스를 반환합니다.

### 3. 전역 설정 객체 <a href="#global-states" id="global-states"></a>

```javascript
class Config {
  static instance;
  configData = {};

  constructor() {
    if (!Config.instance) {
      Config.instance = this;
      this.configData = { theme: "dark", language: "en" };
    }
    return Config.instance;
  }

  getConfig() {
    return this.configData;
  }

  updateConfig(newConfig) {
    this.configData = { ...this.configData, ...newConfig };
  }
}

const config1 = new Config();
const config2 = new Config();

console.log(config1.getConfig()); // { theme: "dark", language: "en" }
config1.updateConfig({ theme: "light" });

console.log(config2.getConfig()); // { theme: "light", language: "en" } (같은 객체를 공유)
console.log(config1 === config2); // true (같은 인스턴스)
```

`configData`를 전역적으로 관리하여 설정을 공유할 수 있습니다. `updateConfig()`로 설정을 변경하면, **참조하고 있는 다른 곳에서도 변경 사항이 반영**됩니다. 즉, `config1`과 `config2`는 같은 인스턴스를 공유하게 됩니다.&#x20;



이제까지 싱글톤 패턴을 구현할 수 있는 여러 방법에 대해 설명했습니다. 이 패턴을 활용했을 때 어떤 점이 좋을까요?

## 성능 최적화 측면에서의 장점 <a href="#benefits" id="benefits"></a>

### 1. 객체 생성을 한 번만 수행하여 불필요한 메모리 낭비 방지 <a href="#unnecessary-instance" id="unnecessary-instance"></a>

매번 `new`를 호출하여 새로운 객체를 생성하는 대신, 하나의 인스턴스를 재사용하여 메모리 사용을 줄입니다. 특히, 데이터베이스 연결, 네트워크 요청, 설정 관리 등의 무거운 객체를 효율적으로 관리 가능합니다.&#x20;

#### Eager Initialization vs Lazy Initialization

객체를 애플리케이션 시작 시 생성(Eager)할지, 실제 사용 시점까지 미룰지(Lazy) 선택하는 것도 중요한 성능 최적화 요소입니다. 메모리 사용이 크지 않고 반드시 필요한 객체라면 Eager 방식이 더 나을 수도 있습니다.

### 2. 객체 생성 비용 감소 <a href="#reduced-object-cost" id="reduced-object-cost"></a>

객체 생성 비용(Initialization Cost) 을 줄여 성능을 향상시킬 수 있습니다. 예를 들어, **DB Connection Pooling**과 같은 기능에서는 매번 새로운 DB 연결을 생성하면 커넥션 설정 비용(Handshake, Authentication 등) 이 발생하여 성능 저하를 유발합니다. 싱글톤을 활용하여 하나의 연결 객체를 유지하면, 불필요한 연결 생성 비용을 절감할 수 있습니다. **JDBC**(Java Database Connectivity) Connection Pooling(HikariCP, DBCP)에서도 동일한 개념이 적용됩니다.

#### 오브젝트 풀링(Object Pooling)과의 조합

생성 비용이 높은 객체(예: Thread, Database Connection, Network Client)를 재사용할 수 있도록, 오브젝트 풀링과 함께 활용하면 추가적인 최적화를 할 수 있습니다. ThreadPoolExecutor 같은 Java의 쓰레드 풀도 비슷한 원리로 동작합니다.

### 3. 캐싱을 활용한 성능 개선 <a href="#caching" id="caching"></a>

API 요청 결과나 설정 값을 캐싱하여 불필요한 연산을 줄이며 중복 데이터 처리를 하지 않고 속도를 향상시킬 수 있습니다. 또한 데이터베이스에서 자주 조회하는 값(예: 사용자 설정, 권한 정보)을 캐싱하여 성능을 개선할 수 있습니다.

#### 싱글톤 + Redis / Memcached 같은 분산 캐싱 활용

Redis, Memcached 등을 사용하면 서버 간 캐싱 데이터 일관성을 유지하면서 성능을 최적화할 수 있습니다. 특히, 여러 서버에서 동일한 캐시 데이터를 공유해야 하는 경우 유용합니다.

#### Local Cache와 Global Cache의 조합

* **Local Cache**: 싱글톤 인스턴스를 활용하여 서버 메모리에 캐싱 (예: LRU 캐싱) 할 수 있습니다.
* **Global Cache**: Redis, Memcached 같은 분산 캐시를 활용하여 여러 서버에서 캐시 공유할 수 있습니다.



{% hint style="warning" %}
싱글톤 패턴이면 무조건 좋을까요? **단점 혹은 문제점은 없을까요?**
{% endhint %}

## 문제점과 해결 방법 <a href="#problems-solutions" id="problems-solutions"></a>

싱글톤 패턴은 전역적으로 하나의 객체를 공유하는 패턴이기 때문에 잘못 사용하면 예상치 못한 문제를 일으킬 수 있습니다.

### 1. 멀티스레드 환경에서의 동기화 이슈 <a href="#multi-thread-issue" id="multi-thread-issue"></a>

Java, C++ 같은 **멀티스레드 환경에서** 싱글톤 객체를 **동시 접근**하면, 여러 스레드가 동시에 인스턴스를 생성할 가능성이 있습니다. 즉, **경쟁 조건(Race Condition)**&#xC774; 발생하면, 싱글톤이 깨지고  락(Lock) 사용이 필요할 수 있습니다.

```java
// Java 예제
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**✅** Java 의 경우, **synchronized** 키워드를 사용하여 멀티스레드 환경에서도 안전하게 동작하도록 합니다.

하지만 **자바스크립트의 경우 싱글스레드 환경**이므로 동기화 문제는 발생하지 않습니다. 다만 **Web Workers와 같이 멀티스레드 환경을 고려**해야 할 수도 있습니다.&#x20;

### 2. 테스트의 어려움 <a href="#test-difficulty" id="test-difficulty"></a>

싱글톤 객체를 변경하면 여러 곳에서 영향을 받기 때문에 모듈 간의 의존성이 증가합니다. 즉, 특정 테스트가 싱글톤의 상태를 변경하면, 다른 테스트에 영향을 줄 수 있기 때문에 **테스트하기 어렵**습니다.

```javascript
class Database {
  static instance;
  data = [];

  constructor() {
    if (!Database.instance) {
      Database.instance = this;
    }
    return Database.instance;
  }

  addData(record) {
    this.data.push(record);
  }

  getData() {
    return this.data;
  }
}

// 테스트 코드
const db1 = new Database();
db1.addData("Test Record");

const db2 = new Database();
console.log(db2.getData()); // ["Test Record"] (다른 테스트에도 영향)
```

`db1`에서 데이터를 추가했더니 `db2`에서도 같은 데이터를 공유합니다. 테스트 환경에서는 싱글톤을 Mock 객체로 대체해보겠습니다. 필요할 경우, 리셋 가능한 싱글톤 객체를 만들 수도 있습니다.

```javascript
class Database {
  static instance;
  data = [];

  constructor() {
    if (!Database.instance) {
      Database.instance = this;
    }
    return Database.instance;
  }

  addData(record) {
    this.data.push(record);
  }

  getData() {
    return this.data;
  }

  reset() { // 테스트를 위해 리셋 기능 추가
    this.data = [];
  }
}

// 테스트 코드
const db1 = new Database();
db1.addData("Test Record");

console.log(db1.getData()); // ["Test Record"]

db1.reset(); // 테스트 간 데이터 초기화
console.log(db1.getData()); // [] (다시 초기화됨)
```

✅ `reset()` 메서드를 추가하여 테스트 간 데이터 공유 문제를 해결할 수 있습니다.

### 3. 글로벌 상태 공유로 인한 부작용 <a href="#side-effects" id="side-effects"></a>

싱글톤 객체는 전역적으로 공유되기 때문에, 프로그램의 여러 부분에서 동일한 데이터를 변경할 수 있습니다. 특정 코드에서 싱글톤의 상태를 변경하면 모든 코드에 영향을 미칠 수 있기 때문에 Side Effect(부작용) 발생 가능성이 높아지고, 디버깅이 어려워질 가능성이 있습니다.&#x20;

```javascript
class Singleton {
  constructor() {
    if (!Singleton.instance) {
      Singleton.instance = this;
      this.config = { theme: "dark" };
    }
    return Singleton.instance;
  }

  updateTheme(theme) {
    this.config.theme = theme;
  }
}

const instance1 = new Singleton();
const instance2 = new Singleton();

instance1.updateTheme("light"); 
console.log(instance2.config.theme); // "light" (예상과 다르게 변경됨)
```

instance1에서 테마를 변경했더니 instance2에도 영향을 줍니다.&#x20;

전역 상태를 직접 변경하지 않고, Immutable(불변) 상태 관리 기법을 적용하는

Redux/Zustand 같은 상태 관리 라이브러리를 활용해보겠습니다.&#x20;

```javascript
class Singleton {
  static instance;
  config;

  constructor() {
    if (!Singleton.instance) {
      Singleton.instance = this;
      this.config = Object.freeze({ theme: "dark" }); // 변경 불가능한 상태
    }
    return Singleton.instance;
  }

  getConfig() {
    return this.config;
  }
}

const instance1 = new Singleton();
console.log(instance1.getConfig().theme); // "dark"

instance1.getConfig().theme = "light"; // 변경 시도해도 반영 안됨
console.log(instance1.getConfig().theme); // "dark"
```

✅ **Immutable 객체** `Object.freeze()`를 사용하여 수정 불가능하게 할 수 있습니다.&#x20;

### 4. 모듈 간 강한 의존성 증가 <a href="#coupling" id="coupling"></a>

싱글톤 인스턴스가 여러 곳에서 직접 참조되면, **모듈 간 강한 의존성(결합도, Coupling)**&#xC774; 생깁니다. 즉, 나중에 다중 인스턴스를 만들고 싶어도 어려울 수 있습니다. 더불어 한 번에 두 가지의 문제를 동시에 해결하기 때문에 단일 책임 원칙(SRP, Single Responsibility Principle)을 위배할 수 있습니다.

```javascript
class Logger {
  static instance;

  constructor() {
    if (!Logger.instance) {
      Logger.instance = this;
    }
    return Logger.instance;
  }

  log(message) {
    console.log(`LOG: ${message}`);
  }
}

class Service {
  constructor() {
    this.logger = new Logger(); // 직접 싱글톤 인스턴스를 사용
  }

  process() {
    this.logger.log("Processing data...");
  }
}
```

Service 클래스가 Logger 싱글톤에 강하게 결합되어 있습니다.

**의존성 주입(Dependency Injection, DI)**&#xC744; 사용하여 결합도를 줄여보겠습니다.&#x20;

```javascript
class Logger {
  log(message) {
    console.log(`LOG: ${message}`);
  }
}

class Service {
  constructor(logger) {
    this.logger = logger;
  }

  process() {
    this.logger.log("Processing data...");
  }
}

// 외부에서 Logger 인스턴스를 주입
const loggerInstance = new Logger();
const service = new Service(loggerInstance);
service.process(); // "LOG: Processing data..."
```

✅ `Logger`를 직접 참조하지 않고, **외부에서 주입**받도록 수정하여 유연성을 증가시킬 수 있습니다.



{% hint style="success" %}
4가지 문제점과 해결방법을 표로 간단히 **정리**해보겠습니다.
{% endhint %}

<table><thead><tr><th width="303">문제점</th><th>해결 방법</th></tr></thead><tbody><tr><td>멀티스레드 환경에서의 동기화 이슈</td><td>synchronized(Java), volatile 변수 사용(Java)</td></tr><tr><td>테스트의 어려움</td><td>Mock 객체 또는 Reset 메서드 활용</td></tr><tr><td>글로벌 상태 공유로 인한 부작용</td><td>불변 객체(Object.freeze()) 사용</td></tr><tr><td>모듈 간 강한 의존성 증가</td><td>의존성 주입(DI) 사용</td></tr></tbody></table>



## JavaScript ES6 Module 활용

ES6 모듈 시스템을 이용하면 자동으로 싱글톤처럼 동작합니다.&#x20;

```javascript
// singleton.js (싱글톤 모듈)
class Singleton {
  constructor() {
    this.value = Math.random(); // 객체가 생성될 때 랜덤값 설정
  }
}

export default new Singleton(); // 싱글톤 인스턴스 내보내기
```

```javascript
// main.js
import singleton from './singleton.js';

console.log(singleton.getValue()); // 예: 0.2453
console.log(singleton.getValue()); // 같은 값 반환 (싱글톤 유지)

import singleton2 from "./singleton.js";
console.log(singleton === singleton2); // true (같은 인스턴스)
```

ES6 모듈 시스템에서는 `export default`로 한 번 생성된 객체가 캐싱되기 때문에 새로운 인스턴스를 만들지 않고 유지됩니다. 하지만 전역 상태를 공유하는 패턴이므로 사용 시 신중해야 하며, ES6 모듈 시스템이나 의존성 주입 기법과 함께 활용하면 더 안전하게 사용할 수 있습니다.&#x20;

### ES6 Module 장점 <a href="#es6-module-benefits" id="es6-module-benefits"></a>

* 자동으로 전역적으로 유지 → `import`할 때 같은 인스턴스를 반환
* 추가적인 싱글톤 구현 없이 간결한 코드 유지 가능
* `Node.js` 환경에서도 같은 모듈을 `import`하면 동일한 인스턴스를 사용

## Redux와 Zustand에서 적용하기

### Redux에서 싱글톤 패턴 적용

Redux의 store 자체가 싱글톤 패턴을 기반으로 동작합니다.

`createStore`나 `configureStore`를 호출하면 하나의 `store` 인스턴스만 유지하고, 모든 컴포넌트가 이 `store`를 공유한다.

```javascript
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});

export default store; // 싱글톤 인스턴스로 export
```

### Zustand에서 싱글톤 패턴 적용

Zustand도 기본적으로 하나의 `store` 인스턴스를 유지하며 싱글톤처럼 동작합니다.

```javascript
import create from "zustand";

const useStore = create((set) => ({
  count: 0,
  increase: () => set((state) => ({ count: state.count + 1 })),
}));

export default useStore; // 싱글톤 Store 인스턴스를 export
```

### Redux vs Zustand 싱글톤 활용 차이점

Redux와 Zustand **모두 store는 기본적으로 싱글톤 패턴**을 따릅니다. 하지만 적용방식에서 차이점이 있습니다.&#x20;

<table><thead><tr><th width="204"></th><th width="258">Redux</th><th>Zustand</th></tr></thead><tbody><tr><td>싱글톤 적용 방식</td><td>전역 Store를 생성하고 export </td><td>create 함수를 통해 Store를 정의하고 export</td></tr><tr><td>여러 개의 store 가능 여부</td><td>기본적으로 하나의 store만 사용</td><td>여러 개의 store 생성 가능</td></tr></tbody></table>



싱글톤 패턴을 더 깊이 이해하고, 실제 프로젝트에서 올바르게 활용할 수 있는 기준을 가지게 되었습니다. **싱글톤 패턴에 대한 여러분의 경험은 어떠한가요?** 어떤 상황에서 효과적이었고, 어떤 경우에는 문제가 되었나요? 깃헙 이슈 남겨주시면 더 좋은 인사이트를 얻을 수 있을 것 같아요! 감사합니다 :smile:

#### **참고 자료** <a href="#reference" id="reference"></a>

{% embed url="https://refactoring.guru/ko/design-patterns/singleton" %}
