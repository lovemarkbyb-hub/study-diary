# 공부 다이어리 배포 가이드 (상세판)

이 폴더의 `index.html`을 인터넷에 올려서, 로그인 없이 링크만 열어도 되고 기록은 클라우드(Firebase)에 저장되어 폰/태블릿/PC 어디서든 같은 내용을 보게 만드는 전체 과정입니다. 순서대로만 따라 하면 됩니다. 막히는 부분이 생기면 그 단계 번호를 알려주세요.

전체 그림: **① Firebase(데이터 저장소) 만들기 → ② 설정값을 코드에 붙여넣기 → ③ GitHub(코드 저장/배포) 계정 준비 → ④ 코드 올리기 → ⑤ GitHub Pages로 공개 링크 만들기 → ⑥ 테스트**

---

## ① Firebase 프로젝트 만들기

### 0단계 — 구글 계정 확인

Firebase는 구글 계정만 있으면 됩니다(새로 가입할 필요 없음). 지메일 계정이 있다면 바로 다음으로 넘어가세요. 없다면 https://accounts.google.com/signup 에서 먼저 하나 만드세요.

### 1단계 — 콘솔 접속 & 로그인

1. 브라우저 주소창에 **`console.firebase.google.com`** 을 입력해 이동합니다.
2. 로그인이 안 되어 있으면 구글 로그인 화면이 뜹니다 → 이메일/비밀번호 입력.
3. 처음 방문하는 경우 "Firebase 서비스 약관" 동의 화면이 뜰 수 있습니다 → 체크박스 두 개(약관 동의 / 스팸 방지 확인) 체크 → **동의합니다**(또는 **계속**) 클릭.
4. 로그인 후 보이는 화면이 "프로젝트 대시보드"입니다. 이전에 만든 프로젝트가 없다면 가운데 큰 카드 하나만 보일 거예요.

### 2단계 — 새 프로젝트 만들기 마법사

5. 가운데(또는 왼쪽 위) **"프로젝트 만들기"** 카드나 버튼을 클릭합니다. (예전 버전에는 "프로젝트 추가"라고 되어 있을 수도 있어요 — 같은 기능입니다.)
6. **프로젝트 이름** 입력창이 뜹니다. `study-diary` 라고 입력하세요.
   - 입력하는 순간 그 아래 작은 글씨로 `study-diary-a1b2c` 처럼 프로젝트 ID가 자동으로 만들어집니다. 이건 신경 쓰지 않아도 됩니다(전 세계에서 겹치지 않게 자동으로 붙는 식별자일 뿐).
   - 아래 이용약관 체크박스가 있다면 체크하고 **계속**을 클릭합니다.
7. (버전에 따라) **"이 프로젝트에서 Gemini 사용"** 같은 AI 관련 안내 화면이 나올 수 있습니다 — 우리는 필요 없는 기능이니 그냥 **계속**을 눌러 넘어갑니다.
8. **Google 애널리틱스** 화면이 나옵니다. 토글이 켜져 있다면 클릭해서 꺼주세요(회색으로 바뀌면 꺼진 것). 방문자 통계 기능인데 이 앱에는 필요 없어서, 꺼두면 다음 화면(애널리틱스 계정 선택)을 건너뛰고 바로 진행됩니다.
9. **"프로젝트 만들기"** 버튼을 클릭합니다.
10. 화면 가운데 로딩 애니메이션(작은 불꽃놀이 아이콘)이 몇십 초간 돌아간 뒤 "새 프로젝트가 준비되었습니다" 메시지가 뜹니다 → **계속**을 클릭합니다.
11. Firebase 콘솔의 **프로젝트 홈 화면**으로 이동합니다. 왼쪽에 세로로 늘어선 아이콘 메뉴(빌드, 실행, 분석 등)가 보이면 프로젝트가 정상적으로 만들어진 것입니다.

여기까지 되면 화면 캡처를 보내주셔도 되고, "여기까지 됐어" 라고만 알려주셔도 다음 단계(Firestore Database 켜기)를 이어서 안내해드릴게요.

### Firestore Database 켜기

7. 왼쪽 사이드바에서 **빌드(Build)** 메뉴를 펼치고 **Firestore Database**를 클릭합니다.
8. 가운데 **"데이터베이스 만들기"** 버튼 클릭.
9. 위치 선택 화면에서 `asia-northeast3 (서울)`을 선택하고 **다음**.
10. 보안 규칙 시작 모드는 **"테스트 모드에서 시작"**을 선택하고 **만들기**. (규칙은 잠시 후 바로 교체할 거라 상관없습니다)
11. 몇 초 후 빈 Firestore Database 화면이 나타납니다.

### 보안 규칙 교체하기

12. Firestore Database 화면 상단 탭에서 **규칙(Rules)** 탭을 클릭합니다.
13. 편집창에 있는 기존 내용을 전부 지우고, 아래 내용을 그대로 붙여넣습니다:

    ```
    rules_version = '2';
    service cloud.firestore {
      match /databases/{database}/documents {
        match /users/{userId}/days/{dayId} {
          allow read, write: if userId in ["재인", "재희"];
        }
      }
    }
    ```

14. 오른쪽 위 **게시(Publish)** 버튼을 클릭합니다. "규칙이 게시되었습니다" 메시지가 뜨면 완료입니다.

> 이 규칙의 의미: `users/재인/...` 와 `users/재희/...` 경로만 누구나(로그인 없이) 읽고 쓸 수 있고, 그 외 경로는 전부 막혀 있습니다. 나중에 아이 이름을 바꾸거나 늘리려면 이 목록(`["재인","재희"]`)과 `index.html`의 `USERS` 목록을 함께 고치면 됩니다.

### 웹 앱 등록하고 설정값 복사하기

15. 왼쪽 사이드바 맨 위, 프로젝트 이름 옆의 **톱니바퀴 아이콘** → **프로젝트 설정**을 클릭합니다.
16. "일반" 탭 아래로 스크롤하면 **"내 앱"** 섹션이 있습니다. 앱이 하나도 없으면 `</>` 모양(웹) 아이콘을 클릭합니다.
17. 앱 닉네임에 아무 이름(예: `study-diary-web`)을 입력하고, "Firebase 호스팅도 설정" 체크박스는 **체크하지 않습니다**. **앱 등록** 클릭.
18. 화면에 아래와 같은 코드 블록이 나타납니다. 이 안의 `firebaseConfig = { ... }` 부분 전체를 복사해두세요 (다음 단계에서 씁니다):

    ```js
    const firebaseConfig = {
      apiKey: "AIza...",
      authDomain: "study-diary-xxxxx.firebaseapp.com",
      projectId: "study-diary-xxxxx",
      storageBucket: "study-diary-xxxxx.appspot.com",
      messagingSenderId: "123456789000",
      appId: "1:123456789000:web:abcdef123456"
    };
    ```

19. "콘솔로 이동" 버튼을 눌러 마무리합니다. (호스팅 관련 안내가 나와도 무시하고 넘어가면 됩니다 — 우리는 Firebase 호스팅이 아니라 GitHub Pages를 쓸 거예요.)

---

## ② 복사한 설정값을 코드에 붙여넣기

1. VS Code(또는 지금 이 파일을 보고 있는 에디터)에서 같은 폴더의 **`index.html`**을 엽니다.
2. `Ctrl+F`로 `firebaseConfig`를 검색하면 아래와 같은 부분이 나옵니다 (파일 하단, `<script>` 태그 안쪽 위쪽):

   ```js
   var firebaseConfig = {
     apiKey: "여기에_API_KEY를_붙여넣으세요",
     authDomain: "여기에_PROJECT_ID.firebaseapp.com",
     projectId: "여기에_PROJECT_ID",
     storageBucket: "여기에_PROJECT_ID.appspot.com",
     messagingSenderId: "여기에_SENDER_ID",
     appId: "여기에_APP_ID"
   };
   ```

3. 이 `{ ... }` 블록 전체를 지우고, ①-18에서 복사해둔 실제 값으로 교체합니다. (`const`를 `var`로 바꿀 필요 없이, `{ ... }` 안의 6개 값만 그대로 옮기면 됩니다.)
4. 파일을 저장합니다 (`Ctrl+S`).

이 파일을 저에게 다시 보여주시면 값이 잘 들어갔는지 확인해드릴게요.

---

## ③ GitHub 계정 & 로그인 준비

이미 GitHub 계정이 있으면 이 단계는 건너뛰세요.

1. **https://github.com/signup** 에서 이메일/비밀번호로 무료 계정을 만듭니다.
2. 터미널(PowerShell)을 열고 아래 명령을 입력합니다:

   ```
   gh auth login
   ```

3. 순서대로 나오는 질문에 이렇게 답하면 됩니다 (화살표 키로 선택, Enter로 확정):
   - `What account do you want to log into?` → **GitHub.com**
   - `What is your preferred protocol for Git operations?` → **HTTPS**
   - `Authenticate Git with your GitHub credentials?` → **Yes**
   - `How would you like to authenticate?` → **Login with a web browser**
4. 화면에 8자리 코드(예: `ABCD-1234`)가 뜨고, **Enter를 누르면 브라우저가 자동으로 열립니다.** 그 코드를 브라우저 화면에 붙여넣고 **Continue → Authorize**를 누릅니다.
5. 터미널로 돌아오면 `✓ Logged in as 내계정` 이라는 메시지가 뜹니다. 이걸로 로그인 완료입니다.
6. 확인하려면: `gh auth status` 를 입력했을 때 `Logged in to github.com account 내계정` 이 보이면 정상입니다.

**여기까지 되면 알려주세요 — 이후 ④, ⑤의 명령어들은 제가 대신 실행해드릴 수 있어요.**

---

## ④ 코드를 GitHub에 올리기

이 폴더(`study-diary-app`)에서 아래 명령을 순서대로 실행합니다:

```
git init
git add index.html README.md
git commit -m "공부 다이어리 초기 배포"
gh repo create study-diary --public --source=. --remote=origin --push
```

- `git init`: 이 폴더를 저장소로 만듭니다.
- `git add` / `git commit`: 파일들을 저장(커밋)합니다.
- `gh repo create ...`: GitHub에 `study-diary`라는 새 저장소를 만들고, 방금 커밋한 내용을 바로 올립니다(`--push`).

명령이 끝나면 `https://github.com/내계정/study-diary` 주소로 저장소가 만들어져 있을 거예요.

---

## ⑤ GitHub Pages로 공개 링크 만들기

1. 브라우저에서 `https://github.com/내계정/study-diary` 로 이동합니다.
2. 저장소 메뉴에서 **Settings** 탭 클릭.
3. 왼쪽 사이드바에서 **Pages** 클릭.
4. **"Build and deployment"** 섹션의 Source를 **"Deploy from a branch"**로 두고, 아래 Branch 드롭다운에서 **`main`** / **`/ (root)`**를 선택한 뒤 **Save**.
5. 1~2분 기다리면 같은 페이지 위쪽에 초록색 박스로 **"Your site is live at `https://내계정.github.io/study-diary/`"** 라는 메시지가 뜹니다. 이 주소가 최종 링크입니다.

이 링크를 카카오톡 등으로 보내거나, 아이들 폰에서 열어 **홈 화면에 추가**(사파리: 공유 → 홈 화면에 추가 / 크롬: 메뉴 → 홈 화면에 추가)해두면 앱처럼 아이콘을 눌러 쓸 수 있어요.

---

## ⑥ 잘 되는지 확인하기

1. PC 브라우저에서 그 링크를 열고 **재인**을 선택 → 아무 계획이나 하나 추가 → 화면 아래 "저장 중…" 이 "OO시 OO분에 저장됨 · 모든 기기에 동기화"로 바뀌는지 확인.
2. 폰에서 같은 링크를 열고 똑같이 **재인**을 선택하면, 방금 PC에서 추가한 계획이 그대로 보여야 합니다.
3. 안 보이면 아래 문제 해결 섹션을 확인하세요.

---

## 문제 해결

| 증상 | 원인 / 해결 |
|---|---|
| 첫 화면에 "설정이 아직 끝나지 않았어요" 경고가 보임 | `index.html`의 `firebaseConfig`가 아직 예시값(`여기에_...`) 그대로임 → ② 단계 다시 확인 |
| "저장 실패 - 인터넷 연결을 확인해주세요" | Firestore 규칙에서 이름 목록(`userId in [...]`)에 오타가 있거나, 아직 규칙을 게시하지 않음 → ①-14 다시 확인 |
| `gh: command not found` | GitHub CLI가 설치 안 됨 → https://cli.github.com 에서 설치 후 터미널 재시작 |
| 폰에서 열었을 때 하얀 화면만 나옴 | GitHub Pages가 아직 배포 중일 수 있음(1~2분 소요) → 새로고침, 그래도 안되면 Settings > Pages에서 상태 확인 |
| 기록이 기기마다 다르게 보임 | 서로 다른 이름(재인/재희)을 선택했거나, 한쪽이 아직 예전(로컬 저장) 버전 링크를 쓰고 있는 것 → 같은 GitHub Pages 링크인지, 같은 이름을 선택했는지 확인 |

## 참고

- 로그인이 필요 없는 대신, Firestore 보안 규칙으로 문서 경로(`users/재인/...`, `users/재희/...`)만 열어둔 상태라 완전한 비밀번호 보호는 아닙니다. 링크는 가족 외에 공유하지 마세요.
- 무료 요금제(Spark) 기준 하루 read/write 한도가 넉넉해서 가정에서 쓰기엔 비용이 들지 않습니다.
- 이름을 추가/변경하려면 Firestore 규칙의 `userId in [...]` 목록과 `index.html`의 `var USERS = [...]`을 함께 수정하고, GitHub에 다시 올려야(재배포) 반영됩니다 (`git add -A && git commit -m "update" && git push`).
