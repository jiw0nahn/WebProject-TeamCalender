# Team Calendar (project_teamcalendar)

팀 단위로 일정을 공유하고 관리할 수 있는 캘린더 웹 애플리케이션입니다. 팀을 생성하고 초대코드로 팀원을 초대하여, 개인 일정과 팀 일정을 하나의 캘린더에서 함께 관리할 수 있습니다.

## 주요 기능

- **회원가입 / 로그인 / 로그아웃**: 이메일과 비밀번호 기반 인증 (bcrypt로 비밀번호 해싱, Passport Local Strategy 사용)
- **팀 생성 및 초대**: 팀 생성 시 6자리 랜덤 초대코드가 발급되며, 코드를 통해 다른 사용자가 팀에 가입할 수 있습니다.
- **팀별 메모**: 각 팀원은 팀마다 개인 메모를 남길 수 있습니다.
- **일정(Todo) 관리**: 개인 일정 또는 팀 일정을 등록/수정/삭제할 수 있으며, 담당자를 지정할 수 있습니다.
- **캘린더 뷰**: [FullCalendar](https://fullcalendar.io/)를 이용해 일정을 시각적으로 확인할 수 있습니다.

## 기술 스택

| 구분 | 사용 기술 |
| --- | --- |
| Runtime / Framework | Node.js, Express 5 |
| 템플릿 엔진 | Nunjucks |
| 인증 | Passport (Local Strategy), bcrypt, express-session |
| 데이터베이스 | MySQL, Sequelize ORM |
| 프론트엔드 | FullCalendar (CDN), 순수 CSS |
| 기타 | dotenv, cookie-parser, morgan, nodemon |

## 프로젝트 구조

```
.
├── app.js                # Express 앱 엔트리포인트
├── config/
│   └── config.json       # Sequelize DB 접속 설정 (dev/test/production)
├── controllers/
│   ├── auth.js           # 회원가입/로그인/로그아웃 로직
│   ├── page.js           # 메인 페이지, 일정(Todo) CRUD 로직
│   └── team.js           # 팀 생성/가입/메모 로직
├── middlewares/
│   └── index.js          # 로그인 여부 확인 미들웨어
├── models/                # Sequelize 모델 (User, Team, TeamMember, Todo)
├── passport/              # Passport 초기화 및 로그인 전략
├── public/                # 정적 파일 (CSS)
├── routes/                # 라우터 (page, auth, team)
└── views/                 # Nunjucks 템플릿 (.html)
```

## 데이터 모델

- **User**: 이메일(고유), 닉네임, 비밀번호 — 팀 소유(Team), 팀 가입(TeamMember), 담당 일정(Todo)과 연결
- **Team**: 이름, 고유 초대코드, 메모 — 소유자(User), 팀원(TeamMember), 팀 일정(Todo)과 연결
- **TeamMember**: User와 Team의 다대다 관계를 위한 중간 테이블 (팀별 개인 메모 포함)
- **Todo**: 제목, 시작/종료 시각, 색상, 종일 여부 — 담당자(User), 팀(Team, null이면 개인 일정)과 연결

## 시작하기

### 사전 준비

- Node.js
- MySQL 서버

### 설치

```bash
npm install
```

### 데이터베이스 설정

1. MySQL에 `team_calendar` 데이터베이스를 생성합니다.
2. `config/config.json`에서 접속 정보(username, password, host 등)를 환경에 맞게 수정합니다.
3. 서버 실행 시 Sequelize가 테이블을 자동으로 생성합니다 (`sequelize.sync`).

### 환경 변수

프로젝트 루트에 `.env` 파일을 만들고 아래 값을 설정합니다.

```
COOKIE_SECRET=원하는_시크릿_값
```

> `.env`에는 민감한 정보가 포함될 수 있으므로 저장소에 커밋되지 않도록 `.gitignore`에 추가하는 것을 권장합니다.

### 실행

```bash
npm start
```

서버는 기본적으로 `http://localhost:8001` 에서 실행됩니다. (환경변수 `PORT`로 변경 가능)

## 라우트 개요

| 경로 | 설명 |
| --- | --- |
| `GET /` | 메인 화면 (캘린더, 팀 목록, 일정 목록) |
| `GET /join` | 회원가입 페이지 |
| `POST /auth/join` | 회원가입 |
| `POST /auth/login` | 로그인 |
| `GET /auth/logout` | 로그아웃 |
| `POST/PATCH/DELETE /schedule` | 일정 등록/수정/삭제 |
| `POST /team/create` | 팀 생성 |
| `POST /team/join` | 초대코드로 팀 가입 |
| `POST /team/:teamId/memo` | 팀별 메모 저장 |
