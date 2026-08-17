[English](README.md)

# Wikiman

개인 위키와 작은 블로그를 함께 운영하는 자기호스팅 도구입니다.

카테고리와 키워드로 지식을 정리하고, 초안은 비공개로 보관하며, 원하는 글은
시간순 블로그로 발행할 수 있습니다. 간단 포스트로 메모를 빠르게 남길 수 있고,
데이터 폴더 하나를 백업해 사이트를 옮길 수 있습니다.

이 저장소는 전체 프로젝트를 안내하는 **허브**입니다. 실제 소스 코드는 아래
저장소에 나뉘어 있습니다.

## 이런 경우에 잘 맞습니다

Wikiman은 여러 사람이 함께 편집하는 공개 위키보다 한 명이 관리하는 개인 공간을
목표로 합니다.

다음과 같은 도구를 찾는다면 적합합니다.

- 같은 글을 위키처럼 정리하고 블로그처럼 발행
- SaaS 계정 대신 직접 호스팅해 데이터 소유
- SQLite 기반의 단순한 백업과 이전
- Markdown, TUI, CKEditor, Editor.js 등 여러 편집기
- 휴대폰 사진과 파일을 간단 포스트로 보내는 보조 앱

첫 번째 가입 계정만 작성자가 됩니다. 방문자는 작성자가 공개한 글을 읽을 수 있습니다.

## 배포 방식 선택

프론트엔드는 하나이며, 실행 환경에 따라 백엔드를 선택합니다.

| 실행 환경 | 백엔드 | 선택 기준 |
| --- | --- | --- |
| Node.js 사용 가능 | [wikiman-backend](https://github.com/kendrickkim/wikiman-backend) | 기준 구현이며 배포가 가장 단순함 |
| Apache 또는 Nginx + PHP | [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) + [phastapiv2](https://github.com/kendrickkim/phastapiv2) | Node.js 없는 공유 호스팅, SQLite 또는 MySQL |

두 백엔드는 [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend)에
같은 API를 제공합니다.

```text
Android·iOS 앱 ─┐
                 ├─ Wikiman 프론트엔드 ── /api ── Node 백엔드
웹 브라우저 ────┘                              └─ PHP 백엔드 + PHAST
```

## 구성 프로젝트

| 저장소 | 역할 |
| --- | --- |
| [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend) | Vue 3 + Quasar PWA |
| [wikiman-backend](https://github.com/kendrickkim/wikiman-backend) | Node.js + Express + SQLite API 및 웹 호스트 |
| [wikiman-backend-php](https://github.com/kendrickkim/wikiman-backend-php) | PHP API와 웹 설치 화면 |
| [phastapiv2](https://github.com/kendrickkim/phastapiv2) | PHP 백엔드가 사용하는 REST 프레임워크 |
| [wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter) | Android·iOS 관리자 WebView·공유 앱 |

## Node.js로 빠르게 시작

```bash
git clone https://github.com/kendrickkim/wikiman-backend.git
git clone https://github.com/kendrickkim/wikiman-frontend.git

cd wikiman-backend
npm install
cp .env.example .env
npm run dev

# 다른 터미널에서
cd ../wikiman-frontend
npm install
npm run dev
```

`http://localhost:9000`을 여세요. 처음 만든 계정이 작성자가 됩니다.

운영할 때는 PWA를 백엔드로 빌드하고 웹 호스트를 시작합니다.

```bash
# wikiman-frontend
npm run build:backend

# wikiman-backend
npm start
```

환경 변수와 리버스 프록시 설정은
[Node 백엔드 README](https://github.com/kendrickkim/wikiman-backend/blob/main/README-kr.md)를
참고하세요.

## PHP 호스팅으로 빠르게 시작

PHP 방식은 세 프로젝트를 함께 사용합니다.

1. [wikiman-frontend](https://github.com/kendrickkim/wikiman-frontend)에서
   `npm run build:php` 실행
2. `wikiman-backend-php/public/`을 Apache 또는 Nginx 문서 루트로 지정
3. [phastapiv2](https://github.com/kendrickkim/phastapiv2)를 `/api`로 연결하고
   사용자 정의 앱 경로를 `wikiman-backend-php`로 지정
4. 사이트를 열어 웹 설치 완료

설치 화면에서 첫 관리자와 SQLite 또는 MySQL/MariaDB를 설정합니다. Apache·Nginx
구성과 보안 설정은
[PHP 백엔드 README](https://github.com/kendrickkim/wikiman-backend-php/blob/main/README-kr.md)를
참고하세요.

## 주요 기능

- 카테고리, 키워드, 검색, 초안, 발행, 휴지통
- 위키 홈페이지 또는 시간순 블로그 모드
- 텍스트, 리치 텍스트, 구조화, Markdown, HTML 편집기
- 나중에 정식 글로 옮길 수 있는 간단 포스트
- 첨부 파일, 파일 접근 제어, 업로드 용량 제한
- PlantUML, 코드 강조, 줄 번호, 복사 버튼
- 서버 요청을 보호하는 링크 미리보기
- 한국어·영어 사이트 UI
- PWA와 서버에서 생성하는 SNS 공유 정보
- SQLite 배포의 백업과 복원

## 모바일 앱

[wikiman-flutter](https://github.com/kendrickkim/wikiman-flutter)는 Android와
iOS용 관리자 보조 앱입니다. 작성자 권한을 확인한 뒤 사이트를 WebView로 열고,
휴대폰 공유 메뉴에서 받은 글·사진·파일을 간단 포스트 초안으로 보냅니다.

이미 운영 중인 Wikiman에 연결해 사용하는 앱이며, 프론트엔드와 백엔드 서버를
포함하지는 않습니다.

## 운영 시 기억할 점

- `.env`, DB, 업로드, PHP의 `data/config.php`를 Git에 올리지 않기
- 운영 환경에 강한 JWT 비밀키 사용
- 외부에 공개하는 사이트는 HTTPS 사용
- DB뿐 아니라 데이터 폴더 전체를 백업
- MySQL/MariaDB는 호스팅 스냅샷이나 `mysqldump` 사용. 현재 `.wkmbak`은
  SQLite 배포를 대상으로 함

각 구성요소의 세부 설정은 해당 저장소 README에 정리되어 있습니다.
