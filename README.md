# 오픈챗 (OpenChat)

## 프로젝트 개요
- **프로젝트 명칭**: 오픈챗 (OpenChat)
- **설명**: 바이브코딩으로 채팅 기능을 쉽게 개발하기 위한 채팅 기능 개발 전문 프롬프트를 제공하는 오픈소스 프로젝트
- **라이센스**: MIT License
- **저장소**: [GitHub - OpenChat](https://github.com/thruthesky/openchat)
- **홈페이지**: [OpenChat - Chat Development with VibeCode](https://thruthesky.github.io/openchat/)
- **LLMs TXT 파일 경로**: [llms.txt](https://thruthesky.github.io/openchat/llms.txt)

본 프로젝트는 소셜, 커뮤니티, 기타 각종 웹/앱에 사용되는 채팅 기능을 바이브코드로 개발하기 위한 전문 프롬프트를 제공합니다. 채팅 기능 제작에 필요한 기본 개념과 로직을 설명하며, 특히 Flutter와 Alpine.js를 사용한 예제 코드를 제공합니다.

### 왜 오픈챗인가?
- 많은 웹/앱에서 채팅 기능이 필요합니다. 고객 상담, 문의/답변 등 채팅은 매우 보편적이고 필수적인 기능입니다.
- 채팅 기능을 개발하는 방법은 여러가지가 있습니다: 오픈소스 솔루션, 상용 솔루션, 직접 개발 등
- 본 프로젝트는 누구나 바이브코딩으로 채팅 기능을 직접 개발할 수 있도록, 채팅 기능 개발에 최적화된 프롬프트와 실질적인 로직 및 소스 코드를 제공합니다.

## 기술 스택

### 기본 제공 예제
본 프로젝트에서는 다음 두 가지 기술 스택으로 예제 코드를 제공합니다:
- **Flutter**: 크로스 플랫폼 모바일 앱 개발 (iOS, Android, Web)
- **Alpine.js**: 경량 JavaScript 프레임워크

### 다른 플랫폼 지원
각자의 프로젝트에 맞는 언어와 프레임워크를 선택하여 구현할 수 있습니다:
- **웹 프론트엔드**: React, Vue.js, Angular, Svelte
- **모바일**: React Native, Swift (iOS), Kotlin (Android)
- **백엔드**: Node.js, Python, Java, Go, Ruby
- **실시간 통신**: WebSocket, Socket.io, Firebase, Pusher

## 주요 기능

### 기본 채팅 기능
- **1:1 채팅**: 두 사용자 간의 개인 대화
- **그룹 채팅**: 여러 사용자가 참여하는 단체 대화
- **채팅방 관리**: 채팅방 생성, 수정, 삭제
- **실시간 메시지**: 메시지 전송/수신, 읽음 확인
- **사용자 상태**: 온라인/오프라인 상태, 타이핑 인디케이터

### 미디어 및 파일
- **이미지/동영상 전송**: 미디어 파일 공유
- **파일 첨부**: 문서 및 각종 파일 전송
- **음성 메시지**: 음성 녹음 및 전송
- **썸네일 생성**: 자동 미리보기 생성

### 고급 기능
- **푸시 알림**: FCM을 통한 실시간 알림
- **사용자 초대**: 채팅방 초대 기능
- **방장 권한**: 채팅방 관리자 기능
- **메시지 검색**: 채팅 내역 검색
- **메시지 번역**: 다국어 지원
- **오프라인 지원**: 메시지 동기화

## 문서

### 기본 개념
- [채팅 기능 기본 개념과 로직](docs/concepts.md) - 채팅 기능의 핵심 개념, 실시간 통신, 메시지 처리 로직

### Firebase 관련
- [파이어베이스 프로젝트 관리](docs/firebase-project-management.md)
- [파이어베이스 Realtime Database](docs/firebase-realtime-database.md) - RTDB 소개 및 Firestore 대신 선택하는 이유(비용 절감)
- [파이어베이스 사용자 관리](docs/firebase-user.md)

### 데이터베이스 및 API
- [Firebase RTDB 데이터베이스 구조](docs/database-structure.md)
- [데이터베이스 구조](docs/firebase-database.md)
- [데이터 타입](docs/data-types.md)
- [푸시 알림 API](docs/push-notification-api.md)
- [API 에러 코드](docs/api-error.md)

## 사용 방법

1. **프로젝트 클론**
   ```bash
   git clone https://github.com/thruthesky/openchat.git
   cd openchat
   ```

2. **문서 확인**
   - 필요한 기능에 맞는 문서를 참고하여 개발을 진행합니다
   - 각 문서에는 상세한 구현 가이드와 예제 코드가 포함되어 있습니다

3. **기술 스택 선택**
   - 프로젝트에 맞는 언어와 프레임워크를 선택합니다
   - Flutter와 Alpine.js 예제 코드를 참고하여 구현합니다

4. **Firebase 설정** (선택사항)
   - Firebase 프로젝트를 생성하고 설정합니다
   - Realtime Database, Authentication 등을 구성합니다

5. **개발 시작**
   - 제공된 프롬프트와 예제 코드를 활용하여 채팅 기능을 구현합니다
   - 바이브코딩 도구를 활용하면 더욱 빠른 개발이 가능합니다

## 기여하기

오픈챗은 오픈소스 프로젝트입니다. 다음과 같은 방법으로 기여할 수 있습니다:

- 버그 리포트 및 기능 제안
- 문서 개선 및 번역
- 새로운 예제 코드 추가
- 다른 언어/프레임워크 지원 추가

자세한 내용은 [CONTRIBUTING.md](CONTRIBUTING.md)를 참고해주세요.

## 라이센스

이 프로젝트는 MIT 라이센스 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참고하세요.

## 문의

- GitHub Issues: [https://github.com/thruthesky/openchat/issues](https://github.com/thruthesky/openchat/issues)
- 홈페이지: [https://thruthesky.github.io/openchat/](https://thruthesky.github.io/openchat/)