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
- 어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상 : 외부 함수 LexicalEnvironment가 가비지 컬렉팅 되지 않는 현상

결론 : 클로저란 어떤 함수 A에서 선안한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 **A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**

-외부에 전달이 꼭 return만 의미하지는 않는다.

return 없어도 클로저가 발생하는 경우
```javascript
//(1) setInterval/setTimeout
  (function(){
    var a = 0;
    var intervalid = null;
    var inner = function(){
      if(++a>=10){
        clearInterval(intervalid);
      }
      console.log(a);
    };
    intervaalid = setInterval(inner,1000);
  })();
```
```javascript
//(2) eventListener
  (function(){
    var count = 0;
    var button = document.createElement('button');
    button.innerText = 'click';
    button.addEventListener('click',function(){
      console.log(++count,'times clicked');
    });
    document.body.appendChild(button);
  })();
```
- (1) : window메서드에 전달할 콜백 함수 내부에서 지역변수를 참조한다.
- (2) : 별도의 외부객체인 DOM의 메서드에 등록할 handler 함수  내부에서 지역변수를 참조한다.
- (1),(2) 모두 지역변수를 참조하는 내부함수를 외부에 전달했으므로 클로저이다.

# 02. 클로저와 메모리 관리

```javascript
  //(1) return에 의한 클로저의 메모리 해제
  var outer = (function(){
    var a = 1;
    var inner = function(){
      return ++a;
    };
    return inner;
  })();
  
  console.log(outer());
  console.log(outer());
  outer = null; // outer 식별자의 inner 함수 참조를 끊는다.
```
```javascript
  (function(){
    var a = 0;
    var intervalid = null;
    var inner = function(){
      if(++a>=10){
        clearInterval(intervalid);
        inner = null; // inner 식별자의 함수 참조를 끊는다.
      }
      console.log(a);
    }
    intervalid = setInterval(inner,1000);
  })();
```
```javascript
  (function(){
    var count = 0;
    var button = document.createElement('button');
    button.innerText = 'click';
    
    var clickHandler = function(){
      console.log(++count,'times clicked');
      if(count>=10){
        button.removeEventListener('click',clickHandler);
        clickHandler = null; // clickHandler 식별자의 함수 참조를 끊음
      }
    }
    
    button.addEventListener('click',clickHandler);
    document.body.appendChild(button);
  })();
```

# 03. 클로저 활용 사례

- 이벤트 리스너에 관한 예시
```javascript
  var fruits = ['apple','banana','peach'];
  var $ul = document.createElement('ul');
  
  fruits.forEach(function(fruit){ //(A)
    var $li = document.createElement('li');
    $li.innerText = fruit;
    $li.addEventListener('click',function(){ //(B)
      alert('your choice is'+fruit);
    });
    $ul.appendChild($li);
  });
  document.body.appendChild($ul);
```
- 콜백 함수(B) : fruit이라는 외부 변수를 참조하고 있으므로 클로저가 존재한다.
- 콜백 함수 (A) : fruits의 개수만큼 실행되고, 그때마다 새로운 실행 컨텍스트가 활성화된다.
- 콜백 함수 (B) : 클릭 이벤트에 의해 (B)가 실행될때는 (B)의 outerEnvironmentReference가 (A)의 LexicalEnvironment를 참조한다.
- 참조 예정인 변수 fruit는 (A)가 종료된 후에도 GC 대상에서 제거되어 계속 참조가 가능하다.

```javascript
  var alert Fruit = function(fruit){
    alert('your choice is'+fruit);
  };
  
  fruits.forEach(function(fruit){
    var $li = document.createElement('li');
    $li.innerText = fruit;
    $li.addEventListener('click',alertFruit);
    $ul.appendChild($li);
  });
  
  document.body.appendChild($ul);
  alertFruit(fruits[1]);
```
- (B) 함수의 쓰임새가 콜백 함수에 국한되지 않는 경우라면 반복을 줄이기 위해 (B)를 외부로 분리하는 편이 좋다.(fruit를 인자로 받아 출력하는 형태)
- 공통 함수로 쓰기위해 콜백 함수를 외부로 꺼내어 alertfruit라는 변수에 담아주었다.
- 문제점 : 각 li 클릭시, 클릭한 대상의 과일명이 아닌 [object MouseEvent] 값이 출력된다 -> 콜백 함수의 인자에 대한 제어권을 addEventListener가 가진 상태이고, addEventListener는 콜백 함수를 호출할 때 첫번째 인자에 '이벤트 객체'를 주입하기 때문
- 위와 같은 문제는 **bind 메서드**를 활용해서 해결해 줄 수 있다.

```javascript
  fruits.forEach(function(fruit){
    var $li = document.createElement('li');
    $li.innerText = fruit;
    $li.addEventListener('click',alertFruit.bind(null,fruit));
    $ul.appendChild($li);
  });
```
- 이벤트 객체가 인자로 넘어오는 순서가 바뀐다.
- 함수 내부에서의 this가 원래의 그것과 달라진다.
- 두개의 달라지는 점 때문에, bind 메서드가 아닌 다른 방식(고차함수)으로 풀어주어야 한다.

```javascript
  var alertFruitBuilder = function(fruit){
    return function(){
      alert('your choice is'+fruit);
    }
  };
  
  fruits.forEach(function(fruit){
    var $li = document.createElement('li');
    $li.innerText = fruit;
    $li.addEventListener('click',alertFruitBuilder(fruit));
    $ul.appendChild($li);
  }
```
- alertFruitBuilder : 함수 내부에는 익명 함수를 반환한다.
- 클릭 이벤트 발생 -> 함수의 실행 컨텍스트가 실행되면서, alertFruitBuilder의 인자로 넘어온 fruit를 outerEnvironmentReference에 의해 참조 : 클로저 존재

### 콜백 함수 내부에서 외부변수를 참조하기 위한 방법 3가지
- 콜백 함수를 내부함수로 선언해 외부변수를 직접 참조
- bind 메서드로 값을 직접 넘겨줌
- 콜백 함수를 고차함수로 변경하여 클로저를 적극적으로 활용

### 접근 권한 제어(정보 은닉)

- 정보 은닉 : 어떤 모듈의 내부 로직에 대해 외부로의 노출을 최소화해서 모듈간의 결합도를 낮추고 유연성을 높이고자 하는 현대 프로그래밍 언어의 중요한 개념 중 하나
- 접근 권한 종류 : public, private, protected
- 클로저를 이용한 함수 차원에서 public한 값과 private한 값을 구분하는 것이 가능하다.
```javascript
  var outer = function(){
    var a = 1;
    var inner = function(){
      return ++a;
    };
    return inner;
  };
  
  var outer2 = outer();
  console.log(outer2());
  console.log(outer2());
```
- 클로저를 활용하여 외부 스코프에서 함수 내부의 변수들 중 선택적으로 일부의 변수에 대해 접근권을 부여 할 수 있다.(return을 활용)
- 외부 공간에 노출돼있는 outer라는 변수를 통해 함수 실행은 가능하지만, outer 함수 내부에는 어떠한 개입도 불가능하다.
- 오직 outer 함수가 return한 정보에만 접근할 수 있다.
- 외부에 제공하고자 하는 정보들을 모아서 return하고, 내부에서만 사용할 정보들은 return하지 않는 것으로 접근 권한 제어가 가능하다.(return 변수 : 공개멤버, 그렇지 않은 변수 : 비공개 멤버)

### 자동차 게임
```javascript
  var car = {
    fuel : Math.ceil(Math.random()*10+10), // 연료
    power : Math.ceil(Math.random()*3+2), // 연비
    moved : 0, // 총 이동거리
    run : function(){
      var km = Math.ceil(Math.random()*6);
      var wasteFuel = km / this.power;
      if(this.fuel < wasteFuel){
        console.log('이동불가');
        return;
      }
      this.fuel -= wasteFuel;
      this.moved += km;
      console.log(km+'km 이동 (총'+this.moved+'km)');
    }
  };
```
- car 변수에 객체를 직접 할당한다.
- fuel,power는 무작위 생성하고, moved라는 프로퍼티에 총 이동거리 부여한다.
- run 메서드를 실행할 때마다 car 객체의 fuel, moved값이 변하게 한다.
- car 객체를 사람 수만큼 생성해서 각자의 턴에 run을 실행하면서 게임을 즐긴다.

```javascript
  car.fuel = 10000;
  car.power = 100;
  car.moved = 1000;
```
- 위와 같은 방식으로 마음껏 값을 바꿔버리게 되면, 일방적인 게임이 된다.
- 값을 바꾸지 못하도록 방어할 필요가 있다. 클로저를 활용해서 방어가 가능하다.
```javascript
  var createCar = function(){
    var fuel = Math.ceil(Math.random()*10+10);
    var power = Math.ceil(Math.random()*3+2);
    var moved = 0;
    return {
      get moved(){
        return moved;
      },
      run : function(){
        var km = Math.ceil(Math.random()*6);
        var wasteFuel = km / power;
        if(fuel < wasteFul){
          console.log('이동불가');
          return;
        }
        fuel -= wasteFuel;
        moved += km;
        console.log(km+'km 이동 (총 ' + moved + 'km). 남은 연료: '+fuel);
      }
    };
  };
  
  var car = createCar();
```
- createCar라는 함수를 실행함으로써 객체를 생성하게 했다.
- fuel,power 변수는 비공개 멤버로 지정해 외부에서 접근을 제한했다.
- moved 변수는 getter 만을 부여함으로써 읽기 전용 속성을 부여했다.
- 오직 run 메서드를 실행하는 것과 현재 moved 값을 확인하는 두 가지 동작 할 수 있다.
- 문제점 : run 메서드를 다른 내용으로 덮어씌우는 어뷰징은 여전히 가능한 상태이다.

```javascript
  var createCar = function(){
    var publicMembers = {
      ...
    };
    
    object.freeze(publicMembers);
    return publicMembers;
  };
```
- 객체를 return 하기전에 freeze를 사용해서 미리 변경할 수 없게끔 조치를 취한다.

### 클로저를 활용해서 접근권한을 제어하는 방법
- 함수에서 지역변수 및 내부함수 등을 생성한다.
- 외부에 접근권한을 주고자 하는 대상들로 구성된 참조형 데이터(대상이 여러개일 경우, 객체 또는 배열, 하나인 경우 함수)를 return 해준다. -> return한 변수 : 공개 멤버  / 그렇지 않은 변수 : 비공개 멤버

### 부분 적용 함수
- 부분 적용 함수 : n개의 인자를 받는 함수에 미리 m개의 인자만 넘겨 기억시키고, 나중에 (n-m)개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수
```javascript
  var add = function(){
    var result = 0;
    for(var i = 0; i < arguments.length; i++){
      result += arguments[i];
    }
    return result;
  };
  var addPartial = add.bind(null,1,2,3,4,5);
  console.log(addPartial(6,7,8,9,10)); // 55
```
- addPartial 함수는 인자 5개를 미리 적용하고, 추후 추가적으로 인자들을 전달하면, 모든 인자를 모아서 원래의 함수가 실행되는 부분에 적용시킨다.
- add 함수는 this를 사용하지 않으므로, bind 메서드 만으로도 문제 없이 구현했다.
- 문제 : this의 값을 변경할 수 밖에 없기 때문에 메서드에서는 사용할 수 없다.

```javascript
  var partial = function(){
    var originalPartialArgs = arguments;
    var func = originalPartialArgs[0];
    if(typeof func !== 'function'){
      throw new Error('첫 번째 인자가 함수가 아닙니다!');
    }
    
    return function(){
      var partialArgs = Array.prototype.slice.call(originalPartialArgs,1);
      var restArgs = Array.prototype.slice.call(arguments);
      return func.apply(this,partialArgs.concat(restArgs));
    };
  };
  
  var add = function(){
    var result = 0;
    for(var i = 0; i<arguments.length;i++){
      result+=arguments[i];
    }
    return result;
  };
  
  var addPartial = partial(add,1,2,3,4,5);
  console.log(addPartial(6,7,8,9,10));
  
  var dog = {
    name : '강아지',
    greet : partial(function(prefix,suffix){
      return prefix + this.name + suffix;
    },'왈왈',')
  };
  
  dog.greet('입니다'); // 왈왈, 강아지입니다.
```
- dog 객체의 greet에서 첫 번째 인자에는 원본 함수, 두 번재 인자 이후부터는 미리 적용할 인자들을 전달한다.
- partial에서 반환할 함수에는 나머지 인자들을 받아 이들을 한데 모아(concat)원본 함수를 호출(apply)한다.
- 실행 시점의 this를 그대로 반영함으로써 this에는 아무런 영향을 주지 않게 되었다.
- 문제 : 부분 적용 함수에 넘길 인자를 반드시 앞에서부터 차례로 전달할 수 밖에 없다는 점

```javascript
  Object.defineProperty(window,'_',{
    value : 'EMPTY_SPACE',
    writable : false,
    configuralble : false,
    enumerable : false
  });
  
  var partial2 = function(){
    var originalPartialArgs = arguments;
    var func = originalPartialArgs[0];
    if(typeof func !== 'function'){
      throw new Error('첫 번째 인자가 함수가 아닙니다.');
    }
    
    return function(){
      var partialArgs = Array.prototype.slice.call(originalPartialArgs,1);
      var restArgs = Array.prototype.slice.call(arguments);
      for (var i = 0; i < partialArgs.length; i++){ // 변화한 부분
        if(partialArgs[i] === _){ // 변화한 부분
          partialArgs[i] = restArgs.shift(); // 변화한 부분
        }
      }
      
      return func.apply(this,partialArgs.concat(resArgs));
    };
  };
  
  var add = function(){
    var result = 0;
    for(var i = 0; i < arguments.length ; i++){
      result += arguments[i];
    }
    return result;
  };
  
  var addPartial = partial2(add,1,2,_,4,5,_,_,8,9);
  console.log(addPartial(3,6,7,10));
  
  var dog = {
    name : '강아지',
    greet : partial2(function(prefix,suffix){
      return prefix + this.name + suffix;
    },'왈왈,')
  };
  
  dog.greet('배고파요!');
```
- 처음에 넘겨준 인자들 중 _로 비워놓은 공간마다 나중에 넘어온 인자들이 차례대로 끼워놓도록 구현

### 결론
- 위 두 예제의 부분 적용 함수는 모두 클로저를 핵심 기법으로 사용해 주었다.
- 미리 일부 인자를 넘겨두어 기억하게끔 만들어 준뒤, 추후에 필요한 시점에 기억했던 인자들까지 함께 실행하게 해준다. -> 개념 : 클로저의 정의에 부합

### 실무에서 사용하는 디바운스 함수
- 디바운스 : 짧은 시간 동안 동일한 이벤트가 많이 발생할 경우, 이를 전부 처리하지 않고, 처음 또는 마지막에 발생한 이벤트에 대해 한 번만 처리한다.(성능 최적화)
- scroll, whell, mousemove, resize에 적용하면 좋다.
```javascript
  var debounce = function(eventName,func,wait){
    var timeoutId = null;
    return funciton(event){
      var self = this;
      console.log(eventName,'event 발생'):
      clearTimeout(timeoutId);
      timeoutId = setTimeoutId(func.bind(self,event),wait);
    };
  };
```
