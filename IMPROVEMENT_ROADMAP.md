# pixi-live2d-display 포크 개선작업 로드맵

> 2년간 업데이트되지 않은 라이브러리의 현대화 및 개선 계획

## 📊 현재 프로젝트 상태 분석

### 주요 이슈
- **마지막 업데이트**: 2년 전 (2022년)
- **보안 취약점**: 22개 (Critical 3개, High 15개, Moderate 3개, Low 1개)
- **PixiJS 버전**: v7.3.2 (현재 최신: v8.12.0)
- **구형 의존성**: 24개 패키지가 outdated

---

## 🚨 **긴급 우선순위 (Critical)**

### 1. **보안 취약점 해결**
**상태**: 🔴 Critical  
**예상 기간**: 1-2주

#### 주요 취약점
- **`elliptic` (Critical)**
  - ECDSA 서명 검증 취약점
  - BER-encoded 서명 허용 문제
  - 개인키 추출 가능성
- **`pbkdf2` (Critical)**
  - 키 생성 예측 가능성
  - 초기화되지 않은 메모리 반환
- **`vitest` (Critical)**
  - 악성 웹사이트 접근 시 RCE 취약점
- **`rollup`, `ws`, `tar-fs` (High)**
  - DOM Clobbering으로 인한 XSS
  - DoS 공격 취약점
  - 경로 순회 취약점

#### 해결 방법
```bash
npm audit fix
npm audit fix --force  # 필요시 breaking changes 포함
```

### 2. **PixiJS v8 호환성 대응**
**상태**: 🔴 Critical  
**예상 기간**: 4-6주

#### 주요 Breaking Changes
- **패키지 구조 변경**
  ```js
  // 기존 (v7)
  import { Application } from '@pixi/app';
  import { Sprite } from '@pixi/sprite';
  
  // 신규 (v8)
  import { Application, Sprite } from 'pixi.js';
  ```

- **BaseTexture 제거**
  ```js
  // v7
  const baseTexture = BaseTexture.from(source);
  const texture = new Texture(baseTexture);
  
  // v8
  const textureSource = TextureSource.from(source);
  const texture = new Texture({ source: textureSource });
  ```

- **Graphics API 완전 재작성**
  ```js
  // v7
  graphics.beginFill(0xff0000);
  graphics.drawRect(0, 0, 100, 100);
  graphics.endFill();
  
  // v8
  graphics.rect(0, 0, 100, 100);
  graphics.fill(0xff0000);
  ```

#### 마이그레이션 체크리스트
- [ ] 패키지 import 구조 변경
- [ ] BaseTexture → TextureSource 마이그레이션
- [ ] Graphics API 업데이트
- [ ] Application 초기화 방식 변경
- [ ] ParticleContainer 대체 방안 검토
- [ ] 테스트 케이스 업데이트

---

## 📦 **높은 우선순위 (High)**

### 3. **의존성 업데이트**
**상태**: 🟡 High  
**예상 기간**: 2-3주

#### 주요 업데이트 대상
| 패키지 | 현재 버전 | 최신 버전 | 변경 사항 |
|--------|-----------|-----------|-----------|
| **TypeScript** | 5.2.2 | 5.9.2 | 마이너 업데이트 |
| **Vite** | 5.0.0 | 7.1.1 | 메이저 업데이트 |
| **Vitest** | 1.0.1 | 3.2.4 | 메이저 업데이트 |
| **ESLint** | 8.51.0 | 9.33.0 | 메이저 업데이트 |
| **@typescript-eslint** | 6.7.4 | 8.39.0 | 메이저 업데이트 |

### 4. **빌드 시스템 현대화**
**상태**: 🟡 High  
**예상 기간**: 2-3주

#### 개선 사항
- **Node.js 지원**: Node 20 → Node 22
- **ES Target**: ES2020 → ES2022/2023
- **Tree-shaking 최적화**
- **Bundle 분석 및 크기 최적화**
- **Source Maps 개선**

### 5. **Live2D Core 업데이트**
**상태**: 🟡 High  
**예상 기간**: 1-2주

#### 작업 내용
- Cubism SDK 최신 버전 호환성 확인
- WebGL 2.0 / WebGPU 지원 검토
- Core 라이브러리 자동 다운로드 스크립트 개선
- 버전 호환성 매트릭스 작성

---

## 🛠 **중간 우선순위 (Medium)**

### 6. **성능 개선**
**상태**: 🟢 Medium  
**예상 기간**: 3-4주

#### 최적화 영역
- **렌더링 성능**
  - 드로우 콜 최적화
  - 배치 렌더링 개선
  - 텍스처 아틀라스 활용
- **메모리 관리**
  - 메모리 리크 방지
  - 가비지 컬렉션 최적화
  - 리소스 풀링
- **모델 로딩**
  - 점진적 로딩
  - 캐싱 전략 개선
  - 백그라운드 로딩

### 7. **개발자 경험 개선**
**상태**: 🟢 Medium  
**예상 기간**: 2-3주

#### 개선 사항
- **Hot Module Replacement** (HMR) 지원
- **개발 서버 성능** 향상
- **에러 메시지** 개선
- **TypeScript strict mode** 적용
- **IntelliSense** 지원 강화

### 8. **테스트 인프라 개선**
**상태**: 🟢 Medium  
**예상 기간**: 2-3주

#### 개선 계획
- **테스트 실행 속도** 향상 (병렬 실행)
- **CI/CD 파이프라인** 최적화
- **크로스 브라우저 테스트** 확장
- **E2E 테스트** 추가 (Playwright)
- **성능 regression 테스트**

---

## 🚀 **새로운 기능 (New Features)**

### 9. **최신 웹 기술 지원**
**상태**: 🔵 Feature  
**예상 기간**: 4-6주

#### WebGPU 지원
- PixiJS v8의 WebGPU 백엔드 활용
- WebGL과의 fallback 메커니즘
- 성능 벤치마킹

#### Web Workers 지원
```js
// 예시: 백그라운드에서 모델 처리
const worker = new Worker('live2d-worker.js');
worker.postMessage({ modelUrl: 'model.json' });
```

#### WebAssembly 활용
- Live2D 모션 계산 WASM 모듈화
- 성능 향상 측정 및 검증

### 10. **모던 JavaScript 기능**
**상태**: 🔵 Feature  
**예상 기간**: 2-3주

#### ES2023 기능 활용
```js
// Top-level await
const model = await Live2DModel.from('model.json');

// Private class fields
class Live2DModel {
  #internalState = {};
  
  get #isLoaded() {
    return this.#internalState.loaded;
  }
}

// Dynamic imports for code splitting
const { Live2DModel } = await import('./live2d-model.js');
```

### 11. **개발 도구 개선**
**상태**: 🔵 Feature  
**예상 기간**: 4-5주

#### 내장 도구
- **Performance Monitor**: 실시간 FPS/메모리 모니터링
- **Model Inspector**: 모델 구조 시각화
- **Animation Debugger**: 모션 디버깅 도구
- **Texture Viewer**: 텍스처 미리보기

---

## 🔧 **기술적 개선**

### 12. **코드베이스 현대화**
**상태**: 🟠 Technical  
**예상 기간**: 3-4주

#### 코드 품질 향상
- **Prettier 3.x** 포맷팅 규칙
- **ESLint 9.x** 룰셋 업데이트
- **JSDoc → TSDoc** 마이그레이션
- **Code coverage** 90% 이상 목표

### 13. **타입 시스템 개선**
**상태**: 🟠 Technical  
**예상 기간**: 2-3주

#### TypeScript 강화
```ts
// 개선된 Generic 타입
interface Live2DModelOptions<T extends CubismVersion> {
  version: T;
  settings: T extends 'cubism2' ? Cubism2Settings : Cubism4Settings;
}

// Utility Types 활용
type RequiredModelSettings = Required<Pick<ModelSettings, 'textures' | 'model'>>;
```

### 14. **번들링 최적화**
**상태**: 🟠 Technical  
**예상 기간**: 2-3주

#### 최적화 전략
- **Rollup 4.x** 최신 기능 활용
- **Code Splitting** 개선
- **Chunk 최적화** (vendor, common, feature별)
- **CDN 친화적** 빌드 생성

---

## 📱 **플랫폼 지원 확장**

### 15. **모바일 최적화**
**상태**: 🟣 Platform  
**예상 기간**: 3-4주

#### 모바일 특화 개선
- **iOS Safari** 호환성 이슈 해결
- **Android WebView** 성능 최적화
- **터치 이벤트** 처리 개선
- **배터리 효율성** 향상
- **메모리 제한** 대응

### 16. **서버사이드 지원**
**상태**: 🟣 Platform  
**예상 기간**: 4-5주

#### SSR/SSG 지원
```js
// 서버에서 모델 렌더링
import { createHeadlessRenderer } from 'pixi-live2d-display/server';

const renderer = createHeadlessRenderer();
const image = await renderer.renderToBuffer(model);
```

---

## 🎯 **추천 우선순위 로드맵**

### 📅 **Phase 1: 기반 안정화 (즉시 시작 ~ 6주)**
**목표**: 보안 및 호환성 확보

1. **Week 1-2**: 보안 취약점 해결
2. **Week 2-4**: PixiJS v8 호환성 조사 및 계획 수립
3. **Week 3-5**: 핵심 의존성 업데이트 (TypeScript, Vite)
4. **Week 5-6**: 기본 기능 검증 및 테스트

### 📅 **Phase 2: 현대화 (6주 ~ 12주)**
**목표**: 최신 기술 스택 적용

1. **Week 7-10**: PixiJS v8 마이그레이션 실행
2. **Week 8-10**: 빌드 시스템 현대화
3. **Week 10-12**: 테스트 인프라 개선

### 📅 **Phase 3: 최적화 (12주 ~ 18주)**
**목표**: 성능 및 개발자 경험 향상

1. **Week 13-16**: 성능 최적화 및 프로파일링
2. **Week 15-17**: 개발자 도구 개발
3. **Week 17-18**: 문서화 및 예제 개선

### 📅 **Phase 4: 확장 (18주 ~ 24주)**
**목표**: 새로운 기능 및 플랫폼 지원

1. **Week 19-22**: WebGPU 지원 개발
2. **Week 21-23**: 모바일 최적화
3. **Week 23-24**: 서버사이드 렌더링 지원

---

## 📋 **작업 진행 체크리스트**

### ✅ 완료된 작업
- [x] 현재 프로젝트 상태 분석
- [x] 보안 취약점 식별
- [x] PixiJS v8 호환성 조사
- [x] 개선 로드맵 작성

### 🔄 진행 중인 작업
- [ ] 보안 취약점 해결
- [ ] PixiJS v8 마이그레이션 계획 수립

### 📋 대기 중인 작업
- [ ] 의존성 업데이트
- [ ] 빌드 시스템 현대화
- [ ] 성능 최적화
- [ ] 새로운 기능 개발

---

## 📈 **성공 지표 (KPI)**

### 기술적 지표
- **보안 취약점**: 0개 목표
- **번들 크기**: 20% 감소 목표
- **빌드 시간**: 30% 단축 목표
- **테스트 커버리지**: 90% 이상

### 성능 지표
- **초기 로딩 시간**: 50% 개선
- **렌더링 FPS**: 60fps 안정성 확보
- **메모리 사용량**: 30% 절약

### 개발자 경험 지표
- **타입 안정성**: TypeScript strict mode 100%
- **문서화**: API 문서 완성도 95%
- **예제 코드**: 10개 이상 실용적 예제

---

## 🤝 **기여 가이드라인**

### 우선순위별 기여 방법
1. **Critical**: 보안 패치, 호환성 이슈 해결
2. **High**: 핵심 기능 개선, 성능 최적화
3. **Medium**: 개발자 도구, 테스트 개선
4. **Feature**: 새로운 기능, 플랫폼 지원

### 코드 리뷰 기준
- 모든 변경사항은 테스트 포함 필수
- 성능에 영향을 주는 변경사항은 벤치마크 필요
- Breaking changes는 마이그레이션 가이드 작성

---

> **⚠️ 중요**: 이 로드맵은 프로젝트 진행상황에 따라 조정될 수 있습니다. 정기적으로 업데이트하여 최신 상태를 유지해주세요.

**최종 업데이트**: 2025년 8월 8일