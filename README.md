# Next.js + AI 기반 코드 에디터 만들어보기

## Part 1: 기초 다지기 (3-4시간)

### 1.1 React 19 핵심 변경사항

- use() hook 실전 활용
- Server Components 아키텍처 설계
- Server Actions로 mutation 처리
- forwardRef 제거, ref as prop 마이그레이션
- Suspense 중첩 전략, Error Boundary 패턴
- **실습**: Server Actions + use()로 비동기 form 구현

### 1.2 Next.js 15-16 주요 기능

- Partial Prerendering (PPR) 적용 시나리오
- after() API - 로깅, 분석 처리
- Turbopack 마이그레이션 및 성능 비교
- fetch caching 전략 변경 대응 (no-store 기본값)
- parallel routes로 모달 구현
- intercepting routes 실전 활용
- **실습**: PPR + parallel routes로 에디터 레이아웃

## Part 2: 프로젝트 세팅 (2-3시간)

### 2.1 Turborepo 모노레포 구축
```
/apps
  /web          - Next.js 메인 앱
/packages
  /editor       - Monaco Editor 래퍼
  /ai           - AI Provider 추상화 레이어
  /ui           - 공통 컴포넌트
  /config       - 공유 설정 (tsconfig, biome)
```
- 패키지 간 의존성 관리 (internal packages)
- tsconfig extends 전략
- turbo.json pipeline 최적화
- **실습**: 모노레포 초기 구조 생성

### 2.2 개발 도구 세팅

- Biome으로 통합 (lint + format)
- TypeScript 5.7+ strict 설정
- Vitest + @testing-library/react 환경
- GitHub Actions CI 파이프라인
- **실습**: Biome 규칙 커스터마이징 with LLM

## Part 3: Monaco Editor 통합 (3-4시간)

### 3.1 기본 에디터 구현
- @monaco-editor/react 설치
- Next.js dynamic import 패턴
```tsx
const Editor = dynamic(() => import('@monaco-editor/react'), { 
  ssr: false 
})
```
- theme 설정 (vs-dark, vs-light)
- 언어별 설정 (typescript, javascript, css)
- **실습**: 기본 에디터 + theme 토글

### 3.2 에디터 UX 개선

- 탭 시스템 (zustand로 상태 관리)
- 파일 트리 (virtual file system with Map)
- 단축키: Cmd+P (파일 검색), Cmd+Shift+P (Command Palette)
- minimap, breadcrumbs 설정
- **실습**: 멀티 파일 에디터 완성

### 3.3 TypeScript IntelliSense

- Monaco TypeScript worker 설정
- @types 패키지 동적 로딩
```typescript
monaco.languages.typescript.typescriptDefaults.addExtraLib(
  reactTypes, 'file:///node_modules/@types/react/index.d.ts'
)
```
- diagnostics, hover, autocomplete
- **실습**: React 타입 지원 + 에러 표시

## Part 4: AI 통합 (4-5시간)

### 4.1 AI Provider 아키텍처

- Claude Sonnet 4.5 메인 통합
- OpenAI 비교 연동 (선택 사항)
- Provider 추상화 인터페이스
```typescript
interface AIProvider {
  complete(prompt: string): AsyncIterable<string>
  chat(messages: Message[]): AsyncIterable<string>
}
```
- Rate limiting, error handling
- **실습**: Provider 전환 가능한 구조

### 4.2 AI 코드 자동완성 (Claude)

- Claude API streaming 연동
- Monaco inline suggestions API
```typescript
monaco.languages.registerInlineCompletionsProvider('typescript', {
  provideInlineCompletions: async (model, position) => {
    // Claude API 호출
  }
})
```
- debounce (300ms), 컨텍스트 최적화
- Copilot 스타일 UI (Tab to accept)
- **실습**: 실시간 자동완성 구현

### 4.3 AI 코드 리뷰 (Claude)

- 선택한 코드 분석
- Monaco decorations로 인라인 피드백
- 심각도별 표시 (error, warning, info)
- 성능, 보안, 타입 안정성 체크
- **실습**: 실시간 코드 품질 검사

### 4.4 AI 채팅 어시스턴트

- 사이드바 채팅 UI
- 선택 영역 컨텍스트 자동 포함
- streaming response (readableStream)
- 명령어: /explain, /refactor, /test, /fix
- **실습**: 코드 어시스턴트 챗봇

### 4.5 AI 코드 생성

- 자연어 → 코드 변환
- 프롬프트 템플릿 최적화
```typescript
const prompt = `Generate a React component:
- Name: ${name}
- Props: ${props}
- Use TypeScript and Tailwind CSS`
```
- 생성된 코드 파싱 및 검증
- **실습**: 컴포넌트 생성기 AI로 만들어보기

## Part 5: 실행 및 협업 (3-4시간)

### 5.1 Sandpack 통합
- @codesandbox/sandpack-react 설치
- Monaco ↔ Sandpack 양방향 동기화
```tsx
<Sandpack
  template="react-ts"
  files={editorFiles}
  options={{ showConsole: true }}
/>
```
- AI 생성 코드 즉시 실행
- 에러 핸들링 및 콘솔 출력
- **실습**: AI가 만든 컴포넌트 실시간 프리뷰

### 5.2 파일 저장 및 공유

- IndexedDB (Dexie.js) 로컬 저장 
- GitHub Gist API 연동 
- Export as zip (JSZip) 
- **공유 링크 생성** (Upstash Redis)
  - 코드를 Redis에 저장 (짧은 ID)
  - `/share/[id]` 동적 라우팅
  - 7일 자동 만료 (TTL)
- **실습**: 프로젝트 저장/불러오기 + 공유

## Part 6: AI 개발 워크플로우 (2-3시간)

### 6.1 실전 AI 도구 활용

- Claude Code CLI 사용법
- GitHub Copilot vs Claude Code 비교
- CLAUDE.md 작성 전략 (프로젝트 컨텍스트)
- **실습**: Claude Code로 기능 추가

### 6.2 자동화 파이프라인

- GitHub Actions로 PR 리뷰 자동화
```yaml
- name: AI Code Review
  run: npx claude-review ${{ github.event.pull_request.number }}
```
- 커밋 메시지 자동 생성 (husky + AI)
- **실습**: PR 자동 리뷰 봇

### 6.3 AI 기반 테스트 생성
- Claude로 테스트 케이스 자동 생성
- Vitest + Testing Library 통합
- Playwright E2E 시나리오 생성
- Coverage 기반 누락 테스트 탐지
- **실습**: 자동 생성된 테스트 실행

## Part 7: 최적화 및 배포 (2시간)

### 7.1 성능 최적화

- Monaco 청크 분리 (webpack 설정)
- turbopack 으로 가능한지 확인 필요
```typescript
webpack: (config) => {
  config.optimization.splitChunks.cacheGroups.monaco = {
    name: 'monaco',
    test: /[\\/]node_modules[\\/]monaco-editor/,
  }
}
```
- AI API 요청 최적화 (batch, cache)
- Lighthouse 점수 개선 (90+ 목표)
- **실습**: 번들 사이즈 50% 감소

### 7.2 배포 및 운영

- Vercel 배포 (edge functions)
- 환경변수 관리 (API keys)
- Upstash Redis로 rate limiting
- Sentry 에러 트래킹
- **실습**: 프로덕션 배포 + 모니터링

## 보너스

### AI 프롬프트 엔지니어링

- Few-shot examples로 정확도 향상
- 코드 컨텍스트 최적화 (주변 100줄만)
- System prompt 튜닝
```typescript
const systemPrompt = `You are an expert TypeScript developer.
Always use modern React patterns and explain your choices.`
```
- github 연동도 해야할지 고민중

---

**총 시간**: 20-25시간  
**선수 지식**: React, TypeScript, Next.js 기초  
**최종 결과물**: 
- AI 기반 웹 코드 에디터 (Monaco + Claude)
- 실시간 코드 실행 (Sandpack)
- GitHub 연동 및 자동화 파이프라인
