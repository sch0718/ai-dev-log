---
description: 
globs: 
alwaysApply: true
---
# airspeeder-api-tesyo 프로젝트 규칙

## 개발 환경 및 프레임워크
- Python 3.10 버전을 사용합니다.
- 주요 프레임워크:
  - fastapi (~0.94)
  - pydantic (~1.10)
  - uvicorn (~0.21)
  - asyncpg (~0.27)
  - aioredis (~2.0)
  - ddtrace (~1.12)
  - pandas (~1.5)
  - APScheduler (~3.9)
  - pyjwt (~2.6)
  - Authlib (~1.0)
  - orjson (~3.8)
  - hasql (~0.5)
- Poetry를 사용하여 의존성을 관리합니다.
- Redis를 캐싱 목적으로 사용합니다.
- PostgreSQL을 데이터베이스로 사용합니다.

## 코드 구조
```
프로젝트/
├── app/                        # 주요 애플리케이션 코드
│   ├── main.py                 # 애플리케이션의 진입점
│   ├── configs.py              # 설정 관련 코드
│   ├── dependencies.py         # 종속성 주입 코드
│   ├── middlewares.py          # 미들웨어 코드
│   ├── exceptions.py           # 예외 처리 코드
│   ├── routers/                # API 엔드포인트 정의
│   │   ├── ver_1/              # 버전 1 API
│   │   └── ver_2/              # 버전 2 API
│   ├── handlers/               # 비즈니스 로직 처리
│   └── datasources/            # 데이터 접근 로직 및 모델 정의
├── configs/                    # 설정 파일
│   ├── tesyo.yml               # 애플리케이션 설정 파일
│   └── logging.yml             # 로깅 설정 파일
├── deploy/                     # 배포 관련 파일
│   ├── skaffold/               # Skaffold 배포 설정
│   └── cloudbuild/             # Cloud Build 설정 파일
├── .github/                    # GitHub 워크플로우 및 CI/CD 구성
├── .kpt-pipeline/              # KPT 파이프라인 구성
├── Dockerfile                  # Docker 이미지 빌드 구성
├── pyproject.toml              # Poetry 의존성 관리
├── poetry.lock                 # 의존성 잠금 파일
└── README.md                   # README 파일
```

## 코딩 스타일 가이드라인
1. **의미 있는 이름 사용**: 변수, 함수, 클래스에 설명적인 이름을 사용합니다.
2. **PEP 8 준수**: Python 스타일 가이드를 따릅니다.
3. **문서화**: 함수와 클래스에 docstring을 작성하여 목적을 설명합니다.
4. **간결성 유지**: 간단하고 명확한 코드를 작성하고 불필요한 복잡성을 피합니다.
5. **리스트 내포**: 적절한 경우 전통적인 루프 대신 리스트 내포를 사용합니다.
6. **예외 처리**: try-except 블록을 사용하여 예외를 적절히 처리합니다.
7. **타입 힌트 사용**: 코드 명확성과 타입 검사를 위해 타입 힌트를 활용합니다.
8. **글로벌 변수 제한**: 부작용을 줄이기 위해 글로벌 변수 사용을 제한합니다.

## API 설계
- JWT를 사용한 인증 시스템을 구현합니다.
- API 키와 시크릿을 HTTP 헤더를 통해 검증합니다.
- CORS를 적절히 설정하여 보안을 강화합니다.
- API 버전 관리를 URL 경로를 통해 수행합니다(예: /api/v1, /api/v2).

## 비동기 프로그래밍
- FastAPI의 비동기 기능을 최대한 활용합니다.
- asyncpg를 사용한 비동기 데이터베이스 연결을 구현합니다.
- aioredis를 사용한 비동기 캐싱을 구현합니다.

## 캐싱 전략
- Redis를 사용하여 자주 접근하는 데이터를 캐싱합니다.
- LRU 캐시 메커니즘을 적용하여 메모리 내 캐싱을 사용합니다.
- 캐시 만료 시간을 적절히 설정하여 데이터 신선도를 유지합니다.

## 모니터링 및 로깅
- 통합된 로깅 시스템을 사용합니다.
- ddtrace를 사용하여 애플리케이션 성능을 모니터링합니다.
- 요청 처리 시간을 헤더에 기록하여 성능을 추적합니다.

이 규칙을 따르면 깨끗하고 효율적이며 유지보수가 용이한 Python 코드를 작성할 수 있습니다.