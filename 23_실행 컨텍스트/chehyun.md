### 23.1 소스코드의 타입

- **4가지 타입**의 소스코드는 실행 컨텍스트를 생성함 → 소스코드 타입에 따라 생성 과정과 관리 내용이 다름
    - 전역 코드 : 전역에 존재하는 소스코드를 말함 (전역에 정의된 함수, 클래스 등 내부 코드는 포함 안함)
    - 함수 코드 : 함수 내부에 존재하는 소스코드를 말함 (함수 내부에 중첩된 내부는 포함 안함)
    - eval 코드 : eval 함수에 인수로 전달되어 실행되는 코드를 말함
    - 모듈 코드 : 모듈 내부에 존재하는 소스코드를 말함
- **전역 코드**
    - 전역 변수를 관리하기 위해 최상위 스코프인 전역 스코프를 생성
    - 전역 변수와 전역 함수를 전역 객체의 프로퍼티와 메서드로 바인딩하기 위해 **전역 객체와 연결**
    - 전역 코드가 평가되면 전역 실행 컨텍스트가 생성됨
- **함수 코드**
    - 지역 스코프를 생성 (지역 변수, 매개 변수, arguments 객체 관리)
    - 생성한 지역 스코프를 전역 스코프에 연결해야 함
    - 함수 코드가 평가되면 함수 실행 컨텍스트가 생성됨
- **eval 코드**
    - strict mode에서 독자적인 스코프를 생성함
    - eval 코드가 평가되면 eval 실행 컨텍스트 생성
- **모듈 코드**
    - 모듈별로 독자적인 모듈 스코프를 생성함

### 23.2 소스코드의 평가와 실행

- 자바스크립트 엔진은 **소스코드의 평가**와 **소스코드의 실행**으로 나누어 처리
- 소스코드 평가
    - 실행 컨텍스트를 생성하고 생성된 변수나 함수 식별자를 키로 실행 컨텍스트가 관리하는 스코프에 등록
- 소스코드 실행
    - 런타임임
    - 실행에 필요한 정보(변수, 함수)를 실행 컨텍스트가 관리하는 스코프에서 검색해서 갖고옴
    - 변수 값 변경 등 소스코드 실행 결과는 다시 스코프에 등록된다
    
    ```jsx
    var x;
    x = 1;
    ```
    
    - 소스코드 평가:
    변수 선언문 var x를 먼저 실행해 식별자 x를 실행 컨텍스트가 관리하는 스코프에 등록하고 undefined로 초기화 한다.
    - 소스코드 실행:
        
        var x 는 이미 코드 평가 과정에서 실행 완료 하였으니, 변수 할당문 x = 1만 실행된다. 이때의 변수에 값을 할당 하기 위해 먼저 변수가 선언되었는지 확인한다. 등록 되어 있으니 값을 할당하고 할당 결과를 실행 컨텍스트에 등록하여 관리한다.
        

### 23.3 실행 컨텍스트의 역할

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
    - 선언문만 먼저 실행한다 (const x, const y)
    - 생성된 전역 변수, 전역 함수가 전역 스코프에 등록된다
    - 이때 var 키워드로 등록한 전역 변수는 객체의 프로퍼티, 전역 함수는 메서드가 된다
- 전역 코드 실행
    - 평가가 끝나면 런타임이 시작되어 코드가 순차적으로 실행됨
    - 변수에 값이 할당되고 함수가 호출됨 (x = 1; y=1;)
    - 함수가 호출되면 전역코드 실행 **중단** 코드 실행 순서를 변경하여 **함수 내부로 진입**
- 함수 코드 평가
    - 함수 내부로 진입했으니, **함수 코드 평가 과정**을 거치자
    - 매개변수 a와 지역 변수 선언문 const x; const y; 먼저 실행 후 스코프에 등록됨
    - arguments 객체가 생성되어 스코프에 등록되고, this 바인딩도 결정
- 함수 코드 실행
    - 평가가 끝나면 런타임이 시작되어 코드가 **순차적으로 실행된다**
    - 매개변수와 지역변수에 값이 할당되고 console.log 메서드가 호출된다
    - console 식별자는 **스코프 체인**을 통해 검색 (현재 스코프에 없으면 상위로 올라가서 검색)
    - 함수가 끝나면, 함수 호출 이전으로 돌아가서 전역 코드 실행을 계속한다

> 코드가 실행되려면 스코프, 식별자, 코드 실행 순서 등 관리가 필요함

- 식별자 스코프를 구분하여 등록, 값이 변하는 것을 지속적으로 관리해야 함
- 스코프는 스코프 체인을 형성해야 함 (그래야 상위 스코프로 타고 이동하며 검색할 수 있음)
- 현재 실행 중인 코드의 실행 순서를 변경할 수 있어야 하고, 이를 기억하고 다시 되돌아갈 수도 있어야 함
> 
- 이 모든 걸 관리하는 것이 실행 컨텍스트임
    - 소스코드를 실행하는 데 필요한 환경을 제공하고 코드의 실행 결과를 실제로 관리하는 영역
    

### 23.4 실행 컨텍스트 스택

예제를 보쟈

```jsx
const x = 1;

function foo () {
  const y = 2;

  function bar () {
    const z = 3;
    console.log(x + y + z);
  }
  bar();
}

foo(); // 6
```

- 전역 코드 + 함수 코드
    - 전역 실행 컨텍스트를 생성하고, 함수가 호출되면 함수 실행 컨텍스트를 생성
    - 이떄 생성된 함수 실행 컨텍스트는 스택으로 관리함 (실행 컨텍스트 스택)
    - 시간의 흐름에 따라 실행 컨텍스트가 추가되고 제거됨
    
    ![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/009a30ff-1579-476d-a248-96bf29a9a004/1d61305a-841d-45ac-ac36-29f069274111/image.png)
    
- **전역 코드의 평가와 실행**
    - 먼저 전역 코드를 평가해서 실행 컨텍스트를 생성하고 스택에 푸시함
    - 전역변수 x와 전역 함수 foo가 실행 컨텍스트에 등록됨
- **foo 함수의 코드의 평가와 실행**
    - 전역 함수 foo가 호출되면 전역 코드 실행 일시중지 시키고 코드 제어권이 foo 함수 내부로 이동
    - 자바스크립트 엔진은 foo 함수 내부 코드를 평가하고 함수 실행 컨텍스트를 등록
    - 이 때 foo 함수의 지역변수 y와 중첩 함수 bar가 foo 함수 실행 컨텍스트에 등록됨
- **bar 함수 코드의 평가와 실행**
    - bar 함수가 호출되면 함수 코드 일시중지 시키고 코드 제어권이 bar 함수 내부로 이동
    - 자바스크립트 엔진은 bar 함수 내부 코드를 평가하고 함수 실행 컨텍스트 생성
    - bar 함수의 지역 변수 z가 컨텍스트에 등록됨
- **foo 함수 코드로 복귀**
    - bar 함수가 종료되면 코드 제어권은 다시 foo 함수로 이동된다
    - 이때 bar 함수 실행 컨텍스트를 실행 **컨텍스트 스택에서 제거**한다
- **전역 코드로 복귀**
    - foo 함수가 종료되면 코드 제어권은 다시 전역 코드로 이동한다
    - 이때 foo 함수 실행 컨텍스트를 실행 **컨텍스트 스택에서 제거**한다
- **요약**
    - 실행 컨텍스트 스택은 **코드 실행 순서를 관리**
    - 실행 컨텍스트 스택 최상위에 존재하는 실행 컨텍스트는 언제나 **실행 중인 코드의 실행 컨텍스트**임

### 23.5 렉시컬 환경

- 식별자와 식별자에 바인딩된 값, 그리고 참조를 기록하는 자료구조임
- 실행 컨텍스트 스택이 실행 순서를 관리하면, 렉시컬 환경은 **스코프와 식별자를 관리한다**
- 렉시컬 환경
    - 객체 형태의 스코프를 생성하여 식별자를 키로 등록하고, 식별자에 바인딩된 값을 관리
    - 렉시컬 환경은 식별자를 등록하고 관리하는 저장소 역할을 함
- 환경 레코드
    - 스코프에 포함된 식별자를 등록하고, 등록된 식별자 바인딩 값을 관리하는 저장소
    - 소스코드 타입에 따라 관리하는 내용이 달라짐
- 외부 렉시컬 환경에 대한 참조
    - 상위 스코프를 가리킴
    - 즉, 해당 실행 컨텍스트를 생성한 소스코드를 포함하는 **상위 코드의 렉시컬 환경을 말함**
    - 이 참조를 통해 단방향 링크드 리스트인 **스코프 체인을 구현**

### 23.6 실행 컨텍스트의 생성과 식별자 검색 과정

예제를 통해 과정을 보쟈

```jsx
var x = 1;
const y = 2;

function foo (a) {
  var x = 3;
  const y = 4;

  function bar (b) {
    const z = 5;
    console.log(a + b + x + y + z);
  }
  bar(10);
}

foo(20); // 42
```

### 23.6.1 전역 객체 생성

- 전역 객체는 전역 코드가 평가되기 이전에 생성된다
- 전역 객체에는 빌트인 전역 프로퍼티, 전역 함수, 호스트 객체를 포함된다
- 전역 객체도 프로토타입 체인의 일원임

### 23.6.2 전역 코드 평가

- 전역 실행 컨텍스트 생성
- 전역 렉시컬 환경 생성
    - 전역 환경 레코드 생성
        - 객체 환경 레코드 생성
        - 선언적 환경 레코드 생성
    - this 바인딩
    - 외부 렉시컬 환경에 대한 참조 결정

이 순서대로 전역 코드 평가가 이루어진다

- **전역 실행 컨텍스트 생성**
    - 전역 실행 컨텍스트를 생성하여 실행 컨텍스트 스택에 푸시한다
- **전역 렉시컬 환경 생성**
    - 전역 렉시컬 환경을 생성하고 전역 실행 컨텍스트에 바인딩한다
    - 전역 환경 레코드는 전역 변수와 함수, 전역 객체의 프로퍼티 등을 관리하며, 코드가 실행될 때 필요한 전역 스코프를 제공
    - 예를 들어, 어떤 스크립트가 실행될 때, **전역 환경 레코드**는 해당 스크립트에서 선언된 모든 전역 변수를 추적하고, 필요할 때 이들 변수를 검색하고 접근할 수 있게 함
    - 이런 전역 환경 레코드는 **객체 환경 레코드**와 **선언적 환경 레코드**로 구성되어 있음
    - 객체 환경 레코드
        - var 키워드로 선언한 전역 변수, 전역 함수, 전역 프로퍼티, 빌트인 전역 함수, 표준 빌트인 객체 관리
    - 선언적 환경 레코드
        - let, const 키워드로 선언한 전역 변수를 관리
    - 즉, 객체 환경 레코드 + 선언적 환경 레코드는 협력하여 전역 스코프와 객체를 관리함
- **전역 환경 레코드 생성**
    - 객체 환경 레코드는 BindingObject라고 부르는 전역 객체와 연결됨
    - var 전역 변수와 전역 함수는 객체 환경 레코드에 연결된 BindingObject를 통해 전역 객체의 프로퍼티와 메서드가 됨
    - 이것이 전역 변수와 전역 함수가 전역 객체를 가리키는 window 식별자 없이 전역 객체의 프로퍼티를 참조할 수 있는 메커니즘임
    - 자바스크립트 엔진이 코드를 처리할 때, 실제로는 이렇게 동작함
        1. **변수 선언 단계**: 자바스크립트 엔진이 코드를 실행하기 전에, `var x;` 부분이 스코프의 맨 위로 끌어올려짐 이 시점에서는 `x`가 선언되었지만, 값이 할당되지 않아서 `undefined`로 초기화됨
            
            ```jsx
            javascriptCopy code
            var x;
            console.log(x); // undefined
            x = 5;
            console.log(x); // 5
            ```
            
        2. **코드 실행 단계**: 이제 코드가 실행되면서 `console.log(x)`는 `x`가 선언된 상태이므로 오류 없이 `undefined`를 출력함 이후 `x`에 `5`가 할당되고, 다음 `console.log(x)`는 `5`를 출력
    - let, const로 선언한 전역 변수는 선언적 환경 레코드에 등록되고 관리됨
    - 전역변수 y가 let, const로 선언되었다면, 전역 객체의 프로퍼티가 아니기 때문에 window.y 안됨
    - const 키워드 변수는 선언 단계와 초기화 단계가 분리되어 진행됨
        - 그래서 런타임에 실행 흐름이 변수 선언문에 도달하기 전까지 일시적 사각지대 TDZ에 빠짐
    - let, const도 변수 호이스팅이 발생하지만, 런타임에 컨트롤이 변수 선언문에 도달하기까지 TDZ에 빠지기 때문에 참조할 수 없는 거임
- **this 바인딩**
    - 전역 환경 레코드의 `[[GlobalThisValue]]` 내부 슬롯에 `this`가 바인딩된다
    - 참고로 전역 환경 레코드를 구성하는 *객체 환경 레코드*와 *선언적 환경 레코드*에는 `this`바인딩 없음
    - `this`바인딩은 *전역 환경 레코드*와 *함수 환경 레코드*에만 존재함
- **외부 렉시컬 환경에 대한 참조 결정**
    - 현재 평가 중인 소스코드를 포함하는 외부 소스코드의 렉시컬 환경, 즉 상위 스코프를 가리킴
    - 이를 통해 스코프 체인을 구현함
    - 근데 지금 평가 중인 소스코드는 전역 코드임
    - 그래서 전역 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에는 null이 할당됨
    - 이는 전역 렉시컬 환경이 스코프 체인의 최상위!!임을 의미함

### 23.6.3 전역 코드 실행

- 변수 할당문/함수 호출문을 실행하려면 변수, 함수가 선언됐는지 확인해야 함
- 식별자는 스코프가 다르면 이름이 같아도 됨
- 그래서 어느 스코프의 식별자를 참조해야될지 결정해야 됨 → **식별자 결정**
- 식별자 결정을 위해 실행 중인 실행 컨텍스트에서 식별자를 검색한다
    - 선언된 식별자는 실행 컨텍스트의 환경 레코드에 등록되어 있음
- 현재 실행 중인 실행 컨텍스트는 전역이므로 전역 렉시컬 환경에서 변수들을 검색한다
    - 만약 실행 중인 컨텍스트에서 식별자를 검색할 수 없으면, 이는 식별자가 선언되지 않았다는 소리임
    - 그러면 외부 렉시컬에 대한 참조가 가리키는, 즉 상위 스코프에서 식별자를 검색한다
    - 이게 스코프 체인의 동작 원리임
- 근데 전역 렉시컬 환경은 스코프 체인의 종점이기 때문에 여기서 검색이 안되는 식별자는 참조 에러가 남

### 23.6.4 foo 함수 코드 평가

- foo 함수가 호출되면 전역 코드의 실행을 일시 중단하고 foo 함수 내부로 코드 제어권이 이동함
- 함수 코드 평가는
    - 함수 실행 컨텍스트 생성
    - 함수 렉시컬 환경 생성 (함수 환경 레코드 생성, this 바인딩, 외부 렉시컬 환경 참조 결정)
- 함수 실행 컨텍스트 생성
    - foo 함수 실행 컨텍스트를 생성한다
    - 생성이 완료되면 실행 컨텍스트 스택에 푸시된다
- 함수 렉시컬 환경 생성
    - foo함수 렉시컬 환경을 생성하고 foo 함수 실행 컨텍스트에 바인딩 한다
    - 렉시컬 환경(환경 레코드 + 외부 렉시컬 환경에 대한 참조)
- 함수 환경 레코드 생성
    - 매개변수, arguments 객체, 함수 내부의 지역변수, 중첩 함수를 등록하고 관리함
- this 바인딩
    - 함수 환경 레코드의 [[ThisValue]] 내부 슬롯에 this가 바인딩 됨
    - 함수 호출 방식에 따라 this가 결정되는데 foo 함수는 일반 함수로 호출되었으므로 this는 전역 객체임
    - 그래서 foo에서 this를 쓰면 전역 객체의 thisvalue 값이 바인딩 됨
- 외부 렉시컬 환경에 대한 참조 결정
    - foo 함수가 평가된 시점에 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 할당된다
    - foo 함수는 전역 코드에 정의된 전역 함수임
        - 그래서 foo 함수 정의는 전역 코드 평가 시점에 평가됨
        - 이 시점의 실행 중인 실행 컨텍스트는 전역 실행 컨텍스트임
        - 따라서 외부 렉시컬 환경에 대한 참조는 전역 렉시컬 환경의 참조가 할당됨
    - 자바스크립트는 함수의 상위 스코프를 함수 객체의 내부 슬롯에 저장한다
    - 그래서 외부 렉시컬 환경에 대한 참조에 할당되는 것은 상위 스코프를 가리키는 함수 객체의 내부 슬롯에 저장된 렉시컬 환경의 참조임
        - 이 말은, 함수가 선언될 당시의 스코프를 기억하고, 그 함수가 나중에 호출되더라도 그 당시의 스코프에 접근할 수 있도록 한다는 뜻임

### 23.6.5 foo 함수 코드 실행

- 이제 런타임이 시작되어 foo 함수 소스코드가 순차적으로 실행됨
- 식별자 결정을 위해 렉시컬 환경에서 식별자를 검색하기 시작한다
- 만약 실행 중인 실행 컨텍스트의 렉시컬 환경에서 식별자가 검색이 안되면 외부 렉시컬 환경에 대한 참조가 가리키는 렉시컬로 이동해서 식별자를 검색한다
    - 뭔 말이냐면, 걍 여기서 검색 안되면 위로 올라가서 검색한다는 거임
- 검색된 식별자에 값을 바인딩 한다

### 23.6.6 bar 함수 코드 평가

- bar 함수가 호출되면 제어권이 bar 함수 내부로 이동한다
- 그리고 bar 함수 코드를 평가하기 시작한다
- 이 실행 컨텍스트와 렉시컬 환경 생성 과정은 위의 foo 함수와 동일함

### 23.6.7 bar 함수 코드 실행

- 이제 런타임이 시작되어 bar 함수 소스코드가 순차적으로 실행됨
- 매개 변수에 인수가 할당되고, 변수 할당문이 실행됨
- 그리고 console.log(a+b+x+y+z);가 실행됨
    - 우선 console.log 식별자를 스코프 체인에서 검색한다
        - 다시 말하지만 스코프 체인은 현재 실행 중인 컨텍스트 렉시컬 환경 → 외부 렉시컬 환경에 대한 참조로 이어지는 렉시컬 환경의 연속임
        - 쉽게 말해 지금 함수 → 상위 함수 → 최상위 함수 이런 구조임
    - 지금 실행 중인 실행 컨텍스트는 bar 함수 실행 컨텍스트임
    - 그래서 bar 렉시컬 환경에서 console 식별자를 검색하는데, 여기엔 console 식별자가 없음
        - 그럼 위로 올라가서 또 검색해야겠죠?
        - 쭉쭉쭉쭉 위로 올라가서 검색하다 보니까 최상위 환경인 전역 렉시컬 환경까지 오게되었음
        - 오잉 근데 전역 객체 환경 레코드의 전역 객체에서 찾았어!
    - 그럼 이제 console 식별자에 바인딩된 객체 log를 찾아야 해
        - console 객체의 프로토타입 체인에 log 메서드를 검색한다
        - log 메서드는 상속된 프로퍼티가 아닌, console 객체가 직접 소유하는 프로퍼티임
    - 그럼 이제 진짜 console.log를 찾았음
        - 이제 인수, a+b+x+y+z 표현식을 평가하기 위해 5개의 식별자를 검색하자
        - 식별자는 스코프 체인에서 검색하면 된다
        - a, x, y는 foo에서, b, z는 bar에서 검색된다
        - 얘네를 생성하면 42가 된다
    - 42를 console.log에 전달해서 호출하면 끝!!!!!!

 

### 23.6.8 bar 함수 실행 종료

- console.log 메서드가 호출되고 종료되면 더 이상 실행할 코드가 없음 → bar 함수 종료!
- 이때 실행 컨텍스트 스택에서 bar 함수 실행 컨텍스트는 **pop되어 제거되고,** 실행 중인 컨텍스트는 최상위에 위치하게 된 foo 실행 컨텍스트가 된다
- 실행 컨텍스트 스택에서 bar 함수 컨텍스트가 없어졌다고 해서 렉시컬 환경까지 **즉시 소멸하는 것은 아님**
- 렉시컬은 독립적인 객체임
    - 객체를 포함한 모든 값은 **누군가에 의해 참조되지 않을 때** 비로소 가비지 컬렉터에 의해 메모리 공간이 해제되어 소멸한다
    - bar 실행 컨텍스트가 소멸되었더라도, bar 함수 렉시컬 환경을 누군가 참조하고 있으면 소멸 X

### 23.6.9 foo 함수 실행 종료

- bar 함수가 종료하면 더 이상 실행할 코드가 없으므로 foo 함수가 종료된다
- 이때 실행 컨텍스트 스택에서 foo 함수 컨텍스트가 pop되어 제거되고, 실행 중인 컨텍스트는 최상위에 위치하게 된 전역 실행 컨텍스트가 된다

### 23.6.10 전역 코드 실행 종료

- foo 함수가 종료되면 더 이상 실행할 전역 코드가 없으므로 전역 코드 실행도 종료된다
- 전역 실행 컨텍스트도 실행 컨텍스트 스택에서 pop되어 실행 컨텍스트 스택에는 **아무것도 없게 된다**

### 23.7 실행 컨텍스트와 블록 레벨 스코프

- var 변수는 함수의 코드 블록만 지역 스코프로 인정하는 **함수 레벨 스코프**임
- let, const 변수는 모든 코드 블록(if, while, for, try/catch)을 인정하는 **블록 레벨 스코프**임

```jsx
let x = 1;

if(true) {
	let x = 10;
	console.log(x); // 10
}

console.log(x); // 1
```

- if문 코드 블록 안에서 let으로 변수가 선언되었음
    - 그래서 if문의 코드 블록이 실행되면, if문을 위한 블록 레벨 스코프를 생성해야 함
    - 이를 위해 렉시컬 환경을 새롭게 생성해서 기존 전역 렉시컬 환경을 교체한다
- 이는 if문 뿐만 아니라 블록 레벨 스코프를 생성하는 **모든 블록문에 적용된다**
- 예를 들어 let 키워드를 사용한 for문이면
    - 코드 블록이 반복해서 실행될 때마다 코드 블록을 위한 새로운 렉시컬 환경을 생성함
    - 이 함수의 상위 스코프는 for문의 코드 블록이 반복해서 실행될 때마다 식별자의 값을 유지해야 함
        - for 문의 변수 선언 혹은 지역 변수 등
    - 이를 위해 for 문이 실행될 때마다 독립적인 렉시컬 환경을 생성해야 하는데, 이는 클로저에서 살펴보자
