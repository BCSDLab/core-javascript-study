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























