# DUO.GG — 게임 친구(듀오) 매칭 플랫폼 MVP

와이어프레임 ①~⑧ 전체 구현. React(JS) + Spring Boot 3 + PostgreSQL + WebSocket(STOMP).

## 구조

```
duo-matching/
├── docker-compose.yml   # PostgreSQL
├── backend/             # Spring Boot 3 (Java 17, Gradle)
└── frontend/            # Vite + React (JavaScript)
```

## 실행 방법

### 1. DB 실행
```bash
docker compose up -d
```

### 2. 백엔드 실행 (Java 17 필요)
```bash
cd backend
# IntelliJ에서 DuoApplication 실행, 또는:
gradle bootRun
```
- 최초 실행 시 JPA(ddl-auto: update)가 테이블을 자동 생성합니다.
- 처음 gradle 프로젝트를 열면 `gradle wrapper` 로 래퍼를 만들어두는 것을 권장합니다.

### 3. 프론트엔드 실행 (Node 18+)
```bash
cd frontend
npm install
npm run dev
```
→ http://localhost:5173 (API/WebSocket은 vite proxy로 8080에 연결)

## 구현된 기능

| 와이어프레임 | 기능 |
|---|---|
| ① 회원가입 | 이메일/비밀번호/닉네임 + 프로필 이미지 업로드 |
| ② 설문 | 성별, 나이대, 게임, 성향, 포지션, 마이크, 티어(선택) |
| ③ 프로필 확인 | 미리보기 → 수정 or 확인(계정 생성 + 자동 로그인) |
| ④ 메인 | 모집글 목록, 카드 토글 상세, 온라인 표시(초록 점), 참가 신청 |
| ⑤ 모집글 작성 | 제목 + 내용 (수정 모드 재사용, 기존 내용 유지) |
| ⑥ 신청 관리 | 신청자 프로필 조회, 승인(→채팅방 자동 생성)/거절, 글 수정/삭제 |
| ⑦ 채팅방 | STOMP WebSocket 실시간 채팅 |
| ⑧ 마이페이지 | 내 프로필(+수정), 내 모집글, 내 신청 현황(1시간 후 대기 신청 만료) |
| GNB | 🔔 알림(10초 폴링 + 토스트), 클릭 시 해당 글로 이동 |

## 주요 API

| Method | Path | 설명 |
|---|---|---|
| POST | /api/auth/signup | 회원가입 (multipart, 이미지 포함) |
| POST | /api/auth/login | 로그인 → JWT |
| GET | /api/auth/check-email, /api/auth/check-nickname | 중복 확인 |
| GET/PUT | /api/users/me | 내 프로필 조회/수정 |
| GET | /api/users/{id} | 타 유저 프로필 조회 |
| GET/POST | /api/posts | 목록(검색 `searchType`/`keyword`, 필터 `game`/`gameMode`/`status`)/작성 |
| GET/PUT/DELETE | /api/posts/{id} | 상세/수정/삭제 |
| POST | /api/posts/{id}/close | 모집 완료 처리 (방장) |
| POST | /api/posts/{id}/apply | 참가 신청 |
| GET | /api/posts/{id}/applications | 신청자 목록 (방장) |
| POST | /api/applications/{id}/approve | 승인 → 파티 채팅방 멤버 추가, `{chatRoomId}` 반환 |
| POST | /api/applications/{id}/confirm | 파티원 확정 (방장) |
| POST | /api/applications/{id}/reject | 거절 |
| DELETE | /api/chat/rooms/{roomId}/members/{userId} | 강퇴 (방장, 신청 거절 처리 포함) |
| GET | /api/my/posts, /api/my/applications | 마이페이지 데이터 |
| GET | /api/notifications | 알림 목록 + 미읽음 수 |
| WS | /ws → /app/rooms/{id} 발행, /topic/rooms/{id} 구독 | 그룹 채팅 |


## 구현 노트

- **온라인 표시**: 요청마다 `lastActiveAt` 갱신, 5분 이내 활동 시 온라인으로 표시
- **신청 1시간 만료**: 배치 없이 조회 시점에 `createdAt` 기준으로 필터링
- **로컬 환경변수**: `.env.example`을 참고해 `.env`를 만들고, DB 비밀번호와 JWT 시크릿은 Git에 커밋하지 않음
- **알림**: 10초 폴링. 이후 SSE/WebSocket user queue로 교체 가능
