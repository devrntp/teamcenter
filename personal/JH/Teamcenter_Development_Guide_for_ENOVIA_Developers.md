# 🎓 Teamcenter 개발 가이드 (ENOVIA 개발자용)

> Siemens Teamcenter vs Dassault ENOVIA 개발 비교 가이드  
> UI, Business Logic, 데이터 모델링 관점

---

## 📊 ENOVIA vs Teamcenter 용어 비교

| ENOVIA 개념 | Teamcenter 동등 개념 | 설명 |
|-------------|---------------------|------|
| **Business Object (BO)** | **Business Object (BO)** | 동일 용어 사용 |
| **Type** | **Type / Class** | TC는 Class(POM)와 Type(BO) 구분 |
| **Attribute** | **Property / Attribute** | 프로퍼티 = 속성 |
| **MQL (Matrix Query Language)** | **ITK (Integration Toolkit) / SOA** | 서버 API |
| **JSP / Widgets** | **AWC (Active Workspace) / RAC** | 클라이언트 UI |
| **OOTB Customization** | **BMIDE + ITK** | 모델 + 코드 커스터마이징 |
| **Business Admin** | **Organization (Org)** | 조직 관리 |
| **Schema** | **Data Model (POM Schema)** | 데이터베이스 스키마 |
| **JPO (Java Program Object)** | **User Exit / Extension / SOA Service** | 서버 로직 |
| **Trigger** | **Extension / Action Handler** | 이벤트 처리 |
| **VPM (CATIA)** | **NX Integration (TCxNX)** | CAD 통합 |

---

## 🏗️ Teamcenter 아키텍처 개요

```
┌─────────────────────────────────────────────────────────────┐
│                        클라이언트 계층                         │
├─────────────────┬─────────────────┬─────────────────────────┤
│   Active        │    Rich         │      NX/CAD             │
│   Workspace     │    Client       │      Integration        │
│   (AWC) - Web   │    (RAC) -Java  │      (TCxNX)           │
├─────────────────┴─────────────────┴─────────────────────────┤
│                     SOA Services Layer                       │
│              (SOAP/REST API - JSON/XML)                      │
├─────────────────────────────────────────────────────────────┤
│                    TC Server (Core)                          │
│  ┌──────────┬──────────┬──────────┬──────────┬──────────┐  │
│  │   ITK    │Extensions│ Workflow │   AM     │ Business │  │
│  │   API    │(User Exit)│ Handlers │(Access)  │  Rules   │  │
│  └──────────┴──────────┴──────────┴──────────┴──────────┘  │
├─────────────────────────────────────────────────────────────┤
│                   POM (Persistent Object Model)              │
│                      + Oracle/SQL Server                     │
└─────────────────────────────────────────────────────────────┘
```

### ENOVIA와의 비교

```
ENOVIA 3DExperience:                 Teamcenter:
┌───────────────────┐               ┌───────────────────┐
│   Widgets (Web)   │      ≈       │   AWC (Web)       │
│   Desktop Client  │      ≈       │   RAC (Java)      │
├───────────────────┤               ├───────────────────┤
│   Web Services    │      ≈       │   SOA Services    │
│   (REST/MQL)      │               │   (REST/SOAP)     │
├───────────────────┤               ├───────────────────┤
│   JPO/Triggers    │      ≈       │   ITK/Extensions  │
│   Business Logic  │               │   User Exits      │
├───────────────────┤               ├───────────────────┤
│   Schema/Types    │      ≈       │   BMIDE/POM       │
│   (Business Admin)│               │   (Data Model)    │
└───────────────────┘               └───────────────────┘
```

---

## 🛠️ BMIDE란? (Business Modeler IDE)

### ENOVIA의 Business Admin과 비교

| 기능 | ENOVIA Business Admin | Teamcenter BMIDE |
|------|----------------------|------------------|
| **타입/클래스 생성** | Schema Designer | BMIDE IDE |
| **속성 추가** | Add Attribute | Add Property |
| **LOV (List Of Values)** | Range Program/Values | LOV Definition |
| **명명 규칙** | Auto-Naming | Naming Rules |
| **관계 정의** | Relationship Type | GRM Rule (Relation) |
| **워크플로우** | Route/Workspace | Workflow Template |
| **UI 설정** | Form Config | StyleSheet/XML |

### BMIDE 샘플 프로젝트 분석

```
bmide\workspace\2506000.0.0\
├── a2custom/             ← 커스텀 프로젝트 (샘플 1)
│   ├── .project          ← Eclipse 프로젝트 파일
│   ├── ProjectInfo.xml   ← 빌드 설정 (컴파일러 옵션)
│   ├── extensions/       ← 데이터 모델 정의
│   │   ├── default.xml   ← 타입, 속성, LOV 정의 ⭐
│   │   ├── dependency.xml
│   │   └── lang/         ← 다국어 라벨
│   ├── icons/            ← 아이콘 이미지
│   ├── install/          ← 배포 스크립트
│   │   └── dc_contributions/  ← Deployment Center용
│   └── output/           ← 빌드 결과물
└── c9custom/             ← 커스텀 프로젝트 (샘플 2)
```

---

## 📝 데이터 모델 커스터마이징 (BMIDE)

### 샘플 분석: a2custom 프로젝트

`extensions/default.xml`에서 커스텀 Item 타입을 정의:

```xml
<!-- 1. 새로운 Class 정의 (DB 테이블 = POM) -->
<TcClass className="A2_custItem" 
         parentClassName="Item"
         description="Custom Item Type"/>

<TcClass className="A2_custItemRevision" 
         parentClassName="ItemRevision">
    <!-- 커스텀 속성 정의 -->
    <TcAttribute attributeName="a2_cust_string_att01" 
                 attributeType="POM_string"
                 maxStringLength="128"/>
    <TcAttribute attributeName="a2_cust_int_att02" 
                 attributeType="POM_int"/>
</TcClass>

<!-- 2. Business Object Type 정의 -->
<TcStandardType typeName="A2_custItem" 
                parentTypeName="Item"
                typeClassName="A2_custItem"/>

<!-- 3. LOV 정의 -->
<TcLOV name="A2_cust_LOV_01" lovType="ListOfValuesString">
    <TcLOVValue value="LOV1"/>
    <TcLOVValue value="LOV2"/>
    <TcLOVValue value="LOV3"/>
</TcLOV>

<!-- 4. LOV를 Property에 연결 -->
<TcLOVAttach lovName="A2_cust_LOV_01" typeName="A2_custItemRevision">
    <TcLOVAttachPropertyInfo valuePropertyName="a2_cust_LOV_01"/>
</TcLOVAttach>

<!-- 5. Naming Rule (자동 ID 생성) -->
<TcNamingRule name="A2_custItem">
    <TcPattern patternString="A-nnnnnn">
        <TcCounter initialValue="A-000000" maximumValue="A-999999"/>
    </TcPattern>
</TcNamingRule>
```

### ENOVIA 방식과 비교

```java
// ENOVIA: MQL로 타입 생성
// mql> add type "A2_custItem" derived "Part";
// mql> add attribute "a2_cust_string_att01" type string;

// Teamcenter: BMIDE XML 또는 GUI에서 정의
// → 더 선언적(Declarative) 방식
```

---

## 💻 Backend (서버) 개발

### 개발 방식 비교

| 항목 | ENOVIA | Teamcenter |
|------|--------|------------|
| **언어** | Java (JPO) | C/C++ (ITK) 또는 Java (SOA) |
| **API** | MQL, ADK, 3DSpace API | ITK, SOA Services |
| **트리거** | Trigger Program | Extension Rule, Post-Action |
| **배포** | Server Restart | Live Update 가능 |

### 1. ITK (Integration Toolkit) - C/C++ API

**ENOVIA의 JPO와 유사한 위치**

```c
// ITK 예시: 아이템 생성
#include <tccore/item.h>
#include <tc/tc.h>

int create_custom_item() {
    tag_t item = NULLTAG;
    tag_t rev = NULLTAG;
    
    // 아이템 생성
    ITEM_create_item(
        "A-000001",           // item_id
        "My Custom Item",     // name
        "A2_custItem",        // type
        NULL,                 // revision_id (auto)
        &item,
        &rev
    );
    
    // 속성 설정
    AOM_set_value_string(rev, "a2_cust_string_att01", "Hello");
    AOM_save(item);
    
    return ITK_ok;
}
```

**ENOVIA 비교 (JPO):**
```java
// ENOVIA JPO
DomainObject item = new DomainObject();
item.createObject(context, "A2_custItem", null);
item.setAttributeValue(context, "a2_cust_string_att01", "Hello");
```

### 2. Extension / User Exit

**ENOVIA의 Trigger와 유사**

```c
// Pre-Action: 저장 전 검증
int A2_validate_before_save(METHOD_message_t* msg, va_list args) {
    tag_t object = va_arg(args, tag_t);
    
    char* value = NULL;
    AOM_ask_value_string(object, "a2_cust_string_att01", &value);
    
    if (value == NULL || strlen(value) == 0) {
        EMH_store_error(EMH_severity_error, 
                        CUSTOM_ERROR_CODE, 
                        "Field cannot be empty!");
        return CUSTOM_ERROR_CODE;
    }
    return ITK_ok;
}

// Extension 등록 (XML)
```

```xml
<!-- extensions/default.xml에 Extension 정의 -->
<TcExtensionRule operationName="ITEM_create_msg"
                 ruleName="A2_validate_rule"
                 condition="isTrue"
                 executionMode="Pre"/>
```

### 3. SOA Services (Java/C++)

**ENOVIA의 Web Service와 유사**

```java
// SOA 클라이언트 코드 (Java)
import com.teamcenter.services.strong.core.DataManagementService;

DataManagementService dmService = DataManagementService.getService(connection);

// 아이템 생성
ItemProperties itemProps = new ItemProperties();
itemProps.itemId = "A-000001";
itemProps.name = "My Item";
itemProps.type = "A2_custItem";

CreateItemsResponse response = dmService.createItems(
    new ItemProperties[] { itemProps },
    null,  // container
    ""     // relationtype
);
```

### 4. Workflow Handler

**ENOVIA의 Route Program/Check와 유사**

```c
// Action Handler 예시
int A2_custom_action_handler(EPM_action_message_t msg) {
    tag_t task = msg.task;
    tag_t* attachments = NULL;
    int count = 0;
    
    // 첨부된 대상 가져오기
    EPM_ask_attachments(task, EPM_target_attachment, &count, &attachments);
    
    for (int i = 0; i < count; i++) {
        // 비즈니스 로직 실행
        AOM_set_value_string(attachments[i], "status", "Approved");
        AOM_save(attachments[i]);
    }
    return EPM_go;
}
```

---

## 🖥️ UI 개발

### 클라이언트 종류

| 클라이언트 | 기술 | 용도 | ENOVIA 비교 |
|-----------|------|------|------------|
| **AWC** | AngularJS/Angular | 웹 클라이언트 (주력) | 3DSpace Widgets |
| **RAC** | Eclipse RCP (Java) | 데스크톱 클라이언트 | Desktop Client |

### 1. Active Workspace (AWC) 커스터마이징

**경로:** `teamcenter_root/aws2/`

```
aws2/
├── stage/src/           ← 소스 코드
│   ├── declarativeui/   ← 선언적 UI 정의
│   ├── viewmodel/       ← ViewModel (JSON)
│   └── view/            ← View (HTML)
├── kit/                 ← 빌드 도구
└── build/               ← 빌드 결과
```

**AWC ViewModel 예시 (JSON):**
```json
{
    "schemaVersion": "1.0.0",
    "data": {
        "myProperty": {
            "displayName": "{{i18n.myPropertyLabel}}",
            "type": "STRING",
            "dbValue": ""
        }
    },
    "actions": {
        "doSearch": {
            "actionType": "JSFunction",
            "method": "performSearch",
            "inputData": {
                "searchCriteria": "{{data.searchString}}"
            }
        }
    }
}
```

**AWC View 예시 (HTML):**
```html
<aw-panel>
    <aw-panel-body>
        <aw-textbox prop="data.myProperty"></aw-textbox>
        <aw-button action="doSearch">Search</aw-button>
    </aw-panel-body>
</aw-panel>
```

### 2. RAC (Rich Application Client) 커스터마이징

**경로:** `teamcenter_root/portal/plugins/`

- Eclipse RCP 기반
- Java로 플러그인 개발
- XML Stylesheet로 Form/Summary 정의

**Stylesheet 예시 (tc_data/*.xml):**
```xml
<!-- A2_custItemRevision의 Summary 화면 정의 -->
<stylesheet>
    <form name="A2_custItemRevisionSummary">
        <section title="General">
            <property name="item_id"/>
            <property name="object_name"/>
            <property name="a2_cust_string_att01"/>
            <property name="a2_cust_LOV_01"/>
        </section>
    </form>
</stylesheet>
```

---

## 🔄 개발 워크플로우

### BMIDE 개발 사이클

```
┌────────────────────────────────────────────────────────────┐
│  1. BMIDE에서 데이터 모델 설계                               │
│     └─ 타입, 속성, LOV, 관계, 명명규칙                        │
├────────────────────────────────────────────────────────────┤
│  2. Template 생성 (Export)                                  │
│     └─ .zip 패키지 생성                                      │
├────────────────────────────────────────────────────────────┤
│  3. 서버에 배포                                              │
│     └─ TEM (Teamcenter Environment Manager) 또는           │
│        Deployment Center를 통해 설치                         │
├────────────────────────────────────────────────────────────┤
│  4. 데이터베이스 스키마 업데이트                               │
│     └─ install.exe / DB Update Script 실행                  │
├────────────────────────────────────────────────────────────┤
│  5. 클라이언트 캐시 갱신                                       │
│     └─ generate_client_meta_cache.exe                       │
└────────────────────────────────────────────────────────────┘
```

### ENOVIA 방식과 비교

| 단계 | ENOVIA | Teamcenter |
|------|--------|------------|
| 모델링 | Business Admin (Web) | BMIDE (Eclipse IDE) |
| 패키징 | Spinner / XML Export | Template Package (.zip) |
| 배포 | Install/Schema Update | Deployment Center / TEM |
| 반영 | Cache Clear / Restart | Live Update 가능 |

---

## 📂 주요 개발 파일 경로

### Backend 개발

| 경로 | 용도 |
|------|------|
| `teamcenter_root/include/` | ITK C 헤더 파일 |
| `teamcenter_root/include_cpp/` | C++ 헤더 파일 |
| `teamcenter_root/lib/` | ITK 라이브러리 (링크용) |
| `teamcenter_root/bin/user_exits.dll` | 컴파일된 커스텀 로직 |

### UI 개발

| 경로 | 용도 |
|------|------|
| `teamcenter_root/aws2/` | AWC 웹 클라이언트 |
| `teamcenter_root/portal/` | RAC 데스크톱 클라이언트 |
| `tc_data/*.xml` | Stylesheet (Summary/Form) |

### 데이터 모델

| 경로 | 용도 |
|------|------|
| `bmide/workspace/` | BMIDE 프로젝트 |
| `bmide/templates/` | 기본 템플릿 (foundation 등) |
| `tc_data/tc_preferences.xml` | 환경설정 |

---

## 🎯 ENOVIA 개발자를 위한 핵심 차이점

### 1. 언어 차이
- **ENOVIA**: Java (JPO), JavaScript (Widget)
- **Teamcenter**: C/C++ (ITK), Java (SOA), TypeScript/JS (AWC)

### 2. 개발 도구
- **ENOVIA**: Business Admin (Web), MQL Console
- **Teamcenter**: BMIDE (Eclipse IDE), Command Line Tools

### 3. 배포 방식
- **ENOVIA**: Server Restart 필요한 경우 많음
- **Teamcenter**: Live Update, Hot Deploy 지원

### 4. API 스타일
```java
// ENOVIA (MQL 스타일)
String result = MqlUtil.mqlCommand(context, 
    "print bus $1 select $2 dump", busId, "attribute[Status]");

// Teamcenter (ITK 스타일)
char* value = NULL;
ITK_CALL(AOM_ask_value_string(tag, "status", &value));
```

### 5. 트랜잭션
- **ENOVIA**: context.start/commit/abort
- **Teamcenter**: POM_AM__set_application_bypass / rollback

---

## 📚 학습 리소스

### 공식 문서
- Teamcenter Documentation Portal (Siemens GTAC)
- BMIDE User Guide
- ITK Programmer's Guide
- SOA Client Developer's Guide
- Active Workspace Configuration Guide

### 주요 유틸리티 (bin 폴더)
| 명령어 | 용도 |
|--------|------|
| `bmide_*.bat` | BMIDE 관련 도구 |
| `generate_client_meta_cache.exe` | 클라이언트 캐시 생성 |
| `preferences_manager.exe` | 환경설정 관리 |
| `install.exe` | 데이터모델 설치 |

---

## ✅ 시작하기 체크리스트

- [ ] BMIDE 설치 및 실행 확인
- [ ] 샘플 프로젝트 (a2custom) 분석
- [ ] 간단한 커스텀 타입 생성 실습
- [ ] ITK 빌드 환경 구성 (Visual Studio)
- [ ] AWC 커스터마이징 환경 구성 (Node.js)
- [ ] SOA Client Kit 다운로드 및 테스트

---

> 📂 **관련 파일**:
> - 샘플 프로젝트: `bmide/workspace/2506000.0.0/a2custom/`
> - BMIDE 클라이언트: `bmide/client/`
> - ITK 헤더: `teamcenter_root/include/`
