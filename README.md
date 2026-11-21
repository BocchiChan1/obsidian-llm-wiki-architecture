# Obsidian Autonomous LLM-Wiki · PMEST v2 · 46-Module Code Harness & Baseline System Architecture

> **[🏛️ Repository Overview | 보편 오픈소스 아키텍처 설계도]**
> 누구나 자신의 옵시디언(Obsidian) 지식 볼트에 즉시 적용할 수 있도록 설계된 **'자율형 LLM-Wiki · PMEST v2 명명 헌법 · 46-모듈 코드 하네스 · 로컬 AI + 멀티 에이전트 아키텍처'**의 공개 블루프린트입니다.
> - **비개발자·기획자**는 각 섹션의 **아키텍처 다이어그램과 요약 표**만으로 전체 작동 원리와 지식 분류 체계를 즉시 파악할 수 있습니다.
> - **엔지니어·시스템 아키텍트**는 본 저장소의 표준 폴더 골격(`000.inbox`~`950.UI`), 지연 로딩 헌법(`AGENTS.md`), 상세 명세서(`docs/01`~`04`)를 그대로 클론하여 **기존 로컬 환경·외부 서버와 포트·코드 충돌(`0%`) 없이 독립된 자율형 지식 볼트**를 구축할 수 있습니다.

> **[📚 Deep-Dive Documentation Index | 심층 레퍼런스 문서 바로가기]**
> - **[`docs/01-baseline-system-configuration.md`](docs/01-baseline-system-configuration.md)** — **상시 운영 기본 시스템 구성 백과사전** (`913.shell` 무창 데몬 & 스케줄러 블루프린트 · 로컬 AI & `LanceDB` 하이브리드 스택 · 멀티 CLI 터미널 & `OpenDecider` 4-Stage · **`46`개 코드 하네스 모듈 카탈로그** · `.obsidian` 추천 플러그인 아키텍처 · 3중 동기화 & 외장 암호화 백업)
> - **[`docs/02-directory-tree-and-domain-catalog.md`](docs/02-directory-tree-and-domain-catalog.md)** — **`000.inbox` ~ `950.UI` 전 도메인 폴더·리프 카탈로그** (`110.science` 46개 정규 학문 리프 · `140.nonacademic` 4분류 · `410`↔`801` 대칭 구조 · `912.vein` 10대 데이터 혈관 · `942.wiki` 6대 온톨로지 허브)
> - **[`docs/03-python-automation-and-7layer-gate.md`](docs/03-python-automation-and-7layer-gate.md)** — **보편 Python 자동화 도메인 아키텍처 · `VaultCli` 17종 치트시트 · `911.test` 7-Layer 코드 승격 게이트(`tcheck` & `.ruff.toml`)**
> - **[`docs/04-pmest-v2-naming-and-bootstrap-guide.md`](docs/04-pmest-v2-naming-and-bootstrap-guide.md)** — **PMEST v2 + UDC 단일 기호 명명법 · 10자리 불변 `file_id` · 원자적 리네임 & 8대상 연쇄 최신화 · 4단계 부트스트랩 가이드**
> - **[`AGENTS.md`](AGENTS.md) / [`CLAUDE.md`](CLAUDE.md) / [`GEMINI.md`](GEMINI.md) & L1 `AGENTS.md`** — 클론 즉시 동작하는 **전역 헌법 + 도메인별 지연 로딩(`Lazy Load Map`) 스타터 템플릿**

---

## 1. 시스템 전체 작동 구조 (한눈에 보는 아키텍처)

```mermaid
flowchart TD
    subgraph IN["1. 입력 계층 (수집 · Read-Only 보존)"]
        I1["000.inbox (웹 리서치·다이제스트·메모 임시 투입)"]
        I2["400.resource (원본 PDF·문서·오디오·이미지 Copy-Only 보관)"]
    end

    subgraph RAW_WIKI["2. 940.llm-wiki 3계층 지식 엔진 (핵심)"]
        R1["941.raw (무손실 원본 마크다운 격리 보관소)<br/>PMEST v2 파일명 & 표준 YAML 자동 부여"]
        R2["942.wiki (도메인별 아웃링크 위키 허브 & 6대 온톨로지 축)<br/>llm-wiki.engine.py가 자동 컴파일"]
        R3["948.mistake (AI 실수·반성 전용 원장)<br/>실수 재발 시 작업 차단 처벌 게이트 연동"]
    end

    subgraph AI_ENGINE["3. 무료 로컬 AI + 벡터 DB 판단 계층 (토큰 0원)"]
        A1["로컬 고밀도 임베딩 (1024d ONNX/CUDA) + LanceDB 하이브리드 인덱스"]
        A2["Cross-Encoder 리랭커 + Calibrated SLM 라우터 (:8000 · P>=0.85 자동 실행)"]
        A3["Local Read-Only Wiki Assistant (:18793 · sys.addaudithook 커널 격리)"]
    end

    subgraph CLI_HARNESS["4. 멀티 CLI 오케스트레이션 & 920.prompt 스킬 계층"]
        C1["921.skill (cskill / txskill / taskill / corskill / ruskill / wfskill)"]
        C2["멀티 CLI 역할 분리 패널 (Leader · Builder · Worker · Verifier)"]
        C3["911.test 7-Layer 오픈소스 코드 검수 게이트 (tcheck)"]
    end

    subgraph LIVE_SURFACE["5. 실전 인터페이스 & 느슨한 외부 교환 경계"]
        S1["옵시디언 내장 Read-Only 위키 챗봇 (:18793) & 북마크 동기화"]
        S2["격리 에이전트 브라우저 (MCP/CDP) & 비동기 원격 작업 큐"]
        S3["800.outbox (검증된 최종 산출물 출고장 ↔ 외부 시스템 파일 교환)"]
    end

    IN --> R1
    R1 --> R2
    R2 <--> AI_ENGINE
    C1 <--> CLI_HARNESS
    CLI_HARNESS <--> R1
    CLI_HARNESS --> R3
    AI_ENGINE <--> LIVE_SURFACE
    CLI_HARNESS --> S3
```

---

## 2. 전체 볼트(Vault) 표준 디렉토리 트리 & 도메인별 역할 백과사전 (`000` ~ `950`)

> **[핵심 설계 철학 (Open-Source LLM-Wiki Standard)]**
> 1. **3자리 숫자 프리픽스(`000`~`950`) 고정**: 모든 최상위·하위 폴더 앞에 3자리 번호를 부여해 정렬 순서를 영구 고정하고, 각 폴더에 **단 하나의 명확한 책임(Single Responsibility)**만 부여합니다.
> 2. **표면(Surface) 뷰와 원본(Raw) 데이터의 물리적 분리**: 사용자가 탐색기에서 여는 `000`~`400` 표면 폴더에는 **폴더 노트(`{folder}.md`), 자동 맵(`,.map.*`), 데이터베이스 뷰(`,.base.*`), `AGENTS.md`만** 남기고, 실제 일반 마크다운(`.md`) 문서는 상시 데몬(`raw-guard.py`)이 **`900.maintenance/940.llm-wiki/941.raw/<원래경로>/`**로 100% 자동 이관·동기화합니다.
> 3. **폴더별 `AGENTS.md` 지연 로딩(Lazy Load Map)**: 루트 `AGENTS.md`에는 핵심 프로토콜만 두고, 세부 폴더 규칙은 각 도메인의 L1 `AGENTS.md`에만 배치하여 AI가 작업할 때 토큰 폭탄 없이 필요한 폴더 규칙만 읽도록 설계했습니다.

### 2.1. 전체 볼트 마스터 디렉토리 트리

```text
my-vault/ (<VAULT_ROOT> — Universal Obsidian Autonomous LLM-Wiki Blueprint)
├─ AGENTS.md / CLAUDE.md / GEMINI.md        ← [전역 헌법] 멀티 CLI 공통 프로토콜 및 L1 폴더 Load Map 라우터 ("규칙 갱생" 핵심)
├─ .agents/                                 ← [CLI 플러그인·스킬 미러 허브] (,.py.sync.cli.py가 921.skill SSOT로부터 자동 동기화)
│  ├─ AGENTS.md                             ← .agents 전용 CLI 스킬 로딩 규칙
│  ├─ agents/                               ← 서브에이전트 정의 (leader, builder, worker, search, grep, audit 등)
│  ├─ plugins/decider-fork-harness/         ← 4-Stage OpenDecider Fork Layer (sharp P>=0.85 코드직행 vs split 세션분기)
│  └─ skills/                               ← 보편 CLI 네이티브 스킬 (bases, browser-automation, canvas.json, loop, markdown, test-code-inspector)
├─ .cursor/ & .gemini/                      ← [IDE·CLI 규칙·훅 미러] (.cursor/rules/*.mdc, intent-constraint-gate 훅, CLI 설정 미러)
├─ .obsidian/                               ← [옵시디언 코어 & 추천 플러그인 생태계] (Read-Only 위키 챗, 유지보수 필터, 빈 파일 저장 가드 등)
│
├─ 000.inbox/                               ← [임시 수집함] 리서치·메모 임시 유입 (상주 0개 · Flat 원칙 · 000.inbox/AGENTS.md)
│
├─ 100.vault/                               ← [영구 지식 도서관 본관] 학문(110)·인문(120)·원형과학(130)·비학문(140)·직무(150) (100.vault/AGENTS.md)
│  ├─ 110.science/                          ← PMEST v2 46개 정규 학문 리프(Leaf) 체계 (영문 약어 ASCII 알파벳순 고정)
│  │  ├─ 111.natural-science/               ← 자연과학 5개 리프 (111-001.astro 천문학 ~ 111-005.phys 물리학)
│  │  ├─ 112.formal-science/                ← 형식과학 9개 리프 (112-001.cs 컴퓨터과학 ~ 112-009.syst 시스템과학)
│  │  ├─ 113.applied-science/               ← 응용과학 13개 리프 (113-001.actuar 보험수리학 ~ 113-013.mil 군사학)
│  │  └─ 114.social-science/                ← 사회과학 19개 리프 (114-001.anthro 인류학 ~ 114-019.socwk 사회복지학)
│  ├─ 120.humanity/                         ← 인문학 (121.arts 예술·문화 / 122.thought 철학·사상)
│  ├─ 130.protoscience/ & 140.nonacademic/  ← 원형과학 및 비학문 4분류 (141.병리과학·142.정크사이언스·143.유사과학·144.반과학)
│  ├─ 150.occupation/                       ← 직무·전문기술·학습 (하위 폴더 세분화 필수, bare 150 지정 금지)
│  │  ├─ 151.specialized/                   ← 전문 기술 예시 리프 (151-001.engineering, 151-002.cli, 151-003.obsidian, 151-004.research)
│  │  ├─ 152.work/                          ← 실무·업무 지식 및 프로젝트 레퍼런스 정본
│  │  └─ 153.learning/                      ← 자기주도 학습·자격·독학 노트
│  └─ 160.calendar/                         ← 연간·분기 캘린더 마스터
│
├─ 200.plan/                                ← [루틴·작업지·타임라인] (200.plan/AGENTS.md)
│  ├─ 210.routine/                          ← 자동화 루틴 (211.daily, 212.weekly, 213.health)
│  ├─ 220.tasks/                            ← 작업 관리 (221.workpaper 유일한 표면 실시간 작업장, 222.output)
│  └─ 230.timeline/                         ← 일일 회고(daily-feedback) 및 시계열 타임라인 기록
│
├─ 300.areas/                               ← [장기 프로젝트·일상 관심 영역] (300.areas/AGENTS.md)
│  ├─ 310.projects/                         ← 장기 프로젝트 영역 (311.active, 312.writing)
│  └─ 320.domains/                          ← 지속 관심·실천 영역 (321.media, 322.hobbies, 323.practice)
│
├─ 400.resource/                            ← [비마크다운 원본 아카이브 (Read-Only)] (400.resource/AGENTS.md)
│  ├─ 410.documents/                        ← 확장자별 문서·미디어 원본 (_inbox Copy-Only, .extracted 추출 캐시, 410-001.pdf ~ 410-006.notebooks)
│  ├─ 420.source/                           ← 외부 유입 원본 소스 5대 표준 리프
│  │  ├─ 420-001.articles/                  ← 아티클·논문·웹 문서 원문
│  │  ├─ 420-002.books/                     ← 도서·장문 발췌 원문
│  │  ├─ 420-003.media/                     ← 오디오·비디오·트랜스크립트 원본
│  │  ├─ 420-004.digests/                   ← 자동 수집·요약 다이제스트 로그 (,.digest.*.md)
│  │  └─ 420-005.references/                ← 공식 명세서·증빙·매뉴얼 원본
│  ├─ 430.assets/                           ← 이미지·다이어그램 리소스 (_inbox, _plugin/excalidraw)
│  └─ 490.log/                              ← 400번대 전 영역 파일 활동 통합 감사 로그
│
├─ 800.outbox/                              ← [최종 산출물 출고장] 대외 제출·외부 연동 단일 정본 (801.deliverables/801-001.pdf ~ 801-004.notebooks · 800.outbox/AGENTS.md)
│
└─ 900.maintenance/                         ← [시스템 엔진·코드·프롬프트·LLM-Wiki·UI 심장부] (900.maintenance/AGENTS.md · 3절 상세 해부)
```

---

### 2.2. 최상위 도메인별(`000` ~ `800`) 핵심 기능 및 전용 도구 매핑표

| 도메인 / 하위 폴더 | 핵심 기능 및 운영 규칙 | 연결된 전용 파이썬 도구 (`.py`) 및 CLI 단축 명령 |
|:---|:---|:---|
| **`000.inbox/`**<br/>임시 수집함 | • 외부 크롤러·다이제스트·빠른 메모가 처음 떨어지는 Flat 임시 버퍼<br/>• 상주 파일 0개 원칙: 분류 즉시 `941.raw` 또는 `420-004.digests`로 자동 이관 | • `,.code-harness.raw-guard.py` (상시 감지·이관)<br/>• `hclassify "<제목>" --body "..."` (도메인·태그 자동 판별) |
| **`100.vault/110.science/`**<br/>46개 정규 학문 리프 | • 자연과학(5)·형식과학(9)·응용과학(13)·사회과학(19) 총 **46개 표준 학문 분과 리프 폴더**로 고정<br/>• 임의 하위 폴더 생성을 금지하고 PMEST v2 `,n_sci,,phys`, `,f_sci,,cs` 기호로 1:1 매핑 | • `915.code-harness/,.code-harness.yaml.py` (`gen_vault_note_from_title()`)<br/>• `912-003.yaml/,.yaml.stem-leaf.yaml` (46개 학문 코드 SSOT)<br/>• `,.py.audit.ruskill-and-py-tools.py` (규격 위반 감사) |
| **`100.vault/150.occupation/`**<br/>전문기술·실무·학습 | • `151.specialized`(엔지니어링, CLI, 옵시디언, 리서치 등 전문 기술)<br/>• `152.work`(업무·아키텍처 정본), `153.learning`(자기주도 학습 노트) | • `houtlink <파일> --apply` (아웃링크 자동 연결)<br/>• `tid <file_id>` / `tlinks <파일>` |
| **`200.plan/`**<br/>루틴·작업지·타임라인 | • `210.routine`: 일간·주간·건강 루틴 관리<br/>• `221.workpaper`: 현재 작업 중인 코드·문서·분석 파일의 **유일한 표면 작업 허용 구역**<br/>• `230.timeline`: 일일 회고(`daily-feedback`) 및 시계열 일정 관리 | • `,.py.cli.token-tools.py` (`tskeleton`, `tslice`, `tsha`)<br/>• `,.py.schedule.checker.py` (루틴·일정 정합성 검증) |
| **`300.areas/`**<br/>장기 프로젝트·지속 영역 | • `310.projects`: 진행 중 프로젝트(`311.active`) 및 장문 집필(`312.writing`)<br/>• `320.domains`: 미디어(`321`), 취미(`322`), 실천 기록(`323`) — 사용자 도메인에 맞게 확장 가능 | • `912-006.json/,.json.entity-canonical-dict.json` (고유명사 영문 표준 사전)<br/>• `,.code-harness.raw-guard.py` (표면/본문 자동 분리) |
| **`400.resource/`**<br/>원본 아카이브 | • **`410.documents`**: 원본 PDF·슬라이드·문서·오디오·노트북을 확장자별 폴더에 보존 (`_inbox` 원본은 절대 삭제/이동 금지, **Copy-Only** 원칙)<br/>• **`420.source`**: 아티클(`420-001`), 도서(`420-002`), 미디어(`420-003`), 다이제스트(`420-004`), 레퍼런스(`420-005`) 5종 고정<br/>• **`430.assets`**: 이미지·다이어그램 자산 및 Bases 썸네일 갤러리 연동 | • `,.code-harness.raw-guard.py` (비정규 중복 폴더 원천 차단)<br/>• `,.py.log.folder-activity.py --profile all` (`490.log` 자동 기록)<br/>• `,.py.pdf.convert.to-md.py` (문서 텍스트 추출) |
| **`800.outbox/`**<br/>최종 산출물 출고장 | • 외부 협업자나 타 시스템에 전달하는 **최종 완성본(PDF, Slides, MD, Notebook)**만 단일 정본으로 배치<br/>• 이전 버전은 `_superseded/` 하위 폴더로 자동 격리하여 오제출 방지 | • `,.code-harness.outcome-verifier.py` (산출물 무결성 검증)<br/>• `,.py.outbox.map-sync.py` (출고 맵 테이블 동기화)<br/>• `tsha <파일>` (SHA12 해시·인코딩·줄 수 1줄 증명) |
| **`<EXTERNAL_PRIVATE_VAULT>/`**<br/>볼트 외부 분리 보안 구역 | • 금융 증빙·SSH 키·복구 키·인증서 같은 민감 파일은 메인 볼트 바깥 별도 디렉터리로 완전 분리하여 Git·클라우드·AI 훅 노출 원천 차단<br/>• AES-256 헤더 암호화 아카이브(`-mhe=on`) + OS ACL 잠금으로 이중 보호 | • OS ACL 잠금 스크립트 (`icacls` / `chmod 700`)<br/>• 암호화 스냅샷 백업 (`Kopia` / `7-Zip AES-256`) |

---

### 2.3. 루트 숨김 시스템 폴더(`.agents` · `.cursor` · `.gemini` · `.obsidian`) & 전역 헌법 지연 로딩(`Lazy Load Map`) — "규칙 갱생" 원리

> **[⚡ 왜 "규칙 갱생(Rule Rehabilitation)"이 핵심인가?]**
> `CLAUDE.md`나 `AGENTS.md` 한 파일에 수천 줄의 모든 세부 규칙을 다 구겨 넣으면 AI가 대화를 시작하자마자 컨텍스트가 꽉 차서 정작 중요한 지시를 무시하는 **'프롬프트 부패(Context Rot)'**가 일어납니다.
> 본 아키텍처는 **① 루트 헌법에는 전역 안전 프로토콜과 `Load Map(길잡이 표)`만 두고**, **② 세부 도메인 규칙은 각 L1 폴더의 `AGENTS.md`로 쪼개어 작업할 때만 지연 로딩(Lazy Loading)**하며, **③ 단일 진실 공급원(`920.prompt/921.skill/`)에서 모든 CLI 숨김 폴더로 자동 동기화(`sync.cli.py`)**합니다.

#### 2.3.1. 전역 헌법(`AGENTS.md` · `CLAUDE.md` · `GEMINI.md`)과 L1 지연 로딩 맵(`Load Map`) 구조

| 계층 구분 | 파일 경로 | 담고 있는 내용 및 토큰 절약 원리 | 검증·동기화 도구 |
|:---|:---|:---|:---|
| **루트 전역 헌법**<br/>(항상 자동 로드) | `AGENTS.md` (Codex/공통)<br/>`CLAUDE.md` (Claude Code)<br/>`GEMINI.md` (Gemini/CLI) | • **데이터 무삭제(`GUARDIAN`)**, 인코딩(`UTF-8 no BOM + LF`), `948.mistake`+`921.skill` 이중 처벌 게이트, 17종 전용 도구 1순위 강제 등 **전역 공통 헌법만 최소한으로 수록**<br/>• 세부 폴더 규칙은 적지 않고 **L1 `AGENTS.md` 라우팅 표(`Load Map`)**만 제시 | • `912-001.py/,.py.sync.cli.py sync`<br/>• `912-001.py/,.py.vault.agents-map.py` (결정적 무결성 검증 `exit 0`) |
| **도메인별 L1 헌법**<br/>(해당 폴더 작업 시에만 열람) | `000.inbox/AGENTS.md`<br/>`100.vault/AGENTS.md`<br/>`200.plan/AGENTS.md`<br/>`300.areas/AGENTS.md`<br/>`400.resource/AGENTS.md`<br/>`800.outbox/AGENTS.md`<br/>`900.maintenance/AGENTS.md`<br/>`.agents/AGENTS.md` | • AI가 특정 도메인(예: `400.resource` 원본 보존, `800.outbox` 최종 출고)을 건드릴 때만 **해당 L1 `AGENTS.md` 단 1개만 열람**<br/>• 전 트리 무차별 탐색을 금지하여 **초기 컨텍스트 토큰 소모량을 90% 이상 감축** | • `,.py.maintenance.autonomous-loop.py` (루트·L1 표면 변조 상시 감시) |

#### 2.3.2. 숨김 에이전트 하네스 폴더 (`.agents/` · `.cursor/` · `.gemini/`) 자동 미러링 구조

| 숨김 시스템 폴더 | 구성 요소 | 핵심 작동 메커니즘 (`921.skill` SSOT 단방향 배포) |
|:---|:---|:---|
| **`.agents/skills/`**<br/>보편 CLI 네이티브 스킬 | `bases`, `browser-automation`, `canvas.json`, `loop`, `markdown`, `test-code-inspector` | • 볼트 본진 `920.prompt/921.skill/`의 정본 지식을 `,.py.sync.cli.py`가 표준 `SKILL.md` 규격으로 자동 미러링<br/>• 멀티 CLI가 작업 도메인 감지 시 해당 스킬만 즉시 호출 |
| **`.agents/plugins/decider-fork-harness/`**<br/>4-Stage OpenDecider Fork 하네스 | **Stage 1** (`:8000` 데몬) → **Stage 2** (`sharp` vs `split`) → **Stage 3** (`Parallel Swarm [3/3]`) → **Stage 4** (`Advisor · on call`) | • **Stage 1**: `opendecider serve`가 단일 순전파 확률 $p$ 계산<br/>• **Stage 2**: **`sharp` ($p \ge 0.85$)**이면 LLM 추론 없이 파이썬 코드로 즉시 결정론적 실행(`runs in code`), **`split` ($p < 0.85$)**이면 메인 코딩 세션(`Leader`/`Builder`/`Worker`)으로 분기<br/>• **Stage 3**: `search`(벡터 DB 탐색), `grep`(호출부 탐색), `audit`(`911.test/audit-wt` Git Worktree 격리 감사) 3병렬 스웜 가동<br/>• **Stage 4**: 평소엔 침묵하고 `before_plan`(계획 전), `same_error_x2`(동일 에러 2회), `before_done`(완료 직전)에만 자문 개입 |
| **`.cursor/` & `.gemini/`**<br/>IDE 규칙·제약 훅 미러 | `.cursor/rules/{blunders,orchestrate,token-efficiency}.mdc`<br/>`.cursor/hooks/intent-constraint-gate.cmd` | • 세션 시작 시 과거 실패 패턴(`948.mistake`)을 물리적으로 선행 인덱싱<br/>• 사용자가 *"X 말고 Y로 해"*라고 지시했는데 대체물을 심으려 하면 `constraint-gate.py`가 파일 쓰기(`Write`)를 즉시 거부(Deny) |

#### 2.3.3. `.obsidian/` 코어 설정 & 추천 플러그인 아키텍처

| 플러그인 분류 | 대표 플러그인 패턴 | 볼트 내 핵심 역할 및 안전 장치 |
|:---|:---|:---|
| **자체 제작 인하우스 가드 패턴**<br/>(안정성·로컬 AI 확장) | • **`llm-wiki-chat`** (Read-Only Local Wiki Assistant)<br/>• **`maintenance-filter`**<br/>• **`safe-startup-mediator`** & **`empty-save-guard`**<br/>• **`folder-note-opener`** & **`status-bar-collapse`** | • **`llm-wiki-chat`**: 옵시디언 우측 패널에서 로컬 AI 데몬(`:18793` Read-Only 커널 감옥)에게 질문하고 각주(`[1]`, `[2]`) 달린 내 노트 근거 답변을 즉시 수신<br/>• **`maintenance-filter`**: `900.maintenance`·`941.raw` 시스템 파일을 검색/Quick Switcher/그래프 뷰/백링크 영역별로 독립 토글해 숨김<br/>• **`safe-startup-mediator` / `empty-save-guard`**: 대형 마크다운 표 렌더링 지연 방지 및 에디터 경쟁 상태로 인한 `0B` 빈 파일 덮어쓰기 원천 차단 |
| **보편 커뮤니티·코어 플러그인**<br/>(지식 탐색·일정·시각화) | • **`folder-notes`** + 코어 **`Bases`**<br/>• **`dataview`** / **`obsidian-tasks-plugin`** / **`obsidian-kanban`**<br/>• **`obsidian-excalidraw-plugin`** / **`drawio-obsidian`**<br/>• **`tag-wrangler`** / **`obsidian-spaced-repetition`** | • **`folder-notes` + `Bases`**: 표면 폴더 클릭 시 안내판(`{folder}.md`)과 데이터베이스 표(`,.base.*`)로 `941.raw` 속 문서를 노션 데이터베이스처럼 깔끔하게 열람<br/>• **`obsidian-tasks-plugin` + `kanban`**: `200.plan` 루틴 및 작업을 보드·타임라인으로 통합 관리<br/>• **`obsidian-spaced-repetition`**: 핵심 지식·개념을 망각곡선(FSRS) 주기에 맞춰 자동 복습 |

---

### 2.4. 핵심 표준 분류 체계 레퍼런스 (`110.science` 46개 학문 · `140.nonacademic` · `410`↔`801` 대칭 구조)

#### 2.4.1. `100.vault/110.science/` — PMEST v2 `46`개 정규 학문 리프 전수 코드표
> **정렬 및 카테고리 철칙**: 각 상위 분과(`111`~`114`) 아래 리프 폴더 번호(`001`~`019`)는 **영문 표준 약어(`abbrev`)의 알파벳 오름차순(ASCII)**으로 영구 고정되어 임의 번호 충돌을 막습니다. 하위 문서의 YAML `personality`는 `[상위분과, 세부약어]`로 통일하고, `tags`는 `{상위약어}/{abbrev}`로 1:1 매핑합니다.

| 상위 학문 분과 (PMEST `P`) | 리프 수 | 정규 학문 리프 폴더 (`{부모}-{번호}.{약어}` : 한글 학문명) 전수 목록 |
|:---|---:|:---|
| **`111.natural-science/`**<br/>자연과학 (`,n_sci`)<br/>태그: `n_sci/<약어>` | **`5`개** | • `111-001.astro` (천문학 Astronomy) · `111-002.biol` (생물학 Biology) · `111-003.chem` (화학 Chemistry)<br/>• `111-004.earth` (지구과학 Earth Science) · `111-005.phys` (물리학 Physics) |
| **`112.formal-science/`**<br/>형식과학 (`,f_sci`)<br/>태그: `f_sci/<약어>` | **`9`개** | • `112-001.cs` (컴퓨터과학 Computer Science) · `112-002.dec` (의사결정론 Decision Theory) · `112-003.dsci` (데이터과학 Data Science)<br/>• `112-004.gt` (게임이론 Game Theory) · `112-005.info` (정보이론 Information Theory) · `112-006.logic` (논리학 Logic)<br/>• `112-007.math` (수학 Mathematics) · `112-008.stat` (통계학 Statistics) · `112-009.syst` (시스템과학 Systems Science) |
| **`113.applied-science/`**<br/>응용과학 (`,a_sci`)<br/>태그: `a_sci/<약어>` | **`13`개** | • `113-001.actuar` (보험수리학 Actuarial Science) · `113-002.agri` (농학 Agriculture) · `113-003.arch` (건축학 Architecture)<br/>• `113-004.dent` (치의학 Dentistry) · `113-005.eng` (공학 Engineering) · `113-006.envsci` (환경과학 Environmental Science)<br/>• `113-007.ergon` (인간공학 Ergonomics) · `113-008.foodsci` (식품과학 Food Science) · `113-009.forens` (법과학 Forensic Science)<br/>• `113-010.hsci` (보건학 Health Science) · `113-011.lis` (문헌정보학 Library & Info Science) · `113-012.med` (의학 Medicine) · `113-013.mil` (군사학 Military Science) |
| **`114.social-science/`**<br/>사회과학 (`,s_sci`)<br/>태그: `s_sci/<약어>` | **`19`개** | • `114-001.anthro` (인류학 Anthropology) · `114-002.archaeo` (고고학 Archaeology) · `114-003.busadm` (행정학 Public/Business Admin)<br/>• `114-004.comm` (커뮤니케이션학 Communication) · `114-005.crim` (범죄학 Criminology) · `114-006.cultst` (문화연구 Cultural Studies)<br/>• `114-007.demo` (인구학 Demography) · `114-008.devst` (개발학 Development Studies) · `114-009.econ` (경제학 Economics)<br/>• `114-010.edsci` (교육학 Education Science) · `114-011.geog` (인문지리학 Human Geography) · `114-012.hist` (역사학 History)<br/>• `114-013.juris` (법학 Jurisprudence) · `114-014.ling` (언어학 Linguistics) · `114-015.mgmt` (경영학 Management)<br/>• `114-016.polisci` (정치학 Political Science) · `114-017.psych` (심리학 Psychology) · `114-018.soc` (사회학 Sociology) · `114-019.socwk` (사회복지학 Social Work) |

#### 2.4.2. `140.nonacademic/` 4대 비학문 격리 구역 & `410.documents/` ↔ `801.deliverables/` 대칭 구조

| 구분 | 폴더 경로 및 세부 구조 | 데이터 무결성 및 운영 철칙 |
|:---|:---|:---|
| **비학문 검증·격리 구역**<br/>(`100.vault/140.nonacademic/`) | • `141.pathological-science/` (병리과학)<br/>• `142.junk-science/` (정크 사이언스)<br/>• `143.pseudoscience/` (유사과학)<br/>• `144.antiscience/` (반과학) | • 과학적 사실(`110.science`)과 섞이면 AI 추론과 RAG 검색이 오염되는 주장을 성격별로 엄격 격리 |
| **원본 바이너리 보존소**<br/>(`400.resource/410.documents/`) | • **특수 버퍼**: `_inbox/` (외부 동기화 유입), `.extracted/` (OCR/STT 추출 텍스트 캐시)<br/>• **확장자별 리프**: `410-001.pdf/`, `410-002.slides/`, `410-003.docs/`, `410-004.md/`, `410-005.audio/`, `410-006.notebooks/` | • **Copy-Only(복사 전용) 절대 불변 원칙**: 외부 기기와 동기화되는 `_inbox/`에서 파일을 잘라내기(Move)하면 원본 기기 파일까지 소실되므로 반드시 **복사(Copy)**로만 정본화<br/>• 원본 바이너리는 `941.raw`에 복제하지 않고 추출된 `.md` 텍스트만 위키로 연결 |
| **최종 산출물 단일 출고장**<br/>(`800.outbox/801.deliverables/`) | • **출고 확장자 리프**: `801-001.pdf/`, `801-002.slides/`, `801-003.md/`, `801-004.notebooks/`<br/>• **버전 격리 및 감사**: `_superseded/` (구버전 자동 격리), `801-999.log/` (출고 이력 감사 로그) | • 원본 자료(`410.documents`)는 수정 없이 보존하고, 외부 전달용으로 최종 완성·검증된 **단일 정본 1개만** 배치<br/>• 재발행 시 이전 파일은 즉시 `_superseded/`로 자동 격리하여 구버전 오발송을 0%로 차단 |

---

## 3. `900.maintenance` 심장부 5대 서브시스템(`910` ~ `950`) 명세

> **[💡 `900.maintenance`가 하는 일]**
> 옵시디언 볼트 전체를 사람이 수동 정리하지 않아도 자율적으로 유지되게 만드는 **운영체제(OS) 커널**입니다. 코드(`910`), 프롬프트/스킬(`920`), 에이전트 협업(`930`), LLM-Wiki 지식 엔진(`940`), UI 시각화(`950`) 5개 계층으로 분리되어 작동합니다.

### 3.1. `910.codey/` — 실행 코드 · 7-Layer 테스트 게이트 · 벡터 DB · 46대 코드 하네스

| 하위 폴더 | 폴더 기능 및 아키텍처 역할 | 내부 핵심 파일 · 도구 (`.py` / 셸 / 모델) |
|:---|:---|:---|
| **`911.test/`**<br/>7-Layer 코드 검수·스테이징 허브 | • 새로 짜거나 수정한 모든 파이썬 코드를 본진(`912-001.py`)에 넣기 전 시험 가동하는 격리 검증소<br/>• **7-Layer 오픈소스 정적·동적 분석(`ruff`, `pyflakes`, `mypy`, `bandit`, `vulture`, `pytest`) + 볼트 실패 예방 규칙(`R-PY-01..10`)**을 통과해야만 `912-001.py` 및 `921-000.cskill`로 승격 허용 | • `tcheck <파일.py> [--autofix] [--promote]`<br/>• `912-001.py/,.py.test.protocol-gate.py`<br/>• `.ruff.toml` (린트 규칙 SSOT)<br/>• `,.test.status.md` (멀티 에이전트 공용 테스트 상태판) |
| **`912.vein/912-001.py/`**<br/>정식 Python 실행 스크립트 허브 | • 볼트 내 모든 자동화 작업을 수행하는 정식 파이썬 스크립트 저장소<br/>• 파일명 규칙: `,.py.<도메인>.<동작>.py` 형식으로 통일하여 이름만 보고 기능 즉시 파악 | • `,.py.cli.token-tools.py` (17종 CLI 단축 도구 통합 엔진)<br/>• `,.py.llm-wiki.engine.py` (`942.wiki` 위키 허브 컴파일러)<br/>• `,.py.memory.harness.py` & `,.py.yaml.mistake.py` (`948.mistake` 실수 원장 기록)<br/>• `,.py.registry.register-path.py` (신규 문서 `file_id` 즉시 발급)<br/>• `,.py.rename.vault.py` & `,.py.rename.folder.py` (전역 안전 치환·리네임)<br/>• `,.py.sync.cli.py` (멀티 CLI 스킬/헌법 통합 싱크)<br/>• `,.py.audit.ruskill-and-py-tools.py` (볼트 전체 규칙·도구 사용 감사) |
| **`912.vein/912-002` ~ `912-010`**<br/>구조화 데이터 프레임 허브 | • 코드와 데이터를 분리하기 위해 확장자별로 표준 프레임과 사전을 모아둔 데이터 혈관(Vein) | • `912-002.mjs/`: Node.js ESM 스크립트 (`,.mjs.*`)<br/>• `912-003.yaml/`: 노트 생성 표준 YAML 템플릿(`,.yaml.frame.*.yaml`) 및 폴더 번호 사전(`,.yaml.stem-leaf.yaml`)<br/>• `912-004.md/`: 워크플로우 프롬프트 데이터<br/>• `912-006.json/`: **고유명사 정본 사전(`,.json.entity-canonical-dict.json`)** 및 캘리브레이션 캐시<br/>• `912-007.base/`: 옵시디언 Bases 데이터베이스 뷰 스키마<br/>• `912-008.txt/`: 외부 RAG 업로드용 압축 텍스트 번들<br/>• `912-009.npz/`: 768차원 경량 임베딩 행렬 캐시<br/>• `912-010.csv/`: 정형 CSV 데이터셋 |
| **`913.shell/`**<br/>원클릭 런처 & 백그라운드 스케줄러 | • 부팅·로그온 시 검은 창(콘솔) 깜빡임 없이 백그라운드 데몬을 띄우고 멀티 CLI 워크스페이스를 여는 셸 스크립트 모음 | • `913-001.bat/`: Windows CLI 래퍼 및 운영 런처<br/>• `913-002.vbs/`: Windows 창 숨김 실행 래퍼 (`wscript.exe` 연동)<br/>• `913-003.xml/`: Windows 작업 스케줄러 등록용 정규 XML<br/>• `913-004.ps1/` & `913-005.sh/`: PowerShell / POSIX 셸 오케스트레이션 스크립트<br/>• `913-006.icon/`: 전용 아이콘 리소스 |
| **`914.data/`**<br/>무료 로컬 AI 모델 & LanceDB 벡터 창고 | • 클라우드 API 비용 0원으로 검색·분류·판단을 수행하는 로컬 ONNX 가중치와 LanceDB 하이브리드 벡터 데이터베이스 | • `914-010.lance/`: **3대 핵심 LanceDB 테이블** (`llm_wiki.lance`, `skills_compact.lance`, `selective_docs.lance`)<br/>• `914-009.onnx/`: 로컬 임베딩·교차 리랭커 ONNX 모델 가중치 |
| **`915.code-harness/`**<br/>`46`대 자율 방어·복구 코드 하네스 센터 | • AI의 실수(파일 무단 삭제, 잘못된 폴더 생성, 링크 파괴, 질책 후 미기록)를 커널 레벨에서 물리적으로 차단·자동 복구하는 `46`개 모듈 패턴 | • `,.code-harness.raw-guard.py`: 표면 `.md`를 `941.raw`로 자동 이관하고 `,.map.*` / `,.base.*` 실시간 갱신<br/>• `,.code-harness.safety-governor.py`: 등록된 중요 파일(`file_id`)에 대한 `unlink`/`rmtree` 무단 삭제 물리 차단(`R-SAFE-06`)<br/>• `,.code-harness.sentinel.py`: 실수 발생 시 `948.mistake` + `921.skill` 이중 기록 누락 적발 및 `COMPLIANCE_BLOCKED` 처벌 집행<br/>• `,.code-harness.yaml.py`: `gen_vault_note_from_title()`로 PMEST v2 파일명 + 표준 YAML + `file_id` 원클릭 생성<br/>• `,.code-harness.decider-engine.py` & `harrier-decider.py`: 임베딩 + 리랭커 + 확률 라우터 로컬 판단 엔진<br/>• `,.code-harness.file-tracker.py` & `id.py`: 10자리 `file_id` 기반 O(1) 파일 위치 추적기<br/>• `,.code-harness.outlink-healer.py` & `path-healer.py`: 깨진 위키링크 및 경로 자동 치유<br/>• `,.code-harness.fault-tolerance.py` & `recovery-harness.py`: 백그라운드 부하 제어 및 장애 자가 복구 |
| **`919.logs/`**<br/>시스템 실행·처벌·감사 로그 | • 모든 코드 데몬, 자율 루프(`autonomous-loop`), 테스트 게이트 실행 로그 및 AI 처벌 원장 보관 (`.gitignore` 제외) | • `,.json.cli-punishment-ledger.json` (AI 실수·처벌 원장)<br/>• `blunder-index.md` (CLI 실패 패턴 인덱스) |

---

### 3.2. `920.prompt/` — 6종 모듈형 스킬(`921.skill`) & 베이킹(`922.baking`)

| 하위 폴더 | 파일 접두어 | 핵심 기능 및 아키텍처 역할 |
|:---|:---|:---|
| **`921-000.cskill/`**<br/>도구 프로토콜 스킬 | `,.cskill.*` | • `912-001.py`의 각 파이썬 스크립트와 1:1로 매핑되는 실행 매뉴얼 (`code_id`로 스크립트와 직결)<br/>• AI가 코드를 중복 작성하는 대신 기존 검증된 `.py` 도구를 즉시 찾아 실행하게 유도 |
| **`921-100.txskill/`**<br/>텍스트·플러그인 스킬 | `,.txskill.*` | • 마크다운 작성 포맷, 폴더 노트 규격(`folder-note.convention`), 옵시디언 플러그인 제어 규칙 |
| **`921-200.taskill/`**<br/>실무·도메인 업무 스킬 | `,.taskill.*` | • 브라우저 자동화, 문서 파이프라인, 공개 저장소 정제(`git.public-repo-sanitize`), 도메인별 실전 업무 노하우 집약 |
| **`921-300.corskill/`**<br/>코어·블런더 방지 스킬 | `,.corskill.*` | • GUARDIAN 무삭제 원칙 및 각 AI CLI별 반복 오판(Blunder) 차단 방어막 |
| **`921-700.ruskill/`**<br/>단일 진실 공급원(SSOT) 헌법 | `,.ruskill.*` | • 볼트 전체의 폴더 명명 규칙(`name.folder.*`), PMEST v2 파일 기호(`name.file.vault.symbol`), YAML 속성 규칙(`yaml.vault.prop.*`), 실수 기록 규격(`name.file.mistake`)을 정의하는 절대 헌법 |
| **`921-800.wfskill/`**<br/>표준 워크플로우 스킬 | `,.wfskill.*` | • 여러 도구를 순서대로 엮어 실행하는 파이프라인 절차서 (예: `audit.ruskill-and-py-tools`, `rename`) |
| **`922.baking/`**<br/>초기 진입 복합 프롬프트 | `,.baking.*` | • 세션 시작 시 작업 목적에 따라 필요한 규칙+스킬+코드를 한 번에 묶어 주입하는 상위 진입점 (`hroute`로 자동 선택) |
| **`928.custom/`**<br/>커스텀 시스템 프롬프트 | `,.custom.*` | • 각 CLI 에이전트별 커스텀 시스템 프롬프트 미러 및 역할 정의서 |

---

### 3.3. `930.agent/` — 멀티 CLI 실시간 협업 · 작업 원장 · 불변 감사 로그

| 하위 폴더 | 핵심 기능 및 작동 방식 | 주요 파일 및 도구 |
|:---|:---|:---|
| **`931.session/931-001.multi-cli/`**<br/>멀티 CLI 실시간 화면 공유소 | • 멀티 패인 터미널(`Leader`, `Builder`, `Worker`, `Verifier`) 각 세션의 최근 출력을 경량 마크다운 스냅샷으로 공유<br/>• 다른 AI의 진행 상황을 전체 로그 없이 단일 인덱스 파일로 즉시 파악 | • `,.session.share.md` (전체 패널 상태 요약 인덱스 — 1순위 열람)<br/>• `,.session.share.<role>.md` (역할별 최근 스냅샷) |
| **`932.tasks/932-001.multi-cli/`**<br/>자동 기록 작업 계획서 | • 멀티 에이전트 작업 배정표 및 완료 기계 검증 로그 (`## Auto log`는 하네스가 자동 append) | • `,.tasks.board.md` (역할별 작업 할당 및 검증 이력) |
| **`939.logs/`**<br/>불변 세션 감사 로그 | • 모든 CLI 세션의 대화 이력과 정제된 리서치 수확본을 영구 보존하는 Append-Only 블랙박스 | • `,.log.<cli>.<timestamp>.md` |

---

### 3.4. `940.llm-wiki/` — 오픈소스 강화형 LLM-Wiki 3계층 엔진 (`941.raw` · `942.wiki` · `948.mistake`)

| 하위 폴더 | 핵심 기능 및 자동화 메커니즘 | 전용 도구 및 명령어 |
|:---|:---|:---|
| **`941.raw/`**<br/>비경유 무손실 마크다운 정본 창고 | • `000.inbox`~`800.outbox`의 모든 일반 마크다운(`.md`) 노트가 실제 저장되는 무손실 격리 보존소<br/>• 도메인별 전체 파일 인벤토리 매니페스트(`,.raw.folders-overview.md`, `,.raw.list.<domain>.md`)를 위키링크 오염 없이 코드스팬으로 자동 유지 | • `,.code-harness.raw-guard.py` (실시간 자동 이관 및 맵/베이스 동기화)<br/>• `,.raw.folders-overview.md` (전체 폴더 인벤토리) |
| **`942.wiki/`**<br/>아웃링크 위키 허브 & 6대 온톨로지 | • **`942-100.overview/`**: 전체 위키 총괄·그래프·린트·합성 허브<br/>• **`942-200.entities/`**: PMEST 6대 온톨로지 축별 자동 분류 위키 (`personality`, `space`, `target`, `matter`, `energy`, `concepts`, `code-ontology`)<br/>• **`942-300.sources/`**: 외부 소스 매핑 허브<br/>• **`942-400.refinery/`**: `000.inbox`~`900.maintenance` 도메인별 정제 아웃링크 허브 | • `twiki "<질의>" --top 6` (로컬 SLM + 1-hop 위키 허브가 클라우드 토큰 0원으로 브리핑)<br/>• `tgrep "<질의>" --top 6` (로컬 AI + AST 스마트 Grep)<br/>• `,.py.llm-wiki.engine.py` (위키 컴파일러) |
| **`948.mistake/`**<br/>AI 실수·반성 전용 원장 | • AI가 범한 코딩 오류, 규칙 위반, 환각 사건을 기록하는 반성문 저장소<br/>• 직접 파일 수정 금지 — 반드시 전용 하네스 명령어로만 기록하며, `921.skill` 재발 방지 규칙과 1:1로 묶여 작동 | • `,.py.memory.harness.py auto-capture --model <cli> --subject <slug> --content "..." --skill-path <스킬경로>`<br/>• `,.py.embedding.memory.py search-text "<키워드>"` |
| **`949.logs/`**<br/>위키 엔진 상태 로그 | • LLM-Wiki 컴파일러 및 로컬 추론 엔진의 빌드 상태·소요 시간·토큰 절감률 기록 | • `,.py.llm-wiki.engine.py status` |

---

### 3.5. `950.UI/` — 순수 Python 100% 자동 생성 아웃링크 맵 & 대시보드

| 하위 폴더 | 핵심 기능 | 전용 생성 도구 |
|:---|:---|:---|
| **`951.map/`**<br/>인터랙티브 아웃링크 맵 | • 볼트 내 연결망을 파이썬 스크립트가 분석하여 색상·군집화된 인터랙티브 아웃링크 지도 노트로 자동 생성 | • `912-001.py/,.py.graph.color-and-map.py`<br/>• 각 폴더 내 `,.map.<폴더명>.md` & `,.base.<폴더명>.base` |
| **`952.homepage/`**<br/>메인 홈페이지 대시보드 | • 옵시디언을 켜자마자 보이는 통합 관제 대시보드 (최근 작업, 일정, 위키 허브를 한눈에 표시) | • Dataview & Bases 통합 뷰 |

---

## 4. `940.llm-wiki` 3계층 아키텍처 & 클라우드 vs 로컬 하네스 이원화 해부

일반적인 위키 운영 방식은 사람이 수동으로 폴더를 정리하거나 고비용 클라우드 토큰으로 위키를 갱신합니다. 반면 본 아키텍처는 **파이썬 코드 하네스와 무료 로컬 AI가 100% 자동으로 관리하는 3계층 구조**입니다.

### 4.1. 제1계층: `941.raw/` (원본 마크다운 자동 격리 + 뷰 분리 원칙)
- **핵심 규칙**: `100.vault`, `200.plan`(`221.workpaper` 제외), `300.areas`, `400.resource` 표면 폴더에는 **폴더 안내판(`{폴더명}.md`), 아웃링크 지도(`,.map.*`), 데이터베이스 표(`,.base.*`), `AGENTS.md`만** 남겨둡니다.
- **자동 라우팅 (`raw-guard.py`)**: 실제로 작성되는 지식 마크다운(`.md`) 노트는 전부 **`900.maintenance/940.llm-wiki/941.raw/<원래폴더경로>/`** 안에 생성됩니다. 만약 바깥 폴더에 파일을 잘못 만들어도 상시 감시 코드(`raw-guard.py`)가 즉시 `941.raw`로 안전하게 옮기고 지도(`,.map.*`)와 베이스(`,.base.*`) 표를 자동으로 갱신합니다.
- **장점**: 탐색기 폴더가 수천 개 파일로 뒤덮이는 시각적 피로를 0%로 만들면서 원본 데이터는 손실 없이 보존합니다.

### 4.2. 제2계층: `942.wiki/` (도메인 아웃링크 위키 허브 & 6대 온톨로지 축 자동 컴파일)
- **작동 원리**: 위키 엔진(`912-001.py/,.py.llm-wiki.engine.py`)이 `941.raw`와 볼트 전체 마크다운 문서 및 폴더를 스캔하여 **`942-100.overview`부터 `942-400.refinery`까지 정제된 아웃링크 위키 허브**를 자동으로 컴파일·동기화합니다.
- **옵시디언 아웃링크(`[[파일명]]`) + 고유 `file_id` 결합**: 모든 문서에 10자리 고유 번호(`file_id`)와 `ids_outlink`를 심어두기 때문에 파일 이름을 바꾸거나 폴더를 옮겨도 링크가 깨지지 않고 O(1) 속도로 추적됩니다.
- **6대 온톨로지 축 자동 분류 (`942-200.entities/`)**: `personality`(도메인/학문 분과), `space`(공간/소속/플랫폼), `target`(고유 대상/기술/작품), `matter`(핵심 소재/개념), `energy`(행위/과정/기능), `concepts`(개념 및 역할 타입)별로 자동 집계합니다.

### 4.3. 제3계층: `948.mistake/` (AI 실수 원장 & 이중 처벌 게이트)
- **핵심 규칙**: AI가 코드를 잘못 작성하거나 규칙을 위반하면 수정에 착수하기 전 반드시 **`948.mistake`에 실패 원인을 선기록**하고 **관련 `921.skill` 문서에 영구 재발 방지 규칙을 동시에 추가(이중 강제 기록)**해야 합니다.
- **자동 처벌 게이트 (`sentinel.py`)**: AI가 실수 기록이나 스킬 업데이트를 누락한 채 완료를 주장하면 감시 코드(`cli_mistake_and_skill_punishment_gate_ok`)가 즉시 **`COMPLIANCE_BLOCKED`(승인 거부 및 강제 재작업)** 처벌을 집행합니다.

### 4.4. 클라우드 CLI 하네스 vs 무료 로컬 모델(SLM·임베딩) 코드 하네스 이원화 비교표

> **[⚡ 왜 하네스(고삐)를 2중으로 나누는가?]**
> 클라우드 LLM에게는 **'실수 방지 규칙과 커널 차단막'**을 씌워 설계·구현 품질을 극대화하고, 파일 검색·분류·단순 라우팅은 **로컬 GPU/CPU에서 무료로 동작하는 '로컬 소형 모델 + 벡터 DB 하네스'**에 전담시켜 **유료 토큰 낭비를 최소화**합니다.

| 비교 항목 | ① 폴더·프롬프트 기반 클라우드 CLI 하네스<br/>(`Claude / Codex / Gemini / Cursor 등`) | ② 로컬 모델(SLM·임베딩) & 벡터 DB 코드 하네스<br/>(`Embedding` · `LanceDB` · `Reranker` · `OpenDecider` · `Read-Only Assistant`) |
|:---|:---|:---|
| **담당 역할** | 고난도 아키텍처 설계, 복합 코드 구현, 심층 팩트체크, 최종 보고서 집필 | **대규모 문서·스킬 0.1초 검색**, 노트 분류, 아웃링크 추천, 단순 명령 자동 실행, 읽기 전용 질의응답 |
| **토큰 비용** | API/구독 쿼터 소모 (`tskeleton`/`tslice`로 85~95% 절감) | **클라우드 토큰 소모 `0원` (100% 로컬 추론)** |
| **핵심 구성 요소** | • `AGENTS.md` L1 지연 로딩 맵 (`Load Map`)<br/>• `921.skill` 6종 모듈형 프롬프트 분리<br/>• **`raw-guard.py`**: 표면 폴더 일반 `.md` 오염 물리 차단 및 `941.raw` 이관<br/>• **`safety-governor.py`**: `file_id` 등록 파일 무단 삭제(`unlink`/`rmtree`) 커널 차단<br/>• **`sentinel.py`**: `948.mistake` + `921.skill` 이중 기록 누락 시 `COMPLIANCE_BLOCKED` 처벌<br/>• **`tcheck`**: `911.test` 7-Layer 정적·동적 코드 승격 게이트 | • **고밀도 로컬 임베딩 (`1024d` ONNX/CUDA)**: `Harrier-OSS-v1-0.6B` / `BGE-M3` 계열<br/>• **`LanceDB` (`914-010.lance`)**: `llm_wiki.lance`, `skills_compact.lance`, `selective_docs.lance` 3대 하이브리드 DB<br/>• **Cross-Encoder 리랭커**: `Jina-Reranker-v3.5` / `BGE-Reranker` 정밀 교차 재순위화<br/>• **`OpenDecider` (`:8000`)**: 신뢰도 $P \ge 0.85$ 달성 시 LLM 없이 파이썬 도구 즉시 자동 실행(`sharp`)<br/>• **Local Read-Only Assistant (`:18793`)**: `sys.addaudithook` 커널 레벨 Read-Only 감옥 위에서 안전한 위키 브리핑 수행 |
| **무인 자가 치유 & 동기화 데몬** | • **`maintenance.autonomous-loop.py`**: `AGENTS.md`·`922.baking`·`921.skill` 핵심 표면 무결성 상시 점검 | • **`embedding.auto-sync.py`**: 30분 주기로 변경된 노트만 무창 증분 임베딩 갱신 후 `:18793/reload`로 무중단 핫리로드 |

---

## 5. 파일 이름만 봐도 내용이 다 보이는 `PMEST v2 + UDC` 네이밍 & 안전 리네임 규칙

> **[⚡ 한 줄 결론]**
> 문헌정보학의 고전 분류법(**랑가나단의 PMEST 5대 패싯**)을 단일 기호로 압축해 파일 이름에 내장합니다. 이 규칙 덕분에 **AI가 파일을 열어보지 않고 제목만 봐도 도메인·대상·공간·소재·행위·문서 역할을 100% 정확하게 해독**합니다.

### 5.1. PMEST v2 파일명 공식
```text
,{상위P},,{하위P}[{대상}][~{공간}][;{물질}][!{에너지}][@{방법론_또는_정보성종류}].{역할type}.md
```

### 5.2. 기호별 의미와 보편 예시 (해독표)
| 순서 | 기호 | 패싯 이름 (YAML 속성) | 뜻 (무엇을 적는가?) | 보편 예시 |
|:---:|:---:|:---|:---|:---|
| **1** | **`,`** / **`,,`** | **P (Personality / 도메인)**<br/>`personality: ["상위", "하위"]` | 가장 큰 학문/분야와 세부 분야 | `,f_sci,,cs` (형식과학 > 컴퓨터과학)<br/>`,a_sci,,eng` (응용과학 > 공학)<br/>`,occ,,dev` (직무 > 소프트웨어 개발) |
| **2** | **`{ }`** | **Target (고유 대상)**<br/>`target: ["..."]` | 특정 기술, 프로젝트, 오픈소스, 인물, 표준 등 고유 대상 (`entity-canonical-dict.json` 정본 영문) | `{transformer}`<br/>`{linux}`<br/>`{python+rust}` |
| **3** | **`~`** | **S (Space / 공간·소속)**<br/>`space: ["..."]` | 국가, 기관, 플랫폼, 표준 단체, 출처 공간 | `~arxiv`<br/>`~kernel`<br/>`~github` |
| **4** | **`;`** | **M (Matter / 물질·핵심 소재)**<br/>`matter: ["..."]` | 문서가 다루는 핵심 재료·단일 개념·피행위 대상 | `;attention-mechanism`<br/>`;scheduler`<br/>`;ci-pipeline` |
| **5** | **`!`** | **E (Energy / 에너지·행위·사건)**<br/>`energy: ["..."]` | 행위, 과정, 문제 해결, 기능 동작 | `!benchmark`<br/>`!bug-fix`<br/>`!deploy` |
| **6** | **`@`** | **Method (방법론·정보 종류)**<br/>`method: ["..."]` | 학술 방법론(`@comparative`, `@structural`, `@causal`) 또는 정보 종류(`@guide`, `@spec`, `@dic`, `@review`)가 명확할 때만 표기 (일반 노트는 생략) | `@comparative`<br/>`@spec`<br/>`@guide` |
| **7** | **`.note.md`** | **Role Type (문서 역할)**<br/>`type: ["note"]` | 일반 지식 문서는 무조건 `.note.md` (시스템 문서는 `.cskill.md`, `.taskill.md`, `.rule.md`, `.wiki.md`) | `.note.md` |

- **완성 예시**:
  - `,f_sci,,cs{transformer}~arxiv;attention-mechanism!benchmark@comparative.note.md`
  - `,a_sci,,eng{linux}~kernel;scheduler!bug-fix@spec.note.md`
  - `,occ,,dev~github;ci-pipeline!deploy@guide.note.md`
- **결합 기호 3원칙**:
  1. `_` (언더바): 한 단어 내부의 띄어쓰기 연결 (예: `linear_algebra`)
  2. `-` (하이픈): 같은 패싯 안에서 전체-부분(상하위) 연결 (예: `attention-mechanism`)
  3. `+` (플러스): 같은 패싯 안에서 대등한 두 대상 알파벳순 병렬 연결 (예: `{python+rust}`)
- **자동 생성 도구**: 사람이 기호를 수동으로 입력할 필요 없이 `915.code-harness/,.code-harness.yaml.py`의 **`gen_vault_note_from_title()`** 함수를 호출하면 파일명, YAML Frontmatter, 아웃링크, `file_id` 등록까지 자동 생성됩니다.

### 5.3. 폴더·파일 리네임(Rename) 안전 프로토콜 & 10자리 불변 `file_id` · 8대상 연쇄 최신화 매트릭스

> **[🚨 절대 금기: 탐색기(Explorer)나 파인더(Finder)에서 수동으로 폴더·파일 이름 바꾸기 금지!]**
> 탐색기에서 이름을 직접 바꾸면 폴더 노트(`{폴더명}.md`), 아웃링크, 파이썬 스크립트 경로, 동기화 미러가 동시에 깨집니다. 반드시 **원자적(Atomic) 리네임 도구 + 10자리 불변 `file_id` + 8대상 연쇄 점검**을 사용합니다.

#### 5.3.1. 링크가 깨지지 않는 3중 안전 장치

| 안전 장치 | 핵심 도구 / 메커니즘 | 무결성 보장 원리 |
|:---|:---|:---|
| **① 10자리 불변 `file_id`**<br/>(영구 식별자 원리) | • `,.py.registry.register-path.py --path <파일>`<br/>• `,.code-harness.file-tracker.py` & `tid <file_id>` | • 모든 문서 상단 YAML에 고유 10자리 ID(예: `file_id: "ex7k9m2p4q"`)와 `ids_outlink`를 부여<br/>• 파일 이름을 전면 개편하거나 폴더를 이동해도 `tid ex7k9m2p4q` 명령으로 **O(1) 속도로 실제 경로 추적** |
| **② 폴더 원자적 리네임**<br/>(`rename.folder.py`) | • `,.py.rename.folder.py OLD NEW --rename-files`<br/>• `,.py.folder.rename.sync.py` | • 폴더명 변경 시 ① 폴더 자체, ② 내부 폴더 노트(`{folder}.md`), ③ 하위 파일 접두어, ④ `,.map.*` 및 `,.base.*` 뷰, ⑤ `941.raw` 대응 경로를 **단일 트랜잭션으로 동시 변경** |
| **③ 볼트 전역 일괄 치환**<br/>(`rename.vault.py`) | • `,.py.rename.vault.py --batch "OLD>NEW,..."`<br/>• `,.code-harness.outlink-healer.py` | • 볼트 전체 마크다운 본문 위키링크, YAML 속성, 파이썬 스크립트 참조 문자열을 누락 없이 일괄 치환하고, 잔여 끊어진 링크는 `outlink-healer.py`가 자동 복구 |

#### 5.3.2. 구조·이름 변경 시 필수 `8대상 연쇄 최신화 매트릭스`

| 순번 | 점검 대상 (8-Target) | 확인 및 갱신 내용 |
|:---:|:---|:---|
| **1** | **`memory` (`948.mistake`)** | 기존 구조 결함이나 변경 사유가 실수/개선 이력에 기록되었는가? |
| **2** | **`stem-leaf.yaml`** | `912-003.yaml/,.yaml.stem-leaf.yaml`의 폴더 번호·약어 매핑 사전에 반영되었는가? |
| **3** | **`filename` (파일·폴더명)** | 실제 디스크 및 `941.raw` 상의 파일·폴더명이 PMEST v2 규격에 맞게 변경되었는가? |
| **4** | **`README / 폴더노트`** | 해당 폴더의 `{folder}.md`, `,.map.<folder>.md`, `,.base.<folder>.base`가 갱신되었는가? |
| **5** | **`py code` (`912-001.py`)** | 관련 파이썬 스크립트 및 `915.code-harness`의 경로 상수가 최신화되었는가? |
| **6** | **`AGENTS.md` (`CLAUDE`/`GEMINI`)** | 루트 및 해당 L1 도메인 `AGENTS.md` 안내 표에 변경 사항이 반영되었는가? |
| **7** | **`921.skill` (스킬 문서)** | 도구를 호출하는 `cskill`·`taskill`·`wfskill` 매뉴얼과 `.agents/skills` 미러가 동기화되었는가? |
| **8** | **`921-700.ruskill` (SSOT 법전)** | 최상위 규칙 문서(`ruskill`)에 영구 헌법으로 반영되었는가? |

---

## 6. `920.prompt/921.skill` — AI를 정확하게 제어하는 6종 스킬 분리 체계

하나의 거대한 프롬프트에 모든 지시를 몰아넣으면 정확도가 급락하고 토큰만 낭비됩니다. 스킬을 **역할별로 6개 폴더로 분리해 필요한 순간에만 필요한 조각을 로드**합니다.

| 폴더 번호 및 이름 | 파일 접두어 | 역할 (무엇을 담는가?) |
|:---|:---|:---|
| **`921-000.cskill/`** | `,.cskill.*` | **코드 실행 스킬 (Code Skill)**: `912-001.py`의 파이썬 스크립트와 1:1로 연결되어 검증된 코드를 즉시 실행시키는 도구 설명서 |
| **`921-100.txskill/`** | `,.txskill.*` | **텍스트·플러그인 스킬 (Text Skill)**: 마크다운 문서 작성 서식, 옵시디언 플러그인 조작 규칙 |
| **`921-200.taskill/`** | `,.taskill.*` | **업무·도메인 스킬 (Task Skill)**: 도메인별 실무 노하우, 브라우저 자동화, 공개 저장소 정제 등 재사용 절차 |
| **`921-300.corskill/`** | `,.corskill.*` | **코어·블런더 방지 스킬 (Core/Blunder Skill)**: GUARDIAN 원칙 및 각 AI CLI가 자주 저지르는 반복 실수(Blunder) 차단 방어막 |
| **`921-700.ruskill/`** | `,.ruskill.*` | **절대 규칙 사전 (Rule Skill — SSOT)**: 폴더 이름 규칙, 파일명 기호 규칙, YAML 속성 규칙 등 볼트 전체의 헌법(단일 진실 공급원) |
| **`921-800.wfskill/`** | `,.wfskill.*` | **워크플로우 스킬 (Workflow Skill)**: 여러 단계의 작업을 순서대로 처리하는 표준 작업 절차서 |

---

## 7. 전용 CLI 단축 도구 17종 치트시트 & 4대 실전 인터페이스 계층

> **[⚡ 왜 이 도구들을 쓰는가?]**
> 수천 줄짜리 파일이나 대규모 문서 집합을 AI에게 통째로 읽히면 토큰 비용이 폭발합니다. 아래 단축 명령어(`VaultCli` 래퍼 및 `,.py.cli.token-tools.py`)를 사용하면 **로컬 AI와 파이썬 AST가 필요한 줄(Line)만 핀셋 추출하여 토큰을 85~95% 절감**합니다.

### 7.1. 로컬 AI + 벡터 DB 검색·라우팅 도구 (6종)
| 명령어 | 하는 일 (한 줄 설명) | 사용 예시 |
|:---|:---|:---|
| **`twiki`** | 클라우드 토큰 0원으로 로컬 AI가 `942.wiki` 위키 허브 + 1-hop 아웃링크를 읽고 브리핑 | `twiki "LLM 위키 구조와 941.raw 역할" --top 6` |
| **`tgrep`** | 전 트리 `grep` 대신 로컬 AI + 파이썬 AST + 벡터 DB로 정확한 줄 번호만 추출 | `tgrep "gen_vault_note_from_title" --top 6` |
| **`hsearch`** | 고밀도 임베딩 + `LanceDB` + Cross-Encoder 리랭커 하이브리드 정밀 검색 | `hsearch "옵시디언 파일명 규칙" --table auto --top 5` |
| **`hsolve`** | 검색 후 판단 AI(`OpenDecider`) 확률이 85% 이상이면 파이썬 도구까지 자동 실행 | `hsolve "깨진 아웃링크 자동 복구" --execute` |
| **`hskill`** | 현재 작업에 딱 맞는 `921.skill` 문서와 연결된 `.py` 코드만 선별 | `hskill "노트 생성 및 YAML 검증" --top 5` |
| **`hroute`** | 어떤 `922.baking` 상위 프롬프트와 실행 모드로 돌릴지 자동 라우팅 | `hroute "일일 볼트 감사" --fast` |

### 7.2. 노트·링크·스펙 자동화 마이크로 도구 (4종)
| 명령어 | 하는 일 (한 줄 설명) | 사용 예시 |
|:---|:---|:---|
| **`houtlink`** | 문서의 `ids_outlink`(연관 문서 링크), `rule_id`, `skill_id`, `code_id`를 임베딩으로 자동 채움 | `houtlink <파일경로> --apply --fast` |
| **`hspec`** | 스킬/규칙 문서 상단의 `skill_spec` / `rule_spec` 블록을 자동 생성 | `hspec <파일경로>` |
| **`hclassify`** | 새 노트의 도메인(`personality`), 태그(`tags`), 목적지 폴더, 아웃링크를 자동 분류 | `hclassify "Transformer Attention Benchmark" --body "..."` |
| **`hcode`** | 스킬 문서에 대응하는 `912-001.py` 파이썬 스크립트를 찾아 자동 연결 | `hcode <파일경로>` |

### 7.3. 토큰 90% 절약형 파일 열람 & 7-Layer 코드 검수 도구 (7종)
| 명령어 | 하는 일 (한 줄 설명) | 사용 예시 |
|:---|:---|:---|
| **`tskeleton`** | 파일 전체를 읽지 않고 **YAML 요약 + 마크다운 제목 트리 / 파이썬 함수 목록과 줄 번호**만 1초 만에 추출 | `tskeleton <파일경로> --max-lines 80` |
| **`tslice`** | `tskeleton`으로 확인한 **특정 섹션 제목이나 함수, 줄 범위(`100:180`)만 핀셋 추출** | `tslice <파일경로> 100:180` |
| **`tid`** | 10자리 `file_id` ↔ 실제 파일 경로 즉시 변환 (파일 이름이 바뀌어도 O(1) 탐색) | `tid ex7k9m2p4q` |
| **`tlinks`** | 문서 안에 적힌 `ids_outlink` ID들이 가리키는 실제 파일 경로 일괄 해석 | `tlinks <파일경로>` |
| **`tsha`** | 파일이 디스크에 무결하게 저장되었는지 **SHA12 해시 + UTF-8 인코딩 + 줄 수** 1줄 검증 | `tsha <파일경로>` |
| **`tdiff`** | 토큰 폭탄 없는 초경량 `git status` 및 변경 줄 수 요약 | `tdiff --stat` |
| **`tcheck`** | **7-Layer 오픈소스 정적분석(`ruff`·`pyflakes`·`mypy`·`bandit`·`vulture`·`pytest`) + 실패 예방 규칙 검수 게이트** | `tcheck <파일.py> --autofix` |

### 7.4. 일상 워크플로우와 볼트를 잇는 4대 실전 인터페이스 패턴

| 실전 인터페이스 | 구동 포트 / 프로토콜 | 핵심 작동 원리 및 가치 |
|:---|:---|:---|
| **① 옵시디언 내장 Read-Only 위키 챗봇**<br/>(`llm-wiki-chat` 패턴) | • `127.0.0.1:18793`<br/>• `,.py.plugin.llm-wiki-readonly-chat.py` | • 파이썬 커널 레벨 **Read-Only 감옥(`sys.addaudithook`)** 위에서 작동하여 파일 훼손 위험이 물리적으로 `0%`<br/>• Full-Body BM25F 키워드 + `LanceDB` 하이브리드 벡터 검색으로 **실제 인용된 노트 링크와 `[1]`·`[2]` 분리 각주만 정밀 응답** |
| **② 브라우저 북마크 ↔ 볼트 동기화**<br/>(`Search-First` 리서치 사이드패널) | • 브라우저 확장 사이드패널<br/>• `,.py.browser.bookmark-obsidian-sync.py` | • 북마크 벡터 검색 + 백그라운드 실본문 크롤링 + 웹 검색을 병렬 결합<br/>• **옵시디언 폴더 트리와 브라우저 북마크 폴더를 1:1 양방향 동기화**하여 수집 즉시 지식화 |
| **③ 비동기 원격 작업 큐 브릿지**<br/>(모바일·원격 발주 패턴) | • `931.session/931-001.multi-cli/.remote/`<br/>• `,.py.agent.remote-bridge.py` | • 외부 기기에서 원격 큐 디렉터리나 웹훅으로 작업 요청을 드롭하면 로컬 멀티 CLI 하네스가 작업을 수행하고 검증된 결과를 `800.outbox/`로 배출 |
| **④ 에이전트 전용 격리 브라우저**<br/>(MCP / CDP 백그라운드 제어) | • `127.0.0.1:9210/mcp` (CDP `:9110`)<br/>• `,.py.crawl.web-digest.py` | • 사용자의 일상 브라우저와 프로필·프로세스를 완전 분리(`R-SAFE-14`, 포커스 스틸 0건)<br/>• 웹 리서치·다이제스트를 자동 정제해 `941.raw` 표준 노트로 적재 |

---

## 8. 상시 운영 기본 시스템 구성 스택 (Universal Baseline System Configuration)

> **[🖥️ 베이스라인(Baseline) 한눈에 보기]**
> 본 섹션은 로컬 워크스테이션에서 자율형 옵시디언 LLM-Wiki 볼트를 무중단으로 운영할 때 권장되는 **OS·무창 데몬·로컬 AI 서버·멀티 에이전트 터미널·옵시디언·동기화·보안 백업의 표준 구성표**입니다. 상세 사양은 [`docs/01-baseline-system-configuration.md`](docs/01-baseline-system-configuration.md)를 참조하십시오.

| 계층 구분 | 권장 구성 요소 (데몬 · 포트 · 도구) | 핵심 설정 규격 및 무중단 운영 원칙 |
|:---|:---|:---|
| **8.1. OS · 셸 · 무창 데몬 · 스케줄러**<br/>(`913.shell/` & `VaultCli`) | • **셸**: Cross-platform `VaultCli` 17종 단축 명령어 래퍼(`twiki`~`tcheck`)<br/>• **무창 실행 표준 (`No-Console-Flash`)**: Windows는 `pythonw.exe` + `CREATE_NO_WINDOW`(`0x08000000`) + `.vbs` 래퍼, POSIX는 `systemd`/`launchd` 데몬<br/>• **정기 스케줄**: 30분 주기 증분 벡터 동기화, 일일 무결성 감사, 자동 `file_id` 발급, 아웃링크 자가 치유 | • 백그라운드 자동화 실행 시 포커스 탈취 및 콘솔 깜빡임 `0건`<br/>• 커널 네임드 뮤텍스/파일 락 싱글톤으로 중복 실행 원천 차단 |
| **8.2. 로컬 AI 모델 · 벡터 DB · 추론 서버**<br/>(`:11434` · `:18793` · `:8000` · `914.data/`) | • **`Ollama` 싱글톤 (`:11434`)**: 로컬 경량 Instruct LLM(2B~4B), `opendecider-small`, `nomic-embed-text-v1.5`(768d NPZ)<br/>• **`ONNX / CUDA` 가속 모델**: `1024d` 고밀도 임베딩(`Harrier-OSS-v1-0.6B` 등), Cross-Encoder 리랭커(`Jina-Reranker-v3.5` 등)<br/>• **추론 데몬**: `:18793` Read-Only 위키 웜 데몬 + `:8000` `OpenDecider` 4-Stage Fork 서버 | • **`LanceDB` 3대 하이브리드 테이블**: `llm_wiki.lance`, `skills_compact.lance`, `selective_docs.lance`<br/>• `:18793` 데몬은 `sys.addaudithook` 커널 감옥으로 쓰기·삭제가 원천 봉쇄되며, `/reload` 엔드포인트로 무중단 핫리로드 |
| **8.3. 멀티 CLI 오케스트레이션 & 헌법 동기화** | • **역할 분리 멀티 패인 워크스페이스**: `Leader`(총괄·검수) · `Builder`(구현) · `Worker`(병렬 리서치·정제) · `Verifier`(테스트·루프)<br/>• **헌법 싱크**: `,.py.sync.cli.py` | • `920.prompt/921.skill` 수정 시 `sync.cli.py`가 루트 `AGENTS.md`/`CLAUDE.md`/`GEMINI.md`, `.agents/skills/`, `.cursor/rules/`를 일괄 최신화 |
| **8.4. 옵시디언 코어 & 플러그인 생태계**<br/>(`.obsidian/`) | • **뷰/원본 분리**: 표면 폴더(`{folder}.md` + `,.map.*` + `,.base.*` + `AGENTS.md`) ↔ 본문 저장소(`941.raw/`)<br/>• **핵심 플러그인 패턴**: `folder-notes`, 코어 `Bases`, `dataview`, `tasks`, `excalidraw`, `tag-wrangler`, `llm-wiki-chat`, `maintenance-filter`, `empty-save-guard` | • 캐시 정리 시 Electron `Partitions` 폴더를 보존하여 플러그인 Restricted Mode 초기화 사고 원천 방지 |
| **8.5. 격리 브라우저 · 3중 동기화 · 외장 암호화 백업** | • **격리 브라우저**: 에이전트 전용 MCP/CDP 브라우저 (`:9210` / `:9110`)<br/>• **3중 동기화**: Git 자동 버전 관리 + 실시간 클라우드/P2P 미러(`_inbox` Copy-Only) + 증분 암호화 백업<br/>• **외부 분리 보안 금고**: `<EXTERNAL_PRIVATE_VAULT>` + AES-256(`-mhe=on`) + OS ACL 잠금 | • 에이전트 자동화가 사용자의 일상 브라우저 창이나 개인 세션을 절대 방해하지 않도록 물리 격리<br/>• 민감 키·인증서는 볼트 바깥 암호화 금고로 완전 분리하여 Git·AI 노출 `0%` 보장 |

---

## 9. 독립 환경 이식(패키징) · 외부 시스템과의 느슨한 결합 경계 · 4단계 구축 로드맵

### 9.1. 새로운 PC·팀 환경에 이식(패키징)할 때의 안전 수칙 3가지

1. **기존 프로덕션·외부 인프라와 물리적 완전 분리**:
   - 기존 앱·서버·컨테이너 코드를 수정하지 않고, 본 블루프린트 기반의 **독립된 옵시디언 볼트 디렉터리(`<VAULT_ROOT>`)와 마크다운 규칙(`921.skill`) 패키지**만 별도 경로에 설치합니다.
2. **하드코딩 경로 및 개인정보 클린징(Sanitizing) 원칙**:
   - 특정 머신의 절대 경로 대신 `<VAULT_ROOT>` 앵커 탐색(`variable.py`) 및 환경 변수 기반으로 동작하게 유지하며, 외부 공개·공유 전 개인정보 Denylist 자동 감사(`0건`)를 통과시킵니다.
3. **원본 추출 텍스트의 출처 우선순위 검증 표준**:
   - 장시간 회의·강의·문서 원본은 `.extracted/` 및 `410-004.md/`로 추출 텍스트를 분리 보존하고, 사실 충돌 시 **최신 1차 원문(녹취/공식 발표) > 수정 문서 > 초기 요약** 순으로 교차 검증합니다.

---

### 9.2. 외부 시스템·인프라와의 업무 경계 분리표 (Loose Coupling Matrix — 포트·코드 충돌 `0%`)

> **[🤝 연동 핵심 원칙: "코드·서버는 섞지 않고, 오직 깨끗한 마크다운/JSON 폴더(`000.inbox` ↔ `800.outbox`)로만 대화한다"]**
> 기존에 운영 중인 외부 서버·NAS·CI/CD·데이터 파이프라인을 1바이트도 수정할 필요가 없도록 두 시스템의 경계를 물리적으로 완전 분리합니다.

| 경계 구분 항목 | [영역 A] 옵시디언 LLM-Wiki 자율 지식 볼트<br/>(지식 도서관 · PMEST v2 · 스킬 · 로컬 RAG) | [영역 B] 기존 외부 인프라 · 프로덕션 시스템<br/>(애플리케이션 서버 · NAS · DB · 외부 파이프라인) | 충돌 방지 및 연동 약속 |
|:---|:---|:---|:---|
| **핵심 목적** | 개인·팀 지식 체계화, 재사용 스킬(`921.skill`) 축적, 로컬 0원 위키 검색, 검증된 문서·리포트 생산 | 상용 서비스 구동, 대규모 데이터 수집, 스토리지 관제, 외부 API/인프라 운영 | **책임 완전 분리** (지식 두뇌 vs 상용 인프라) |
| **설치 경로 (파일시스템)** | 독립 로컬 폴더 `<VAULT_ROOT>/` (`000.inbox` ~ `950.UI`) 및 CLI 래퍼 디렉터리 | 기존 지정 서버 디렉터리, Docker 컨테이너, NAS 볼륨 | **디렉터리 겹침 `0%`** (서로의 내부 폴더 직접 쓰기 금지) |
| **네트워크 포트** | 로컬 루프백(`127.0.0.1`) 전용 고번호 포트만 선택 사용 (`:18793`, `:9210`, `:11434`, `:8000` — 모두 환경변수로 변경 가능) | 기존 웹 서버·DB·프록시 포트 일체 그대로 유지 | **외부 포트 개방 없음** 및 포트 환경변수화로 **포트 충돌 `0%`** |
| **데이터 교환 인터페이스** | • **수신**: 외부 시스템이 넘겨준 텍스트·리포트를 `000.inbox/`에서 읽어 `941.raw` 지식으로 흡수<br/>• **발신**: 최종 검증된 마크다운/PDF/JSON 결과물을 **`800.outbox/`**에 단일 정본으로 배출 | • 외부 파이프라인 산출물 중 지식화가 필요한 파일만 `000.inbox/`에 드롭(Drop)<br/>• `800.outbox/`에 배출된 최종 정본만 읽어 대시보드·배포에 활용 | API 강결합 없이 **순수 파일 드롭(`000.inbox` ↔ `800.outbox`)으로 느슨한 결합(Loose Coupling)** 달성 |
| **장애 격리 (Fault Isolation)** | 옵시디언이나 로컬 AI 모델을 꺼도 외부 인프라에는 영향 `0%` | 외부 서버 점검·재배포 중에도 로컬 옵시디언 노트 열람·CLI 작업은 100% 정상 작동 | **상호 장애 전파 `0%`** |

---

### 9.3. 실패 없는 4단계 단계별 구축 체크리스트 (Step-by-Step Roadmap)

처음부터 모든 데몬을 한꺼번에 올리지 않고, 아래 **4단계 순서대로 하나씩 검증하며 확장**하는 것이 가장 안전합니다.

- [ ] **Step 1 — 폴더 골격(`000`~`950`) & 전역 헌법 "규칙 갱생" 세팅 (소요: 즉시)**
  - 본 저장소를 `<VAULT_ROOT>`로 클론하여 `000.inbox` ~ `950.UI` 표준 폴더 트리와 루트 `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` + 도메인별 L1 `AGENTS.md` 지연 로딩 맵을 확보합니다.
- [ ] **Step 2 — `941.raw` 무손실 격리 & PMEST v2 노트 자동 생성기 연결**
  - `921-700.ruskill` 핵심 명명 규칙과 `915.code-harness/,.code-harness.yaml.py`(`gen_vault_note_from_title()`), `raw-guard.py`, `registry.register-path.py`(10자리 불변 `file_id` 발급)를 연결하여 표면 폴더 오염 없이 표준 노트가 `941.raw`에 자동 적재되고 `,.map.*` / `,.base.*` 뷰가 갱신되게 합니다.
- [ ] **Step 3 — 비용 0원 로컬 임베딩 · `LanceDB` · 17종 CLI 단축 도구 장착**
  - 로컬 임베딩 모델 + `LanceDB`(`914-010.lance`) + `token-tools.py`(`twiki`, `tgrep`, `hsearch`, `tskeleton`, `tslice`, `tid`, `tsha`, `tcheck`) 및 30분 주기 무창 증분 동기화(`embedding.auto-sync.py`)를 활성화해 클라우드 토큰 소모량을 85~95% 절감합니다.
- [ ] **Step 4 — 멀티 CLI 하네스 · Read-Only 위키 챗봇 · 외부 파일 교환 확장 (선택 확장)**
  - 필요에 따라 옵시디언 내장 Read-Only 위키 챗봇(`:18793`), 격리 에이전트 브라우저(`:9210`), 역할 분리 멀티 CLI 워크스페이스, 그리고 외부 시스템과의 `000.inbox` ↔ `800.outbox` 파일 교환 파이프라인을 연결합니다.
