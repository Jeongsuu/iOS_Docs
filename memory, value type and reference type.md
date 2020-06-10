# 💉 Memory Basics
운영체제는 프로그램이 실행될 때마다 프로그램이 사용할 메모리를 할당한다. 이 공간은 사용 용도에 따라서 다음과 같이 크게 4가지로 구분된다. 

![memory](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggVERcbkFbQ29kZV1cbkJbRGF0YV1cbkNbSGVhcF1cbkRbU3RhY2tdIiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

  
    
`Code` 영역에는 기계어로 번역된 프로그램 코드가 저장된다. `Data`영역에는 정적변수와 전역변수가 저장된다. 여기에 저장된 데이터들은 프로그램이 시작될 때 저장되었다가 프로그램이 종료될 때 사라진다.  `Stack`에는 지역변수, parameter, return값 등이 저장된다. Stack은 함수호출과 밀접한 관련이 있다. 함수가 실행되면 함수에 필요한 공간들이 생성된다. 이것을 Stack Frame이라고 한다. Stack Frame은 함수가 종료되면 자동으로 제거되고 다른 함수가 사용할 수 있게 된다. `Heap`은 동적으로 할당된 데이터가 저장된다. 동적으로 할당되는 특성때문에 공간의 크기를 예측하기 어렵다. Heap에 저장되는 데이터는 생성시점과 제거시점이 정해져있지 않다. 그래서 자동으로 생성되거나 제거되지 않고 코드를 통해 직접 생성하거나 제거해야한다. 사용하지 않는 데이터를 제거하지 않는다면 프로그램이 종료될 때까지 유지되고 이런 데이터가 많다면 결국 메모리가 부족하게 된다. 더이상 필요하지 않는 데이터가 제거되지 않는 것을 메모리 누수라고 한다.

  
![vs](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggVERcbnZhbHVlKFtWYWx1ZSBUeXBlXSktLT5TWyhTdGFjayldXG5yZWZlcmVuY2UoW1JlZmVyZW5jZSBUeXBlXSktLT5IWyhIZWFwKV0iLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)
  
    
값 형식(Value Type)은 Stack에 저장된다. 더이상 사용되지 않는 경우 자동으로 제거되기 때문에 메모리 관리가 쉽다. 반면 참조 형식(Reference Type)은 실제 값을 Heap에 저장하고 Heap 메모리 주소를 Stack에 저장한다. 실제 값에 접근하려면 항상 Stack에 있는 주소를 통해 접근한다. 따라서 이 값들을 잘 지워서 메모리 누수를 방지해야한다.

# 🗂 Value Types and Reference Types

값형식과 참조형식의 메모리 저장방식 차이를 자세히 알아보자. 스위프트에서 구조체, 열거형, 튜플은 값 형식이고 클래스와 클로저는 참조형식이다. 

|Value Type|Reference Type|
|--|--|
|Structure <br> Enumeration <br> Tuple | Class <br> Closure |

많이 사용하는 클래스와 구조체로 예를 들어보겠다.

### 구조체
```
struct SizeValue {
	var width = 0.0
	var height = 0.0
}
var value = SizeValue()
```
`value` 인스턴스를 생성하면 Stack에 메모리 공간이 생성되고 여기에는 0.0으로 초기화된 값들이 저장된다. 그리고 `value`변수와 메모리공간이 연결된다. 이번에는 새로운 변수를 선언하고 초기값을 `value`로 저장해보자.

```
var value2 = value
```
이 경우 값형식은 값이 복사된다. value의 복사본이 새로운 공간에 저장되고 이 공간과 value2가 연결된다. 즉 value와 value2는 각각의 인스턴스이다.

```
value2.width = 1.0
value2.height = 2.0
```
value2의 값을 변경해보자. value2의 값이 바꼈지만 value의 값은 바뀌지 않는다. **값형식**은 **Stack**에 저장된다. 그리고 값을 전달할때마다 새로운 복사본이 전달된다. 
```
                Stack
            +------------+
 Value +--> | width:0.0  |
            | height:0.0 |
            +------------+
            |            |
            |            |
            +------------+
Value2 +--> | width:0.0  |
            | height:0.0 |
            +------------+
            |            |
            |            |
            +------------+
```
### 클래스
 이제 참조형식을 보자.

```
class SizeObject {
	var width = 0.0
	var height = 0.0
}
var object = SizeObject()
```
`object` 인스턴스를 생성하면 Stack과 Heap에 새로운 공간이 생긴다. **Heap**에는 새로운 **인스턴스**가 저장되고 **Stack**에는 **Heap의 메모리 주소**가 저장된다. 그리고 `object` 변수는 Stack에 생성된 메모리공간에 연결된다. 값 형식과 달리 메모리에 바로 접근할 수 없고 항상 Stack을 거쳐서 접근하게 된다. 

```
var object2 = object
```
새로운 인스턴스 `object2`를 생성하고 초기값을 `object`로 저장해보자. 그러면 Stack에 새로운 메모리공간이 생성되고 여기에는 `object`에 연결된 주소가 그대로 복사된다. Heap에서는 새로운 공간이 생기거나 인스턴스가 복사되지 않는다. 어떤 변수로 접근하더라도 결국 Heap에 저장된 인스턴스에 도달한다.
```
object2.width = 1.0
object2.height = 2.0
```
이번에는 값형식에서와 달리 object와 object2의 값이 모두 변경되었다. 참조형식은 Heap에 인스턴스를 저장하고 Stack에 메모리주소를 저장한다. 값을 전달할때마다 인스턴스 복사본이 전달되지않는다. 대신 Stack에 저장되어 있는 주소가 복사된다. 
```
                Stack                   Heap
            +------------+         +------------+
            |            |         |            |
            |            |         |            |
            +------------+         +------------+
 object+--> |   0x1234   +-------> | width:0.0  |
            |            |     +-->+ height:0.0 |
            +------------+     |   +------------+
object2+--> |   0x1234   +-----+   |            |
            |            |         |            |
            +------------+         +------------+
            |            |         |            |
            |            |         |            |
            +------------+         +------------+

```
참조를 전달한다와 참조를 복사한다라는 말에서 참조는 Stack에 저장되어 있는 메모리 주소를 말한다. 동일한 인스턴스에 접근하는 주소가 복사되기 때문에 주소를 수백번 복사하더라도 동일한 인스턴스에 접근하게 된다.

### let keword
이제 `let` keyword를 메모리 관점에서 보자. 
```
struct SizeValue {
	var width = 0.0
	var height = 0.0
}
let v = SizeValue()
v.width = 1.0 //Error
```
let keyword는 값 형식에 특별한 제한을 더해준다. 값 형식의 인스턴스를 다음과 같이 상수에 저장하면 인스턴스의 모든 속성이 상수가 된다. 따라서 `v.width = 1.0`과 같이 속성을 변경하려 하면 `Cannot assign`이라는 오류가 뜨면서 변경할 수 없다. `let` 키워드는 상수가 가리키는 스택을 값을 바꾸지 못하는 공간으로 바꾼다. 그래서 속성이 `var`로 선언되어 있지만 변경할 수 없게 된다.
```
class SizeObject {
	var width = 0.0
	var height = 0.0
}
let o = SizeObject()
o.width = 1.0
o.height = 2.0
```
이번에도 `let`키워드는 상수가 가리키는 스택을 값을 바꾸지 못하는 공간으로 바꾼다. 참조형식은 스택에 메모리 주소가 저장된다. 따라서 상수가 가리키는 인스턴스를 변경할 수 없다.
반면 인스턴스가 저장된 힙은 아무런 제약이 없다. 따라서 값 형식과 달리 인스턴스 속성을 마음대로 바꿀 수 있다. 

### 비교연산자와 항등연산자
다음으로 비교연산자와 항등연산자를 메모리 관점에서 보자.
|Value Type|Reference Type|
|-|-|
|==<br>!=|==<br>!=<br>=\==<br>!\==

**비교연산자**는 값형식을 비교할 때 스택에 저장된 값을 비교한다. 반면 참조형식을 비교할 때는 힙에 저장된 값을 비교한다. 다시말해서 형식에 상관 없이 실제 값을 비교한다.
참조형식은 주소를 따로 저장하기 때문에 주소를 비교하는 방법도 필요하다. 이때에는 **항등연산자**를 사용한다.

# 👩🏻‍🔧 그래서 구조체와 클래스 중 어떤걸 사용할까?

**속도** : 참조 타입은 참조 추적에 비용이 많이 든다. 값 타입은 시스템 리소스가 적게 들어간다.  
**안전성** :  클래스는 참조 타입이기 때문에  멀티쓰레드 환경에서 여기저기 참조하여 작업하면 데이터가 꼬일 가능성이 크다.  

     
따라서 애플은 가급적으로 값 형식인 구조체나 열거형을 사용하는 것을 권장한다. 
<br>
<br>
**[애플의 가이드라인](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)**
**기본적으로 구조체를 사용하십시오.**
**Objective-C 클래스나 Objective-C API를 사용해야 한다면 클래스를 사용하십시오.**
**클래스 인스턴스의 identity를 제어해야 한다면 클래스를 사용하십시오.**
	ex) file handles, network connections and shared hardware
	하지만 이 경우에는 앱 내에서 인스턴스(예를 들면 데이터베이스에 액세스 하는 클래스)에 접근하는 부분을 제한해야 한다. 앱 전체에서 클래스 인스턴스를 공유한다면 오류가 발생할 가능성이 높다.
**프로토콜과 함께 구조체를 사용하여 기능을 공유하십시오.**



