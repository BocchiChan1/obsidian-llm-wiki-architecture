# 02. 전체 디렉토리 트리 & 도메인별 세부 리프 전수 카탈로그 (`000.inbox` ~ `950.UI`)

> 본 문서는 옵시디언 LLM-Wiki 볼트의 **`000.inbox`부터 `950.UI`까지 모든 도메인·서브폴더·정규 리프 코드표**와 **`940.llm-wiki` 위키 허브 및 `912.vein` 데이터 혈관 구조**를 빠짐없이 수록한 레퍼런스입니다.

---

## 1. 최상위 도메인 아키텍처 개요 (`Single Responsibility` & `Surface vs Raw Separation`)

| 번호 대역 | 최상위 폴더 | 아키텍처 계층 | 표면 폴더 허용 파일 | 실제 마크다운(`.md`) 저장 위치 |
|:---:|:---|:---|:---|:---|
| **Root** | `.agents/`, `.cursor/`, `.obsidian/` | CLI 하네스 미러 & 코어 플러그인 | `AGENTS.md`, `SKILL.md`, `.mdc`, `manifest.json` | `920.prompt/921.skill/` (SSOT 원본) |
| **`000`** | `000.inbox/` | 임시 유입 버퍼 (상주 `0개` 원칙) | `000.inbox.md`, `AGENTS.md` | 분류 즉시 `941.raw/` 또는 `420-005.digest/` 직통 이관 |
| **`100`** | `100.vault/` | 영구 객관 지식 도서관 (학문·인문·직무) | `{folder}.md`, `,.map.*`, `,.base.*`, `AGENTS.md` | `900.maintenance/940.llm-wiki/941.raw/100.vault/...` |
| **`200`** | `200.plan/` | 루틴·실시간 작업장(`221.workpaper`)·타임라인 | `{folder}.md`, `,.map.*`, `,.base.*`, `AGENTS.md` (`221`은 작업 파일 허용) | `900.maintenance/940.llm-wiki/941.raw/200.plan/...` |
| **`300`** | `300.areas/` | 창작 프로젝트(`310`) & 일상·실천 공간(`320`) | `{folder}.md`, `,.map.*`, `,.base.*`, `AGENTS.md` | `900.maintenance/940.llm-wiki/941.raw/300.areas/...` |
| **`400`** | `400.resource/` | 비-마크다운 원본 바이너리 아카이브 (Read-Only) | 원본 PDF/PPTX/M4A/PNG/DWG/IPYNB 직접 보존 | 크롤링·추출 텍스트만 `941.raw/400.resource/...` 수록 |
| **`800`** | `800.outbox/` | 대외 제출·외부 연동 단일 정본 출고장 | 최종 제출 정본 1개 + `_superseded/` 구버전 격리 | 추출 요약 메타만 `941.raw/800.outbox/...` 수록 |
| **`900`** | `900.maintenance/` | 자율 운영체제 커널 (코드·프롬프트·에이전트·위키·UI) | 시스템 코드·스킬·위키·로그 직접 보존 | 자체가 엔진 본진 (`941.raw` 이중 복제 안 함) |

---

## 2. `100.vault` 지식 도서관 세부 리프 전수 코드표

### 2.1. `110.science/` — PMEST v2 `46`개 정규 학문 리프 전수 표 (영문 약어 ASCII 오름차순 번호 고정)

| 상위 분과 | 폴더 번호·이름 | PMEST 기호 | 영문 학문명 | 한글 학문명 | 표준 YAML 태그 |
|:---|:---|:---|:---|:---|:---|
| `111.natural-science` | `111-001.astro` | `,n_sci,,astro` | Astronomy | 천문학 | `n_sci/astro` |
| `111.natural-science` | `111-002.biol` | `,n_sci,,biol` | Biology | 생물학 | `n_sci/biol` |
| `111.natural-science` | `111-003.chem` | `,n_sci,,chem` | Chemistry | 화학 | `n_sci/chem` |
| `111.natural-science` | `111-004.earth` | `,n_sci,,earth` | Earth Science | 지구과학 | `n_sci/earth` |
| `111.natural-science` | `111-005.phys` | `,n_sci,,phys` | Physics | 물리학 | `n_sci/phys` |
| `112.formal-science` | `112-001.cs` | `,f_sci,,cs` | Computer Science | 컴퓨터과학 | `f_sci/cs` |
| `112.formal-science` | `112-002.dec` | `,f_sci,,dec` | Decision Theory | 의사결정론 | `f_sci/dec` |
| `112.formal-science` | `112-003.dsci` | `,f_sci,,dsci` | Data Science | 데이터과학 | `f_sci/dsci` |
| `112.formal-science` | `112-004.gt` | `,f_sci,,gt` | Game Theory | 게임이론 | `f_sci/gt` |
| `112.formal-science` | `112-005.info` | `,f_sci,,info` | Information Theory | 정보이론 | `f_sci/info` |
| `112.formal-science` | `112-006.logic` | `,f_sci,,logic` | Logic | 논리학 | `f_sci/logic` |
| `112.formal-science` | `112-007.math` | `,f_sci,,math` | Mathematics | 수학 | `f_sci/math` |
| `112.formal-science` | `112-008.stat` | `,f_sci,,stat` | Statistics | 통계학 | `f_sci/stat` |
| `112.formal-science` | `112-009.syst` | `,f_sci,,syst` | Systems Science | 시스템과학 | `f_sci/syst` |
| `113.applied-science` | `113-001.actuar` | `,a_sci,,actuar` | Actuarial Science | 보험수리학 | `a_sci/actuar` |
| `113.applied-science` | `113-002.agri` | `,a_sci,,agri` | Agriculture | 농학 | `a_sci/agri` |
| `113.applied-science` | `113-003.arch` | `,a_sci,,arch` | Architecture | 건축학 | `a_sci/arch` |
| `113.applied-science` | `113-004.dent` | `,a_sci,,dent` | Dentistry | 치의학 | `a_sci/dent` |
| `113.applied-science` | `113-005.eng` | `,a_sci,,eng` | Engineering | 공학 | `a_sci/eng` |
| `113.applied-science` | `113-006.envsci` | `,a_sci,,envsci` | Environmental Science | 환경과학 | `a_sci/envsci` |
| `113.applied-science` | `113-007.ergon` | `,a_sci,,ergon` | Ergonomics | 인간공학 | `a_sci/ergon` |
| `113.applied-science` | `113-008.foodsci` | `,a_sci,,foodsci` | Food Science | 식품과학 | `a_sci/foodsci` |
| `113.applied-science` | `113-009.forens` | `,a_sci,,forens` | Forensic Science | 법과학 | `a_sci/forens` |
| `113.applied-science` | `113-010.hsci` | `,a_sci,,hsci` | Health Science | 보건학 | `a_sci/hsci` |
| `113.applied-science` | `113-011.lis` | `,a_sci,,lis` | Library & Information Science | 문헌정보학 | `a_sci/lis` |
| `113.applied-science` | `113-012.med` | `,a_sci,,med` | Medicine | 의학 | `a_sci/med` |
| `113.applied-science` | `113-013.mil` | `,a_sci,,mil` | Military Science | 군사학 | `a_sci/mil` |
| `114.social-science` | `114-001.anthro` | `,s_sci,,anthro` | Anthropology | 인류학 | `s_sci/anthro` |
| `114.social-science` | `114-002.archaeo` | `,s_sci,,archaeo` | Archaeology | 고고학 | `s_sci/archaeo` |
| `114.social-science` | `114-003.busadm` | `,s_sci,,busadm` | Public / Business Admin | 행정학 | `s_sci/busadm` |
| `114.social-science` | `114-004.comm` | `,s_sci,,comm` | Communication Studies | 커뮤니케이션학 | `s_sci/comm` |
| `114.social-science` | `114-005.crim` | `,s_sci,,crim` | Criminology | 범죄학 | `s_sci/crim` |
| `114.social-science` | `114-006.cultst` | `,s_sci,,cultst` | Cultural Studies | 문화연구 | `s_sci/cultst` |
| `114.social-science` | `114-007.demo` | `,s_sci,,demo` | Demography | 인구학 | `s_sci/demo` |
| `114.social-science` | `114-008.devst` | `,s_sci,,devst` | Development Studies | 개발학 | `s_sci/devst` |
| `114.social-science` | `114-009.econ` | `,s_sci,,econ` | Economics | 경제학 | `s_sci/econ` |
| `114.social-science` | `114-010.edsci` | `,s_sci,,edsci` | Education Science | 교육학 | `s_sci/edsci` |
| `114.social-science` | `114-011.geog` | `,s_sci,,geog` | Human Geography | 인문지리학 | `s_sci/geog` |
| `114.social-science` | `114-012.hist` | `,s_sci,,hist` | History | 역사학 | `s_sci/hist` |
| `114.social-science` | `114-013.juris` | `,s_sci,,juris` | Jurisprudence | 법학 | `s_sci/juris` |
| `114.social-science` | `114-014.ling` | `,s_sci,,ling` | Linguistics | 언어학 | `s_sci/ling` |
| `114.social-science` | `114-015.mgmt` | `,s_sci,,mgmt` | Management | 경영학 | `s_sci/mgmt` |
| `114.social-science` | `114-016.polisci` | `,s_sci,,polisci` | Political Science | 정치학 | `s_sci/polisci` |
| `114.social-science` | `114-017.psych` | `,s_sci,,psych` | Psychology | 심리학 | `s_sci/psych` |
| `114.social-science` | `114-018.soc` | `,s_sci,,soc` | Sociology | 사회학 | `s_sci/soc` |
| `114.social-science` | `114-019.socwk` | `,s_sci,,socwk` | Social Work | 사회복지학 | `s_sci/socwk` |

### 2.2. `120.humanity` · `130.protoscience` · `140.nonacademic` · `150.occupation` 구조
- **`120.humanity/`**: `121.arts/` (문학·음악·시각예술·영화·게임·애니·버튜버·창작 세계관), `122.thought/` (철학·종교·신화)
- **`130.protoscience/`**: 정식 과학 성립 이전의 원형과학 기록
- **`140.nonacademic/`**: `141.pathological-science/`(병리과학), `142.junk-science/`(정크 사이언스), `143.pseudoscience/`(유사과학), `144.antiscience/`(반과학)
- **`150.occupation/`** (반드시 아래 4개 하위 폴더 중 하나로만 배정):
  - `151.specialized/`: `151-001.stock/`(모의투자 런타임 원장), `151-002.cli/`, `151-003.fintech/`, `151-004.mnemonic/`, `151-005.kdata-cert/`, `151-006.obsidian/`
  - `152.work/`: 실무 산출물 및 `152-000.university/` 전공 과목 지식 정본
  - `153.hobby/`: 취미·여가 지식 정리 노트
  - `154.friends/`: 인물·네트워크 프로파일

---

## 3. `912.vein/` 정형 데이터 혈관 (`912-001` ~ `912-010`) & `942.wiki/` 온톨로지 허브

### 3.1. `910.codey/912.vein/` 10대 확장자 분리 허브

| 폴더명 | 파일 접두어 / 포맷 | 전담 데이터 및 시스템 역할 |
|:---|:---|:---|
| `912-001.py/` | `,.py.<domain>.<action>.py` | 볼트 전 영역 자동화를 수행하는 `532`개 정식 Python 실행 스크립트 |
| `912-002.mjs/` | `,.mjs.*` | Node.js ESM 자동화 및 플러그인 빌드 보조 스크립트 |
| `912-003.yaml/` | `,.yaml.frame.*.yaml` / `stem-leaf.yaml` | 도메인별 YAML 프론트매터 표준 템플릿 및 폴더-약어 매핑 사전 |
| `912-004.md/` | `,.md.*` | 워크플로우 주입용 마크다운 프롬프트 조각 |
| `912-005.html/` | `,.html.*` | 리포트·대시보드 렌더링용 HTML 템플릿 |
| `912-006.json/` | `,.json.entity-canonical-dict.json` 등 | 고유명사 영문 표준 사전, 캘리브레이션 상태 및 설정 캐시 |
| `912-007.base/` | `912-007-001.bases` | 옵시디언 Bases 데이터베이스 뷰 마스터 스키마 |
| `912-008.txt/` | `,.txt.*` | 외부 RAG 업로드용 압축 텍스트 번들 |
| `912-009.npz/` | `,.npz.*` | `nomic-embed-text-v1.5` 768차원 NumPy 압축 임베딩 행렬 |
| `912-010.csv/` | `,.csv.*` | 정량 데이터셋 및 매핑 테이블 |

### 3.2. `940.llm-wiki/942.wiki/` 디렉토리 및 온톨로지 노드 분포 (실측 스냅샷)

| 순번 | 위키 허브 하위 경로 | 수록 마크다운 노드 수 | 역할 설명 |
|:---:|:---|---:|:---|
| `01` | `942.wiki/942-100.overview/` | `10` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `02` | `942.wiki/942-200.entities/` | `4` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `03` | `942.wiki/942-200.entities/942-201.personality/` | `143` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `04` | `942.wiki/942-200.entities/942-202.space/` | `162` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `05` | `942.wiki/942-200.entities/942-203.target/` | `155` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `06` | `942.wiki/942-200.entities/942-204.matter/` | `4102` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `07` | `942.wiki/942-200.entities/942-205.energy/` | `70` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `08` | `942.wiki/942-200.entities/942-207.concepts/` | `66` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `09` | `942.wiki/942-200.entities/942-208.code-ontology/` | `10` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `10` | `942.wiki/942-300.sources/` | `9` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `11` | `942.wiki/942-400.refinery/` | `11` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `12` | `942.wiki/942-400.refinery/942-001.inbox/` | `3` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `13` | `942.wiki/942-400.refinery/942-100.vault/` | `8` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `14` | `942.wiki/942-400.refinery/942-200.plan/` | `5` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `15` | `942.wiki/942-400.refinery/942-300.areas/` | `4` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `16` | `942.wiki/942-400.refinery/942-400.resource/` | `5` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `17` | `942.wiki/942-400.refinery/942-800.outbox/` | `3` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
| `18` | `942.wiki/942-400.refinery/942-900.maintenance/` | `7` | 정제 위키 허브 및 온톨로지 집계 디렉터리 |
