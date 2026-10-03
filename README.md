# AWS 스터디 과제 모음

AWS 스터디에서 수행한 과제와 실행·검증 기록을 누적하는 저장소입니다. 현재 수록된 AI Exam Coach Mini MSA는 그중 한 과제입니다. 새 과제를 추가하면 이 목차와 해당 과제의 실행법·학습 결과를 함께 갱신합니다.

## 학습 목표

- AWS 스터디의 과제별 학습 내용과 검증 근거를 계속 정리합니다.
- 작은 서비스를 구현하며 API, 컨테이너, 서비스 간 통신과 장애 대응을 익힙니다.
- 각 과제의 실행 조건과 결과를 남겨 다시 실행하고 비교할 수 있게 합니다.

## 과제 목차

| 과제 | 주요 학습 내용 | 현재 위치·실행 안내 | 기록 |
|---|---|---|---|
| AI Exam Coach Mini MSA | FastAPI, Docker Compose, HTTPX, 문제 조회·답안 채점, 서비스 간 HTTP 통신 | 루트 [compose.yaml](compose.yaml), [question-service](question-service/), [attempt-service](attempt-service/), [실행법](#run) | [DEV_LOG.md](DEV_LOG.md), [검증 화면](docs/01_msa_attempt_success_201.png) |

현재 코드와 Compose 파일은 루트 및 위 서비스 폴더에 있습니다. 앞으로 과제가 쌓이면 과제별 하위 폴더 분리를 검토하되, 이번 문서 정리에서는 파일 위치를 바꾸지 않았습니다.

## 현재 과제: AI Exam Coach Mini MSA

FastAPI와 Docker를 이용하여 구현한 간단한 MSA 기반 문제 풀이·채점 백엔드입니다. 문제 조회와 객관식 답안 채점을 두 서비스로 분리하는 수업 실습으로, 전체 Exam Coach 제품이나 해커톤 개발 원본과는 별도입니다.

## Architecture

사용자
↓
Attempt Service
↓ HTTP
Question Service

- Question Service: 문제와 정답 제공
- Attempt Service: 답안 제출, Question Service 호출, 채점 결과 반환
- 각 서비스는 독립적인 FastAPI 서버와 Docker 컨테이너로 실행됩니다.

## Tech Stack

- Python 3.12
- FastAPI
- Docker
- Docker Compose
- HTTPX

## Swagger

Docker Compose 실행 후 아래 주소에서 API를 테스트할 수 있습니다.

- Question Service: http://localhost:8001/docs
- Attempt Service: http://localhost:8002/docs

## Services

### Question Service

- `GET /health`
- `GET /questions`
- `GET /questions/{question_id}`

외부 포트: `8001`

### Attempt Service

- `GET /health`
- `POST /attempts`
- `GET /attempts/{attempt_id}`

외부 포트: `8002`

## Run

```bash
docker compose up -d --build
```

## Result

### MSA 서비스 간 통신 및 채점 성공

Attempt Service가 Question Service를 HTTP로 호출하여 정답을 확인하고, 채점 결과를 정상적으로 반환했습니다.

![MSA Attempt Success](docs/01_msa_attempt_success_201.png)


## 개발·검증 기록

[DEV_LOG.md](./DEV_LOG.md)에 2026-09-25의 구현·검증·디버깅 과정을 보존했습니다. 당시 `POST /attempts`의 HTTP 201 및 `is_correct: true`, Question Service 중지 시 HTTP 503, Compose 내부 서비스 DNS와 이미지 재빌드 확인을 기록했습니다. 위 설명은 기존 검증 기록의 요약이며 이번 문서 정리에서 컨테이너를 다시 실행했다는 의미는 아닙니다.
