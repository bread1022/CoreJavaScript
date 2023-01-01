## 1. 데이터타입

### 1-1. 데이터타입의 종류
<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209169000-419d18d6-1907-4c8c-96c4-09ef2b9435b8.png" width="600"/></p>

**primitive type** : 변수에 원시값을 할당하면 변수(메모리공간)에 실제값이 저장되고, 다른 변수에 할당하면 원시값이 복사되어 “값에 의한 전달”이라 한다

**reference type** : 변수에 할당하면 원본의 참조값이 복사되어 전달되므로 “참조에 의한 전달”이라 한다

### 1-2. 데이터 타입에 관한 배경지식
- 컴퓨터는 0과 1로 이루어진 기계어만 이해할 수 있기때문에 (이진법) 프로그래밍 언어가 필요하다
- CPU는 전기신호를 바탕으로 연산을 하는데 이 전기신호의 최소단위가 1bit
    - 1bit = 1 또는 0 으로 이루어진 하나의 메모리 조각 → 1 byte = 8 bit
    - 컴퓨터 메모리 최소단위 = **1 byte** 단위로 관리
        - JS는 숫자 64bit (8 byte 를 확보) , 문자열은 정해진 규격이 없음
        - 1 byte 영문자 1글자 저장 / 한글은 2 byte … (언어별 필요한 메모리 용량과 글자수가 가변적임)
- **변수** : 하나의 값을 저장하기 위한 *메모리 공간*
- **식별자** : 값을 식별할 수 있는 고유한 이름으로 *변수의 이름*
    - 값의 위치를 가리키는 상징적인 이름
    - 선언 必 -> 선언하지 않은 식별자에 접근시 Reference Error 발생
    - 값이 아니라 메모리 주소를 기억하고, 주소를 통해 저장된 값에 접근 => 메모리 주소에 붙인 이름


### 1-3. 변수 선언 & 할당

```js
var a = 'abc';

(선언)
var a ;

(할당)
a = 'abc' ;
```

    (선언)
    1. 변할 수 있는 데이터 공간을 생성
    2. 공간의 이름은 a : 식별자

    (할당)
    3. a 라는 공간에 문자열 ’ abc ’ 를 할당



### 1-4. 기본형데이터와 참조형데이터의 차이

**변수** : 변수 영역 메모리의 변경 O - 변수 공간에 다른 데이터 재할당 O

**상수** : 변수 영역 메모리의 변경 X

**불변성** : 데이터 영역 메모리의 변경 X - Primitive type(number, string, boolean, null, undefined, symbol)


### 1-5. 불변객체

객체에 값을 전달해 변경을 가해도 원본 객체는 불변성을 확보해야할 때 불변객체가 필요하다

- 얕은 복사 : 기존 정보 복사하여 새 객체를 반환 (프로퍼티 복사 시 그 주솟값만 복사 → 사본이 변경되면 원본도 바뀐다)

```js
var copy = function(target) {
  var result = {};
  for(var prop in target) {
    result[prop] = target[prop];
  }
  return result;
};
```

- 깊은 복사 : 객체의 프로퍼티에서 값이 기본형데이터인 경우 그대로 복사하면 되지만, 참조형 데이터는 다시 그 내부 프로퍼티를 복사해야한다 → 재귀적으로 수행 ⇒ 원본과 사본이 서로 다른 객체를 참조, 서로 영향X

```js
var deepCopy = function(target) {
  var result = {};
  if(typeof target === 'object' && target !== null) {
    for(var prop in target){
      result[prop] = deepCopy(target[prop]);
    }
  } else {
    result = target;
  }
  return result;
};
```

```js
var copyJSON = function(target){
  return JSON.parse(JSON.stringify(target));
};
```

### 1-6. undefined, null

**undefined 가 반환되는 경우**

1. 데이터영역 메모리 주소를 지정하지 않은 식별자에 접근하는 경우(값을 대입하지 않은 변수)
2. 객체 내부에서 존재하지 않는 프로퍼티에 접근하는 경우
3. return 문이 없거나 호출되지 않은 함수의 실행결과

**비어있음을 명시적으로 나타내고 싶은 경우 null을 사용**

- typeof null = Object 이므로 동등연산자말고, 일치연산자(===)로 null 데이터타입 을 확인해야한다