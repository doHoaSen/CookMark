# Cookmark 전체 제품 PRD

## 1. 문서 목적

이 문서는 Cookmark를 단순한 레시피 링크 북마크가 아니라, 레시피 발견·정리·조리·회고·재활용을 연결하는 개인 요리 지식 서비스로 발전시키기 위한 전체 제품 맥락을 정의한다.

MVP는 `URL 저장 → 메뉴 그룹화 → 조리 메모 → 선택적 익명 팁 공개 → 다시 찾기`로 시작한다. 이후 공유 진입, 자동 메타데이터 추출, 개인화 검색, RAG, 장보기와 냉장고 추천으로 확장한다.

## 2. 제품 비전

알고리즘이 흘려보낸 레시피를 나만의 요리 자산으로 바꾼다.

## 3. 핵심 가치

### 발견 순간의 저장

사용자가 레시피를 발견한 앱에서 공유 버튼을 누르는 즉시 Cookmark로 가져온다.

### 메뉴 중심의 재구성

플랫폼이나 크리에이터가 아니라 메뉴를 중심으로 레시피를 비교한다.

### 조리 경험의 자산화

“다음에는 간장을 줄인다”와 같은 경험을 레시피에 연결해 다음 조리에 활용한다.

### 개인 데이터 기반 AI

일반적인 인터넷 레시피가 아니라 사용자가 직접 저장하고 조리한 기록을 근거로 답변한다.

## 4. 문제 구조

```text
콘텐츠 발견
  ↓
플랫폼별 저장소에 분산
  ↓
재검색 어려움
  ↓
조리 경험이 기록되지 않음
  ↓
같은 실수 반복
```

Cookmark는 다음 흐름으로 문제를 해결한다.

```text
공유로 수집
  ↓
메뉴 단위 정리
  ↓
조리 기록 축적
  ↓
키워드·의미 기반 검색
  ↓
개인화된 요리 보조
```

## 5. 제품 단계

### Phase 1: 링크 북마크 MVP

- URL 직접 저장
- Android 설치형 PWA 공유 수신
- 메뉴 생성 및 그룹화
- 레시피 상세
- 조리 메모와 별점
- 기본 비공개 조리 메모
- 사용자가 승인한 익명 조리 팁 공개
- 레시피 상세의 공개 팁 조회
- Open Graph 기반 기본 메타데이터 추출 및 실패 시 수동 입력
- 일반 검색

### Phase 2: 수집 자동화

- Open Graph 메타데이터 추출
- 플랫폼 자동 판별
- 제목 기반 메뉴명 추천
- 주요 재료·소요 시간·난이도 추출
- AI 결과 확인 및 사용자 수정

AI 결과는 자동 확정하지 않고 추천 결과로 제공한다. 사용자의 수정 결과를 축적해 메뉴 별칭과 분류 품질을 개선한다.

### Phase 3: 개인 지식 검색

- 레시피 설명, 사용자가 입력한 텍스트, 조리 메모 임베딩
- PostgreSQL pgvector 기반 벡터 검색
- 키워드 검색과 벡터 검색의 하이브리드 검색
- 검색 결과에 원본 레시피와 개인 메모 출처 표시

예시 질문:

- “내가 저장한 레시피 중 20분 안에 만들 수 있는 메뉴는?”
- “지난번에 짜다고 기록한 국물 요리 찾아줘.”
- “양파와 두부를 사용하는 레시피를 보여줘.”

### Phase 4: AI 요리 보조

- 저장한 레시피 요약
- 내 메모를 반영한 조리 전 체크리스트
- 재료가 부족할 때 대체 재료 제안
- 인분 변경에 따른 계량 변환
- 과거 조리 결과를 반영한 개인화 조언

AI 답변은 검색된 사용자 데이터에 근거해야 하며, 근거가 없을 경우 추측하지 않고 부족한 정보를 표시한다.

### Phase 5: 생활 도구 확장

- 레시피 재료 기반 장보기 목록
- 냉장고 재료 기반 추천
- 여러 레시피 재료 통합
- 식단 계획
- 조리 사진과 결과 비교
- 가족 또는 친구와 메뉴 공유
- 공개 팁을 기반으로 한 공개 피드와 커뮤니티 기능

## 6. 플랫폼 및 공유 전략

### 웹 앱

웹에서는 두 가지 공유를 제공한다.

1. Cookmark에서 다른 앱으로 레시피 링크 공유
2. 다른 앱에서 Cookmark로 링크 수신

첫 번째는 Web Share API의 `navigator.share()`를 사용한다. 두 번째는 PWA manifest의 `share_target`을 사용한다.

### PWA

PWA는 설치 후 운영체제 공유 메뉴의 대상으로 등록될 수 있다. 공유 대상은 title, text, URL을 받을 수 있으며, 북마크 생성처럼 서버 상태를 변경하는 경우 POST 수신이 적합하다.

MVP에서는 Android 설치형 PWA를 우선 지원한다. 브라우저와 운영체제에 따라 공유 대상 지원 범위가 다르므로 “모든 모바일 환경에서 동일하게 지원”을 제품 보장사항으로 두지 않는다.

### 네이티브 앱

사용자 유입과 공유 수신이 핵심 지표로 검증된 뒤 네이티브 앱을 검토한다.

- Android: `ACTION_SEND` 기반 text/plain 수신
- iOS: Share Extension 검토
- 공통 UI: React Native 또는 Expo 가능성 검토
- 웹과 앱 간 Universal Link/App Link 연결

네이티브 앱 도입 시에도 공유 데이터 수신 로직은 공통 백엔드 endpoint를 사용한다.

## 7. 전체 사용자 경험

### 신규 사용자

1. 서비스 소개
2. 이메일 또는 소셜 로그인
3. 첫 URL 저장 유도
4. 메뉴 생성
5. 저장 완료
6. “조리 후 메모 남기기” 안내

### 반복 사용자

1. 영상 발견
2. OS 공유 메뉴에서 Cookmark 선택
3. 메뉴 확인
4. 저장
5. 조리 후 알림 또는 홈 화면에서 기록 추가

### AI 사용자

1. 자연어 질문 입력
2. 관련 레시피와 조리 메모 검색
3. 답변과 근거 확인
4. 원본 레시피 열기
5. 조리 후 새 기록 저장

## 8. 도메인 모델 확장안

### User

사용자와 인증 정보.

### Menu

사용자별 대표 메뉴. 향후 표준 메뉴 사전과 연결할 수 있다.

필드 예시:

- id
- user_id
- name
- normalized_name
- standard_menu_id
- created_at

### RecipeLink

사용자가 저장한 원본 링크.

- id
- user_id
- menu_id
- url
- normalized_url
- platform
- title
- thumbnail_url
- description
- source_status
- created_at

### RecipeContent

링크에서 추출했거나 사용자가 입력한 비정형 원문.

- recipe_link_id
- content_type
- content
- language
- extraction_status
- source_metadata

`content_type` 예시:

- DESCRIPTION
- TRANSCRIPT
- USER_NOTE
- MANUAL_INPUT

### RecipeMetadata

AI가 추출한 구조화 정보.

- recipe_link_id
- ingredients
- estimated_minutes
- difficulty
- tools
- extraction_model
- confidence
- confirmed_by_user

### CookLog

실제 조리 경험.

- id
- user_id
- recipe_link_id
- rating
- memo
- cooked_at
- photo_asset_id

개인 조리 메모는 기본적으로 비공개다. `visibility`는 `PRIVATE`, `PUBLIC_PENDING`, `PUBLIC`, `HIDDEN` 중 하나로 관리한다. 공개용 내용은 개인 메모와 분리된 `public_memo`로 저장할 수 있다.

### PublicTip

사용자가 명시적으로 승인한 익명 조리 팁이다.

- id
- cook_log_id
- user_id: 신고·삭제·운영 처리를 위한 내부 식별자
- source_url_normalized 또는 recipe_link_id
- public_text
- rating
- status: `PENDING`, `PUBLISHED`, `HIDDEN`, `DELETED`
- published_at
- created_at
- updated_at

외부에는 작성자 이름·이메일·프로필 정보를 노출하지 않는다. 기존 개인 메모를 자동 공개하지 않으며, 공개 전 미리보기와 개인정보 주의 안내를 제공한다.

### EmbeddingChunk

RAG 검색을 위한 텍스트 조각.

- id
- user_id
- recipe_link_id
- cook_log_id
- content_type
- chunk_text
- embedding
- embedding_model
- created_at

### ShoppingListItem

향후 장보기 목록.

- id
- user_id
- shopping_list_id
- ingredient_name
- quantity
- checked

## 9. AI 및 RAG 아키텍처

```text
[Next.js / PWA / Native App]
          ↓
      [Spring Boot]
       ├── 인증·레시피·메뉴·조리 기록
       ├── URL 메타데이터 수집
       ├── AI 구조화 추출
       └── RAG 검색·답변
          ↓
 [PostgreSQL + pgvector]
          ↓
 [Gemini 또는 교체 가능한 LLM Provider]
```

초기에는 하나의 Spring Boot 애플리케이션 안에서 모듈을 분리한다. 노트북에서는 Docker Compose로 개발·테스트·로컬 AI 실험을 수행하고, AWS Lightsail에서는 Docker Compose 기반 실제 배포를 운영한다. 트래픽이나 처리 시간이 증가하면 다음을 별도 worker로 분리한다.

- URL fetch worker
- metadata extraction worker
- embedding worker
- chat generation worker

## 10. RAG 처리 원칙

### 수집

- 제목
- 설명
- 허용 가능한 텍스트 콘텐츠
- 사용자 메모
- 조리 기록

### 정제

- HTML 제거
- 중복 텍스트 제거
- 사용자별 접근 권한 부여
- 원문 출처 보존

### 청킹

재료, 조리 단계, 사용자 메모가 지나치게 섞이지 않도록 콘텐츠 유형과 레시피 단위로 분리한다.

### 검색

1. 사용자 권한 필터
2. 키워드 검색
3. 벡터 검색
4. 점수 결합
5. 관련 링크와 메모를 LLM에 전달

### 답변

- 답변 근거 링크 표시
- 개인 메모인지 원본 설명인지 구분
- 불확실한 정보 표시
- 의료·알레르기·식품 안전과 관련된 내용은 단정하지 않음

## 11. 플랫폼 데이터 정책

Cookmark는 원본 콘텐츠를 재배포하는 서비스가 아니라 원본 링크와 개인 기록을 관리하는 서비스로 정의한다.

- 영상 파일을 자체 저장하지 않는다.
- 원본 콘텐츠의 저작권을 주장하지 않는다.
- 원본 링크로 이동하는 기능을 제공한다.
- 플랫폼 API와 이용약관을 우선한다.
- 자막·설명·본문 수집이 불가능하면 사용자 직접 입력을 제공한다.
- 파싱 결과와 원본을 구분한다.

특히 플랫폼별 HTML 스크래핑을 핵심 기능으로 두면 구조 변경과 차단에 취약하므로, 수집 실패를 제품 오류가 아닌 대체 입력 흐름으로 처리한다.

## 12. 기술 스택 진화 계획

### 초기

- Next.js + TypeScript
- Java 21 + Spring Boot + Spring Security + Spring Data JPA
- PostgreSQL
- Flyway + Gradle
- PWA manifest 및 service worker
- 단일 API 서버

### AI 도입

- Gemini API 또는 교체 가능한 LLM adapter
- Spring AI structured output
- pgvector
- 비동기 작업 큐

### 규모 확장

- Redis 기반 job queue
- 별도 AI worker
- object storage
- observability와 rate limit
- 필요할 때만 AI microservice 분리

Spring Boot와 별도 Python AI 서버를 동시에 도입하지 않는다. 외부 LLM 호출과 RAG는 Spring AI로 먼저 구현하고, 직접 모델 실험·음성·이미지 처리처럼 Python이 유리한 작업이 생길 때만 AI Worker를 분리한다. 팀 규모와 트래픽이 증가할 때 서비스 경계를 분리한다.

## 13. 비용 및 무료 운영 원칙

개발·포트폴리오·소규모 테스트는 노트북과 AWS Free Tier 또는 저비용 계층을 활용한다. 무료 요금제를 영구 운영 보장으로 간주하지 않는다.

초기 실제 배포는 AWS Lightsail 단일 인스턴스를 사용한다. Spring Boot와 PostgreSQL은 Docker Compose로 함께 실행하며, 비용을 낮추는 대신 고가용성과 관리형 DB를 제공하지 않는 구성을 명시적으로 수용한다. 이후 데이터 안정성이나 트래픽 요구가 커지면 RDS와 ECS Fargate로 이전한다.

- 노트북: 개발, Docker Compose, PostgreSQL, 테스트, Terraform plan, 로컬 AI 실험
- AWS Lightsail: 실제 배포, 외부 접속, HTTPS, 도메인, GitHub Actions 배포
- 초기에는 NAT Gateway, ALB, RDS Multi-AZ, 상시 GPU, ECS 다중 서비스를 사용하지 않는다.
- PostgreSQL은 주기적 백업과 Lightsail 스냅샷을 구성한다.
- AWS 예산 알림과 Free Tier 사용량을 확인한다.

- AI 요청 결과 캐싱
- 같은 URL 재분석 방지
- 사용자별 일일 AI 요청 제한
- 비동기 분석
- 무료 DB의 만료·용량·백업 정책 확인
- 파일은 로컬 디스크가 아닌 외부 저장소 사용
- 배포 서비스의 cold start와 sleep 고려

AI API rate limit은 모델과 프로젝트 등급에 따라 달라지므로 환경변수와 provider adapter로 교체 가능하게 만든다.

## 14. 보안·개인정보·신뢰성

- URL fetch 시 SSRF 방어
- 내부 IP와 로컬호스트 요청 차단
- 요청 타임아웃과 응답 크기 제한
- 사용자별 DB 권한 검증
- 비밀번호 해시 저장
- API key 서버 보관
- 사진과 메모의 삭제 기능 제공
- 익명 공개 팁의 신고·숨김·삭제 기능 제공
- AI가 원문에 없는 사실을 만들어내지 않도록 근거 기반 프롬프트 사용
- 공유 수신 URL의 중복·재전송 처리

## 15. 제품 지표

### 핵심 지표

- 링크 저장 완료율
- 공유 → 저장 완료 전환율
- 저장 후 재방문율
- 메뉴별 레시피가 2개 이상 쌓인 사용자 비율
- 조리 메모 작성률
- 조리 기록의 익명 팁 공개 전환율
- 공개 팁 조회 후 원본 레시피 클릭률
- 저장 후 실제 원본 링크를 다시 연 비율

### AI 단계 지표

- AI 메뉴 추천 확인율
- AI 분류 수정률
- 구조화 추출 성공률
- 자연어 질문의 근거 포함 답변 비율
- AI 답변 후 원본 레시피 클릭률

초기에는 AI 정확도보다 저장 완료율과 재방문율을 우선한다.

## 16. 단계별 완료 기준

### MVP 완료

- URL 저장
- 메뉴 그룹화
- 조리 메모
- 기본 비공개 조리 메모
- 선택적 익명 조리 팁 공개와 공개 팁 조회
- 일반 검색
- Android PWA 공유 수신

### 자동화 완료

- 메타데이터 추출
- 메뉴 추천
- 실패 시 수동 입력
- 비동기 처리와 재시도

### RAG 완료

- 개인 데이터 권한 필터
- 레시피·메모 임베딩
- 하이브리드 검색
- 답변 근거 표시

### 모바일 앱 완료

- Android 공유 수신
- iOS 공유 확장 검증
- 앱 링크와 웹 링크 연결
- 미로그인 공유 데이터 복원

## 17. 제품 원칙

1. 공유 직후 저장까지의 단계를 최소화한다.
2. AI 실패가 핵심 저장 실패로 이어지지 않게 한다.
3. 사용자가 수정할 수 없는 자동 분류는 제공하지 않는다.
4. 원본 링크와 개인 메모를 명확히 구분한다.
5. 검색 답변에는 근거를 표시한다.
6. 익명 공개 팁은 사용자의 명시적 동의 후에만 게시한다.
7. 공개 팁의 신고·숨김·삭제 경로를 제공한다.
8. 무료·저비용 인프라의 한계를 제품 보장사항과 혼동하지 않는다.
9. MVP의 핵심 사용자 흐름을 검증한 뒤 복잡한 AI와 네이티브 기능을 추가한다.
