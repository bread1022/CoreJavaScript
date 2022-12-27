## 4. 콜백함수

**정의** : parameter로 콜백함수를 전달받고 함수의 내부에서 실행되는 함수

→ 파라미터로 콜백함수 받아, 나중에 호출하는 개념(call back)

→ 콜백함수를 위임받은 코드는 자체 내부 로직에 의해 이 콜백함수를 적절한 시점에서 실행한다

**용도** : 순차적으로 함수를 실행해야 될 때 (비동기)

**사용원칙**

1. 익명함수를 사용
2. 파라미터로 함수명만 전달
3. 변수(전역,지역)를 콜백함수의 파라미터로 전달가능하다

```js
function 함수1(parameter_함수) {
  //함수의 파라미터로 함수를 넣어, 비동기 작업이 가능
  parameter_함수();
}

함수1( function() {	console.log(2);} );
```

```js
function 함수1(parameter_함수) {
  parameter_함수();
}

function second() {
  console.log(2);
};

함수1(second);
```

```js
function 함수1(parameter_함수) {
  console.log(1);
  //1 출력 후, 함수 실행하고 싶을 때 인자에 원하는 함수를 넣는다
  parameter_함수();
}

function second() {
  console.log(2);
};

함수1(second);
```
<br>

### 콜백함수 예제

1. **배열 메서드**

    콜백함수를 호출하는 주체가 배열의 메서드

    → 메서드에 콜백함수를 넘겨받았기 때문에 콜백함수를 호출 할 때 인자의 값과 순서에 대한 제어권을 갖는다

	```js
	Array.map(callback(currentValue[, index[, array]])[, thisArg])
	Array.forEach(callback(currentvalue[, index[, array]])[, thisArg])
	```



      	- 첫번째인자로 callback 함수를 받고, 생략가능한 두번째 인자로 콜백함수 내부 this 특정가능
      	- callback: function(currentValue, index, array)
      	  1. currentValue : 처리할 현재 요소
      	  2. index :  처리할 현재 요소의 인덱스
      	  3. array : 메소드를 호출한 배열 (순회되는 대상 - 배열 객체)



	```js
	Array.filter(callback(element[, index[, array]])[, thisArg])
	Array.find(callback(element[, index[, array]])[, thisArg])
	```
      	- callback : function(element, index, array)
      			1. element : 콜백함수에서 처리할 현재 요소
      			2. index : 콜백함수에서 처리할 현재 요소의 인덱스
      			3. find 함수를 호출한 배열



	```js
	Array.reduce(callback[, initialValue])
	```

      	- callback: function(acc, cur, curIndex, array)
      			1. accumulator : 콜백 반환값을 누적
      			2. currentValue : 처리할 현재 요소
      			3. currentIndex : 처리할 현재 요소의 인덱스
      			(initialValue가 없는 경우 1부터 시작, 제공한 경우 0)
      			4. array : reduce()를 호출한 배열

2. DOM 의 **addEventListener** → 버튼을 누르면 ? 코드를 실행

	```js
	document.querySeletector('.button').addEventListener('click', function() {});
	```

3. 비동기함수 **setTimeout** →일정 시간 후 코드를 실행

	```js
	setTimeout(function() {}, 1000);
	```
		콜백함수의 호출이 비동기로 동작
		1. setTimeout 함수 호출되면 실행컨텍스트가 생성되어 콜스택에 푸시
		2. 콜백함수호출을 기다리지않고 즉시 종료되어 콜스택에서 제거 & 콜백함수를 스케쥴링
		3. 타이머가 만료되면 콜백함수는 태스크 큐로 푸시되고
			콜스택이 비어졌을 때 이벤트루프에 의해 콜스택으로 푸시되어 실행
		4. 타이머 id를 반환하고 즉시 종료
		=> setTimeout함수가 종료된 이후 콜백 함수가 호출되는 것이므로
		setTimeout함수의 콜백함수가 실행 중일 때 현재 실행중인 실행컨텍스트의 하위 실행컨텍스트가 setTimeout이 아님
		따라서 내부 콜백함수에서 결과를 외부로 반환(return)하거나 상위 스코프의 변수에 할당할 수 없고
		try...catch구문으로 에러처리를 잡을 수 없다

<br>

### 콜백함수도 일반 함수다

콜백함수로 객체의 메서드를 전달하면 메서드가 아닌 일반 함수로서 호출된다

```js
var obj = {
  element : [1, 2, 3],
  func : function(v, i) {
    console.log(this, v, i);
  }
};

obj.func(1, 2);  // {element: Array(3), func: ƒ} 1 2  => this = obj객체

[4,5,6].forEach(obj.func); // 인자로 메서드를 함수로서 전달한 것 (obj와 관련이 없음)
// Window {...} 4 0   => this = 전역객체
// Window {...} 5 1
// Window {...} 6 2
```
<br>

### 콜백함수의 this 바인딩

```js
<전통방식>

var obj1 = {
  name : 'obj1',
  func : function() {

    var self = this;  //변수에 this 담기

    return function() {
      console.log(self.name);
    }
  }
}

var callback = obj1.func();
setTimeout(callback, 1000);   // 1초 후 -> obj1
```

```js
<bind 메서드>

var obj1 = {
  name : 'obj1',
  func : function() {
    console.log(this.name);
  }
};

setTimeout(obj1.func.bind(obj1), 1000); //첫번째 인자를 this binding

var obj2 = { name: 'obj2' };
setTimeout(obj1.func.bind(obj2), 2000);

// 1초 후 obj1
// 2초 후 obj2
```

<br>

## 콜백지옥..이란?

비동기작업 중 발생하는 문제로, 함수의 매개변수로 넘겨지는 콜백함수가 반복되어 가독성이 나빠지고 비동기처리 중 발생한 에러의 처리가 곤란해지는 현상

#### 해결방법

1. 콜백함수를 분리
2. Promise : 전통적인 콜백패턴의 단점을 보완하며 비동기처리 시점을 명확하게 표현할 수 있다는 장점
3. Async, await : 프로미스 후속 처리 메서드를 사용하지않고 비동기처리를 동기처럼 동작하도록 구현
4. Generator, yield

### Promise

new 연산자로 호출하여 Promise 생성자를 실행하고

Promise 생성자 함수는 비동기 처리를 수행할 콜백함수를 인수로 받고

이 콜백함수는 인수로 resolve, reject 함수를 전달받는다

⇒ “완료되면 통보해, 콜백은 내가 실행하는걸로 약속할게 Promise”


<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209607804-9e800fb6-9301-43fd-a167-5b5224b959b8.png" width="600"/></p>

	프로미스의 상태는 resolve 또는 reject 함수를 호출하는 것으로 결정되고
	프로미스는 비동기 처리상태와 처리결과를 관리하는 객체이다

```js
const promise = new Promise((resolve, reject) => {

  //Promise 생성자 함수의 콜백함수 내부에서 비동기 처리를 수행

  if(비동기 처리 성공 시) { resolve('result'); } //성공시 콜백함수 인수로 전달받은 resolve 함수를 호출하고
  else { 비동기 처리 실패 시 reject('error'); } //실패시 reject 함수를 호출

});
```

#### 프로미스의 후속 처리 메서드

후속 처리 메서드의 콜백함수에 프로미스처리결과가 인수로 전달

모든 후속 처리 메서드는 프로미스를 반환
<br>

- **Promise.then**
    - new Promise 실행 성공 시 실행되고 promise를 반환
    - 두개의 콜백함수를 인수로 전달받는다
        - 첫번째 콜백함수 : Promise가 fulfilled상태(resolve)가 되면 호출되는 성공처리 콜백함수
        → 프로미스 결과를 인수로 받음
        - 두번째 콜백함수 : Promise가 rejected상태가 되면 호출되는 실패처리 콜백함수
        → 프로미스 에러를 인수로 받음
<br>

- **Promise.catch**
    - new Promise 실행 실패 시 실행되고 promise를 반환
    - 콜백함수가 rejected 상태인 경우 호출되며 예외처리를 실행한다
    - 한개의 콜백함수를 인수로 받음
<br>

- **Promise.finally**
    - resolve, reject의 성공여부의 상관없이 무조건 1번 호출된다
    - 프로미스의 상태와 상관없이 공통적으로 수행해야 할 처리가 있을 때 유용하다
    - 한개의 콜백함수를 인수로 받음
<br>

- **Promise.all**
    - 여러개의 비동기작업(Promise)을 병렬적으로 처리할 때 사용
    - [배열] 등의 이터러블을 인수로 받고,
    전달받은 모든 프로미스가 fulfilled되면 처리결과를 배열에 저장해 새로운 프로미스를 반환한다
    - 하나라도 reject 이면 나머지가 fulfilled되기를 기다리지 않고 즉시 종료하고
    먼저 rejected된 프로미스의 reject 에러가 catch 메서드로 전달된다

		```js
		const request1 = () => { new Promise(resolve => setTimeout(() => resolve(1), 3000)) }
		const request2 = () => { new Promise(resolve => setTimeout(() => resolve(2), 2000)) }
		const request3 = () => { new Promise(resolve => setTimeout(() => resolve(3), 1000)) }

		//3개의 비동기작업을 병렬로 처리 => 3000sec 조금 더 소요됨
		Promise.all([request1(), request2(), request3()]).then().catch(console.log(err));
		```
<br>


### Async, await

- 제너레이터보다 가독성이 좋고,
프로미스의 후속처리 메서드를 사용하지않고
비동기처리를 동기처럼 동작하도록 구현할 수 있다
- 에러처리
    - try…catch문을 사용해 에러를 캐치한다
    (프로미스를 반환하는 비동기함수를 명시적으로 호출할 수 있기때문에 호출자가 명확)

		```js
		const foo = async () => {
		  try {
			 const response = await fetch(URL);
		  } catch (err) {
			 console.error(err);
		  }
		}

		foo();
		```

	- catch문을 사용하지 않으면 async함수는 발생한 에러를 reject 하는 프로미스를 반환

		```js
		const foo = async () => {
		  const response = await fetch(URL);
		}

		foo().then().catch(console.error);
		```

<br>

#### async 함수
async키워드를 사용해 정의하며 반환값을 resolve하는 프로미스를 반환한다

```js
async function bar() {

  //처리 순서 상관없이 개별적으로 수행되는 비동기처리이므로 대기가 필요없을 때
  const resol = await Promise.all([
	 new Promise(resolve => setTimeout(() => resolve(1), 3000)),
	 new Promise(resolve => setTimeout(() => resolve(2), 2000)),
	 new Promise(resolve => setTimeout(() => resolve(3), 1000))
]);

  console.log(resol);   //[1,2,3]
}

bar();
```


#### await 함수

```js
async function bar(n) {
  const a = await new Promise(resolve => setTimeout(() => resolve(n), 3000));
  const b = await new Promise(resolve => setTimeout(() => resolve(a+1), 2000));
  const c = await new Promise(resolve => setTimeout(() => resolve(b+1), 1000));

  //a 다음 b 다음 c 처리순서가 보장되어야 하므로 모든 프로미스에 await을 써서 순차적으로 처리한다
  console.log([a,b,c]);  //[1,2,3]
}
bar(1);
```


<br>

### Generator, yield

코드블록의 실행을 일시중지했다가 필요한 시점에 재개할 수 있는 특수한 함수

**선언** : function* / 하나이상의 **yield 표현식**을 포함

```js
function* genFunc() {
  yield 1;
}
```

```js
const obj = {
  *genObjMethod() {
    yield 1;
  }
};
```

	*(애스터리스크)의 위치는 function키워드와 함수명 사이에 위치

**규칙** : 화살표함수 사용 할 수 없고, new 연산자와 함께 생성자 함수로 호출할 수 없다

**특징** : 제너레이터 함수를 호출하면 제너레이터객체를 생성해 반환

**제너레이터 객체** : Symbol.iterator메서드를 상속받는 이터러블이면서 value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유하는 이터레이터이다