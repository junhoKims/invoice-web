# Notion Invoice Management System - AI Agent 개발 규칙

> **프로젝트**: 노션 기반 견적서 관리 시스템 (Notion CMS Blog)
> **목적**: 노션을 DB로 활용, 견적서 관리 및 클라이언트 웹 조회/PDF 다운로드
> **버전**: v2.0 | 업데이트: 2026-03-08

---

## 1. 프로젝트 아키텍처

### 기술 스택

- **Framework**: Next.js 15.5.3 (App Router + Turbopack)
- **Runtime**: React 19.1.0 + TypeScript 5 (strict mode)
- **Styling**: TailwindCSS v4 + shadcn/ui (new-york style)
- **Forms**: React Hook Form + Zod + Server Actions
- **Auth**: jose (JWT) + middleware
- **External**: @notionhq/client, @react-pdf/renderer
- **Utils**: date-fns, lucide-react, sonner, next-themes

### 디렉토리 구조

```
src/
├── app/
│   ├── admin/                        # 관리자 전용 (인증 필요)
│   │   ├── invoices/                 # 견적서 목록 (error.tsx, loading.tsx, page.tsx)
│   │   ├── actions.ts                # 관리자 Server Actions
│   │   ├── layout.tsx                # 관리자 레이아웃
│   │   └── page.tsx                  # 관리자 대시보드
│   ├── (auth)/admin-login/           # 인증 라우트 그룹
│   │   ├── actions.ts                # 로그인 Server Actions
│   │   └── page.tsx
│   ├── invoice/[id]/                 # 공개 견적서 조회 (인증 불필요)
│   │   ├── error.tsx, loading.tsx, not-found.tsx, page.tsx
│   ├── invoice/guide/                # 견적서 가이드 페이지
│   ├── api/generate-pdf/route.ts     # PDF 생성 API Route
│   ├── layout.tsx                    # 루트 레이아웃
│   ├── page.tsx                      # 루트 페이지
│   ├── not-found.tsx
│   └── globals.css
├── components/
│   ├── ui/                           # shadcn/ui 자동 생성 (직접 수정 금지)
│   ├── admin/                        # 관리자 전용 컴포넌트
│   ├── invoice/                      # 견적서 공개 조회 컴포넌트
│   │   └── index.ts                  # barrel export
│   ├── pdf/InvoiceTemplate.tsx       # PDF 렌더링 템플릿
│   ├── layout/                       # 공통 레이아웃 (container.tsx, footer.tsx)
│   ├── providers/theme-provider.tsx
│   └── theme-toggle.tsx
├── lib/
│   ├── notion.ts                     # Notion API 클라이언트 (단일 진입점)
│   ├── services/invoice.service.ts   # 견적서 비즈니스 로직
│   ├── auth/
│   │   ├── session.ts                # JWT 세션 관리
│   │   └── password.ts              # 비밀번호 해싱
│   ├── utils/
│   │   ├── notion-parser.ts          # Notion 응답 파싱
│   │   └── link-generator.ts        # 공유 링크 생성
│   ├── env.ts                        # 환경변수 유일한 진입점
│   ├── cache.ts                      # 캐싱 로직
│   ├── constants.ts                  # 앱 상수
│   ├── format.ts                     # 포매팅 유틸리티
│   ├── logger.ts                     # 로깅
│   ├── mock-data.ts                  # 목 데이터
│   ├── rate-limit.ts                 # Rate limiting
│   └── utils.ts                      # cn() 헬퍼
├── types/
│   ├── invoice.ts                    # 견적서 관련 타입
│   ├── notion.ts                     # Notion API 응답 타입
│   ├── pdf.ts                        # PDF 관련 타입
│   └── auth.ts                       # 인증 관련 타입
├── hooks/use-clipboard.ts
└── middleware.ts                     # 인증 미들웨어 (admin/* 경로 보호)
```

### 데이터 흐름

```
Notion DB
  → lib/notion.ts (API 클라이언트)
  → lib/utils/notion-parser.ts (응답 파싱)
  → lib/services/invoice.service.ts (비즈니스 로직)
  → Server Components (데이터 패칭)
  → Client Components (UI 렌더링)
```

---

## 2. 필수 규칙

### 환경변수

- **`src/lib/env.ts`를 통해서만 접근** — `process.env.XXX` 직접 접근 금지 (env.ts 외부)
- 새 환경변수 추가 시 `.env.example`도 동시 업데이트

### TypeScript

- `any` 타입 사용 금지 (strict mode 유지)
- 새 타입은 반드시 `src/types/` 내 해당 파일에 추가
  - 견적서 관련 → `src/types/invoice.ts`
  - Notion API 응답 → `src/types/notion.ts`
  - PDF 관련 → `src/types/pdf.ts`
  - 인증 관련 → `src/types/auth.ts`
- 상대 경로 import 금지 → `@/` 별칭 필수

```typescript
// ✅
import { cn } from '@/lib/utils'
// ❌
import { cn } from '../../../lib/utils'
```

### 상수 및 매직값

- 매직 넘버/문자열 → `src/lib/constants.ts`에 정의
- 컴포넌트 내 하드코딩 금지

### 파일 네이밍

- 컴포넌트 파일: PascalCase (`InvoiceTable.tsx`)
- 일반 파일/폴더: kebab-case (`invoice-service.ts`, `admin-login/`)
- shadcn/ui 컴포넌트: kebab-case (`button.tsx`, `card.tsx`)

---

## 3. Notion API 연동

- Notion API 직접 호출 → **`src/lib/notion.ts`에서만**
- 비즈니스 로직 → **`src/lib/services/invoice.service.ts`**
- Notion 응답 파싱 → **`src/lib/utils/notion-parser.ts`**

**새 Notion 쿼리 추가 순서:**

1. `src/lib/notion.ts` — 클라이언트 메서드 추가
2. `src/lib/utils/notion-parser.ts` — 응답 파싱 로직 추가
3. `src/lib/services/invoice.service.ts` — 비즈니스 로직 구현
4. Server Component에서 서비스 호출

**Notion API 에러 처리:**

- 모든 호출에 try-catch 필수
- 404 → `notFound()` 호출
- 500 → `error.tsx` 처리

---

## 4. Server Actions

- **Server Actions 위치**: 해당 라우트 폴더의 `actions.ts`
  - 관리자 액션 → `src/app/admin/actions.ts`
  - 로그인 액션 → `src/app/(auth)/admin-login/actions.ts`
- 파일 상단 `'use server'` 디렉티브 필수
- Zod 스키마로 서버 측 재검증 필수

---

## 5. 인증

- 인증 보호 → **`src/middleware.ts`** (admin/\* 경로)
- 세션 로직 → **`src/lib/auth/session.ts`에만**
- 비밀번호 해싱 → **`src/lib/auth/password.ts`에만**
- 클라이언트 컴포넌트에서 인증 상태 직접 확인 금지

---

## 6. PDF 생성

- PDF 템플릿 → `src/components/pdf/InvoiceTemplate.tsx`
- PDF 생성 API → `src/app/api/generate-pdf/route.ts`
- `@react-pdf/renderer` → **서버 사이드에서만** (클라이언트 번들 포함 금지)

---

## 7. Server/Client Component 구분

- 기본값: **Server Component (RSC)**
- `'use client'` 추가 조건: 이벤트 핸들러, useState, useEffect, 브라우저 API
- `'use client'` 경계는 가능한 하위 컴포넌트로 밀어내기

```typescript
// ✅ async params 접근 (Next.js 15 필수)
export default async function Page({
  params,
}: {
  params: Promise<{ id: string }>
}) {
  const { id } = await params
}

// ❌ 동기 params 접근 금지
export default function Page({ params }: { params: { id: string } }) {
  const data = getData(params.id) // 에러
}
```

---

## 8. 컴포넌트 규칙

- `src/components/ui/` — **직접 수정 금지** (shadcn 자동 생성)
- 새 shadcn 컴포넌트 추가: `npx shadcn@latest add <component-name>`
- `cn()` 유틸리티 사용: `import { cn } from '@/lib/utils'`

**컴포넌트 배치:**

- 관리자 전용 → `src/components/admin/`
- 견적서 공개 → `src/components/invoice/`
- PDF 전용 → `src/components/pdf/`
- 공통 레이아웃 → `src/components/layout/`
- 범용 공통 → `src/components/` (루트)

**Props 타입 정의 필수:**

```typescript
// ✅
interface InvoiceCardProps {
  invoice: Invoice
  className?: string
}
export function InvoiceCard({ invoice, className }: InvoiceCardProps) { ... }

// ❌
export function InvoiceCard(props) { ... }
```

---

## 9. 스타일링

- Tailwind CSS v4 유틸리티 클래스 우선, 인라인 `style={{}}` 금지
- 시맨틱 색상 변수 사용 (`bg-background`, `text-foreground`, `text-muted-foreground`)
- 하드코딩 색상 금지 (`bg-white`, `text-black`)
- 커스텀 CSS → `src/app/globals.css`에만
- 클래스 정렬 → `prettier-plugin-tailwindcss` 자동 처리 (수동 정렬 금지)

---

## 10. 캐싱

- Notion API 응답 캐싱 → `src/lib/cache.ts` 활용
- Next.js `fetch` 캐싱 또는 `unstable_cache` 사용 시 캐시 태그 명시

---

## 11. 다중 파일 동시 수정 규칙

| 작업                 | 수정 필요 파일                                                                                                            |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| 새 Notion 필드 추가  | `src/types/notion.ts` + `src/types/invoice.ts` + `src/lib/utils/notion-parser.ts` + `src/lib/services/invoice.service.ts` |
| 새 환경변수 추가     | `src/lib/env.ts` + `.env.example`                                                                                         |
| 새 상수 추가         | `src/lib/constants.ts`                                                                                                    |
| 새 admin 페이지 추가 | `src/app/admin/<path>/page.tsx` + `src/middleware.ts` (경로 확인)                                                         |
| PDF 레이아웃 변경    | `src/components/pdf/InvoiceTemplate.tsx` + `src/types/pdf.ts` (타입 변경 시)                                              |
| 인증 로직 변경       | `src/middleware.ts` + `src/lib/auth/session.ts`                                                                           |
| 새 폼 추가           | Zod 스키마 (types/) + Server Actions (actions.ts) + 폼 컴포넌트 ('use client')                                            |

---

## 12. AI 의사결정 기준

### 새 기능 배치 결정 트리

```
새 기능?
├── Notion 데이터 관련
│   ├── API 호출 → src/lib/notion.ts
│   ├── 파싱 → src/lib/utils/notion-parser.ts
│   └── 비즈니스 로직 → src/lib/services/invoice.service.ts
├── UI 컴포넌트
│   ├── 관리자 전용 → src/components/admin/
│   ├── 견적서 공개 → src/components/invoice/
│   └── 공통 → src/components/
├── 페이지
│   ├── 관리자 (인증 필요) → src/app/admin/
│   └── 공개 → src/app/invoice/ 또는 src/app/
└── Server Actions
    └── 해당 라우트 폴더의 actions.ts
```

### 유틸리티 함수 위치

- 특정 도메인 → `src/lib/utils/<domain>.ts`
- 범용 → `src/lib/utils.ts`

---

## 13. 코드 품질 (작업 완료 후 필수)

```bash
npm run check-all   # typecheck + lint + format:check 통합
npm run build       # 프로덕션 빌드 검증
```

---

## 14. 금지 사항

- `src/components/ui/` 직접 수정 금지
- `process.env` 직접 접근 금지 (`src/lib/env.ts` 경유 필수)
- `any` 타입 사용 금지
- 클라이언트 컴포넌트에서 Notion API 직접 호출 금지
- `eslint-disable` 주석 무분별 사용 금지
- 환경변수 코드에 하드코딩 금지
- `@react-pdf/renderer` 클라이언트 번들 포함 금지
- Pages Router 사용 금지 (App Router만)
- `getServerSideProps`, `getStaticProps` 사용 금지
- params/searchParams 동기 접근 금지 (반드시 `await`)
- 인라인 스타일 (`style={{}}`) 사용 금지
- 하드코딩 색상 클래스 금지 (`bg-white`, `text-black`)
