# Wikiman

개인 위키 **Wikiman**의 전체 구성과 저장소 안내입니다.

Wikiman은 혼자 쓰는 위키를 목표로 합니다. 글·카테고리·첨부·간단 포스트·백업·링크 미리보기·PlantUML을 제공하고, 첫 가입 계정만 작성자(`writer`)가 될 수 있습니다.

이 저장소는 소스 코드를 모은 모노레포가 아니라, 관련 프로젝트를 한눈에 보기 위한 **허브(문서) 저장소**입니다.

## 구성 프로젝트

| 프로젝트 | 역할 | 저장소 |
| --- | --- | --- |
| **wikiman-frontend** | Vue 3 + Quasar PWA UI | [kendrickkim/wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend) |
| **wikiman-backend** | Node.js + Express + SQLite API (원본) | [kendrickkim/wikiman-backend](https://github.com/kendrickkim/wikiman-backend) |
| **wikiman-php** | PHP 백엔드 앱 (Node API와 호환) | 이 워크스페이스의 `wikiman-php` (PHAST 사용자 앱) |
| **phastapiv2** | PHP REST 프레임워크 (PHAST API v2) | [kendrickkim/phastapiv2](https://github.com/kendrickkim/phastapiv2) |

```text
┌─────────────────────────────┐
│      wikiman-frontend       │
│   Vue 3 + Quasar (PWA)      │
└──────────────┬──────────────┘
               │ 동일 오리진 /api
       ┌───────┴────────┐
       ▼                ▼
┌─────────────┐   ┌──────────────────┐
│  Node 경로  │   │    PHP 경로      │
│ wikiman-    │   │ phastapiv2       │
│ backend     │   │   + wikiman-php  │
│ Express     │   │ Apache + SQLite  │
└─────────────┘   └──────────────────┘
```

프론트엔드는 **하나의 API 계약**을 기대합니다.  
따라서 Node 백엔드와 PHP 백엔드는 경로·JSON·권한·상태 코드를 맞추도록 포팅했습니다.

## 왜 PHP 경로가 있는가

`wikiman-backend`는 Node 런타임이 필요합니다.  
공유 호스팅처럼 **Apache + PHP + SQLite**만 가능한 환경에서도 같은 위키를 돌리기 위해 PHP로 API를 옮기는 작업이 진행되었습니다.

- 앱 로직: `wikiman-php` (도메인·필터·JWT·SQLite)
- 공통 프레임워크: [phastapiv2](https://github.com/kendrickkim/phastapiv2)

PHAST API는 라우팅, Attribute, 요청 파싱, 필터, 응답 훅만 담당하고, Wikiman 전용 규칙은 `wikiman-php`에 둡니다.

## 기능 요약

- 인증: JWT, 최초 1명만 작성자 가입
- 카테고리 트리 (공개/비공개)
- 글 CRUD, 검색, 키워드, 홈페이지 배치, 휴지통
- 간단 포스트와 정식 글로 승격
- 이미지·첨부 업로드와 파일 접근 제어
- 설정·상단 메뉴
- 링크 미리보기 (SSRF 방어·캐시)
- `.wkmbak` 백업 내려받기·검사·복원
- PlantUML SVG 프록시

## 빠른 시작

### A. Node로 로컬 실행

1. [wikiman-backend](https://github.com/kendrickkim/wikiman-backend) 설치·실행 (`API` 기본 포트 85)
2. [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend)에서 `npm run dev`
3. 브라우저: `http://localhost:9000`

배포 시 프론트를 PWA로 빌드해 백엔드 `public/`에 넣는 흐름을 권장합니다.

```bash
# frontend 저장소
npm run build:backend
# backend 저장소
npm start
```

### B. PHP 호스팅으로 실행

1. 문서 루트에 프론트 PWA 빌드 결과를 둡니다.
2. [phastapiv2](https://github.com/kendrickkim/phastapiv2)를 `/api`로 배치합니다.
3. `wikiman-php`를 PHAST의 `$G_PHASTAPI_CUSTOM_DIR`로 연결합니다.
4. PHP에 `pdo_sqlite`, `mbstring`, `fileinfo`, `curl`, `zlib` 등이 필요합니다.

예:

```php
// api/config.phastapi.php
$G_PHASTAPI_CUSTOM_DIR = "../wikiman-php";
```

프론트는 `baseURL: '/api'`로 동일 오리진 API를 호출합니다.

## 저장소별 역할

### wikiman-frontend

- Vue 3, Quasar, Pinia, PWA
- 에디터: CKEditor, Markdown, Editor.js 등
- 서비스 워커는 `/api`를 캐시하지 않음

### wikiman-backend

- Express 라우트, JWT, SQLite(`better-sqlite3`)
- 업로드, 백업(`.wkmbak`), 링크 미리보기, PlantUML
- API 계약의 **기준 구현**

### wikiman-php

- Node API와 같은 경로·응답을 PHAST 앱으로 구현
- `domain/` + `libs/common/` + 권한 Attribute/필터
- 데이터: `data/wiki.db`, `data/uploads/`

### phastapiv2

- 경량 PHP REST 프레임워크
- PHP 8 Attribute (`_GET_`, `_POST_`, `_PUT_`, `_PATCH_`, `_DELETE_`)
- IN/OUT 필터, multipart/`json_body`, 응답 포매터 훅
- 앱 전용 코드는 custom 디렉터리에 분리

자세한 사용법은 [phastapiv2 README](https://github.com/kendrickkim/phastapiv2/blob/master/README.md)를 보세요.

## API 계약 (공통)

성공 응답은 Wikiman 전용 형식입니다. PHAST 기본 `{ success, data }` 래핑을 PHP 경로에서는 쓰지 않습니다.

```json
{ "posts": [], "total": 0, "page": 1, "pageSize": 10 }
```

오류:

```json
{ "error": "한국어 메시지" }
```

인증:

```http
Authorization: Bearer <jwt>
```

주요 경로 예:

- `GET/POST /auth/*`
- `GET/POST/PATCH/DELETE /categories*`
- `GET/POST/PATCH/DELETE /posts*`
- `GET/POST/PATCH/DELETE /quick-posts*`
- `POST /uploads*`, `GET /files/{name}`
- `GET/PATCH /settings`, `GET/PUT /settings/top-menu`
- `GET /link-preview`, `GET|DELETE /link-preview/cache`
- `GET /backup/download`, `POST /backup/inspect|restore`
- `GET|POST /plantuml*`

## 보안·운영 메모

- `JWT_SECRET`(또는 PHP의 `.jwt-secret`)을 운영에서 반드시 강하게 설정하세요.
- `data/`, `.env`, 업로드 파일을 공개 저장소에 올리지 마세요.
- PHP 경로에서 링크 미리보기·PlantUML은 내부망(SSRF) 접근을 차단합니다.
- 백업 복원은 DB와 업로드를 통째로 덮어씁니다. 복구 전 현재 데이터를 따로 보관하세요.

## 라이선스·기여

각 하위 저장소의 LICENSE·기여 규칙을 따릅니다.  
이 허브 저장소의 문서는 Wikiman 구성을 설명하기 위한 것입니다.

## 링크

- Frontend: https://github.com/kendrickkim/wikiman-frontend  
- Backend (Node): https://github.com/kendrickkim/wikiman-backend  
- PHAST API v2: https://github.com/kendrickkim/phastapiv2  
