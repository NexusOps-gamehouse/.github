# 🎮 GameHouse

> **게임 파트너 매칭 플랫폼**
>
> React + Spring Boot + PostgreSQL + RabbitMQ(STOMP) 기반의 게임 친구(듀오) 매칭 서비스입니다.
>
> Docker 기반 컨테이너 환경에서 실행되며, GitHub Actions를 이용한 CI와 AWS 기반 CD를 구축하는 것을 목표로 합니다.

---

# 📂 프로젝트 구조

```text
gamehouse/
├── frontend/        # React + Vite
├── backend/         # Spring Boot 3 (Java 17)
└── infra/           # Docker Compose / CI-CD / Monitoring
```

---

# 🚀 실행 방법

## 1. 환경변수 준비

```bash
cp infra/.env.example infra/.env
```

`.env` 파일에 필요한 값을 입력합니다.

- DB_PASSWORD
- JWT_SECRET
- RIOT_API_KEY
- RABBITMQ_PASSWORD
- FRONTEND_API_BASE_URL
- FRONTEND_WS_URL
- FRONTEND_BACKEND_ORIGIN

---

## 2. Docker Compose 실행

```bash
cd infra

docker compose up -d
```

실행되는 서비스

- PostgreSQL
- RabbitMQ
- Backend
- Frontend

---

## 3. 실행 확인

```bash
docker compose ps
```

정상 실행 시

```text
gamehouse-db
gamehouse-rabbitmq
gamehouse-backend
gamehouse-frontend
```

모든 컨테이너가 **healthy** 상태가 됩니다.

---

# ✨ 구현된 기능

| 기능 | 설명 |
|------|------|
| 회원가입 | 이메일, 닉네임 중복 확인, 프로필 생성 |
| 로그인 | JWT 기반 인증 |
| 프로필 | 조회 및 수정 |
| 모집글 | 생성 / 조회 / 수정 / 삭제 |
| 신청 | 참가 신청 / 승인 / 거절 |
| 채팅 | WebSocket(STOMP) 실시간 채팅 |
| 마이페이지 | 내 프로필, 내 모집글, 신청 현황 |

---

# 📌 주요 API

| Method | URL | 설명 |
|---------|-----|------|
| POST | `/api/auth/signup` | 회원가입 |
| POST | `/api/auth/login` | 로그인 |
| GET | `/api/auth/check-email` | 이메일 중복 확인 |
| GET | `/api/auth/check-nickname` | 닉네임 중복 확인 |
| GET | `/api/users/me` | 내 정보 조회 |
| PUT | `/api/users/me` | 프로필 수정 |
| GET | `/api/posts` | 모집글 목록 |
| POST | `/api/posts` | 모집글 작성 |
| GET | `/api/posts/{id}` | 모집글 상세 |
| PUT | `/api/posts/{id}` | 모집글 수정 |
| DELETE | `/api/posts/{id}` | 모집글 삭제 |
| POST | `/api/posts/{id}/apply` | 참가 신청 |
| GET | `/api/posts/{id}/applications` | 신청 목록 |
| POST | `/api/applications/{id}/approve` | 신청 승인 |
| POST | `/api/applications/{id}/reject` | 신청 거절 |
| GET | `/api/my/posts` | 내 모집글 |
| GET | `/api/my/applications` | 내 신청 현황 |
| WS | `/ws` | WebSocket 연결 |

---

# 🐳 Docker 구성

| 서비스 | 설명 |
|---------|------|
| frontend | React + Vite + serve |
| backend | Spring Boot 3 |
| db | PostgreSQL 16 |
| rabbitmq | RabbitMQ(STOMP) |

## Backend

- Multi-stage Build
- Gradle Build
- bootJar 생성
- jlink Custom JRE
- Non-root User 실행

## Frontend

- Multi-stage Build
- Vite Build
- serve 기반 실행
- 런타임 config.js 생성

---

# 🔄 CI

Feature 브랜치에서 Pull Request 생성 시

```text
Feature

↓

Secret Scan

↓

Docker Build

↓

Smoke Test

↓

Review

↓

Merge
```

## Verify

- gitleaks Secret Scan

## Image Check

- Backend Docker Build
- Frontend Docker Build
- 컨테이너 실행 확인

---

## Publish

develop 브랜치 Merge 시

```text
develop

↓

Docker Build

↓

Docker Hub Push
```

생성되는 이미지

```text
backend-develop
backend-<commitSHA>

frontend-develop
frontend-<commitSHA>
```

---

# 🚀 CD (구현 중)

```text
GitHub Actions

↓

Docker Hub

↓

AWS OIDC

↓

AWS Systems Manager

↓

EC2

↓

docker compose pull

↓

docker compose up -d

↓

Health Check
```

### 주요 설계

- GitHub OIDC를 이용한 Keyless 인증
- AWS Systems Manager 기반 무중단 배포
- Docker Hub 이미지 기반 배포
- Health Check를 통한 배포 검증

---

# 📊 Monitoring

Prometheus + Grafana 기반 모니터링

구성

```text
Prometheus

↓

Grafana

↓

cAdvisor

↓

node-exporter
```

모니터링 대상

- Spring Boot Actuator
- Docker Container
- EC2 Host
- RabbitMQ

---

# 🛠️ 기술 스택

### Frontend

- React
- Vite
- React Router
- Axios
- SockJS
- STOMP

### Backend

- Spring Boot
- Spring Security
- JWT
- Spring Data JPA
- PostgreSQL
- RabbitMQ
- WebSocket(STOMP)

### DevOps

- Docker
- Docker Compose
- GitHub Actions
- Docker Hub
- AWS EC2
- AWS Systems Manager
- AWS OIDC

### Monitoring

- Prometheus
- Grafana
- cAdvisor
- node-exporter

---

# 📦 Repository

```text
frontend/
    React + Vite

backend/
    Spring Boot

infra/
    Docker Compose
    CI/CD
    Monitoring
```

---

# 👥 Team

| 이름 | 역할 |
|------|------|
| - 박윤희, 남서현, 조현우 | Frontend |
| - 이태환, 이석현, 오수아 | Backend |
| - 전 팀원 | DevOps |
| - 이태환 | PM |

---

## 📄 License

This project was developed as a team project for educational purposes.
