# Budapest 2027 Travel Planner

웹사이트: https://seungmiyou-ux.github.io/budapest-trip/

## PC와 휴대폰 자동 동기화: 최초 연결 안내

기본 페이지는 여전히 **이 브라우저에만 저장**됩니다. 동기화를 사용하려면 본인이 Firebase 프로젝트를 만든 다음, 아래 공개 웹 앱 설정을 사이트 코드에 한 번 반영해야 합니다.

1. https://console.firebase.google.com/ 에서 Firebase 프로젝트를 생성합니다.
2. 프로젝트 설정 > 일반 > 앱 추가(웹 앱)에서 Firebase 설정 객체(firebaseConfig)를 확인합니다.
3. Authentication > Sign-in method에서 **Google 로그인**을 활성화합니다.
4. Authentication > Settings > Authorized domains에서 `seungmiyou-ux.github.io`를 등록합니다.
5. Firestore Database를 생성하고 아래 **Security Rules**를 적용한 후 게시합니다.

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/planner/{docId} {
      allow read, write: if request.auth != null
        && request.auth.uid == userId
        && docId == 'main';
    }
  }
}
```

**중요:** Firestore에서 `allow read, write: if true` 같이 전체 공개 규칙을 사용하지 마세요.

6. GitHub 저장소의 `index.html`에서 다음 설정을 찾으세요.

```js
window.BUDAPEST_FIREBASE_CONFIG = null;
```

이를 Firebase 콘솔에서 발급받은 웹 앱 설정으로 바꿉니다.

```js
window.BUDAPEST_FIREBASE_CONFIG = {
  apiKey: "프로젝트 API 키",
  authDomain: "프로젝트ID.firebaseapp.com",
  projectId: "프로젝트ID",
  appId: "Firebase 웹 앱 ID",
  messagingSenderId: "메시지 발신 ID"
};
```

이 Firebase 웹 앱 설정은 클라이언트용 식별 정보이며 비밀번호가 아닙니다. **데이터 접근 제어는 Google 로그인과 위 Firestore Rules에 의존**합니다.

7. GitHub에서 파일을 Commit한 뒤 Pages 배포가 성공하면 상단의 **☁ 동기화 설정**을 열어 Google 로그인합니다.

### 기존 PC 체크리스트를 동기화하려면
- **먼저** 기존 체크리스트를 편집한 PC에서 ‘내 데이터 백업’을 눌러 JSON 파일을 보관합니다.
- 같은 PC에서 Google로 로그인한 후 **이 기기 데이터 클라우드에 올리기**를 선택합니다.
- 다른 기기에서는 같은 Google 계정으로 로그인하여 저장된 데이터를 받습니다.
- 연결 후에는 체크리스트, 일정, 여행 예산, 지출 내역, 메모 등이 하나의 문서에 저장됩니다.
- 서로 다른 기기에서 동시에 변경하면 마지막에 저장된 문서가 우선할 수 있습니다. 중요한 변경 전에는 JSON 파일을 백업하세요.

### 문제 해결
- 인증 오류: Google 로그인 제공자와 Authorized domains를 확인하세요.
- permission-denied: Firestore Rules의 사용자 UID 조건을 확인하세요.
- 연결되지 않음: Firebase 설정을 실제 값으로 입력했는지 확인하세요.
- 브라우저 자료가 초기화되면 내 데이터 백업(JSON)으로 복원하세요.

이 구조는 **같은 Google 계정으로 접속하는 개인 기기 간 동기화**입니다. 두 명이 서로 다른 Google 계정으로 공동 편집하는 기능은 별도 작업이 필요합니다.
