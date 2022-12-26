## 3. this

JS에서 this 는 기본적으로 실행컨텍스트가 생성될 때( = **함수** **호출**될 때 ) 결정된다

1. 전역공간에서 this → window(브라우저) / global (Node.js)
2. 메서드 호출에서 this → 메서드 바로 앞 객체 (호출 주체)
3. 일반 함수 호출에서 this → 전역객체
4. 콜백 함수에서 this → 제어권을 받은 함수가 정의한 바에 따름 (정의하지 않으면 전역객체)
5. 생성자 함수에서 this → 생성될 인스턴스
6. 명시적 this 바인딩 (call, apply, bind를 사용해 간접 호출하는 방법)

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

- **delete 연산자**를 사용하여 전역객체의 프로퍼티를 삭제하고자 할 때
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
<br>

  ***함수** : 그 자체로 독립적인 기능을 수행

  ***메서드** : 자신을 호출한 **대상 객체**에 관한 동작을 수행한다 (*객체의 메서드로 호출*할 경우에만 해당)


### 메서드 호출에서 this

this 에는 호출한 주체에 대한 정보가 담긴다

<img src="https://user-images.githubusercontent.com/107349637/209519318-78b39568-e134-4dbf-9989-605fdcf40a2e.jpg" width="300"/>

    ⇒ 함수를 호출할 때 함수 이름 앞에 점 또는 대괄호 표기가 있고 객체가 명시돼 있으면 메서드로 호출한 것

<br>

### 일반 함수 호출에서 this

일반 함수로 호출할 경우 호출 주체를 명시하지 않고
개발자가 직접 실행한 것이므로 호출 주체의 정보를 알 수 없다

this 가 지정되어 있지 않기 때문에 일반 함수 호출에서 this 는 **전역객체**를 가리킨다

<img src="https://user-images.githubusercontent.com/107349637/209519525-f43de1d0-0ae0-4f59-9604-5af55e47e558.jpg" width="350"/>

    ⇒ 호출할 때 함수명 앞에 점(.) 이 있으면 메서드로 호출한 것이므로
      점 앞의 객체가 this에 바인딩 되고, 없으면 전역객체를 가리키는 것이다


#### 내부함수에서 (전역객체를 가리키는)this를 우회하는 방법

#### (1) 상위 스코프에 변수를 생성하여 this를 저장하고, 내부 함수에서 활용

<img src="https://user-images.githubusercontent.com/107349637/209519996-087faabf-5789-473a-977c-ef38e82b0fca.jpg" width="350"/>

    ⇒ 상위 스코프에서 var self = this; 를 저장하고,내부함수에서 변수 self를 호출하면 상위 스코프의 this 객체가 출력된다


#### (2) 화살표함수 사용

화살표 함수는 실행컨텍스트 생성 때 **this바인딩 과정이 없다**는 성질을 이용하여

내부 함수로 화살표 함수를 사용하면 this는 상위 스코프의 this를 그대로 활용할 수 있다

<img src="https://user-images.githubusercontent.com/107349637/209520277-6630b567-a665-461f-bda6-7e3a50b1c6cb.jpg" width="350"/>


#### (3) call, apply, bind 메서드 활용하여 명시적으로 this 지정

<br>

### 콜백 함수에서 this

콜백함수도 일반 함수이므로 this가 전역객체를 참조하지만,

제어권을 받은 함수에서 콜백함수에 별도로 this 대상을 지정한 경우 그 대상을 참조한다

- **addEventListener 메서드**
    - 콜백함수를 호출할 때 자신의 this를 상속하도록 정의돼 있다
    - 메서드명의 점(.)앞 객체가 곧 this

<br>

### 생성자 함수에서 this

생성자 함수로 호출된 경우 내부의 this는 구체적인 인스턴스 자신이 된다

<img src="https://user-images.githubusercontent.com/107349637/209520469-44fc3552-d34b-44a4-a29b-0b01a3dc523e.jpg" width="350"/>


    1. 생성자 함수를 호출하면
    2. 생성자 prototype 의 프로퍼티를 참조하는 __proto__ 프로퍼티가 있는 인스턴스(객체)를 만들고
    3. 해당 객체(this)에 공통된 속성을 부여한다

<br>

### 명시적 this 바인딩

1. call 메서드 ( 첫번째 인자 : this, 매개변수… )
2. apply 메서드 (첫번째 인자 : this, 두번째 인자 : 배열→ 매개변수)
3. bind 메서드 (첫번째 인자 : this, 인수…)


#### (1) call 메서드

호출 주체 함수를 즉시 실행하도록 하는 명령

이때 **첫번째 인자 ⇒ this 바인딩** / 나머지 인자 ⇒ 호출할 함수의 매개변수 로 지정

<img src="https://user-images.githubusercontent.com/107349637/209520728-022d2f09-585b-4271-9d7d-afef7e1dd3ea.jpg" width="300"/>
<br>

#### (2) apply 메서드

호출 주체 함수를 즉시 실행하도록 하는 명령 (call 과 동일)

이때 **첫번째 인자 ⇒ this 바인딩** / 두번째 인자 : **배열** ⇒ 배열의 요소를 호출할 함수의 매개변수 로 지정

![코어자바스크립트-16 4 (1)](https://user-images.githubusercontent.com/107349637/209521046-c7b11732-286f-420a-9391-f9276fbe2f7f.jpg)

#### (3) bind 메서드

call, apply 처럼 바로 함수를 호출하지않고 넘겨받은 this 와 인수를 새로운 함수에 반환하는 메서드

첫번째 인자 ⇒ this 바인딩 / 두번째 인자 부분 적용할 인수

→ 새 함수에 **미리 this를 지정하고 매개변수를 부분 적용한다**

![코어자바스크립트-17 4 (1)](https://user-images.githubusercontent.com/107349637/209521002-7c854ce9-6354-42ac-b5b4-15e24d575fa1.jpg)

    원본 함수 (this = 전역객체 / 매개변수를 부분적용할 수 없다 / 함수명 앞에 접두사가 붙지 않는다


<img src="https://user-images.githubusercontent.com/107349637/209521097-4eabfa97-3ae5-4d23-9889-9c5bad926a19.jpg" width="350"/>

    ⇒ 기존 bind 메서드를 호출할 때 전달했던 ❶ 인수들 뒤에 이어서 ❷ 인수를 적용한다

    ⇒ name 프로퍼티에 접두사 ‘bound’가 붙는 성질이 있다 ( = 원본 함수에 bind 메서드를 적용한 새로운 함수라는 의미 )


#### (4) 메서드를 사용하여 내부함수에 this전달하는 방법

<img src="https://user-images.githubusercontent.com/107349637/209521233-3d12b6da-e1d6-40cb-ab44-c01107b602bd.jpg" width="300"/>

#### (5) call, apply 메서드의 활용

유사배열객체에 배열메소드 적용

생성자 내부에서 다른 생성자 호출

여러 인수를 묶어 하나의 배열로 전달