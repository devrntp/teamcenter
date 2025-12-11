# Active Workspace 로컬 개발 환경 설정

## 🚀 빠른 시작

```bash
# 1. 환경 변수 설정 (TC 서버 주소)
set AW_PROXY_SERVER=http://your-tc-server:7001 ? 안되면 3000

# 2. 개발 서버 실행
npm run start

# 3. 브라우저에서 접속
# http://localhost:3000
```

---

## 📋 상세 설정 단계

### Step 1: 환경 초기화 (최초 1회)

```bash
# Windows
initenv.cmd

# NPM 의존성 설치
npm install
```

### Step 2: TC 서버 연결 설정

**방법 1: 환경 변수 사용 (권장)**

```bash
# Windows CMD
set AW_PROXY_SERVER=http://192.168.0.225:3000
$env:AW_PROXY_SERVER = "http://192.168.0.225:3000"

# 또는 Gateway 직접 지정
set ENDPOINT_GATEWAY=http://192.168.0.225:3000
$env:ENDPOINT_GATEWAY = "http://192.168.0.225:3000"
```

**방법 2: 포트 변경 (선택)**

```bash
set PORT=4000
$env:PORT = "4000"

(풀셋)
$env:AW_PROXY_SERVER = "http://192.168.0.225:3000"
$env:ENDPOINT_GATEWAY = "http://192.168.0.225:3000"
$env:PORT = "4000"
npm run start
```

### Step 3: 개발 서버 실행

```bash
npm run start
```

성공 시 출력:
```
setupProxy: Routing non-file communication to http://tc-server:7001/aw
setupProxy: devServer running on http://localhost:3000
```

### Step 4: 브라우저 접속

```
http://localhost:4000
```

---

## 🔧 주요 NPM 스크립트

| 명령어 | 설명 |
|--------|------|
| `npm run start` | 개발 서버 실행 |
| `npm run build` | 프로덕션 빌드 |
| `npm run clean` | 빌드 결과물 정리 |
| `npm run eslint` | 코드 린트 검사 |

---

## 🔍 디버깅

### 브라우저 개발자 도구
- `F12` → Console 탭에서 에러 확인
- Network 탭에서 SOA 호출 확인

### 디버그 모드
```
http://localhost:4000?debug=true
```

### VS Code 디버깅
1. `.vscode/launch.json` 설정
2. Chrome 디버거 연결

---

## ⚠️ 주의사항

1. **TC 서버 필수** - 백엔드 없이는 로그인/데이터 조회 불가
2. **CORS** - 서버와 다른 도메인이면 CORS 설정 필요
3. **인증서** - HTTPS 사용 시 인증서 설정 필요

---

## 📁 관련 파일

| 파일 | 역할 |
|------|------|
| `src/setupProxy.js` | 프록시 설정 (TC 서버 연결) |
| `package.json` | NPM 스크립트 정의 |
| `out/devServer.json` | 실행 중인 서버 정보 |
