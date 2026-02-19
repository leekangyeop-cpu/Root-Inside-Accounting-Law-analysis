# Root-Inside-Accounting-Law-analysis
# Root Inside — AI-Native 회계·법률 자문 리포트 파이프라인
### Version 5.0 | Accounting & Law Advisory Report Pipeline
### Last Updated: 2026-02-19

> **본 문서는 Claude Project의 커스텀 지침서(Project Knowledge)로 사용하거나,**
> **AI 에이전트가 파일시스템에서 읽고 즉시 실행할 수 있는 완전 자동화 지침서입니다.**
> 새 프로젝트에서 이 README + 원본 자료만 제공하면, AI가 파일 트리 생성 → 원문 추출 → 구조화 → 검증 → 초안 리포트까지 **원스텝으로 완성**합니다.

---

## 📋 목차

1. [시스템 개요](#1-시스템-개요)
2. [적용 범위: 회계·법률 도메인 정의](#2-적용-범위-회계법률-도메인-정의)
3. [보안 및 격리 규칙](#3-보안-및-격리-규칙)
4. [프로젝트 디렉토리 구조](#4-프로젝트-디렉토리-구조)
5. [에이전트 절대 준수 규칙](#5-에이전트-절대-준수-규칙)
6. [PHASE 1: 프로젝트 초기화](#6-phase-1-프로젝트-초기화)
7. [PHASE 2: 원문 추출 및 Markdown 변환](#7-phase-2-원문-추출-및-markdown-변환)
8. [PHASE 3: 구조화 데이터 생성 (Semi-DB)](#8-phase-3-구조화-데이터-생성-semi-db)
9. [PHASE 4: 무결성 검증 (QA)](#9-phase-4-무결성-검증-qa)
10. [PHASE 5: 초안 리포트 자동 생성](#10-phase-5-초안-리포트-자동-생성)
11. [PHASE 6: 최종 윤문](#11-phase-6-최종-윤문)
12. [원스텝 실행 프롬프트](#12-원스텝-실행-프롬프트)
13. [데이터베이스 스키마 상세](#13-데이터베이스-스키마-상세)
14. [도메인별 참조 체계: 회계 기준·법령 인용 규칙](#14-도메인별-참조-체계-회계-기준법령-인용-규칙)
15. [참조 사례](#15-참조-사례)

---

## 1. 시스템 개요

이 파이프라인은 **루트인사이드(Root Inside)** 회계·법률 자문 리포트의 전체 1단계 공정을 담당하는 **AI 네이티브 파이프라인**입니다.

| 구성 요소 | 역할 | 도구 |
|-----------|------|------|
| **AI Agent (VS Code / Claude Code)** | 데이터 파서 — 원문에서 구조화 데이터 추출 | VS Code + AI Agent 또는 Claude Code CLI |
| **Python Scripts** | 자동 검증(QA) + 초안 렌더링 | `ops/run_validate.py`, `ops/run_report.py` |
| **Claude Desktop / Claude Project** | 수석 자문역 — 최종 리포트 윤문, 법률·회계 전문용어 검수 | Claude Desktop Project |

**핵심 원칙:**

| 원칙 | 설명 |
|------|------|
| **환각 제로 (Zero Hallucination)** | 원문에 없는 내용은 절대 생성하지 않는다 |
| **기준 준거 (Standards-Based)** | 모든 회계 판단은 K-IFRS/K-GAAP, 법률 판단은 해당 법령·판례에 근거한다 |
| **출처 완전 추적 (Full Traceability)** | 모든 주장은 법령 조항·회계 기준서·판례·원본 문서까지 역추적 가능해야 한다 |
| **전문가 검수 전제 (Human-in-the-Loop)** | AI 산출물은 반드시 공인회계사(CPA) 또는 변호사의 최종 검수를 거친다 |

---

## 2. 적용 범위: 회계·법률 도메인 정의

### 2.1 회계 (Accounting) 도메인

| 서비스 유형 | 주요 원본 자료 | 적용 기준 |
|-------------|---------------|-----------|
| **재무제표 분석** | 감사보고서, 재무제표, 주석, 내부회계관리제도 보고서 | K-IFRS, K-GAAP, 외감법 |
| **세무 자문** | 세금계산서, 부가세 신고서, 법인세 신고서, 세무조정계산서 | 법인세법, 소득세법, 부가가치세법, 조세특례제한법 |
| **회계 감사 지원** | 시산표, 계정별원장, 증빙서류, 내부통제 평가서 | 회계감사기준(KSA), ISA |
| **재무 실사 (FDD)** | 실사보고서, Quality of Earnings 분석, 운전자본 분석 | FDD 관행, IFRS 3 (사업결합) |
| **내부회계관리** | 내부회계관리제도 운영보고서, 통제활동 매트릭스 | 내부회계관리제도 모범규준 |

### 2.2 법률 (Law) 도메인

| 서비스 유형 | 주요 원본 자료 | 적용 기준 |
|-------------|---------------|-----------|
| **법률 실사 (LDD)** | 계약서, 등기부등본, 소송현황, 인허가 목록 | 상법, 민법, 각 산업별 특별법 |
| **규제 준수 (Compliance)** | 내부규정, 준법감시 보고서, 윤리경영 현황 | 공정거래법, 자본시장법, 개인정보보호법 |
| **계약 분석** | 주요 계약서, 약관, MOU, LOI, SPA/SHA | 민법(계약법), 상법, 약관규제법 |
| **소송/분쟁 지원** | 판결문, 소장/답변서, 감정서, 조정/중재 기록 | 민사소송법, 중재법, 해당 실체법 |
| **기업 구조조정** | 회생계획안, 채권자 목록, 법원 결정문 | 채무자 회생 및 파산에 관한 법률 |
| **지식재산권** | 특허/상표/디자인 등록원부, 라이선스 계약 | 특허법, 상표법, 저작권법 |

### 2.3 회계·법률 복합 서비스

| 서비스 유형 | 설명 |
|-------------|------|
| **M&A 자문** | FDD + LDD + Valuation + SPA 검토 |
| **IPO 지원** | 회계 감사 + 증권신고서 법률 검토 + 상장적격성 평가 |
| **기업 지배구조** | 이사회 운영 법률 검토 + 특수관계자 거래 회계 분석 |
| **조세 쟁송** | 세무 분석 + 행정소송/조세심판 법률 지원 |

---

## 3. 보안 및 격리 규칙

1. **Private Repository 강제:** 클라이언트의 재무정보, 소송정보, 계약내용 등 민감한 법률·회계 정보가 포함되므로 반드시 **Private(비공개)** 상태를 유지한다.
2. **비밀유지 원칙:** 변호사-의뢰인 비밀유지특권(Attorney-Client Privilege) 및 공인회계사 비밀유지의무 범위에 해당할 수 있는 자료를 취급하므로, 외부 유출을 엄격히 금지한다.
3. **저장소 분리 원칙:** 본 저장소는 Template으로만 유지하고, 실제 프로젝트는 클라이언트별로 Private Repository를 Clone하여 작업한다.
4. **원본 파일 보호:** `00_sources/` 내 원본 파일은 절대 수정·삭제하지 않는다. 추출 결과만 별도 파일로 생성한다.
5. **개인정보 최소화:** 원본에 포함된 개인정보(주민등록번호, 계좌번호 등)는 구조화 데이터 추출 시 마스킹 처리한다 (예: `***-**-*****`).

---

## 4. 프로젝트 디렉토리 구조

AI 에이전트는 새 프로젝트 시작 시 반드시 아래 구조를 **그대로** 생성해야 한다.

```
Root-Inside-AL-Report/
│
├── README.md                          ← 본 파일 (AI 지침서 겸 스키마 정의)
├── LICENSE
│
├── ops/                               ← 자동화 스크립트
│   ├── run_validate.py                ← QA 검증 스크립트
│   └── run_report.py                  ← 초안 리포트 렌더링 스크립트
│
└── reports/
    └── {client_name}_{year}/          ← 클라이언트별 프로젝트 폴더
        │
        ├── 00_sources/                ← [INPUT] 원본 자료 + 추출된 텍스트
        │   ├── (원본파일).docx/.pdf/.xlsx/.hwp
        │   ├── {파일명}_extracted.txt
        │   └── ...
        │
        ├── 01_structured/             ← [SEMI-DB] 구조화된 JSON/CSV 데이터
        │   ├── sources.json           ← 출처 문서 목록 (메타데이터)
        │   ├── references.json        ← 개별 참조/인용 목록 (법령·기준서·판례·URL)
        │   ├── claims.json            ← 핵심 주장/판단 목록
        │   ├── evidences.json         ← 근거/인용문 목록 (출처 추적 포함)
        │   ├── metrics.json           ← 정량 지표 목록 (재무수치, 세액 등)
        │   ├── legal_refs.json        ← 법령·판례·기준서 참조 테이블
        │   ├── risks.json             ← 리스크/이슈 사항 목록
        │   ├── claim_evidence.csv     ← 주장↔근거 매핑 테이블
        │   └── claim_risk.csv         ← 주장↔리스크 매핑 테이블
        │
        ├── 02_checks/                 ← [QA] 무결성 검증 결과
        │   └── validation_report.json
        │
        └── 03_report/                 ← [OUTPUT] 최종 산출물
            └── report_draft.md        ← AI가 생성한 초안 리포트
```

**명명 규칙:**
- 클라이언트 폴더: `{영문소문자회사명}_{서비스연도}` (예: `hanjin_2026`, `samsung_2025`)
- 추출 텍스트: `{원본파일의_식별가능한_이름}_extracted.txt`
- 프로젝트 유형이 복합인 경우 폴더 내 `project_type.txt`에 유형 명시 (예: `MnA_FDD_LDD`, `IPO`, `TAX_ADVISORY`)

---

## 5. 에이전트 절대 준수 규칙

> **AI 에이전트는 작업 시작 전, 자신의 역할을 '감정 없고 정확한 데이터 파서(Data Parser)'로 엄격히 제한하라.**
> **회계·법률 분야는 오류가 법적 책임으로 이어질 수 있으므로, 일반 컨설팅보다 더 엄격한 기준을 적용한다.**

### 5.1 창작 및 추론 절대 금지

- `00_sources/` 원문에 **없는** 내용, 숫자, 법적 판단, 회계 해석을 절대 지어내지 마라.
- 원문의 수치를 임의로 반올림하거나 단위를 변환하지 마라.
- 법령 조항 번호, 판례 번호, 회계 기준서 번호를 추측하지 마라. 원문에 명시된 것만 기록하라.
- "~로 판단된다", "~에 해당할 여지가 있다", "~의 위험이 있다" 등의 법적/회계적 판단을 에이전트가 자체 생성하지 마라.
- 원문이 이미 그러한 판단을 포함하고 있다면, 그 판단을 **원문 인용으로서** 추출하라 (에이전트의 판단이 아님을 명시).

### 5.2 선택적 추출 (No Noise)

- 원문 전체를 복사하는 것이 아니다. 원문 전체 보관은 `00_sources/`에서 충족한다.
- **'최종 리포트 결론에 사용될 핵심 데이터와 근거'**만을 `01_structured/`에 구조화하여 추출하라.
- 추출 기준:

**회계 도메인 추출 기준:**

| # | 추출 대상 | 예시 |
|---|-----------|------|
| 1 | 주요 재무지표 | 매출액, 영업이익, 당기순이익, 부채비율, 유동비율, ROE, EBITDA |
| 2 | 회계 처리 쟁점 | 수익인식 시점, 충당부채 설정 적정성, 특수관계자 거래 공정성 |
| 3 | 감사 의견 및 특기사항 | 적정/한정/부적정/의견거절, 강조사항, 계속기업 불확실성 |
| 4 | 세무 쟁점 | 손금산입/불산입, 이전가격, 세액공제 적용, 부당행위계산부인 |
| 5 | 내부통제 평가 결과 | 유효/비유효, 중요한 취약점, 미비점 |
| 6 | 재무 비율 추세 | 연도별 비교, 동업계 비교, 벤치마크 대비 |

**법률 도메인 추출 기준:**

| # | 추출 대상 | 예시 |
|---|-----------|------|
| 1 | 법적 리스크 사항 | 계류 소송, 잠재 소송, 규제 위반 가능성 |
| 2 | 계약 핵심 조건 | 계약 기간, 해지 조건, 손해배상 한도, 경업금지, Change of Control 조항 |
| 3 | 인허가·등록 현황 | 보유 인허가 목록, 갱신 기한, 결격 사유 해당 여부 |
| 4 | 규제 준수 현황 | 법령별 준수/미준수 사항, 시정 명령 이행 현황 |
| 5 | 지배구조 이슈 | 이사회 구성 적법성, 특수관계자 거래 승인 절차, 소수주주권 |
| 6 | 판례·유권해석 | 쟁점별 관련 판례, 행정해석, 유권해석 |

### 5.3 스키마 강제 (Schema Strictness)

- 섹션 13에 정의된 JSON/CSV 스키마를 **단 하나의 필드라도** 임의로 변경하거나 누락해서는 안 된다.
- 스키마에 정의되지 않은 필드를 임의로 추가하지 마라 (단, `description`, `note` 등 보조 필드는 예외).

### 5.4 출처 추적 의무화 (Citation-Enforced)

- 모든 Evidence(근거)는 반드시 `ref_ids`를 통해 개별 참조(Reference)에 연결되어야 한다.
- **회계 근거**는 해당 K-IFRS/K-GAAP 기준서 또는 법령 조항에, **법률 근거**는 해당 법령 조문·판례에 연결되어야 한다.
- 모든 Evidence는 `citation` 필드에 사람이 읽을 수 있는 인용 표기를 포함해야 한다.
- 출처 없는 근거는 **에러**로 처리된다.

### 5.5 법령·기준서 인용 정확성 (Legal/Accounting Citation Accuracy)

- 법령 인용 시: **법률명 + 조·항·호** 형식을 준수 (예: `법인세법 제19조 제1항`, `상법 제382조의4 제1항`)
- 판례 인용 시: **법원명 + 선고일 + 사건번호** 형식 준수 (예: `대법원 2023.5.18. 선고 2022다12345 판결`)
- 회계 기준서 인용 시: **기준서 번호 + 문단 번호** 형식 준수 (예: `K-IFRS 제1115호 문단 35`, `K-GAAP 일반기업회계기준 제16장 제16.3`)
- 원문에 인용이 불완전하게 기재된 경우, 원문 그대로 기록하되 `incomplete_citation: true` 플래그를 추가하라.

---

## 6. PHASE 1: 프로젝트 초기화

### AI에게 주는 지시:

```
이 README를 읽고, reports/ 아래에 "{client_name}_{year}" 폴더와 하위 디렉토리 구조
(00_sources, 01_structured, 02_checks, 03_report)를 생성해줘.
프로젝트 유형은 {project_type}이야.
```

### 에이전트 행동:

1. `reports/{client_name}_{year}/` 폴더 생성
2. 하위 4개 디렉토리 생성: `00_sources/`, `01_structured/`, `02_checks/`, `03_report/`
3. `01_structured/` 내 빈 JSON/CSV 파일 스캐폴딩
4. `project_type.txt` 파일에 프로젝트 유형 기록

### 스캐폴딩 초기 파일 내용:

```json
// sources.json, claims.json, evidences.json, metrics.json, references.json, legal_refs.json, risks.json
[]
```

```csv
// claim_evidence.csv
claim_id,evidence_id
```

```csv
// claim_risk.csv
claim_id,risk_id
```

---

## 7. PHASE 2: 원문 추출 및 Markdown 변환

### 전제 조건:

인간이 `00_sources/`에 원본 자료 파일(`.docx`, `.pdf`, `.xlsx`, `.hwp`, `.txt` 등)을 투입한 상태.

### AI에게 주는 지시:

```
00_sources/ 안의 모든 원본 파일을 읽고, 각 파일의 전체 내용을 Markdown 형식의
텍스트 파일로 변환하여 같은 폴더에 "{파일명}_extracted.txt"로 저장해줘.
원본의 모든 수치, 표, 출처 번호, 법령 조항, 판례 번호를 빠짐없이 보존해야 해.
```

### 에이전트 행동:

1. `00_sources/` 내 모든 원본 파일을 순회하며 읽기
2. 각 파일의 내용을 Markdown 형식으로 정리:
   - 제목/소제목 → `#`, `##`, `###` 헤딩
   - 숫자 데이터가 포함된 표 → Markdown 테이블 (`| col1 | col2 |`)
   - 원문의 출처 번호(각주) → 그대로 보존 (예: `[1]`, `[REF-01]`)
   - 법령 조문 번호 → 그대로 보존 (예: `법인세법 제19조 제2항`)
   - 판례 번호 → 그대로 보존 (예: `대법원 2023.5.18. 선고 2022다12345`)
   - 회계 기준서 인용 → 그대로 보존 (예: `K-IFRS 제1115호 문단 35`)
   - 강조 텍스트 → `**볼드**`, `*이탤릭*`
3. `{파일명}_extracted.txt` 파일로 저장
4. 추출 완료 후 파일 목록과 각 파일의 분량(줄 수)을 보고

### 변환 규칙:

| 원본 요소 | Markdown 변환 |
|-----------|---------------|
| 문서 제목 | `# 제목` |
| 섹션 제목 | `## 섹션명`, `### 하위섹션명` |
| 데이터 표 (재무제표, 세무조정표 등) | Markdown 테이블 (파이프 구분) — 열 정렬 유지 |
| 각주/출처 번호 | 원문 그대로 보존 |
| 법령 조문 | 원문 그대로 (축약/의역 금지) |
| 판례 인용 | 원문 그대로 (사건번호, 선고일 보존) |
| 회계 기준서 | 원문 그대로 (기준서 번호, 문단 번호 보존) |
| 수치 데이터 | 원문 그대로 (반올림/단위변환 금지) |
| 볼드/강조 | `**텍스트**` |
| 목록 | `- 항목` 또는 `1. 항목` |
| 개인정보 | 마스킹 처리 후 보존 (`[개인정보 마스킹]` 표기) |

---

## 8. PHASE 3: 구조화 데이터 생성 (Semi-DB)

### AI에게 주는 지시:

```
00_sources/의 _extracted.txt 파일들을 모두 읽고, README 섹션 13의 스키마에 맞춰
01_structured/ 내부의 JSON 파일들과 CSV를 채워줘.
모든 근거에는 반드시 개별 출처(ref_ids)를 연결하고, citation 필드를 작성해야 해.
법령·기준서 인용은 legal_refs.json에도 등록해야 해.
리스크 사항은 risks.json에 추출하고, 관련 주장에 매핑해줘.
```

### 에이전트 행동 (순서 엄수):

#### Step 1: `sources.json` 생성

- 각 원본 파일(감사보고서, 실사보고서, 법률의견서 등)을 하나의 Source로 등록
- ID 형식: `SRC-01`, `SRC-02`, ...
- `domain` 필드에 `accounting`, `legal`, 또는 `mixed` 기입
- 해당 Source에서 파생되는 개별 참조(REF)의 ID 목록을 `ref_ids`에 기록

#### Step 2: `references.json` 생성

- Source 내에서 인용된 **개별 출처**(법령, 판례, 기준서, 논문, 정부통계, 보도자료 등)를 각각 Reference로 등록
- ID 형식: `REF-01`, `REF-02`, ... 또는 `REF-FS`(재무제표 자체인 경우)
- `type` 필드에 정확한 참조 유형을 기입 (법령, 판례, 회계기준서, 정부통계, 감사보고서, 계약서, 협회보고서, 언론보도 등)
- `legal_ref_id`가 있는 경우(법령·판례·기준서) 연결

#### Step 3: `legal_refs.json` 생성

- 원문에 인용된 모든 법령 조문, 판례, 회계 기준서를 개별 항목으로 등록
- `category`: `statute`(법령), `case_law`(판례), `accounting_standard`(회계기준서), `tax_ruling`(세법해석례), `guideline`(가이드라인/고시)
- 법령의 경우 `law_name`, `article`, `paragraph`, `subparagraph` 세분화
- 판례의 경우 `court`, `date`, `case_number` 세분화
- 회계기준서의 경우 `standard_id`, `paragraph` 세분화

#### Step 4: `claims.json` 생성

- 리포트의 핵심 결론이 될 주장/판단을 추출
- `domain`: 해당 주장이 `accounting`, `legal`, 또는 `mixed` 중 어디에 속하는지 기입
- `claim_type`: 주장의 성격 분류 — `finding`(발견사항), `opinion`(의견), `recommendation`(권고), `risk_assessment`(리스크 평가), `compliance_status`(준수 현황)
- `importance`: `critical`(법적 책임 또는 계속기업 관련), `high`(재무 건전성·핵심 전략에 직결), `med`(보조적), `low`(참고)
- `requires_rebuttal`: 반대 논거나 리스크가 예상되면 `true`

#### Step 5: `evidences.json` 생성

- 각 주장을 뒷받침하는 원문 인용을 추출
- **반드시** `ref_ids` 배열에 해당 인용의 개별 참조 ID를 연결
- 법령·기준서 근거의 경우 `legal_ref_ids` 배열에도 연결
- `citation` 필드에 사람이 읽을 수 있는 인용 표기 작성 (섹션 14의 형식 준수)
- `evidence_type`: `financial_data`(재무수치), `legal_provision`(법령조문), `case_law`(판례), `accounting_standard`(기준서), `expert_opinion`(전문가의견), `statistical_data`(통계), `contractual_clause`(계약조항), `regulatory_record`(행정기록)

#### Step 6: `metrics.json` 생성

- 정량적 수치 데이터를 추출
- `value`: 원문의 수치를 **그대로** 기록 (반올림 금지)
- `unit`, `period` 반드시 기입
- `metric_category`: `financial`(재무지표), `tax`(세무수치), `ratio`(재무비율), `legal`(법적 수치 — 소송가액, 벌금, 과태료 등), `operational`(영업지표)

#### Step 7: `risks.json` 생성

- 원문에서 식별된 리스크/이슈 사항을 추출
- `risk_type`: `legal_risk`(법적 리스크), `tax_risk`(세무 리스크), `accounting_risk`(회계 리스크), `regulatory_risk`(규제 리스크), `litigation_risk`(소송 리스크), `compliance_risk`(준수 리스크)
- `severity`: `critical`, `high`, `medium`, `low`
- `status`: `identified`(식별됨), `mitigated`(완화됨), `resolved`(해결됨), `monitoring`(모니터링 중)
- 관련 법령·기준서 참조를 `legal_ref_ids`에 연결

#### Step 8: `claim_evidence.csv` 생성

- 주장(Claim)과 근거(Evidence)의 매핑 테이블 작성
- 하나의 Claim에 여러 Evidence가 연결될 수 있음 (1:N)
- 하나의 Evidence가 여러 Claim에 재사용될 수 있음 (N:M)

#### Step 9: `claim_risk.csv` 생성

- 주장(Claim)과 리스크(Risk)의 매핑 테이블 작성
- `requires_rebuttal: true`인 Claim은 최소 1개의 Risk와 매핑되어야 함

---

## 9. PHASE 4: 무결성 검증 (QA)

### AI에게 주는 지시:

```
터미널에서 python ops/run_validate.py reports/{client_name}_{year} 를 실행해줘.
에러가 있으면 01_structured/ 파일들을 수정하고 다시 검증해줘.
```

### 검증 규칙 (15대 QA 하드 룰):

| # | 규칙명 | 유형 | 설명 |
|---|--------|------|------|
| 1 | Evidence 부족 | ERROR | `importance: critical/high` Claim에 매핑된 Evidence가 2개 미만 |
| 2 | 고아 데이터 | ERROR | `source_id`가 `sources.json`에 없는 Evidence, Metric, 또는 Risk 존재 |
| 3 | 단위/기간 누락 | ERROR | Metric의 `unit` 또는 `period` 값이 비어있음 |
| 4 | 수치 모순 | ERROR | 동일 Metric 이름이 서로 다른 Value로 중복 존재 (기간이 다른 경우 제외) |
| 5 | 기간 혼재 | WARNING | 서로 다른 Period가 명시 없이 혼재 |
| 6 | 최신성 경고 | WARNING | `sources.json`의 Date가 1년 이상 경과 |
| 7 | 가정 누락 | WARNING | 미래 추정 Metric에 관련 가정이 없음 |
| 8 | 리스크 매핑 누락 | ERROR | `requires_rebuttal: true` Claim에 매핑된 Risk가 0건 |
| 9 | 출처 미연결 | ERROR | Evidence에 `ref_ids`가 없거나 빈 배열 |
| 10 | 참조 미존재 | ERROR | Evidence의 `ref_ids`에 있는 ID가 `references.json`에 없음 |
| 11 | 법령 참조 무결성 | ERROR | `legal_ref_ids`에 있는 ID가 `legal_refs.json`에 없음 |
| 12 | 법령 형식 오류 | WARNING | `legal_refs.json` 항목의 `law_name` 또는 `article` 필드가 비어있음 |
| 13 | Critical 주장 리스크 | ERROR | `importance: critical` Claim에 연결된 Risk의 severity가 모두 `low`인 경우 (리스크 과소평가 의심) |
| 14 | 도메인 불일치 | WARNING | Claim의 `domain`과 연결된 Evidence의 `evidence_type`이 논리적으로 불일치 |
| 15 | 인용 형식 불완전 | WARNING | Evidence의 `citation`이 섹션 14의 형식 규칙을 미준수 |

### 검증 결과 형식 (`02_checks/validation_report.json`):

```json
{
  "validation_summary": {
    "total_errors": 0,
    "total_warnings": 0,
    "evidences_checked": 0,
    "references_checked": 0,
    "legal_refs_checked": 0,
    "risks_checked": 0,
    "citation_coverage": "0/0 (0%)",
    "legal_ref_coverage": "0/0 (0%)"
  },
  "errors": [],
  "warnings": []
}
```

**에러가 0이 될 때까지 PHASE 3 ↔ PHASE 4를 반복한다.**

---

## 10. PHASE 5: 초안 리포트 자동 생성

### AI에게 주는 지시:

```
터미널에서 python ops/run_report.py reports/{client_name}_{year} 를 실행해줘.
```

### 리포트 구조 (자동 생성):

`run_report.py`는 `01_structured/` 데이터를 읽어 아래 구조의 Markdown 리포트를 자동 생성한다.

```markdown
# Root Inside 초안 리포트: {클라이언트명} {연도}
## 프로젝트 유형: {project_type}

## 1. 핵심 발견사항 및 판단 (Claims & Findings)
   ### 1.1 Critical / High Importance
   - 각 Claim별 근거 테이블 (인용문 + 신뢰도 + 법적 근거 + 각주)
   ### 1.2 Medium / Low Importance
   - 각 Claim별 근거 요약

## 2. 리스크 분석 (Risk Assessment)
   ### 2.1 법적 리스크
   - 리스크별 심각도, 현황, 관련 법령, 관련 주장 매핑
   ### 2.2 회계/세무 리스크
   - 리스크별 심각도, 현황, 관련 기준서/법령, 관련 주장 매핑
   ### 2.3 규제 리스크
   - 리스크별 심각도, 현황, 관련 규제, 관련 주장 매핑

## 3. 핵심 재무 지표 (Key Metrics)
   - Metrics 전체 테이블 (ID, 지표명, 수치, 단위, 기간, 카테고리)
   - 카테고리별 그룹핑 (재무, 세무, 비율, 법적 수치, 영업)

## 4. 전체 근거 목록 (Evidence Detail)
   - 모든 Evidence의 개별 출처 citation + 법적 근거 표시
   - 근거 유형별 분류

## 5. 법령·기준서 참조표 (Legal & Accounting Standards Reference)
   - 적용된 법령 조문 목록 (법률명, 조항, 요지)
   - 적용된 회계 기준서 목록 (기준서 번호, 문단, 요지)
   - 인용된 판례 목록 (법원, 사건번호, 판시사항 요지)

## 6. 각주 (References)
   - 전체 참조의 각주 번호 → 발행기관, 제목, URL/법령정보

## 7. 데이터 출처 요약 (Source Summary)
   - Source별 참조 수 + 도메인(회계/법률) 분류
   - 기관별 분류 (법원, 국세청, 금융감독원, 회계법인 등)
```

### 출처 추적 체계 (V3.0 — 법령·기준서 통합):

```
Claim → Evidence → ref_ids    → Reference (publisher, title, URL)
  │        │
  │        └── legal_ref_ids  → Legal_Ref (law_name, article, paragraph / case_number / standard_id)
  │
  └── claim_risk.csv → Risk → legal_ref_ids → Legal_Ref
```

모든 주장은 근거를 통해 원본 URL 또는 법령 조문·판례·기준서까지 역추적 가능해야 한다.

---

## 11. PHASE 6: 최종 윤문

### Claude Desktop/Project에게 주는 지시:

```
03_report/report_draft.md를 기반으로, Big 4 회계법인 / 대형 로펌 스타일의 전문
자문 리포트로 재구성해 주세요.

[준수 사항]
- 데이터와 수치는 절대 변경하지 마세요.
- 법령 조항 번호, 판례 번호, 기준서 번호는 절대 수정하지 마세요.
- 각주와 출처 추적 체계는 반드시 유지하세요.
- 서술 흐름과 가독성을 개선하되, 법률·회계 전문용어의 정확성을 유지하세요.
- Executive Summary를 추가하세요.
- 회계 리포트의 경우 감사보고서 양식에 준하는 구조를 참고하세요.
- 법률 리포트의 경우 법률의견서(Legal Opinion) 양식에 준하는 구조를 참고하세요.
- 면책 조항(Disclaimer)을 포함하세요:
  "본 리포트는 제공된 자료에 기반한 분석 결과이며, 법률 자문 또는
   회계 감사 의견을 구성하지 않습니다. 최종 판단은 관련 전문가와
   협의하시기 바랍니다."
```

---

## 12. 원스텝 실행 프롬프트

> **아래 프롬프트 하나로 PHASE 1~5를 한 번에 실행할 수 있다.**
> 인간은 `00_sources/`에 원본 파일만 넣고 아래 프롬프트를 AI에게 전달하면 된다.

### 마스터 프롬프트 (복사하여 사용)

```
당신은 Root Inside AI Data Parser (Accounting & Law Edition)입니다.
이 프로젝트의 README.md를 먼저 읽고, 아래 작업을 순서대로 수행하세요.

[사전 정보]
- 클라이언트명: {client_name}
- 서비스 연도: {year}
- 프로젝트 유형: {project_type}
  (FDD / LDD / TAX_ADVISORY / AUDIT_SUPPORT / COMPLIANCE / MnA / IPO / 기타)
- 원본 파일 위치: reports/{client_name}_{year}/00_sources/

[실행 순서]
1. README의 섹션 4에 따라 프로젝트 폴더 구조가 없으면 생성하세요.
2. 00_sources/ 안의 모든 원본 파일을 읽고, 각각을 Markdown 형식으로
   변환하여 "{파일명}_extracted.txt"로 같은 폴더에 저장하세요.
   (README 섹션 7의 변환 규칙을 준수)
3. 변환된 _extracted.txt 파일들을 읽고, README 섹션 13의 스키마에 맞춰
   01_structured/ 내부의 JSON/CSV 파일들을 생성하세요.
   (README 섹션 8의 Step 1~9 순서를 엄수)
4. 터미널에서 python ops/run_validate.py reports/{client_name}_{year} 를
   실행하여 검증하세요. 에러가 있으면 수정 후 재검증하세요.
5. 에러가 0이 되면, python ops/run_report.py reports/{client_name}_{year} 를
   실행하여 초안 리포트를 생성하세요.
6. 완료되면 최종 결과 요약을 보고하세요:
   - 추출된 Sources 수 (도메인별)
   - 생성된 References 수 (유형별)
   - 생성된 Legal Refs 수 (카테고리별)
   - 생성된 Claims / Evidences / Metrics / Risks 수
   - 검증 결과 (에러/경고 수)
   - 초안 리포트 경로

[절대 준수]
- README 섹션 5의 에이전트 규칙을 반드시 따르세요.
- 원문에 없는 내용을 절대 창작하지 마세요.
- 법령 조항, 판례 번호, 기준서 번호를 추측하거나 지어내지 마세요.
- 모든 근거에 개별 출처(ref_ids + citation)를 반드시 연결하세요.
- 법령·기준서 근거는 legal_refs.json에도 등록하세요.
- 리스크 사항은 risks.json에 추출하고 관련 주장에 매핑하세요.
```

---

## 13. 데이터베이스 스키마 상세

### 13.1 `sources.json` — 출처 문서 목록

```json
[
  {
    "id": "SRC-01",                          // 필수 | 형식: SRC-{순번}
    "title": "문서 제목",                      // 필수 | 원본 문서의 정식 제목
    "type": "감사보고서",                       // 필수 | 문서 유형 (하단 유형표 참조)
    "domain": "accounting",                   // 필수 | accounting / legal / mixed
    "date": "2026-02-10",                     // 필수 | ISO 8601 형식
    "path_or_url": "reports/.../파일명.docx",   // 필수 | 원본 파일 경로 또는 URL
    "ref_ids": ["REF-01", "REF-02"],          // 필수 | 이 문서에서 파생된 개별 참조 ID 목록
    "ref_count": 2,                           // 필수 | ref_ids의 개수
    "author": "작성 기관/인",                   // 필수 | 작성 주체 (회계법인, 로펌, 내부 등)
    "description": "문서에 대한 간략 설명"       // 선택 | 보조 설명
  }
]
```

**문서 유형 (`type`) 분류표:**

| 도메인 | 유형 값 | 설명 |
|--------|---------|------|
| 회계 | `감사보고서` | 외부감사인의 감사보고서 |
| 회계 | `재무제표` | 재무상태표, 손익계산서, 현금흐름표 등 |
| 회계 | `주석` | 재무제표 주석 |
| 회계 | `내부통제보고서` | 내부회계관리제도 운영/검토 보고서 |
| 회계 | `세무조정계산서` | 법인세 세무조정 관련 서류 |
| 회계 | `재무분석보고서` | 재무 실사(FDD) 분석 보고서 |
| 회계 | `세무의견서` | 세무 자문 의견서 |
| 법률 | `법률의견서` | Legal Opinion |
| 법률 | `법률실사보고서` | Legal Due Diligence Report |
| 법률 | `계약서` | 주요 계약서 (SPA, SHA, 임대차 등) |
| 법률 | `판결문` | 법원 판결문/결정문 |
| 법률 | `소송현황보고` | 소송 목록 및 현황 분석 |
| 법률 | `규제준수보고서` | Compliance Report |
| 법률 | `등기부등본` | 법인/부동산 등기 |
| 법률 | `인허가현황` | 인허가/등록 목록 |
| 복합 | `실사종합보고서` | FDD + LDD 통합 보고서 |
| 복합 | `기업가치평가보고서` | Valuation Report |
| 기타 | `산업분석보고서` | 시장/산업 분석 자료 |
| 기타 | `내부자료` | 회사 내부 제출 자료 |

### 13.2 `references.json` — 개별 참조/인용 목록

```json
[
  {
    "ref_id": "REF-01",                       // 필수 | 형식: REF-{순번} 또는 REF-{식별자}
    "source_id": "SRC-01",                    // 필수 | 이 참조가 속한 Source ID
    "title": "참조 문헌/법령/판례 제목",         // 필수
    "publisher": "발행 기관명",                  // 필수 | 법원, 국회, 금융감독원, 회계법인 등
    "type": "법령",                            // 필수 | 하단 유형표 참조
    "date": "2026-02-11",                     // 필수 | ISO 8601 (법령: 시행일, 판례: 선고일)
    "url": "https://...",                     // 필수 | 원본 URL (없으면 "N/A")
    "legal_ref_id": "LREF-01"                 // 선택 | 법령·판례·기준서인 경우 legal_refs.json ID 연결
  }
]
```

**참조 유형 (`type`) 분류표:**

| 유형 값 | 설명 | 예시 |
|---------|------|------|
| `법령` | 법률, 시행령, 시행규칙 | 법인세법, 상법 시행령 |
| `판례` | 법원 판결/결정 | 대법원, 고등법원, 조세심판원 |
| `회계기준서` | K-IFRS, K-GAAP 등 | K-IFRS 제1001호 |
| `세법해석례` | 국세청 예규/심사결정 | 서면인터넷방문상담2팀-1234 |
| `정부통계` | 정부 발행 통계 자료 | 국세통계, 사법연감 |
| `행정규칙` | 고시, 훈령, 예규, 지침 | 금융감독원 감독규정 |
| `감사보고서` | 외부감사인 보고서 자체 | — |
| `계약서` | 각종 계약 문서 | SPA, 임대차계약 |
| `내부자료` | 클라이언트 내부 자료 | 이사회 의사록, 내부규정 |
| `협회보고서` | 산업 협회 발행 자료 | — |
| `언론보도` | 뉴스 기사 | — |
| `학술논문` | 학술지 게재 논문 | — |
| `리서치리포트` | 증권사/연구기관 보고서 | — |

### 13.3 `legal_refs.json` — 법령·판례·기준서 참조 테이블

```json
[
  {
    "id": "LREF-01",                          // 필수 | 형식: LREF-{순번}
    "category": "statute",                    // 필수 | statute / case_law / accounting_standard / tax_ruling / guideline

    // --- 법령(statute) 전용 필드 ---
    "law_name": "법인세법",                     // 조건부 필수 (statute) | 법률 정식 명칭
    "article": "제19조",                       // 조건부 필수 (statute) | 조 번호
    "paragraph": "제1항",                      // 선택 | 항 번호
    "subparagraph": "제3호",                   // 선택 | 호 번호
    "provision_text": "조문 원문 (원문에 있는 경우)", // 선택 | 해당 조문의 원문
    "enforcement_date": "2025-01-01",          // 선택 | 시행일

    // --- 판례(case_law) 전용 필드 ---
    "court": "대법원",                          // 조건부 필수 (case_law) | 법원명
    "case_date": "2023-05-18",                 // 조건부 필수 (case_law) | 선고일
    "case_number": "2022다12345",              // 조건부 필수 (case_law) | 사건번호
    "ruling_summary": "판시사항 요지",           // 선택 | 판시사항 요약 (원문 인용)

    // --- 회계기준서(accounting_standard) 전용 필드 ---
    "standard_id": "K-IFRS 제1115호",          // 조건부 필수 (accounting_standard) | 기준서 번호
    "standard_title": "고객과의 계약에서 생기는 수익", // 선택 | 기준서 제목
    "paragraph_number": "문단 35",              // 선택 | 문단 번호

    // --- 세법해석례(tax_ruling) 전용 필드 ---
    "ruling_id": "서면인터넷방문상담2팀-1234",    // 조건부 필수 (tax_ruling) | 해석례 번호
    "ruling_date": "2024-03-15",               // 선택 | 회신일

    // --- 공통 필드 ---
    "summary": "해당 법령/판례/기준서의 핵심 내용 요약", // 선택 | 원문 기반 요약
    "incomplete_citation": false               // 필수 | 인용 정보가 불완전한 경우 true
  }
]
```

### 13.4 `claims.json` — 핵심 주장/판단 목록

```json
[
  {
    "id": "CLM-01",                           // 필수 | 형식: CLM-{순번}
    "text": "핵심 주장/판단 전문",               // 필수 | 하나의 완결된 문장
    "domain": "accounting",                   // 필수 | accounting / legal / mixed
    "claim_type": "finding",                  // 필수 | finding / opinion / recommendation / risk_assessment / compliance_status
    "importance": "critical",                 // 필수 | critical / high / med / low
    "requires_rebuttal": true                 // 필수 | true: 반박/리스크 존재, false: 팩트 기반
  }
]
```

**importance 분류 기준:**

| 등급 | 기준 |
|------|------|
| `critical` | 법적 책임, 계속기업 가정 위반, 감사 의견 변경, 중대 소송 패소 가능성 등 존립에 직결 |
| `high` | 재무 건전성, 핵심 전략, 주요 법적 리스크에 직결 |
| `med` | 시장 동향, 부수적 기회/위협, 경미한 규제 위반 |
| `low` | 참고 정보, 배경 지식, 행정적 사항 |

**claim_type 분류 기준:**

| 유형 | 설명 | 예시 |
|------|------|------|
| `finding` | 객관적 발견사항 | "매출채권 회전일수가 전년 대비 15일 증가하였다" |
| `opinion` | 전문가 의견/판단 | "해당 거래의 수익인식 시점은 K-IFRS 제1115호에 부합한다" |
| `recommendation` | 권고/제안 | "내부통제 미비점에 대한 시정 조치가 필요하다" |
| `risk_assessment` | 리스크 평가 | "진행 중인 특허 소송에서 패소 시 약 50억 원의 손실이 예상된다" |
| `compliance_status` | 규제 준수 현황 | "공정거래법상 대규모내부거래 이사회 의결 요건을 준수하고 있다" |

### 13.5 `evidences.json` — 근거/인용문 목록

```json
[
  {
    "id": "EV-01",                            // 필수 | 형식: EV-{순번}
    "source_id": "SRC-01",                    // 필수 | 이 근거가 속한 Source ID
    "ref_ids": ["REF-01"],                    // 필수 | 개별 참조 ID 배열 (1개 이상)
    "legal_ref_ids": ["LREF-01"],             // 선택 | 법령·판례·기준서 참조 ID 배열
    "quote": "원문에서 직접 인용한 문장",         // 필수 | 원문 그대로 (수정 금지)
    "location": "섹션 3.3 매출액 분석",          // 필수 | 페이지/섹션/장 정보
    "evidence_type": "financial_data",        // 필수 | 하단 유형표 참조
    "reliability": "high",                    // 필수 | high / med / low
    "citation": "코지맘바이오 감사보고서 §3.3 (삼일회계법인, 2026.02.10)"
                                              // 필수 | 사람이 읽을 수 있는 인용 표기
  }
]
```

**evidence_type 분류표:**

| 유형 값 | 설명 | 예시 |
|---------|------|------|
| `financial_data` | 재무제표/회계 수치 | 매출액, 영업이익, 부채비율 |
| `legal_provision` | 법령 조문 인용 | 법인세법 제19조 |
| `case_law` | 판례 인용 | 대법원 판결 |
| `accounting_standard` | 회계 기준서 인용 | K-IFRS 문단 인용 |
| `expert_opinion` | 전문가 의견 | 감사인 의견, 법률 자문 의견 |
| `statistical_data` | 통계 데이터 | 정부 통계, 산업 통계 |
| `contractual_clause` | 계약 조항 인용 | SPA 제5조, 임대차계약 해지 조항 |
| `regulatory_record` | 행정 기록 | 인허가 이력, 과태료 부과 이력 |
| `corporate_record` | 법인 기록 | 등기사항, 이사회 의사록 |
| `tax_data` | 세무 관련 수치/기록 | 세무조정 항목, 세액 |

**reliability 분류 기준:**

| 등급 | 기준 |
|------|------|
| `high` | 공식 재무제표, 감사보고서, 법원 판결, 정부 통계, 공증 계약서, 등기부등본 등 1차 공식 자료 |
| `med` | 내부 분석, 시뮬레이션, 전문가 의견, 미공증 계약서, 내부 보고서 |
| `low` | 언론 보도, 비공식 자료, 간접 인용, 구두 자료의 문서화 |

### 13.6 `metrics.json` — 정량 지표 목록

```json
[
  {
    "id": "MET-01",                           // 필수 | 형식: MET-{순번}
    "name": "매출액",                          // 필수 | 지표명
    "value": "24,340,000,000",                // 필수 | 원문 수치 그대로 (문자열)
    "unit": "원",                             // 필수 | 단위 (원, %, 억 원, 건, 일, 개월 등)
    "period": "2025(연간)",                    // 필수 | 기간 (형식: {연도}({범위}))
    "source_id": "SRC-01",                    // 필수 | 출처 Source ID
    "location": "재무상태표 p.5",               // 필수 | 원문 내 위치
    "metric_category": "financial"            // 필수 | financial / tax / ratio / legal / operational
  }
]
```

**metric_category 분류표:**

| 카테고리 | 설명 | 예시 |
|----------|------|------|
| `financial` | 재무제표 수치 | 매출액, 영업이익, 총자산, 부채총계 |
| `tax` | 세무 관련 수치 | 법인세 납부액, 세무조정 금액, 이연법인세 |
| `ratio` | 재무비율 | 부채비율, 유동비율, ROE, 이자보상비율 |
| `legal` | 법적 수치 | 소송가액, 벌금/과태료, 손해배상액, 보증금 |
| `operational` | 영업/운영 지표 | 매출채권 회전일수, 재고 회전율, 직원 수 |

**period 작성 규칙:**
- 연간: `2025(연간)`
- 분기: `2025_Q1(분기)`
- 반기: `2025_H1(반기)`
- 비교: `2025 vs 2024`
- 전망: `2030(전망)`
- 특정 시점: `2025(12.31 기준)` — 재무상태표 기준일 등
- 누적: `2020~2025(누적)`
- 사업연도: `제11기(2025.01.01~2025.12.31)`

### 13.7 `risks.json` — 리스크/이슈 사항 목록

```json
[
  {
    "id": "RISK-01",                          // 필수 | 형식: RISK-{순번}
    "title": "리스크 제목",                     // 필수 | 간결한 리스크 명칭
    "description": "리스크 상세 설명",           // 필수 | 원문 기반 설명 (창작 금지)
    "risk_type": "legal_risk",                // 필수 | 하단 유형표 참조
    "severity": "high",                       // 필수 | critical / high / medium / low
    "status": "identified",                   // 필수 | identified / mitigated / resolved / monitoring
    "source_id": "SRC-01",                    // 필수 | 출처 Source ID
    "legal_ref_ids": ["LREF-01"],             // 선택 | 관련 법령·기준서 참조 ID
    "financial_impact": "약 50억 원",          // 선택 | 재무적 영향 추정 (원문에 있는 경우만)
    "location": "섹션 4.2 소송 현황"            // 필수 | 원문 내 위치
  }
]
```

**risk_type 분류표:**

| 유형 값 | 설명 | 예시 |
|---------|------|------|
| `legal_risk` | 법적 리스크 (일반) | 계약 위반, 지재권 침해 |
| `litigation_risk` | 소송/분쟁 리스크 | 계류 소송, 잠재 소송 |
| `tax_risk` | 세무 리스크 | 추징 가능성, 이전가격 이슈 |
| `accounting_risk` | 회계 처리 리스크 | 수익인식 오류, 자산 과대계상 |
| `regulatory_risk` | 규제 리스크 | 인허가 취소 가능성, 법령 위반 |
| `compliance_risk` | 준법 리스크 | 공정거래법 위반, 개인정보보호법 위반 |
| `governance_risk` | 지배구조 리스크 | 이해충돌, 내부통제 미비 |
| `going_concern_risk` | 계속기업 리스크 | 자본잠식, 채무불이행 |

### 13.8 `claim_evidence.csv` — 주장↔근거 매핑

```csv
claim_id,evidence_id
CLM-01,EV-01
CLM-01,EV-02
CLM-02,EV-03
```

- UTF-8 인코딩 필수
- 헤더 행 필수: `claim_id,evidence_id`
- 1개 Claim : N개 Evidence 관계
- 1개 Evidence가 여러 Claim에 매핑 가능

### 13.9 `claim_risk.csv` — 주장↔리스크 매핑

```csv
claim_id,risk_id
CLM-01,RISK-01
CLM-03,RISK-02
CLM-03,RISK-03
```

- UTF-8 인코딩 필수
- 헤더 행 필수: `claim_id,risk_id`
- `requires_rebuttal: true`인 Claim은 최소 1개의 Risk와 매핑 필수
- 1개 Claim : N개 Risk 관계

---

## 14. 도메인별 참조 체계: 회계 기준·법령 인용 규칙

### 14.1 법령 인용 형식

```
{법률명} 제{조}조 제{항}항 [제{호}호]
```

**예시:**
- `법인세법 제19조 제1항`
- `상법 제382조의4 제1항 제3호`
- `소득세법 시행령 제51조 제1항`
- `자본시장과 금융투자업에 관한 법률 제159조 제1항`

### 14.2 판례 인용 형식

```
{법원명} {선고일(yyyy.m.d.)} 선고 {사건번호} {재판유형}
```

**예시:**
- `대법원 2023.5.18. 선고 2022다12345 판결`
- `서울고등법원 2024.3.15. 선고 2023나56789 판결`
- `조세심판원 2024.6.20. 조심 2024서1234`
- `헌법재판소 2023.12.21. 2022헌바123 결정`

### 14.3 회계 기준서 인용 형식

```
{기준서 체계} 제{번호}호 '{제목}' 문단 {번호}
```

**예시:**
- `K-IFRS 제1115호 '고객과의 계약에서 생기는 수익' 문단 35`
- `K-IFRS 제1001호 '재무제표 표시' 문단 10`
- `일반기업회계기준 제5장 '유형자산' 제5.2`
- `회계감사기준 700 '재무제표에 대한 의견 형성과 보고'`

### 14.4 세법해석례 인용 형식

```
{해석기관} {문서번호} ({회신일})
```

**예시:**
- `국세청 서면인터넷방문상담2팀-1234 (2024.03.15.)`
- `기획재정부 법인세제과-567 (2024.05.20.)`
- `조세심판원 조심 2024서1234 (2024.06.20.)`

### 14.5 Evidence citation 필드 작성 규칙

| 근거 유형 | citation 형식 | 예시 |
|-----------|---------------|------|
| 재무제표 수치 | `"{회사명} {보고서명} §{섹션} ({감사인/작성자}, {날짜})"` | `"(주)코지맘바이오 감사보고서 §재무상태표 (삼일회계법인, 2026.02.10)"` |
| 법령 조문 | `"{법률명} {조항} ({시행일})"` | `"법인세법 제19조 제1항 (2025.01.01. 시행)"` |
| 판례 | `"{법원명} {선고일} 선고 {사건번호} {재판유형}"` | `"대법원 2023.5.18. 선고 2022다12345 판결"` |
| 회계 기준서 | `"{기준서체계} 제{번호}호 문단 {번호}"` | `"K-IFRS 제1115호 문단 35"` |
| 계약서 | `"{계약명} 제{조}조 ({계약일})"` | `"주식매매계약서(SPA) 제5조 (2025.06.15.)"` |
| 전문가 의견 | `"{보고서명} §{섹션} ({작성자}, {날짜})"` | `"법률실사보고서 §4.2 소송현황 (김앤장, 2026.01.20)"` |

---

## 15. 참조 사례

### 15.1 회계 도메인 사례 — cozymom_2025

| 항목 | 내용 |
|------|------|
| 클라이언트 | (주)코지맘바이오 |
| 프로젝트 유형 | 재무제표 분석 + 산업 분석 |
| 분석 기간 | 제11기 (2025.01.01 ~ 2025.12.31) |
| 원본 문서 | 재무제표 분석 보고서 + 건강기능식품 산업 분석 보고서 |
| 데이터 규모 | Sources 2, References 32, Claims 12, Evidences 22, Metrics 33 |
| 검증 결과 | 에러 0건, 출처 추적 100% |

### 15.2 법률 도메인 사례 구조 (참고 템플릿)

아래는 법률 실사(LDD) 프로젝트의 예상 데이터 구조이다. 새 프로젝트에서 참고하라.

```
예상 데이터 규모 (LDD 기준):
- Sources: 5~10개 (법률의견서, 주요계약서, 등기부등본, 소송현황, 인허가현황 등)
- References: 30~80개 (법령 조문, 판례, 계약 조항, 행정기록 등)
- Legal Refs: 20~50개 (적용 법령, 관련 판례, 해석례 등)
- Claims: 15~30개 (법적 발견사항, 리스크 평가, 준수 현황 등)
- Evidences: 30~60개 (계약 조항 인용, 법령 인용, 판례 인용, 등기 기록 등)
- Metrics: 10~20개 (소송가액, 보증금, 과태료, 계약금액 등)
- Risks: 10~25개 (소송 리스크, 계약 리스크, 규제 리스크 등)
```

**새 프로젝트에서 이 사례의 JSON 파일 구조와 필드 값을 참조 기준으로 삼아라.**

---

*Root Inside AI Data Parser | Pipeline V5.0 | Accounting & Law Edition*
*Citation-Enforced, Standards-Based, Full-Auto Report Generation*
*Last Updated: 2026-02-19*
