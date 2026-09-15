# 🌳 Forest of Learning Frontend

혼자 공부하는 사용자가 **스터디 · 습관 · 집중 · 포인트**를 이어가도록 만든 학습 기록 서비스입니다.

👉 [Live Demo](https://forest-of-learning-frontend.vercel.app/) · [Backend](https://github.com/sebikawa32/Forest-of-Learning-Backend)

---

## Tech Stack

React 19 · Vite · React Router 7 · TanStack Query · i18next · emoji-mart · react-toastify

<p>
  <img src="https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB"/>
  <img src="https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white"/>
  <img src="https://img.shields.io/badge/React_Router-CA4245?style=for-the-badge&logo=reactrouter&logoColor=white"/>
  <img src="https://img.shields.io/badge/TanStack_Query-FF4154?style=for-the-badge&logo=reactquery&logoColor=white"/>
  <img src="https://img.shields.io/badge/i18next-26A69A?style=for-the-badge&logo=i18next&logoColor=white"/>
</p>

---

## My Role — 원세빈 (Detail)

스터디 **상세 페이지**를 맡았습니다. 한 화면에서 스터디 정보, 응원 이모지, 주간 습관 기록이 보이도록 UI와 데이터 흐름을 구성했습니다.

- **상세 레이아웃**  
  이모지 / 스터디 정보 / 액션 버튼 / 습관 기록표를 역할별 컴포넌트로 분리하고, 수정·삭제·습관·집중은 비밀번호 인증 뒤에만 진입하도록 연결했습니다.
- **응원 이모지**  
  emoji-mart 피커로 반응을 추가하고, `native` 문자열만 서버에 보내 집계된 count를 리스트로 그립니다. 바깥 클릭 시 피커가 닫히고 다크 모드 테마도 맞췄습니다.
- **주간 습관 기록표**  
  스터디 단위 주간 API 응답 `{ habitId, habitName, dates }`를 요일 그리드로 렌더링합니다. 습관별 반복 요청 없이 한 번에 그립니다.
- **공유**  
  카카오톡 공유와 링크 복사를 상세 페이지에서 바로 쓸 수 있게 했습니다.

---

## 주요 기능

| 도메인 | 내용 |
| --- | --- |
| Study | 생성 / 수정 / 삭제, 검색·정렬·더보기, 최근 조회 스터디 |
| Detail | 스터디 정보, 응원 이모지, 주간 습관 기록표, 공유 |
| Habit | 습관 CRUD, 오늘 체크/해제 (optimistic update) |
| Focus | 타이머 시작·일시정지·종료, 실집중 시간 기준 포인트 지급 |
| Auth | 스터디별 비밀번호 + sessionStorage, 습관/집중/수정 라우트 보호 |
| UX | 다크/라이트 모드, ko/en/ja/zh-CN, 유저 생성 텍스트 번역 |

---

## 구조

기능 단위(`feature`)와 공통 모듈(`shared`)을 나눠, 페이지는 조합만 담당하도록 구성했습니다.

```text
src/
├── api/                         # fetch 클라이언트, 도메인별 API
├── pages/                       # 라우트 엔트리
├── feature/
│   ├── study/                   # 홈 목록, 생성/수정 폼
│   │   └── studyDetail/         # 상세, 이모지, 습관 기록표
│   ├── habit/
│   └── focus/
├── shared/                      # 레이아웃, 헤더, 토스트, i18n, 테마
├── contexts/
└── router/
```

---

## 트러블슈팅

### 1. 이모지 피커 값이 화면에 `[object Object]`로 보임

emoji-mart `onEmojiSelect`는 문자열 대신 객체를 넘깁니다. 그대로 POST하면 서버/렌더링이 깨졌습니다.

`emojiData?.native ?? emojiData`로 실제 이모지 문자만 저장하도록 바꿨습니다.  
라이브러리 이벤트 값과 서버가 기대하는 값을 맞춰야 집계 UI가 안정적이었습니다.

### 2. 피커를 닫으면 다른 팝업까지 같이 닫힘

문서 클릭으로 피커를 닫으려다 공유/액션 팝업까지 같이 닫히는 경우가 있었습니다.

피커 영역에 `useOnClickOutside`를 한정하고, 피커 내부 클릭은 버블을 막았습니다.  
전역 클릭 핸들러보다 **닫을 대상의 ref 범위**를 먼저 정하는 편이 안전했습니다.

### 3. 배포 후 비밀번호 인증이 유지되지 않음 (팀)

로컬에선 됐지만 Vercel → Render 요청에서 세션 쿠키가 빠졌습니다.

공통 `client`에 `credentials: 'include'`를 넣고, 인증 성공 시 `sessionStorage`에 `study-auth-{id}`를 저장해 새로고침 후에도 습관/집중 진입이 유지되게 했습니다.  
프론트 fetch 옵션과 백엔드 CORS·쿠키 설정이 같이 맞아야 세션 인증이 동작했습니다.

---

## Team

| GitHub | 담당 |
| --- | --- |
| [@juengseulki](https://github.com/juengseulki) | PM, Focus 데이터 구조, 포인트 로직 |
| [@DevGangMin](https://github.com/DevGangMin) | API 연동, Habit 데이터 흐름 |
| [@sojeong0302](https://github.com/sojeong0302) | 스터디 목록, 검색·정렬·페이지네이션 |
| **원세빈** ([@sebikawa32](https://github.com/sebikawa32)) | **상세 페이지 UI, 응원 이모지, 주간 습관 기록표** |
| [@Crong-dev](https://github.com/Crong-dev) | 습관 CRUD, 체크/해제 |
| [@karrum5692](https://github.com/karrum5692) | 집중 타이머 UI, 완료 흐름 |

---

## 실행

```bash
npm install
```

프로젝트 루트에 `.env`를 만듭니다.

```env
VITE_API_BASE_URL=http://localhost:5000
VITE_KAKAO_JAVASCRIPT_KEY=your_kakao_javascript_key
```

```bash
npm run dev
```

백엔드가 `http://localhost:5000`에서 떠 있어야 합니다. ([Backend 저장소](https://github.com/sebikawa32/Forest-of-Learning-Backend))
