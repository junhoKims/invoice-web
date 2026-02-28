# Notion CMS 개인 블로그 MVP PRD

## 핵심 정보

**목적**: Notion을 CMS로 활용하여 별도 백엔드 없이 개인 블로그를 운영할 수 있는 정적/동적 하이브리드 웹사이트 구현
**사용자**: Notion으로 콘텐츠를 작성하고 싶은 1인 개발자 및 기술 블로거

---

## 사용자 여정

```
1. [블로그 홈 페이지] - 최신 포스트 목록 표시
   ↓ 태그/카테고리 클릭

2. [태그/카테고리 필터 페이지] - 해당 태그의 포스트만 필터링
   ↓ 포스트 카드 클릭

   [포스트 카드 클릭] → [블로그 포스트 상세 페이지] → [관련 포스트 탐색]
   [홈으로 버튼] → [블로그 홈 페이지]
   ↓

3. [블로그 포스트 상세 페이지] - Notion 블록을 렌더링하여 본문 표시
   ↓ 읽기 완료 후

4. [목차 내비게이션] → [다른 섹션으로 이동]
   [관련 포스트] → [다른 포스트 상세 페이지]
   [태그 클릭] → [태그 필터 페이지]
```

---

## 기능 명세

### 1. MVP 핵심 기능

| ID | 기능명 | 설명 | MVP 필수 이유 | 관련 페이지 |
|----|--------|------|-------------|------------|
| **F001** | Notion DB 포스트 목록 조회 | Notion Database API로 Published 상태의 포스트 목록 가져오기, ISR 적용 | 블로그의 핵심 콘텐츠 피드 | 블로그 홈 페이지, 태그 필터 페이지 |
| **F002** | Notion 블록 렌더링 | Notion blocks API로 포스트 본문을 HTML로 변환하여 표시 (paragraph, heading, code, image, callout, quote, list 등) | 포스트 본문 표시의 핵심 | 블로그 포스트 상세 페이지 |
| **F003** | 태그/카테고리 필터링 | Notion Database의 Multi-select 속성을 기반으로 포스트 필터링 | 콘텐츠 탐색 핵심 기능 | 블로그 홈 페이지, 태그 필터 페이지 |
| **F004** | 포스트 상세 페이지 | 포스트 제목, 커버 이미지, 작성일, 태그, 본문 렌더링 | 블로그 핵심 콘텐츠 표시 | 블로그 포스트 상세 페이지 |
| **F005** | 포스트 목차(TOC) 자동 생성 | H1, H2, H3 블록을 파싱하여 클릭 가능한 목차 생성 | 긴 포스트 탐색 편의성 | 블로그 포스트 상세 페이지 |
| **F006** | SEO 메타태그 최적화 | 각 페이지별 title, description, Open Graph, Twitter Card 메타태그 자동 생성 | 검색엔진 노출 필수 | 블로그 홈 페이지, 블로그 포스트 상세 페이지, 태그 필터 페이지 |
| **F007** | sitemap.xml 자동 생성 | Notion DB의 포스트 목록 기반 sitemap 동적 생성 | 검색엔진 크롤링 지원 | sitemap 페이지 (자동) |
| **F008** | 다크모드 지원 | next-themes 기반 라이트/다크 테마 토글 | 사용자 경험 기본 요소 | 전체 페이지 (공통 헤더) |

### 2. MVP 필수 지원 기능

| ID | 기능명 | 설명 | MVP 필수 이유 | 관련 페이지 |
|----|--------|------|-------------|------------|
| **F009** | ISR 캐시 전략 | revalidate 설정으로 Notion API 호출 최소화, 포스트별 캐시 태그 관리 | Notion API 요청 제한 대응, 빠른 페이지 로딩 | 블로그 홈 페이지, 블로그 포스트 상세 페이지, 태그 필터 페이지 |
| **F010** | 포스트 검색 | 포스트 제목 기반 클라이언트 사이드 검색 | 포스트 수 증가에 따른 탐색 지원 | 블로그 홈 페이지 |
| **F011** | robots.txt 생성 | 검색엔진 크롤러 접근 제어 파일 자동 생성 | SEO 기본 설정 | robots 페이지 (자동) |
| **F012** | 읽기 예상 시간 표시 | 본문 텍스트 분량 기반 읽기 소요 시간 계산 표시 | 독자 경험 향상 | 블로그 홈 페이지, 블로그 포스트 상세 페이지 |
| **F013** | 반응형 레이아웃 | 모바일/태블릿/데스크탑 대응 레이아웃 | 다양한 기기 지원 필수 | 전체 페이지 (공통) |

### 3. MVP 이후 기능 (제외)

- 댓글 시스템 (Giscus, Utterances 등)
- 뉴스레터 구독 기능
- 포스트 조회수 추적
- RSS 피드
- 다국어 지원 (i18n)
- 포스트 시리즈 기능

---

## 메뉴 구조

```
블로그 내비게이션
├── 로고/블로그명 (홈으로 이동)
│   └── 기능: F001 (포스트 목록 조회)
├── 검색
│   └── 기능: F010 (포스트 제목 검색)
└── 테마 토글
    └── 기능: F008 (다크모드 전환)

메인 콘텐츠 영역
├── 블로그 홈 페이지
│   └── 기능: F001, F003, F006, F010, F012, F013
├── 태그 필터 페이지
│   └── 기능: F001, F003, F006, F013
└── 블로그 포스트 상세 페이지
    └── 기능: F002, F004, F005, F006, F012, F013

자동 생성 페이지
├── sitemap.xml
│   └── 기능: F007
└── robots.txt
    └── 기능: F011
```

---

## 페이지별 상세 기능

### 블로그 홈 페이지

> **구현 기능:** `F001`, `F003`, `F006`, `F010`, `F012`, `F013` | **렌더링:** ISR (revalidate: 3600)

| 항목 | 내용 |
|------|------|
| **역할** | 블로그 랜딩 페이지. 모든 Published 포스트 목록을 카드 형태로 표시하고 태그 필터 및 검색 제공 |
| **진입 경로** | 도메인 루트 접속, 헤더 로고 클릭, 브라우저 뒤로가기 |
| **사용자 행동** | 포스트 카드 탐색, 태그 클릭으로 필터링, 검색어 입력, 포스트 클릭으로 상세 이동 |
| **주요 기능** | - Notion DB에서 Published=true 포스트 목록 조회 (ISR)<br>- 포스트 카드: 제목, 커버 이미지, 작성일, 태그 배지, 요약, 읽기 예상 시간 표시<br>- 전체 태그 목록 사이드바 표시 (활성 태그 하이라이트)<br>- 포스트 제목 기반 클라이언트 검색 (Input 입력 시 실시간 필터)<br>- 페이지네이션 (포스트 9개 단위)<br>- **포스트 클릭** 시 상세 페이지 이동 |
| **다음 이동** | 포스트 카드 클릭 → 블로그 포스트 상세 페이지, 태그 클릭 → 태그 필터 페이지 |

---

### 태그 필터 페이지

> **구현 기능:** `F001`, `F003`, `F006`, `F013` | **렌더링:** ISR (revalidate: 3600)

| 항목 | 내용 |
|------|------|
| **역할** | 특정 태그 또는 카테고리에 해당하는 포스트만 필터링하여 표시 |
| **진입 경로** | 블로그 홈 페이지의 태그 클릭, 포스트 상세 페이지의 태그 배지 클릭 |
| **사용자 행동** | 해당 태그 포스트 목록 탐색, 포스트 클릭, 다른 태그 선택으로 전환 |
| **주요 기능** | - Notion DB filter API로 특정 태그 포스트만 조회<br>- 현재 선택된 태그 이름 헤더에 표시<br>- 태그별 포스트 수 표시<br>- 포스트 카드 그리드 표시 (홈과 동일한 카드 컴포넌트 재사용)<br>- 사이드바에 전체 태그 목록 표시<br>- **포스트 카드 클릭** 시 상세 이동 |
| **다음 이동** | 포스트 클릭 → 블로그 포스트 상세 페이지, 다른 태그 클릭 → 해당 태그 필터 페이지, 홈 로고 클릭 → 블로그 홈 페이지 |

---

### 블로그 포스트 상세 페이지

> **구현 기능:** `F002`, `F004`, `F005`, `F006`, `F012`, `F013` | **렌더링:** ISR (revalidate: 3600)

| 항목 | 내용 |
|------|------|
| **역할** | 단일 블로그 포스트의 전체 본문을 Notion 블록 기반으로 렌더링하여 표시 |
| **진입 경로** | 블로그 홈 페이지 포스트 카드 클릭, 태그 필터 페이지 포스트 카드 클릭 |
| **사용자 행동** | 본문 읽기, 목차로 섹션 이동, 코드 블록 복사, 태그 클릭으로 관련 포스트 탐색 |
| **주요 기능** | - 포스트 커버 이미지, 제목, 작성일, 태그 배지, 읽기 예상 시간 헤더에 표시<br>- Notion blocks 렌더링: paragraph, heading_1/2/3, code(신택스 하이라이트), image, callout, quote, bulleted_list, numbered_list, toggle, divider, table<br>- 우측 사이드바 목차(TOC): H2/H3 기반 자동 생성, 현재 섹션 스크롤 하이라이트<br>- 코드 블록 언어 표시 + 복사 버튼<br>- 포스트 하단 태그 목록<br>- 이전/다음 포스트 내비게이션<br>- **태그 클릭** 시 태그 필터 페이지 이동 |
| **다음 이동** | 태그 클릭 → 태그 필터 페이지, 이전/다음 포스트 → 해당 포스트 상세 페이지, 헤더 로고 → 블로그 홈 페이지 |

---

### sitemap.xml 페이지 (자동)

> **구현 기능:** `F007` | **렌더링:** 동적 생성

| 항목 | 내용 |
|------|------|
| **역할** | 검색엔진 크롤러를 위한 사이트맵 자동 생성 |
| **진입 경로** | 검색엔진 크롤러 자동 접근, 직접 URL 접속 |
| **사용자 행동** | 검색엔진 자동 소비 (일반 사용자 직접 접근 없음) |
| **주요 기능** | - Next.js MetadataRoute.Sitemap 활용<br>- Notion DB 전체 포스트 URL 포함<br>- 정적 페이지(홈, 태그별) URL 포함<br>- lastModified 날짜 포함 |
| **다음 이동** | 없음 (검색엔진 소비용) |

---

## Notion Database 스키마 정의

Notion에서 아래 속성으로 블로그 Database를 생성해야 합니다.

| 속성명 | Notion 타입 | 설명 |
|--------|------------|------|
| Title | Title | 포스트 제목 |
| Slug | Rich Text | URL용 슬러그 (영문, 하이픈) |
| Status | Select | 발행 상태 (Draft / Published) |
| PublishedAt | Date | 발행일 |
| Tags | Multi-select | 태그 목록 |
| Category | Select | 카테고리 (선택적) |
| Summary | Rich Text | 포스트 요약 (카드에 표시) |
| Cover | Files & Media | 커버 이미지 |

---

## 데이터 모델

### NotionPost (Notion DB 응답 매핑)

| 필드 | 설명 | 타입/관계 |
|------|------|----------|
| id | Notion 페이지 ID | string (UUID) |
| slug | URL 슬러그 | string |
| title | 포스트 제목 | string |
| summary | 포스트 요약 | string |
| status | 발행 상태 | "Draft" \| "Published" |
| publishedAt | 발행일 | Date \| null |
| tags | 태그 목록 | string[] |
| category | 카테고리 | string \| null |
| coverImageUrl | 커버 이미지 URL | string \| null |
| readingTime | 읽기 예상 시간(분) | number |

### NotionBlock (Notion blocks API 응답 매핑)

| 필드 | 설명 | 타입/관계 |
|------|------|----------|
| id | 블록 ID | string (UUID) |
| type | 블록 유형 | BlockType (paragraph \| heading_1 \| heading_2 \| heading_3 \| code \| image \| callout \| quote \| bulleted_list_item \| numbered_list_item \| toggle \| divider \| table) |
| content | 블록 내용 | RichText[] |
| children | 중첩 블록 | NotionBlock[] \| null |
| language | 코드 언어 (code 블록) | string \| null |
| url | 이미지 URL (image 블록) | string \| null |

### TocItem (목차 아이템)

| 필드 | 설명 | 타입/관계 |
|------|------|----------|
| id | 앵커 ID | string |
| text | 헤딩 텍스트 | string |
| level | 헤딩 레벨 | 1 \| 2 \| 3 |
| children | 하위 목차 | TocItem[] |

---

## 프로젝트 파일 구조

```
src/
├── app/
│   ├── layout.tsx                    # 루트 레이아웃 (ThemeProvider, 헤더, 푸터)
│   ├── page.tsx                      # 블로그 홈 페이지 (F001, F003, F006, F010, F012, F013)
│   ├── sitemap.ts                    # sitemap.xml 자동 생성 (F007)
│   ├── robots.ts                     # robots.txt 자동 생성 (F011)
│   └── posts/
│       └── [slug]/
│           ├── page.tsx              # 블로그 포스트 상세 페이지 (F002, F004, F005, F006, F012, F013)
│           └── loading.tsx           # 스켈레톤 로딩 UI
│   └── tags/
│       └── [tag]/
│           └── page.tsx              # 태그 필터 페이지 (F001, F003, F006, F013)
│
├── components/
│   ├── layout/
│   │   ├── header.tsx                # 헤더 (로고, 검색, 테마 토글)
│   │   └── footer.tsx                # 푸터
│   ├── blog/
│   │   ├── post-card.tsx             # 포스트 카드 컴포넌트
│   │   ├── post-grid.tsx             # 포스트 그리드 레이아웃
│   │   ├── post-header.tsx           # 포스트 상세 헤더 (제목, 날짜, 태그)
│   │   ├── post-body.tsx             # Notion 블록 렌더러
│   │   ├── tag-badge.tsx             # 태그 배지 컴포넌트
│   │   ├── tag-sidebar.tsx           # 태그 목록 사이드바
│   │   ├── toc.tsx                   # 목차(TOC) 컴포넌트 (F005)
│   │   ├── post-pagination.tsx       # 이전/다음 포스트 내비게이션
│   │   └── reading-time.tsx          # 읽기 시간 표시 (F012)
│   ├── notion/
│   │   ├── notion-block.tsx          # 블록 타입별 렌더러 (F002)
│   │   ├── notion-code.tsx           # 코드 블록 (신택스 하이라이트 + 복사)
│   │   ├── notion-image.tsx          # 이미지 블록 (next/image 최적화)
│   │   └── notion-callout.tsx        # 콜아웃 블록
│   └── ui/                           # shadcn/ui 컴포넌트
│
└── lib/
    ├── notion/
    │   ├── client.ts                 # Notion Client 초기화
    │   ├── posts.ts                  # 포스트 목록/상세 조회 함수 (F001, F004)
    │   ├── blocks.ts                 # 블록 조회 및 변환 함수 (F002)
    │   └── types.ts                  # Notion 관련 TypeScript 타입
    ├── utils/
    │   ├── reading-time.ts           # 읽기 시간 계산 (F012)
    │   ├── toc.ts                    # 목차 생성 유틸 (F005)
    │   └── slug.ts                   # 슬러그 처리 유틸
    └── constants.ts                  # 환경변수, 상수 정의
```

---

## 기술 스택

### 프론트엔드 프레임워크

- **Next.js 15.5.3** (App Router + Turbopack) - ISR, SSG, 동적 라우팅
- **TypeScript 5** - 타입 안전성 (Notion API 응답 타입 정의)
- **React 19.1.0** - Server Components 우선 설계

### 스타일링 & UI

- **TailwindCSS v4** (설정파일 없는 새로운 CSS 엔진) - 유틸리티 CSS
- **shadcn/ui** (new-york 스타일) - Badge, Card, Skeleton, Separator, ScrollArea 등
- **Lucide React** - 아이콘 (검색, 달력, 시계, 태그 등)
- **next-themes** - 다크모드 지원 (F008)

### Notion 연동

- **@notionhq/client** - 공식 Notion JavaScript SDK
  - `notion.databases.query()` - 포스트 목록 조회 (F001)
  - `notion.blocks.children.list()` - 포스트 본문 블록 조회 (F002)
  - `notion.pages.retrieve()` - 포스트 메타데이터 조회 (F004)
- **notion-to-md** (선택적) - Notion 블록을 Markdown으로 변환 보조

### 코드 하이라이팅

- **shiki** - 서버 사이드 신택스 하이라이터 (Next.js Server Components와 최적 호환)

### 캐시 & 성능

- **Next.js ISR** - `revalidate: 3600` (1시간) 기본 설정 (F009)
- **next/image** - 이미지 최적화 (Notion 이미지 URL 도메인 허용 설정 필요)
- **unstable_cache** - 함수 레벨 캐싱 (Notion API 응답 캐시)

### SEO

- **Next.js Metadata API** - `generateMetadata()` 함수로 동적 메타태그 생성 (F006)
- **Next.js sitemap.ts** - MetadataRoute.Sitemap 타입 활용 (F007)
- **Next.js robots.ts** - MetadataRoute.Robots 타입 활용 (F011)

### 개발 도구

- **ESLint** + **Prettier** + **Husky** - 코드 품질 관리
- **npm** - 패키지 관리

---

## 환경변수 설정

```bash
# .env.local
NOTION_API_KEY=secret_xxxxxxxxxxxxxxxxxxxx
NOTION_DATABASE_ID=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# 선택적
NEXT_PUBLIC_SITE_URL=https://yourdomain.com
NEXT_PUBLIC_SITE_NAME=내 블로그
NEXT_PUBLIC_SITE_DESCRIPTION=개인 기술 블로그
```

---

## Notion API 핵심 구현 패턴

### 포스트 목록 조회 (F001)

```typescript
// src/lib/notion/posts.ts
import { Client } from '@notionhq/client'
import { unstable_cache } from 'next/cache'

const notion = new Client({ auth: process.env.NOTION_API_KEY })

export const getPosts = unstable_cache(
  async (tag?: string) => {
    // [수정] 태그 필터 적용 시에도 Status: Published 조건을 항상 포함 (and 복합 필터)
    const filter = {
      and: [
        { property: 'Status', select: { equals: 'Published' } },
        ...(tag ? [{ property: 'Tags', multi_select: { contains: tag } }] : []),
      ],
    }

    const response = await notion.databases.query({
      database_id: process.env.NOTION_DATABASE_ID!,
      filter,
      sorts: [{ property: 'PublishedAt', direction: 'descending' }],
    })

    return response.results.map(mapPageToPost)
  },
  ['posts'],
  { revalidate: 3600, tags: ['posts'] }
)
```

### 포스트 블록 조회 (F002)

```typescript
// src/lib/notion/blocks.ts
export const getPostBlocks = unstable_cache(
  async (pageId: string) => {
    const blocks = []
    let cursor: string | undefined

    do {
      const response = await notion.blocks.children.list({
        block_id: pageId,
        start_cursor: cursor,
        page_size: 100,
      })
      blocks.push(...response.results)
      cursor = response.next_cursor ?? undefined
    } while (cursor)

    return blocks
  },
  ['blocks'],
  { revalidate: 3600, tags: ['blocks'] }
)
```

### ISR 설정 (F009) - 포스트 상세 페이지

```typescript
// src/app/posts/[slug]/page.tsx
import { notFound } from 'next/navigation'

export const revalidate = 3600 // 1시간 ISR

export async function generateStaticParams() {
  const posts = await getPosts()
  return posts.map((post) => ({ slug: post.slug }))
}

export async function generateMetadata({
  params,
}: {
  params: Promise<{ slug: string }>
}) {
  const { slug } = await params
  const post = await getPostBySlug(slug)
  if (!post) return {}

  return {
    title: post.title,
    description: post.summary,
    openGraph: {
      title: post.title,
      description: post.summary,
      images: post.coverImageUrl ? [post.coverImageUrl] : [],
    },
  }
}
```

### Notion 이미지 URL 만료 대응 전략 (Critical)

> Notion의 파일/이미지 URL은 AWS S3 Signed URL로 **1시간 후 만료**됩니다. ISR 캐시와 구조적으로 충돌하므로 아래 전략을 적용합니다.

**선택 전략: API Route 프록시 (MVP 기본 전략)**

- 커버 이미지: Notion `Files & Media` 속성에 **외부 CDN URL**(직접 업로드)을 저장하여 영구 URL 보장
- 본문 내부 이미지: Next.js API Route를 프록시로 사용하여 Notion 블록 ID 기반으로 실시간 조회 후 반환

```typescript
// src/app/api/notion-image/route.ts
import { NextRequest, NextResponse } from 'next/server'

export async function GET(request: NextRequest) {
  const blockId = request.nextUrl.searchParams.get('blockId')
  if (!blockId) return NextResponse.json({ error: 'blockId required' }, { status: 400 })

  // Notion blocks API로 최신 이미지 URL을 실시간 조회하여 리다이렉트
  const block = await notion.blocks.retrieve({ block_id: blockId })
  const imageUrl =
    block.type === 'image'
      ? block.image.type === 'external'
        ? block.image.external.url
        : block.image.file.url // S3 Signed URL - 프록시로 반환
      : null

  if (!imageUrl) return NextResponse.json({ error: 'Image not found' }, { status: 404 })

  const imageResponse = await fetch(imageUrl)
  return new NextResponse(imageResponse.body, {
    headers: {
      'Content-Type': imageResponse.headers.get('Content-Type') ?? 'image/png',
      'Cache-Control': 'public, max-age=3600, s-maxage=3600',
    },
  })
}
```

```typescript
// src/components/notion/notion-image.tsx - 본문 이미지 렌더링
// block.type === 'image' 인 경우 /api/notion-image?blockId={block.id} 로 src 지정
<Image src={`/api/notion-image?blockId=${block.id}`} alt={caption} ... />
```

> **커버 이미지 운영 방침**: Notion DB의 `Cover` 속성에는 Notion 내부 업로드 대신 외부 이미지 URL(Cloudinary, S3 등)을 직접 입력하세요.

---

### next.config.ts 설정

```typescript
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'www.notion.so',
      },
      {
        protocol: 'https',
        hostname: 'prod-files-secure.s3.us-west-2.amazonaws.com',
      },
      {
        protocol: 'https',
        hostname: '*.amazonaws.com',
      },
    ],
  },
  experimental: {
    optimizePackageImports: ['lucide-react', '@notionhq/client'],
  },
}

export default nextConfig
```

---

## shadcn/ui 컴포넌트 설치 목록

```bash
npx shadcn@latest add badge        # 태그 배지
npx shadcn@latest add card         # 포스트 카드
npx shadcn@latest add skeleton     # 로딩 스켈레톤
npx shadcn@latest add separator    # 구분선
npx shadcn@latest add scroll-area  # 목차 스크롤
npx shadcn@latest add input        # 검색 입력
npx shadcn@latest add button       # 버튼
```

---

## 신규 패키지 설치

```bash
# Notion 공식 SDK
npm install @notionhq/client

# 서버 사이드 신택스 하이라이터
npm install shiki
```

---

## 정합성 검증 체크리스트

### 1단계: 기능 명세 → 페이지 연결 검증

- [x] F001 → 블로그 홈 페이지, 태그 필터 페이지에서 구현
- [x] F002 → 블로그 포스트 상세 페이지에서 구현
- [x] F003 → 블로그 홈 페이지, 태그 필터 페이지에서 구현
- [x] F004 → 블로그 포스트 상세 페이지에서 구현
- [x] F005 → 블로그 포스트 상세 페이지에서 구현
- [x] F006 → 블로그 홈 페이지, 블로그 포스트 상세 페이지, 태그 필터 페이지에서 구현
- [x] F007 → sitemap 페이지에서 구현
- [x] F008 → 전체 페이지 공통 헤더에서 구현
- [x] F009 → 블로그 홈, 포스트 상세, 태그 필터 페이지에서 구현
- [x] F010 → 블로그 홈 페이지에서 구현
- [x] F011 → robots 페이지에서 구현
- [x] F012 → 블로그 홈 페이지, 블로그 포스트 상세 페이지에서 구현
- [x] F013 → 전체 페이지 공통 레이아웃에서 구현

### 2단계: 메뉴 구조 → 페이지 연결 검증

- [x] 블로그 홈 페이지 → 페이지별 상세 기능에 존재
- [x] 태그 필터 페이지 → 페이지별 상세 기능에 존재
- [x] 블로그 포스트 상세 페이지 → 페이지별 상세 기능에 존재
- [x] sitemap.xml → 페이지별 상세 기능에 존재

### 3단계: 누락 항목 검증

- [x] 기능 명세에만 있고 구현 페이지 없는 기능: 없음
- [x] 페이지에만 있고 기능 명세 미정의 기능: 없음
- [x] 메뉴에만 있고 실제 페이지 없는 항목: 없음
