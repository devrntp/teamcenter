# Teamcenter CAD Integration 개발 가이드

> **작성일**: 2025-11-28  
> **목적**: Teamcenter CAD 통합 개발 및 ENOVIA와의 비교 분석

---

## 📋 목차

1. [CAD Integration 개요](#cad-integration-개요)
2. [Teamcenter의 CAD Integration 강점](#teamcenter의-cad-integration-강점)
3. [지원 CAD 시스템](#지원-cad-시스템)
4. [통합 아키텍처](#통합-아키텍처)
5. [개발 방법](#개발-방법)
6. [ENOVIA와 비교](#enovia와-비교)
7. [실무 예제](#실무-예제)
8. [장단점 분석](#장단점-분석)

---

## CAD Integration 개요

### CAD Integration이란?

PLM 시스템과 CAD 도구를 연결하여:
- ✅ CAD에서 직접 PLM에 저장/불러오기
- ✅ 설계 데이터 버전 관리
- ✅ BOM 자동 동기화
- ✅ 설계 재사용
- ✅ 협업 설계 (Design in Context)

### 왜 중요한가?

```
설계자 워크플로우:

❌ 통합 없이:
CAD 설계 → 파일 저장 → 수동으로 PLM 업로드 
→ BOM 수동 입력 → 속성 수동 입력 → 오류 발생!

✅ 통합 있을 때:
CAD 설계 → "Save to Teamcenter" 클릭 
→ 자동 업로드 → BOM 자동 생성 → 속성 자동 매핑 → 완료!
```

---

## Teamcenter의 CAD Integration 강점

### 🏆 1. Siemens 자체 CAD (NX)와의 완벽 통합

**Teamcenter와 NX는 같은 회사 제품!**

```
Siemens Digital Industries Software
├─ Teamcenter (PLM)
└─ NX (CAD)
   └─ 네이티브 통합!
```

#### NX와의 통합 기능

| 기능 | 설명 | ENOVIA 대비 |
|------|------|-------------|
| **Managed Mode** | NX가 Teamcenter 없이 실행 불가 (완전 통합) | 🏆 월등 |
| **JT 자동 생성** | 경량 3D 파일 자동 생성 | 🏆 기본 제공 |
| **Design in Context** | 상위 Assembly 맥락에서 설계 | 🏆 완벽 지원 |
| **Inter-Part Link** | 부품 간 참조 관계 자동 관리 | 🏆 네이티브 지원 |
| **Validation** | 설계 규칙 자동 검증 | 🏆 강력 |

### 🏆 2. 주요 CAD 시스템 공식 지원

모든 주요 CAD와 **공식 Integrator** 제공:

```
✅ Siemens NX (완벽 통합)
✅ CATIA V5/V6 (Dassault)
✅ SolidWorks (Dassault)
✅ Creo (PTC)
✅ AutoCAD (Autodesk)
✅ Inventor (Autodesk)
✅ Solid Edge (Siemens)
```

### 🏆 3. Multi-CAD 환경 지원

하나의 제품에서 **여러 CAD 혼용 가능**:

```
자동차 프로젝트
├─ 차체: CATIA V5
├─ 엔진: NX
├─ 전기 배선: AutoCAD
└─ 시트: SolidWorks

→ Teamcenter에서 통합 관리!
```

### 🏆 4. JT (Jupiter Tessellation) 기술

**Siemens의 경량 3D 파일 형식**

```
CAD 원본 파일: 100MB
     ↓
JT 파일: 5MB (95% 감소!)
     ↓
장점:
✓ 빠른 로딩
✓ CAD 없이 3D 뷰어 가능
✓ PMI (Product Manufacturing Information) 포함
✓ 협업 리뷰
```

### 🏆 5. Active Workspace Visualization

**브라우저에서 3D CAD 확인!**

```
CAD 설치 없이:
- 웹 브라우저 열기
- Teamcenter에 접속
- 3D 모델 즉시 확인
- 측정, 단면, 주석 가능
```

---

## 지원 CAD 시스템

### 1. Siemens NX (최고 수준 통합)

#### 통합 방식
```
NX → Teamcenter Integration (네이티브)
- NX 설치 시 Teamcenter 모듈 포함
- 별도 설치 거의 불필요
- Managed Mode 지원
```

#### 주요 기능
```c++
// NX ITK API 예제 (Teamcenter 연동)
#include <uf_ugmgr.h>

int save_to_teamcenter() {
    char* item_id = "PART-001";
    char* item_name = "Motor Housing";
    char* rev_id = "A";
    
    // NX Part를 Teamcenter Item으로 저장
    UF_UGMGR_create_item(item_id, item_name, rev_id);
    
    // BOM 자동 동기화
    UF_UGMGR_update_structure();
    
    return 0;
}
```

#### Managed Mode
```
Managed Mode ON:
- NX 파일이 Teamcenter에서만 열기/저장 가능
- 로컬 파일 시스템 접근 차단
- 완벽한 버전 제어
- 체크아웃/체크인 강제

Managed Mode OFF:
- 일반 파일 시스템 사용 (통합 없음)
```

### 2. CATIA V5/V6 통합

#### 통합 방식
```
CATIA V5/V6 Integrator for Teamcenter
- Official Dassault + Siemens 협력
- Add-in 형태로 설치
- VPM (CATIA 자체 PDM)과 병행 가능
```

#### BOM 매핑
```
CATIA Product Structure → Teamcenter BOM

CATIA:
Product (CATProduct)
├─ Part (CATPart)
└─ Part (CATPart)

자동 변환 ↓

Teamcenter:
Item (Product)
├─ ItemRevision (Part A)
└─ ItemRevision (Part B)
```

### 3. SolidWorks 통합

```
SolidWorks Integrator for Teamcenter
- Assembly → BOM 자동 생성
- 속성 자동 매핑
- Where-Used 추적
```

### 4. Creo (Pro/ENGINEER) 통합

```
Creo Integrator for Teamcenter
- Windchill에서 마이그레이션 시나리오 지원
- Family Table → Variants 매핑
```

---

## 통합 아키텍처

### Client-Side Integration

```
┌─────────────────────────────────────┐
│  CAD Application (NX, CATIA 등)     │
│  ┌───────────────────────────────┐  │
│  │  CAD Integrator (Add-in)      │  │
│  │  - File Operations            │  │
│  │  - BOM Extraction             │  │
│  │  - Property Mapping           │  │
│  └───────────────┬───────────────┘  │
└──────────────────┼───────────────────┘
                   │
            ┌──────▼──────┐
            │ ITK Client  │
            │  (C/C++)    │
            └──────┬──────┘
                   │
            ┌──────▼──────────────┐
            │ Teamcenter Server   │
            │  (Pool Manager)     │
            └─────────────────────┘
```

### Server-Side Processing

```
CAD 파일 업로드 시:

1. CAD → Teamcenter
   └─ CAD 원본 파일 (Dataset)

2. Teamcenter Server
   ├─ JT 변환 (Translator)
   ├─ BOM 추출
   ├─ 속성 추출
   └─ 관계 생성

3. 결과물
   ├─ Item/ItemRevision
   ├─ Dataset (CAD 파일)
   ├─ Dataset (JT 파일)
   └─ BOM Structure
```

---

## 개발 방법

### 1. CAD Integrator 커스터마이징

#### NX Customization (예시)

```c++
// NX Open API + Teamcenter ITK
#include <NXOpen/Session.hxx>
#include <tc/tc.h>
#include <tccore/item.h>

using namespace NXOpen;

extern "C" DllExport int custom_save_to_tc() {
    Session* session = Session::GetSession();
    Part* workPart = session->Parts()->Work();
    
    // 1. Part 정보 가져오기
    NXString partName = workPart->Name();
    
    // 2. Teamcenter Item 생성
    tag_t new_item = NULLTAG;
    tag_t new_rev = NULLTAG;
    
    ITEM_create_item(
        partName.GetUTF8Text(),
        "Auto-created from NX",
        "Part",
        "A",
        &new_item,
        &new_rev
    );
    
    // 3. 커스텀 속성 매핑
    NXString customProperty = workPart->GetStringAttribute("Material");
    AOM_set_value_string(new_rev, "material", 
                         customProperty.GetUTF8Text());
    
    // 4. 저장
    AOM_save(new_item);
    AOM_save(new_rev);
    
    return 0;
}
```

### 2. BOM 동기화 커스터마이징

```c++
// Assembly BOM 자동 동기화
int sync_cad_bom_to_tc() {
    Session* session = Session::GetSession();
    Part* rootPart = session->Parts()->Work();
    
    // 1. CAD Assembly 구조 탐색
    ComponentAssembly* rootCA = rootPart->ComponentAssembly();
    std::vector<Component*> components = rootCA->GetComponents();
    
    // 2. Teamcenter BOM Window 생성
    tag_t bom_window = NULLTAG;
    tag_t top_line = NULLTAG;
    tag_t root_rev = get_current_item_revision();
    
    BOM_create_window(&bom_window);
    BOM_set_window_top_line(bom_window, NULL, root_rev, 
                             NULLTAG, &top_line);
    
    // 3. CAD Component → BOM Line 매핑
    for(Component* comp : components) {
        Part* childPart = comp->Prototype();
        NXString childName = childPart->Name();
        
        // Teamcenter Item 찾기
        tag_t child_item = NULLTAG;
        ITEM_find_item(childName.GetUTF8Text(), &child_item);
        
        if(child_item != NULLTAG) {
            tag_t child_rev = NULLTAG;
            ITEM_ask_latest_rev(child_item, &child_rev);
            
            // BOM Line 추가
            tag_t new_bom_line = NULLTAG;
            BOM_line_add(top_line, child_rev, NULLTAG, &new_bom_line);
            
            // Quantity 설정
            double quantity = comp->GetQuantity();
            BOM_line_set_quantity(new_bom_line, quantity);
        }
    }
    
    // 4. 저장
    BOM_save_window(bom_window);
    BOM_close_window(bom_window);
    
    return 0;
}
```

### 3. 속성 매핑 커스터마이징

```c++
// CAD 속성 → Teamcenter 속성 매핑
struct PropertyMapping {
    const char* cad_property;
    const char* tc_property;
};

PropertyMapping mappings[] = {
    {"Material", "material"},
    {"Weight", "weight"},
    {"Supplier", "supplier"},
    {"Coating", "surface_finish"},
    {NULL, NULL}
};

int map_properties(Part* cadPart, tag_t tc_revision) {
    for(int i = 0; mappings[i].cad_property != NULL; i++) {
        // CAD에서 속성 읽기
        NXString value = cadPart->GetStringAttribute(
            mappings[i].cad_property);
        
        if(!value.IsEmpty()) {
            // Teamcenter에 쓰기
            AOM_set_value_string(tc_revision, 
                                 mappings[i].tc_property,
                                 value.GetUTF8Text());
        }
    }
    
    AOM_save(tc_revision);
    return 0;
}
```

### 4. Design in Context 지원

```c++
// 상위 Assembly 로드
int load_parent_assembly_context() {
    tag_t current_rev = get_current_item_revision();
    tag_t parent_item = NULLTAG;
    int n_parents = 0;
    tag_t* parents = NULL;
    
    // Where-Used 조회 (상위 Assembly)
    WSOM_where_used(current_rev, 1, &n_parents, &parents);
    
    if(n_parents > 0) {
        parent_item = parents[0];
        
        // 상위 Assembly CAD 파일 다운로드 및 로드
        load_cad_file_to_session(parent_item);
        
        // Child Part를 Context 내에서 편집
        enable_design_in_context_mode();
    }
    
    MEM_free(parents);
    return 0;
}
```

---

## ENOVIA와 비교

### 기능 비교표

| 기능 | Teamcenter | ENOVIA | 승자 |
|------|-----------|--------|------|
| **Siemens NX 통합** | 완벽 (네이티브) | 기본 | 🏆 TC |
| **CATIA 통합** | 우수 (공식 Integrator) | 완벽 (같은 회사) | 🏆 ENOVIA |
| **SolidWorks 통합** | 우수 | 우수 | 🤝 비슷 |
| **Multi-CAD 지원** | 매우 강력 | 강력 | 🏆 TC |
| **JT 경량화** | 기본 제공 | 별도 솔루션 | 🏆 TC |
| **3D Visualization** | Active Workspace | 3DPlay | 🤝 비슷 |
| **BOM 동기화** | 자동 + 양방향 | 자동 + 양방향 | 🤝 비슷 |
| **Design in Context** | 강력 | 강력 | 🤝 비슷 |
| **개발 난이도** | 어려움 (C/C++) | 중간 (Java) | 🏆 ENOVIA |
| **커스터마이징 깊이** | 매우 깊음 | 깊음 | 🏆 TC |
| **성능** | 우수 | 우수 | 🤝 비슷 |

### 아키텍처 비교

#### Teamcenter CAD Integration
```
장점:
✅ C/C++ 네이티브로 성능 우수
✅ CAD 깊숙이 통합 가능
✅ Multi-CAD 환경에 최적화
✅ JT 기본 지원 (경량화)
✅ 대규모 Assembly 처리 우수

단점:
❌ 개발 난이도 높음 (C/C++)
❌ 컴파일 필요
❌ 디버깅 어려움
❌ 배포 복잡함 (DLL/SO)
```

#### ENOVIA CAD Integration
```
장점:
✅ Java 기반 개발 (쉬움)
✅ CATIA와 완벽 통합 (같은 회사)
✅ 3DEXPERIENCE 플랫폼 통합
✅ 핫디플로이 가능
✅ 웹 기반 협업 강력

단점:
❌ NX 통합은 Teamcenter보다 약함
❌ Java 오버헤드
❌ Multi-CAD 지원이 TC보다 약간 약함
```

### 실무 시나리오별 비교

#### 시나리오 1: 자동차 회사 (Multi-CAD)
```
환경:
- 차체: CATIA V5
- 엔진: NX
- 전기: AutoCAD
- 시트: SolidWorks

권장: Teamcenter 🏆
이유: Multi-CAD 통합 우수, JT로 통일된 뷰어
```

#### 시나리오 2: 항공우주 (CATIA 중심)
```
환경:
- 주 CAD: CATIA V5/V6
- 일부: NX

권장: ENOVIA 또는 Teamcenter 🤝
이유: 둘 다 우수, 회사 전략에 따라 선택
```

#### 시나리오 3: 기계 제조 (NX 중심)
```
환경:
- 주 CAD: NX
- 일부: SolidWorks

권장: Teamcenter 🏆
이유: NX와의 네이티브 통합, Managed Mode
```

---

## 실무 예제

### 예제 1: NX Managed Mode 설정

#### 서버 측 설정
```bash
# Teamcenter Preferences
TC_NX_MANAGED_MODE=TRUE
TC_NX_DEFAULT_TEMPLATE=nx_part_template
TC_NX_AUTO_ASSIGN_ITEM_ID=TRUE
```

#### NX 클라이언트 설정
```
환경 변수:
UGII_TMP_DIR=C:\Temp
UGII_BASE_DIR=C:\Siemens\NX2312
TC_ROOT=C:\Siemens\Teamcenter13

실행:
NX → File → Open
→ Teamcenter 연결 자동
→ Teamcenter에서만 파일 열기/저장 가능
```

### 예제 2: CATIA V5 Assembly → Teamcenter BOM

```
CATIA Product:
Main_Assembly.CATProduct
├─ Part1.CATPart (Qty: 2)
├─ Part2.CATPart (Qty: 1)
└─ Sub_Assembly.CATProduct
    ├─ Part3.CATPart (Qty: 4)
    └─ Part4.CATPart (Qty: 1)

Save to Teamcenter:

Teamcenter BOM:
Main_Assembly (Item)
├─ Part1 (Qty: 2)
├─ Part2 (Qty: 1)
└─ Sub_Assembly
    ├─ Part3 (Qty: 4)
    └─ Part4 (Qty: 1)

자동 매핑!
```

### 예제 3: 커스텀 CAD 명령 추가

#### NX Ribbon 버튼 추가
```c++
// NX Open API
#include <NXOpen/MenuBar.hxx>

extern "C" DllExport void ufusr(char* param, int* retcod, int param_len) {
    Session* session = Session::GetSession();
    MenuBar* menuBar = session->UserInterface()->MenuBar();
    
    // "Save to Teamcenter" 버튼 추가
    menuBar->AddMenuAction("Save to TC", 
                          "Save Part to Teamcenter",
                          MenuBar::ActionType::PUSH_BUTTON,
                          callback_save_to_tc);
}

// Callback 함수
void callback_save_to_tc() {
    // Teamcenter에 저장 로직
    custom_save_to_tc();
}
```

### 예제 4: 대량 CAD 파일 Import

```c
// ITK로 대량 CAD 파일 일괄 Import
int batch_import_cad_files(char* directory) {
    int ifail = ITK_ok;
    
    // 디렉토리 내 모든 CAD 파일 찾기
    char** file_list = NULL;
    int n_files = 0;
    find_cad_files(directory, &n_files, &file_list);
    
    TC_write_syslog("Found %d CAD files\n", n_files);
    
    for(int i = 0; i < n_files; i++) {
        char* filename = file_list[i];
        
        // 1. Item 생성
        tag_t new_item = NULLTAG;
        tag_t new_rev = NULLTAG;
        
        char item_id[128];
        sprintf(item_id, "IMP-%05d", i + 1);
        
        ITEM_create_item(item_id, filename, "Part", "A",
                        &new_item, &new_rev);
        
        // 2. Dataset 생성 및 파일 첨부
        tag_t dataset = NULLTAG;
        tag_t dataset_type = NULLTAG;
        
        TCTYPE_find_type("UGMASTER", "Dataset", &dataset_type);
        DATASET_create_dataset(dataset_type, filename, 
                              "Imported CAD", "NX", &dataset);
        
        // 3. 파일 Import
        tag_t named_ref = NULLTAG;
        char full_path[512];
        sprintf(full_path, "%s\\%s", directory, filename);
        
        AE_create_dataset_uifile(dataset, "UGMASTER", 
                                 full_path, &named_ref);
        
        // 4. ItemRevision에 연결
        tag_t rel_type = NULLTAG;
        GRM_find_relation_type("IMAN_specification", &rel_type);
        GRM_create_relation(new_rev, dataset, rel_type, 
                           NULLTAG, NULLTAG);
        
        // 5. 저장
        AOM_save(new_item);
        AOM_save(new_rev);
        AOM_save(dataset);
        
        TC_write_syslog("Imported: %s → %s\n", filename, item_id);
        
        MEM_free(file_list[i]);
    }
    
    MEM_free(file_list);
    
    return ITK_ok;
}
```

---

## 장단점 분석

### Teamcenter CAD Integration

#### 🎯 장점

**1. 성능 및 확장성**
```
✅ C/C++ 네이티브 → 빠른 처리
✅ 대규모 Assembly (10,000+ 부품) 처리 우수
✅ 메모리 효율적
✅ JT 경량화 기술 (파일 크기 90% 감소)
```

**2. Multi-CAD 지원**
```
✅ 모든 주요 CAD 공식 지원
✅ 혼합 CAD 환경 최적화
✅ 통일된 뷰어 (JT Viewer)
✅ CAD 중립적 협업
```

**3. 깊이 있는 통합**
```
✅ CAD 내부 API 접근 (NX Open 등)
✅ 커스텀 명령/메뉴 추가 가능
✅ Workflow와 연동
✅ 설계 규칙 자동 검증
```

**4. 엔터프라이즈 기능**
```
✅ Design in Context (상위 Assembly 맥락)
✅ Inter-Part Linking (부품 간 참조)
✅ Revision 자동 관리
✅ Effectivity 지원 (Serial/Date)
```

**5. Siemens NX**
```
✅ 완벽한 네이티브 통합
✅ Managed Mode (강제 버전 관리)
✅ Synchronous Technology 지원
✅ Manufacturing 통합 (CAM)
```

#### ⚠️ 단점

**1. 개발 난이도**
```
❌ C/C++ 필수 → 진입 장벽 높음
❌ 포인터, 메모리 관리 복잡
❌ 컴파일 필요 (개발 사이클 느림)
❌ 디버깅 어려움
```

**2. 배포 및 유지보수**
```
❌ DLL/SO 배포 복잡
❌ 버전 호환성 관리 어려움
❌ 서버 재시작 필요
❌ 롤백 어려움
```

**3. 학습 곡선**
```
❌ ITK API 방대함 (200+ 모듈)
❌ CAD API 별도 학습 필요
❌ 공식 문서 방대 (읽기 어려움)
❌ 예제 부족
```

**4. CATIA 통합**
```
⚠️ Dassault 제품이라 ENOVIA보다는 약함
⚠️ VPM과의 호환성 이슈
```

### ENOVIA CAD Integration

#### 🎯 장점

**1. 개발 편의성**
```
✅ Java 기반 → 쉬운 개발
✅ JPO 핫디플로이 가능
✅ 예외 처리 간편
✅ IDE 지원 우수
```

**2. CATIA 통합**
```
✅ 같은 회사 (Dassault Systèmes)
✅ 완벽한 네이티브 통합
✅ VPM과 호환성
✅ 3DEXPERIENCE 플랫폼
```

**3. 웹 기반**
```
✅ 3DPlay 웹 뷰어
✅ 브라우저에서 협업
✅ 모바일 지원
```

#### ⚠️ 단점

**1. Multi-CAD**
```
⚠️ Teamcenter보다 약간 약함
⚠️ JT 지원 제한적
⚠️ NX 통합은 기본 수준
```

**2. 성능**
```
⚠️ Java 오버헤드
⚠️ 대규모 Assembly 처리 시 느릴 수 있음
```

**3. 커스터마이징 깊이**
```
⚠️ CAD 내부 API 접근 제한적
⚠️ Teamcenter만큼 깊은 통합 어려움
```

---

## 결론 및 추천

### 🎯 Teamcenter를 선택해야 하는 경우

```
✅ Multi-CAD 환경 (여러 CAD 혼용)
✅ Siemens NX 주 사용
✅ 대규모 Assembly (항공, 자동차)
✅ 제조업체 (Manufacturing 통합 필요)
✅ JT 경량화 필요
✅ 성능 최우선
✅ 깊은 커스터마이징 필요
✅ 엔터프라이즈급 확장성

대표 산업:
- 자동차 (현대, GM, Ford 등)
- 항공우주 (Boeing, Airbus)
- 중공업
- 전자제품 대기업
```

### 🎯 ENOVIA를 선택해야 하는 경우

```
✅ CATIA 주 사용
✅ 3DEXPERIENCE 플랫폼 활용
✅ 빠른 개발 필요 (Java)
✅ 웹 기반 협업 중시
✅ 모바일 지원 필요
✅ 중소규모 프로젝트

대표 산업:
- 항공우주 (CATIA 중심)
- 자동차 (Renault, PSA 등 Dassault 고객)
- 패션/소비재
```

### 📊 종합 평가

| 평가 항목 | Teamcenter | ENOVIA |
|----------|-----------|--------|
| **Multi-CAD 지원** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **NX 통합** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **CATIA 통합** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **개발 난이도** | ⭐⭐ (어려움) | ⭐⭐⭐⭐ (쉬움) |
| **성능** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **확장성** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **경량화 (JT)** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **커스터마이징 깊이** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

### 💡 개발자 관점 추천

**현재 ENOVIA 개발 경험이 있다면:**

1. **Teamcenter CAD Integration 도전 가치 있음!**
   ```
   이유:
   - 더 깊은 기술 역량 확보
   - C/C++ 경험 (커리어 강화)
   - 더 넓은 산업 적용 (자동차, 항공)
   - Multi-CAD 환경 경험
   ```

2. **하지만 학습 곡선 고려해야 함**
   ```
   준비 필요:
   - C/C++ 복습 (포인터, 메모리 관리)
   - ITK API 학습 (2-3개월)
   - CAD API 학습 (NX Open 등)
   - 컴파일/디버깅 환경 구축
   ```

3. **단계별 접근 추천**
   ```
   Step 1: ITK 기초 (1개월)
   Step 2: CAD Integration 개념 (1개월)
   Step 3: NX Open 등 CAD API (1개월)
   Step 4: 실전 프로젝트 (2-3개월)
   
   총 6개월 정도면 실무 가능!
   ```

---

## 학습 리소스

### Teamcenter CAD Integration 학습

```
1. 공식 문서
   - Teamcenter Integration for NX Guide
   - ITK CAD Integration API Reference
   - JT Open Toolkit Documentation

2. 교육
   - Siemens 공식 교육: CAD Integration Course
   - NX Open Programming Course

3. 커뮤니티
   - Siemens Community: CAD Integration Forum
   - NX User Forum

4. 샘플 코드
   - %TC_ROOT%\sample_extensions\cad_integration
```

---

> **결론**: Teamcenter CAD Integration은 **ENOVIA보다 개발 난이도는 높지만**, **더 강력하고 확장 가능한 기능**을 제공합니다. Multi-CAD 환경과 대규모 프로젝트에서는 Teamcenter가 우위입니다!

**교육에서 CAD Integration도 다룬다면 꼭 배워보세요! 🚀**
