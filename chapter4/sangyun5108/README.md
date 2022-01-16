# 04. 콜백함수 

## 01. 콜백 함수란?

- 콜백함수 : 다른 코드의 인자로 넘겨주는 함수
- 콜백함수는 **제어권**과 관련이 깊다.
- call(부르다,호출하다) + back(뒤돌아오다,되돌다) : 되돌아 호출해달라
- 다른 코드(함수 또는 메서드)에게 인자를 넘겨줌으로써 제어권도 함께 위임한 함수이다.

## 02. 제어권

### 호출 시점

```javascript
  var count = 0;
  var timer = setInterval(function(){
    console.log(count);
    if(++count>4)clearInterval(timer);
  },300);
```
- 0.3초마다 count 값 출력
- count가 4보다 큰경우 반복 실행 종료

***setInterval 구조***
```javascript
  var IntervalID = scope.setInterval(func,delay[,param1,param2,...]);
```
- scope : Window객체 or Worker의 인스턴스
- func(함수), delay(밀리초단위 숫자), 나머지(func함수 실행시 매개변수로 전달할 인자 : 선택사항)
- func에 넘겨준 함수는 매 delay(ms)마다 실행되고, 어떠한 값도 return하지 않는다.
- setInterval 실행시 반복적으로 실행되는 내용 자체를 특정할 수 있는 고유한 ID값이 반환된다.
- 반환하는 이유 : 반복 실행되는 중간에 종료(clearInterval)할 수 있게 하기 위해서

|code|호출 주체|제어권|
|----|------|-----|
|cbFunc();|사용자|사용자|
|setInterval(cbFunc,300);|setInterval|setInterval|


### 인자

```javascript
  var newArr = [10,20,30].map(function(currentValue,index){
    console.log(currentValue,index);
    return currentValue + 5;
  });
  
  console.log(newArr);
  
  // 10 0
  // 20 1
  // 30 2
  // [15,25,35]
```

**map 메서드**

```javascript
  Array.prototype.map(callback,[,thisArg])
  callback : function(currentValue,index,array)
```
- 첫번째 인자 : callback, 생략가능한 두번째 인자 : 콜백함수 내부에서 this로 인식할 대상
- thisArg 생략시 : 전역객체가 바인딩 된다.
- callback 함수 **첫 번째 인자 : 현재값** / **두 번째 인자 : 인덱스** / 세 번째 값 : map 메서드의 대상이 되는 배열 자체


### this 

- "콜백 함수도 함수이므로, 기본적으로는 this가 전역객체를 참조하지만, 제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다"

**map메서드 직접 구현**

```javascript
  Array.prototype.map = function(callback,thisArg){
    var mappedArr = [];
    for (var i = 0; i < this.length; i++)}{
      var mappedValue = callback.call(thisArg||window,this[i],i,this);
      mappedArr[i] = mappedValue;
    }
    return mappedArr;
  }
```
- this : thisArg 값이 있을 경우에는 그 값을, 없을 경우에는 전역객체를 지정하고, 첫 번째 인자에는 메서드의 this가 배열을 가리킬 것이므로, 배열의 i번째 요소 값을, 두 번째 인자에는 i 값을, 세번째 인자에는
배열 자체를 지정해 호출한다.
- 위 결과가 mappedValue에 담겨 mappedArr의 1번째 인자에 할당된다.
- this에 다른 값이 담기는 이유 : 제어권을 넘겨받은 코드에서 call/apply 메서드의 첫번째 인자에 콜백 함수 내부에서의 this가 될 대상을 명시적으로 바인딩하기 때문이다.


```javascript
  setTimeout(function(){console.log(this);},300); // (1) Window{...}
  
  [1,2,3,4,5].forEach(function(x){
    console.log(this);  // (2) Window{...}
  });
  
  document.body.innerHTML += '<button id = "a">클릭</button>';
  document.body.querySelector('#a')
    .addEventListener('click',function(e){console.log(this,e);})
 // (3) <button id="a">클릭</button> MouseEvent { isTrued:true, ...}
```
- (1) setTimeout의 내부에서 콜백 함수를 호출할 때 call 메서드의 첫번째 인자에 전역객체를 넘기므로, 콜백 함수 내부에서의 this가 전역객체를 가리킨다.
- (2) forEach는 별도의 인자로 this를 받는 경우에 해당하지만, 별도의 인자로 this를 넘겨주지 않아서, 전역객체를 가리키게 된다.
- (3) addEventListener는 내부에서 콜백 함수를 호출할때 call 메서드의 첫 번째 인자에 addEventListener 메서드의 this를 그대로 넘기도록 정의돼 있기 때문에, 콜백 함수 내부에서 this가
addEventListener를 호출한 주체인 HTML을 가리키게 된다.

# 03. 콜백 함수는 함수다

- 콜백 함수는 함수이다.
- 콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.

```javascript
  var obj = {
    vals : [1,2,3],
    logValues : function(v,i){
      console.log(this,v);
    }
  }
  
  obj.logValues(1,2); // {vals : [1,2,3], logValues:f} 1 2
  [4,5,6].forEach(obj.logValues);
  // Window{...} 4 0
  // Window{...} 5 1
  // Window{...} 6 2
```

- obj 객체의 logValues는 메서드로 정의되었다. 메서드의 이름 앞에 점이 있으므로 메서드로서 호출 한것이다.
- 메서드는 forEach함수의 콜백 함수로서 전달했다. obj를 this로 하는 메서드를 그대로 전달한 것이 아닌, obj.logValues가 가리키는 함수만 전달했다.
- 메서드로서의 호출할때가 아닌 한 obj와의 직접적인 연관이 없어진다.
- forEach에 의해 콜백이 함수로서 호출되고, 별도로 this를 지정하는 인자를 지정하지 않았으므로, 함수 내부에서의 this는 **전역객체**를 바라보게 된다.

결론 : 어떠한 함수의 인자에 객체의 메서드를 전달하더라도 메서드가 아닌 함수일 뿐이다.

# 04. 콜백 함수 내부의 this에 다른 값 바인딩하기

- 전통 방식 : this를 다른 변수에 담아 콜백 함수로 활용할 함수에서는 this대신 그 변수를 사용하고, 클로저로 만드는 방식

```javascript
  var obj1 = {
    name: 'obj1',
    func : function(){
      var self = this;
      return function(){
        console.log(self.name);
      };
    }
  };
  var callback = obj1.func();
  setTimeout(callback,1000);
```

- obj1.func 메서드 내부에서 self 변수에 this를 담고, 익명 함수를 선언과 동시에 반환해준다.
- obj1.func를 호출하면 앞서 선언한 내부함수가 반환되어 callback 변수에 담긴다.
- callback을 setTimeout 함수에 인자로 전달 하면 1초뒤 callback이 실행되면서, 'obj1'을 출력한다.
- 너무 번거롭다!


**함수 내부에서 this 사용 x**
```javascript
  var obj1 = {
    name:'obj1',
    func:function(){
      console.log(obj1.name);
    }
  };
  
  setTimeout(obj1.func,1000);
```
- this를 이용해 다양한 상황에 재활용 할 수 없게 되어버렸다.

**func함수 재활용**

```javascript
  var obj2 = {
    name : 'obj2',
    func : obj1.func
  };
  
  var callback2 = obj2.func();
  setTimeout(callback2,1500);
  
  var obj3 = {name:'obj3'};
  var callback3 = obj1.func.call(obj3);
  setTimeout(callback3,2000);
```

- callback2는 (obj1의 func를 복사한) obj2의 func를 실행한 결과를 담아 콜백으로 사용했다.
- callback3는 obj1의 func를 실행하면서 this를 obj3가 되도록 지정해 콜백으로 사용했다.
- 실행 시점으로 부터 1.5초 후에는 'obj2'가, 실행 시점으로부터 2초 후에는 'obj3'이 출력된다.
- 다양한 상황에서 원하는 객체를 바라보는 콜백 함수를 만들 수 있는 방법이다.

**bind메서드 활용

```javascript
  var obj1 = {
    name : 'obj1',
    func : function(){
      console.log(this.name);
    }
  };
  
  setTimeout(obj1.func.bind(obj1),1000);
  
  var obj2 = {name:'obj2'};
  setTimeout(obj1.func.bind(obj2),1500); 
```
# 05. 콜백 지옥과 비동기 제어

- 콜백 지옥 : 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상
- 비동기 : 동기의 반댓말, 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어간다.(별도의 요청, 실행 대기, 보류)
- 동기 : 현재 실행 중인 코드가 완료된 후에야 다음 코드를 실행하는 방식이다.

**콜백 지옥 예시**

```javascript
  setTimeout(function(name){
    var coffeeList = name;
    console.log(coffeeList);
    
    setTimeout(function(name){
      coffeeList += ', ' + name;
      console.log(coffeeList);
    
      setTimeout(function(name){
        coffeeList += ', ' + name;
        console.log(coffeeList);
        
          setTimeout(function(name){
            coffeeList += ', ' + name;
            console.log(coffeeList);
          },500,'카페라떼'};
        },500,'카페모카'};
      },500,'아메리카노'};
    },500,'에스프레소'};
```


**콜백 지옥 해결 - 기명함수로 변환

```javascript
  var coffeeList = '';
  
  var addEspresso = function(name){
    coffeeList = name;
    console.log(coffeeList);
    setTimeout(addAmericano,500,'아메리카노');
  }
  
  var addAmericano = function(name){
    coffeeList = name;
    console.log(coffeeList);
    setTimeout(addAmericano,500,'카페모카');
  }
  
  var addMocha = function(name){
    coffeeList = name;
    console.log(coffeeList);
    setTimeout(addLatte,500,'카페라떼');
  }
  
  var addLatte = function(name){
    coffeeList = name;
    console.log(coffeeList);
    setTimeout(addEspresso,500,'에스프레소');
  }
```

**비동기 작업의 동기적 표현**

```javascript
  new Promise(function(resolve){
    setTimeout(function(){
      var name = '에스프레소';
      console.log(name);
      resolve(name);
    },500);
  }).then(function(preName){
      return new Promise(function(resolve) {
         setTimeout(function(){
            var name = prevName + ', 아메리카노';
            console.log(name);
            resolve(name);
         },500);
      });
 }).then(function(prevName){
  return new Promise(function(resolve){
      setTimeout(function(){
        var name = prevName + ', 카페모카';
        console.log(name);
        resolve(name);
      },500);
   });
 }).then(function(prevName){
    return new Promise(function(resolve){
      setTimeout(function(){
        var name = prevName + ', 카페라떼';
        console.log(name);
        resolve(name);
      },500);
    });
 });
```

- ES6 Promise를 이용한 방식이다.
- new 연산자와 함께 호출한 Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행된다.
- 내부에 **resolve 또는 reject 함수를 호출하는 구문이 있을 경우 둘 중 하나가 실행되기 전까지 다음(then)또는 오류 구문(catch)로 넘어가지 않는다.**
- 따라서 비동기 작업이 완료될 때 비로소 resolve 또는 reject를 호출하는 방법으로 비동기 작업의 동기적 표현이 가능하다.  

**비동기 작업의 동기적 표현2**

```javascript
  var addCoffee = function(name){
    return function(prevName){
      return new Promise(function(resolve){
        setTimeout(function(){
          var newName = prevName ? (prevName + ',' + name) : name;
          console.log(newName);
          resolve(newName);
        },500);
      });
    };
  };
  
  addCoffee('에스프레소')()
      .then(addCoffee('아메리카노'))
      .then(addCoffee('카페모카'))
      .then(addCoffee('카페라떼'));
```
- 반복적인 내용을 함수화 해서 더욱 짧게 표현한 것


**비동기 작업의 동기적 표현 - Generator**
```javascript
  var addCoffee = function(prevName,name){
    setTimeout(function(){
      coffeeMaker.next(prevName?prevName+', '+name:name);
    },500);
  };
  
  var coffeeGenerator = function* (){
    var espresso = yield addCoffee('','에스프레소');
    console.log(espresso);
    var americano = yield addCoffee(espresso,'아메리카노');
    console.log(americano);
    var mocha = yield addCoffee(americano,'카페모카');
    console.log(mocha);
    var latte = yield addCoffee(mocha,'카페라떼');
    console.log(latte);
  };
  
  var coffeeMaker = coffeeGenerator();
  coffeeMaker.next();
```

- ES6의 Generator를 이용한다.
- '*'가 붙은 함수가 Generator 함수이다.
- Generator 함수 실행시 Iterator가 반환되고, Iterator는 next라는 메서드를 갖고 있다.
- next 메서드 호출시 Generator 함수 내부에서 가장 먼저 등장하는 yield에서 함수의 실행을 멈춘다.
- 다시 next 메서드를 호출하면, 앞서 멈췄던 부분부터 시작해서 그 다음 등장하는 yield 함수의 실행을 멈춘다.
- 비동기 작업이 완료되는 시점마다 next 메서드를 호출해준다면 Generaotr 함수 내부의 소스가 위에서부터 아래로 순차적으로 진행된다.

```javascript
  var addCoffee = function(name){
    return new Promise(function(resolve){
      setTimeout(function(){
        resolove(name);
      },500);
    });
  };
  
  var coffeeMaker = async function(){
    var coffeeList = '';
    var _addCoffee = async function(name){
      coffeeList += (coffeeList?',':'') + await addCoffee(name);
    };
    
    await _addCoffee('에스프레소');
    console.log(coffeeList);
    await _addCoffee('아메리카노');
    console.log(coffeeList);
    await _addCoffee('카페모카');
    console.log(coffeeList);
    await _addCoffee('카페라떼');
    console.log(coffeeList);
  };
  
  coffeeMaker();
```
- ES2017에서는 가독성이 뛰어나면서 작성법도 간단한 새로운 기능인 async/await가 추가되었다.
- 비동기 작업을 수행하고자 하는 함수 앞에 async를 표기하고, 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 await을 표기해준다.
- 위와 같이 표기하면 뒤의 내용을 Promise로 자동 전환해주고, 해당 내용이 resolve된 이후에 다음으로 진행된다.
- Promise의 then과 흡사한 효과를 얻을 수 있다.

# 06.정리

- 콜백 함수는 다른 코드에 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수이다.
- 제어권을 넘겨받은 코드는 다음과 같은 제어권을 가진다.
1. 콜백 함수를 호출하는 시점을 스스로 판단해 실행한다.
2. 콜백 함수를 호출할때 인자로 넘겨줄 값들 및 그 순서가 정해져 있다. 이 순서를 따르지 않고 코드를 작성하면 엉뚱한 결과를 얻게 된다.
3. 콜백 함수의 this가 무엇을 바라보도록 할지가 정해져 있는 경우도 있다. 정하지 않은 경우에는 전역객체를 바라본다.사용자가 임의로 this를 바꾸고 싶은 경우에는 bind메서드를 활용하면 된다.
4. 어떤 함수에 인자로 메서드를 전달하더라도 이는 결구 함수로서 실행된다.
5. 비동기 제어를 위해 콜백 함수를 사용하다 보면 콜백 지옥에 빠지게 된다 -> Promise,Generator,async/await을 이용하면 콜백 지옥에서 벗어날 수 있다.
