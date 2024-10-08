## 23.1 소스코드의 타입

- ECMAScript는 소스코드를 4가지 타입으로 구분
  - 전역 코드(Global Code): 전역에 존재하는 소스코드
  - 함수 코드(Function Code): 함수 내부에 존재하는 소스코드
  - eval 코드(Eval Code): 빌트인 전역 함수인 eval 함수에 인수로 전달되어 실행되는 소스코드
  - 모듈 코드(Module Code): 모듈 내부에 존재하는 소스코드
- 전역 코드
  - 전역 변수를 관리하기 위해 전역 스코프 생성
  - var 전역 변수와 함수 선언문의 전역 함수를 전역 객체의 프로퍼티와 메서드로 바인딩
- 함수 코드
  - 지역 스코프를 생성하고 지역변수, 매개변수, arguments 객체를 관리
  - 전역 스코프에서 시작하는 스코프 체인의 일원으로 연결
- eval 코드
  - strict mode(엄격 모드)에서 자신만의 독자적인 스코프를 생성
- 모듈 코드
  - 모듈별로 독자적인 모듈 스코프를 생성

<br>

## 23.2 소스코드의 평가와 실행

- JS 엔진은 소스코드를 “평가” 및 “실행”의 2가지 과정으로 처리
- 소스코드의 평가
  - 실행 컨텍스트를 생성
  - 변수/함수 등의 선언문만 먼저 실행
  - 생성된 변수나 함수 식별자를 Key로, 실행 컨텍스트가 관리하는 스코프에 등록
- 소스코드의 실행

  - 선언문을 제외한 소스코드가 순차적으로 실행
  - 변수나 함수의 참조 등 소스코드 실행에 필요한 정보는 실행 컨텍스트가 관리하는 스코프에서 검색해 취득
  - 스코프의 실행 결과는 다시 실행 컨텍스트가 관리하는 스코프에 등록

    ```jsx
    var x = 1;
    var x;
    x = 1;

    // 소스코드 평가 과정에서 변수 선언문 var x가 먼저 실행
    // 변수 식별자 x는 실행 컨텍스트가 관리하는 스코프에 등록되고 undefined로 초기화
    // 소스코드 실행 과정에서 변수 할당문 x = 1만 실행
    // x 변수에 값을 할당하려면 먼저 x 변수가 선언된 변수인지 확인
    // 실행 컨텍스트가 관리하는 스코프에 등록되어 있는지 확인
    // 선언된 변수라면 값을 할당하고 할당 결과를 실행 컨텍스트에 등록하여 관리
    ```

<br>

## 23.3 실행 컨텍스트의 역할

- 실행 컨텍스트
  - 소스 코드 실행에 필요한 환경을 제공
  - 코드의 실행 결과를 실제로 관리하는 영역
  - 식별자를 등록하고 관리하는 스코프와 코드 실행 순서 관리를 구현한 내부 메커니즘
  - 모든 코드는 실행 컨텍스트를 통해 실행되고 관리
  - 식별자와 스코프는 실행 컨텍스트의 렉시컬 환경에서 관리
  - 코드 실행 순서는 실행 컨텍스트 스택으로 관리
- 설명을 위한 예시 코드

  ```jsx
  // 전역 변수 선언
  const x = 1;
  const y = 2;

  // 함수 정의
  function foo(a) {
    // 지역 변수 선언
    const x = 10;
    const y = 20;

    // 메서드 호출
    console.log(a + x + y); // 130
  }

  // 함수 호출
  foo(100);

  // 메서드 호출
  console.log(x + y); // 3
  ```

- 전역 코드 평가
  - 선언문만 먼저 실행하여 실행 컨텍스트가 관리하는 전역 스코프에 등록
  - var 변수와 함수 선언문의 전역 함수는 전역 객체의 프로퍼티 및 메서드로 등록
- 전역 코드 실행
  - 런타임이 시작되어 전역 코드가 순차적으로 실행
  - 전역 변수에 값이 할당되고 함수 호출
  - 함수가 호출되면 전역 코드 실행이 멈추고 해당 함수 내부로 진입
- 함수 코드 평가
  - 매개변수와 지역변수 선언문이 먼저 실행
  - 실행 컨텍스트가 관리하는 지역 스코프에 등록
  - 함수 내부에서 지역 변수처럼 사용할 수 있는 arguments 객체가 생성되어 지역 스코프에 등록
  - this 바인딩 결정
- 함수 코드 실행
  - 런타임이 시작되어 함수 코드가 순차적으로 실행
  - 매개변수와 지역 변수에 값이 할당
  - console.log() 메서드 호출
  - 식별자 console을 스코프 체이닝으로 검색하여 전역 객체의 프로퍼티까지 접근
  - 전역 객체 중 console 객체의 log 프로퍼티를 검색 (프로토타입 체인)
  - 인수 a, x, y를 전달하여 console.log(a + x + y) 실행
- 함수 코드 종료
  - 함수 코드 실행이 종료되고 다시 전역 코드 실행을 재개

<br>

## 23.4 실행 컨텍스트 스택

- 실행 컨텍스트는 스택 자료 구조로 관리
- 실행 중인 실행 컨텍스트(Running Execution Context)
  - 실행 컨텍스트 스택의 최상위에 존재하는 실행 컨텍스트는, 현재 실행 중인 코드의 실행 컨텍스트
- 설명을 위한 예시 코드

  ```jsx
  const x = 1;

  function foo() {
    const y = 2;

    function bar() {
      const z = 3;
      console.log(x + y + z);
    }
    bar();
  }

  foo(); // 6
  ```

  - 전역 코드의 평가와 실행: 전역 실행 컨텍스트가 생성되고, 실행 컨텍스트 스택에 푸시
  - foo 함수 코드의 평가와 실행: foo 함수가 호출되어 전역 코드 실행이 중단되고, 코드의 제어권이 foo 함수 내부로 이동 → foo 함수 실행 컨텍스트가 생성되고, 실행 컨텍스트 스택에 푸시
  - bar 함수 코드의 평가와 실행: 중첩 함수 bar가 호출되면 코드의 제어권이 bar 함수 내부로 이동 → bar 함수 실행 컨텍스트가 생성되고, 실행 컨텍스트 스택에 푸시 → console.log()가 실행되고 bar 함수는 종료
  - foo 함수 코드로 복귀: 코드의 제어권은 foo 함수로 이동 → bar 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거 → foo 함수 종료
  - 전역 코드로 복귀: 코드의 제어권은 전역 코드로 이동 → foo 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거 → 전역 코드도 종료되어 실행 컨텍스트에서 제거

<br>

## 23.5 렉시컬 환경

- 식별자와 식별자에 바인딩된 값, 상위 스코프에 대한 참조를 기록하는 자료구조
- 스코프와 식별자를 관리
  - 환경 레코드(Environment Record): 스코프에 포함된 식별자와 바인딩된 값을 관리하는 저장소
  - 외부 렉시컬 환경에 대한 참조(Outer Lexical Environment Reference): 해당 실행 컨텍스트를 생성한 소스코드를 포함하는 상위 코드의 렉시컬 환경에 대한 참조, 스코프 체이닝을 위해 필요

<br>

## 23.6 실행 컨텍스트의 생성과 식별자 검색 과정

- 설명을 위한 예시 코드

  ```jsx
  var x = 1;
  const y = 2;

  function foo(a) {
    var x = 3;
    const y = 4;

    function bar(b) {
      const z = 5;
      console.log(a + b + x + y + z);
    }
    bar(10);
  }

  foo(20); // 42
  ```

  - 1단계) 전역 객체 생성
    - 전역 코드가 평가되기 이전에 생성
    - 빌트인 전역 프로퍼티, 빌트인 전역 함수, 표준 빌트인 객체, Web API 등이 포함
    - Object.prototype을 상속받는 자식 객체
  - 2단계) 전역 코드 평가
    - 전역 실행 컨텍스트 생성: 비어 있는 전역 실행 컨텍스트가 생성되어 실행 컨텍스트 스택에 추가
    - 전역 렉시컬 환경 생성: 전역 렉시컬 환경이 생성되어 전역 실행 컨텍스트에 바인딩, 렉시컬 환경은 환경 레코드와 외부 렉시컬 환경에 대한 참조로 구성
  - 3단계) 전역 코드 실행
    - 변수 또는 함수를 실행하기 위해 현재의 실행 컨텍스트에서 식별자를 검색 (식별자 결정, Identifier Resolution)
  - 4단계) foo 함수 코드 평가
    - 전역 코드 실행을 중단하고 foo 함수 내부로 이동
  - 5단계) foo 함수 코드 실행
    - foo 함수 실행 컨텍스트의 렉시컬 환경에서 식별자를 검색, 검색된 식별자에 값을 바인딩
  - 6단계) bar 함수 코드 평가
    - 변수 z에 매개 변수 값 할당, console.log() 실행을 위해 스코프 체이닝을 통해 전역 객체에서 console 객체를 검색, console 객체의 log 메서드를 검색, console.log에 a + b + x + y+ z 값을 전달하여 최종 실행
  - 7단계) bar 함수 코드 실행 종료
    - 실행 컨텍스트 스택에서 pop 되어 제거, foo 함수 실행 컨텍스트가 다시 실행
  - 8단계) foo 함수 코드 실행 종료
    - 실행 컨텍스트 스택에서 pop 되어 제거, 전역 실행 컨텍스트가 다시 실행
  - 9단계) 전역 코드 실행 종료
    - 실행 컨텍스트 스택에서 pop 되어 제거, 실행 컨텍스트 스택은 빈 공간이 됨

<br>

## 23.7 실행 컨텍스트와 블록 레벨 스코프

- 설명을 위한 예시 코드

  ```jsx
  let x = 1;

  if (true) {
    let x = 10;
    console.log(x); // 10
  }

  console.log(x); // 1
  ```

  - if 문, for 문 등의 블록이 실행되면, 이 블록만을 위한 선언적 환경 레코드를 갖는 렉시컬 환경이 생성되고 기존의 렉시컬 환경을 교체
  - 블록이 종료되면 다시 이전의 렉시컬 환경으로 복귀
