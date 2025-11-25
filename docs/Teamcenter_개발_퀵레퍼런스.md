# Teamcenter 개발 퀵 레퍼런스 가이드

> **작성일**: 2025-11-25  
> **목적**: Teamcenter ITK 개발 교육 준비 및 실무 참고용

---

## 📋 목차

1. [핵심 개념](#핵심-개념)
2. [ITK 기본 구조](#itk-기본-구조)
3. [주요 ITK API 모듈](#주요-itk-api-모듈)
4. [데이터 모델](#데이터-모델)
5. [개발 패턴](#개발-패턴)
6. [디버깅 & 로깅](#디버깅--로깅)
7. [자주 사용하는 코드 스니펫](#자주-사용하는-코드-스니펫)

---

## 핵심 개념

### Tag
```c
tag_t object_tag = NULLTAG;  // 객체 참조 핸들 (정수형)
```
- Teamcenter 객체를 참조하는 고유 식별자
- `NULLTAG` = 초기화되지 않은 또는 유효하지 않은 태그
- Java의 객체 참조와 유사하지만 정수형

### 리턴 코드 (Return Code)
```c
int ifail = ITK_ok;  // 성공 시 ITK_ok (0)
```
- 모든 ITK 함수는 `int` 타입 리턴 코드 반환
- `ITK_ok` (0) = 성공
- 0이 아닌 값 = 에러 코드

### 메모리 관리
```c
char* value = NULL;
AOM_ask_value_string(tag, "property", &value);
// ... 사용 ...
MEM_free(value);  // 필수!
```
- ITK API가 할당한 메모리는 **`MEM_free()`로 반드시 해제**
- 해제하지 않으면 메모리 누수 발생

---

## ITK 기본 구조

### 기본 템플릿

```c
// 필수 헤더
#include <tc/tc.h>
#include <tccore/item.h>
#include <tccore/aom.h>
#include <pom/pom/pom.h>

// 에러 체크 매크로 (권장)
#define ERROR_CHECK(code) \
    if(code != ITK_ok) { \
        char* err_msg = NULL; \
        EMH_ask_error_text(code, &err_msg); \
        TC_write_syslog("ERROR: %s\n", err_msg); \
        MEM_free(err_msg); \
        return code; \
    }

// 메인 함수
extern int ITK_user_main(int argc, char* argv[]) {
    int ifail = ITK_ok;
    
    // 작업 수행
    
    return ITK_ok;
}

// User Exit (커스텀 이벤트 핸들러)
extern int USER_gs_creation(METHOD_message_t* msg, va_list args) {
    int ifail = ITK_ok;
    tag_t object_tag = va_arg(args, tag_t);
    
    // 객체 생성 시 실행되는 로직
    
    return ITK_ok;
}
```

### Exit 함수 시그니처

```c
// Method 핸들러
extern int METHOD_NAME(METHOD_message_t* msg, va_list args);

// Action 핸들러  
extern int ACTION_NAME(EPM_action_message_t msg);

// Rule 핸들러
extern int RULE_NAME(EPM_rule_message_t msg);
```

---

## 주요 ITK API 모듈

### 1. AOM (Application Object Model) - 범용 속성 접근

#### 속성 읽기
```c
// 문자열 속성
char* value = NULL;
ifail = AOM_ask_value_string(object_tag, "object_name", &value);
ERROR_CHECK(ifail);
printf("Name: %s\n", value);
MEM_free(value);

// 정수 속성
int int_value;
ifail = AOM_ask_value_int(object_tag, "some_int_property", &int_value);

// 날짜 속성
date_t date_value;
ifail = AOM_ask_value_date(object_tag, "creation_date", &date_value);

// 논리 속성
logical boolean_value;
ifail = AOM_ask_value_logical(object_tag, "is_released", &boolean_value);

// 태그 속성 (참조)
tag_t ref_tag = NULLTAG;
ifail = AOM_ask_value_tag(object_tag, "owning_user", &ref_tag);
```

#### 속성 쓰기
```c
// 문자열 설정
ifail = AOM_set_value_string(object_tag, "object_desc", "New Description");
ERROR_CHECK(ifail);

// 정수 설정
ifail = AOM_set_value_int(object_tag, "quantity", 100);

// 저장 (중요!)
ifail = AOM_save(object_tag);
ERROR_CHECK(ifail);

// 새로고침 (서버에서 최신 데이터 가져오기)
ifail = AOM_refresh(object_tag, TRUE);
```

#### 속성 정보 조회
```c
// 모든 속성 이름 가져오기
int count;
char** prop_names = NULL;
ifail = AOM_ask_prop_names(object_tag, &count, &prop_names);

for(int i = 0; i < count; i++) {
    printf("Property: %s\n", prop_names[i]);
}
MEM_free(prop_names);
```

---

### 2. ITEM - Item 관리

#### Item 찾기
```c
tag_t item_tag = NULLTAG;

// Item ID로 찾기
ifail = ITEM_find_item("ITEM-001234", &item_tag);
ERROR_CHECK(ifail);

// Item ID와 Revision으로 찾기
tag_t item_rev_tag = NULLTAG;
ifail = ITEM_find_revision("ITEM-001234", "A", &item_tag, &item_rev_tag);
```

#### Item 생성
```c
tag_t new_item = NULLTAG;
tag_t new_rev = NULLTAG;
tag_t item_type = NULLTAG;
tag_t rev_type = NULLTAG;

// Item Type 가져오기
ifail = TCTYPE_find_type("Item", "Item", &item_type);
ifail = TCTYPE_find_type("ItemRevision", "Item Revision", &rev_type);

// Item 생성
ifail = ITEM_create_item(
    "NEW-ITEM-001",           // Item ID
    "New Item Name",          // Item Name
    "Item",                   // Item Type
    "A",                      // Revision ID
    &new_item,                // 생성된 Item Tag
    &new_rev                  // 생성된 Revision Tag
);
ERROR_CHECK(ifail);

// Unit of Measure 설정 (옵션)
ifail = ITEM_set_uom(new_item, "Each");

// 저장
ifail = AOM_save(new_item);
ifail = AOM_save(new_rev);
```

#### Item Revision 조회
```c
int n_revs;
tag_t* revisions = NULL;

// Item의 모든 Revision 가져오기
ifail = ITEM_list_all_revs(item_tag, &n_revs, &revisions);

for(int i = 0; i < n_revs; i++) {
    char* rev_id = NULL;
    AOM_ask_value_string(revisions[i], "item_revision_id", &rev_id);
    printf("Revision: %s\n", rev_id);
    MEM_free(rev_id);
}

MEM_free(revisions);
```

---

### 3. WSOM (Workspace Object Model) - 객체 검색

#### 객체 ID로 찾기
```c
tag_t object_tag = NULLTAG;

// UID로 찾기 (가장 일반적)
ifail = WSOM_ask_object_id2("AbCdEfG12345", &object_tag);
ERROR_CHECK(ifail);

// Type + Name으로 찾기
ifail = WSOM_where_is_named_item("Item", "ITEM-001", &object_tag);
```

#### 객체 타입 확인
```c
char* type_name = NULL;
ifail = WSOM_ask_object_type2(object_tag, &type_name);
printf("Type: %s\n", type_name);
MEM_free(type_name);

// 특정 타입인지 체크
logical is_item;
ifail = WSOM_is_type_of(object_tag, "Item", &is_item);
if(is_item) {
    printf("This is an Item\n");
}
```

---

### 4. GRM (Generic Relationship Manager) - 관계 관리

#### 관계 생성
```c
tag_t relation_type = NULLTAG;
tag_t relation_tag = NULLTAG;

// 관계 타입 찾기
ifail = GRM_find_relation_type("IMAN_specification", &relation_type);

// 관계 생성 (Primary -> Secondary)
ifail = GRM_create_relation(
    primary_tag,      // Primary 객체
    secondary_tag,    // Secondary 객체  
    relation_type,    // 관계 타입
    NULLTAG,          // User data
    &relation_tag     // 생성된 관계 Tag
);
ERROR_CHECK(ifail);

ifail = GRM_save_relation(relation_tag);
```

#### 관계 조회
```c
int n_secondary = 0;
tag_t* secondary_objects = NULL;

// Primary -> Secondary 방향 조회
ifail = GRM_list_secondary_objects_only(
    primary_tag,
    relation_type,
    &n_secondary,
    &secondary_objects
);

for(int i = 0; i < n_secondary; i++) {
    char* name = NULL;
    AOM_ask_value_string(secondary_objects[i], "object_name", &name);
    printf("Related object: %s\n", name);
    MEM_free(name);
}

MEM_free(secondary_objects);

// Secondary -> Primary 방향 조회
int n_primary = 0;
tag_t* primary_objects = NULL;
ifail = GRM_list_primary_objects_only(
    secondary_tag,
    relation_type,
    &n_primary,
    &primary_objects
);
MEM_free(primary_objects);
```

#### 관계 삭제
```c
// 관계 찾기
tag_t* relations = NULL;
int n_relations = 0;

ifail = GRM_find_relations(
    primary_tag,
    secondary_tag,
    relation_type,
    &n_relations,
    &relations
);

// 삭제
for(int i = 0; i < n_relations; i++) {
    ifail = GRM_delete_relation(relations[i]);
}

MEM_free(relations);
```

---

### 5. QRY - 쿼리

#### Saved Query 실행
```c
tag_t query_tag = NULLTAG;
int n_results = 0;
tag_t* results = NULL;

// Saved Query 찾기
ifail = QRY_find("General...", &query_tag);
ERROR_CHECK(ifail);

// 쿼리 파라미터 설정
char* entries[] = {"Type", "Name"};
char* values[] = {"Item", "*PART*"};

// 실행
ifail = QRY_execute(
    query_tag,
    2,              // 파라미터 개수
    entries,
    values,
    &n_results,
    &results
);

printf("Found %d items\n", n_results);

// 결과 처리
for(int i = 0; i < n_results; i++) {
    char* item_id = NULL;
    AOM_ask_value_string(results[i], "item_id", &item_id);
    printf("Item: %s\n", item_id);
    MEM_free(item_id);
}

MEM_free(results);
```

#### POM 쿼리 (저수준)
```c
char* select_attrs[] = {"puid", "item_id", "object_name"};
void*** values = NULL;
int n_objects = 0;

// 쿼리 실행
ifail = POM_enquiry_select_expr(
    "Item",                          // 클래스 이름
    select_attrs,                    // 선택할 속성
    3,                               // 속성 개수
    "object_name LIKE '*PART*'",    // WHERE 절
    "",                              // ORDER BY
    &n_objects,
    &values
);

// 결과 처리
for(int i = 0; i < n_objects; i++) {
    printf("Item ID: %s, Name: %s\n", 
           (char*)values[i][1], 
           (char*)values[i][2]);
}

// 메모리 해제
MEM_free(values);
```

---

### 6. SA (Security Access) - 권한 관리

#### 권한 체크
```c
int access_bits = 0;

// 읽기/쓰기 권한 확인
ifail = SA_ask_privilege2(object_tag, &access_bits);

// 비트 마스크로 체크
if(access_bits & SA_READ) {
    printf("Read access granted\n");
}

if(access_bits & SA_WRITE) {
    printf("Write access granted\n");
}

if(access_bits & SA_DELETE) {
    printf("Delete access granted\n");
}

// 특정 권한 체크
logical has_write = FALSE;
ifail = SA_is_privileged(object_tag, SA_WRITE, &has_write);
```

#### ACL (Access Control List) 설정
```c
tag_t acl_tag = NULLTAG;

// ACL 찾기
ifail = SA_find_acl("My_ACL", &acl_tag);

// 객체에 ACL 적용
ifail = AOM_set_value_tag(object_tag, "acl", acl_tag);
ifail = AOM_save(object_tag);
```

---

### 7. TCTYPE - 타입 관리

#### 타입 정보 조회
```c
tag_t type_tag = NULLTAG;
char* type_name = NULL;

// 타입 찾기
ifail = TCTYPE_find_type("Item", "Item", &type_tag);

// 타입 이름 가져오기
ifail = TCTYPE_ask_name2(type_tag, &type_name);
printf("Type: %s\n", type_name);
MEM_free(type_name);

// 상속 여부 체크
logical is_subtype = FALSE;
tag_t parent_type = NULLTAG;

ifail = TCTYPE_find_type("Part", "Item", &parent_type);
ifail = TCTYPE_is_type_of(type_tag, parent_type, &is_subtype);
```

#### 속성 정보 조회
```c
int n_props = 0;
tag_t* prop_tags = NULL;

// 타입의 모든 속성 가져오기
ifail = TCTYPE_ask_properties(type_tag, &n_props, &prop_tags);

for(int i = 0; i < n_props; i++) {
    char* prop_name = NULL;
    int prop_type = 0;
    
    ifail = TCTYPE_ask_prop_name(prop_tags[i], &prop_name);
    ifail = TCTYPE_ask_prop_type(prop_tags[i], &prop_type);
    
    printf("Property: %s (Type: %d)\n", prop_name, prop_type);
    MEM_free(prop_name);
}

MEM_free(prop_tags);
```

---

### 8. DATASET - 파일 관리

#### Dataset 생성
```c
tag_t dataset_tag = NULLTAG;
tag_t dataset_type = NULLTAG;

// Dataset Type 찾기
ifail = TCTYPE_find_type("MSExcelX", "Dataset", &dataset_type);

// Dataset 생성
ifail = DATASET_create_dataset(
    dataset_type,
    "MyDataset",
    "Dataset Description",
    "",                    // Tool name
    &dataset_tag
);
ERROR_CHECK(ifail);

// ItemRevision에 연결
tag_t relation_type = NULLTAG;
ifail = GRM_find_relation_type("IMAN_specification", &relation_type);
ifail = GRM_create_relation(item_rev_tag, dataset_tag, relation_type, 
                            NULLTAG, NULLTAG);

// 저장
ifail = AOM_save(dataset_tag);
```

#### 파일 업로드
```c
tag_t tool_tag = NULLTAG;
tag_t named_ref_tag = NULLTAG;

// Named Reference Type 가져오기
ifail = AOM_ask_value_tag(dataset_tag, "ref_list", &tool_tag);

// 파일 업로드
ifail = IMF_import_file(
    "C:\\temp\\myfile.xlsx",    // 로컬 파일 경로
    SS_TEXT,                     // Transfer mode
    &tool_tag                    // Named reference
);

// Dataset에 Named Reference 추가
ifail = AE_create_named_reference(
    dataset_tag,
    "Excel",                     // Reference name
    tool_tag,
    &named_ref_tag
);

ifail = AOM_save(dataset_tag);
```

#### 파일 다운로드
```c
char** file_names = NULL;
int n_files = 0;

// Dataset의 파일 가져오기
ifail = IMF_ask_dataset_files(dataset_tag, &n_files, &file_names);

for(int i = 0; i < n_files; i++) {
    printf("File: %s\n", file_names[i]);
    
    // 다운로드
    ifail = IMF_export_file(
        dataset_tag,
        file_names[i],
        "C:\\temp\\downloaded_file.xlsx",
        SS_TEXT
    );
    
    MEM_free(file_names[i]);
}

MEM_free(file_names);
```

---

### 9. BOM (Bill of Materials) - 구조 관리

#### BOM Line 조회
```c
tag_t bom_window = NULLTAG;
tag_t top_line = NULLTAG;
int n_lines = 0;
tag_t* lines = NULL;

// BOM Window 생성
ifail = BOM_create_window(&bom_window);
ifail = BOM_set_window_top_line(bom_window, NULL, item_rev_tag, NULLTAG, &top_line);

// 자식 라인 가져오기
ifail = BOM_line_ask_child_lines(top_line, &n_lines, &lines);

for(int i = 0; i < n_lines; i++) {
    tag_t child_item = NULLTAG;
    char* item_id = NULL;
    double quantity = 0.0;
    
    // BOM Line에서 Item 가져오기
    ifail = BOM_line_ask_child_item_revision(lines[i], &child_item);
    ifail = AOM_ask_value_string(child_item, "item_id", &item_id);
    
    // Quantity 조회
    ifail = BOM_line_ask_quantity(lines[i], &quantity);
    
    printf("Child: %s, Qty: %.2f\n", item_id, quantity);
    MEM_free(item_id);
}

MEM_free(lines);

// BOM Window 닫기
ifail = BOM_close_window(bom_window);
```

#### BOM Line 생성
```c
tag_t new_line = NULLTAG;

// 자식 추가
ifail = BOM_line_add(
    parent_line,        // 부모 BOM Line
    child_rev_tag,      // 자식 ItemRevision
    NULLTAG,            // Occurrence type
    &new_line           // 생성된 BOM Line
);

// Quantity 설정
ifail = BOM_line_set_quantity(new_line, 5.0);

// 저장
ifail = BOM_save_window(bom_window);
```

---

### 10. PREFERENCES - 환경설정

#### Preference 읽기
```c
int n_values = 0;
char** values = NULL;

// User Preference 읽기
ifail = PREF_ask_char_values("TC_default_item_type", &n_values, &values);

if(n_values > 0) {
    printf("Default Item Type: %s\n", values[0]);
}

for(int i = 0; i < n_values; i++) {
    MEM_free(values[i]);
}
MEM_free(values);

// Site Preference 읽기
ifail = PREF_ask_site_char_values("IMAN_volume_name", &n_values, &values);
```

#### Preference 설정
```c
char* new_values[] = {"NewValue"};

// User Preference 설정
ifail = PREF_set_char_values("MY_PREFERENCE", 1, new_values);
```

---

### 11. USER & GROUP - 사용자/그룹 관리

#### 현재 사용자 조회
```c
tag_t current_user = NULLTAG;
tag_t current_group = NULLTAG;
char* user_name = NULL;
char* group_name = NULL;

// 로그인 사용자
ifail = SA_ask_user_login(&current_user);
ifail = AOM_ask_value_string(current_user, "user_id", &user_name);
printf("Current User: %s\n", user_name);
MEM_free(user_name);

// 현재 그룹
ifail = SA_ask_user_current_group(current_user, &current_group);
ifail = AOM_ask_value_string(current_group, "name", &group_name);
printf("Current Group: %s\n", group_name);
MEM_free(group_name);
```

#### 특정 사용자 찾기
```c
tag_t user_tag = NULLTAG;

// User ID로 찾기
ifail = SA_find_user("john_doe", &user_tag);

// 이메일 조회
char* email = NULL;
ifail = AOM_ask_value_string(user_tag, "email_address", &email);
MEM_free(email);
```

---

### 12. LOV (List of Values) - 드롭다운 값

#### LOV 값 조회
```c
tag_t lov_tag = NULLTAG;
int n_values = 0;
tag_t* lov_values = NULL;

// LOV 찾기
ifail = LOV_find("Status_LOV", &lov_tag);

// LOV 값 가져오기
ifail = LOV_ask_values(lov_tag, &n_values, &lov_values);

for(int i = 0; i < n_values; i++) {
    char* value_name = NULL;
    char* display_name = NULL;
    
    ifail = AOM_ask_value_string(lov_values[i], "lov_value_name", &value_name);
    ifail = AOM_ask_value_string(lov_values[i], "lov_value_desc", &display_name);
    
    printf("Value: %s (%s)\n", value_name, display_name);
    
    MEM_free(value_name);
    MEM_free(display_name);
}

MEM_free(lov_values);
```

---

## 데이터 모델

### Item 계층 구조

```
Item (고유 ID)
  ├─ item_id (문자열)
  ├─ object_name (문자열)
  └─ ItemRevision[] (1:N)
       ├─ item_revision_id (문자열, "A", "B", "C")
       ├─ object_desc (문자열)
       ├─ release_status_list (문자열)
       ├─ Dataset[] (파일)
       │    ├─ Named Reference
       │    └─ ImanFile (실제 파일)
       ├─ Form[] (속성 그룹)
       └─ BOMLine[] (구조)
```

### 주요 관계 (Relation Types)

| Relation Type | Primary | Secondary | 설명 |
|---------------|---------|-----------|------|
| `IMAN_specification` | ItemRevision | Dataset | 파일 첨부 |
| `IMAN_reference` | Any | Any | 범용 참조 |
| `IMAN_manifestation` | ItemRevision | Form | 속성 그룹 |
| `TC_Attaches` | Any | Any | 첨부 파일 |
| `contents` | Folder | Any | 폴더 포함 |

---

## 개발 패턴

### 1. 에러 처리 패턴

```c
// 패턴 1: 매크로 사용 (권장)
#define ERROR_CHECK(code) \
    if((code) != ITK_ok) { \
        char* err_msg = NULL; \
        EMH_ask_error_text(code, &err_msg); \
        TC_write_syslog("Error at %s:%d - %s\n", __FILE__, __LINE__, err_msg); \
        MEM_free(err_msg); \
        return code; \
    }

int my_function() {
    int ifail;
    
    ifail = ITEM_find_item("ITEM-001", &item_tag);
    ERROR_CHECK(ifail);
    
    return ITK_ok;
}

// 패턴 2: Goto 패턴 (복잡한 정리 작업 필요 시)
int my_function() {
    int ifail = ITK_ok;
    char* buffer = NULL;
    tag_t* results = NULL;
    
    buffer = (char*)MEM_alloc(1024);
    
    ifail = some_function();
    if(ifail != ITK_ok) goto cleanup;
    
    ifail = another_function();
    if(ifail != ITK_ok) goto cleanup;
    
cleanup:
    if(buffer) MEM_free(buffer);
    if(results) MEM_free(results);
    
    return ifail;
}
```

### 2. NULL 체크 패턴

```c
tag_t object_tag = NULLTAG;
char* value = NULL;

// 1. Tag 체크
if(object_tag != NULLTAG) {
    // 안전하게 사용
}

// 2. 포인터 체크
if(value != NULL && strlen(value) > 0) {
    // 안전하게 사용
}

// 3. 배열 체크
tag_t* array = NULL;
int count = 0;

get_objects(&count, &array);

if(count > 0 && array != NULL) {
    for(int i = 0; i < count; i++) {
        // 사용
    }
    MEM_free(array);
}
```

### 3. 메모리 관리 패턴

```c
void example_function() {
    char* str1 = NULL;
    char* str2 = NULL;
    tag_t* tags = NULL;
    
    // 할당
    AOM_ask_value_string(obj, "prop1", &str1);
    AOM_ask_value_string(obj, "prop2", &str2);
    ITEM_list_all_revs(item, &count, &tags);
    
    // 사용
    printf("%s %s\n", str1, str2);
    
    // 해제 (역순 권장하지만 필수는 아님)
    if(tags) MEM_free(tags);
    if(str2) MEM_free(str2);
    if(str1) MEM_free(str1);
}
```

### 4. 트랜잭션 패턴

```c
int modify_objects() {
    int ifail = ITK_ok;
    
    // 여러 객체 수정 시작
    tag_t objects[] = {obj1, obj2, obj3};
    
    for(int i = 0; i < 3; i++) {
        ifail = AOM_set_value_string(objects[i], "status", "Modified");
        if(ifail != ITK_ok) {
            // 실패 시 rollback은 자동으로 됨 (저장 안 됨)
            return ifail;
        }
    }
    
    // 모두 성공 시 일괄 저장
    for(int i = 0; i < 3; i++) {
        ifail = AOM_save(objects[i]);
        ERROR_CHECK(ifail);
    }
    
    return ITK_ok;
}
```

---

## 디버깅 & 로깅

### 로깅 방법

```c
// 1. Syslog (서버 로그)
TC_write_syslog("Message: %s, Value: %d\n", str, value);

// 2. Printf (콘솔 - 디버깅 시에만)
printf("Debug: object_tag = %u\n", object_tag);

// 3. 조건부 로그
#ifdef DEBUG_MODE
    TC_write_syslog("DEBUG: Entering function %s\n", __FUNCTION__);
#endif

// 4. 에러 메시지 출력
char* err_msg = NULL;
EMH_ask_error_text(ifail, &err_msg);
TC_write_syslog("ERROR: %s\n", err_msg);
MEM_free(err_msg);
```

### 디버깅 팁

```c
// 객체 정보 덤프 함수
void dump_object_info(tag_t object_tag) {
    if(object_tag == NULLTAG) {
        TC_write_syslog("Object is NULLTAG\n");
        return;
    }
    
    char* type = NULL;
    char* name = NULL;
    char* uid = NULL;
    
    WSOM_ask_object_type2(object_tag, &type);
    AOM_ask_value_string(object_tag, "object_name", &name);
    POM_tag_to_uid(object_tag, &uid);
    
    TC_write_syslog("=== Object Info ===\n");
    TC_write_syslog("Type: %s\n", type ? type : "N/A");
    TC_write_syslog("Name: %s\n", name ? name : "N/A");
    TC_write_syslog("UID: %s\n", uid ? uid : "N/A");
    TC_write_syslog("Tag: %u\n", object_tag);
    
    MEM_free(type);
    MEM_free(name);
    MEM_free(uid);
}
```

---

## 자주 사용하는 코드 스니펫

### 1. Item 찾고 속성 수정

```c
int update_item_description(char* item_id, char* new_desc) {
    int ifail = ITK_ok;
    tag_t item_tag = NULLTAG;
    tag_t item_rev = NULLTAG;
    
    // Item 찾기
    ifail = ITEM_find_item(item_id, &item_tag);
    ERROR_CHECK(ifail);
    
    // Latest Revision 가져오기
    int n_revs = 0;
    tag_t* revisions = NULL;
    ifail = ITEM_list_all_revs(item_tag, &n_revs, &revisions);
    ERROR_CHECK(ifail);
    
    if(n_revs > 0) {
        item_rev = revisions[n_revs - 1];  // 마지막 revision
        
        // 속성 수정
        ifail = AOM_set_value_string(item_rev, "object_desc", new_desc);
        ERROR_CHECK(ifail);
        
        // 저장
        ifail = AOM_save(item_rev);
        ERROR_CHECK(ifail);
    }
    
    MEM_free(revisions);
    return ITK_ok;
}
```

### 2. 모든 Dataset 파일 다운로드

```c
int download_all_datasets(tag_t item_rev, char* target_dir) {
    int ifail = ITK_ok;
    tag_t rel_type = NULLTAG;
    int n_datasets = 0;
    tag_t* datasets = NULL;
    
    // IMAN_specification 관계 찾기
    ifail = GRM_find_relation_type("IMAN_specification", &rel_type);
    ERROR_CHECK(ifail);
    
    // Dataset들 가져오기
    ifail = GRM_list_secondary_objects_only(
        item_rev, rel_type, &n_datasets, &datasets);
    ERROR_CHECK(ifail);
    
    // 각 Dataset 처리
    for(int i = 0; i < n_datasets; i++) {
        char** file_names = NULL;
        int n_files = 0;
        
        ifail = IMF_ask_dataset_files(datasets[i], &n_files, &file_names);
        
        for(int j = 0; j < n_files; j++) {
            char target_path[512];
            sprintf(target_path, "%s\\%s", target_dir, file_names[j]);
            
            TC_write_syslog("Downloading: %s\n", file_names[j]);
            
            ifail = IMF_export_file(
                datasets[i], file_names[j], target_path, SS_TEXT);
            
            MEM_free(file_names[j]);
        }
        
        MEM_free(file_names);
    }
    
    MEM_free(datasets);
    return ITK_ok;
}
```

### 3. BOM 전체 순회 (재귀)

```c
void traverse_bom(tag_t bom_line, int level) {
    int ifail;
    tag_t item_rev = NULLTAG;
    char* item_id = NULL;
    char* name = NULL;
    double qty = 0.0;
    
    // 현재 라인 정보
    ifail = BOM_line_ask_child_item_revision(bom_line, &item_rev);
    if(ifail == ITK_ok && item_rev != NULLTAG) {
        AOM_ask_value_string(item_rev, "item_id", &item_id);
        AOM_ask_value_string(item_rev, "object_name", &name);
        BOM_line_ask_quantity(bom_line, &qty);
        
        // 들여쓰기 출력
        for(int i = 0; i < level; i++) printf("  ");
        printf("- %s (%s) x %.2f\n", item_id, name, qty);
        
        MEM_free(item_id);
        MEM_free(name);
    }
    
    // 자식 라인 순회
    int n_children = 0;
    tag_t* children = NULL;
    
    ifail = BOM_line_ask_child_lines(bom_line, &n_children, &children);
    
    for(int i = 0; i < n_children; i++) {
        traverse_bom(children[i], level + 1);
    }
    
    MEM_free(children);
}

// 사용 예
tag_t window = NULLTAG;
tag_t top_line = NULLTAG;

BOM_create_window(&window);
BOM_set_window_top_line(window, NULL, item_rev, NULLTAG, &top_line);

traverse_bom(top_line, 0);

BOM_close_window(window);
```

### 4. User Exit: 객체 생성 전 검증

```c
extern int USER_gs_creation(METHOD_message_t* msg, va_list args) {
    int ifail = ITK_ok;
    tag_t object_tag = va_arg(args, tag_t);
    
    char* type_name = NULL;
    char* object_name = NULL;
    
    // 타입 체크
    WSOM_ask_object_type2(object_tag, &type_name);
    
    if(strcmp(type_name, "Item") == 0) {
        // Item 이름 규칙 체크
        AOM_ask_value_string(object_tag, "object_name", &object_name);
        
        if(strlen(object_name) < 5) {
            TC_write_syslog("ERROR: Item name must be at least 5 characters\n");
            
            MEM_free(type_name);
            MEM_free(object_name);
            
            // 생성 거부
            return ITK_error;
        }
        
        MEM_free(object_name);
    }
    
    MEM_free(type_name);
    return ITK_ok;
}
```

### 5. Workflow Handler: 승인 자동화

```c
extern int EPM_auto_approve(EPM_action_message_t msg) {
    int ifail = ITK_ok;
    int n_attachments = 0;
    tag_t* attachments = NULL;
    
    // Task의 첨부 객체 가져오기
    ifail = EPM_ask_attachments(
        msg.task, EPM_target_attachment, &n_attachments, &attachments);
    
    for(int i = 0; i < n_attachments; i++) {
        char* status = NULL;
        
        // Release Status 확인
        ifail = AOM_ask_value_string(
            attachments[i], "release_status_list", &status);
        
        if(status && strcmp(status, "Approved") == 0) {
            TC_write_syslog("Object already approved, auto-completing task\n");
            
            // Task 자동 완료
            ifail = EPM_complete_action(msg.task, EPM_completed);
            
            MEM_free(status);
            MEM_free(attachments);
            return ITK_ok;
        }
        
        MEM_free(status);
    }
    
    MEM_free(attachments);
    
    // 수동 처리 필요
    return ITK_ok;
}
```

---

## 컴파일 & 배포

### Visual Studio (Windows)

```bat
REM 환경 변수 설정
set TC_ROOT=C:\Siemens\Teamcenter13
set TC_DATA=%TC_ROOT%\data

REM 컴파일
cl /I%TC_ROOT%\include /c my_itk.c
link /DLL /OUT:my_itk.dll my_itk.obj %TC_ROOT%\lib\*.lib

REM 배포
copy my_itk.dll %TC_ROOT%\bin\
```

### GCC (Linux)

```bash
# 환경 변수
export TC_ROOT=/opt/siemens/teamcenter
export TC_DATA=$TC_ROOT/data

# 컴파일
gcc -I$TC_ROOT/include -fPIC -c my_itk.c
gcc -shared -o my_itk.so my_itk.o -L$TC_ROOT/lib -ltc

# 배포
cp my_itk.so $TC_ROOT/bin/
```

---

## 주요 환경 변수

| 변수 | 설명 |
|------|------|
| `TC_ROOT` | Teamcenter 설치 경로 |
| `TC_DATA` | 데이터 디렉토리 |
| `USER_EXITS_DIR` | User Exit DLL/SO 경로 |
| `BMIDE_HOME` | BMIDE 설치 경로 |

---

## 추가 학습 자료

### 공식 문서 (교육 시 제공)
- ITK Programmer's Guide
- ITK API Reference
- BMIDE User Guide
- SOA Developer's Guide

### 온라인 리소스
- Siemens GTAC Support Portal
- Teamcenter Community Forums
- PLM World Conference Papers

---

## 체크리스트

개발 전 확인사항:
- [ ] 모든 포인터 NULL로 초기화
- [ ] 모든 tag NULLTAG로 초기화
- [ ] 리턴 코드 체크
- [ ] MEM_free() 호출
- [ ] AOM_save() 호출 (수정 시)
- [ ] 에러 로깅
- [ ] BMIDE 템플릿 배포
- [ ] 서버 재시작

---

> **마지막 업데이트**: 2025-11-25  
> **다음 학습**: SOA 서비스 개발, BMIDE 템플릿 생성

**교육 화이팅! 🚀**
