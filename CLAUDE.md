# pixi-live2d-display 개발 가이드

이 문서는 Claude가 pixi-live2d-display 프로젝트 개발을 지원하기 위한 필수 정보를 제공합니다.

## 프로젝트 개요

**pixi-live2d-display**는 PixiJS v7용 Live2D 통합 플러그인으로, 웹에서 Live2D 모델을 표시하기 위한 통합 프레임워크를 제공합니다. 향상된 기능과 단순화된 API로 모든 버전의 Live2D 모델(Cubism 2.1, 3, 4)을 지원합니다.

### 주요 기능
- 범용 Live2D 모델 지원 (Cubism 2.1/3/4)
- PIXI.RenderTexture 및 PIXI.Filter 호환성
- Pixi 스타일 변환 API
- 자동 상호작용 (포커싱, 히트 테스팅)
- 향상된 모션 예약 로직
- 파일 업로드/zip 로딩 지원
- 완전한 TypeScript 지원

## 빌드 시스템

### 빌드 명령어
- `npm run build` - 메인 빌드 명령 (dist/ 제거 후 build.js 실행)
- `npm run type` - TypeScript 선언 파일 생성
- `npm run lint` - prettier 규칙과 함께 ESLint 실행
- `npm run lint:fix` - 린팅 이슈 자동 수정
- `npm run test` - Vitest 브라우저 테스트 실행
- `npm run test:u` - 테스트 스냅샷 업데이트

### 빌드 프로세스
빌드는 Vite를 사용하는 `scripts/build.js`로 처리됩니다:
- 4개의 엔트리 포인트 빌드: cubism2, cubism4, index (둘 다), extra
- ES 모듈과 UMD 번들 모두 생성
- UMD 빌드의 압축 버전 생성
- 압축에 Terser 사용

### 빌드 출력물 (dist/)
- `index.js/es.js` - 두 Cubism 버전 모두 포함된 전체 번들
- `cubism2.js/es.js` - Cubism 2.1만
- `cubism4.js/es.js` - Cubism 4만
- `extra.js/es.js` - 추가 유틸리티
- `*.min.js` - 압축된 UMD 버전

## 개발 환경 설정

### 필수 의존성
- **Cubism Core 라이브러리**: `core/` 디렉터리에 배치해야 함
  - `live2d.min.js` (Cubism 2.1)
  - `live2dcubismcore.js` (Cubism 4)
- **Cubism 서브모듈**: `cubism/` 위치 (CubismWebFramework)

### 설정 프로세스
1. `npm run setup` 실행하여 Cubism core 라이브러리 다운로드
2. git 서브모듈이 초기화되었는지 확인: `git submodule update --init`

## 프로젝트 구조

### 소스 코드 (`src/`)
- `index.ts` - 메인 엔트리 포인트 (모든 것 내보냄)
- `csm2.ts` - Cubism 2.1 엔트리 포인트
- `csm4.ts` - Cubism 4 엔트리 포인트
- `extra.ts` - 추가 유틸리티
- `Live2DModel.ts` - 메인 모델 클래스
- `Live2DTransform.ts` - 변환 유틸리티
- `Automator.ts` - 자동 행동 처리

### 상세 디렉터리 구조
```
pixi-live2d-display/
├── src/                        # 메인 소스 코드
│   ├── index.ts               # 전체 엔트리 포인트 (cubism2 + cubism4)
│   ├── csm2.ts                # Cubism 2.1 전용 엔트리 포인트
│   ├── csm4.ts                # Cubism 4 전용 엔트리 포인트
│   ├── extra.ts               # 추가 유틸리티 엔트리 포인트
│   ├── common.ts              # 공통 내보내기
│   ├── config.ts              # 전역 설정
│   ├── Live2DModel.ts         # 메인 모델 클래스
│   ├── Live2DTransform.ts     # 변환 유틸리티
│   ├── Automator.ts           # 자동 행동 처리
│   ├── cubism-common/         # Cubism 버전 간 공유 기능
│   │   ├── ExpressionManager.ts    # 표정 관리
│   │   ├── FocusController.ts      # 포커스 제어
│   │   ├── InternalModel.ts        # 내부 모델 인터페이스
│   │   ├── ModelSettings.ts        # 모델 설정 관리
│   │   ├── MotionManager.ts        # 모션 관리
│   │   ├── MotionState.ts          # 모션 상태
│   │   ├── SoundManager.ts         # 사운드 관리
│   │   ├── constants.ts            # 공통 상수
│   │   └── index.ts               # 공통 내보내기
│   ├── cubism2/               # Cubism 2.1 전용 구현
│   │   ├── Cubism2ExpressionManager.ts  # Cubism2 표정 관리
│   │   ├── Cubism2InternalModel.ts      # Cubism2 내부 모델
│   │   ├── Cubism2ModelSettings.ts      # Cubism2 모델 설정
│   │   ├── Cubism2MotionManager.ts      # Cubism2 모션 관리
│   │   ├── Live2DExpression.ts          # Live2D 표정 처리
│   │   ├── Live2DEyeBlink.ts            # 눈 깜빡임 처리
│   │   ├── Live2DPhysics.ts             # 물리 시뮬레이션
│   │   ├── Live2DPose.ts                # 포즈 관리
│   │   ├── check-runtime.ts             # 런타임 확인
│   │   ├── factory.ts                   # Cubism2 팩토리
│   │   ├── index.ts                     # Cubism2 내보내기
│   │   └── patch-motion.ts              # 모션 패치
│   ├── cubism4/               # Cubism 4 전용 구현
│   │   ├── Cubism4ExpressionManager.ts  # Cubism4 표정 관리
│   │   ├── Cubism4InternalModel.ts      # Cubism4 내부 모델
│   │   ├── Cubism4ModelSettings.ts      # Cubism4 모델 설정
│   │   ├── Cubism4MotionManager.ts      # Cubism4 모션 관리
│   │   ├── check-runtime.ts             # 런타임 확인
│   │   ├── factory.ts                   # Cubism4 팩토리
│   │   ├── index.ts                     # Cubism4 내보내기
│   │   └── setup.ts                     # Cubism4 설정
│   ├── factory/               # 모델 로딩 및 생성
│   │   ├── FileLoader.ts              # 파일 로더
│   │   ├── Live2DFactory.ts           # 메인 팩토리 클래스
│   │   ├── Live2DLoader.ts            # Live2D 로더
│   │   ├── XHRLoader.ts               # XHR 로더
│   │   ├── ZipLoader.ts               # ZIP 파일 로더
│   │   ├── index.ts                   # 팩토리 내보내기
│   │   ├── model-middlewares.ts       # 모델 미들웨어
│   │   └── texture.ts                 # 텍스처 처리
│   ├── tools/                 # 개발 도구
│   │   └── HitAreaFrames.ts           # 히트 영역 프레임
│   ├── types/                 # TypeScript 타입 정의
│   │   ├── Cubism2Spec.d.ts           # Cubism2 타입 명세
│   │   ├── env.d.ts                   # 환경 타입
│   │   ├── events.d.ts                # 이벤트 타입
│   │   └── helpers.d.ts               # 헬퍼 타입
│   └── utils/                 # 유틸리티 함수
│       ├── array.ts                   # 배열 유틸리티
│       ├── index.ts                   # 유틸리티 내보내기
│       ├── log.ts                     # 로깅 유틸리티
│       ├── math.ts                    # 수학 유틸리티
│       ├── middleware.ts              # 미들웨어 유틸리티
│       ├── obj.ts                     # 객체 유틸리티
│       └── string.ts                  # 문자열 유틸리티
├── test/                       # 테스트 파일
│   ├── assets/                # 테스트용 Live2D 모델
│   │   ├── haru/              # Cubism 4 테스트 모델
│   │   └── shizuku/           # Cubism 2.1 테스트 모델
│   ├── features/              # 기능 통합 테스트
│   ├── units/                 # 단위 테스트
│   ├── rpc/                   # RPC 테스트 유틸리티
│   └── utils.ts               # 테스트 유틸리티
├── scripts/                   # 빌드 및 개발 스크립트
│   ├── build.js               # 메인 빌드 스크립트
│   ├── gen-docs.js            # 문서 생성
│   ├── gen-spec.js            # 스펙 생성
│   ├── patch-dts-generator.js # DTS 생성기 패치
│   ├── patch-types.js         # 타입 패치
│   └── setup.js               # 개발 환경 설정
├── core/                      # Cubism Core 라이브러리 (외부)
│   ├── live2d.min.js          # Cubism 2.1 코어
│   ├── live2d.d.ts            # Cubism 2.1 타입 정의
│   ├── live2dcubismcore.js    # Cubism 4 코어
│   └── live2dcubismcore.d.ts  # Cubism 4 타입 정의
├── cubism/                    # CubismWebFramework 서브모듈
├── docs/                      # 프로젝트 문서
├── playground/                # 개발/테스트용 플레이그라운드
│   ├── index.html
│   └── index.ts
├── dist/                      # 빌드 출력물
├── types/                     # 생성된 타입 정의 파일
├── package.json               # 패키지 설정
├── tsconfig.json              # TypeScript 설정
├── tsconfig.build.json        # 빌드용 TypeScript 설정
├── vite.config.ts             # Vite 설정
└── CLAUDE.md                  # 이 파일
```

### 테스트 구조 (`test/`)
- Vitest를 사용한 브라우저 기반 테스팅
- 이미지 스냅샷을 사용한 시각적 회귀 테스트
- 핵심 컴포넌트 단위 테스트
- 기능 통합 테스트
- 테스트 에셋에는 Shizuku (Cubism 2.1)와 Haru (Cubism 4) 모델 포함

## 설정 파일

### TypeScript 설정
- `tsconfig.json` - 경로 별칭이 있는 메인 TS 설정 (`@/*`, `@cubism/*`)
- `tsconfig.build.json` - 빌드별 설정
- 엄격한 타입 검사로 ES6+ 대상

### Vite 설정
- `/playground/index.html`에 플레이그라운드가 있는 개발 서버
- `@` (src) 및 `@cubism` (cubism/src)에 대한 경로 별칭
- 외부 PixiJS 의존성 (@pixi/* 패키지 사용)
- Chrome을 사용한 브라우저 테스팅 설정
- 테스트에서 Cubism core 파일 로딩을 위한 커스텀 플러그인

## 개발 가이드라인

### 코드 표준
- 엄격한 타입 검사로 TypeScript 사용
- 기존 코드 규칙과 패턴 따르기
- 포맷팅에 Prettier와 함께 ESLint 사용
- 외부 의존성: PixiJS 패키지만 (@pixi/*)

### 테스팅 요구사항
- 커밋 전 항상 테스트 실행: `npm run test`
- UI 변경 시 스냅샷 업데이트: `npm run test:u`
- 브라우저 환경(Chrome)에서 테스트 통과해야 함

### 빌드 검증
- 코드 품질 확인: `npm run lint` 실행
- TypeScript 컴파일 검증: `npm run type` 실행
- 모든 번들이 올바르게 빌드되는지 확인: `npm run build` 실행

## 엔트리 포인트 및 내보내기

### 패키지 내보내기
- `.` - 메인 내보내기 (모든 Cubism 버전)
- `./cubism2` - Cubism 2.1만
- `./cubism4` - Cubism 4만
- `./extra` - 추가 유틸리티

### 핵심 클래스 및 API
- `Live2DModel` - `src/Live2DModel.ts`의 메인 모델 클래스
- `Live2DFactory` - `src/factory/Live2DFactory.ts`의 모델 로딩
- 각 디렉터리의 Cubism별 구현

## 의존성

### 런타임 의존성
- **피어 의존성**: `pixi.js ^7.0.0`
- **외부 코어**: Cubism 런타임 라이브러리 (번들되지 않음)

### 개발 의존성
- **빌드**: Vite, TypeScript, Terser
- **테스팅**: Vitest, WebDriverIO, jest-image-snapshot
- **린팅**: ESLint, Prettier
- **타입**: @types/node, @types/lodash-es
- **문서**: MkDocs, Handlebars

## 일반적인 개발 작업

### 기능 추가
1. 기능이 어떤 Cubism 버전에 영향을 주는지 식별
2. 적절한 디렉터리에 구현 (cubism-common, cubism2, cubism4)
3. TypeScript 타입 추가
4. 기능을 다루는 테스트 작성
5. 필요시 문서 업데이트

### 버그 수정
1. 테스트 케이스로 이슈 재현
2. 적절한 모듈에서 수정
3. 모든 테스트 통과 확인
4. 해당하는 경우 모든 Cubism 버전에서 검증

### 성능 최적화
1. 브라우저 개발자 도구로 프로파일링
2. 불필요한 재렌더링이나 계산 확인
3. 캐싱 전략 고려
4. 다양한 모델 복잡도로 테스트

## 개발 참고사항

- 이것은 그래픽/애니메이션 라이브러리입니다 - 성능이 중요함
- Live2D 모델은 많은 움직이는 부분(표정, 모션, 물리)이 있는 복잡한 구조
- 웹 배포를 위한 브라우저 호환성이 중요
- 다른 사용 사례를 위해 ES 모듈과 UMD 빌드 모두 유지
- 프로젝트는 Cubism 버전 간 하위 호환성을 유지함