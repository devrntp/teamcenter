# Teamcenter 공식 문서 구조 및 학습 로드맵

> **작성일**: 2025-11-26  
> **출처**: Siemens Teamcenter Documentation Portal  
> **목적**: Teamcenter 공식 문서의 체계적 학습 가이드

---

## 📋 개요

Teamcenter 공식 문서는 일반적으로 다음과 같은 대분류로 구성됩니다. 각 카테고리별로 주요 내용과 학습 순서를 정리했습니다.

---

## 주요 대분류 (Main Categories)

> **참고**: 실제 문서 페이지의 대분류를 확인하신 후, 해당 내용을 공유해주시면 더 정확하게 업데이트하겠습니다.

---

## 1. 시작하기 (Getting Started)

### 📖 주요 주제
- Teamcenter 개요 및 소개
- 핵심 개념 (Item, Revision, BOM, Workflow)
- 사용자 인터페이스 (RAC, Active Workspace)
- 기본 탐색 방법

### 🎯 학습 목표
- Teamcenter가 무엇인지 이해
- PLM의 기본 개념 파악
- UI 사용법 익히기

### 📝 주요 프로세스
```
1. Teamcenter 로그인
   └─ Username, Password, Group 선택
   
2. 홈 화면 탐색
   └─ My Teamcenter, Search, Create 메뉴
   
3. 기본 검색
   └─ Simple Search, Advanced Search
   
4. 객체 상세 보기
   └─ Summary, Properties, Relations
```

---

## 2. 설치 및 배포 (Installation & Deployment)

### 📖 주요 주제
- 시스템 요구사항
- 설치 계획 및 아키텍처
- Database 설정
- Server 구성요소 설치
- Client 배포
- 업그레이드 및 마이그레이션

### 🎯 학습 목표
- Teamcenter 설치 프로세스 이해
- 멀티 서버 구성 방법
- 성능 최적화를 위한 배포 전략

### 📝 주요 프로세스
```
Phase 1: 사전 준비
1. 하드웨어 요구사항 확인
2. OS 및 소프트웨어 준비
3. 네트워크 구성
4. Database 설치

Phase 2: Teamcenter Server 설치
5. TEM (Teamcenter Environment Manager) 실행
6. License Server 구성
7. Pool Manager 설치 및 구성
8. Volume Server 설정
9. DBConfig 실행 (Schema 초기화)

Phase 3: Web Tier 배포
10. Tomcat 설치
11. Active Workspace 배포
12. Gateway 구성

Phase 4: Client 배포
13. RAC 설치 패키지 생성
14. 사용자 PC에 배포
15. 접속 테스트

Phase 5: 검증
16. Database 연결 테스트
17. 기본 기능 테스트
18. 성능 벤치마크
```

---

## 3. 관리 (Administration)

### 📖 주요 주제
- 조직 모델 (Organization Model)
- 사용자 및 그룹 관리
- 액세스 제어 (Access Manager)
- Preference 관리
- 분류 관리 (Classification)
- 볼륨 관리
- 백업 및 복구

### 🎯 학습 목표
- 조직 구조 설정 방법
- 권한 관리 체계 이해
- 시스템 유지보수 방법

### 📝 주요 프로세스
```
1. Organization Model 구성
   1.1 Site 생성
   1.2 Organization 생성
   1.3 Group 생성
   1.4 Role 정의

2. 사용자 관리
   2.1 사용자 생성
   2.2 그룹 할당
   2.3 Role 할당
   2.4 라이선스 할당

3. Access Control
   3.1 ACL (Access Control List) 정의
   3.2 객체에 ACL 적용
   3.3 Named ACL 생성
   3.4 Conditional Access Rules

4. Preference 관리
   4.1 Site Preferences 설정
   4.2 User Preferences 관리
   4.3 환경 변수 구성

5. Classification 설정
   4.1 Classification Hierarchy 정의
   4.2 속성 정의
   4.3 부품에 Classification 적용

6. 백업 및 유지보수
   6.1 Database 백업
   6.2 Volume 백업
   6.3 로그 모니터링
   6.4 성능 튜닝
```

---

## 4. 데이터 모델링 (Data Modeling)

### 📖 주요 주제
- BMIDE (Business Modeler IDE)
- Business Objects 정의
- 속성 (Properties) 커스터마이징
- 관계 (Relations) 정의
- LOV (List of Values)
- Constants
- 템플릿 배포

### 🎯 학습 목표
- BMIDE 사용법
- 커스텀 데이터 모델 생성
- 템플릿 기반 배포

### 📝 주요 프로세스
```
1. BMIDE 프로젝트 생성
   1.1 Eclipse 기반 BMIDE 실행
   1.2 새 프로젝트 생성
   1.3 Teamcenter Server 연결

2. Business Object 정의
   2.1 새 Item Type 생성 (예: CustomPart)
   2.2 상속 관계 설정
   2.3 아이콘 설정

3. 속성 추가
   3.1 Property 정의 (String, Integer, Date 등)
   3.2 Form 생성 (속성 그룹)
   3.3 속성 표시 순서 설정

4. 관계 정의
   4.1 새 Relation Type 생성
   4.2 Primary/Secondary Object Type 지정
   4.3 Cardinality 설정

5. LOV 생성
   5.1 List of Values 정의
   5.2 값 추가 (Static/Dynamic)
   5.3 속성에 LOV 연결

6. 템플릿 생성 및 배포
   6.1 템플릿 빌드
   6.2 template.xml 생성
   6.3 Server에 배포
   6.4 template_install 실행
   6.5 서버 재시작
```

---

## 5. 개발 (Development)

### 📖 주요 주제
- ITK (Integration Toolkit)
- SOA (Service Oriented Architecture)
- RAC 커스터마이징
- Active Workspace 커스터마이징
- User Exits
- Workflow Handlers
- Web Services 통합

### 🎯 학습 목표
- ITK C/C++ 프로그래밍
- SOA 서비스 개발
- 커스텀 로직 구현

### 📝 주요 프로세스
```
Phase 1: 개발 환경 구축
1. ITK 개발 환경 설정
   1.1 Visual Studio / GCC 설치
   1.2 TC_ROOT, TC_DATA 환경 변수 설정
   1.3 Include Path, Library Path 설정
   
2. 첫 번째 ITK 프로그램 작성
   2.1 기본 템플릿 작성
   2.2 컴파일 및 링크
   2.3 테스트 실행

Phase 2: ITK 핵심 기능 개발
3. 객체 접근 및 조작
   3.1 AOM API 사용 (속성 읽기/쓰기)
   3.2 ITEM API 사용 (Item 생성/조회)
   3.3 WSOM API 사용 (객체 검색)

4. BOM 관리
   4.1 BOM Window 생성
   4.2 BOM Line 조회 및 수정
   4.3 BOM 구조 순회

5. Query 및 검색
   5.1 Saved Query 실행
   5.2 POM Query 사용

Phase 3: User Exit 및 Handler
6. User Exit 작성
   6.1 Method Handler (객체 생성/수정 시)
   6.2 Validation Logic
   6.3 DLL/SO 컴파일 및 배포

7. Workflow Handler
   7.1 Action Handler (Task 실행)
   7.2 Rule Handler (분기 조건)
   7.3 EPM API 사용

Phase 4: SOA 개발
8. SOA 서비스 생성
   8.1 Java/C++ SOA Client
   8.2 원격 서비스 호출
   8.3 Custom Service 작성

Phase 5: UI 커스터마이징
9. RAC 커스터마이징
   9.1 커스텀 메뉴/버튼 추가
   9.2 ITK와 연동

10. Active Workspace 커스터마이징
    10.1 Angular 기반 커스터마이징
    10.2 View Model, View 작성
    10.3 Command Handler
```

---

## 6. BOM 관리 (Bill of Materials)

### 📖 주요 주제
- BOM 구조 생성 및 관리
- BOM View
- Variant Configuration
- Effectivity 관리
- Manufacturing BOM (MBOM)
- Engineering BOM (EBOM)

### 🎯 학습 목표
- BOM 구조 이해
- Effectivity 활용
- EBOM → MBOM 전환

### 📝 주요 프로세스
```
1. BOM 구조 생성
   1.1 Top Assembly Item 생성
   1.2 Component Items 생성
   1.3 BOM Line 추가 (부모-자식 관계)
   1.4 Quantity 설정

2. BOM View 관리
   2.1 View 생성 (Design, Manufacturing, Service 등)
   2.2 View에 따라 다른 구조 구성
   2.3 View 전환

3. Variant Configuration
   3.1 Variant Rule 정의
   3.2 Option Family 생성
   3.3 Variant Option 설정
   3.4 Configuration 적용

4. Effectivity 설정
   4.1 Serial Effectivity (일련번호 기준)
      └─ "Serial 1001~5000: 구 부품"
      └─ "Serial 5001~UP: 신 부품"
   
   4.2 Date Effectivity (날짜 기준)
      └─ "2025-01-01부터 적용"
   
   4.3 Unit Effectivity (단위 기준)
      └─ "Lot 50부터 적용"

5. EBOM → MBOM 전환
   5.1 EBOM 완성
   5.2 Manufacturing View 생성
   5.3 공정별 BOM 구조 재구성
   5.4 Assembly Sequence 설정
```

---

## 7. Change Management

### 📖 주요 주제
- Problem Report
- ECR (Engineering Change Request)
- ECO (Engineering Change Order)
- ECN (Engineering Change Notice)
- Impact Analysis
- Change Workflow
- Configuration Management

### 🎯 학습 목표
- 변경 관리 프로세스 이해
- ECR/ECO 사용법
- Impact Analysis 활용

### 📝 주요 프로세스
```
Phase 1: 문제 식별
1. Problem Report 생성
   1.1 문제 발견 및 기록
   1.2 심각도 설정 (Critical, High, Medium, Low)
   1.3 Problem Item 연결
   1.4 담당자 지정

Phase 2: 변경 요청
2. ECR 생성
   2.1 Problem Report 참조
   2.2 변경 사유 작성
   2.3 제안 솔루션 설명
   2.4 비용 분석

3. ECR 검토 Workflow
   3.1 기술 검토 (Technical Review)
   3.2 비용 검토 (Cost Review)
   3.3 리스크 평가
   3.4 승인/반려

Phase 3: 변경 실행
4. ECO 생성 (ECR 승인 후)
   4.1 ECR 참조
   4.2 Problem Items → Solution Items
   4.3 새 Revision 생성
   4.4 설계 변경 작업

5. Impact Analysis
   5.1 Where-Used 분석 (상위 조립품)
   5.2 Document Impact (관련 문서)
   5.3 BOM Impact (하위 부품)
   5.4 Manufacturing Impact (공정 변경)
   5.5 Project Impact (일정 영향)

6. Effectivity 설정
   6.1 변경 적용 시점 결정
   6.2 Serial/Date Effectivity 설정
   6.3 구 버전 Obsolete 처리

7. ECO 검토 및 승인
   7.1 변경 사항 검증
   7.2 승인 Workflow
   7.3 Release

Phase 4: 변경 통지
8. ECN 생성 및 배포
   8.1 ECO 완료 후 ECN 생성
   8.2 이해관계자 통보
      - 제조팀
      - 구매팀
      - 품질팀
      - 서비스팀
   8.3 변경 사항 문서 배포
```

---

## 8. Workflow 및 프로세스 관리

### 📖 주요 주제
- Workflow Designer
- Process Template 생성
- Task 정의
- Handler 작성
- Routing Rules
- Notification 설정

### 🎯 학습 목표
- Workflow Designer 사용법
- 커스텀 Workflow 생성
- Handler 개발 및 통합

### 📝 주요 프로세스
```
1. Workflow Template 생성
   1.1 Workflow Designer 실행
   1.2 새 Process Template 생성
   1.3 프로세스 이름 설정

2. Task 정의
   2.1 Start Task 추가
   2.2 Review Task 추가
   2.3 Approval Task 추가
   2.4 Decision Task 추가
   2.5 End Task 추가

3. Task 속성 설정
   3.1 Assignee (담당자) 설정
      - User
      - Group
      - Role
      - Dynamic (Query 기반)
   
   3.2 Actions 정의
      - Approve
      - Reject
      - Rework
      - Hold
   
   3.3 Deadline 설정
      - Duration
      - Reminder

4. Handler 연결
   4.1 Pre-Action Handler
      └─ Task 시작 전 검증
   
   4.2 Post-Action Handler
      └─ Task 완료 후 처리
   
   4.3 Rule Handler
      └─ 분기 조건 평가

5. Path 및 Flow 설정
   5.1 Success Path
   5.2 Failure Path
   5.3 Conditional Branch

6. Template 배포
   6.1 템플릿 저장
   6.2 BMIDE로 export
   6.3 Server에 배포

7. Workflow 실행
   7.1 객체에 Workflow 시작
   7.2 Task 할당 확인
   7.3 프로세스 모니터링
   7.4 완료 확인
```

---

## 9. Visualization 및 협업

### 📖 주요 주제
- Visualization (JT, 3D Viewer)
- Markup 및 PMI
- Redlining
- Live Collaboration
- Digital Mockup

### 🎯 학습 목표
- 3D 모델 시각화
- 설계 검토 도구 활용
- 협업 기능 사용

### 📝 주요 프로세스
```
1. Visualization 설정
   1.1 JT 파일 생성 (CAD 변환)
   1.2 Teamcenter에 업로드
   1.3 3D Viewer 실행

2. 설계 검토
   2.1 3D 모델 로드
   2.2 단면도 (Section) 생성
   2.3 측정 (Measure)
   2.4 주석 (Annotation) 추가

3. Markup 및 Redlining
   3.1 변경 사항 표시
   3.2 주석 달기
   3.3 스냅샷 저장
   3.4 검토자에게 공유

4. Live Collaboration
   4.1 세션 시작
   4.2 참여자 초대
   4.3 실시간 화면 공유
   4.4 동시 주석 작업
```

---

## 10. CAD 통합 (CAD Integration)

### 📖 주요 주제
- NX Integration
- CATIA Integration
- SolidWorks Integration
- Creo Integration
- CAD 파일 관리
- Design in Context

### 🎯 학습 목표
- CAD 도구와 Teamcenter 통합
- CAD 파일 버전 관리
- Design in Context 활용

### 📝 주요 프로세스
```
1. CAD Integration 설정
   1.1 CAD Integrator 설치
   1.2 Teamcenter 연결 설정
   1.3 환경 변수 구성

2. CAD에서 부품 생성
   2.1 NX/CATIA에서 모델링
   2.2 "Save to Teamcenter" 실행
   2.3 Item/Revision 정보 입력
   2.4 Dataset 자동 생성

3. CAD 파일 체크아웃/체크인
   3.1 Assembly 로드
   3.2 Component 체크아웃
   3.3 수정 작업
   3.4 체크인 (새 버전 생성)

4. Design in Context
   4.1 상위 Assembly 로드
   4.2 Context 내에서 Component 수정
   4.3 간섭 체크 (Interference Check)
   4.4 변경사항 저장

5. BOM 동기화
   5.1 CAD Assembly 구조 → Teamcenter BOM
   5.2 자동 BOM 생성
   5.3 속성 매핑
```

---

## 11. 보고서 및 분석 (Reporting & Analytics)

### 📖 주요 주제
- Query Builder
- Advanced Search
- Reports
- Dashboards
- Business Intelligence

### 🎯 학습 목표
- 효과적인 검색 방법
- 커스텀 리포트 생성
- 데이터 분석

### 📝 주요 프로세스
```
1. Query 생성
   1.1 Query Builder 실행
   1.2 Object Type 선택
   1.3 조건 추가 (Criteria)
   1.4 결과 컬럼 선택
   1.5 Saved Query로 저장

2. Advanced Search
   2.1 검색 조건 설정
   2.2 와일드카드 사용
   2.3 날짜 범위 검색
   2.4 결과 필터링

3. Report 생성
   3.1 리포트 템플릿 선택
   3.2 데이터 소스 지정
   3.3 레이아웃 설정
   3.4 스케줄 설정 (자동 생성)

4. Dashboard 구성
   4.1 위젯 추가
   4.2 실시간 데이터 연결
   4.3 차트/그래프 설정
```

---

## 12. 마이그레이션 및 통합 (Migration & Integration)

### 📖 주요 주제
- 데이터 마이그레이션
- Legacy 시스템 통합
- ERP 연동 (SAP, Oracle EBS)
- Excel Import/Export
- Web Services

### 🎯 학습 목표
- 기존 데이터 이관 방법
- 외부 시스템 통합
- 대량 데이터 처리

### 📝 주요 프로세스
```
1. 데이터 마이그레이션 계획
   1.1 Source 시스템 분석
   1.2 데이터 매핑 정의
   1.3 템플릿 생성

2. Excel Import
   2.1 Excel 템플릿 다운로드
   2.2 데이터 입력
   2.3 Import 실행
   2.4 검증

3. ERP 통합
   3.1 BOM → ERP 동기화
   3.2 부품 정보 전송
   3.3 재고 정보 수신

4. Web Services 통합
   4.1 SOAP/REST API 사용
   4.2 인증 설정
   4.3 데이터 교환
```

---

## 학습 로드맵 (추천 순서)

### 입문자 (1-2주)
```
주차 1: 기본 개념 및 사용법
□ 1. 시작하기 → Teamcenter 개요
□ 6. BOM 관리 → BOM 구조 이해
□ 11. 보고서 및 분석 → 기본 검색

주차 2: 관리 및 프로세스
□ 3. 관리 → 사용자 및 권한
□ 7. Change Management → ECR/ECO 개념
□ 8. Workflow → 기본 Workflow 사용
```

### 개발자 (2-4주)
```
주차 1-2: 개발 환경 및 데이터 모델
□ 2. 설치 및 배포 → 개발 환경 구축
□ 4. 데이터 모델링 → BMIDE 사용법
□ 5. 개발 → ITK 기초

주차 3-4: 심화 개발
□ 5. 개발 → User Exit, Handler
□ 5. 개발 → SOA 서비스
□ 8. Workflow → Handler 개발
```

### 시스템 관리자 (1-2주)
```
주차 1: 시스템 구축
□ 2. 설치 및 배포 → 전체 설치 프로세스
□ 3. 관리 → Organization Model
□ 3. 관리 → 백업 및 유지보수

주차 2: 고급 관리
□ 3. 관리 → Performance Tuning
□ 4. 데이터 모델링 → 템플릿 배포
□ 12. 마이그레이션 및 통합
```

---

## 교육 준비 체크리스트

### 다음 주 교육 전 학습 추천
- [x] 1. 시작하기 - Teamcenter 개요
- [x] 5. 개발 - ITK 기초 개념
- [x] 7. Change Management - ECR/ECO 이해
- [ ] 4. 데이터 모델링 - BMIDE 개념
- [ ] 8. Workflow - Process Template 이해

### 교육 중 집중 항목
- ITK 개발 실습 (C/C++)
- BMIDE 템플릿 생성
- User Exit 작성
- Workflow Handler 개발

---

## 참고 사항

### 실제 페이지 구조 확인 필요
페이지 접근이 로그인 필요로 제한되어 있어, 실제 대분류 구조를 확인하지 못했습니다. 

**확인하신 대분류를 알려주시면:**
1. 정확한 카테고리명으로 업데이트
2. 각 카테고리별 하위 항목 상세화
3. 실제 문서 링크 추가

가능하시다면 상단의 대분류(Main Categories) 목록을 공유해주세요!

---

> **마지막 업데이트**: 2025-11-26  
> **다음 단계**: 실제 문서 페이지의 대분류 확인 후 업데이트 예정

**교육 준비 파이팅! 🚀**
