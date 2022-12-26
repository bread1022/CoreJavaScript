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

### 콜백함수 예제