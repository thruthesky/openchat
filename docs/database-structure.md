# Firebase Realtime Database 구조

## 개요

Firebase Realtime Database (RTDB)는 NoSQL 클라우드 호스팅 데이터베이스로, 실시간으로 데이터를 저장하고 동기화할 수 있습니다. 채팅 애플리케이션에서는 메시지, 사용자 정보, 채팅방 정보 등을 효율적으로 저장하고 관리하기 위해 체계적인 데이터 구조 설계가 필요합니다.

## 용어 설명

### RTDB 노드 용어
RTDB의 각 노드는 상황에 따라 다음과 같이 부릅니다:
- **문서**: 특정 경로의 데이터 전체를 지칭할 때
- **값**: 단순 값(문자열, 숫자 등)을 가리킬 때
- **노드**: 트리 구조에서 특정 위치를 가리킬 때
- **데이터**: 일반적인 정보를 지칭할 때

각 문서는 맵(객체)의 형태로 "키", "값" 쌍으로 구성됩니다.

### 변수 표기법
문서에서 변수 값의 표시는 `<변수명>` 형식으로 표시합니다. 즉, `<`와 `>` 사이에 값이 들어가면 변수로 동적인 값이 들어간다는 뜻입니다.

예시:
- `<uid>`: 사용자 고유 ID
- `<roomId>`: 채팅방 ID
- `<messageId>`: 메시지 ID
- `<timestamp>`: 타임스탬프 값

## 데이터베이스 구조

### 1. 사용자 정보 (/users)

사용자 데이터는 `/users/<uid>` 경로에 저장됩니다.

```json
/users/<uid>
{
  "displayName": "사용자 이름",        // 필수: 사용자 표시 이름
  "photoUrl": "프로필 이미지 URL",     // 필수: 프로필 사진 URL
  "status": "online|offline",         // 선택: 온라인 상태
  "lastSeen": "<timestamp>",          // 선택: 마지막 접속 시간
  "createdAt": "<timestamp>",         // 선택: 계정 생성 시간
  // 추가 속성을 원하는 대로 추가할 수 있습니다
  "bio": "자기소개",                   // 예시: 추가 속성
  "phone": "전화번호"                  // 예시: 추가 속성
}
```

**기본 속성**:
- `displayName`: 채팅에서 표시될 사용자 이름 (필수)
- `photoUrl`: 사용자 프로필 이미지 URL (필수)

**확장 가능성**: 기본적으로 `displayName`과 `photoUrl` 속성을 가지지만, 애플리케이션의 요구사항에 따라 얼마든지 추가 속성을 정의할 수 있습니다.

### 2. 채팅방 정보 (/rooms)

채팅방 데이터는 `/rooms/<roomId>` 경로에 저장됩니다.

```json
/rooms/<roomId>
{
  "name": "채팅방 이름",
  "type": "private|group",            // 채팅방 유형 (1:1 또는 그룹)
  "members": {                        // 채팅방 참여자 목록
    "<uid1>": true,
    "<uid2>": true
  },
  "createdBy": "<uid>",              // 채팅방 생성자
  "createdAt": "<timestamp>",        // 생성 시간
  "lastMessage": {                   // 마지막 메시지 정보 (빠른 미리보기용)
    "text": "마지막 메시지",
    "senderId": "<uid>",
    "timestamp": "<timestamp>"
  }
}
```

### 3. 메시지 정보 (/messages)

메시지는 채팅방별로 `/messages/<roomId>/<messageId>` 경로에 저장됩니다.

```json
/messages/<roomId>/<messageId>
{
  "text": "메시지 내용",
  "senderId": "<uid>",               // 보낸 사람 ID
  "timestamp": "<timestamp>",        // 전송 시간
  "type": "text|image|file",        // 메시지 타입
  "readBy": {                       // 읽은 사람 목록
    "<uid1>": true,
    "<uid2>": true
  },
  "mediaUrl": "미디어 URL",          // 이미지/파일 메시지의 경우
  "fileName": "파일명",               // 파일 메시지의 경우
  "fileSize": 123456                // 파일 크기 (bytes)
}
```

### 4. 사용자별 채팅방 목록 (/userRooms)

각 사용자가 참여한 채팅방 목록은 `/userRooms/<uid>/<roomId>` 경로에 저장됩니다.

```json
/userRooms/<uid>/<roomId>
{
  "joinedAt": "<timestamp>",         // 채팅방 참여 시간
  "lastReadAt": "<timestamp>",       // 마지막 읽은 시간
  "unreadCount": 0,                  // 읽지 않은 메시지 수
  "muted": false,                     // 알림 음소거 여부
  "pinned": false                    // 채팅방 고정 여부
}
```

### 5. 푸시 알림 토큰 (/fcmTokens)

FCM 푸시 알림을 위한 토큰은 `/fcmTokens/<uid>/<tokenId>` 경로에 저장됩니다.

```json
/fcmTokens/<uid>/<tokenId>
{
  "token": "FCM 토큰 문자열",
  "platform": "ios|android|web",
  "createdAt": "<timestamp>",
  "updatedAt": "<timestamp>"
}
```

## 데이터 접근 예제

### Flutter에서 사용자 정보 가져오기

```dart
// 사용자 정보 가져오기
Future<Map<String, dynamic>> getUserInfo(String uid) async {
  final snapshot = await FirebaseDatabase.instance
    .ref('users/$uid')
    .get();

  if (snapshot.exists) {
    return Map<String, dynamic>.from(snapshot.value as Map);
  }
  return {};
}

// 사용자 정보 생성/업데이트
Future<void> createOrUpdateUser(String uid, String displayName, String photoUrl) async {
  await FirebaseDatabase.instance
    .ref('users/$uid')
    .update({
      'displayName': displayName,
      'photoUrl': photoUrl,
      'lastSeen': ServerValue.timestamp,
    });
}

// 사용자 상태 업데이트
Future<void> updateUserStatus(String uid, String status) async {
  await FirebaseDatabase.instance
    .ref('users/$uid')
    .update({
      'status': status,
      'lastSeen': ServerValue.timestamp,
    });
}
```

### Alpine.js에서 사용자 정보 가져오기

```javascript
// 사용자 정보 가져오기
function getUserInfo(uid) {
  return firebase.database()
    .ref(`users/${uid}`)
    .once('value')
    .then(snapshot => snapshot.val());
}

// 사용자 정보 생성/업데이트
function createOrUpdateUser(uid, displayName, photoUrl) {
  return firebase.database()
    .ref(`users/${uid}`)
    .update({
      displayName: displayName,
      photoUrl: photoUrl,
      lastSeen: firebase.database.ServerValue.TIMESTAMP
    });
}

// 사용자 상태 실시간 구독
function watchUserStatus(uid, callback) {
  firebase.database()
    .ref(`users/${uid}/status`)
    .on('value', snapshot => {
      callback(snapshot.val());
    });
}
```

### 메시지 전송 예제

```dart
// Flutter - 메시지 전송
Future<void> sendMessage(String roomId, String text) async {
  final messageRef = FirebaseDatabase.instance
    .ref('messages/$roomId')
    .push();

  await messageRef.set({
    'text': text,
    'senderId': currentUserId,
    'timestamp': ServerValue.timestamp,
    'type': 'text'
  });

  // 채팅방의 lastMessage 업데이트
  await FirebaseDatabase.instance
    .ref('rooms/$roomId/lastMessage')
    .set({
      'text': text,
      'senderId': currentUserId,
      'timestamp': ServerValue.timestamp
    });
}
```

```javascript
// Alpine.js - 메시지 전송
async function sendMessage(roomId, text) {
  const messageRef = firebase.database()
    .ref(`messages/${roomId}`)
    .push();

  await messageRef.set({
    text: text,
    senderId: currentUserId,
    timestamp: firebase.database.ServerValue.TIMESTAMP,
    type: 'text'
  });

  // 채팅방의 lastMessage 업데이트
  await firebase.database()
    .ref(`rooms/${roomId}/lastMessage`)
    .set({
      text: text,
      senderId: currentUserId,
      timestamp: firebase.database.ServerValue.TIMESTAMP
    });
}
```

## 보안 규칙 설정

Firebase Realtime Database의 보안 규칙은 데이터 접근을 제어합니다.

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "auth != null",
        ".write": "$uid === auth.uid"
      }
    },
    "rooms": {
      "$roomId": {
        ".read": "auth != null && root.child('rooms').child($roomId).child('members').child(auth.uid).exists()",
        ".write": "auth != null && root.child('rooms').child($roomId).child('members').child(auth.uid).exists()"
      }
    },
    "messages": {
      "$roomId": {
        ".read": "auth != null && root.child('rooms').child($roomId).child('members').child(auth.uid).exists()",
        ".write": "auth != null && root.child('rooms').child($roomId).child('members').child(auth.uid).exists()"
      }
    },
    "userRooms": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

## 성능 최적화 고려사항

### 1. 데이터 비정규화
- 자주 접근하는 데이터는 여러 위치에 중복 저장
- 예: 채팅방의 `lastMessage`는 별도 쿼리 없이 빠른 미리보기 제공

### 2. 얕은 쿼리 사용
- 큰 데이터셋을 가져올 때는 `shallow=true` 파라미터 사용
- 하위 데이터 없이 키만 가져와 성능 향상

### 3. 페이지네이션
- `limitToLast()`, `startAt()`, `endAt()` 등을 사용한 메시지 페이징
- 초기 로딩 시 최근 메시지만 불러오기

### 4. 인덱싱
- 자주 쿼리하는 필드에 대해 인덱스 설정
- Firebase 콘솔에서 자동으로 제안되는 인덱스 활용

## 데이터 동기화

### 오프라인 지원
Firebase RTDB는 자동으로 오프라인 지원을 제공합니다:
- 로컬 캐싱을 통한 오프라인 데이터 읽기
- 재연결 시 자동 동기화
- 미전송 쓰기 작업 큐잉

### 충돌 해결
- 타임스탬프 기반 Last-Write-Wins 전략
- 트랜잭션을 사용한 원자적 업데이트
- 낙관적 업데이트 후 서버 응답으로 최종 확정

## 마이그레이션 및 백업

### 데이터 내보내기
- Firebase 콘솔의 내보내기 기능 사용
- REST API를 통한 프로그래매틱 백업

### 데이터 가져오기
- JSON 파일을 통한 일괄 가져오기
- 스크립트를 사용한 점진적 마이그레이션

## 모범 사례

1. **키 명명 규칙**: camelCase 사용 (예: `displayName`, `photoUrl`)
2. **타임스탬프**: 서버 타임스탬프 사용 (`ServerValue.timestamp`)
3. **중복 데이터 최소화**: 필요한 경우에만 비정규화
4. **보안 규칙 테스트**: 프로덕션 배포 전 충분한 테스트
5. **모니터링**: Firebase 콘솔의 사용량 모니터링 활용