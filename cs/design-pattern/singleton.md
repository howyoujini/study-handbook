---
description: 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴
---

# 싱글톤 패턴

싱글톤 패턴(Singleton Pattern)은 특정 클래스의 인스턴스를 오직 하나만 생성하도록 보장하고,

그 인스턴스를 어디서든 접근할 수 있도록 제공하는 디자인 패턴이다.\


이 패턴은 객체 생성을 제한하고, 동일한 객체를 여러 곳에서 공유해야 할 때 유용하다.

<img src="../../.gitbook/assets/file.excalidraw (7).svg" alt="" class="gitbook-drawing">

싱글톤 패턴의 주요 특징

1\. 단 하나의 인스턴스만 존재 → 새로운 인스턴스가 생성되지 않음.

2\. 글로벌 접근 가능 → 인스턴스가 어디서든 접근 가능함.

3\. 클래스 내부에서만 객체 생성 가능 → 외부에서 new 키워드를 사용할 수 없음.



싱글톤 패턴이 필요한 이유

싱글톤 패턴은 한 번만 생성되어야 하는 객체가 필요할 때 유용하다.



✅ 활용 사례

• 로그 관리(Logger) → 하나의 로깅 인스턴스를 유지하며 로그를 기록

• 데이터베이스 연결 관리(DB Connection) → 같은 DB 연결을 여러 곳에서 공유

• 앱 설정 관리(Config Manager) → 전역적으로 사용되는 설정값을 하나의 인스턴스로 유지

• 전역 상태 관리(State Manager) → Redux, Zustand 같은 상태 관리 라이브러리에서 사용



싱글톤 패턴 JavaScript 코드 예제\


✅ 1. 기본적인 싱글톤 패턴 구현

```javascript
class Singleton {
  static instance; // 단 하나의 인스턴스를 저장할 변수

  constructor() {
    if (Singleton.instance) {
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

🔹 설명

• Singleton 클래스는 instance라는 정적 변수를 가짐.

• new를 사용해 새로운 객체를 생성하면,

• 기존에 instance가 존재하면 새로운 객체를 만들지 않고 기존 객체를 반환.

• instance가 없다면 새로운 객체를 생성.

• 따라서 여러 번 new Singleton()을 호출해도 항상 같은 객체를 참조하게 됨.



2. getInstance()를 이용한 싱글톤 패턴

생성자를 private으로 두고, getInstance() 메서드로 접근하는 방식이 더 일반적이다.

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

// 사용 예제
const obj1 = Singleton.getInstance();
const obj2 = Singleton.getInstance();

console.log(obj1 === obj2); // true (항상 같은 인스턴스 반환)
```

🔹 설명

• getInstance()를 통해서만 인스턴스를 생성할 수 있도록 제한.

• new Singleton()을 직접 호출하면 에러 발생.

• Singleton.getInstance()를 호출하면 항상 동일한 인스턴스를 반환.



3. 싱글톤 패턴을 활용한 전역 설정 객체

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

// 사용 예제
const config1 = new Config();
const config2 = new Config();

console.log(config1.getConfig()); // { theme: "dark", language: "en" }
config1.updateConfig({ theme: "light" });

console.log(config2.getConfig()); // { theme: "light", language: "en" } (같은 객체를 공유)
console.log(config1 === config2); // true (같은 인스턴스)
```

🔹 설명

• configData를 전역적으로 관리하여 설정을 공유할 수 있음.

• updateConfig()로 설정을 변경하면, 다른 인스턴스에서도 변경 사항이 반영됨.

• config1과 config2는 같은 인스턴스를 공유.



🛠 싱글톤 패턴의 단점

1\. 글로벌 상태를 공유하기 때문에, 예기치 않은 부작용이 발생할 수 있음.

• 예를 들어, 멀티스레드 환경에서는 동기화 이슈가 발생할 가능성이 있음.

2\. 모듈 간의 의존성이 증가하여 테스트하기 어려움.

• 싱글톤 객체를 변경하면 여러 곳에서 영향을 받을 수 있음.

3\. 유연성이 부족함.

• 특정 클래스가 싱글톤 패턴으로 설계되면, 나중에 다중 인스턴스를 만들고 싶어도 어렵다.



🎯 싱글톤 패턴을 더 안전하게 사용하려면?

1\. 의존성 주입(Dependency Injection, DI)과 함께 사용하기

• 직접 싱글톤을 호출하는 대신, 필요한 곳에 의존성 주입(DI) 을 통해 관리하는 것이 좋음.

2\. JavaScript ES6 Module 활용

• ES6 모듈 시스템을 이용하면 자동으로 싱글톤처럼 동작한다.

• 예제:

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



• ES6 모듈 시스템에서는 export default로 한 번 생성된 객체가 캐싱되기 때문에 새로운 인스턴스를 만들지 않고 유지된다.



🚀 마무리\


싱글톤 패턴은 “하나의 인스턴스를 전역적으로 공유”하는 디자인 패턴으로, 주로 로깅, 설정 관리, DB 연결 등에 사용된다.

하지만 전역 상태를 공유하는 패턴이므로 사용 시 신중해야 하며, ES6 모듈 시스템이나 의존성 주입 기법과 함께 활용하면 더 안전하게 사용할 수 있다.



## 싱글톤 패턴의 문제점과 해결 방법

싱글톤 패턴은 전역적으로 하나의 객체를 공유하는 패턴이기 때문에, 잘못 사용하면 예상치 못한 문제를 일으킬 수 있다.

다음은 싱글톤 패턴의 주요 문제점과 해결 방법을 정리한 내용이다.



❌ 1. 글로벌 상태 공유로 인해 예기치 않은 부작용 발생

\


🚨 문제:

• 싱글톤 객체는 전역적으로 공유되기 때문에, 프로그램의 여러 부분에서 동일한 데이터를 변경할 수 있다.

• 특정 코드에서 싱글톤의 상태를 변경하면 모든 코드에 영향을 미칠 수 있음.

• Side Effect(부작용) 발생 가능성이 높아지고, 디버깅이 어려워짐.

\


🔍 예제:

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

\


✅ 해결 방법:

• 전역 상태를 직접 변경하지 않고, Immutable(불변) 상태 관리 기법을 적용.

• Redux/Zustand 같은 상태 관리 라이브러리를 활용.

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

❌ 2. 모듈 간 강한 의존성(Coupling) 증가

\


🚨 문제:

• 싱글톤 인스턴스가 여러 곳에서 직접 참조되면, 모듈 간 강한 의존성(결합도, Coupling)이 생김.

• 코드의 유연성이 떨어지고, 유지보수가 어려워짐.

• 특히, 유닛 테스트(Unit Test)가 어려워질 수 있음.

\


🔍 예제:

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

➡ 문제: Service 클래스가 Logger 싱글톤에 강하게 결합되어 있음.

\


✅ 해결 방법:

• \*\*의존성 주입(Dependency Injection, DI)\*\*을 사용하여 결합도를 줄임.

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

❌ 3. 테스트(Testability) 어려움

\


🚨 문제:

• 싱글톤 객체는 전역적으로 공유되기 때문에, 테스트 환경에서 격리(Isolation)가 어렵다.

• 특정 테스트가 싱글톤의 상태를 변경하면, 다른 테스트에 영향을 줄 수 있음.

\


🔍 예제:

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

\


✅ 해결 방법:

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

❌ 4. 멀티스레드 환경에서 동기화 문제 발생

\


🚨 문제:

• Java, C++ 같은 멀티스레드 환경에서 싱글톤 객체를 동시 접근하면,

여러 스레드가 동시에 인스턴스를 생성할 가능성이 있음.

• \*\*경쟁 조건(Race Condition)\*\*이 발생하면, 싱글톤이 깨질 위험이 있음.

\


✅ 해결 방법 (Java 예제):

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

➡ 해결: synchronized 키워드를 사용하여 멀티스레드 환경에서도 안전하게 동작하도록 함.

\


📌 JavaScript의 경우 싱글 스레드 환경이므로 동기화 문제는 발생하지 않음.



✅ 싱글톤 패턴 사용 시 주의할 점

| 문제점                  | 해결 방법                              |
| -------------------- | ---------------------------------- |
| 글로벌 상태 공유로 인한 부작용    | 불변 객체(Object.freeze()) 사용          |
| 모듈 간 강한 의존성 증가       | 의존성 주입(DI) 사용                      |
| 테스트하기 어려움            | Mock 객체 또는 Reset 메서드 활용            |
| 멀티스레드 환경에서 동기화 문제 발생 | synchronized(Java), volatile 변수 사용 |



🎯 마무리

\


싱글톤 패턴은 전역적으로 하나의 인스턴스를 공유할 때 유용하지만,

잘못 사용하면 테스트 어려움, 의존성 증가, 동기화 문제 등의 단점이 발생할 수 있다.

따라서 의존성 주입(DI), 불변 객체, Mock 객체 활용 등의 해결책을 적용하는 것이 중요하다.
