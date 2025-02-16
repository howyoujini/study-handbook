---
description: 어떤 함수가 선언될 때의 자신의 렉시컬 스코프를 기억하고, 그 환경에 있는 변수에 접근할 수 있는 현상
---

# 클로저 (Closure)

사실 클로저라는 개념은 자바스크립트의 고유 개념이 아닙니다. **함수를 일급 객체로 취급**하는 프로그래밍 언어(JavaScript, Dart, Python, Ruby 등)에서 사용되는 중요한 특성입니다. 여기서 궁금해집니다.

## 왜 클로저는 함수를 일급 객체로 취급하는 언어에서 등장하는가?

### 일급 객체(First-Class Citizen)의 의미

한마디로 **함수도 값처럼** 다룰 수 있다면 일급 객체라고 표현할 수 있습니다.&#x20;

<details>

<summary>변수에 할당할 수 있다.</summary>

<pre class="language-typescript" data-title="sayHello.ts" data-line-numbers><code class="lang-typescript"><strong>function sayHello(): string {
</strong>  return "Hello";
}

let greeting = sayHello; // ✅ 함수가 변수에 할당됨
console.log(greeting()); // Hello (변수처럼 사용 가능)

</code></pre>

{% code title="sayHello.dart" lineNumbers="true" %}
```dart
String sayHello() {
  return "Hello";
}

void main() {
  final greeting = sayHello; // ✅ 함수가 변수에 할당됨
  print(greeting()); // Hello (변수처럼 사용 가능)
}

```
{% endcode %}

</details>

<details>

<summary>다른 함수의 <strong>인자</strong>로 전달할 수 있고 <strong>반환값</strong>으로 사용할 수 있다.</summary>

{% code title=".ts" lineNumbers="true" %}
```typescript
function sayHello(): string {
  return "Hello";
}

// ✅ executeFunction 함수: 함수를 인자로 받음
function executeFunction(func: () => string): string {
  return func();
}

console.log(executeFunction(sayHello)); // Hello

// ✅ getFunction 함수: 함수를 반환 - 고차 함수
function getFunction(): () => string {
  return function() {
    return "Returned Function";
  };
}

const newFunc = getFunction();
console.log(newFunc()); // Returned Function

```
{% endcode %}

{% code title=".dart" lineNumbers="true" %}
```dart
// ✅ 함수가 인자로 전달될 수 있음
String executeFunction(String Function() func) {
  return func();
}

// ✅ 함수가 반환값으로 사용될 수 있음
String Function() getFunction() {
  return () => "Returned Function";
}

String sayHello() {
  return "Hello";
}

void main() {
  print(executeFunction(sayHello)); // Hello

  final newFunc = getFunction(); 
  print(newFunc()); // Returned Function
}

```
{% endcode %}

</details>

<details>

<summary>동적으로 생성 및 실행할 수 있다.</summary>

실행 시점(runtime)에 함수를 생성하고, 즉시 실행할 수 있다는 것을 의미합니다. 이는 함수를 변수에 저장하거나, 즉석에서 함수를 만들고 실행하는 것이 가능하다는 뜻입니다.

#### 1. 익명 함수 즉시 실행 - 함수를 선언하자마자 즉시 실행하는 방식

{% code title=".ts" lineNumbers="true" %}
```typescript
// ✅ 즉시 실행 함수 (IIFE: Immediately Invoked Function Expression)
(function () {
  console.log("This is an immediately invoked function!");
})();

// ✅ 매개변수가 있는 즉시 실행 함수
((name: string) => {
  console.log(`Hello, ${name}!`);
})("Ella");

```
{% endcode %}

{% code title=".dart" lineNumbers="true" %}
```dart
void main() {
  // ✅ Dart에서 즉시 실행 함수 (IIFE 방식과 유사)
  (() {
    print("This is an immediately invoked function!");
  })();

  // ✅ 매개변수가 있는 즉시 실행 함수
  ((String name) {
    print("Hello, $name!");
  })("Ella");
}

```
{% endcode %}

#### 2. 동적으로 생성된 함수를 실행

{% code title=".ts" lineNumbers="true" %}
```typescript
// ✅ 동적으로 함수를 생성하고 변수에 저장
const dynamicFunc = new Function("name", "return `Hello, ${name}!`;");

console.log(dynamicFunc("Ella")); // Hello, Ella

```
{% endcode %}

{% code title=".dart" lineNumbers="true" %}
```dart
void main() {
  // ✅ 익명 함수를 변수에 저장하여 실행
  var dynamicFunc = (String name) => "Hello, $name!";
  
  print(dynamicFunc("Ella")); // Hello, Ella
}
```
{% endcode %}

#### 3. 고차 함수로 실행 시점에 함수 생성

{% code title=".ts" lineNumbers="true" %}
```typescript
// ✅ 실행 시점에 새로운 함수 생성
function createMultiplier(multiplier: number): (num: number) => number {
  return function (number: number): number { 
    return number * multiplier;
  };
}

const double = createMultiplier(2); // 함수 생성 O, 함수 실행 X
console.log(double(5)); // 10       // 함수 실행 O

const triple = createMultiplier(3);
console.log(triple(5)); // 15

```
{% endcode %}

{% code title=".dart" lineNumbers="true" %}
```dart
// ✅ 실행 시점에 함수를 생성하는 고차 함수
Function createMultiplier(int multiplier) {
  return (int number) => number * multiplier;
}

void main() {
  var double = createMultiplier(2);
  print(double(5)); // 10

  var triple = createMultiplier(3);
  print(triple(5)); // 15
}

```
{% endcode %}

</details>

클로저는 **함수가 선언될 당시의 외부 변수를 기억하고 유지하는 것**이 핵심입니다. **함수가 일급 객체가 아니면**, 함수를 다른 함수의 반환값으로 사용할 수도 없고, 다른 곳에서 실행할 수도 없기 때문에 **클로저가 성립할 수 없습**니다.



✅ 함수를 값처럼 다룰 수 있기 때문에 함수 내부에서 변수를 기억하는 클로저 개념이 등장한다.

✅ 클로저는 함수가 외부 환경을 기억하면서도 독립적인 실행 컨텍스트를 유지할 수 있도록 해준다.

✅ 함수가 일급 객체가 아니었다면, 클로저를 사용할 수 없었을 것이다.

✅ 자바스크립트는 함수형 프로그래밍 패러다임을 지원하기 때문에 클로저를 활용할 수 있다.



## 스코프와 렉시컬환경은 어떻게 다를까?

<table><thead><tr><th width="193">개념</th><th>설명</th></tr></thead><tbody><tr><td>스코프(Scope)</td><td>변수가 접근할 수 있는 범위를 의미 (e.g., 블록 스코프, 함수 스코프, 파일 스코프)</td></tr><tr><td>렉시컬 환경(Lexical Environment)</td><td><strong>실행 컨텍스트</strong>에 포함되며, 함수가 선언될 당시의 변수 참조 정보를 저장하는 객체</td></tr></tbody></table>

#### 스코프(Scope)

* 단순히 **접근 가능한 변수의 범위**를 의미하지만, 클로저는 단순한 접근이 아니라 실행이 끝난 후에도 변수를 기억하는 것이 핵심입니다.
* 전역 스코프 (Global Scope), 지역 스코프 (Local Scope)가 있습니다.
  * 지역 스코프(Local Scope)에는 **블록레벨 스코프(let, const)와** **함수레벨 스코프(var)**&#xAC00; 있습니다.&#x20;
* 자바스크립트에서는 **스코프 체인(Scope Chain)**&#xC744; 통해 상위 스코프의 변수에 접근할 수 있습니다.

#### 렉시컬 환경(Lexical Environment)

• 함수가 선언될 당시의 변수 저장소로, 실제로 클로저가 작동하는 원리입니다.&#x20;



* **렉시컬 스코프**

**변수가 선언된 위치(정적인 코드 구조)를 기준으로 스코프가 결정되는 방식**을 의미한다.

즉, 함수가 호출되는 위치가 아니라 **함수가 선언된 위치에서의 스코프가 유지**된다.

이를 정적 스코핑(Static Scoping)이라고도 한다.

자바스크립트는 **렉시컬 스코프를 따르는 언어**이다.



#### 참고 자료

{% embed url="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures" %}

{% embed url="https://witch.work/ko/posts/javascript-closure-deep-dive-application" %}
