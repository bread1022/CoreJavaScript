## 2. 실행컨텍스트

- **실행컨텍스트** : 실행할 코드에 제공할 환경 정보들을 모아놓은 **객체**
    - 소스코드를 실행하는데 필요한 환경을 제공하고 실행결과를 관리하는 영역
    - 식별자를 등록하고 관리하는 스코프 ( ***렉시컬환경으로 관리*** )
    & 코드 실행순서를 관리 ( ***실행 컨텍스트 Stack 으로 관리*** ) 구현한 내부 메커니즘

        ⇒ 모든 코드는 실행컨텍스트를 통해 실행되고 관리
<br>

- 실행컨텍스트 객체는 **활성화** 되는 시점에서

    ①VariableEnvironment(VE)

    ②LexicalEnvironment(LE)

    ③ThisBinding 의 정보를 수집
<br>

- 실행컨텍스트를 생성 초기에
VariableEnvironment 와 LexicalEnvironment 는 하나의 동일한 렉시컬환경을 참조한다

    - 함수를 실행할 때마다 LE 는 변경사항을 즉시 반영


    - VE 는 초기상태를 유지


- **Variable environment (VE)**
    - Environment Record : 현재 실행 컨텍스트 내에서 호이스팅이 되는 변수 선언, 함수 선언문 등을 저장

    - outerLexicalEnvironment Reference : outer environment

- **Lexical environment (LE)**
    - Environment Record: 변수선언 및 함수선언문 만을 저장

    - outerLexicalEnvironment Reference : Variable environment (VE)

<br>

### 렉시컬환경 **(LE)**

실행컨텍스트를 구성하는 컴포넌트로

식별자와 식별자에 바인딩된 값, 상위 스코프에 대한 참조를 기록하는 자료구조

⇒ 식별자와 스코프를 관리

<p align="center"><img src="https://user-images.githubusercontent.com/107349637/209531135-66d575f9-17ac-4c8a-9323-f771c6a12026.png" width="500"/></p>

    식별자를 Key로 등록하고 식별자에 바인딩된 값을 관리
    ⇒ 렉시컬환경은 스코프를 구분하여 식별자를 등록하고 관리하는 저장소 역할


### 렉시컬환경의 구성

**2개의 컴포넌트**
1. 환경레코드 **environmentRecord**
2. 외부렉시컬환경에 대한 참조 **outerLexicalEnvironmentReference**

- **environmentRecord**
    - 매개변수명, 변수 함수의 식별자 등을 수집
    - 현재 실행 컨텍스트 내에서 호이스팅 되는 var, let, const, 함수 선언문 등을 저장한다

- **outerLexicalEnvironmentReference**
    - 상위 스코프( 외부 렉시컬 환경 )
    즉, 해당 실행 컨텍스트를 생성한 코드를 포함하는 상위 코드의 렉시컬 환경을 말한다
    - 해당 함수가 선언된 위치의 LexicalEnvironment를 참조
    - 직전 컨텍스트의 LexicalEnvironment 정보를 참조