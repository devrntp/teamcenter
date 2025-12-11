# Teamcenter Active Workspace 커스텀 화면 개발 가이드

> **⚠️ 중요**: `src/repo/` 폴더는 OOTB(Out-of-the-Box) 소스입니다. **절대 수정하지 마세요!**  
> 커스텀 코드는 별도의 커스텀 모듈 폴더를 생성하여 개발합니다.

---

## 📁 프로젝트 구조 개요

```
stage/
├── src/
│   ├── repo/                    # ❌ OOTB 소스 (수정 금지)
│   │   ├── tc-aw-framework/     # 핵심 프레임워크
│   │   ├── subscription/        # 예시 모듈
│   │   └── ...
│   ├── solution/                # Workspace 정의 파일들
│   └── [custom-module]/         # ✅ 커스텀 모듈 생성 위치
├── build.json                   # 빌드 설정
└── bundles.json                 # 번들 설정
```

---

## 🧩 모듈 구조 이해

각 AW 모듈은 다음 구조를 가집니다:

```
[모듈명]/
├── module.json              # 모듈 메타데이터 정의
├── kit.json                 # 모듈 패키징 및 SOA 의존성
├── commandsViewModel.json   # Commands, Handlers, Placements, Actions
├── states.json              # URL 라우팅 및 View 매핑
├── bootstrap.json           # 초기화 스크립트
├── aliasRegistry.json       # 아이콘/이미지 별칭
├── typeIconsRegistry.json   # 타입별 아이콘 등록
└── src/
    └── assets/
        ├── html/            # View 템플릿 (*View.html)
        ├── viewmodel/       # ViewModel 정의 (*ViewModel.json)
        ├── js/              # 서비스/유틸리티 JS
        ├── i18n/            # 다국어 메시지
        └── policies/        # Property Policy
```

---

## 🚀 새로운 화면 개발 단계

### Step 1: 커스텀 모듈 폴더 생성

`src/` 아래에 새로운 모듈 폴더를 생성합니다:

```
src/
└── myCustomModule/
    ├── module.json
    ├── kit.json
    ├── commandsViewModel.json
    ├── states.json
    └── src/
        └── assets/
            ├── html/
            ├── viewmodel/
            ├── js/
            └── i18n/
```

---

### Step 2: module.json 작성

```json
{
  "name": "myCustomModule",
  "description": "My Custom Module Description",
  "pathOffset": ".",
  "version": "1.0.0",
  "author": "Your Company"
}
```

---

### Step 3: kit.json 작성

```json
{
  "name": "myCustomModule",
  "description": "My Custom Module",
  "modules": [
    "myCustomModule"
  ],
  "version": "1.0.0",
  "OOTB": false,
  "soaDeps": [
    "Teamcenter.Soa.Core"
  ]
}
```

---

### Step 4: View 파일 생성 (HTML)

`src/assets/html/MyCustomPanelView.html`:

```html
<aw-command-panel caption="{{i18n.panelTitle}}" context="subPanelContext">
    <div class="aw-layout-panelBody">
        <!-- aw-widget 사용 예시 -->
        <aw-textbox prop="data.myTextProperty"></aw-textbox>
        
        <!-- 버튼 예시 -->
        <aw-button action="submitAction">{{i18n.submitButtonText}}</aw-button>
    </div>
</aw-command-panel>
```

**주요 AW 컴포넌트:**
| 컴포넌트 | 용도 |
|---------|------|
| `aw-command-panel` | 명령 패널 컨테이너 |
| `aw-command-sub-panel` | 서브 패널 |
| `aw-navigate-panel` | 네비게이션 패널 |
| `aw-textbox` | 텍스트 입력 |
| `aw-button` | 버튼 |
| `aw-list` | 리스트 |
| `aw-table` | 테이블 |
| `aw-include` | 다른 View 포함 |

---

### Step 5: ViewModel 파일 생성 (JSON)

`src/assets/viewmodel/MyCustomPanelViewModel.json`:

```json
{
  "schemaVersion": "1.0.0",
  "imports": [],
  
  "i18n": {
    "panelTitle": ["MyCustomMessages"],
    "submitButtonText": ["MyCustomMessages"]
  },
  
  "props": {
    "sub-panel-context": {
      "type": "object"
    }
  },
  
  "data": {
    "myTextProperty": {
      "displayName": "{{i18n.myFieldLabel}}",
      "type": "STRING",
      "dbValue": "",
      "isRequired": true
    }
  },
  
  "actions": {
    "submitAction": {
      "actionType": "JSFunction",
      "method": "mySubmitMethod",
      "deps": "js/myCustomService",
      "inputData": {
        "textValue": "{{data.myTextProperty.dbValue}}"
      },
      "events": {
        "success": [
          { "name": "myCustom.submitSuccess" }
        ]
      }
    }
  },
  
  "lifecycleHooks": {
    "onMount": "initAction"
  },
  
  "onEvent": [
    {
      "eventId": "myCustom.submitSuccess",
      "action": "closePanel"
    }
  ],
  
  "conditions": {
    "isButtonEnabled": {
      "expression": "data.myTextProperty.dbValue !== ''"
    }
  }
}
```

**ViewModel 주요 섹션:**
| 섹션 | 설명 |
|------|------|
| `i18n` | 다국어 메시지 참조 |
| `data` | 화면 데이터/프로퍼티 정의 |
| `actions` | 사용자 액션 정의 (JSFunction, TcSoaService, Event, dialog 등) |
| `lifecycleHooks` | 컴포넌트 생명주기 (`onMount`, `onUnmount`) |
| `onEvent` | 이벤트 리스너 |
| `conditions` | 조건부 로직 |

---

### Step 6: commandsViewModel.json 작성

```json
{
  "commands": {
    "MyCustomCommand": {
      "iconId": "cmdAdd",
      "title": "{{i18n.myCommandTitle}}",
      "description": "{{i18n.myCommandDesc}}"
    }
  },
  
  "commandHandlers": {
    "MyCustomCommandHandler": {
      "id": "MyCustomCommand",
      "action": "openMyCustomPanel",
      "activeWhen": true,
      "visibleWhen": {
        "condition": "conditions.isMyCustomCommandVisible"
      }
    }
  },
  
  "commandPlacements": {
    "MyCustomCommandPlacement": {
      "id": "MyCustomCommand",
      "uiAnchor": "aw_primaryWorkArea",
      "priority": 100
    }
  },
  
  "actions": {
    "openMyCustomPanel": {
      "actionType": "dialog",
      "inputData": {
        "options": {
          "view": "MyCustomPanel",
          "parent": ".aw-layout-workareaMain",
          "placement": "right",
          "width": "STANDARD",
          "height": "FULL",
          "isCloseVisible": false
        }
      }
    }
  },
  
  "conditions": {
    "isMyCustomCommandVisible": {
      "expression": "ctx.selected && ctx.selected.type === 'Item'"
    }
  },
  
  "i18n": {
    "myCommandTitle": ["MyCustomMessages"],
    "myCommandDesc": ["MyCustomMessages"]
  }
}
```

**주요 uiAnchor 위치:**
| uiAnchor | 위치 |
|----------|------|
| `aw_globalToolbar` | 전역 툴바 |
| `aw_primaryWorkArea` | 메인 작업 영역 |
| `aw_userSessionbar` | 사용자 세션바 |
| `aw_rightWall` | 우측 패널 영역 |

---

### Step 7: states.json 작성 (페이지 라우팅)

새로운 Location/Sublocation을 추가할 때:

```json
{
  "com_mycompany_MyCustomLocation": {
    "data": {
      "browserSubTitle": {
        "source": "/i18n/MyCustomMessages",
        "key": "myLocationTitle"
      },
      "headerTitle": {
        "source": "/i18n/MyCustomMessages",
        "key": "myLocationTitle"
      }
    },
    "view": "AwSearchLocation",
    "parent": "root"
  },
  "com_mycompany_MyCustomSublocation": {
    "data": {
      "priority": 0,
      "label": {
        "source": "/i18n/MyCustomMessages",
        "key": "mySublocationLabel"
      }
    },
    "params": {
      "filter": null
    },
    "parent": "com_mycompany_MyCustomLocation",
    "view": "MyCustomPage",
    "url": "/com.mycompany.MyCustomSublocation"
  }
}
```

---

### Step 8: 서비스 JS 파일 작성

`src/assets/js/myCustomService.js`:

```javascript
import eventBus from 'js/eventBus';
import appCtxService from 'js/appCtxService';
import soaService from 'soa/kernel/soaService';
import messagingService from 'js/messagingService';

/**
 * My custom method
 * @param {String} textValue - input text value
 * @returns {Promise} promise
 */
export const mySubmitMethod = function( textValue ) {
    // SOA 호출 예시
    return soaService.postUnchecked( 'Core-2006-03-DataManagement', 'createObjects', {
        input: [{
            boName: 'Item',
            stringProps: {
                object_name: textValue
            }
        }]
    }).then( function( response ) {
        messagingService.showInfo( 'Created successfully!' );
        return response;
    });
};

export default {
    mySubmitMethod
};
```

---

### Step 9: i18n 메시지 파일 작성

`src/assets/i18n/MyCustomMessages.json`:

```json
{
  "panelTitle": "My Custom Panel",
  "submitButtonText": "Submit",
  "myCommandTitle": "My Custom Command",
  "myCommandDesc": "Opens my custom panel",
  "myFieldLabel": "Enter Name",
  "myLocationTitle": "My Custom Location",
  "mySublocationLabel": "My Sublocation"
}
```

다국어 지원시 파일명에 locale 추가:
- `MyCustomMessages.json` (기본/영어)
- `MyCustomMessages_ko.json` (한국어)
- `MyCustomMessages_ja.json` (일본어)

---

## 🔧 빌드 및 배포

### build.json에 커스텀 모듈 경로 추가

`build.json`의 `srcPaths`에 커스텀 모듈 경로를 추가합니다:

```json
{
  "srcPaths": [
    "...",
    "src/myCustomModule"
  ]
}
```

### 빌드 명령어

```bash
# 개발 빌드
awbuild.cmd

# 또는
npm run dev
```

---

## 📋 개발 체크리스트

| 단계 | 파일 | 완료 |
|------|------|------|
| 1 | 커스텀 모듈 폴더 생성 | ☐ |
| 2 | `module.json` 작성 | ☐ |
| 3 | `kit.json` 작성 | ☐ |
| 4 | View HTML 작성 (`*View.html`) | ☐ |
| 5 | ViewModel JSON 작성 (`*ViewModel.json`) | ☐ |
| 6 | `commandsViewModel.json` 작성 | ☐ |
| 7 | `states.json` 작성 (필요시) | ☐ |
| 8 | 서비스 JS 작성 | ☐ |
| 9 | i18n 메시지 파일 작성 | ☐ |
| 10 | `build.json` 경로 추가 | ☐ |
| 11 | 빌드 및 테스트 | ☐ |

---

## 💡 화면 유형별 구현 방법

### 1. Command Panel (우측 패널)

```json
// commandsViewModel.json의 action
"openPanel": {
  "actionType": "dialog",
  "inputData": {
    "options": {
      "view": "MyPanelView",
      "placement": "right",
      "width": "STANDARD"
    }
  }
}
```

### 2. Modal Dialog (팝업)

```json
"openDialog": {
  "actionType": "dialog",
  "inputData": {
    "options": {
      "view": "MyDialogView",
      "isModal": true,
      "width": "MEDIUM"
    }
  }
}
```

### 3. Full Page (전체 페이지)

`states.json`에 등록하고 URL로 접근:
```
/#/com.mycompany.MyCustomSublocation
```

---

## 🔍 디버깅 팁

1. **브라우저 개발자 도구**: F12로 Console 확인
2. **AW Debug**: URL에 `?debug=true` 추가
3. **ViewModel 확인**: `appCtxService`를 통해 context 확인
4. **이벤트 디버깅**: `eventBus.subscribe('*', console.log)` 사용

---

## 📚 참고 자료

- OOTB 모듈 예시: `src/repo/subscription/` 폴더 참조
- 프레임워크: `src/repo/tc-aw-framework/` 참조
- Generator 템플릿: `src/generator/templates/` 참조
