# Cookmark MVP PRD

## 1. 문서 목적

이 문서는 요리 영상과 게시물 링크를 저장하고, 메뉴별로 묶어 관리하며, 실제 조리 후 개인 메모를 남기고 선택적으로 익명 조리 팁으로 공개할 수 있는 Cookmark의 최소 기능 제품(MVP)을 정의한다.

MVP의 목표는 AI나 플랫폼 자동 수집 기술을 과시하는 것이 아니라, 사용자가 레시피를 발견한 순간부터 다시 찾아 조리 기록을 남기는 핵심 흐름을 검증하는 것이다.

## 2. 제품 한 줄 설명

유튜브 Shorts, Instagram Reels, 블로그 등에서 발견한 레시피를 공유 메뉴 또는 URL 입력으로 저장하고, 메뉴별로 모아 본 뒤 조리 경험을 개인 기록으로 축적하는 서비스.

## 3. 해결하려는 문제

사용자는 숏폼과 웹에서 레시피를 발견하지만 다음 문제를 겪는다.

- 저장한 레시피가 플랫폼별 좋아요·북마크에 흩어진다.
- 나중에 “그 김치찌개 영상”을 다시 찾기 어렵다.
- 같은 메뉴의 여러 레시피를 한곳에서 비교하기 어렵다.
- 실제로 만들어 본 뒤 맛, 계량 변경, 실패 원인을 기록하기 어렵다.

## 4. 목표 사용자

### 핵심 사용자

- 숏폼으로 요리 아이디어를 찾는 자취생과 초보 요리 사용자
- 저장한 레시피를 실제로 따라 해보는 사용자
- 메뉴별로 여러 레시피를 비교하고 자신만의 조리 팁을 쌓고 싶은 사용자

### MVP에서 제외하는 사용자

- 공개 레시피를 검색하고 소비하려는 불특정 다수 사용자
- 전문 레시피를 판매하거나 공유하려는 크리에이터
- 가족 단위 식단과 장보기까지 관리하려는 사용자

## 5. 제품 목표와 성공 기준

### 제품 목표

1. 링크를 10초 안에 저장할 수 있다.
2. 저장한 레시피를 메뉴 기준으로 다시 찾을 수 있다.
3. 조리 후 1분 안에 개인 메모를 남길 수 있다.
4. 모바일 공유 메뉴에서 Cookmark로 바로 진입할 수 있다.

### MVP 성공 기준

- 사용자가 링크 저장을 완료한다.
- 저장한 링크가 선택한 메뉴에 표시된다.
- 사용자가 저장한 레시피를 다시 열 수 있다.
- 조리 메모가 레시피와 함께 보존된다.
- 사용자가 승인한 조리 메모가 작성자 정보 없이 익명 팁으로 공개된다.
- Android 설치형 PWA에서 공유 URL 수신 흐름이 동작한다.

## 6. 핵심 사용자 여정

### A. URL 직접 저장

1. 사용자가 Cookmark에 로그인한다.
2. URL을 입력한다.
3. 서비스가 URL 형식과 중복 여부를 확인한다.
4. 제목과 기본 메타데이터를 추출한다.
5. 사용자가 메뉴를 선택하거나 새 메뉴를 입력한다.
6. 저장한다.
7. 메뉴 상세 또는 레시피 상세 화면으로 이동한다.

### B. 공유 메뉴를 통한 저장

1. 사용자가 YouTube Shorts, Instagram Reels 또는 웹페이지에서 공유 버튼을 누른다.
2. 공유 대상에서 설치된 Cookmark PWA를 선택한다.
3. Cookmark가 공유된 URL과 제목을 받는다.
4. 로그인 상태를 확인한다.
5. 저장 확인 화면에서 메뉴를 선택한다.
6. 사용자가 저장을 확정한다.

로그인하지 않은 경우 공유 데이터는 임시 저장하고, 로그인 후 저장 확인 화면으로 복원한다.

### C. 조리 기록 작성

1. 사용자가 메뉴 또는 레시피를 연다.
2. “조리 기록 추가”를 누른다.
3. 별점과 메모를 입력한다.
4. 선택적으로 조리 날짜와 사진을 기록한다.
5. 저장된 기록을 레시피 상세에서 확인한다.

## 7. MVP 기능 범위

### 7.1 인증

- 이메일 기반 회원가입 및 로그인
- 로그아웃
- 로그인 사용자별 데이터 분리
- 인증되지 않은 사용자의 저장 요청 차단

MVP에서는 소셜 로그인보다 이메일 인증을 우선한다. 공유 진입 후 인증 흐름을 단순하게 검증하는 것이 목적이다.

### 7.2 레시피 링크 저장

사용자가 저장할 수 있는 정보:

- 원본 URL
- 플랫폼
- 제목
- 썸네일 URL
- 사용자 지정 메모
- 메뉴
- 저장 시각

플랫폼은 URL 호스트를 기준으로 `YOUTUBE`, `INSTAGRAM`, `BLOG`, `OTHER`로 분류한다.

### 7.3 URL 메타데이터 추출

자동 추출 대상:

- Open Graph title
- Open Graph image
- description
- canonical URL

추출 실패 시에도 원본 URL과 사용자가 입력한 메뉴만으로 저장할 수 있어야 한다.

MVP에서 보장하지 않는 항목:

- 모든 플랫폼의 자막 추출
- 영상 내용의 완전한 분석
- 인스타그램 릴스의 안정적인 본문·자막 수집
- 재료와 조리법의 완전한 자동 추출

### 7.4 메뉴 관리

- 메뉴 생성
- 메뉴 목록 조회
- 메뉴명 수정
- 메뉴 삭제
- 메뉴별 레시피 수 표시
- 메뉴 상세에서 소속 레시피 조회

메뉴명은 사용자별로 관리한다. 동일한 이름의 메뉴가 사용자 사이에서 공유되는 구조는 MVP 범위에서 제외한다.

### 7.5 조리 메모

레시피별 조리 기록:

- 별점: 1~5점
- 메모
- 조리 날짜
- 선택적 사진 URL
- 작성 시각

예시:

> 다음에는 간장을 반 스푼 줄인다.
>
> 중불로 오래 끓이는 편이 더 맛있었다.

MVP에서는 사진 저장을 후순위로 두고 텍스트 메모를 먼저 완성한다.

### 7.6 익명 조리 팁

공개 팁은 실제 조리 여부를 인증한 리뷰가 아니라, 사용자가 레시피에 남기는 경험·의견·변형 팁이다. 별도의 사진, 영수증, 운영자 확인은 요구하지 않는다.

개인 조리 메모는 기본적으로 비공개로 저장한다. 조리 기록 작성 화면의 `익명으로 공개하기` 체크박스가 해제된 상태로 등록하면 작성자 본인만 메모를 볼 수 있다. 체크한 상태로 등록하면 메모 내용이 다른 사용자에게 익명 조리 팁으로 즉시 표시된다. 또한 레시피 상세에서 조리 기록 없이도 별도의 익명 팁을 작성할 수 있다.

- 기본 공개 상태는 `PRIVATE`다.
- `익명으로 공개하기` 체크박스의 기본값은 해제다.
- 체크한 상태로 등록한 메모는 공개 상태를 `PUBLIC`으로 저장한다.
- 작성자 이름, 이메일, 프로필 정보는 외부에 표시하지 않는다.
- 공개된 메모 내용은 레시피 상세의 익명 팁 목록에 표시한다.
- 로그인한 사용자는 레시피 상세에서 해당 레시피에 대한 익명 팁을 바로 작성할 수 있다.
- 작성자는 공개를 취소할 수 있으며, 공개 취소한 팁은 다시 공개할 때 기존 팁을 되살린다.
- 작성자가 삭제한 팁은 복구하지 않는다.
- 공개 팁 수정 내용은 즉시 반영한다.
- 공개 팁 신고가 5회 누적되면 자동으로 숨김 처리한다.
- 운영 처리를 위해 내부적으로 작성자 식별자는 보존한다.

기존 개인 메모를 자동으로 공개하지 않는다. 메모에 개인정보나 자기 식별 정보가 포함될 수 있으므로 공개 전 주의 문구를 표시한다.

MVP에서는 공개 피드, 댓글, 좋아요, 팔로우, 사용자 프로필을 제공하지 않는다. 공개 팁은 특정 레시피 상세 화면 안에서만 확인한다.

### 7.7 검색과 다시 찾기

- 메뉴명 검색
- 레시피 제목 검색
- 플랫폼 필터
- 별점 필터
- 최근 저장순 정렬
- 최근 조리 기록순 정렬

자연어 RAG 검색은 MVP 필수 기능이 아니다. 일반적인 PostgreSQL 검색으로 핵심 재방문 문제를 먼저 검증한다.

### 7.8 공유 수신

PWA manifest에 `share_target`을 등록한다.

수신 데이터:

- title
- text
- url

공유 수신 엔드포인트는 상태 변경을 유발하므로 POST를 사용한다. 공유 URL을 받은 뒤 자동 확정하지 않고, 메뉴 확인 화면을 보여준다.

지원 정책:

- 1차 목표: Android 설치형 PWA
- 데스크톱 브라우저: URL 직접 입력과 서비스 내부 공유 지원
- iOS: 동작 가능성을 별도 검증하며 MVP의 완전한 네이티브 공유 확장은 제외

## 8. 화면 요구사항

### 홈

- 최근 저장 레시피
- 메뉴 목록
- URL 저장 입력창
- 공유 수신 진입 화면

### 저장 확인 화면

- 제목
- 썸네일
- 원본 링크
- 플랫폼
- 메뉴 선택
- 새 메뉴 생성
- 저장 버튼

### 메뉴 목록

- 메뉴명
- 레시피 수
- 최근 조리일
- 메뉴 검색

### 메뉴 상세

- 메뉴명
- 소속 레시피 목록
- 레시피별 별점 요약
- 레시피 추가

### 레시피 상세

- 제목과 썸네일
- 원본으로 이동
- 메뉴명
- 저장일
- 조리 기록 목록
- 조리 기록 추가 버튼
- 익명 조리 팁 목록
- 익명 팁 작성 버튼
- 공개 팁 신고 버튼

### 조리 기록 작성

- 별점
- 메모
- 조리 날짜
- `익명으로 공개하기` 체크박스: 기본 해제
- 체크 시 메모 내용이 다른 사용자에게 익명으로 공개됨을 안내
- 저장 및 취소

### 운영자 화면

- 신고된 공개 팁 목록
- 신고 횟수와 신고 사유 확인
- 공개 팁 내용 확인
- 공개 팁 숨김, 숨김 해제, 삭제
- 처리 상태와 처리 시각 확인

## 9. 데이터 모델

### User

- id
- email
- password_hash
- role: `USER` 또는 `ADMIN`
- created_at

### Menu

- id
- user_id
- name
- created_at
- updated_at

제약조건: `UNIQUE(user_id, name)`

### RecipeLink

- id
- user_id
- menu_id
- url
- normalized_url
- platform
- title
- thumbnail_url
- description
- created_at
- updated_at

제약조건: `UNIQUE(user_id, normalized_url)`

### CookLog

- id
- user_id
- recipe_link_id
- rating
- memo
- cooked_at
- created_at
- updated_at

`visibility`는 개인 조리 메모의 공개 여부를 나타내며 `PRIVATE`, `PUBLIC`, `HIDDEN` 중 하나다. 기본값은 `PRIVATE`이며, 작성 화면에서 `익명으로 공개하기`를 체크한 상태로 등록할 때 `PUBLIC`으로 저장한다. 공개 팁을 별도로 삭제하거나 숨기는 경우에는 `PublicTip`의 상태를 사용한다.

### PublicTip

레시피에 연결된 익명 경험·의견·변형 팁을 표현한다. 조리 기록 작성 여부와 관계없이 생성할 수 있다. 조리 메모를 공개한 경우에는 해당 메모의 내용을 공개 팁으로 복사해 생성할 수 있다.

- id
- recipe_link_id: 공개 팁이 작성된 레시피. 원본 삭제 시 NULL 처리
- cook_log_id: 조리 메모에서 공개한 경우에만 연결하며 nullable
- author_user_id: 내부 관리 및 신고 처리용이며 외부에 노출하지 않음. 회원 탈퇴 시 NULL 처리
- recipe_title_snapshot: 공개 당시 레시피 제목
- source_url_snapshot: 공개 당시 원본 URL
- public_text
- rating: nullable
- status: `PUBLISHED`, `UNPUBLISHED`, `HIDDEN`, `DELETED`
- report_count: 5회 이상이면 자동으로 `HIDDEN`
- published_at
- created_at
- updated_at

`UNPUBLISHED`는 작성자가 공개를 취소한 상태다. 다시 공개하면 같은 `PublicTip`을 `PUBLISHED`로 변경한다. `HIDDEN`은 신고 또는 운영자 처리로 숨겨진 상태이며 운영자가 다시 공개할 수 있다. `DELETED`는 작성자가 삭제한 상태이며 복구하지 않는다.

회원 탈퇴 시 개인 조리 기록은 삭제할 수 있지만, 공개 팁은 공개 당시의 레시피 제목·원본 URL·내용을 보존한다. `recipe_link_id`와 `cook_log_id`는 nullable로 두고, 원본 레시피나 조리 기록이 삭제되면 연결을 해제한다. `author_user_id`도 NULL로 변경해 공개 팁을 익명 상태로 유지한다.

### PublicTipReport

공개 팁 신고와 신고자별 중복 신고 방지를 표현한다.

- id
- public_tip_id
- reporter_user_id
- reason
- created_at

제약조건: `UNIQUE(public_tip_id, reporter_user_id)`

## 10. API 초안

```text
POST   /api/v1/auth/signup
POST   /api/v1/auth/login
POST   /api/v1/auth/logout

POST   /api/v1/recipes/preview
POST   /api/v1/recipes
GET    /api/v1/recipes
GET    /api/v1/recipes/{recipe_id}
PATCH  /api/v1/recipes/{recipe_id}
DELETE /api/v1/recipes/{recipe_id}

GET    /api/v1/menus
POST   /api/v1/menus
PATCH  /api/v1/menus/{menu_id}
DELETE /api/v1/menus/{menu_id}
GET    /api/v1/menus/{menu_id}/recipes

POST   /api/v1/recipes/{recipe_id}/cook-logs
GET    /api/v1/recipes/{recipe_id}/cook-logs
PATCH  /api/v1/cook-logs/{cook_log_id}
DELETE /api/v1/cook-logs/{cook_log_id}
POST   /api/v1/cook-logs/{cook_log_id}/public-tip
POST   /api/v1/recipes/{recipe_id}/public-tips
PATCH  /api/v1/public-tips/{public_tip_id}
DELETE /api/v1/public-tips/{public_tip_id}
POST   /api/v1/public-tips/{public_tip_id}/reports
GET    /api/v1/recipes/{recipe_id}/public-tips

GET    /api/v1/admin/public-tip-reports
PATCH  /api/v1/admin/public-tips/{public_tip_id}/status

POST   /api/v1/share-target/receive
```

`recipes/preview`는 메타데이터를 미리 보여주기 위한 API이며, 실제 저장은 `POST /recipes`에서 확정한다.

`/api/v1/admin/*` API는 `ADMIN` 역할만 호출할 수 있다. 운영자는 신고 팁을 숨김·해제·삭제할 수 있다.

## 11. 오류 및 예외 처리

- 잘못된 URL: 400
- 지원하지 않는 URL: 링크 저장은 허용하되 플랫폼을 OTHER로 처리
- 메타데이터 추출 실패: 원본 URL로 저장 가능
- 중복 링크: 기존 레시피를 반환하거나 저장 확인 화면으로 이동
- 존재하지 않는 메뉴: 404
- 다른 사용자의 데이터 접근: 404 또는 403
- 로그인 만료: 로그인 화면으로 이동 후 pending share 복원
- 공유 데이터에 URL 없음: 제목·텍스트를 메모로 보존하고 사용자에게 URL 입력 요청
- 공개 팁 신고: 신고 횟수를 증가시키고 5회 누적 시 자동으로 숨김 처리
- 동일 사용자의 동일 팁 중복 신고: `UNIQUE(public_tip_id, reporter_user_id)` 위반으로 거부
- 공개 팁 공개 취소: `UNPUBLISHED`로 변경하며 삭제하지 않음
- 삭제된 공개 팁 재공개: 허용하지 않음
- 회원 탈퇴: 개인 기록은 삭제하되 공개 팁은 작성자 연결을 제거하고 익명 상태로 유지

## 12. 비기능 요구사항

- 모든 데이터는 사용자별로 격리한다.
- API key는 서버 환경변수에만 둔다.
- 원본 콘텐츠를 다운로드하거나 재배포하지 않는다.
- 원본 플랫폼 링크로 이동하는 기능을 제공한다.
- 개인 메모와 익명 공개 팁을 명확히 구분한다.
- 익명 공개는 사용자 명시적 동의 후에만 처리한다.
- 공개 팁의 신고·숨김·삭제 경로를 제공한다.
- 공개 팁은 레시피에 연결된 공개 콘텐츠로 보존하며 개인 조리 기록과 생명주기를 분리한다.
- 공개 팁은 작성자 공개 취소 시 복구 가능한 상태로 전환하고, 작성자 삭제 시 복구하지 않는다.
- 운영자 API는 `ADMIN` 역할로 보호한다.
- URL은 SSRF 방지를 위해 허용 스킴을 `http`, `https`로 제한한다.
- 메타데이터 요청에는 타임아웃을 둔다.
- 동일 URL 저장은 멱등적으로 처리한다.
- 공유 수신 endpoint는 요청 크기와 URL 길이를 제한한다.

## 13. 권장 기술 스택

- Frontend: Next.js, TypeScript, Tailwind CSS, PWA manifest
- Backend: Java 21, Spring Boot, Spring Security, Spring Data JPA
- Database: PostgreSQL
- Migration: Flyway
- Build: Gradle
- 테스트: JUnit 5, Testcontainers, API contract test, 모바일 공유 수신 수동 테스트
- 로컬 실행: 노트북의 Docker Compose와 PostgreSQL 컨테이너
- 실제 배포: AWS Lightsail 단일 인스턴스, Docker Compose, HTTPS, 도메인
- IaC: Terraform
- CI/CD: GitHub Actions

노트북은 개발·테스트·로컬 AI 실험용으로 사용하며, AWS Lightsail은 외부 사용자용 실제 배포 환경으로 사용한다. MVP에서는 메인 백엔드와 AI 서버를 분리하지 않는다. 메타데이터 추출과 메뉴 추천 코드는 서비스 모듈로 분리해 이후 확장 가능성을 확보한다.

## 14. 완료 조건

- 사용자가 회원가입과 로그인을 할 수 있다.
- URL을 직접 저장할 수 있다.
- 저장된 링크가 메뉴별로 분류된다.
- 메뉴에서 소속 링크를 다시 찾을 수 있다.
- 레시피별 조리 메모를 작성하고 다시 볼 수 있다.
- 조리 메모를 기본 비공개로 저장할 수 있다.
- 공개 체크 없이 등록한 메모는 비공개로 유지된다.
- 공개 체크 후 등록한 메모는 즉시 익명 조리 팁으로 공개된다.
- 작성자가 공개된 메모의 공개를 취소할 수 있다.
- 공개 취소한 팁을 다시 공개하면 기존 팁이 복원된다.
- 작성자가 삭제한 팁은 복구할 수 없다.
- 레시피 상세에서 조리 기록 없이도 익명 팁을 작성할 수 있다.
- 공개 팁을 수정하면 변경 내용이 즉시 반영된다.
- 공개 팁 신고 5회 누적 시 자동 숨김 처리된다.
- 운영자 화면에서 신고 팁을 확인하고 숨김·해제·삭제할 수 있다.
- 회원 탈퇴 후에도 공개 팁이 익명 상태로 유지된다.
- 레시피 상세에서 공개 팁을 확인할 수 있다.
- 중복 링크가 중복 생성되지 않는다.
- 메타데이터 추출 실패에도 링크 저장이 가능하다.
- Android 설치형 PWA에서 공유 URL이 저장 확인 화면으로 전달된다.
- 기본 인증·권한·중복·오류 테스트가 통과한다.

## 15. MVP 이후로 명확히 미룰 기능

- RAG 자연어 검색
- 자막 기반 재료·조리법 자동 추출
- LangChain/LangGraph 에이전트
- 장보기 목록
- 냉장고 재료 기반 추천
- 공개 피드, 댓글, 좋아요, 팔로우 등 커뮤니티 기능
- 소셜 로그인 및 Kakao 로그인
- iOS Share Extension
- 네이티브 모바일 앱
- 조리 사진 업로드 및 이미지 분석
