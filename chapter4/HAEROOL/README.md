# 4주차 - 콜백 함수

# 목차

- 콜백 함수란?
- 제어권
- 콜백 함수는 함수다
- 콜백 함수 내부의 this에 다른 값 바인딩하기
- 콜백 지옥과 비동기 제어

# 콜백 함수란?

<aside>
💡 다른 코드(함수 또는 매서드)에게 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수. 콜백 함수를 위임받은 코드는 자체적인 내부 로직에 의해 콜백 함수를 적절한 시점에 실행하게 됨.

</aside>

# 제어권

## 호출 시점

```jsx
var count = 0;
var cbFunc function(){
		console.log(count);
		if(++count > 4) clearInterval(timer);
};
var imer = setInterval(cbFunc, 300);
```

위의 코드는 0.3초마다 cbFunc을 실행시키는 함수이다. 코드를 실행하면 0.3초에 한 번씩 숫자가 1씩 증가하며 출력되다가 4가 출력된 이후에 종료되게 된다. `setInterval`이라고 하는 다른 코드에 첫 번재 인자로서 `cbFunc` (콜백 함수)를 넘겨주자 제어권을 넘겨받은 `setInterval` 이 스스로의 판다에 따라 적절한 시점에 이 함수를 실행한다. 이처럼 **콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가지게 된다.**

## 인자

```jsx
Array.prototype.map(callback[, thisArg])
callback: function(currentValue, index, array) 
```

 `map` 메서드를 예로 들자면 `map` 은 첫번째 인자로  콜백 함수를 받고, 생략 가능한 두 번째 인자로 콜백 함수 내부에서 this로 인식할 대상을 특정할 수 있다. 여기서 콜백함수의 첫 번째 인자에는 배열의 요소 중 현재값이, 두 번째 인자에는 현재값의 인덱스, 그리고 세 번째 인자에는 `map` 메서드의 대상이 되는 배열 자체가 담기게 된다.

여기서 주의해야 할 것은 **currentValue나 index같은 이름의 경우 사람에 의해 명명된 것일 뿐 컴퓨터는 순서에 의해 각각을 구분하고 인식하게 된다. 이름은 관계 없다.**

<aside>
💡 콜백 함수를 호출하는 주체가 사용자가 아닌 메서드이므로 해당 메서드가 콜백 함수를 호출할 때 인자에 어떤 값들을 어떤 순서로 넘길 것인지는 전적으로 해당 메서드에 달렸다. 이처럼 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수를 호출할 때 인자에게 어떤 값들을 어떤 순서로 넘길 것인지에 대한 제어권을 가진다.

</aside>

# 콜백 함수는 함수다

<aside>
💡 콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출되게 된다.

</aside>

# 콜백 함수 내부의 this에 다른 값 바인딩하기

- 전통적인 방법
    - this를 다른 변수에게 담아 콜백 함수로 활용할 함수에서는 this 대신 그 변수를 사용하게  하고 이를 클로저로 만드는 방식
    - 하지만, 번거로움
- 아예 this를 사용하지 않으면?
    - 간결하고 직관적
    - 하지만 범용성이 떨어짐

상황에 따라서 적절하게 선택해서 사용하면 될듯

<aside>
💡 ES5부터는 bind메서드 사용하면 됨

</aside>

# 콜백 지옥과 비동기 제어

- 콜백 지옥: 콜백 함수를 익명함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 계속해서 깊어지는 현상
- 동기와 비동기
    - 동기: 실행중인 코드가 끝나야 다음 코드로 넘어감
    - 비동기: 실행중인 코드의 완료 여부와 무관하게 즉시 다음 코드 실행

### Promise

```jsx
var addCoffee = function(name){
		return function(prevName){
				return new Promise(function(resolve){
						setTimeout(function(){
								var newName = prevName ? (prevName + ', ' + name) : name;
								console.log(newName);
								resolve(newName);
						}, 500);
				});
		};
};

addCoffee('espresso')()
		.then(addCoffee('americano'))
		.then(addCoffee('cafemocha'))
		.then(addCoffee('cafelatte'));
```

new 연산자와 함께 호출한 Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행되지만, 내부의 resolve / reject 함수를 호출하는 구분이 있을 경우 둘 중 하난가 실행되기 전까지는 then(다음) 또는 오류구문으로 넘어가지 않는다. 따라서 resolve나 reject를 호출하는 방법으로 비동기 작업의 동기적  표현이 가능하다.

### Generator

```jsx
var addCoffee = function(prevName, name){
    setTimeout(function(){
        coffeeMaker.next(prevName? prevName + ', ' + name : name);
    }, 500);
};

var coffeGenerator = function*(){
    var espresso = yield addCoffee('', 'espresso');
    console.log(espresso);
    var americano = yield addCoffee(espresso, 'americano');
}

var coffeeMaker = coffeGenerator();
coffeeMaker.next()
```

Generator함수는 ‘*’를 사용하여 생성한다. 이를 실행하면 Iterator가 반환되는데, Iterator는 next라는 메서드를 갖고 있다. 이를 호출하면 Generator 함수 내부에서 가장 먼저 등장하는 yield에서 함수의 실행을 멈추고 다시 next를 호출하면 멈췄던 부분부터 다음 yield까지 실행된다.

### Async/await

```jsx
var addCoffee = function(prevName, name){
    return new Promise(function(resolve){
        setTimeout(function(){
            resolve(name);
        }, 500);
    });
};

var coffeeMaker = async function(){
    var coffeeList = '';
    var _addCoffee = async function (name){
        coffeeList += (coffeeList? ',':'') + await addCoffee(name);
    };
    await _addCoffee('espresso');
    console.log(coffeeList);
    await _addCoffee('americano');
    console.log(coffeeList);
};
coffeeMaker();
```

비동기 작업을 수행하고자 하는 함수 앞에 async를 표기하고, 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 await을 표기하는 것만으로 뒤의 내용을 Promise로 전환하고 해당 내용이 resolve된 이후에 다음으로 진행한다.
