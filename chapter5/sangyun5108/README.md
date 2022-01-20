# 05. 클로저

## 01. 클로저의 의미 및 원리 이해

- 자신을 내포하는 함수의 컨텍스트에 접근할 수 있는 함수
- 함수가 특정 스코프에 접근할 수 있도록 의도적으로 그 스코프에서 정의하는 것
- 함수를 선언할 때 만들어지는 유효범위가 사라진 후에도 호출할 수 있는 함수
- 이미 생명 주기상 끝난 외부 함수의 변수를 참조하는 함수
- 자유변수가 있는 함수와 자유변수를 알 수 있는 환경의 결합
- 로컬변수를 참조하고 있는 함수 내의 함수
- 자신이 생성될 때의 스코프에서 알 수 있었던 변수들 중 언젠가 자신이 실행될 때 사용할 변수들만을 기억하여 유지시키는 함수

### MDN
- 클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 상호관계에 따른 현상

```javascript
  var outer = function(){
    var a = 1;
    var inner = function(){
      console.log(++a);
    };
    
    inner();
  };
  
  outer();
```
- outer 함수에 변수 a 선언, outer의 내부함수인 inner 함수에 a의 값을 1만큼 증가시킨 다음 출력한다.
- inner 함수 내부에서는 a 를 선언해주지 않아서, environmentRecord에서 값을 찾지 못한다.
- outerEnvironmentReference에 지정된 상위 컨텍스트인 outer의 lexicalEnvironment에 접근해서 다시 a를 찾는다.
- a를 찾고나서 2가 출력된다.
- outer 함수 실행 컨텍스트가 종료되면 LexicalEnvironment에 저장된 식별자들(a,inner)에 대한 참조를 지운다.
- 각 주소에 저장돼 있던 값들은 자신을 참조하는 변수가 하나도 없게 되므로 가비지 컬렉터의 수집 대상이 된다.

```javascript
  var outer = function(){
    var a = 1;
    var inner = function(){
      return ++a;
    };
    return inner();
  };
  
  var outer2 = outer();
  console.log(outer2);
```
- inner 함수 내부에서 외부변수인 a를 사용
- outer 함수의 실행 컨텍스트가 종료된 시점에는 a변수를 참조하는 대상이 없어진다.
- a,inner 변수의 값들은 언제가 가비지 컬렉터에 의해 소멸된다.

- 앞서 나온 두 예제는 outer 함수의 실행 컨텍스트가 종료되기 이전에 inner 함수의 실행 컨텍스트가 종료되어 있으며, 이후 **별도로 inner 함수를 호출할 수 없다는 공통점**이 있다.

```javascript
  var outer = function(){
    var a = 1;
    var inner = function(){
      return ++a;
    };
    return inner;
  };
  
  var outer2 = outer();
  console.log(outer2()); // 2
  console.log(outer2()); // 3
```
- inner 함수의 실행 결과가 아닌 inner 함수 자체를 반환했다.
- outer 함수의 실행 컨텍스트가 종료될 때 outer2 변수는 outer의 실행 결과인 inner 함수를 참조하게 된다.
- outer2를 호출하면 앞서 반환된 함수인 inner가 실행된다.
- inner 함수의 실행 컨텍스트의 environmentRecord에는 수집할 정보가 없다.
- outer-EnvironmnetReference에는 inner 함수가 선언된 위치의 LexicalEnvironment가 참조 복사된다.(outer 함수의 LexicalEnvironment가 담긴다.)
- 이상한 점 : inner 함수 실행 시점에 outer함수는 이미 실행이 종료된 상태인데 outer 함수의 LexicalEnvironment에 어떻게 접근할 수 있을까?
- 이유 : 가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있는 경우, 그 값은 수집 대상에 포함되지 않는다.





