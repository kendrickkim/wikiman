# Wikiman

개인 위키 **Wikiman**의 전체 구성과 저장소 안내입니다.

Wikiman은 혼자 쓰는 위키를 목표로 합니다. 글·카테고리·첨부·간단 포스트·백업·링크 미리보기·PlantUML을 제공하고, 첫 가입 계정만 작성자(`writer`)가 될 수 있습니다.

이 저장소는 소스 코드를 모은 모노레포가 아니라, 관련 프로젝트를 한눈에 보기 위한 **허브(문서) 저장소**입니다.

## 특징

Wikiman은 “문서 위키”와 “개인 블로그”를 한 제품에 묶은 자기호스팅 도구입니다. Notion·Wiki.js 같은 위키만, 또는 Ghost·WordPress 같은 블로그만 쓰는 것과 달리, **지식 정리와 시간순 발행을 같은 데이터·같은 UI에서** 오갈 수 있습니다.

- **위키 + 블로그 하이브리드** — 카테고리 트리·키워드·검색으로 지식을 쌓고, 블로그 모드를 켜면 홈이 최근 발행 글 본문 피드가 됩니다. 필요하면 홈페이지로 지정한 글을 피드보다 먼저(맨 위)에 고정해 둘 수 있습니다.
- **에디터를 글마다 고름** — 텍스트, CKEditor, Summernote, TUI, Editor.js, Markdown, HTML을 지원합니다. 작성·수정 중 에디터를 바꿔도 본문이 사라지지 않도록, 가능한 범위에서 HTML·텍스트로 이어 받습니다.
- **가벼운 자기호스팅** — SQLite와 업로드 디렉터리만으로 동작합니다. DB 서버·플러그인 생태계에 묶이지 않고 `data/`만 백업하면 이전할 수 있습니다. Node(Express)뿐 아니라 **Apache + PHP** 경로로도 같은 API를 돌릴 수 있습니다.
- **공개와 초안의 경계가 분명함** — 초안 저장·발행, 글·카테고리 공개/비공개, 작성자만 쓰는 휴지통(복원·완전 삭제)을 기본으로 둡니다. 초안 글 보기에서는 확인 후 바로 발행할 수 있습니다.
- **마크다운을 제대로 씀** — PlantUML 다이어그램, 코드 하이라이트, 라인 번호, 코드 블록 복사 버튼을 지원합니다. 공유용 Open Graph는 서버가 HTML에 넣어 주므로 SNS 크롤러에도 제목·요약·이미지가 맞습니다.
- **일상 작성에 맞춘 UX** — 모바일 간단 포스트, PWA, 상단 메뉴, 사이트 언어(한국어 / English), 글자 크기·다크 모드·오른쪽 카테고리 패널 기본 열림 등 사이트 관리에서 바로 조절합니다. DokuWiki 가져오기로 기존 문서를 옮길 수 있습니다.

다른 플랫폼과의 자리매김을 한 줄로 말하면, **혼자 쓰는 위키의 구조에 블로그형 발행·공유를 얹되, SaaS나 무거운 CMS 없이 운영**하는 쪽입니다.

## 주요 기능

- 카테고리 트리(공개/비공개), 키워드, 검색, 휴지통
- 홈페이지 지정 글 / 블로그 모드 피드(페이지당 글 수, 홈페이지 글 선표시)
- 다중 에디터와 에디터 전환 시 본문 이어받기
- 초안·발행, 공개·비공개, 초안 보기에서 발행
- Markdown 코드 하이라이트·라인 번호·복사, PlantUML
- 간단 포스트와 정식 글로 승격
- 첨부 파일·이미지 업로드와 파일 접근 제어
- 링크 미리보기 캐시(SSRF 방어), 백업·복구(`.wkmbak`)
- 사이트 관리(일반·카테고리·홈페이지·블로그·간단 포스트·첨부·데이터·상단 메뉴)
- 사이트 언어(한국어 / English), PWA, 서버 사이드 Open Graph/Twitter 카드
- 인증: JWT, 최초 1명만 작성자 가입
- Android·iOS 관리자 앱(WebView, 접속 정보 보안 저장, 간단 포스트 공유 수신)

## 구성 프로젝트

| 프로젝트 | 역할 | 저장소 |
| --- | --- | --- |
| **wikiman-frontend** | Vue 3 + Quasar PWA UI | [kendrickkim/wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend) |
| **wikiman-backend** | Node.js + Express + SQLite API (원본) | [kendrickkim/wikiman-backend](https://github.com/kendrickkim/wikiman-backend) |
| **wikiman-backend-php** | PHP 백엔드 앱 (Node API와 호환) | [kendrickkim/wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) |
| **wikiman-flutter** | Android·iOS 관리자 WebView·공유 앱 | [kendrickkim/wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter) |
| **phastapiv2** | PHP REST 프레임워크 (PHAST API v2) | [kendrickkim/phastapiv2](https://github.com/kendrickkim/phastapiv2) |

```text
┌─────────────────────────────┐
│      wikiman-flutter        │
│  WebView + 공유 수신 (관리자) │
└──────┬───────────────┬──────┘
       │ WebView       │ /api (로그인·업로드)
       ▼               │
┌──────────────────┐   │
│ wikiman-frontend │   │
│ Vue 3 + Quasar   │   │
└────────┬─────────┘   │
         │ 동일 오리진 /api
         └───────┬─────┘
         ┌───────┴────────┐
         ▼                ▼
┌─────────────┐   ┌──────────────────────┐
│  Node 경로  │   │       PHP 경로       │
│ wikiman-    │   │ phastapiv2           │
│ backend     │   │   + wikiman-backend- │
│ Express     │   │     php              │
│             │   │ Apache + SQLite      │
└─────────────┘   └──────────────────────┘
```

프론트엔드는 **하나의 API 계약**을 기대합니다.  
따라서 Node 백엔드와 PHP 백엔드는 경로·JSON·권한·상태 코드를 맞추도록 포팅했습니다.

관리자용 모바일 앱(`wikiman-flutter`)은 배포된 Wikiman을 WebView로 열고, 로그인·공유 업로드는 `/api`를 직접 호출합니다. `writer` 권한이 확인된 뒤에만 WebView에 진입합니다.

## 왜 PHP 경로가 있는가

`wikiman-backend`는 Node 런타임이 필요합니다.  
공유 호스팅처럼 **Apache + PHP + SQLite**만 가능한 환경에서도 같은 위키를 돌리기 위해 PHP로 API를 옮기는 작업이 진행되었습니다.

- 앱 로직: [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) (도메인·필터·JWT·SQLite)
- 공통 프레임워크: [phastapiv2](https://github.com/kendrickkim/phastapiv2)

PHAST API는 라우팅, Attribute, 요청 파싱, 필터, 응답 훅만 담당하고, Wikiman 전용 규칙은 `wikiman-backend-php`에 둡니다.

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
3. [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php)를 PHAST의 `$G_PHASTAPI_CUSTOM_DIR`로 연결합니다.
4. PHP에 `pdo_sqlite`, `mbstring`, `fileinfo`, `curl`, `zlib` 등이 필요합니다.

예:

```php
// phastapiv2의 config.phastapi.php
$G_PHASTAPI_CUSTOM_DIR = "../wikiman-backend-php";
```

프론트는 `baseURL: '/api'`로 동일 오리진 API를 호출합니다.

## 저장소별 역할

### wikiman-frontend

- Vue 3, Quasar, Pinia, PWA
- 사이트 언어(한국어 / English)와 UI·오류 메시지 번역 (`src/i18n/`)
- 에디터: 텍스트 / CKEditor / Summernote / TUI / Editor.js / Markdown / HTML
- 서비스 워커는 `/api`를 캐시하지 않음

### wikiman-backend

- Express 라우트, JWT, SQLite(`better-sqlite3`)
- 업로드, 백업(`.wkmbak`), 링크 미리보기, PlantUML
- API 계약의 **기준 구현**

### wikiman-backend-php

- Node API와 같은 경로·응답을 PHAST 앱으로 구현
- `domain/` + `libs/common/` + 권한 Attribute/필터
- 데이터: `data/wiki.db`, `data/uploads/`
- 저장소: [kendrickkim/wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php)

### phastapiv2

- 경량 PHP REST 프레임워크
- PHP 8 Attribute (`_GET_`, `_POST_`, `_PUT_`, `_PATCH_`, `_DELETE_`)
- IN/OUT 필터, multipart/`json_body`, 응답 포매터 훅
- 앱 전용 코드는 custom 디렉터리에 분리
- `/api/docs` 문서 UI (검색·메서드 필터·다크/라이트 테마)

자세한 사용법은 [phastapiv2 README](https://github.com/kendrickkim/phastapiv2/blob/master/README.md)를 보세요.

### wikiman-flutter

- Flutter 기반 Android·iOS 관리자 전용 앱
- Wikiman URL·관리자 아이디·비밀번호 입력 후, 로그인 API에서 `writer` 권한을 확인한 뒤에만 WebView 진입
- 접속 정보는 기기 보안 저장소에 보관하고 다음 입력 화면에 다시 표시
- 웹 로그아웃 시 접속 정보 화면으로 자동 복귀
- 앱에서만 웹 사용자 메뉴에 **접속 정보 변경** 표시
- Android·iOS 공유 메뉴에서 **Wikiman**으로 텍스트·이미지·파일 수신 → 업로드 후 간단 포스트 새 입력 화면에 Markdown 초안 전달
- 사설망 HTTP Wikiman도 쓸 수 있도록 평문 HTTP를 허용(외부망은 HTTPS 권장)
- 공유 파일 용량 제한은 사이트 관리의 첨부파일 설정을 따르며 서버에서도 검증
- iOS 배포 시 Runner와 ShareExtension에 같은 Development Team·App Group 필요
- 저장소: [kendrickkim/wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter)

자세한 사용법은 [wikiman-flutter README](https://github.com/kendrickkim/wikiman-flutter/blob/main/README-kr.md)를 보세요.

## API 계약 (공통)

성공 응답은 Wikiman 전용 형식입니다. PHAST 기본 `{ success, data }` 래핑을 PHP 경로에서는 쓰지 않습니다.

```json
{ "posts": [], "total": 0, "page": 1, "pageSize": 10 }
```

오류:

```json
{ "error": "ERROR_CODE", "params": { } }
```

API는 안정적인 오류 코드를 반환하고, 프론트엔드가 사이트 언어에 맞게 번역해 표시합니다.

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
- Backend (PHP): https://github.com/kendrickkim/wikiman-backend-php  
- Flutter (관리자 앱): https://github.com/kendrickkim/wikiman-flutter  
- PHAST API v2: https://github.com/kendrickkim/phastapiv2  
