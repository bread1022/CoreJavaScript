## 3. this

JS에서 this 는 기본적으로 실행컨텍스트가 생성될 때( = **함수** **호출**될 때 ) 결정된다

1. 전역공간에서 this → window(브라우저) / global (Node.js)
2. 메서드 호출에서 this → 메서드 바로 앞 객체 (호출 주체)
3. 일반 함수 호출에서 this → 전역객체
4. 콜백 함수에서 this → 제어권을 받은 함수가 정의한 바에 따름 (정의하지 않으면 전역객체)
5. 생성자 함수에서 this → 생성될 인스턴스
6. 명시적 this 바인딩 (call, apply, bind를 사용해 간접 호출하는 방법)

<br>
<br>

### 전역공간에서 this

전역변수를 선언하면 JS는 실행컨텍스트의 LE(객체)의 프로퍼티의 key로 할당하고, 변수를 호출하면 LE에서 일치하는 프로퍼티를 조회하여 그 값을 반환한다

따라서 전역변수 선언이 곧 **전역객체의 프로퍼티**로 할당하는 것이다

```js
var a = 1;
console.log(a, window.a, this.a); // 1 1 1

window.b = 2;
console.log(b, window.b, this.b); // 2 2 2

window.a = 3;
b = 4;
console.log(a, window.a, this.a);  // 3 3 3
console.log(b, window.b, this.b); // 4 4 4
```

- 하지만 **delete 연산자**를 사용하여 전역객체의 프로퍼티를 삭제하고자 할 때
전역객체의 프로퍼티로 할당한 경우에만 삭제가 되고
전역변수로 선언한 경우에는 삭제가 되지않는다

- 전역변수로 선언하면 JS가 전역객체의 프로퍼티로 할당하면서
configurable속성(변경삭제 가능성)을 false 로 정의하기 때문에 차이가 발생한다

  ```js
  var a = 1;
  delete window.a; // false
  console.log(a, window.a, this.a); // 1 1 1 => 삭제안됨

  window.c = 3;
  delete window.c; // true
  console.log(c, window.c, this.c); // Uncaught ReferenceError: c is not defined

  window.d = 4;
  delete d;  // true
  console.log(d, window.d, this.d); // Uncaught ReferenceError: d is not defined
  ```

<br>

  ***함수** : 그 자체로 독립적인 기능을 수행

  ***메서드** : 자신을 호출한 **대상 객체**에 관한 동작을 수행한다 (*객체의 메서드로 호출*할 경우에만 해당)

### 메서드 호출에서 this

this 에는 호출한 주체에 대한 정보가 담긴다

### 일반 함수 호출에서 this

#### 내부함수에서 (전역객체를 가리키는)this를 우회하는 방법

### 콜백 함수에서 this

### 생성자 함수에서 this

### 명시적 this 바인딩