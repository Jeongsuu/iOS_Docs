# Dispatch Queues in iOS

## Dispatch Queues?

Grand Central Dispatch(GCD) 디스패치 큐는 task 수행을 위한 강력한 도구이다. 디스패치 큐를 사용하면 호출자와 관련하여 비동기/동기식으로 임의의 코드 블록을 수행할 수 있다. 디스패치 큐는 사용하기 쉽다는 장점이 있다.

디스패치 큐는 앱에서 task를 비동기적으로 동시에 수행할 수 있는 손쉬운 방법이다. 비동기적으로 수행하고 싶은 작업을 디스패치 큐에 추가하여 task를 정의한다.

## Dispatch Queue의 타입

- Serial : Serial queues는 큐에 추가된 순서대로 한번에 하나의 task를 실행한다. 예를 들어 Serial queues를 4개 작성하면 각 큐는 한 번에 하나의 task만 처리하지만, 최대 4개의 task가 각 큐에서 동시에 실행될 수 있다.
- Concurrent : Concurrent queues는 동시에 하나 이상의 task를 실행하지만 task는 큐에 추가된 순서대로 계속 시작된다. 현재 실행 중인 task는 dispatch queue에서 관리하는 고유한 쓰레드에서 실행된다.
- Main dispatch queue : 앱의 main 쓰레드에서 task를 실행하는 전역적으로 사용 가능한 serial qjueue이다. 앱의 메인 쓰레드에서 실행되기 때문에 종종 앱의 주요 동기화 지점으로 사용되고, UI 관련 작업은 반드시 메인 스레드에서 이뤄져야 한다.

## Cocoa Application에서 제공하는 2가지 큐

- main queue : 앱의 메인 스레드에서 task를 실행하는 전역적으로 사용 가능한 serial queue이다. 이 큐는 앱의 실행루프와 함께 작동하여 큐에 있는 task의 실행을 다른 이벤트 소스의 실행과 얽힌다.
- global queue :  Concurrent queue로 동시에 하나 이상의 task를 실행하며 task는 순서에 따라 시작된다.

```swift
DispatchQueue.main.sync {
	//code
	//메인 큐의 사용법
}

/DispatchQueue.global().async {
	//code
}
```

- 위 형태와 같이 코드를 작성한다, sync, async는 말 그대로 동기적으로 코드를 실행할 지 비동기적으로 실행할 지 결정하는 것이다.

- global 큐의 경우 파라미터로 무엇인가를 받을 수 있는데 qos라는 파라미터를 받을 수 있다.


- qos는 작업의 중요도라고 보면 된다. 즉 qos를 지정함으로써 중요도를 표시하고 시스템은 이 우선순위에 따라 스케쥴링을 정한다.


main과 global 큐 이외에 자신만의 큐를 생성할 수도 있다.

```swift
let q = DispatchQueue(label: "seok")
// or
let q = DispatchQueue(label: "seok", attributes: .concurrent)
```

위와 같이 고유한 레이블의 큐를 생성할 수 있고 attributes 속성을 주지 않으면 기본적으로 serial queue로 생성되고 .concurrent라는 속성을 이용해 concurrent queue를 생성할 수 있다.

## 동기와 비동기

```swift
DispatchQueue.global().sync {
    for i in 1...5 {
        print(i)
    }
    print("==================")
}
for i in 100...105 {
    print(i)
}
```

위와 같이 1~5를 출력하는 동기작업과 그 아래에 100~105를 출력하는 코드를 작성했을 때 동기적으로 실행되기 때문에 코드의 순서에 따라 1~5가 출력되고 그 이후 global 큐의 작업이 끝나면 100~105를 출력하게 된다.

```swift
DispatchQueue.global().async {
    for i in 1...5 {
        print("\(i)🐶")
    }
    print("==================")
}
DispatchQueue.global().async {
    for i in 200...205 {
        print("\(i)😍")
    }
    print("==================")
}

for i in 100...105 {
    print("\(i)👻")
    
}
```


하지만 위처럼 1~5를 출력하는 작업과 200~205를 출력하는 작업을 비동기적으로 큐에 넣었을 때 async는 완료 여부에 상관없이 다음 코드를 실행하기 때문에 결과는 매번 다르다. 또한 글로벌 큐를 사용하여 concurrent queue(동시에 여러 task를 수행)가 이용되었기 때문에 1~5를 출력하는 작업이 끝나지 않더라도 200~205를 출력하는 작업이 실행될 수 있다.

## Dispatch WorkItem

WorkItem은 실행할 task를 캡슐화한 것이다. WorkItem은 Dispatch queue나 group에 dispatch할 수 있다.

```swift
let item = DispatchWorkItem {
	for i in 1...10 {
		print("\(i)")
	}
}

DispatchQueue.global().async(execute: item)
```

위와 같이 task를 캡슐화하고 dispatch queue의 execute 속성에 item을 넣어주어서 실행시킬 수 있도록 한다.

## QoS

```swift
let firstQueue = DispatchQueue(label: "first", qos: .userInitiated)
let secondQueue = DispatchQueue(label: "second", qos: .utility)

firstQueue.async {
	for i in 1...5{
		print("\(i)")
	}
}

secondQueue.async {
	for i in 100...105 {
		print("\(i)")
	}
}
```

위와 같이 first 큐와 second 큐에 서로 다른 QoS를 부여할 때 항상 더 높은 QoS의 task가 먼저 실행되는 것은 아니지만 대체로 먼저 실행되며 더 빠르게 실행 완료 된다.

또한 Queue 자체에 QoS를 부여할 수도 있지만 DispatchWorkItem에도 QoS를 부여할 수 있다.

```swift
let item = DispatchWorkItem(qos: .userInitiated)
```

## Dispatch Group

Dispatch Group은 여러 개의 다른 작업을 제출하고 모든 작업이 완료되면 이를 추적할 수 있다. 따라서 이 그룹은 할당된 모든 작업이 완료될 때까지 기다려야 하는 경우 유용하게 사용될 수 있다.

```swift
import Foundation

let group = DispatchGroup()
let q = DispatchQueue(label: "mine", attributes: .concurrent)

q.async(group: group) {
    for i in 1...5 {
        print("\(i)")
    }
}

q.async(group: group) {
    for i in 100...105 {
        print("\(i)")
    }
}

group.notify(queue: q) {
    print("done!")
}
```


위처럼 그룹을 생성하고 큐의 group 속성에 그룹을 지정해준 뒤 group의 notify를 이용하면 모든 작업이 완료되는 시점에 notify가 호출되게 된다.
