# 커넥트닷츠 (Connect Dots)

> 서로 다른 진로(점)들이 연결되어 하나의 선을 완성하는 협동 독서 활동 웹앱

고등학교 2학년 창의적 체험활동 **'진로 독서 × 융합 나눔'** 을 위해 만든 도구입니다.
학생이 매 차시 입력한 내용이 실시간으로 저장되고, 마지막에 **생기부(창체 특기사항) 원문 한 단락**으로 자동 조립되어 엑셀로 나옵니다.

- **학생용** — `https://persian32.github.io/connectdots/`
- **교사용** — `https://persian32.github.io/connectdots/teacher.html` (학생에게 공유하지 않음)

---

## 1. 소개 — 이 활동은 무엇인가

각자 자기 진로와 연결된 책을 읽고, **하나의 공통 질문**에 대한 '내 분야의 답'을 찾아,
서로 다른 진로의 친구들과 나누고 **연결(융합)** 하여 시각을 넓히는 협동 독서 프로젝트입니다.
최종적으로 여러 진로의 관점을 한 지면에 엮은 **모둠 신문**을 만듭니다.

### 학생 흐름

| 단계 | 하는 일 | 형태 |
|---|---|---|
| 입장 | 반 코드 → 이름·출석번호 확인 → **모둠 결성** | 개인 |
| 1차시 | 공통 대질문 선택 → 내 진로 언어의 소질문으로 변환 → 책 선정 | 개인 |
| 2차시 | 책이 내 소질문에 준 답 정리 | 개인 |
| 3차시 | 모둠 나눔 — 친구에게 배운 관점 기록 (최대 3명) | 대면 + 기록 |
| 4차시 | **실시간 마인드맵** — 모둠 전원의 모든 짝을 연결하고 연결점 작성 + 융합 씨앗 | 모둠 협동 |
| 5차시 | **모둠 신문 원고** — 개인 칼럼(전원) + 공동 지면(담당제) + 완성 신문 이미지 제출 | 모둠 협동 |
| 6차시 | 성찰 3축 (나눔 전후 변화 / 구체적 연결점 / 진로로의 회귀) | 개인 |
| 완료 | 전 차시가 한 단락으로 조립된 미리보기 확인 | 개인 |

### 핵심 설계

- **모둠은 학생이 스스로 결성** — 모둠명을 직접 입력. 없는 이름이면 새로 결성(첫 입력자가 방장), 있는 이름이면 참여. 정원 4명. 모둠명은 **대소문자·띄어쓰기·부호까지 완전 일치**해야 같은 모둠(앞뒤 공백만 자동 제거).
- **한 번 들어간 모둠은 기억** — 다음부터 로그인만 하면 자기 모둠으로 자동 입장. '모둠 바꾸기'로 초반 재편성 가능.
- **각자 조각을 기여해야 완성** — 마인드맵 연결점에 작성자 태그, 신문 개인 칼럼은 전원 필수. 안 쓰면 원고에 `(아직 안 씀)`으로 표시되어 협업이 강제됨.
- **공동 지면은 1인 1담당제** — 헤드라인·부제 / 융합 코너 / 광고 중 하나씩 맡음. 담당자만 편집 가능 → 덮어쓰기 방지.
- **디자인은 각자 도구로** — 원고를 복사해 캔바·미리캔버스 등에서 꾸민 뒤, 완성 이미지를 앱에 제출.

### 교사 대시보드

- 반 코드로 불러오기 → **명단 대조** 실시간 현황 (미입력 학생 즉시 확인)
- 학생별 상세 — 차시별 입력 전문 + **생기부 원문 미리보기 + 나이스 바이트 수**(1200 초과 시 빨강)
- **명단 관리** — 엑셀에서 `번호 · 이름` 복사해 붙여넣기
- **모둠 신문** — 제출된 모둠 신문 갤러리 (모둠별 제출/미제출 현황)
- **엑셀 내보내기** — `이름` / `글내용(통합)` 2열 → `changche-cleaner` 스킬에 그대로 투입

### 생기부 파이프라인

```
학생 입력 → Firestore 저장 → 대시보드에서 '글내용(통합)' 자동 조립
         → 엑셀(이름/글내용(통합)) → changche-cleaner 스킬 → 창체 특기사항
```

**생기부 원문에 포함되는 것**: 진로·소질문·책 / 독서 답 / 나눔 관점 / 마인드맵 연결점 / 융합 씨앗 / 성찰 3축
**포함되지 않는 것**: 신문 개인 칼럼(1·2차시와 중복이라 제외 — 대시보드에는 참고용으로 표시)

---

## 2. 개발 — 구조와 설정

### 파일 구조

```
connectdots/
├── index.html        학생용 (단일 파일: HTML+CSS+JS)
├── teacher.html      교사 대시보드 (단일 파일)
├── cake-sticker.png  완료 화면 응원 스티커
└── README.md
```

빌드 도구 없음. 정적 파일을 GitHub Pages로 배포하고, 데이터만 Firebase Firestore에 저장한다.

### 기술 구성

- **프론트엔드** — 순수 HTML/CSS/JS, 라이브러리 없음. Firebase JS SDK 12.16.0을 CDN 모듈로 로드.
- **백엔드** — Firebase **Firestore만** 사용 (Firebase 프로젝트명은 `connectdot`으로, 저장소명 `connectdots`와 다름 / 서울 `asia-northeast3` / Spark 무료).
- **인증 없음** — 반 코드 + 명단 대조(이름+출석번호)로 확인. 담임 학급용 수준의 통제.
- **엑셀** — SheetJS(xlsx) CDN.
- **이미지** — Cloud Storage를 쓰지 않고 **Firestore에 압축 저장**(base64). 2026년 2월부터 Storage는 Blaze(카드 등록) 필수라 무카드 원칙을 지키기 위한 선택. 업로드 시 브라우저에서 1200×1700 이내·약 420KB 이하로 자동 축소.

### 데이터 구조

```
responses/{classCode}__{number}
  classCode, number, name, groupName, groupKey, updatedAt
  data: { bigq, career, subq, bookTitle, bookAuthor, answer, quote,
          f1name~f3view, cLink, seed, npColumn, r1, r2, r3 }

classes/{classCode}
  classCode, roster: [{ no, name }], updatedAt

groups/{classCode}__{모둠명}
  classCode, name, nameKey, members: [{ number, name }], updatedAt
  └ links/{번호_번호}   a, b, text, byNumber, byName, updatedAt   ← 마인드맵 연결점
  └ paper/main          npHead, npSub, npFusion, npAd, roles{}    ← 신문 공동 지면·담당
  └ paper/image         dataUrl, byNumber, byName, at             ← 제출된 완성 신문
```

- 학생 문서 키가 **번호 기준**이라 동명이인이 안전하게 분리된다.
- 데이터 모델은 다중 교사 확장을 염두에 두고 일반화되어 있다(반 코드가 최상위 구분자).

### Firebase 설정 (새로 만들 때)

1. [Firebase 콘솔](https://console.firebase.google.com)에서 프로젝트 생성 (Analytics 불필요)
2. **Firestore Database** 생성 — 위치 `asia-northeast3`(서울), 테스트 모드로 시작
3. 프로젝트 개요 → **+ 앱 추가** → 웹 `</>` → 닉네임 입력 (Hosting 체크 안 함)
4. 나온 `firebaseConfig`를 `index.html`과 `teacher.html`의 모듈 스크립트에 붙여넣기
5. 아래 보안 규칙을 게시 (테스트 모드의 30일 만료를 없애기 위해 반드시 필요)

> `apiKey`는 비밀이 아니다. 웹 앱에서 공개되도록 설계된 식별자이며, 실제 방어선은 보안 규칙이다.

### 보안 규칙

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /responses/{docId} {
      allow read: if true;
      allow create, update: if isValidResponse(request.resource.data);
      allow delete: if false;
    }

    match /classes/{classCode} {
      allow read: if true;
      allow create, update: if request.resource.data.classCode is string
                            && request.resource.data.roster is list;
      allow delete: if false;
    }

    match /groups/{gid} {
      allow read: if true;
      allow create, update: if request.resource.data.classCode is string
                            && request.resource.data.members is list
                            && request.resource.data.members.size() <= 4;
      allow delete: if false;
    }

    match /groups/{gid}/links/{linkId} {
      allow read: if true;
      allow write: if true;
    }

    match /groups/{gid}/paper/{docId} {
      allow read: if true;
      allow write: if true;
    }

    match /{document=**} { allow read, write: if false; }

    function isValidResponse(d) {
      return d.name is string && d.name.size() > 0 && d.name.size() <= 40
        && d.classCode is string && d.classCode.size() <= 30
        && d.data is map;
    }
  }
}
```

**한계(의도된 것)** — 로그인이 없으므로 **읽기는 열려 있다.** 주소를 아는 사람은 학생 글을 읽을 수 있다. 삭제 금지·형식 검증·경로 차단으로 쓰기 쪽만 통제한다. 담임 학급이 독서 성찰을 짧은 기간 다루는 맥락이라 이 수준이 적정선이며, 다중 교사로 확장할 경우 Firebase Authentication(구글 로그인)을 얹어야 한다.

### 배포

GitHub Pages. 저장소에 파일을 올리고 Settings → Pages → Branch `main` / `/(root)` → Save.
`<meta name="robots" content="noindex, nofollow">`로 검색 노출을 차단한다.

### 사용 순서 (교사)

1. 대시보드 → 반 코드 입력 → **명단 관리**에서 `번호 이름` 붙여넣고 저장
2. 학생에게 학생용 링크 + 반 코드 안내 (모둠명은 칠판에 적어두면 대소문자 사고 예방)
3. 활동 중 대시보드로 실시간 모니터링 (미입력·모둠 결성 현황)
4. 종료 후 **엑셀 내보내기** → `changche-cleaner` 스킬로 생기부 문체 정리

### 알려진 제약

- 아이폰 HEIC 사진은 업로드 불가 (JPG/PNG로 변환 필요) — 앱이 안내 메시지 표시
- 신문 이미지는 화면 확인용 화질. 인쇄용 원본은 별도 수령 필요
- 모둠명 완전 일치 정책상, 대소문자가 다르면 다른 모둠이 됨 (대시보드에서 확인 가능)

---

## 라이선스 / 문의

교실 활동용으로 자유롭게 복제·수정 가능.
