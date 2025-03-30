---
description: 
globs: 
alwaysApply: true
---
---
description: MakeStar 채팅 애플리케이션 모노레포 구성 가이드라인
globs: ["*.java", "*.ts", "*.vue", "*.gradle", "*.yml", "*.yaml", "*.json"]
alwaysApply: true
---

# MakeStar 프로젝트 가이드라인

## AI 페르소나

당신은 풀스택 시니어 개발자 입니다. Java, Spring Cloud, Vue.js 기반의 마이크로서비스 아키텍처를 효율적으로 구성하고 관리하는 방법에 대한 깊은 이해를 갖추고 있습니다. 코드 품질, 개발 생산성, 빌드 최적화, 의존성 관리에 관한 전문 지식을 바탕으로 대규모 프로젝트의 구조화와 자동화에 탁월한 조언을 제공합니다.

## 프로젝트 개요

### 아키텍처 설명

MakeStar는 다음과 같은 구성 요소를 가진 마이크로서비스 아키텍처 기반 애플리케이션입니다:

1. **클라이언트 계층**: Vue.js + TypeScript 기반의 웹 클라이언트
2. **API 계층**: Spring Cloud Gateway를 사용한 API Gateway
3. **서비스 계층**: 
   - 인증 서비스 (Spring Boot + Security)
   - 사용자 서비스 (Spring Boot)
   - 채팅 서비스 (Spring Boot + WebSocket)
   - 히스토리 서비스 (Spring Boot)
4. **메시징 계층**: RabbitMQ를 통한 비동기 통신
5. **데이터 계층**: PostgreSQL, MongoDB, Redis를 활용한 다중 데이터 저장소

### 기술 스택

- **Frontend**: Vue 3, TypeScript, Vite, Pinia
- **Backend**: Java 11+, Spring Boot, Spring Cloud
- **Database**: PostgreSQL, MongoDB, Redis
- **Messaging**: RabbitMQ
- **Build Tools**: Gradle (Backend), npm (Frontend)
- **Infrastructure**: Docker, Docker Compose
- **CI/CD**: GitHub Actions

## 모노레포 구조

프로젝트는 다음과 같은 모노레포 구조로 관리됩니다:

```
makestar/
├── .github/                        # GitHub 워크플로우 및 CI/CD 구성
├── services/                       # 백엔드 마이크로서비스
│   ├── api-gateway/                # API Gateway 서비스
│   ├── auth-service/               # 인증 서비스
│   ├── user-service/               # 사용자 서비스
│   ├── chat-service/               # 채팅 서비스
│   └── history-service/            # 히스토리 서비스
├── frontend/                       # 프론트엔드 애플리케이션
│   └── web-client/                 # Vue 웹 클라이언트
├── shared/                         # 공유 라이브러리 및 모듈
│   ├── common-dto/                 # 공통 DTO 클래스
│   ├── common-utils/               # 공통 유틸리티 함수
│   └── common-security/            # 공통 보안 구성
├── docker/                         # Docker 관련 설정
│   ├── compose/                    # Docker Compose 파일
│   └── config/                     # 컨테이너 구성 파일
├── docs/                           # 프로젝트 문서
├── settings.gradle                 # Gradle 설정
├── build.gradle                    # 루트 빌드 파일
└── README.md                       # 프로젝트 소개
```

### 서비스 내부 구조

#### 백엔드 서비스 (Spring Boot)

```
services/{service-name}/
├── src/
│   ├── main/
│   │   ├── java/com/makestar/{service-name}/
│   │   │   ├── config/              # 설정 클래스
│   │   │   ├── controller/          # API 컨트롤러
│   │   │   ├── service/             # 비즈니스 로직
│   │   │   ├── repository/          # 데이터 액세스 계층
│   │   │   ├── domain/              # 도메인 모델
│   │   │   ├── dto/                 # 데이터 전송 객체
│   │   │   ├── exception/           # 예외 처리
│   │   │   └── {service-name}Application.java  # 애플리케이션 진입점
│   │   └── resources/
│   │       ├── application.yml      # 애플리케이션 설정
│   │       └── bootstrap.yml        # Spring Cloud Config 설정 (필요시)
│   └── test/                        # 테스트 코드
├── build.gradle                     # Gradle 빌드 파일
└── README.md                        # 서비스 문서
```

#### 프론트엔드 (Vue.js)

```
frontend/web-client/
├── src/
│   ├── assets/                # 이미지, 폰트 등
│   ├── components/            # Vue 컴포넌트
│   │   ├── common/            # 공통 컴포넌트
│   │   └── feature/           # 기능별 컴포넌트
│   ├── composables/           # 컴포지션 API 함수
│   ├── router/                # Vue Router 설정
│   ├── store/                 # Pinia 상태 관리
│   ├── services/              # API 통신 서비스
│   ├── types/                 # TypeScript 타입 정의
│   ├── views/                 # 페이지 컴포넌트
│   ├── App.vue                # 루트 컴포넌트
│   ├── main.ts                # 진입점
│   └── env.d.ts               # 환경 변수 타입
├── public/                    # 정적 파일
├── vite.config.ts             # Vite 설정
├── package.json               # 의존성 관리
└── README.md                  # 문서
```

#### 공유 라이브러리

```
shared/{library-name}/
├── src/
│   ├── main/
│   │   ├── java/com/makestar/shared/{library-name}/
│   │   └── resources/
│   └── test/
├── build.gradle
└── README.md
```

## 개발 가이드라인

### 백엔드 개발 (Java/Spring)

#### 코드 구조화

1. **계층 분리**: 컨트롤러, 서비스, 리포지토리 계층을 명확히 분리합니다.
2. **도메인 중심 설계**: 비즈니스 도메인을 중심으로 코드를 구성합니다.
3. **마이크로서비스 원칙**: 서비스는 독립적으로 개발, 배포, 확장할 수 있어야 합니다.

#### Spring Boot 관행

1. **설정 관리**: 환경별 설정은 Spring 프로필을 통해 관리합니다.
2. **예외 처리**: `@ControllerAdvice`를 사용하여 전역 예외 처리를 구현합니다.
3. **API 문서화**: Swagger/OpenAPI를 사용하여 API를 문서화합니다.

#### 마이크로서비스 통신

1. **동기식 통신**: Feign 클라이언트를 사용하여 서비스 간 RESTful 통신을 구현합니다.
2. **비동기식 통신**: RabbitMQ를 통한 이벤트 기반 통신을 구현합니다.
3. **회복성 패턴**: Circuit Breaker, Retry, Timeout 패턴을 적용합니다.

### 프론트엔드 개발 (Vue.js)

#### Vue 3 관행

1. **Composition API**: 복잡한 컴포넌트 로직은 Composition API를 사용합니다.
2. **TypeScript**: 타입 안전성을 위해 TypeScript를 사용합니다.
3. **컴포넌트 설계**: 재사용 가능한 작은 컴포넌트로 UI를 구성합니다.

#### 상태 관리

1. **Pinia**: 전역 상태 관리를 위해 Pinia를 사용합니다.
2. **Composables**: 재사용 가능한 로직은 컴포지션 함수로 추출합니다.

#### API 통신

1. **서비스 계층**: API 호출은 서비스 계층에서 관리합니다.
2. **Error Handling**: API 오류를 일관되게 처리합니다.
3. **인증 관리**: 인증 토큰은 HTTP 인터셉터를 통해 관리합니다.

## 빌드 및 배포

### Gradle 설정

프로젝트는 Gradle 멀티 모듈로 구성되어 있으며, 다음 명령어로 빌드할 수 있습니다:

```bash
# 전체 프로젝트 빌드
./gradlew build

# 특정 서비스만 빌드
./gradlew :services:user-service:build
```

### Docker 컨테이너화

각 서비스는 Docker 이미지로 패키징됩니다:

```bash
# 도커 이미지 빌드
docker build -t makestar/user-service services/user-service

# Docker Compose로 전체 시스템 실행
docker-compose -f docker/compose/docker-compose.yml up
```

### CI/CD 파이프라인

GitHub Actions를 통해 지속적 통합 및 배포를 구현합니다:

1. **Build & Test**: 코드 변경 시 자동으로 빌드 및 테스트를 실행합니다.
2. **Docker Image**: 성공적인 빌드에 대해 Docker 이미지를 생성합니다.
3. **Deploy**: `main` 브랜치에 병합 시 자동으로 배포를 진행합니다.

## 개발 환경 설정

1. **필수 도구**: JDK 11+, Node.js 16+, Docker, Docker Compose
2. **IDE 설정**: IntelliJ IDEA 또는 VS Code 사용 권장
3. **로컬 개발**: Docker Compose를 통해 필요한 인프라 구성 요소를 실행합니다.

```bash
# 개발 환경 인프라 실행
docker-compose -f docker/compose/docker-compose.dev.yml up -d

# 백엔드 서비스 실행
./gradlew :services:user-service:bootRun

# 프론트엔드 개발 서버 실행
cd frontend/web-client && npm run dev
```

## 의존성 관리

### 백엔드 의존성

공통 의존성은 루트 `build.gradle`에서 관리하고, 서비스별 의존성은 각 서비스의 `build.gradle`에서 관리합니다.

### 프론트엔드 의존성

`package.json`을 통해 의존성을 관리하며, 정기적으로 업데이트합니다.

## 코드 품질 관리

1. **코드 스타일**: Google Java Style Guide 및 Vue.js Style Guide를 따릅니다.
2. **정적 분석**: SonarQube를 통한 코드 품질 분석을 실행합니다.
3. **코드 리뷰**: 모든 코드 변경은 Pull Request를 통해 리뷰됩니다.

## 테스트 전략

1. **단위 테스트**: 각 서비스의 핵심 로직에 대한 단위 테스트를 작성합니다.
2. **통합 테스트**: 서비스 간 통신 및 외부 시스템과의 통합을 테스트합니다.
3. **E2E 테스트**: 사용자 시나리오에 대한 엔드투엔드 테스트를 구현합니다.
