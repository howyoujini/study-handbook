---
description: 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴
---

# 싱글톤 패턴

<img src="../../.gitbook/assets/file.excalidraw (7).svg" alt="" class="gitbook-drawing">

싱글톤 패턴(Singleton Pattern)은 특정 클래스의 인스턴스를 **오직 하나만 생성하도록 보장**하고, 그 인스턴스를 어디서든 접근할 수 있도록 제공하는 디자인 패턴입니다.

이 패턴은 객체 생성을 제한하고, 동일한 객체를 여러 곳에서 공유해야 할 때, 즉 객체 생성이 비싼 로직일 때, 싱글톤 패턴을 주로 활용합니다.

***

## 주요 특징

1\. 단 하나의 인스턴스만 존재 → 새로운 인스턴스가 **생성되지 않도록** 하기

2\. 글로벌 접근 가능 → 인스턴스를 **어디서든 접근 가능**하도록 하기

3\. 클래스 내부에서만 객체 생성 가능 → **외부에서 new 키워드를 사용하더라도 단 하나의 동일 인스턴스 유지하기**

## 활용 사례

<details>

<summary><strong>로그 관리 (Logger)</strong></summary>

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

<summary><strong>데이터베이스 연결 관리 (DB Connection)</strong></summary>

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

<summary><strong>앱 설정 관리 (Config Manager)</strong></summary>

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

## 싱글톤 패턴 구현 예제

싱글톤 패턴으로 코드를 구현할 수 있는 방법은 여러가지가 있습니다.&#x20;

### 1. static instance

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

### 2. getInstance()

생성자를 `private`으로 두고, `getInstance()` 메서드로 접근하는 방식이 더 일반적입니다. 왜냐하면 `static instance` 변수를 사용해 싱글톤 패턴을 구현하면, 클라이언트 코드가 직접 `instance` 변수에 접근할 위험이 있기 때문입니다.&#x20;

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

`getInstance()`를 통해서만 인스턴스를 생성할 수 있도록 제한합니다. 즉, `new Singleton()`을 직접 호출하면 에러가 발생되도록 하여 `new` 키워드를 사용하는 것을 방지합니다. `Singleton.getInstance()`를 호출하면 항상 동일한 인스턴스를 반환합니다.

### 3. 전역 설정 객체

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

`configData`를 전역적으로 관리하여 설정을 공유할 수 있습니다. `updateConfig()`로 설정을 변경하면, 다른 인스턴스에서도 변경 사항이 반영됩니다. 즉, `config1`과 `config2`는 같은 인스턴스를 공유하게 됩니다.&#x20;

***

## 단점

1. 멀티스레드 환경에서는 동기화 이슈가 발생할 가능성
2. 싱글톤 객체를 변경하면 여러 곳에서 영향을 받기 때문에 모듈 간의 의존성이 증가하여 테스트하기 어려움
3. 특정 클래스가 싱글톤 패턴으로 설계되면, 나중에 다중 인스턴스를 만들고 싶어도 어려움 (유연성 X)

## 싱글톤 패턴을 더 안전하게 사용하려면?

### 1. 의존성 주입(Dependency Injection, DI)과 함께 사용하기

> 직접 싱글톤을 호출하는 대신, 필요한 곳에 의존성 주입(DI) 을 통해 관리하는 것이 좋습니다.&#x20;

### 2. JavaScript ES6 Module 활용

> ES6 모듈 시스템을 이용하면 자동으로 싱글톤처럼 동작합니다.&#x20;

```javascript
// singleton.js
class Singleton {
  constructor() {
    this.value = Math.random();
  }
}

export default new Singleton(); // 싱글톤 인스턴스 내보내기
```

```
// main.js
import singleton from './singleton.js';

console.log(singleton.value); // 같은 인스턴스를 공유
```

ES6 모듈 시스템에서는 export default로 한 번 생성된 객체가 캐싱되기 때문에 새로운 인스턴스를 만들지 않고 유지됩니다. 하지만 전역 상태를 공유하는 패턴이므로 사용 시 신중해야 하며, ES6 모듈 시스템이나 의존성 주입 기법과 함께 활용하면 더 안전하게 사용할 수 있습니다.&#x20;

## 싱글톤 패턴의 문제점과 해결 방법

싱글톤 패턴은 전역적으로 하나의 객체를 공유하는 패턴이기 때문에, 잘못 사용하면 예상치 못한 문제를 일으킬 수 있다.



❌ 1. 글로벌 상태 공유로 인해 예기치 않은 부작용 발생

🚨 싱글톤 객체는 전역적으로 공유되기 때문에, 프로그램의 여러 부분에서 동일한 데이터를 변경할 수 있다.

특정 코드에서 싱글톤의 상태를 변경하면 모든 코드에 영향을 미칠 수 있음.

Side Effect(부작용) 발생 가능성이 높아지고, 디버깅이 어려워짐.



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

➡ 문제: instance1에서 테마를 변경했더니 instance2에도 영향을 줌.



전역 상태를 직접 변경하지 않고, Immutable(불변) 상태 관리 기법을 적용.

Redux/Zustand 같은 상태 관리 라이브러리를 활용.

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

➡ 해결: Object.freeze()를 사용하여 불변 객체로 만들어 수정 불가능하게 함.

### 2. 모듈 간 강한 의존성(Coupling) 증가

🚨 싱글톤 인스턴스가 여러 곳에서 직접 참조되면, 모듈 간 강한 의존성(결합도, Coupling)이 생깁니다.

코드의 유연성이 떨어지고, 유지보수가 어려워집니다. 특히, 유닛 테스트(Unit Test)가 어려워질 수 있습니다.&#x20;

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

➡ Service 클래스가 Logger 싱글톤에 강하게 결합



• **의존성 주입(Dependency Injection, DI)**&#xC744; 사용하여 결합도를 줄임.

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

➡ 해결: Logger를 직접 참조하지 않고, 외부에서 주입받도록 수정하여 유연성을 증가시킴.



### 3. 테스트(Testability) 어려움

🚨 싱글톤 객체는 전역적으로 공유되기 때문에, 테스트 환경에서 격리(Isolation)가 어렵습니다.&#x20;

특정 테스트가 싱글톤의 상태를 변경하면, 다른 테스트에 영향을 줄 수 있음.

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

➡ 문제: db1에서 데이터를 추가했더니 db2에서도 같은 데이터를 공유함.



• 테스트 환경에서는 싱글톤을 Mock 객체로 대체.

• 필요할 경우, 리셋 가능한 싱글톤 객체를 만들기.

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

➡ 해결: reset() 메서드를 추가하여 테스트 간 데이터 공유 문제를 해결.



### 4. 멀티스레드 환경에서 동기화 문제 발생

🚨 Java, C++ 같은 **멀티스레드 환경에서** 싱글톤 객체를 동시 접근하면, 여러 스레드가 동시에 인스턴스를 생성할 가능성이 있습니다. **경쟁 조건(Race Condition)**&#xC774; 발생하면, 싱글톤이 깨질 위험이 있습니다.&#x20;

✅ Java 예제

```java
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

➡ **synchronized** 키워드를 사용하여 멀티스레드 환경에서도 안전하게 동작하도록 합니다.&#x20;

📌 JavaScript의 경우 싱글 스레드 환경이므로 동기화 문제는 발생하지 않습니다.&#x20;

✅ 싱글톤 패턴 사용 시 주의할 점

| 문제점                  | 해결 방법                              |
| -------------------- | ---------------------------------- |
| 글로벌 상태 공유로 인한 부작용    | 불변 객체(Object.freeze()) 사용          |
| 모듈 간 강한 의존성 증가       | 의존성 주입(DI) 사용                      |
| 테스트하기 어려움            | Mock 객체 또는 Reset 메서드 활용            |
| 멀티스레드 환경에서 동기화 문제 발생 | synchronized(Java), volatile 변수 사용 |



🎯 마무리

싱글톤 패턴은 전역적으로 하나의 인스턴스를 공유할 때 유용하지만,

잘못 사용하면 테스트 어려움, 의존성 증가, 동기화 문제 등의 단점이 발생할 수 있다.

따라서 의존성 주입(DI), 불변 객체, Mock 객체 활용 등의 해결책을 적용하는 것이 중요하다.



## 🔹 성능 최적화 측면에서 싱글톤 패턴의 장점

### 1. 객체 생성을 한 번만 수행하여 불필요한 메모리 낭비 방지

• 매번 new를 호출하여 새로운 객체를 생성하는 대신, 하나의 인스턴스를 재사용하여 메모리 사용을 줄임.

• 특히, 데이터베이스 연결, 네트워크 요청, 설정 관리 등의 무거운 객체를 효율적으로 관리 가능



### 2. 객체 생성 비용 감소

• 객체 생성 비용(Initialization Cost) 을 줄여 성능을 향상시킴.

• 예를 들어, DB Connection Pooling과 같은 기능에서는 매번 새로운 DB 연결을 생성하는 대신, 싱글톤을 사용하여 하나의 연결을 유지함.

### 3. 캐싱을 활용한 성능 개선

API 요청 결과나 설정 값을 캐싱하여 불필요한 연산을 줄이고 속도를 향상시킬 수 있음.



🔻 성능 저하를 초래할 수 있는 경우

1\. 멀티스레드 환경에서 동기화 이슈

• Java, C++ 같은 멀티스레드 환경에서는 동기화 문제로 인해 락(Lock) 사용이 필요할 수 있음.

• JavaScript는 싱글 스레드 환경이지만, Web Workers와 같이 멀티스레드 환경을 고려해야 할 수도 있음.

2\. 의존성이 증가하여 유지보수 어려움

• 전역적으로 접근할 수 있기 때문에 의존성이 강해지면서 결합도(Coupling)가 증가할 수 있음.

• 잘못 사용하면 단일 책임 원칙(SRP, Single Responsibility Principle) 위배 가능.



🚀 싱글톤으로 성능을 최적화하는 방법

✅ Lazy Initialization을 적용하여 필요할 때만 객체 생성하기

✅ 캐싱 전략과 함께 사용하여 중복 데이터 처리를 줄이기

✅ 의존성 주입(DI)과 조합하여 결합도를 낮추기



## 싱글톤 패턴을 Redux, Zustand 같은 상태 관리 라이브러리에서 어떻게 활용할 수 있을까?

### Redux에서 싱글톤 패턴 적용

Redux의 store 자체가 싱글톤 패턴을 기반으로 동작한다.

createStore나 configureStore를 호출하면 하나의 store 인스턴스만 유지하고, 모든 컴포넌트가 이 store를 공유한다.

```javascript
import { configureStore } from "@reduxjs/toolkit";

const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});

export default store; // 싱글톤 인스턴스로 export
```

➡ Redux Store는 하나의 인스턴스를 전역적으로 관리하는 싱글톤 패턴의 예시

### Zustand에서 싱글톤 패턴 적용

Zustand도 기본적으로 하나의 store 인스턴스를 유지하며 싱글톤처럼 동작한다.

```javascript
import create from "zustand";

const useStore = create((set) => ({
  count: 0,
  increase: () => set((state) => ({ count: state.count + 1 })),
}));

export default useStore; // 싱글톤 Store 인스턴스를 export
```

📌 Redux vs Zustand 싱글톤 활용 차이점

<table><thead><tr><th width="212"></th><th width="243">Redux</th><th>Zustand</th></tr></thead><tbody><tr><td>싱글톤 적용 방식</td><td>전역 Store를 생성하고 export </td><td>create 함수를 통해 Store를 정의하고 export</td></tr><tr><td>여러 개의 store 가능 여부</td><td>기본적으로 하나의 store만 사용</td><td>여러 개의 store 생성 가능</td></tr></tbody></table>

➡ Redux는 전역적으로 하나의 store를 공유하며, Zustand는 필요하면 여러 store를 만들 수 있음.

➡ 하지만 Redux와 Zustand 모두 store는 기본적으로 싱글톤 패턴을 따름.



## JavaScript ES6 모듈 시스템에서 싱글톤 패턴을 자연스럽게 적용하는 방법은 무엇일까?

🔹 ES6 모듈을 이용한 싱글톤 패턴

JavaScript에서는 ES6 모듈 시스템이 자체적으로 싱글톤처럼 동작한다.

즉, 모듈을 import하면 한 번만 로드되며, 이후에는 같은 인스턴스를 반환한다.

```javascript
// singleton.js (싱글톤 모듈)
class Singleton {
  constructor() {
    this.value = Math.random(); // 객체가 생성될 때 랜덤값 설정
  }

  getValue() {
    return this.value;
  }
}

export default new Singleton(); // 하나의 인스턴스만 export
```

```javascript
// main.js
import singleton from "./singleton.js";

console.log(singleton.getValue()); // 예: 0.2453
console.log(singleton.getValue()); // 같은 값 반환 (싱글톤 유지)

import singleton2 from "./singleton.js";
console.log(singleton === singleton2); // true (같은 인스턴스)
```

➡ ES6 모듈 시스템은 기본적으로 싱글톤처럼 동작하므로, 별도의 구현 없이 싱글톤을 자연스럽게 사용할 수 있음.

## 🔹 ES6 모듈 시스템에서 싱글톤의 장점

✅ 자동으로 전역적으로 유지 → import할 때 같은 인스턴스를 반환

✅ 추가적인 싱글톤 구현 없이 간결한 코드 유지 가능

✅ Node.js 환경에서도 같은 모듈을 import하면 동일한 인스턴스를 사용
