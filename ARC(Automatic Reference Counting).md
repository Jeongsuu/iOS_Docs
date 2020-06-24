# 🔖 코코아의 메모리 관리 모델

Stack에 저장된 데이터는 자동으로 제거되기 때문에 특별한 관리가 필요없다. 하지만 Heap에 저장된 데이터는 필요하지 않은 시점에 직접 제거해야 한다. 메모리 관리 모델은 힙에 저장되는 데이터를 관리한다. 다시 말하면 클래스 인스턴스의 메모리를 관리한다. 따라서 이번 포스트에서 언급하는 인스턴스는 모두 **[클래스 인스턴스](https://ahyeonlog.tistory.com/1)** 이다.
**Objective-C**는 **MRC, ARC**, **Swift**는 Objective-C와 동일한 메모리 관리모델인 **ARC**를 사용한다. 관리모델이 언어에 속하는 건 아니고 애플이 제공하는 개발 환경인 코코아에 속해있다고 보는 것이 정확하다. 

메모리 관리모델을 이해하기 위해서는 **소유 정책(Ownership Policy)** 와 **참조 카운트(Reference Count)** 를 먼저 이해해야 한다. 인스턴스는 하나 이상의 소유자가 있는 경우 메모리에 유지된다. 반대로 소유자가 없는 경우 메모리에서 제거된다. 메모리 제거 시점을 파악하기 위해 소유자 수를 파악하는데 이것을 참조카운트라고 한다. 즉, 참조카운트가 1이상이면 인스턴스가 메모리에서 유지되고 0이면 메모리에서 제거된다. 소유자가 `retain` 메소드를 호출해서 소유권을 얻으면 참조카운트가 +1이 되고, `release` 메소드를 호출해서 소유권을 포기하면 참조카운트가 -1이 된다. 그리고 참조카운트가 0이되면 메모리에서 즉시 제거된다.

# 💿 MRC(Manual Reference Counting)
MRC 모델에서는 소유정책과 관련된 모델을 직접 코드로 구현한다. 하지만 소유 정책을 구현하는 것은 매우 매우!! 어렵다. ARC에 비해 많은 코드를 작성해야 하고 메모리 오류가 날 확률도 높다. 그래서 프로그램의 안전성이 낮아지고 디버깅이 어려워진다. 이런 문제로 ARC가 도입되었다. ARC(Automatic Reference Counting) 이름에서 처럼 자동으로 소유 정책을 자동으로 처리한다. 

# 💿 ARC(Automatic Reference Counting)
소유 정책과 참조 카운트를 처리하는 방식은 MRC와 동일하다. 하지만 컴파일러가 메모리 관리 코드를 자동으로 처리해주기 때문에 직접 작성할 필요가 없다. 따라서 프로그램의 안전성도 높아지게 된다. Objective-C는 MRC, ARC 둘 다 지원하지만 특별한 이유가 없다면 ARC를 사용한다. 메모리 관리를 ARC가 해주기 때문에 구현에 관한 부분은 다루지 않겠다.
![https://mermaid.ink/svg/eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG5Pd25lci0tPj4rSW5zdGFuY2U6IHJldGFpbiArMVxuSW5zdGFuY2UtLT4-T3duZXI6IHJlbGVhc2UgLTFcdFx0IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0](https://mermaid.ink/svg/eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG5Pd25lci0tPj4rSW5zdGFuY2U6IHJldGFpbiArMVxuSW5zdGFuY2UtLT4-T3duZXI6IHJlbGVhc2UgLTFcdFx0IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

우선 강한 참조에 대해 알아보자.
기본적으로 인스턴스와 소유자는 강한 참조로 연결된다. 다시 정리하자면 대상을 소유할 때마다 참조 카운트가 1씩 증가하고 소유를 포기할 때마다 참조 카운트가 1씩 감소한다. 그리고 0이 되면 메모리에서 인스턴스는 제거된다. 


```swift
class Person {
	var name = "Ahyeon"
	deinit {
		print("person deinit")
	}
```
다음 코드는 소멸자가 구현되어 있다. 소멸자는 인스턴스가 해제되기 전에 호출된다. 

```swift
var person1: Person?
var person2: Person?
var person3: Person?

person1 = Person()
person2 = person1
person3 = person1
```

![https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcbnBlcnNvbjEtLT58KzF8UFtQZXJzb24gSW5zdGFuY2U6IDNdXG5wZXJzb24yLS0-fCsxfFBcbnBlcnNvbjMtLT58KzF8UFxuXG5cdFx0XHRcdFx0IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcbnBlcnNvbjEtLT58KzF8UFtQZXJzb24gSW5zdGFuY2U6IDNdXG5wZXJzb24yLS0-fCsxfFBcbnBlcnNvbjMtLT58KzF8UFxuXG5cdFx0XHRcdFx0IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)


person1, person2, person3가 Person Instance와 강한 참조로 연결된다. 
앞에서 인스턴스를 소유할 때 `retain` 메소드를 호출한다고 했는데 코드엔 없다. Swift에서는 ARC를 사용하기 때문에 컴파일러가 코드를 분석한 후 자동으로 추가해준다. 

```
person1 = nil
person2 = nil
```
![https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcbnBlcnNvbjEtLi1QW1BlcnNvbiBJbnN0YW5jZTogMV1cbnBlcnNvbjItLi1QXG5wZXJzb24zLS0-fCsxfFBcblxuXHRcdFx0XHRcdCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In19](https://mermaid.ink/svg/eyJjb2RlIjoiZ3JhcGggTFJcbnBlcnNvbjEtLi1QW1BlcnNvbiBJbnN0YW5jZTogMV1cbnBlcnNvbjItLi1QXG5wZXJzb24zLS0-fCsxfFBcblxuXHRcdFx0XHRcdCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In19)

이번에는 person1, person2 변수에 nil을 저장했다. `nil`을 저장한다는 것은 소유권을 포기하는 것과 같고 소유권을 포기하면 즉시 강한 참조가 제거되고 참조카운트가 1씩 감소한다. 

```
person3 = nil
```
이제 마지막으로 person3 변수에 nil을 저장해보자. 그러면 마지막 소유권자였던 person3이 소유권을 포기하며 참조카운트가 0이 되고 이 시점에 인스턴스의 메모리가 소멸된다. 따라서 `deinit()`이 호출되고 person deinit 로그가 출력된다. 







