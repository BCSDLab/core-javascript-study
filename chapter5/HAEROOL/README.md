# 5주차-클로저(1)

# 목차

- 클로저의 의미 및 원리 이해
- 클로저와 메모리 관리

# 클로저의 의미 및 원리 이해

<aside>
💡 클로저란? **어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수  B를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**

</aside>

```jsx
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

 외부 함수의 변수를 참조하는 내부함수의 예시이다. outer함수 내에서 inner함수 자체를 반환하고 있다. outer 함수의 실행 컨텍스트가 종료될 때 outer2 변수는 outer의 실행 결과인 inner함수를 참조하게 되고, 이후 outer2를 호출하면 반환된 함수인 inner가 실행되게 된다.

 inner 함수의 실행 컨텍스트의 environmentRecord에는 수집할 정보가 없다, outerEnvironmentReference에는 inner 함수가 선언된 위치의 LexicalEnvironment가 참조복사된다. inner 함수는 outer 함수 내부에서 선언됐으므로, outer 함수의 LexicalEnvironment가 담긴다.

 여기서 inner함수의 실행시점에 실행이 종료된 outer함수의 LexicalEnvironment에 접근할 수 있는 이유는 어떤 값을 참조하는 변수가 하나라도 있다면 수집대상에 포함시키지 않는 가비지 컬렉터의 특성 때문이다. outer함수는 실행 종료 시점에 inner 함수를 반환한다. 외부함수인 outer의 실행이 종료되더라도 내부 함수인 inner 함수는 outer2를 실행함으로써 호출된 가능성이 생기게 된다. inner 함수의 실행 컨텍스트가 활성화되면 outerEnvironmentReference가 outer 함수의 LexicalEnvironment를 필요로 할 것이므로 수집대상에서 제외되기 때문에 inner함수에 접근이 가능하다.

<aside>
💡 외부로의 전달이 return만을 의미하지는 않는다

</aside>

```jsx
(function(){
		var count = 0;
		var button - document.createElement('button');
		button.innerText = 'click';
		button.addEventListener('click', function(){
				console.log(++count, 'times clicked');
		});
		document.body.appendChild(button);
})();
```

별도의 외부객체인 DOM의 메서드에 등록할 handler 함수 내부에서 지역변수를 참조한다. 지역변수를 참조하는 내부함수를 외부에 전달했기 떄문에 클로저이다.

# 클로저와 메모리 관리

클로저는 어떤 필요에 의해 의도적으로 함수의 지역 변수를 메모리를 소모하도록 함으로써 발생하게 된다. 따라서 참조 카운트를 0으로 만들어 가비지 컬렉터가 수거하도록 하면 된다.

식별자에 참조형이 아닌 기본형 데이터 (보통 null이나 undefined)를 할당하면 된다.

```jsx
var outer = (function(){
		var a = 1;
		var inner = function(){
				return ++a;
		}
		return inner;
})();
console.log(outer());
console.log(outer()0;
outer = null;             // outer 식별자의 inner 함수 참조를 끊음
```

```jsx
(function(){
		var count = 0;
		var button = document.createElemet('button');
		button.innerText = 'click';
		
		var clickHandler = function(){
				console.log(++count, 'times clicked');
				if(count>=10){
						button.removeEventListener('click', clickHandler);
						clickHandler = null;    //clickHandler 식별자의 함수 참조를 끊음
				}
		}
};
```
