# Cookmark

요리 레시피 링크를 저장하고, 메뉴별로 관리하며, 조리 경험을 개인 메모와 익명 팁으로 남기는 서비스입니다.

현재 저장소는 기능 구현 전 최소 개발 템플릿입니다.

## 구조

```text
.
├─ backend/     Spring Boot + Java 21 + Gradle
├─ frontend/    Next.js + TypeScript + Tailwind CSS
├─ PRD-MVP.md
├─ REQUIREMENTS-MVP.md
└─ FUNCTIONAL-SPEC-MVP.md
```

## 사전 요구사항

- Java 21
- Node.js
- npm

Docker Compose와 PostgreSQL은 다음 단계에서 추가합니다.

## 백엔드 실행

```powershell
cd backend
.\gradlew.bat bootRun
```

기본 주소: `http://localhost:8080`

## 프론트엔드 의존성 설치 및 실행

PowerShell 실행 정책에 따라 `npm` 대신 `npm.cmd`를 사용할 수 있습니다.

```powershell
cd frontend
npm.cmd install
npm.cmd run dev
```

기본 주소: `http://localhost:3000`

## 문서

- [MVP PRD](PRD-MVP.md)
- [요구사항 정의서](REQUIREMENTS-MVP.md)
- [기능명세서](FUNCTIONAL-SPEC-MVP.md)
