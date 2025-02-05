---
description: 객체 생성의 책임을 하위클래스에 위임하는 패턴
---

# 팩토리 메서드 (Factory Method)

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://refactoring.guru/ko/design-patterns/factory-method">https://refactoring.guru/ko/design-patterns/factory-method</a></p></figcaption></figure>

팩토리 메소드 패턴(Factory Method Pattern)은 객체 **생성을 캡슐화**하여, 하위클래스에서 객체의 **생성 방식을 결정하도록 하는 디자인 패턴**입니다. 즉, 객체를 직접 생성하는 것이 아니라, **객체 생성을 담당하는 메소드를 통해 객체를 반환**하도록 합니다. 이 패턴을 활용하면 클라이언트 코드가 특정 클래스의 인스턴스를 직접 생성하지 않고, 추상화된 인터페이스를 통해 객체를 생성할 수 있습니다.



## 팩토리 패턴과 팩토리 메서드 패턴은 어떻게 다를까? <a href="#factory-factory-method" id="factory-factory-method"></a>

둘 다 객체 생성을 캡슐화하는 디자인 패턴이지만, 그 방식과 의도에서 차이가 있습니다.

### 팩토리 패턴 <a href="#factory" id="factory"></a>

객체 생성을 직접 수행하는 것이 아니라, 객체 생성을 담당하는 **별도의 클래스(팩토리 클래스)**&#xB97C; 두고, 이를 통해 객체를 생성하는 방식입니다. 즉, **객체 생성 로직을 클라이언트 코드에서 분리**하여 관리하는 것입니다. 주로 **단순한 객체 생성을 중앙 집중화할 때** 유용합니다.&#x20;

`User`라는 엔티티와 DTO를 만들어보겠습니다.&#x20;

<details>

<summary>❌ 팩토리 패턴이 없을 경우 (변경 범위가 넓음)</summary>

{% code lineNumbers="true" %}
```typescript
// 1. UserDto 클래스 (데이터 구조를 정의하는 DTO)
class UserDto {
  id: number;
  name: string;
  email: string;
  age?: number;

  constructor(id: number, name: string, email: string, age?: number) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.age = age ?? 0; // 기본값 설정
  }

  validate(): boolean {
    if (!this.name || !this.email) {
      throw new Error("이름과 이메일은 필수 입력 값입니다.");
    }
    return true;
  }
}

// 2. User 클래스 (엔티티)
class User {
  id: number;
  name: string;
  email: string;
  age?: number;
  
  constructor(id: number, name: string, email: string, age?: number) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.age = age ?? 0; // 기본값 설정
  }

  getProfile(): string {
    return `이름: ${this.name}, 이메일: ${this.email}, 나이: ${this.age ?? "미입력"}`;
  }
}

// 3. UserService (User 관련 비즈니스 로직 관리)
class UserService {
  signIn(dto: UserDto): User {
    console.log("UserService를 통해 User 로그인");
    return new User(dto.id, dto.name, dto.email, dto.age);
  }
  
  signUp(dto: UserDto): User {
    console.log("UserService를 통해 User 회원가입");
    return new User(dto.id, dto.name, dto.email, dto.age);
  }
}

```
{% endcode %}

만약 `UserDto`에 `gender` 프로퍼티가 추가된다면, `UserDto`, `User`, 그리고 `UserService`의 `signIn`, `signUp` 메서드를 포함하여 `UserDto`에 의존하는 모든 코드에서 수정이 필요하게 됩니다.

</details>

<details>

<summary>✅ 팩토리 패턴을 적용한 경우 (변경 범위 최소화)</summary>

{% code lineNumbers="true" %}
```typescript
// 1. UserDto 클래스 (데이터 구조를 정의하는 DTO)
class UserDto {
  id: number;
  name: string;
  email: string;
  age?: number;

  constructor(id: number, name: string, email: string, age?: number) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.age = age ?? 0; // 기본값 설정
  }

  validate(): boolean {
    if (!this.name || !this.email) {
      throw new Error("이름과 이메일은 필수 입력 값입니다.");
    }
    return true;
  }
}

// 2. User 클래스 (도메인 객체)
class User {
  id: number;
  name: string;
  email: string;
  age?: number;
  
  constructor(id: number, name: string, email: string, age?: number) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.age = age ?? 0; // 기본값 설정
  }

  getProfile(): string {
    return `이름: ${this.name}, 이메일: ${this.email}, 나이: ${this.age ?? "미입력"}`;
  }
}

// 3. UserFactory (팩토리 클래스 - User 객체 생성 책임) - 캡슐화
class UserFactory {
  static createUser(dto: UserDto): User {
    dto.validate(); // ✅ 데이터 검증 실행
    return new User(dto.id, dto.name, dto.email, dto.age);
  }
}

// 4. UserService (User 관련 비즈니스 로직 관리)
class UserService {
  signIn(dto: UserDto): User {
    console.log("✅ UserService: 로그인 요청 처리");
    return UserFactory.createUser(dto); // 🔥 팩토리 사용하여 객체 생성
  }
  
  signUp(dto: UserDto): User {
    console.log("✅ UserService: 회원가입 요청 처리");
    return UserFactory.createUser(dto); // 🔥 팩토리 사용하여 객체 생성
  }
}

```
{% endcode %}

만약 `UserDto`에 `gender`라는 프로퍼티가 추가된다면, 팩토리를 활용하여 `UserService` 코드 수정 없이도 객체 생성 로직을 변경할 수 있습니다.

</details>

<details>

<summary>🔄 생성 방식이 바뀐 후 (팩토리 패턴 적용)</summary>

{% code lineNumbers="true" %}
```typescript
// 1. UserDto 클래스 (데이터 구조를 정의하는 DTO)
class UserDto {
  id: number;
  name: string;
  email: string;
  age?: number;
  gender: string;

  constructor(id: number, name: string, email: string, age?: number, gender: string) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.age = age ?? 0; // 기본값 설정
    this.gender = gender;
  }

  validate(): boolean {
    if (!this.name || !this.email) {
      throw new Error("이름과 이메일은 필수 입력 값입니다.");
    }
    return true;
  }
}

// 2. User 클래스 (도메인 객체)
class User {
  id: number;
  name: string;
  email: string;
  age?: number;
  gender: string;
  
  constructor(id: number, name: string, email: string, age?: number, gender: string) {
    this.id = id;
    this.name = name;
    this.email = email;
    this.age = age ?? 0; // 기본값 설정
    this.gender = gender;
  }

  getProfile(): string {
    return `이름: ${this.name}, 이메일: ${this.email}, 나이: ${this.age ?? "미입력"}, 성별: ${this.gender}`;
  }
}

// 3. UserFactory (팩토리 클래스 - User 객체 생성 책임) - 캡슐화
class UserFactory {
  static createUser(dto: UserDto): User {
    dto.validate(); // ✅ 데이터 검증 실행
    return new User(dto.id, dto.name, dto.email, dto.age, dto.gender);
  }
}

// 4. UserService (User 관련 비즈니스 로직 관리)
class UserService {
  signIn(dto: UserDto): User {
    console.log("✅ UserService: 로그인 요청 처리");
    return UserFactory.createUser(dto); // 🔥 팩토리 사용하여 객체 생성
  }
  
  signUp(dto: UserDto): User {
    console.log("✅ UserService: 회원가입 요청 처리");
    return UserFactory.createUser(dto); // 🔥 팩토리 사용하여 객체 생성
  }
}

```
{% endcode %}

`User` 엔티티에 `gender` 속성을 추가해보면 UserService 코드엔 영향을 끼치지 않게 됩니다.

</details>

이제 `User` 엔티티 말고, 소셜 로그인 인터페이스를 통해 **다양한 제 3자 로그인**을 구현해보겠습니다. 하나의 객체 생성이 아니라 다양한 객체 생성이 필요하겠죠?

<details>

<summary>❌ 모든 로그인 객체를 관리하는 SocialLoginFactory 구현</summary>

{% code lineNumbers="true" fullWidth="false" %}
```typescript
// 1. SocialLogin 인터페이스 (추상화)
interface SocialLogin {
  login(): void;
}

// 2. 구체적인 로그인 클래스 (Kakao, Naver, Google, Apple)
class KakaoLogin implements SocialLogin {
  login(): void {
    console.log("카카오 로그인 실행");
  }
}

class NaverLogin implements SocialLogin {
  login(): void {
    console.log("네이버 로그인 실행");
  }
}

class GoogleLogin implements SocialLogin {
  login(): void {
    console.log("구글 로그인 실행");
  }
}

class AppleLogin implements SocialLogin {
  login(): void {
    console.log("애플 로그인 실행");
  }
}

// 3. 팩토리 클래스 (모든 로그인 객체를 관리) - OCP(개방폐쇄) 위반, SRP(단일책임) 위반
class SocialLoginFactory {
  static createLogin(type: string): SocialLogin {
    switch (type) {
      case "kakao":
        return new KakaoLogin();
      case "naver":
        return new NaverLogin();
      case "google":
        return new GoogleLogin();
      case "apple":
        return new AppleLogin();
      default:
        throw new Error("지원하지 않는 로그인 타입입니다.");
    }
  }
}

// 4. 사용 예시
const kakaoLogin = SocialLoginFactory.createLogin("kakao");
const googleLogin = SocialLoginFactory.createLogin("google");

kakaoLogin.login(); // "카카오 로그인 실행"
googleLogin.login(); // "구글 로그인 실행"
googleLogin.login(); // 구글 로그인 실행
 
```
{% endcode %}

현재 `SocialLoginFactory` 클래스는 타입에 따라 `KakaoLogin` 객체 생성을, 때론 다른 객체 생성을 합니다. 그래서 OOP의 단일책임원칙에 어긋나고 있습니다. 뿐만 아니라 깃허브 로그인이 추가된다면, 기존 코드에 case 문을 추가해서 생성해야하는 개방-폐쇄 원칙을 위배하고 있습니다.

</details>

### 팩토리 메서드 패턴 <a href="#factory-method" id="factory-method"></a>

팩토리 메서드는 객체 **생성을 위한 인터페이스(추상 메서드)**&#xB97C; 정의하고, **하위 클래스가 이를 구현**하여 객체를 생성하도록 하는 방식입니다. 즉, 객체 생성을 하위 클래스에서 결정하도록 유도하여, **개방-폐쇄 원칙(OCP)**&#xC744; 따르게 합니다. 이렇게 되면 **확장 가능성이 높아 새로운 객체 타입을 추가하기 쉽습**니다.&#x20;

소셜 로그인 인터페이스와 **소셜 로그인 팩토리 추상클래스**를 통해 **다양한 제 3자 로그인을 구현해보겠습니다.**

<details>

<summary>✅ 추상 클래스 SocialLoginFactory</summary>

{% code lineNumbers="true" %}
```typescript
// 1. SocialLogin 인터페이스 (추상화)
interface SocialLogin {
  login(): void;
}

// 2. 구체적인 로그인 클래스 (Kakao, Naver, Google, Apple)
class KakaoLogin implements SocialLogin {
  login(): void {
    console.log("카카오 로그인 실행");
  }
}

class NaverLogin implements SocialLogin {
  login(): void {
    console.log("네이버 로그인 실행");
  }
}

class GoogleLogin implements SocialLogin {
  login(): void {
    console.log("구글 로그인 실행");
  }
}

class AppleLogin implements SocialLogin {
  login(): void {
    console.log("애플 로그인 실행");
  }
}

// 3. 팩토리 메서드를 가진 추상 팩토리 클래스
abstract class SocialLoginFactory {
  abstract createLogin(): SocialLogin;
}

// 4. 개별 팩토리 클래스 (각 로그인 방식별로 별도 팩토리 구현)
class KakaoLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new KakaoLogin();
  }
}

class NaverLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new NaverLogin();
  }
}

class GoogleLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new GoogleLogin();
  }
}

class AppleLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new AppleLogin();
  }
}

// 5. 사용 예시
const kakaoFactory = new KakaoLoginFactory();
const googleFactory = new GoogleLoginFactory();

const kakaoLogin = kakaoFactory.createLogin(); // SocialLogin
const googleLogin = googleFactory.createLogin(); // SocialLogin

kakaoLogin.login(); // "카카오 로그인 실행"
googleLogin.login(); // "구글 로그인 실행"
```
{% endcode %}

만약 깃허브 로그인이 추가된다면, SocialLoginFactory를 확장하여 GithubLoginFactory 클래스만 추가해주면 됩니다.

</details>

<table><thead><tr><th width="136">구분</th><th width="306">팩토리 (Factory)</th><th>팩토리 메서드 (Factory Method)</th></tr></thead><tbody><tr><td>객체 생성 책임</td><td>하나의 팩토리 클래스가 생성 책임을 가짐</td><td>하위클래스에서 객체 생성 로직을 결정</td></tr><tr><td>유연성</td><td>새로운 객체 유형 추가 시 기존 팩토리 클래스 수정 필요</td><td>새로운 하위클래스를 추가하여 확장 가능 (OCP 준수)</td></tr><tr><td>설계 방식 </td><td>단순한 정적 메서드 또는 별도의 팩토리 클래스를 사용</td><td>상속을 활용해 객체 생성 방식을 변경</td></tr><tr><td>사용 예</td><td>단순한 객체 생성을 중앙 집중화할 때</td><td>다양한 객체 생성을 하위클래스에 위임할 때</td></tr></tbody></table>



## 주요 특징 <a href="#features" id="features"></a>

1. **객체 생성 로직을 캡슐화** → 객체 생성을 직접 하지 않고, **팩토리 메소드를 통해** 생성
2. **유연한 확장성** → 새로운 객체 타입이 추가되더라도 **기존 코드 수정 없이** 확장 가능
3. **결합도 낮추기** → 클라이언트 코드가 구체적인 클래스를 몰라도 객체를 생성할 수 있도록 유도



## 언제 활용할 수 있을까? <a href="#use-cases" id="use-cases"></a>

싱글톤 패턴에 비해 **팩토리 메서드 패턴**을 언제 활용할 수 있을지 이해하는 것이 어려웠습니다. 하지만 **비슷한 객체를 반복적으로(공장처럼) 생성해야 할 경우**에 팩토리 메서드 패턴 사용을 고려해본다고 생각하니 이해가 쉬워졌습니다. 또 개발자가 컴파일 단계에서 어떤 객체를 생성해야할 지 모르고, **런타임 단계에서 동적으로 객체를 생성해야 할 때**도 사용할 수 있습니다.

<details>

<summary>플랫폼(Windows, MacOS 등) 환경에 따라 다른 버튼을 생성해하는 경우</summary>

OS마다 UI 컴포넌트가 다르기 때문에 Button을 직접 생성하면 OS별 분기 처리가 필요합니다. 이 경우 팩토리 메소드 패턴을 활용하면 OS별 버튼을 쉽게 추가할 수 있습니다. 자바스크립트의 `class`를 활용하여 버튼 인터페이스를 만들어보겠습니다.&#x20;

```typescript
// 1. 버튼 인터페이스 정의
abstract class Button {
  abstract render(): void;
}

// 2. 각 OS에 맞는 버튼 클래스 구현
class WindowsButton extends Button {
  render(): void {
    console.log("Windows 스타일의 버튼 렌더링");
  }
}

class MacOSButton extends Button {
  render(): void {
    console.log("MacOS 스타일의 버튼 렌더링");
  }
}

// 3. 팩토리 메소드가 있는 렌더러 클래스
abstract class Renderer {
  abstract createButton(): Button;

  render(): void {
    const button = this.createButton();
    button.render();
  }
}

// 4. OS에 맞는 팩토리 구현
class WindowsRenderer extends Renderer {
  createButton(): Button {
    return new WindowsButton();
  }
}

class MacOSRenderer extends Renderer {
  createButton(): Button {
    return new MacOSButton();
  }
}

// 5. 클라이언트 코드
function application(OS: string): void {
  let renderer: Renderer;

  if (OS === "Windows") {
    renderer = new WindowsRenderer();
  } else if (OS === "MacOS") {
    renderer = new MacOSRenderer();
  } else {
    throw new Error("지원되지 않는 OS");
  }

  renderer.render();
}

// 사용 예시
application("Windows"); // Windows 스타일의 버튼 렌더링
application("MacOS");   // MacOS 스타일의 버튼 렌더링
```

OS에 따라 적절한 버튼을 생성하여 렌더링할 수 있게 되었습니다. 새로운 OS 타입이 추가될 경우, 기존 코드를 수정할 필요 없이 새로운 클래스만 추가하면 됩니다.&#x20;

</details>

<details>

<summary>다양한 데이터베이스(MySQL, PostgreSQL 등)를 지원해하는 경우</summary>

어떤 애플리케이션이 다양한 데이터베이스를 지원해야 할 때, 팩토리 메소드 패턴을 사용하면 코드의 변경 없이 쉽게 확장할 수 있습니다.&#x20;

```typescript
// 1. 데이터베이스 인터페이스 정의
abstract class Database {
  abstract connect(): void;
}

// 2. 특정 데이터베이스 연결 클래스 구현
class MySQLDatabase extends Database {
  connect(): void {
    console.log("MySQL 데이터베이스에 연결됨");
  }
}

class PostgreSQLDatabase extends Database {
  connect(): void {
    console.log("PostgreSQL 데이터베이스에 연결됨");
  }
}

// 3. 팩토리 메소드 패턴 적용
abstract class DatabaseFactory {
  abstract createDatabase(): Database;
}

class MySQLDatabaseFactory extends DatabaseFactory {
  createDatabase(): Database {
    return new MySQLDatabase();
  }
}

class PostgreSQLDatabaseFactory extends DatabaseFactory {
  createDatabase(): Database {
    return new PostgreSQLDatabase();
  }
}

// 4. 클라이언트 코드
function getDatabaseConnection(type: string): void {
  let factory: DatabaseFactory;

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

<details>

<summary>다양한 소셜로그인(Kakao, Naver 등)을 지원해야하는 경우</summary>

```typescript
// 1. 소셜 로그인 인터페이스 정의
abstract class SocialLogin {
  abstract authenticate(): void;
}

// 2. 각 소셜 로그인 클래스 구현
class KakaoLogin extends SocialLogin {
  authenticate(): void {
    console.log("카카오 로그인 성공");
  }
}

class NaverLogin extends SocialLogin {
  authenticate(): void {
    console.log("네이버 로그인 성공");
  }
}

class GoogleLogin extends SocialLogin {
  authenticate(): void {
    console.log("구글 로그인 성공");
  }
}

// 3. 팩토리 메소드 패턴 적용
abstract class SocialLoginFactory {
  abstract createLogin(): SocialLogin;
}

class KakaoLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new KakaoLogin();
  }
}

class NaverLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new NaverLogin();
  }
}

class GoogleLoginFactory extends SocialLoginFactory {
  createLogin(): SocialLogin {
    return new GoogleLogin();
  }
}

// 4. 클라이언트 코드
function socialLogin(type: string): void {
  let factory: SocialLoginFactory;

  if (type === "Kakao") {
    factory = new KakaoLoginFactory();
  } else if (type === "Naver") {
    factory = new NaverLoginFactory();
  } else if (type === "Google") {
    factory = new GoogleLoginFactory();
  } else {
    throw new Error("지원되지 않는 소셜 로그인 타입");
  }

  const login = factory.createLogin();
  login.authenticate();
}

// 사용 예시
socialLogin("Kakao"); // 카카오 로그인 성공
socialLogin("Naver"); // 네이버 로그인 성공
socialLogin("Google"); // 구글 로그인 성공
```

</details>



## 추상 팩토리 패턴과 팩토리 메서드 패턴은 어떻게 다를까? <a href="#abstract-factory" id="abstract-factory"></a>

팩토리 메소드 패턴은 종종 **추상 팩토리 패턴(Abstract Factory Pattern)**&#xACFC; 함께 사용됩니다.

<details>

<summary>🏭 OS별 버튼 생성 - 팩토리 메서드 패턴</summary>



```typescript
// 인터페이스 정의
abstract class Button {
  abstract render(): void;
}

// 구체적인 클래스
class WindowsButton extends Button {
  render(): void {
    console.log("Windows 스타일 버튼 렌더링");
  }
}

class MacOSButton extends Button {
  render(): void {
    console.log("MacOS 스타일 버튼 렌더링");
  }
}

// 팩토리 메서드가 있는 클래스 (Renderer)
abstract class ButtonFactory {
  abstract createButton(): Button;

  render(): void {
    const button = this.createButton();
    button.render();
  }
}

// 팩토리 클래스 (각 OS별 버튼 생성)
class WindowsButtonFactory extends ButtonFactory {
  createButton(): Button {
    return new WindowsButton();
  }
}

class MacOSButtonFactory extends ButtonFactory {
  createButton(): Button {
    return new MacOSButton();
  }
}

// 사용 예시
const factory: ButtonFactory = new WindowsButtonFactory();
factory.render(); // "Windows 스타일 버튼 렌더링"
```

</details>

<details>

<summary>🏢 OS별 UI 컴포넌트(Button &#x26; Checkbox) 생성</summary>

하나의 팩토리에서 서로 연관된 객체 여러 개를 생성할 수 있습니다. UI 요소 같은 제품군(Product Family) 관리 가능합니다. 새로운 UI 시스템이 추가될 경우, 새로운 UIFactory를 만들 수 있습니다. (확장성 우수)

```typescript
// 1. 공통 인터페이스 정의
abstract class Button {
  abstract render(): void;
}

abstract class Checkbox {
  abstract render(): void;
}

// 2. 구체적인 제품 클래스 (Windows UI)
class WindowsButton extends Button {
  render(): void {
    console.log("Windows 스타일 버튼 렌더링");
  }
}

class WindowsCheckbox extends Checkbox {
  render(): void {
    console.log("Windows 스타일 체크박스 렌더링");
  }
}

// 3. 구체적인 제품 클래스 (MacOS UI)
class MacOSButton extends Button {
  render(): void {
    console.log("MacOS 스타일 버튼 렌더링");
  }
}

class MacOSCheckbox extends Checkbox {
  render(): void {
    console.log("MacOS 스타일 체크박스 렌더링");
  }
}

// 4. 추상 팩토리 (서로 연관된 제품을 한 번에 생성)
abstract class UIFactory {
  abstract createButton(): Button;
  abstract createCheckbox(): Checkbox;
}

// 5. 구체적인 팩토리 클래스 (Windows용 UI)
class WindowsUIFactory extends UIFactory {
  createButton(): Button {
    return new WindowsButton();
  }

  createCheckbox(): Checkbox {
    return new WindowsCheckbox();
  }
}

// 6. 구체적인 팩토리 클래스 (MacOS용 UI)
class MacOSUIFactory extends UIFactory {
  createButton(): Button {
    return new MacOSButton();
  }

  createCheckbox(): Checkbox {
    return new MacOSCheckbox();
  }
}

// 7. 클라이언트 코드
function createUI(factory: UIFactory): void {
  const button = factory.createButton();
  const checkbox = factory.createCheckbox();

  button.render();
  checkbox.render();
}

// 사용 예시
const factory: UIFactory = new MacOSUIFactory();
createUI(factory);
// "MacOS 스타일 버튼 렌더링"
// "MacOS 스타일 체크박스 렌더링"
```

</details>



팩토리 메소드를 추상 클래스로 정의하여 **각 팩토리마다 독립적인 생성 방식 유지**할 수 있습니다. 클라이언트 코드가 특정 팩토리 인스턴스를 선택하여 사용할 수 있도록 유도합니다.&#x20;

<table><thead><tr><th width="130">구분</th><th>팩토리 메서드 (Factory Method)</th><th>추상 팩토리 (Abstract Factory)</th></tr></thead><tbody><tr><td>패턴 유형</td><td>생성 패턴</td><td>생성 패턴</td></tr><tr><td>목적</td><td>하나의 객체를 생성하는 팩토리 메서드를 제공</td><td>관련된 <strong>여러 객체군(Family)</strong>을 생성하는 팩토리를 제공</td></tr><tr><td>구성</td><td>단일 객체 생성을 위한 하위 클래스에서 팩토리 메서드를 구현</td><td>여러 객체를 생성하는 팩토리 클래스 자체를 추상화</td></tr><tr><td>확장성</td><td>새로운 객체 유형을 추가하려면 기존 팩토리 메서드를 수정해야 함</td><td>새로운 객체군을 추가할 때 기존 팩토리를 수정할 필요 없음</td></tr><tr><td>유지보수</td><td>상대적으로 단순한 구조</td><td>보다 복잡한 구조지만 유지보수성이 높음</td></tr><tr><td>사용 사례</td><td>특정 인터페이스를 가진 객체를 동적으로 생성해야 할 때</td><td>특정 제품군(예: UI 컴포넌트, 데이터베이스 등)을 <strong>그룹화</strong>하여 생성할 때</td></tr><tr><td>예제</td><td>ButtonFactory → Button (WindowsButton, MacOSButton)</td><td>UIFactory → Button, Checkbox (WindowsUIFactory, MacOSUIFactory)</td></tr></tbody></table>



## 팩토리 메서드 패턴의 장점 <a href="#benefits" id="benefits"></a>

1. **객체 생성 로직을 분리하여 코드 가독성 향상**
2. **클라이언트 코드의 결합도 낮추기 (Loose Coupling)**
3. **새로운 객체 타입을 쉽게 추가하여 확장성 증가**

## 팩토리 메서드 패턴의 단점

* 각 제품 구현체마다 팩토리 객체들을 모두 구현해주어야 하기 때문에, 구현체가 늘어날때 마다 팩토리 클래스가 증가하여 서브 클래스 수가 폭발합니다.
* 코드가 매우 복잡해질 수 있습니다.
* 단순한 객체 생성에는 오버헤드가 발생할 수 있습니다. (단순한 객체는 new 키워드 사용이 더 적합)



## 정리

<table><thead><tr><th width="102">구분</th><th>팩토리 (Factory)</th><th>팩토리 메소드 (Factory Method) </th><th>추상 팩토리 (Abstract Factory)</th></tr></thead><tbody><tr><td>핵심 개념</td><td>객체 생성을 위한 단순한 팩토리 함수 또는 클래스</td><td>객체 생성을 위한 하위클래스에서 구현해야 하는 메서드 제공 </td><td>서로 연관된 객체 그룹을 생성하는 팩토리 제공</td></tr><tr><td>구성 요소</td><td><ul><li>팩토리 클래스 또는 함수</li><li>제품 객체</li></ul></td><td><ul><li>팩토리 클래스를 부모로 두고, 하위 클래스에서 구현 </li><li>서브클래스에서 <code>create(</code>)를 구현</li></ul></td><td><ul><li>여러 개의 팩토리 메서드를 포함한 팩토리 클래스</li><li>여러 개의 제품이 생성됨</li></ul></td></tr><tr><td>사용 목적</td><td>단순한 객체 생성 방식 제공</td><td>객체 생성을 하위클래스에서 결정하도록 강제</td><td>객체 계열(Family)을 함께 생성</td></tr><tr><td>확장성</td><td>새로운 제품을 추가할 때 팩토리 로직 수정 필요</td><td>새로운 제품을 추가할 때 새로운 하위 클래스 추가</td><td>새로운 제품군을 추가할 때 새로운 팩토리 추가</td></tr><tr><td>예제</td><td><code>ButtonFactory.createButton("Windows")</code></td><td><code>WindowsButtonFactory.createButton()</code></td><td><code>WindowsUIFactory.createButton()</code>, <code>WindowsUIFactory.createCheckbox()</code></td></tr></tbody></table>



팩토리 메소드 패턴은 객체 생성 로직을 캡슐화하고, 유연한 확장을 가능하게 합니다. 다양한 프레임워크와 라이브러리에서 널리 사용되므로, 실제 프로젝트에서 적절히 활용하면 보다 유지보수성이 높은 코드를 작성할 수 있습니다. 여러분은 어떤 사례에서 팩토리 메소드 패턴을 활용해 보셨나요? 😊

#### **참고 자료**

{% embed url="https://refactoring.guru/ko/design-patterns/factory-method" %}
