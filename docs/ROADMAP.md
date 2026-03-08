# 노션 기반 견적서 관리 시스템 - 개발 로드맵

노션을 데이터베이스로 활용하여 견적서를 관리하고, 클라이언트가 웹에서 조회 및 PDF 다운로드할 수 있는 시스템

**📊 전체 진행률: 100% 완료** (모든 Phase 완료 ✅)

---

## Phase 1: 프로젝트 초기 설정 ✅ (1-2일)

> 📌 **이 순서인 이유**: 모든 개발의 기반이 되는 환경 구성 없이는 어떤 기능도 구현할 수 없다. 프레임워크, 도구, 타입 시스템을 먼저 확립해야 이후 단계가 일관된 품질로 진행된다.

### Task 001: 프로젝트 기반 구성

- [x] Next.js 15.5.3 + TypeScript 프로젝트 초기화
- [x] TailwindCSS v4 + shadcn/ui (new-york 스타일) 설정
- [x] 환경 변수 구조 설정 (`.env.local`: `NOTION_API_KEY`, `NOTION_DATABASE_ID`)
- [x] 기본 레이아웃 (`layout.tsx`, `globals.css`)
- [x] ESLint + Prettier + Husky + lint-staged 개발 환경 구성
- [x] App Router 기반 라우트 구조 구현 (`/invoice/[id]`)

### Task 002: 타입 시스템 설계

- [x] 견적서 데이터 타입 정의 (`types/invoice.ts`)
- [x] Notion API 응답 타입 정의 (`types/notion.ts`)
- [x] PDF 생성 관련 타입 정의 (`types/pdf.ts`)
- [x] 인증 관련 타입 정의 (`types/auth.ts`)
- [x] 공통 상수 정의 (`lib/constants.ts`)

**완료 기준**: `npm run dev` 정상 실행, `npm run check-all` 통과, TypeScript 컴파일 에러 없음

---

## Phase 2: 공통 모듈 개발 ✅ (2-3일)

> 📌 **이 순서인 이유**: 개별 기능(견적서 조회, PDF 생성)보다 먼저 공통 모듈을 만들어야 한다. Notion API 클라이언트, 포맷 유틸리티, 레이아웃 컴포넌트는 여러 곳에서 재사용되므로, 먼저 단단하게 만들어두면 이후 기능 개발 속도가 크게 빨라진다.

### Task 003: Notion API 클라이언트

- [x] Notion API 클라이언트 초기화 (`lib/notion.ts`)
- [x] 견적서 데이터 조회 서비스 (`lib/services/invoice.service.ts`)
- [x] Notion 데이터 파싱 유틸리티 (`lib/utils/notion-parser.ts`)
- [x] 환경 변수 검증 (`lib/env.ts`)

### Task 004: 공통 유틸 및 레이아웃

- [x] 포맷 유틸리티 (`lib/format.ts` - 날짜, 금액 등)
- [x] 공통 유틸 함수 (`lib/utils.ts`)
- [x] Rate Limiting 구현 (`lib/rate-limit.ts`)
- [x] 캐싱 전략 구현 (`lib/cache.ts`)
- [x] 로거 설정 (`lib/logger.ts`)
- [x] 컨테이너 레이아웃 (`components/layout/container.tsx`)
- [x] 푸터 컴포넌트 (`components/layout/footer.tsx`)
- [x] shadcn/ui 공통 컴포넌트 추가 (Button, Card, Table, Skeleton 등)

**완료 기준**: Notion API 연결 확인, 타입 오류 없음, 유틸 함수 정상 동작

---

## Phase 3: 핵심 기능 개발 ✅ (3-4일)

> 📌 **이 순서인 이유**: 공통 모듈이 준비된 후에야 핵심 기능을 구현할 수 있다. 견적서 조회(F001, F002)와 유효성 검증(F011)은 시스템의 존재 이유이며, PDF 생성(F003)보다 먼저 데이터 표시가 정상 동작해야 한다.

### Task 005: 견적서 조회 페이지 (F001, F002)

- [x] 견적서 조회 페이지 (`app/invoice/[id]/page.tsx`)
- [x] Server Component에서 Notion 데이터 페칭
- [x] 견적서 헤더 컴포넌트 (`components/invoice/InvoiceHeader.tsx`)
- [x] 클라이언트 정보 컴포넌트 (`components/invoice/InvoiceClientInfo.tsx`)
- [x] 견적 항목 테이블 (`components/invoice/InvoiceTable.tsx`)
- [x] 금액 요약 컴포넌트 (`components/invoice/InvoiceSummary.tsx`)
- [x] 반응형 레이아웃 (모바일/태블릿/데스크톱)

### Task 006: 에러 및 로딩 상태 (F011)

- [x] 404 에러 페이지 (`app/invoice/[id]/not-found.tsx`)
- [x] 글로벌 404 페이지 (`app/not-found.tsx`)
- [x] 에러 바운더리 (`app/invoice/[id]/error.tsx`)
- [x] 로딩 스켈레톤 (`app/invoice/[id]/loading.tsx`)
- [x] InvoiceSkeleton 컴포넌트 (`components/invoice/InvoiceSkeleton.tsx`)
- [x] 존재하지 않는 견적서 ID 접근 시 적절한 에러 처리

**완료 기준**: 실제 Notion 데이터로 견적서 렌더링 성공, 잘못된 URL 접근 시 404 표시

---

## Phase 4: 추가 기능 개발 ✅ (2-3일)

> 📌 **이 순서인 이유**: 핵심 기능(견적서 조회)이 안정화된 후에야 PDF 생성(F003)과 반응형(F012) 완성에 집중할 수 있다. PDF는 견적서 데이터가 정확히 렌더링되어야 올바르게 생성되므로 Phase 3 이후에 구현한다.

### Task 007: PDF 다운로드 기능 (F003)

- [x] PDF 생성 API Route (`app/api/generate-pdf/route.ts`)
- [x] PDF 템플릿 컴포넌트 (`components/pdf/InvoiceTemplate.tsx`)
- [x] PDF 다운로드 버튼 (`components/invoice/PDFDownloadButton.tsx`)
- [x] 한글 폰트 지원 설정
- [x] 다운로드 트리거 및 파일명 설정

### Task 008: 보안 및 안정성

- [x] Rate Limiting 적용 (PDF API)
- [x] API 키 보안 검증
- [x] CORS 정책 설정
- [x] 상세 에러 로깅 시스템
- [x] 미들웨어 인증 처리 (`middleware.ts`)

**완료 기준**: PDF 다운로드 성공, 한글 정상 표시, 모든 디바이스에서 정상 표시

---

## Phase 5: 최적화 및 배포 ✅ (1-2일)

> 📌 **이 순서인 이유**: 기능이 완성된 이후에 성능 최적화와 배포를 진행한다. 완성되지 않은 기능을 최적화하는 것은 낭비이며, MVP 성공 기준 5가지를 모두 충족한 후 실제 환경에 배포해야 안정적인 서비스를 제공할 수 있다.

### Task 009: 성능 최적화

- [x] Next.js 캐싱 전략 (ISR) 구현
- [x] Notion API 호출 최적화
- [x] React Suspense 경계 설정
- [x] 이미지 및 폰트 최적화

### Task 010: 배포

- [x] Vercel 배포 설정
- [x] 환경 변수 설정 (프로덕션)
- [x] 보안 헤더 설정
- [x] MVP 성공 기준 최종 검증

**완료 기준**: Vercel 배포 성공, 5가지 MVP 성공 기준 모두 충족

---

## MVP 성공 기준 체크리스트

- [x] **F001**: 노션 데이터베이스에서 견적서 정보를 정상적으로 가져옴
- [x] **F002**: 고유 URL로 접근 시 견적서가 웹에서 정확하게 표시됨
- [x] **F003**: PDF 다운로드 버튼 클릭 시 견적서가 PDF로 다운로드됨
- [x] **F011**: 잘못된 URL 접근 시 적절한 에러 메시지 표시
- [x] **F012**: 모바일/태블릿/데스크톱에서 정상 작동

---

**📝 문서 버전**: v1.0
**📅 작성일**: 2026-03-08
**🎯 목표**: MVP 핵심 기능 구현을 통한 빠른 시장 검증
**📊 진행 상황**: 전체 완료 🎊 (Phase 1-5 전체 완성 ✅)
