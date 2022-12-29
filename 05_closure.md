## 5. 클로저

” 생명 주기가 끝난 외부함수의 변수를 참조하는 함수 ”

” 함수 선언때 생성된 유효범위가 사라진 후에도 호출할 수 있는 함수 ”

” 자신이 생성될 때 스코프에서 알 수 있었던 변수들 중 언젠가 자신이 실행될 때
   사용할 변수들만을 기억하여 유지 시키는 함수 “

⇒ 어떤 함수 A에서 선언한 **변수 a 를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행컨텍스트가 종료된 이후에도 변수 a에 접근이 가능한 현상**

<br>

---

### 클로저의 활용
1. 콜백함수 내부에서 외부데이터 사용하고자 할 때
2. 정보은닉
3. 부분적용
4. 커링함수
<br>

### (1) 콜백함수 내부에서 외부데이터 사용하고자 할 때

#### (1)-① 콜백함수를 내부함수로 선언하여 외부변수를 직접 참조
```js
var fruits = ['apple', 'banana'];
var $ul = document.createElement('ul');

fruits.forEach(function(fruit) {
//fruits배열을 순회하며

	var $li = document.createElement('li');
  //li태그생성하고

	$li.innerText = fruit;
	//배열요소를 text로 입력

	$li.addEventListener('click', function() {
		alert(fruit);
		//li태그가 클릭될 때마다 function alert(배열요소) -> 외부변수 계속 참조
	});

	$ul.appendChild($li);
});

document.body.appendChild($ul);
```

```js
<이벤트리스너함수 외부로 분리>

var alertFruit = function(fruit) {
  alert(fruit);
};

fruits.forEach(function(fruit) {
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListner('click', alertFruit);
  $ul.appendChild($li);
});

document.body.appendChild($ul);
alertFruit(fruits[1]);
```

#### (1)-② bind 활용


#### (1)-③ 콜백함수를 고차함수로 바꿔 클로저로 활용

```js
var fruits = ['apple', 'banana'];
var $ul = document.createElement('ul');

var alertFruit = function(fruit) {

  //고차함수 : 함수를 인자를 받거나 함수를 리턴하는 함수
  return function() {
    alert(fruit);
  };

};

fruits.forEach(function(fruit) {
  var $li = document.createElement('li');
  $li.innerText = fruit;
  $li.addEventListner('click', alertFruit); -> li
  $ul.appendChild($li);
});

document.body.appendChild($ul);
alertFruit(fruits[1]);
```

<br>

### (2) 정보은닉(접근 권한 제어)

모듈 내부 로직에 대해 외부로의 노출을 최소화하여 모듈간 결합도를 낮추고 유연성을 높이는데 필요한 개념

```js
let num = 0;

function makeCounter1() {
  num ++;
}

console.log(num); // 0
makeCounter1();
console.log(num); // 1

//어디서나 num에 접근이 가능함
num = 100;
makeCounter1();
console.log(num);  // 101
```

```js
<위의 예제를 클로저를 활용해 num의 정보를 은닉>

function closure() {
  let num = 0;

// 아래의 함수 외에는 num에 접근할 수 없음
// => 클로저
  function numPlus() {
    num++;
  }
  function printNum() {
    console.log(num);
  }

// 외부에서 접근 권한을 주고자 하는
// 대상들로 구성된 참조형데이터를 return
// => return만 공개, 그 외 변수는 비공개
  return {
    numPlus,
    printNum,
  }
}

const numClosure = closure();
numClosure.printNum(); // 0
numClosure.numPlus();
numClosure.printNum(); // 1
```

### (3) 부분적용

- 원하는 만큼의 인자를 **미리 넘겨 기억 해두고**,
추후 *필요한 시점에 기억했던 인자들과 추가할 인자를 함께 실행하는 것*
- bind메서드 (this 바인딩, 부분적용인자들) → this를 명시적으로 변경하고 부분적용
클로저는 this에 관여하지 않고 부분적용이 가능하다

```js
var partial = function() {
  var originalArgs = arguments;
  var func = originalArgs[0];

  if(typeof func !== 'function') {
    throw Error;
  }

  return function () {
    var partialArgs = Array.prototype.slice.call(originalArgs, 1);
  //this = originalArgs, slice(0인덱스 제외하고 얕은복사)
  //[1, 2, 3, 4, 5]

  var restArgs = Array.prototype.slice.call(arguments);
  //this = arguments
  //[6, 7, 8, 9, 10]

  //실행시점의 this를 바인딩하여 this에는 아무 영향을 주지 않는다
  return func.apply(this, partialArgs.concat(restArgs));
  };
};

var add = function() {
  var result = 0;
  for(var i = 0; i < arguments.length; i++) {
    result += arguments[i];
  }
  return result;
};

var addPartial = partial(add, 1,2,3,4,5); //인자로 함수와 부분적용인자를 전달
console.log(addPartial(6,7,8,9,10)); //55
```

### (4) 커링함수

- 여러개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠 순차적으로 호출될 수 있게 체인형태로 구성한 것
- 한번에 하나의 인자만 전달하는 것을 원칙으로 한다
- 중간과정 함수 결과는 다음 인자를 받기위해 대기하고,
마지막 인자가 전달되기 전까지 원본함수가 실행되지 않는다

```js
var curryFunc = function(func) {
  return function (a) {
    return function (b) {
    return func(a,b);
    };
  };
};

var getMaxValue = curryFunc(Math.max)(5);
console.log(getMaxValue(3));  //5 (a = 5 , b = 3)
console.log(getMaxValue(7));  //7 (a = 5 , b = 7)


var getMinValue = curryFunc(Math.min)(5);
console.log(getMinValue(3));  //3 (a = 5 , b = 3)
console.log(getMinValue(7));  //5 (a = 5 , b = 7)
```

- 필요 인자가 많아 가독성이 떨어질 땐 → 화살표 함수를 사용하면 좋다
- 값을 계속해서 넘겨주고 마지막 func 함수가 호출되는 마지막 단계에서 인자들을 모두 참조할 것이다

  이때 전달한 값들은 GC되지않고 메모리에 쌓여있다 마지막 호출로 실행컨텍스트가 종료되면 그때 GC 수거대상이 된다

  ⇒ 이것을 함수형프로그래밍에서 **지연실행**이라고 한다

```js
var curryFunc = func => a => b => c => d => e => func(a,b,c,d,e);
```