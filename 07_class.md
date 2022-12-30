## 7. 클래스

JS 프로토타입기반 언어이기 때문에 상속의 개념이 존재하지 않지만

여러 니즈에 따라 ES6에 클래스 문법이 추가되었고 일정부분이 프로토타입을 활용하고 있다
<br>

### 7-1. 클래스와 인스턴스

**Class** : 어떤 사물의 공통 속성을 모아 정의한 추상적인 개념 / (인스턴스입장에서 : **조건**)

**instance** : 클래스의 속성을 지니는 구체적인 **예시**

- 하나의 인스턴스는 하나의 클래스만을 바탕으로 생성된다
    - 어떤 인스턴스가 다양한 클래스에 속할 순 있지만
    인스턴스를 생성할 때 호출할 수 있는 클래스는 오직 하나뿐이기 때문이다

<p><img src="https://user-images.githubusercontent.com/107349637/210022347-0eedb2ac-3fb4-4cf8-9436-801aafaad675.png" width="400"/></p>

    SuperClass(상위클래스) 의 조건을 충족하면서
    구체적인 조건이 추가된 것을 Subclass(하위클래스)
    클래스의 여러 속성들을 지니고 실존하는 개체를 instance(인스턴스)라고 한다

- 프로그래밍 언어상에서는 (현실에선 존재하는 개체들을 구분짓기위해 클래스를 이용)
    - 사용자가 직접 여러가지 클래스를 정의해야하며
    - *클래스를 바탕으로 인스턴스를 만들때 비로소 어떤 개체가 클래스의 속성을 지니게 된다*

<br>

### 7-2. 자바스크립트의 클래스

```js
// 생성자 함수 Array
const arr = new Array;

// new 연산자와 함께 Array 생성자함수를 호출하여 Array인스턴스를 생성하고 이를 arr에 할당
```

<p><img src="https://user-images.githubusercontent.com/107349637/210022553-f9e82bc2-0d89-49ed-9400-5b62ef073056.png" width="400"/></p>

    인스턴스의 참조 여부에 따라 스태틱 멤버, 인스턴스 멤버로 나뉜다

    - Array의 프로퍼티 중 prototype 를 제외한 나머지는 인스턴스에서 참조가 불가하다 ⇒ static member
    - Array의 prototype 객체 요소들이 인스턴스에서 참조가 가능하고 (상속: 프로토타입 체이닝에 의한 참조) ⇒ instance member

스태틱 메서드 | 프로토타입 메서드 |
---|---|
클래스(생성자함수)에 직접 정의한 메서드 | 클래스의 프로토타입 내부에 정의된 메서드
→ 인스턴스에서 호출불가 (클래스에 의해서만 호출가능) | 인스턴스 참조가능 (호출가능)
스태틱메서드를 호출할 때는 this 를 클래스로 하여 직접 접근해야만 하기 때문에 클래스 그 자체가 하나의 개체로 취급된다 (예제7-1)| 

<br>

```js
(클래스 관점에서 본 프로토타입 시스템, 예제 7-1)

// ❶ 생성자 함수 Rectangle
var Rectangle = function(width, height) {
  this.width = widthi;
  this.height = height;
};

// 프로토타입 메서드
Rectangle.prototype.getArea = function() {
  return this.width * this.height;
};

// 스태틱 메서드
Rectangle.isRectangle = function(istance) {
  return instance instanceof Rectangle && instance.width > 0 && instance.height > 0;
};

// ❷ new 연산자와 함께 Rectangle 함수를 호출해서 인스턴스 생성하고 rect1에 할당
var rect1 = new Rectangle(3,4);

console.log(rect1.getArea()); //12 -> 인스턴스에서 직접 호출 가능
console.log(rect1.isRectangle(rect1)); //Error: not a function -> 인스턴스에서 접근 불가
console.log(Rectangle.isRectangle(rect1)); // true -> 생성자 함수를 this로 해야 호출 가능
```

<br>

### 7-3. 클래스의 상속

### 7-3-1. 상속 구현

```js
(예제 7-5)
// ❶ 생성자 함수 Rectangle
var Rectangle = function(width, height) { //constructor: ƒ (width, height)
  this.width = width;
  this.height = height;
};

Rectangle.prototype.getArea = function() {
  return this.width * this.height;
};

// new 연산자와 함께 Rectangle 함수를 호출해서 인스턴스를 할당
var rect = new Rectangle(3, 4);
console.log(rect.getArea());  //12

// ❷ Rectangle과 공통요소를 가지는 Square 생성자 함수 => 중복되는 코드를 위해 하위클래스로 넣는 과정이 필요하다
var Square = function(width) {
  this.width = width;
  this.height = width;
};

Square.prototype.getArea = function() {
  return this.width * this.height;
};
```

```js
(예제 7-6, 7-7)
// Square를 Rectangle의 하위 클래스로 삼기위해 Rectangle을 상속하는 Square생성자함수로 바꿔 정의
var Square = function(width) {

// ❹ 생성자 함수 내부에서 Rectangle 생성자 함수를 함수로써 호출
// <this_call메서드의 활용p85> this = Rectangle 생성자 함수를 호출하여 인스턴스의 속성을 정의
  Rectangle.call(this, width, width);
};

// ❺ Square 프로토타입객체에 Rectangle 인스턴스를 부여하여 메서드를 상속
Square.prototype = new Rectangle();

var sq = new Square(5);
console.log(sq.getArea());  //25

console.dir(sq);  // Square
```

    ▼ Square의 인스턴스임을 표시
    ▼ __proto__ : Rectangle의 인스턴스임을 표시

    * call 메서드 : 함수를 그냥 호출하면 전역이지만 call 메서드를 이용해 임의의 객체를 this로 지정이 가능하다


```js
var rect2 = new sq.constructor(2,3);
console.log(rect2); // Rectangle {width: 2, height: 3} -> Square의 인스턴스가 아닌 Rectangle
```

    - Rectangle (width, height) : undefined ( Square.prototype 에 값이 존재함 )
    - 하위클래스로 삼을 생성자 함수의 prototype에 상위클래스의 인스턴스를 부여하여  메서드 상속을 할 순 있지만 다양한 문제가 발생할 수 있다

- 7-3-2. 클래스 상속 및 추상화 방법 (클래스가 구체적인 데이터를 지니지 않게 하는 방법)
(1) 인스턴스 생성 후 프로퍼티 제거
(2) 빈 함수 활용
(3) Object.create 활용
<br>


### 7-4. ES6의 클래스 및 클래스 상속

```js
(예제 7-15)
// ❶ 생성자 함수 ES5
var ES5 = function(name) {
  this.name = name;
};

// ❷ ES5 스태틱 메서드
ES5.staticMethod = function() {
  return this.name + ' staticMethod';
};

// ❸ ES5 프로토타입 메서드
ES5.prototype.method = function() {
  return this.name + ' method';
};

// ❹ new 연산자를 이용해 ES5 인스턴스 생성하고 es5Instance에 할당
var es5Instance = new ES5('es5');

console.log(ES5.staticMethod());  // ES5 staticMethod
console.log(es5Instance.method());  // es5 method
```

```js
(클래스 문법)

// ❶ ES6 클래스 생성 : Class { } = 클래스 본문 영역 -> constructor, method
var ES6 = class {
  constructor(name) {  // ❷ constructor { } = 생성자함수와 같은 역할(인스턴스생성)
    this.name = name;
  }

  static staticMethod() {  // ❸ 해당 클래스만 호출가능한 스태틱 메서드
    return this.name + ' staticMethod';
  }

  method() {  // ❹ 자동적으로 프로토타입 객체 내부에 할당되는 메서드 (인스턴스에서 호출 가능)
    return this.name + ' mthod';
  }
};

var es6Instance = new ES6('es6');
console.log(ES6.staticMethod());  // ES6 staticMethod
console.log(es6Instance.method()); // es6 mthod
```

```js
(클래스 상속, 예제 7-16)

// ❶ Rectangle 클래스 생성
var Rectangle = class {
  constructor(width, height) {  // Constructor
    this.width = width;
    this.height = height;
  }
  getArea() {
    return this.width * this.height;
  }
};

// ❷ Square에 Rectangle 클래스를 상속받는 subClass로 만들기 위함(class명령어 뒤 키워드extends)
var Square = class extends Rectangle {
  constructor(width) {
    super(width, width); // ❸ super키워드를 이용해 SuperClass의 constructor를 실행
  }

  getArea() {
  // ❹ constructor 외에 다른 메서드에서 super(= 객체)는 SuperClass.prototype 로 사용
  // 이때 this = 원래의 this 따름, not super
    console.log('size is:', super.getArea());
  }
};
```