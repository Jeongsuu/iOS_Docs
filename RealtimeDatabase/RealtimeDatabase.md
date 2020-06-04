# Firebase 공부

# 1. Firebase란?

- 구글에서 만든 서버 프로그래밍 없이 서버가 하는 역할을 수행해주는 플랫폼
- 데이터베이스, 분석, 광고, FCM(Firebase Cloud Message), 파일 저장 공간 등 많은 것을 SDK를 통해 구현 가능하게 해줌

# 2. NoSQL

- 스키마가 없는 데이터베이스이므로 테이블을 구성하는 스키마, 컬럼의 자료형 등 규칙이 따로 없다. 따라서 확장성에 용이

[NoSQL의 종류와 특징](Firebase%204d1dc0698d364835a09b2a36dc99dbfd/NoSQL%202c09aa2e32a44660804f4a4faf2f5227.csv)

- Firebase는 위의 Document 종류에 해당한다.
- Firebase는 SQL을 사용하지 않고 jSON(JavaScript Object Notation) 방식의 문서를 사용해 저장한다. 즉 JSON을 Dictionary 형태로 사용하여 데이터를 주고 받는다.
- 프로젝트에서 사용할 게시판 JSON 구조 생각

```json
{
	"Board" : {
		"1" : {
			"author"     : "name"
			"title"      : "안녕하세요"
			"body"       : "석도형입니다."
			"createTime" : "2020-06-02"
			"images"     : [image]
			"viewers"    : 12
      "likes"      : 14
      "replys"     : {
				"1" : {
					"author"     : "name"
					"text"       : "하이하이"
					"createTime" : "2020-06-03"
}
```

- Firebase 실시간 데이터베이스 규칙
    - 읽기 및 쓰기 권한, 데이터 구조 및 색인 생성 여부 결정

[규칙 유형](Firebase%204d1dc0698d364835a09b2a36dc99dbfd/Untitled%2010302b16ac6947279a4d87743d324724.csv)

- 승인

```json
{
  "rules": {
    "foo": {
      ".read": true,
      ".write": false
    }
  }
}
```

- 위 코드는 /foo/ 경로는 누구나 읽을 수 있지만 아무도 쓸 수 없도록 하는 보안 규칙이다.

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

- 위는 인증된 사용자에게 /users/<uid>/에 대한 쓰기 권한을 부여하는 것

- 데이터 검증

```json
{
  "rules": {
    "foo": {
      ".validate": "newData.isString() && newData.val().length < 100"
    }
  }
}
```

- /foo/에 기록하는 데이터를 문자열로 제한하고 문자열 길이를 100자 미만으로 제한하도록 하는 검증 규칙

- 데이터베이스 색인

```json
{
  "rules": {
    "dinosaurs": {
      ".indexOn": ["height", "length"]
    }
  }
}
```

- /dinosaurs/의 키 및 길이 필드로 색인화 하는 색인 규칙

- 사용자의 프로필은 일반적으로 /users/$uid에 들어감

- 데이터 중첩 배제
    - 파이어베이스는 최대 32단계 데이터 중첩이 가능하지만 특정 위치의 데이터를 가져오면 모든 하위 노드가 함께 검색되므로 데이터 구조를 평면화해주는 것이 좋다.

        ```json
        {
          // This is a poorly nested data architecture, because iterating the children
          // of the "chats" node to get a list of conversation titles requires
          // potentially downloading hundreds of megabytes of messages
          "chats": {
            "one": {
              "title": "Historical Tech Pioneers",
              "messages": {
                "m1": { "sender": "ghopper", "message": "Relay malfunction found. Cause: moth." },
                "m2": { ... },
                // a very long list of messages
              }
            },
            "two": { ... }
          }
        }
        ```

    - 예를 들어 위와 같은 코드는 채팅의 제목을 나열하기 위해서는 모든 멤버와 메시지를 포함한 chats 트리를 받아와야 한다.

    ```json
    {
      // Chats contains only meta info about each conversation
      // stored under the chats's unique ID
      "chats": {
        "one": {
          "title": "Historical Tech Pioneers",
          "lastMessage": "ghopper: Relay malfunction found. Cause: moth.",
          "timestamp": 1459361875666
        },
        "two": { ... },
        "three": { ... }
      },

      // Conversation members are easily accessible
      // and stored by chat conversation ID
      "members": {
        // we'll talk about indices like this below
        "one": {
          "ghopper": true,
          "alovelace": true,
          "eclarke": true
        },
        "two": { ... },
        "three": { ... }
      },

      // Messages are separate from data we may want to iterate quickly
      // but still easily paginated and queried, and organized by chat
      // conversation ID
      "messages": {
        "one": {
          "m1": {
            "name": "eclarke",
            "message": "The relay seems to be malfunctioning.",
            "timestamp": 1459361875337
          },
          "m2": { ... },
          "m3": { ... }
        },
        "two": { ... },
        "three": { ... }
      }
    }
    ```

    - 하지만 위처럼 비정규화를 통해 평면화를 한다면 chats 트리만 불러와 제목을 표시할 수 있다.

## iOS에서 데이터 저장

[4가지 메소드](Firebase%204d1dc0698d364835a09b2a36dc99dbfd/4%20798712ef39344a23b15c5cad61365588.csv)

- 기본 쓰기 작업
    - setValue를 사용해 지정된 참조에 데이터를 저장하고 기존 경로의 모든 데이터를 대체할 수 있다.

    ```json
    self.ref.child("users").child(user!.uid).setValue(["username": username])
    ```

    - 위 처럼 새로운 유저를 추가할 수 있다.

    ```json
    self.ref.child("users/(user.uid)/username").setValue(username)
    ```

    - 위는 user의 프로필을 업데이트하는 코드
- 데이터 목록 추가
    - 멀티 사용자 앱에서는 childByAutoId 메소드를 사용해 목록에 데이터를 추가, childByAutoId는 타임스탬프에 기초하므로 시간순으로 자동 정렬된다.
- 특정 필드 업데이트
    - 다른 하위 노드를 덮어쓰지 않고 특정 하위노드를 업데이트하려면 updateChildValues를 사용

    ```json
    let key = ref.child("posts").childByAutoId().key
    let post = ["uid": userID,
                "author": username,
                "title": title,
                "body": body]
    let childUpdates = ["/posts/\(key)": post,
                        "/user-posts/\(userID)/\(key)/": post]
    ref.updateChildValues(childUpdates)
    ```

    - 위 예제는 posts에 childByAutoId를 통해 게시물을 만들고 키를 받아온 후 /posts/&postId와   /user-posts/$userid/$postid 두 곳에 같은 게시물을 등록하는 것이다. posts는 모든 사용자의 게시물이고 /user-posts/userid는 각각의 사용자가 적은 게시물 목록이다.
- 데이터 삭제는 removeValue 호출을 통해 이뤄짐
- 트랜잭션으로 저장
    - 게시물의 좋아요, 조회수와 같은 동시 수정으로 인해 손상될 수 있는 데이터를 트랜잭션 작업으로 사용 가능하게 한다.

## iOS에서 데이터 검색

- Firebase 데이터를 검색하려면 FIRDatabase 참조에 비동기 리스너를 연결합니다. 리스너는 데이터의 초기 상태가 확인될 때 한 번 호출된 후 데이터가 변경될 때마다 다시 호출됩니다.

[이벤트 리스너](Firebase%204d1dc0698d364835a09b2a36dc99dbfd/Untitled%20f86a02a5cd6e486c8eb10e433b58d405.csv)

- 이벤트 리스너를 추가하기 위해서는 observeEventType 메소드를 사용해 이벤트 유형 및 콜백 블록을 지정한다.
- FIRDataEventTypeChildAdded는 일반적으로 하위 노드를 검색하기 위해 사용한다.
- 리스너 분리
    - 리스너는 ViewController에서 벗어날 때 데이터 동기화를 자동으로 중지하지 않기 때문에 적절히 삭제하지 않으면 메모리에 남아 있는다. 리스너가 더 이상 필요하지 않은 경우 removeObserverWithHandle 메소드에 해당 FIRDatabaseHandle을 전달하여 삭제해야 한다.ㄹ

- 데이터 한번 읽기
    - 한 번만 호출되고 즉시 삭제되는 콜백이 필요할 수 있다.

    ```json
    let userID = FIRAuth.auth()?.currentUser?.uid
    ref.child("users").child(userID!).observeSingleEventOfType(.Value, withBlock: { (snapshot) in
      // Get user value
      let username = snapshot.value!["username"] as! String
      let user = User.init(username: username)

      // ...
      }) { (error) in
        print(error.localizedDescription)
    }
    ```

    - 위 코드는 새 게시물을 작성할 때 사용자의 프로필을 로드한다.
    - 

[데이터 정렬](Firebase%204d1dc0698d364835a09b2a36dc99dbfd/Untitled%209192c9993e9642559ba0341f22d3fa73.csv)

[데이터 필터링](Firebase%204d1dc0698d364835a09b2a36dc99dbfd/Untitled%20ef2de9fd0cb2441ba68e0a6851695f37.csv)