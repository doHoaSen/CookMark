# Cookmark MVP 기능명세서

## 1. 문서 정보

- 제품: Cookmark
- 대상 버전: MVP
- 기준 문서: `PRD-MVP.md`, `REQUIREMENTS-MVP.md`
- 목적: 요구사항을 화면·API·데이터 동작 단위로 구체화하여 개발에 사용한다.
- 상태: 개발 기준 초안

## 2. 공통 규칙

### 2.1 사용자와 권한

| 구분 | 인증 | 권한 |
|---|---|---|
| 비로그인 사용자 | 불필요 | 공개 화면과 인증 화면 접근 |
| 일반 사용자 | 필요 | 자신의 데이터 관리, 공개 팁 작성·신고 |
| 운영자 | 필요 | 일반 사용자 기능과 운영자 기능 |

- 기본 역할은 `USER`다.
- 운영자 API는 `ADMIN` 역할만 호출할 수 있다.
- 개인 리소스 API는 인증 사용자와 리소스 소유자가 같은지 서버에서 확인한다.
- 권한이 없는 리소스는 존재 여부를 추측할 수 없도록 `404`로 응답하는 방식을 우선 검토한다.
- 인증은 JWT Access Token과 JWT Refresh Token을 사용한다.
- 두 토큰은 HttpOnly Cookie로 전달한다.
- Access Token 만료 시간은 15분, Refresh Token 만료 시간은 7일이다.
- Refresh Token은 해시하여 PostgreSQL에 저장하고 재발급 시 Rotation한다.

### 2.2 공통 응답 형식

성공 응답은 기능별 데이터 구조를 사용한다. 오류 응답은 다음 공통 구조를 사용한다.

```json
{
  "code": "RESOURCE_NOT_FOUND",
  "message": "요청한 리소스를 찾을 수 없습니다.",
  "details": {}
}
```

오류 응답은 6장의 오류 코드와 공통 응답 형식을 사용한다.

성공 응답 규칙:

- 단일 리소스는 리소스 객체를 직접 반환한다.
- 목록은 `items`, `page`, `size`, `totalElements`를 포함한다.
- JSON 필드는 `camelCase`를 사용한다.

HTTP 상태 코드 규칙:

- `200`: 조회·수정 성공
- `201`: 생성 성공
- `204`: 삭제·로그아웃 성공
- `400`: 입력값 오류
- `401`: 인증 필요
- `403`: 권한 부족
- `404`: 리소스 없음
- `409`: 중복·상태 충돌
- `422`: 도메인 검증 실패
- `500`: 서버 오류

### 2.3 날짜·식별자

- 식별자는 서버가 생성한다.
- 날짜와 시각은 API에서 일관된 ISO 8601 형식으로 주고받는다.
- 생성·수정 시각은 서버 시각을 사용한다.
- 사용자가 입력한 조리 날짜와 서버 기록 시각은 구분한다.

### 2.4 목록 응답

목록 API는 다음 형태를 기본으로 검토한다.

```json
{
  "items": [],
  "page": 0,
  "size": 20,
  "totalElements": 0
}
```

목록 기본 크기는 20, 최대 크기는 100으로 한다.

## 3. 화면 기능명세

### 3.1 인증 화면

#### 회원가입

입력:

- 이메일
- 비밀번호

동작:

1. 사용자가 입력값을 제출한다.
2. 서버가 이메일 형식과 중복 여부를 확인한다.
3. 비밀번호를 해시하여 계정을 생성한다.
4. 기본 역할 `USER`를 부여한다.
5. 회원가입 성공 후 로그인 화면 또는 로그인 상태로 이동한다.

실패:

- 입력값이 유효하지 않으면 필드별 오류를 표시한다.
- 이미 가입된 이메일이면 중복 오류를 표시한다.

#### 로그인

동작:

1. 이메일과 비밀번호를 제출한다.
2. 서버가 인증 정보를 확인한다.
3. 성공하면 JWT Access Token과 Refresh Token을 HttpOnly Cookie로 발급한다.
4. pending share가 있으면 저장 확인 화면으로 이동한다.
5. 없으면 홈으로 이동한다.

### 3.2 홈 화면

표시:

- 최근 저장 레시피
- 메뉴 목록
- URL 저장 입력 진입점
- 공유 수신 진입점

동작:

- 레시피를 선택하면 레시피 상세로 이동한다.
- 메뉴를 선택하면 메뉴 상세로 이동한다.
- URL 저장을 선택하면 저장 확인 화면으로 이동한다.
- 비로그인 사용자는 인증 화면으로 이동한다.

### 3.3 레시피 저장 확인 화면

표시:

- 입력 URL
- 추출된 제목
- 썸네일
- 설명
- 플랫폼
- 메뉴 선택
- 새 메뉴 생성 입력
- 저장 버튼

동작:

1. URL을 입력하거나 공유 데이터를 전달받는다.
2. 클라이언트가 미리보기 API를 호출한다.
3. 서버가 메타데이터를 추출한다.
4. 추출 실패 시 원본 URL과 빈 메타데이터를 표시한다.
5. 사용자가 메뉴를 선택하거나 새 메뉴를 생성한다.
6. 저장 버튼을 누르면 실제 레시피가 생성된다.
7. 중복이면 기존 레시피 안내를 표시한다.

필수 조건:

- URL이 없으면 저장할 수 없다.
- 메뉴가 확정되지 않으면 저장할 수 없다.
- 메타데이터 추출 실패만으로 저장을 차단하지 않는다.

### 3.4 메뉴 목록·상세 화면

메뉴 목록 표시:

- 메뉴명
- 소속 레시피 수
- 최근 조리일
- 메뉴 검색 입력

메뉴 동작:

- 메뉴 생성
- 메뉴명 수정
- 메뉴 삭제
- 메뉴 상세 이동

메뉴 상세 표시:

- 메뉴명
- 소속 레시피 목록
- 레시피별 별점 요약
- 레시피 추가 진입점

소속 레시피가 있는 메뉴는 삭제할 수 없다. 사용자는 레시피를 다른 메뉴로 이동한 뒤 삭제해야 한다.

### 3.5 레시피 상세 화면

표시:

- 제목
- 썸네일
- 원본 링크 이동 버튼
- 메뉴명
- 저장일
- 조리 기록 목록
- 조리 기록 추가 버튼
- 익명 팁 목록
- 익명 팁 작성 버튼
- 공개 팁 신고 버튼

동작:

- 원본 링크는 저장된 URL로 이동한다.
- 조리 기록 추가는 조리 기록 작성 화면을 연다.
- 팁 작성은 공개 팁 작성 화면 또는 작성 영역을 연다.
- 공개 팁은 `PUBLISHED` 상태만 표시한다.
- `UNPUBLISHED`, `HIDDEN`, `DELETED` 팁은 일반 사용자에게 표시하지 않는다.

### 3.6 조리 기록 작성·수정 화면

입력:

- 별점: 1~5점
- 메모
- 조리 날짜
- 익명으로 공개하기 체크박스

기본 동작:

- 체크박스 기본값은 해제다.
- 체크 해제 상태로 저장하면 `PRIVATE` 조리 기록을 생성한다.
- 체크 상태로 저장하면 조리 기록과 공개 팁을 함께 생성한다.
- 공개 전 개인정보 입력 주의 문구를 표시한다.

공개 전환:

- 기존 개인 메모를 공개로 전환하면 기존 메모 내용을 공개 팁으로 복사한다.
- 공개 팁은 레시피 상세에서 즉시 표시한다.
- 개인 메모와 공개 팁은 별도 리소스로 관리한다.

### 3.7 익명 팁 작성·수정 화면

입력:

- 공개 팁 내용
- 선택 별점

동작:

- 로그인 사용자만 작성할 수 있다.
- 조리 기록이 없어도 작성할 수 있다.
- 레시피 상세에서 작성한 팁은 해당 `recipe_link_id`에 연결한다.
- 저장 성공 시 `PUBLISHED` 상태로 즉시 표시한다.
- 수정 성공 시 공개 화면에 즉시 반영한다.
- 작성자 정보는 화면에 표시하지 않는다.

### 3.8 공개 취소·재공개·삭제

#### 공개 취소

- 작성자가 자신의 공개 팁에서 공개 취소를 선택한다.
- 상태를 `UNPUBLISHED`로 변경한다.
- 팁은 일반 사용자에게서 즉시 숨긴다.
- 팁 ID, 작성일, 내용은 유지한다.

#### 재공개

- 작성자가 `UNPUBLISHED` 팁을 다시 공개한다.
- 동일 팁을 `PUBLISHED`로 변경한다.
- 기존 팁 ID와 작성일을 유지한다.

#### 삭제

- 작성자가 자신의 팁을 삭제한다.
- 상태를 `DELETED`로 변경한다.
- 일반 조회에서 제외한다.
- `DELETED` 상태는 복구하지 않는다.

### 3.9 공개 팁 신고

신고 사유:

- 욕설·혐오 표현
- 개인정보 노출
- 광고·홍보·스팸
- 요리와 관계없는 내용
- 기타

동작:

1. 로그인 사용자가 공개 팁의 신고 버튼을 누른다.
2. 신고 사유를 선택한다.
3. 서버가 동일 사용자의 기존 신고 여부를 확인한다.
4. 최초 신고면 신고를 저장하고 횟수를 증가시킨다.
5. 신고 횟수가 5회가 되면 상태를 `HIDDEN`으로 변경한다.
6. 중복 신고면 새로운 신고를 저장하지 않는다.

### 3.10 운영자 화면

표시:

- 신고된 팁 목록
- 팁 내용
- 레시피 제목과 원본 URL
- 신고 횟수
- 신고 사유
- 현재 상태
- 처리 시각

동작:

- `HIDDEN` 팁을 `PUBLISHED`로 해제할 수 있다.
- 팁을 `HIDDEN`으로 변경할 수 있다.
- 팁을 `DELETED`로 변경할 수 있다.
- 처리 결과와 처리 시각을 기록한다.
- `ADMIN`이 아닌 사용자는 접근할 수 없다.
- 신고 사유 코드는 `ABUSE`, `PRIVACY`, `SPAM`, `OFF_TOPIC`, `OTHER`다.
- 운영자 처리 이력은 별도 로그로 저장한다.

### 3.11 검색·필터 화면

검색 대상:

- 메뉴명
- 레시피 제목

필터:

- 플랫폼
- 별점

정렬:

- 최근 저장순
- 최근 조리 기록순

검색 규칙:

- 검색어 앞뒤 공백을 제거한다.
- 메뉴명과 레시피 제목에 부분 일치한다.
- 대소문자를 구분하지 않는다.
- 허용되지 않은 정렬값은 오류로 처리한다.
- 목록 기본 크기는 20, 최대 크기는 100이다.
- 페이지 번호는 0부터 시작한다.

검색 결과에는 로그인한 사용자의 개인 레시피만 포함한다.

### 3.12 PWA 공유 수신

수신 데이터:

- `title`
- `text`
- `url`

동작:

1. 사용자가 외부 앱에서 공유 버튼을 누른다.
2. Cookmark PWA가 공유 데이터를 수신한다.
3. 로그인 상태를 확인한다.
4. 비로그인 상태면 데이터를 임시 보존하고 로그인 화면으로 이동한다.
5. 로그인 후 pending share를 복원한다.
6. 저장 확인 화면에서 메뉴를 선택한다.
7. 사용자가 저장을 확정한다.

URL이 없는 경우 저장하지 않고 URL 입력을 요청한다.

## 4. API 기능명세

### 4.1 공통 API 규칙

- Base path: `/api/v1`
- 요청 본문은 JSON을 기본으로 한다.
- 인증이 필요한 API는 인증 상태를 검증한다.
- 소유권이 필요한 API는 URL의 ID와 인증 사용자를 비교한다.
- 성공·실패 HTTP 상태 코드와 오류 코드는 6장의 규칙을 사용한다.
- 상태 변경 요청은 CSRF 방어와 Origin 검증을 거친다.

### 4.2 인증 API

#### `POST /auth/signup`

목적: 이메일 기반 계정 생성

요청:

```json
{
  "email": "user@example.com",
  "password": "********"
}
```

성공:

- `201 Created`
- `USER` 역할 계정 생성

실패:

- 이메일 형식 오류
- 비밀번호 정책 위반
- 이메일 중복

#### `POST /auth/login`

목적: 사용자 인증

요청:

```json
{
  "email": "user@example.com",
  "password": "********"
}
```

성공:

- 인증 상태 생성
- 사용자 기본 정보 반환 여부는 구현 방식에 따라 결정

실패:

- 인증 정보 불일치
- 비활성 또는 탈퇴 계정

#### `POST /auth/logout`

목적: 인증 상태 종료

성공:

- 인증 상태 폐기
- `204 No Content`
- Access Token과 Refresh Token Cookie 삭제

### 4.3 레시피 API

#### `POST /recipes/preview`

목적: URL 메타데이터 미리보기

요청:

```json
{
  "url": "https://example.com/recipe"
}
```

응답:

```json
{
  "url": "https://example.com/recipe",
  "canonicalUrl": "https://example.com/recipe",
  "platform": "BLOG",
  "title": "Recipe title",
  "thumbnailUrl": "https://example.com/image.jpg",
  "description": "Recipe description",
  "metadataStatus": "SUCCESS"
}
```

메타데이터 추출 실패 시 `metadataStatus`를 실패 상태로 반환하되 저장 가능한 URL은 유지한다.

- 메타데이터 요청 전체 타임아웃은 5초다.
- 최대 응답 본문 크기는 1MB다.
- 리다이렉트는 최대 3회다.
- localhost·루프백·사설 IP 등 내부 주소 접근을 차단한다.

#### `POST /recipes`

목적: 레시피 링크 저장

입력:

- URL
- 메뉴 ID 또는 새 메뉴명
- 미리보기 결과에서 사용자가 수정한 값

동작:

- URL 정규화
- 사용자별 중복 확인
- 메뉴 소유권 확인
- 레시피 생성

#### `GET /recipes`

목적: 로그인 사용자의 레시피 목록 조회

지원 조건:

- 메뉴 필터
- 플랫폼 필터
- 검색어
- 정렬
- 페이지네이션

#### `GET /recipes/{recipeId}`

목적: 레시피 상세 조회

포함 데이터:

- 레시피 기본 정보
- 메뉴 정보
- 저장일
- 사용자의 조리 기록
- 공개 팁 목록

#### `PATCH /recipes/{recipeId}`

목적: 자신의 레시피 정보 수정

소유권이 없는 경우 수정하지 않는다.

#### `DELETE /recipes/{recipeId}`

목적: 자신의 레시피 삭제

- 공개 팁은 레시피 스냅샷으로 유지한다.
- `recipe_link_id`는 원본 삭제 시 `NULL`로 변경한다.

### 4.4 메뉴 API

#### `GET /menus`

자신의 메뉴 목록과 메뉴별 레시피 수를 반환한다.

#### `POST /menus`

요청:

```json
{
  "name": "김치찌개"
}
```

사용자별 동일 메뉴명 중복은 허용하지 않는다.

#### `PATCH /menus/{menuId}`

자신의 메뉴명만 수정할 수 있다.

#### `DELETE /menus/{menuId}`

소속 레시피가 있는 메뉴는 삭제할 수 없다. 레시피를 다른 메뉴로 이동한 뒤 삭제해야 한다.

#### `GET /menus/{menuId}/recipes`

자신의 특정 메뉴에 속한 레시피를 반환한다.

### 4.5 조리 기록 API

#### `POST /recipes/{recipeId}/cook-logs`

요청:

```json
{
  "rating": 4,
  "memo": "다음에는 간장을 조금 줄인다.",
  "cookedAt": "2026-08-16",
  "publishAnonymously": false
}
```

동작:

- `publishAnonymously=false`: 개인 기록만 생성
- `publishAnonymously=true`: 개인 기록과 `PublicTip`을 함께 생성
- 공개 팁 생성 시 상태는 `PUBLISHED`
- 두 데이터 생성은 하나의 트랜잭션으로 처리한다.

#### `GET /recipes/{recipeId}/cook-logs`

자신의 조리 기록만 반환한다.

#### `PATCH /cook-logs/{cookLogId}`

- 자신의 조리 기록만 수정한다.
- 공개 체크를 해제하면 연결된 공개 팁을 `UNPUBLISHED`로 변경한다.
- 다시 체크하면 기존 공개 팁을 `PUBLISHED`로 변경하거나, 없는 경우 새 공개 팁을 생성한다.
- 공개 팁 자체의 내용 수정은 PublicTip API를 사용한다.

#### `DELETE /cook-logs/{cookLogId}`

- 개인 조리 기록을 삭제한다.
- 연결된 공개 팁은 개인 기록과 분리되어 유지될 수 있어야 한다.

### 4.6 공개 팁 API

#### `POST /recipes/{recipeId}/public-tips`

목적: 조리 기록 없이 레시피에 직접 익명 팁 작성

요청:

```json
{
  "publicText": "감자를 먼저 볶고 물을 넣으면 더 고소합니다.",
  "rating": 5
}
```

성공 시:

- `PUBLISHED` 상태로 저장
- 작성자 정보 없이 조회 가능

#### `GET /recipes/{recipeId}/public-tips`

- `PUBLISHED` 상태만 반환한다.
- 작성자 식별 정보는 응답에서 제외한다.
- 최신 작성순으로 반환한다. 별점순 정렬은 MVP에서 제공하지 않는다.

#### `PATCH /public-tips/{publicTipId}`

- 작성자 본인 또는 운영자만 호출할 수 있다.
- 작성자는 공개 문구와 별점을 수정할 수 있다.
- 작성자 수정은 저장 즉시 `PUBLISHED` 내용에 반영한다.

#### `DELETE /public-tips/{publicTipId}`

- 작성자 본인만 삭제할 수 있다.
- 상태를 `DELETED`로 변경한다.
- 삭제 후 복구하지 않는다.

#### `POST /public-tips/{publicTipId}/reports`

요청:

```json
{
  "reason": "OFF_TOPIC"
}
```

동작:

- 동일 사용자 중복 신고를 차단한다.
- 신고 저장 후 `report_count`를 계산한다.
- 5회 누적 시 `HIDDEN`으로 변경한다.

#### `GET /admin/public-tip-reports`

- `ADMIN`만 호출할 수 있다.
- 신고된 팁과 신고 사유·횟수·현재 상태를 반환한다.

#### `PATCH /admin/public-tips/{publicTipId}/status`

요청:

```json
{
  "status": "PUBLISHED"
}
```

허용 동작:

- `HIDDEN → PUBLISHED`
- `PUBLISHED → HIDDEN`
- 운영 판단에 따른 `DELETED`

### 4.7 공유 수신 API

#### `POST /share-target/receive`

목적: PWA 공유 데이터 수신

요청:

```json
{
  "title": "Recipe title",
  "text": "Recipe note",
  "url": "https://example.com/recipe"
}
```

동작:

- URL이 있으면 저장 확인 흐름으로 전달한다.
- URL이 없으면 제목·텍스트를 임시 보존하고 URL 입력을 요청한다.
- 비로그인 공유 데이터는 로그인 후 복원한다.
- 공유 요청만으로 레시피를 자동 확정하지 않는다.

## 5. 데이터 동작 명세

### 5.0 인증 데이터

사용자 계정과 인증 수단을 분리한다.

```text
User
 └─ AuthIdentity
     ├─ provider: LOCAL | GOOGLE | KAKAO
     ├─ providerUserId
     └─ passwordHash nullable
```

- MVP에서는 `LOCAL` 인증 수단만 생성한다.
- 향후 소셜 로그인 성공 시 외부 공급자의 사용자 식별자를 `AuthIdentity`에 연결한다.
- 외부 OAuth Access Token은 Cookmark API 인증에 직접 사용하지 않는다.
- 인증 성공 후 Cookmark 자체 JWT Access Token과 Refresh Token을 발급한다.
- Refresh Token은 원문이 아닌 해시와 토큰 계열 식별자를 저장한다.
- 이미 사용된 Refresh Token이 재사용되면 같은 토큰 계열을 모두 폐기한다.

### 5.1 공개 팁 상태

```text
PUBLISHED ── 작성자 공개 취소 ──> UNPUBLISHED
UNPUBLISHED ── 작성자 재공개 ──> PUBLISHED
PUBLISHED ── 신고 5회 ──> HIDDEN
HIDDEN ── 운영자 해제 ──> PUBLISHED
PUBLISHED ── 작성자 삭제 ──> DELETED
UNPUBLISHED ── 작성자 삭제 ──> DELETED
```

- `DELETED`는 재공개할 수 없다.
- 신고로 숨겨진 팁은 운영자가 검토 후 다시 공개할 수 있다.
- 작성자가 공개를 취소한 팁은 기존 ID와 작성일을 유지한다.

운영자 처리 이력은 `PublicTipModerationLog`로 저장한다.

- publicTipId
- adminUserId
- previousStatus
- newStatus
- reason
- createdAt

### 5.2 회원 탈퇴

1. 탈퇴 요청자의 현재 JWT 인증 상태를 확인한다.
2. 개인 메뉴·레시피·조리 기록을 삭제 대상에 포함한다.
3. 공개 팁은 레시피 제목·원본 URL·공개 문구 스냅샷을 유지한다.
4. `author_user_id`를 `NULL`로 변경한다.
5. 탈퇴한 계정으로 재로그인할 수 없도록 처리한다.

별도의 비밀번호 재입력은 MVP에서 요구하지 않는다. 탈퇴가 완료되면 해당 사용자의 모든 Refresh Token을 폐기한다.

### 5.3 원본 레시피 삭제

- `PublicTip`의 레시피 참조는 nullable로 관리한다.
- 원본 레시피 삭제 시 `recipe_link_id`를 해제한다.
- 공개 팁은 스냅샷 제목과 URL로 계속 표시할 수 있어야 한다.

## 6. 오류 처리 명세

| 상황 | HTTP 상태 코드 | 오류 코드 |
|---|---:|---|
| 인증되지 않은 요청 | 401 | `UNAUTHENTICATED` |
| 관리자 권한 없음 | 403 | `FORBIDDEN` |
| 존재하지 않는 리소스 | 404 | `RESOURCE_NOT_FOUND` |
| 잘못된 URL | 400 | `INVALID_URL` |
| 잘못된 입력값 | 400 | `VALIDATION_ERROR` |
| 중복 이메일 | 409 | `EMAIL_ALREADY_EXISTS` |
| 중복 메뉴명 | 409 | `MENU_ALREADY_EXISTS` |
| 중복 레시피 URL | 409 또는 기존 리소스 반환 | `RECIPE_ALREADY_EXISTS` |
| 중복 신고 | 409 | `REPORT_ALREADY_EXISTS` |
| 메타데이터 추출 실패 | 200 또는 저장 가능 응답 | `METADATA_FETCH_FAILED` |
| 삭제된 팁 재공개 | 409 | `PUBLIC_TIP_NOT_REPUBLISHABLE` |

오류 코드는 이 문서의 오류 코드 표를 기준으로 구현한다.

## 7. 보안 구현 기준

- 비밀번호는 최소 8자, 최대 72자이며 대문자·소문자·숫자·특수문자 혼용은 강제하지 않는다.
- 비밀번호 유출 데이터베이스 연동은 MVP에서 제공하지 않는다.
- 비밀번호는 BCrypt 또는 동등한 안전한 해시 알고리즘으로 저장한다.
- JWT Access Token과 Refresh Token은 HttpOnly·Secure Cookie로 전달한다.
- Access Token은 15분, Refresh Token은 7일 동안 유효하다.
- Refresh Token은 해시하여 PostgreSQL에 저장하고 Rotation한다.
- 이미 사용된 Refresh Token이 다시 사용되면 해당 토큰 계열을 폐기한다.
- 로그아웃·회원 탈퇴·비밀번호 변경 시 Refresh Token을 폐기한다.
- Access Token Cookie는 `HttpOnly`, `Secure`, `SameSite=Lax`, `Path=/`를 사용한다.
- Refresh Token Cookie는 `HttpOnly`, `Secure`, `SameSite=Strict`, `Path=/api/v1/auth`를 사용한다.
- CSRF 방어, CORS 허용 출처 제한, Origin 검증을 적용한다.
- 공개 팁 API 응답에서 `author_user_id`를 제거한다.
- 개인 레시피·메뉴·조리 기록 API마다 소유권을 확인한다.
- 관리자 API마다 `ADMIN` 역할을 확인한다.
- 메타데이터 요청은 SSRF 방어 정책을 적용한다.
- 외부 URL 요청은 5초 타임아웃, 1MB 응답 크기, 리다이렉트 3회 제한을 적용한다.

## 8. 테스트 시나리오

### 8.1 인증·권한

- 정상 회원가입
- 이메일 중복 회원가입
- 정상 로그인·로그아웃
- 인증 없이 개인 API 호출
- 일반 사용자의 관리자 API 호출
- 다른 사용자의 메뉴·레시피·조리 기록 접근

### 8.2 레시피 저장

- 정상 URL 저장
- 잘못된 스킴 URL
- 메타데이터 추출 성공
- 메타데이터 추출 실패 후 저장
- 동일 사용자 중복 URL
- 다른 사용자의 동일 URL 저장
- 메뉴 선택 저장
- 새 메뉴 생성과 동시에 저장

### 8.3 조리 기록·공개 팁

- 기본 비공개 조리 기록 생성
- 공개 체크 조리 기록 생성
- 레시피 상세에서 직접 공개 팁 생성
- 공개 팁 수정 즉시 반영
- 공개 취소 후 목록에서 숨김
- 공개 취소 팁 재공개
- 삭제 팁 재공개 차단
- 공개 팁 작성자 정보 미노출
- 회원 탈퇴 후 공개 팁 유지

### 8.4 신고·운영

- 정상 신고
- 동일 사용자의 중복 신고 차단
- 신고 4회 상태 유지
- 신고 5회 자동 숨김
- 운영자 숨김 해제
- 일반 사용자 운영자 API 접근 차단
- 운영자 삭제 후 팁 조회 제외

### 8.5 공유 수신

- URL 포함 Android 공유
- URL 없는 공유
- 비로그인 공유 후 로그인 복원
- 공유 데이터 자동 저장 방지

## 9. 결정 완료 항목

MVP 개발에 필요한 주요 정책은 결정했다.

- 비밀번호는 최소 8자, 최대 72자다.
- API 성공 응답·오류 응답·HTTP 상태 코드 규칙을 사용한다.
- 목록은 기본 20개, 최대 100개이며 페이지 번호는 0부터 시작한다.
- 소속 레시피가 있는 메뉴는 삭제할 수 없다.
- 메타데이터 요청은 5초·1MB·리다이렉트 3회 제한을 적용한다.
- 신고 사유는 `ABUSE`, `PRIVACY`, `SPAM`, `OFF_TOPIC`, `OTHER`다.
- 운영자 처리 이력은 별도 로그로 저장한다.
- 회원 탈퇴는 현재 JWT 인증으로 처리하고 Refresh Token을 폐기한다.
- 공개 팁은 최신 작성순으로 표시한다.

MVP 이후 소셜 로그인을 추가할 수 있도록 사용자 계정과 인증 수단을 분리한다. 향후 `AuthIdentity`에 `LOCAL`, `GOOGLE`, `KAKAO` 등의 공급자와 외부 사용자 식별자를 연결하고, 외부 OAuth 인증 성공 후에는 Cookmark 자체 JWT를 발급한다.

## 10. 구현 순서

1. 프로젝트와 Docker Compose 초기화
2. User·Menu·RecipeLink·CookLog·PublicTip·PublicTipReport 마이그레이션
3. 인증·역할·소유권 검증
4. 메뉴 CRUD
5. 레시피 미리보기·저장·조회
6. 조리 기록과 개인 메모
7. 레시피 상세 공개 팁
8. 공개 팁 상태 전이와 신고
9. 운영자 화면
10. 검색·필터·정렬
11. PWA 공유 수신
12. 통합 테스트와 모바일 수동 테스트
13. Docker 이미지·Terraform·Lightsail·GitHub Actions
