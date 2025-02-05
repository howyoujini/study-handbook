# ES6 화살표 함수



```javascript
() => expression

param => expression

(param) => expression

(param1, paramN) => expression

() => {
  statements
}

param => {
  statements
}

(param1, paramN) => {
  statements
}
```

*   1번 예제

    ```javascript
    const a = () => 'hi';
    const b = x => x * 2;
    const c = (x, y) => x + y;

    const d = x, y => x + y;
    ```
*   2번 예제

    ```javascript
    const a = x => x * x;

    const b = (x, y) => {
      const result = x + y;
      return result;
    };

    const c = x => if (x > 0) x;
    ```
*   3번 예제

    ```javascript
    const a = () => ({ key: 'value' });
    const b = () => { key: 'value' };
    ```
*   4번 예제

    ```javascript
    (() => console.log('vaco'))();

    const a = () => console.log('바코')();
    ```
*   5번 예제

    ```javascript
    const obj = {
      name: 'vaco',
      arrowFunc: () => console.log(this.name),
      regularFunc: function () {
        console.log(this.name);
      }
    };

    obj.arrowFunc();
    obj.regularFunc();
    ```
*   6-1번 예제

    ```javascript
    class Prefixer {
      constructor(prefix) {
        this.prefix = prefix;
      }
      add(arr) {
        return arr.map(item => this.prefix + item);
      } 
      plus(arr) {
    	  return arr.map(function(item) {
    	    return this.prefix + item;
    	  });
    	} 
    }

    const prefixer = new Prefixer('-webkit-');
    prefixer.add(['transition', 'user']);
    prefixer.plus(['transition', 'user']);
    ```
*   6-2번 예제

    ```javascript
    const arrowFunc = () => console.log(this);
    const regularFunc = function () {
      console.log(this);
    };

    arrowFunc.call({ value: 'vaco' });
    regularFunc.call({ value: '바코' });
    ```
*   7번 예제

    ```javascript
    const obj = {
      value: 'vaco',
      arrowMethod: () => console.log(this.value),
      regularMethod() {
        console.log(this.value);
      }
    };

    obj.arrowMethod();
    obj.regularMethod();
    ```
*   8번 예제

    ```javascript
    const ArrowFunc = () => {};
    const a = new ArrowFunc();
    ```
*   9번 예제

    ```javascript
    const a = [1, 2, 3];
    const b = a.map(n => n * n);

    const c = a.map(function () {
      return this.value;
    });
    ```



#### 참고 자료

{% embed url="https://compat-table.github.io/compat-table/es6" %}

{% embed url="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Arrow_functions" %}
