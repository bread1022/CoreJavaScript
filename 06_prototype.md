## 6. 프로토타입

클래스 기반 언어에서는 ‘상속’을 사용하지만

프로토타입기반 언어인 JS에서는
어떤 객체를 **prototype**(원형)삼고 이를 **참조**(복제)함으로써 상속과 비슷한 효과를 낸다


> 프로토타입 기반 언어란,
> 객체들이 속성과, 메서드들을 상속받기 위한
> 템플릿으로써 프로토타입 객체 (prototype object)를 가진다는 의미이다

<br>

### 프로토타입의 핵심 관계

<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209900893-610d4491-49f0-4759-b5db-c52be43b3293.png" width="600"/></p>

프로토타입 (= 객체) 내부에는 메서드가 저장되고,

인스턴스의 __ proto__ 프로퍼티(속성)를 통해 메서드에 접근하게 한다

> 상속되는 속성과 메서드들은 각 객체가 아니라,
> 객체의 생성자의 prototype (속성)에 정의되어 있다

> 프로토타입 객체 : __ proto __ 속성으로 접근 가능한 내장 객체이다

- **prototype** (프로퍼티) = 객체
- **__ proto __** (프로퍼티) = 객체
  - 정의된 명칭은 [[ prototype ]]
  - 생략가능한 인스턴스의 프로퍼티 __ proto __ 는 Constructor의 prototype을 참조한다 !
  - **생략가능**하도록 구현돼있기 때문에
  생성자 함수의 Constructor 프로토타입에 있는 메서드 & 프로퍼티 를
  인스턴스 자신의 것처럼 접근할 수 있게 된다 ! (상속을 구현할 수 있게 해주는 방법)
  - “ 객체와 객체를 연결하는 링크 ”

<br>

```js
(예제 6-1)
// 1. Person이라는 생성자 함수
var Person = function(name) {

  // new 연산자를 호출하면 새로운 빈 객체가 메모리상 생성되고, 그 빈 객체가 this에 바인딩되어 리턴을 따로하지 않아도 this.name 값이 상속된다
  this.name = name;  <생성자함수의 속성과 메소드를 정의>
};

// 2. Person 생성자 함수 prototype 에 getName 메서드를 지정
Person.prototype.getName = function() {
  return this.name;
} // ∴ Person 인스턴스는 __proto__ (프로퍼티)를 통해 getName메서드를 호출할 수 있다


var suzi = new Person('suzi'); <인스턴스 생성>
suzi.__proto__.getName(); // undefined => getName()가 메서드로 호출됐을 때의 this = 메서드명 바로 앞 객체 (suzi.__proto__)

console.log(suzi.__proto__);  // {getName: ƒ, constructor: ƒ}
console.log(Person.prototype); // {getName: ƒ, constructor: ƒ}

suzi.getName();  // 'suzi' => 인스턴스에서 __proto__객체를 생략하고 바로 메서드를 호출가능! this = instance (JS에서 생략가능한 프로퍼티로 정의했기 때문)
```
    ⇒ prototype객체를 참조하는 __proto__ 를 생략하면
    인스턴스는 prototype에 정의된 프로퍼티나 메서드를 자신의 것처럼 사용할 수 있다

<br>

```js
(예제 6-2)
var Constructor = function(name) {
  this.name = name; // 생성자함수의 속성 정의
};

// 생성자 함수 prototype 에 메서드와 속성 지정
Constructor.prototype.method1 = function() {};
Constructor.prototype.property1 = 'Constructor 프로토타입 프로퍼티';

var instance = new Constructor('인스턴스');  // new연산자와 함께 인스턴스를 생성

console.dir(Constructor);

console.dir(instance);
```

<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209901742-04e15e81-bf56-4619-bf3a-4682d7ad72a4.jpg" width="400"/></p>

    ▼ Constructor 의 디렉토리
      - f Counstructor(name) : 생성자함수 (= 함수라는 의미)

    ▼ instance의 디렉토리
      - 생성자함수의 인스턴스는 생성자함수의 이름을 표기함으로써
      - new Constructor(’인스턴스’) 해당함수의 인스턴스임을 나타낸다
      - Constructor 프로토타입을 상속받음

<br>

```js
(예제 그림 6-5)
var arr = [1,2]
console.dir(arr);
console.dir(Array);
```

<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209902076-88fd0026-a0a4-4962-a123-f49954b77193.png" width="600"/></p>

    → new 연산자와 함께 Array 호출하면 인스턴스[1, 2] 가 생성되고 그 인스턴스의 __ proto __ 는 Array.prototype을 참조한다

    ⇒ Array.prototype 내 메서드를 인스턴스가 자신의 것처럼 사용이 가능하고, 프로토타입 프로퍼티 내부에 있지 않은 메서드들은 직접 호출할 수 없다

<br>
<br>

### Constructor 프로퍼티

생성자함수의 프로퍼티인 **prototype 객체 내부**와
**인스턴스 __ proto __ 객체 내부**에는 Constructor (프로퍼티)가 있다

- Counstructor 프로퍼티는 원래의 생성자 함수(자기 자신)을 참조한다
- 인스턴스의 생성자 함수를 찾을 때 필요한 수단이다
- 읽기전용속성이 부여된 경우를 제외하고 값변경이 가능하다

```js
(예제 6-4)
var NewConstructor = function () {
  console.log('뉴 컨스트럭터');
};

var dataTypes = [
  1,                   // Number false
  'test',              // String false
  true,                // Boolean false
  [],                  // NewConstructor false -> NewConstructor을 출력(변경된 것)
  new Number(),        // NewConstructor false
  new Object(),        // NewConstructor false
];

console.dir(dataTypes);  // Array(6)
console.log(dataTypes.constructor);   // ƒ Array() { [native code] }
console.log(dataTypes.constructor.name);   // Array


dataTypes.forEach(function(d) {
  d.constructor = NewConstructor; // 값을 변경했을 때
  console.log(d.constructor.name, d instanceof NewConstructor);
});


const constructor = new NewConstructor    // 뉴 컨스트럭터
console.log(constructor instanceof NewConstructor);  // true
```

    => Constructor를 변경하여도 참조대상만 바뀌지 인스턴스의 원형이나 데이터타입이 바뀌진 않는다

<br>

<details>
<summary> instanceof 연산자 : 생성자의 속성이 개체의 프로토타입 체인에 나타나는지 확인 </summary>
<div markdown="1">

```js
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
const auto = new Car('Honda', 'Accord', 1998);

console.log(auto instanceof Car);  // true

console.log(auto instanceof Object);  // true
```

</div>
</details>

<br>

```js
var Person = function(name) {
  this.name = name;
};

var p1 = new Person('사람1');                        // Person {name: '사람1'} true
var p1Proto = Object.getPrototypeOf(p1);            // {constructor: ƒ} false
var p2 = new Person.prototype.constructor('사람2');  // Person {name: '사람2'} true
var p3 = new p1.__proto__.constructor('사람3');
var p4 = new p1.constructor('사람4');
var p5 = new p1Proto.constructor('사람5');

[p1,p1Proto,p2,p3,p4,p5].forEach(function(p) {
  console.log(p, p instanceof Person);
});
```

    Constructor
    인스턴스__proto__컨스트럭터
    인스턴스의 컨스트럭터
    컨스트럭터 프로토타입의 컨스트럭터   ⇒  모두 Person의 인스턴스를 가리킨다

<br>
<br>

### 프로토타입 체이닝

<br>

> 프로토타입 체인이란
> 프로토타입 객체 상위의 프로토타입 객체로부터 속성과 메서드를 상속받고,
> 또 그 상위 프로토타입 객체로부터 상속 받을 수 있도록 하는 것

<br>

```js
(참고 예시)
function sayHello() {
  console.log(`${this.name}:hello`);
}

function Person(name) {
  this.name = name;
}

const chris = new Person('chris');
chris.sayHello(); //Uncaught TypeError: chris.sayHello is not a function
```

```js
function sayHello() {
  console.log(`${this.name}:hello`);
}

function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = sayHello; //프로토타입 메서드를 할당

const chris = new Person('chris');
chris.sayHello(); //chris:hello
```

    - 프로토타입체이닝이 발생 ⇒ 프로토타입링크를 따라 프로토타입을 탐색해나가는 과정
    - chris.sayHello(); 는 chris객체에서 먼저 sayHello( )메서드를 탐색
    → 없으면 Person’s prototype 객체로 이동하여 sayHello( ) 를 탐색
    → 없으면 Person 생성자함수의 prototype 객체로 이동하여 탐색
    → 원하는 값을 찾으면 검색을 중단

<br>

#### (1) 메서드 오버라이드

```js
( 생성자함수의 메서드와 인스턴스의 메서드 이름이 동일할 경우 )

var Person = function(name) {
  this.name = name;
};

Person.prototype.getName = function() {
  return this.name;
};

var iu = new Person('지금');
iu.getName = function() {
  return this.name;
};

console.log(iu.getName());
```

    메서드명이 동일하여 메서드 위에 메서드를 덮어 씌웠다 (원본 변경X)
    → iu.__proto__.getName 이 아닌 iu객체의 getName메서드가 호출된다
    → JS에서는 getName메서드를 가장 가까운 대상인 iu객체 자신의 프로퍼티를 탐색하고,
        없으면 그 다음 대상 __proto__를 탐색하는 순서로 진행된다

<br>

#### (2) 프로토타입 체인

프로토타입은 데이터 타입이 객체이기 때문에 Object.prototype이 객체의 __proto__ 에 연결되고

Object.prototype내부의 메서드도 자신의 것처럼 실행할 수 있다

```js
var arr = [1,2]
console.dir(arr);
```

<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209903270-e843bde5-9d57-4a80-a15d-8285dbd46c5e.png" width="450"/></p>


    → 배열의 리터럴의 __proto__에는 배열의 메서드가 있고, 모든 객체의 __proto__에는 Object.prototype 가 연결되어있다
    → 이때 __proto__는 생략이 가능하므로 Array.prototype 과 Object.prototype 내부의 메서드를 자신의 것처럼 실행할 수 있다
    ⇒ __proto__프로퍼티 내부에 __proto__프로퍼티가 연쇄적으로 이어진 것을
      “프로토타입 체인” 이라하고, 이 체인을 따라가며 탐색하는 것을 “프로토타입 체이닝”이라 한다

<br>

#### (3) 객체 전용 메서드의 예외사항

- 어떤 생성자 함수든 prototype 은 객체이기 때문에 Object.prototype을 프로토타입 체인 최상단에 위치시킨다
- 객체에서만 사용할 메서드는 프로토타입 객체안에 정의할 수 없다
    - 참조형뿐만아니라 다른 데이터 타입도 __proto__에 반복 접근함으로써 접근가능한 최상위존재 이기 때문
    - 객체만을 대상으로 동작하는 객체 전용 메서드들은 Object.prototype이 아닌 static method로 부여해야한다 (정적메서드)
- Object.prototype에는 모든 데이터타입에서 사용할 수 있는 범용적인 메서드만이 존재
- Object.prototype의 메서드는 어떤 데이터에서도 활용가능하다
    - toString
    - hasOwnProperty
    - valueOf
    - isPrototypeOf
    - 등 모든 변수가 자신의 메서드인 것처럼 호출이 가능함

<br>

<details>
<summary> 예외) Object.create : __proto__가 없는 객체를 생성 </summary>
<div markdown="1">

```js
var Person = function(name) {
  this.name = name;
}

var person1 = Person('nani');

var person2 = Object.create(person1); // person1객체를 프로토타입 객체로 삼아 새로운 객체를 생성

console.log(person1);     // Person {name: 'nani'}
console.log(person1.__proto__);  // {constructor: ƒ}
console.log(person2);  // Person {}
```

</div>
</details>

<br>

#### (4) 다중 프로토타입 체인
