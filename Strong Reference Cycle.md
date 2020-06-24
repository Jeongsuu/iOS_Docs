
# 🦖 Strong Reference Cycle이란?

```
class Person {
	var name = "Ahyeon"
	var car: Car?
	deinit {
		print("person deinit")
	}
}

class Car {
	var model: String
	var lesee: Person?

	init(model: String) {
		self.model = model
	}
	deinit {
		print("car deinit")
	}
}
```
Person class는 Car 속성, Car class는 Person 속성을 가지고 있다. 

```
var person: Person? = Person()
var rentedCar: Car? = Car(model: "Benz")
```

![https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcblx0QVtwZXJzb25dIC0tPnzqsJXtlZwg7LC47KGwICsxfCBCKFBlcnNvbiBJbnN0YW5jZSlcblx0Q1tyZW50ZWQgQ2FyXSAtLT586rCV7ZWcIOywuOyhsCArMXwgRChDYXIgSW5zdGFuY2UpXG5cdFx0XHQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcblx0QVtwZXJzb25dIC0tPnzqsJXtlZwg7LC47KGwICsxfCBCKFBlcnNvbiBJbnN0YW5jZSlcblx0Q1tyZW50ZWQgQ2FyXSAtLT586rCV7ZWcIOywuOyhsCArMXwgRChDYXIgSW5zdGFuY2UpXG5cdFx0XHQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)

`person`과 `rentedCar`는 각각의 Person, Car 인스턴스에 강한 참조로 연결 된다. 이 시점에 각각 참조카운트는 1이다.

```
person?.car = rentedCar
rentedCar?.lessee = person
```
이 코드로 car속성과 retedCar인스턴스가 강한 참조로 연결되고 lessee 속성과 person인스턴스가 강한 참조로 연결된다.

![https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggVERcblx0QVtwZXJzb25dIC0tPnzqsJXtlZwg7LC47KGwICsxfCBQSSgyKVxuXHRcblx0c3ViZ3JhcGggUGVyc29uSW5zdGFuY2VcbiBcdFBJXG5cdCBjYXJbY2FyXVxuXHRlbmRcblxuXHRDW3JlbnRlZCBDYXJdIC0tPnzqsJXtlZwg7LC47KGwICsxfCBDSSgyKVxuXHRcblx0c3ViZ3JhcGggQ2FySW5zdGFuY2Vcblx0bGVzc2VlXG5cdENJXG5cdGVuZFxuXG5cdGxlc3NlZS0tPnzqsJXtlZzssLjsobAgKzF8UElcblx0Y2FyLS0-fOqwle2VnOywuOyhsCArMXxDSSIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggVERcblx0QVtwZXJzb25dIC0tPnzqsJXtlZwg7LC47KGwICsxfCBQSSgyKVxuXHRcblx0c3ViZ3JhcGggUGVyc29uSW5zdGFuY2VcbiBcdFBJXG5cdCBjYXJbY2FyXVxuXHRlbmRcblxuXHRDW3JlbnRlZCBDYXJdIC0tPnzqsJXtlZwg7LC47KGwICsxfCBDSSgyKVxuXHRcblx0c3ViZ3JhcGggQ2FySW5zdGFuY2Vcblx0bGVzc2VlXG5cdENJXG5cdGVuZFxuXG5cdGxlc3NlZS0tPnzqsJXtlZzssLjsobAgKzF8UElcblx0Y2FyLS0-fOqwle2VnOywuOyhsCArMXxDSSIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)


PersonInstance와 CarInstance의 참조카운트가 2로 증가한다.

```
person = nil
rentedCar = nil
```
person변수에 nil을 할당하여 소유권을 포기하고 그 후 rentedCar 변수에 nil을 할당하여 소유권을 포기해보자.

![https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggVERcblx0XG5cdFxuXHRzdWJncmFwaCBQZXJzb25JbnN0YW5jZVxuIFx0UElbMV1cblx0IGNhcltjYXJdXG5cdGVuZFxuXG5cblx0XG5cdHN1YmdyYXBoIENhckluc3RhbmNlXG5cdGxlc3NlZVxuXHRDSVsxXVxuXHRlbmRcblxuXHRsZXNzZWUtLT586rCV7ZWc7LC47KGwICsxfFBJXG5cdGNhci0tPnzqsJXtlZzssLjsobAgKzF8Q0kiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggVERcblx0XG5cdFxuXHRzdWJncmFwaCBQZXJzb25JbnN0YW5jZVxuIFx0UElbMV1cblx0IGNhcltjYXJdXG5cdGVuZFxuXG5cblx0XG5cdHN1YmdyYXBoIENhckluc3RhbmNlXG5cdGxlc3NlZVxuXHRDSVsxXVxuXHRlbmRcblxuXHRsZXNzZWUtLT586rCV7ZWc7LC47KGwICsxfFBJXG5cdGNhci0tPnzqsJXtlZzssLjsobAgKzF8Q0kiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)
소유권을 포기했지만 참조카운트가 1이상이기 때문에 메모리에서 사라지지 않는다. 두 인스턴스의 속성이 서로를 소유하고 있기 때문이다. `nil`로 인해 두 인스턴스에 접근할 방법이 없기때문에 메모리를 해지할 방법이 없다.


이렇게 강한 참조 때문에 인스턴스를 제대로 해지할 수 없는 경우를 **강한 참조 사이클(Strong Reference Cycle)** 이라고 한다. ARC는 메모리 관리를 자동으로 해주지만 강한 참조 사이클을 해결하지는 못한다.

따라서 강한 참조 사이클은 **약한(Weak) 참조**, **비소유(Unowned) 참조**를 사용해서 해결한다.

# 🐇 Weak Reference
강한 참조와 달리 약한 참조는 인스턴스를 참조하지만 소유하진 않으므로 참조 카운트가 증가하지 않는다.
대상 인스턴스를 참조하고 있는 중 언제든 메모리에서 사라질 수 있다. 따라서 소유자에 비해서 짧은 생명주기를 가진 인스턴스를 참조할 때 사용한다.

![https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcblx0QVtSZWZlcnJlcl0gLS4tPnxXZWFrIFJlZmVyZW5jZXwgQltJbnN0YW5jZV1cblxuXHQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcblx0QVtSZWZlcnJlcl0gLS4tPnxXZWFrIFJlZmVyZW5jZXwgQltJbnN0YW5jZV1cblxuXHQiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)


**Syntax**
```
weak var name: Type?
```

약한 참조는 항상 옵셔널 형식으로 선언한다. 그리고 `var` 키워드 앞에 `weak` 키워드를 선언한다.  참조하고 있는 인스턴스가 해제되면 자동으로 nil로 초기화 된다. 
```
class Person {
	var name = "Ahyeon"
	weak var car: Car?
	deinit {
		print("person deinit")
	}
}

class Car {
	var model: String
	weak var lesee: Person?

	init(model: String) {
		self.model = model
	}
	deinit {
		print("car deinit")
	}
}
```
`weak` 이 붙은 속성은 약한 참조로 선언되고 인스턴스를 참조하지만 소유하진 않는다. 따라서 강한 참조 사이클이 생기지않는다.

```
var person: Person? = Person()
var rentedCar: Car? = Car(model: "Benz")

person?.car = rentedCar
rentedCar?.lessee = person

person = nil

```
이렇게 하면 메모리 누수 없이 코드를 짤 수 있다.

# 🤔 Unowned Reference

비소유 참조도 약한 참조와 같은 방식으로 속성을 참조한다. 하지만 옵셔널이 아니라 비옵셔널로 선언한다.
비옵셔널로 선언해야할 때나, 인스턴스의 생명주기가 소유자와 같거나 더 긴 경우에 사용한다.
**Syntax**
```
unowned var name: Type
```
`unowned` 키워드를 사용한다.

```
class Person {
	var name = "Ahyeon"
	var car: Car?
	deinit {
		print("person deinit")
	}
}

class Car {
	var model: String
	unowned var lesee: Person

	init(model: String, lessee: Person) {
		self.model = model
		self.lessee = lessee
	}
	deinit {
		print("car deinit")
	}
}

var person: Person? = Person()
var rentedCar: Car? = Car(model: "Benz", lessee: person!)

person?.car = rentedCar


person = nil
rentedCar = nil
```

