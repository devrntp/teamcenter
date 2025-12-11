# Active Workspace Stage 폴더 구조

## 📁 폴더별 역할

| 폴더 | 역할 |
|------|------|
| **src/** | ⭐ **소스 코드** - OOTB 모듈(`repo/`), 솔루션 설정(`solution/`), 이미지 등 |
| **out/** | 빌드 결과물 출력 폴더 (12,325개 파일) |
| **node_modules/** | NPM 패키지 의존성 |
| **tarLibs/** | Siemens 제공 추가 라이브러리 패키지 (3,651개) |
| **build/** | 빌드 스크립트 및 설정 |
| **bin/** | 실행 스크립트 |
| **cli/** | CLI 도구 스크립트 |
| **conf/** | 설정 파일들 |
| **lib/** | 공통 라이브러리 |
| **xsd/** | XML 스키마 정의 |
| **public/** | 정적 리소스 |
| **localNpmServer/** | 로컬 NPM 서버 설정 |
| **.vscode/** | VS Code 설정 |
| **jh/** | 커스텀 작업 폴더 |

---

## 📄 주요 파일

| 파일 | 역할 |
|------|------|
| `package.json` | NPM 프로젝트 설정, 의존성 정의 |
| `build.json` | 빌드 소스 경로 설정 |
| `bundles.json` | JS 번들링 설정 |
| `awbuild.cmd` | Windows 빌드 스크립트 |
| `awbuild.sh` | Linux/Mac 빌드 스크립트 |
| `initenv.cmd` | 환경 초기화 스크립트 |
| `.eslintrc.js` | ESLint 코드 규칙 |
| `.npmrc` | NPM 설정 |

---

## 📂 src/ 하위 구조

```
src/
├── repo/           # OOTB 모듈 43개 (수정 금지❌)
│   ├── tc-aw-framework/   # 핵심 프레임워크
│   ├── subscription/      # 구독/알림
│   ├── workflow/          # 워크플로우
│   └── ...
├── solution/       # Workspace 정의 파일들
├── generator/      # 코드 생성기 템플릿
├── image/          # 아이콘/이미지 리소스
└── soa/            # SOA 서비스 정의
```

---

## 🔧 개발 시 주요 경로

| 용도 | 경로 |
|------|------|
| **OOTB 참고** | `src/repo/` (읽기 전용) |
| **커스텀 모듈** | `src/[myModule]/` 생성 |
| **빌드 결과** | `out/site/` |
| **아이콘 추가** | `src/image/` |
