# JavaScript로 채팅 기능 개발을 위한 초기 설정

이 문서는 JavaScript와 Alpine.js를 사용하여 채팅 기능을 개발하기 위한 초기 설정 방법을 안내합니다.

## 개요

OpenChat 프로젝트에서 제공하는 채팅 기능을 JavaScript로 구현하기 위해서는 다음과 같은 라이브러리가 필요합니다:
- **Alpine.js**: 반응형 UI 구성을 위한 경량 JavaScript 프레임워크
- **Bootstrap**: UI 컴포넌트와 스타일링을 위한 CSS 프레임워크
- **Firebase JS SDK**: 실시간 데이터베이스와 인증 기능 사용

## HTML 파일 구조

JavaScript로 채팅 기능을 개발하기 위한 기본 HTML 구조는 다음과 같습니다:

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>OpenChat - 채팅 애플리케이션</title>

    <!-- Alpine.js 로드 (상단에 위치) -->
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>

    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

    <!-- 커스텀 스타일 (선택사항) -->
    <style>
        /* 채팅 UI 관련 커스텀 스타일 */
        .chat-container {
            height: 100vh;
            display: flex;
            flex-direction: column;
        }

        .messages-area {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
        }

        .message {
            margin-bottom: 15px;
            padding: 10px;
            border-radius: 8px;
        }

        .message.sent {
            background-color: #007bff;
            color: white;
            margin-left: auto;
            max-width: 70%;
        }

        .message.received {
            background-color: #f1f3f5;
            max-width: 70%;
        }
    </style>
</head>
<body>
    <div class="container-fluid chat-container">
        <!-- 채팅 UI 컴포넌트들이 여기에 위치 -->
        <div class="messages-area" id="messagesArea">
            <!-- 메시지들이 동적으로 렌더링됩니다 -->
        </div>

        <div class="input-area p-3 border-top">
            <!-- 메시지 입력 폼 -->
        </div>
    </div>

    <!-- Bootstrap JavaScript Bundle (Popper 포함) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

    <!-- Firebase SDK v9 (모듈형) -->
    <script type="module">
        // Firebase 모듈 import
        import { initializeApp } from 'https://www.gstatic.com/firebasejs/10.7.0/firebase-app.js';
        import { getDatabase, ref, onValue, push, set } from 'https://www.gstatic.com/firebasejs/10.7.0/firebase-database.js';
        import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, onAuthStateChanged } from 'https://www.gstatic.com/firebasejs/10.7.0/firebase-auth.js';

        // Firebase 설정 객체 (Firebase 콘솔에서 가져온 설정값)
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_AUTH_DOMAIN",
            databaseURL: "YOUR_DATABASE_URL",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_STORAGE_BUCKET",
            messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
            appId: "YOUR_APP_ID"
        };

        // Firebase 초기화
        const app = initializeApp(firebaseConfig);
        const database = getDatabase(app);
        const auth = getAuth(app);

        // 전역 객체에 Firebase 인스턴스 저장 (Alpine.js에서 사용하기 위함)
        window.firebaseApp = app;
        window.firebaseDatabase = database;
        window.firebaseAuth = auth;

        // Alpine.js 데이터 컴포넌트 정의
        document.addEventListener('alpine:init', () => {
            Alpine.data('chatApp', () => ({
                // 채팅 애플리케이션의 상태 관리
                user: null,
                messages: [],
                newMessage: '',
                currentRoom: 'general', // 기본 채팅방

                // 초기화 함수
                init() {
                    // 인증 상태 변경 감지
                    onAuthStateChanged(auth, (user) => {
                        if (user) {
                            this.user = user;
                            this.listenToMessages();
                        } else {
                            this.user = null;
                            this.messages = [];
                        }
                    });
                },

                // 메시지 실시간 리스닝
                listenToMessages() {
                    const messagesRef = ref(database, `messages/${this.currentRoom}`);

                    onValue(messagesRef, (snapshot) => {
                        const data = snapshot.val();
                        if (data) {
                            this.messages = Object.entries(data).map(([key, value]) => ({
                                id: key,
                                ...value
                            }));
                        }
                    });
                },

                // 메시지 전송
                async sendMessage() {
                    if (!this.newMessage.trim() || !this.user) return;

                    const messagesRef = ref(database, `messages/${this.currentRoom}`);
                    const newMessageRef = push(messagesRef);

                    await set(newMessageRef, {
                        text: this.newMessage,
                        userId: this.user.uid,
                        userName: this.user.displayName || this.user.email,
                        timestamp: Date.now(),
                        roomId: this.currentRoom
                    });

                    this.newMessage = '';
                },

                // 로그인
                async login(email, password) {
                    try {
                        await signInWithEmailAndPassword(auth, email, password);
                    } catch (error) {
                        console.error('로그인 에러:', error);
                        alert('로그인 실패: ' + error.message);
                    }
                },

                // 회원가입
                async signup(email, password) {
                    try {
                        await createUserWithEmailAndPassword(auth, email, password);
                    } catch (error) {
                        console.error('회원가입 에러:', error);
                        alert('회원가입 실패: ' + error.message);
                    }
                }
            }));
        });
    </script>

    <!-- 채팅 애플리케이션 스크립트 -->
    <script>
        // 추가적인 JavaScript 로직을 여기에 작성
        // 예: 유틸리티 함수, 헬퍼 함수 등

        // 메시지 영역 자동 스크롤
        function scrollToBottom() {
            const messagesArea = document.getElementById('messagesArea');
            messagesArea.scrollTop = messagesArea.scrollHeight;
        }

        // 시간 포맷팅 함수
        function formatTime(timestamp) {
            const date = new Date(timestamp);
            return date.toLocaleTimeString('ko-KR', {
                hour: '2-digit',
                minute: '2-digit'
            });
        }
    </script>
</body>
</html>
```

## 주요 구성 요소 설명

### 1. Alpine.js 설정
- `defer` 속성과 함께 `<head>` 태그에 로드
- 반응형 UI 구성을 위한 경량 프레임워크
- `x-data`, `x-show`, `x-for` 등의 디렉티브를 사용하여 동적 UI 구현

### 2. Bootstrap 설정
- CSS는 `<head>` 태그에 로드
- JavaScript Bundle은 `</body>` 태그 직전에 로드
- Popper.js가 포함된 bundle 버전 사용 (드롭다운, 툴팁 등 지원)

### 3. Firebase 설정
- ES6 모듈 방식으로 Firebase SDK 로드
- `initializeApp()`으로 Firebase 앱 초기화
- Realtime Database와 Authentication 서비스 활성화
- Firebase 설정값은 Firebase 콘솔에서 프로젝트 설정 > 일반 > 내 앱에서 확인 가능

## Firebase 프로젝트 설정 방법

1. [Firebase 콘솔](https://console.firebase.google.com)에 접속
2. 새 프로젝트 생성 또는 기존 프로젝트 선택
3. 웹 앱 추가 (</> 아이콘 클릭)
4. 앱 등록 후 제공되는 설정 코드 복사
5. `firebaseConfig` 객체에 설정값 입력

## Alpine.js를 활용한 채팅 UI 구현 예제

```html
<div x-data="chatApp" class="container-fluid chat-container">
    <!-- 로그인하지 않은 경우 -->
    <div x-show="!user" class="login-form p-4">
        <h2>로그인</h2>
        <input type="email" x-model="email" class="form-control mb-2" placeholder="이메일">
        <input type="password" x-model="password" class="form-control mb-2" placeholder="비밀번호">
        <button @click="login(email, password)" class="btn btn-primary">로그인</button>
        <button @click="signup(email, password)" class="btn btn-secondary">회원가입</button>
    </div>

    <!-- 로그인한 경우 -->
    <div x-show="user" class="d-flex flex-column h-100">
        <!-- 메시지 영역 -->
        <div class="messages-area flex-grow-1 overflow-auto p-3">
            <template x-for="message in messages" :key="message.id">
                <div class="message mb-3"
                     :class="message.userId === user?.uid ? 'sent text-end' : 'received'">
                    <div class="message-content p-2 rounded"
                         :class="message.userId === user?.uid ? 'bg-primary text-white' : 'bg-light'">
                        <div class="fw-bold" x-text="message.userName"></div>
                        <div x-text="message.text"></div>
                        <small class="text-muted" x-text="formatTime(message.timestamp)"></small>
                    </div>
                </div>
            </template>
        </div>

        <!-- 메시지 입력 영역 -->
        <div class="input-area p-3 border-top">
            <div class="input-group">
                <input type="text"
                       x-model="newMessage"
                       @keydown.enter="sendMessage"
                       class="form-control"
                       placeholder="메시지를 입력하세요...">
                <button @click="sendMessage" class="btn btn-primary">전송</button>
            </div>
        </div>
    </div>
</div>
```

## 보안 고려사항

1. **Firebase 보안 규칙 설정**
   - Realtime Database 규칙을 적절히 설정하여 인증된 사용자만 접근 가능하도록 설정
   - 각 사용자가 자신의 데이터만 수정할 수 있도록 제한

2. **환경 변수 사용**
   - Firebase 설정값을 환경 변수로 관리
   - 프로덕션 환경에서는 설정값을 서버에서 제공받도록 구성

3. **입력값 검증**
   - XSS 공격 방지를 위한 입력값 살균(sanitization)
   - 메시지 길이 제한 설정

## 다음 단계

1. [사용자 인증 구현](firebase-user.md) - 이메일/비밀번호, 소셜 로그인 등
2. [데이터베이스 구조 설계](database-structure.md) - 채팅방, 메시지, 사용자 정보 구조
3. [실시간 메시지 구현](concepts.md) - 메시지 송수신, 읽음 확인, 타이핑 인디케이터
4. [푸시 알림 설정](push-notification-api.md) - FCM을 활용한 푸시 알림

## 트러블슈팅

### CORS 에러 발생 시
로컬 개발 환경에서 CORS 에러가 발생하는 경우:
- 로컬 서버 사용 (예: Live Server VS Code 확장)
- Firebase Hosting에 배포하여 테스트

### Alpine.js가 작동하지 않는 경우
- `defer` 속성 확인
- `alpine:init` 이벤트가 제대로 발생하는지 확인
- 브라우저 콘솔에서 에러 메시지 확인

### Firebase 연결 실패
- Firebase 프로젝트 설정값 확인
- Realtime Database가 활성화되어 있는지 확인
- 보안 규칙이 올바르게 설정되어 있는지 확인