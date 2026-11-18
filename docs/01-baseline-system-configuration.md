# 01. 상시 유지 전체 기본 시스템 구성 백과사전 (Exhaustive Baseline System Configuration)

> 본 문서는 메인 워크스테이션에서 매일 24시간 무중단으로 가동·유지되는 **운영체제·무창 데몬·스케줄러(`913.shell`) · 로컬 AI 모델 및 벡터 DB(`914.data`) · 멀티 에이전트 터미널(`Herdr` & `OpenDecider`) · `46`개 자율 방어 코드 하네스(`915.code-harness`) · `79`개 옵시디언 플러그인(`.obsidian`) · 외부 브라우저/원격 브릿지/3중 동기화/보안 금고**의 전수 구성을 코드에서 직접 추출해 정리한 레퍼런스입니다.

---

## 1. OS · Shell · Windowless Daemon & Windows Task Scheduler Baseline (`913.shell`)

### 1.1. `No-Console-Flash` 무창(Windowless) 백그라운드 데몬 표준
Windows 환경에서 수십 개의 백그라운드 워처·동기화 스크립트가 돌 때 화면에 검은색 `cmd.exe` / `conhost.exe` 창이 0.1초라도 깜빡이면 작업 몰입이 깨지고 포커스를 빼앗깁니다. 이를 물리적으로 차단하기 위해 다음 4대 원칙을 전역 강제합니다:

1. **서브프로세스 플래그 강제**: 모든 Python `subprocess` 호출 시 `CREATE_NO_WINDOW (0x08000000)` 및 `STARTUPINFO(dwFlags=STARTF_USESHOWWINDOW, wShowWindow=0)` 필수 지정.
2. **GUI 서브시스템 바이너리 사용**: 옵시디언 플러그인·Electron·백그라운드 워처에서 파이썬 호출 시 `python.exe` 대신 콘솔 할당이 없는 **`pythonw.exe`**(`windowsHide: true`, `shell: false`)만 호출.
3. **작업 스케줄러 `.vbs` 무창 래퍼 경유**: Windows 작업 스케줄러에 `.bat`/`.cmd`를 직접 등록하지 않고, 반드시 `wscript.exe //B //Nologo "<VAULT_ROOT>\900.maintenance\910.codey\913.shell\913-002.vbs\,.vbs.*.vbs"` (`WshShell.Run ..., 0, False`) 형태로만 등록.
4. **커널 네임드 뮤텍스 싱글톤(Singleton Lock)**: 주기적 스케줄러 중복 실행을 막기 위해 `Local\ObsidianVaultGitSyncSingleton`, `cdp_lock`, `heavy_model_infer_lock`, `RegistryLock` 등 OS/파일 락으로 단일 인스턴스만 유지.

### 1.2. 로컬 루프백(`127.0.0.1`) 상시 서비스 포트 맵 (Local TCP Port Map)

| 바인딩 주소 및 포트 | 상주 프로세스 / 데몬 | 핵심 프로토콜 및 아키텍처 역할 |
|:---|:---|:---|
| **`127.0.0.1:8000`** | `opendecider serve` (`ollama:opendecider-small`) | **Stage 1 System-One 확률 라우터** (`/v1/systemone`): 단일 순전파 신뢰도 `p >= 0.85` 시 LLM 없이 파이썬 코드로 즉시 직행(`sharp`) |
| **`127.0.0.1:9110`** | `BrowserOS CDP` (`cdp_lock` 보호) | 에이전트 전용 브라우저의 숨김 백그라운드 탭 DOM 추출·SSO 쿠키 하이드레이션 전용 포트 |
| **`127.0.0.1:9210`** | `BrowserOS neo MCP` (`/mcp`) | 클로드·코덱스·제미나이·안티그래비티·커서가 공용으로 연결하는 **1순위 Streamable HTTP 브라우저 MCP 서버** |
| **`127.0.0.1:11434`** | `Ollama` (`ollama app.exe` 싱글톤) | 로컬 3-Model CoT(`Qwen3.8-4B`, `ImaJev-2B`, `Spark-X2.6-1.7B`), `opendecider-small`, `nomic-embed-text-v1.5` 상시 서빙 |
| **`127.0.0.1:18793`** | `llm-wiki-readonly-chat.py` (`스페짱` 웜 데몬) | **`sys.addaudithook` 커널 레벨 Read-Only 감옥** 위에서 BM25F + `LanceDB` 하이브리드 검색 및 `/reload` 무중단 인덱스 핫리로드 수행 |
| **`127.0.0.1:27129`** | `Obsidian Local Reload / Bridge` | 옵시디언 로컬 플러그인 상태 점검 및 무간섭 브릿지 |

### 1.3. `913.shell` 셸 스크립트 인벤토리 요약 & `913-003.xml` 작업 스케줄러 `24`개 전수 매핑표
- **`913-001.bat/` (`125`개)**: `%LOCALAPPDATA%\VaultCli\bin` 단축 명령어 래퍼 및 원클릭 운영 런처
- **`913-002.vbs/` (`59`개)**: 콘솔 깜빡임 `0건`을 보장하는 무창 실행 래퍼
- **`913-003.xml/` (`24`개)**: Windows 작업 스케줄러 등록용 정규 XML (`Logon + ConsoleConnect + SessionUnlock` 3중 트리거 등)
- **`913-004.ps1/` (`28`개)**: `herdr-tabs.ps1`(6-탭 터미널 런처 SSOT), Defender 제외 경로 설정, 데스크톱 복구 스크립트
- **`913-005.ahk/` & `913-006.icon/`**: 생산성 단축키 스크립트 및 에이전트별 전용 트레이/바탕화면 아이콘(`10`종)

| 순번 | 작업 스케줄러 XML 정의서 (`913-003.xml/`) | 트리거 및 상시 자동화 역할 |
|:---:|:---|:---|
| `01` | `,.xml.audio.dolby-logon.xml` | 로그온 시 오디오 DSP/EQ 프로필 무창 자동 복원 |
| `02` | `,.xml.baking.code-harness-daily-audit-schedule.xml` | 46개 코드 하네스 메쉬 및 `ruskill` 규격 준수 여부 일일 자동 감사 |
| `03` | `,.xml.baking.trigger-daily-schedule.xml` | 일일 베이킹(Baking) 루틴 및 자율 유지보수 점검 트리거 |
| `04` | `,.xml.brave.logon-schedule.xml` | 로그온 시 격리 프로필 세션 및 필수 작업 탭 무간섭 복원 |
| `05` | `,.xml.display.aspect-restore.xml` | 디스플레이 해상도·화면비 전환 후 복구 가드 |
| `06` | `,.xml.evilclaw.discord-logon.xml` | 보조 디스코드 리서치·알림 봇 로그온 자동 기동 |
| `07` | `,.xml.herdr.discord-bridge-schedule.xml` | `HerdrBridge` 디스코드 양방향 원격 명령 브릿지 백그라운드 상주 데몬 |
| `08` | `,.xml.herdr.schedule.xml` | `Logon + ConsoleConnect + SessionUnlock` 3중 트리거 기반 Herdr 6-탭 멀티 에이전트 런처 기동 |
| `09` | `,.xml.inbox.activity-log.xml` | `000.inbox` 및 `400.resource` 유입 파일 활동 감사 로깅 |
| `10` | `,.xml.memory-embed-poll-schedule.xml` | 실수 원장 변경 감지 폴링 및 즉시 임베딩 반영 |
| `11` | `,.xml.memory-embed-schedule.xml` | `948.mistake` 실수 원장 `nomic-embed-text-v1.5` 768d NPZ 임베딩 배치 갱신 |
| `12` | `,.xml.registry.auto-id-schedule.xml` | 신규 생성 마크다운 문서 10자리 불변 `file_id` 누락 스캔 및 자동 발급 |
| `13` | `,.xml.registry.ghost-outlink-healer-schedule.xml` | 끊어진 유령 아웃링크(`ids_outlink`) 탐지 및 벡터·레지스트리 기반 자동 치유 |
| `14` | `,.xml.resync-schedule.xml` | 볼트 인덱스·맵·베이스 뷰 정합성 재동기화 스케줄 |
| `15` | `,.xml.schedule.obsidian-routine-everytime.xml` | 시간표·일일 루틴 및 타임라인 자동 동기화 |
| `16` | `,.xml.schedule.obsidian-vector-db-sync.xml` | 30분 주기(`PT30M`) 무창 증분 벡터 임베딩 갱신(`embedding.auto-sync.py`) 및 `:18793/reload` 무중단 핫리로드 |
| `17` | `,.xml.schedule.routine-discord-daily.xml` | 일일 루틴·할일 브리핑 디스코드 자동 알림 |
| `18` | `,.xml.schedule.university-daily-pipeline.xml` | 강의 녹음 STT(NotebookLM) + 슬라이드 + LMS 공지 교차검증 일일 파이프라인 |
| `19` | `,.xml.schedule.university-discord-daily.xml` | 학사 일정·과제 마감 디스코드 일일 브리핑 |
| `20` | `,.xml.schedule.webchat-evening-batch.xml` | 매일 저녁 5대 웹챗(ChatGPT·Claude·Gemini·Grok·AI Mode) 대화 무인 수확 및 `941.raw` 정제 적재 |
| `21` | `,.xml.sync.cli-schedule.xml` | `920.prompt/921.skill` SSOT → `AGENTS.md`/`CLAUDE.md`/`GEMINI.md`·`.agents/skills`·`.cursor/rules` 정기 동기화 |
| `22` | `,.xml.sync.gdrive-schedule.xml` | Google Drive 미러 디바운스·정기 무결성 동기화 데몬 |
| `23` | `,.xml.sys.auto-recover-keyboard-desktop.xml` | 입력 훅·데스크톱 응답 지연 감지 시 무창 자동 복구 워치독 |
| `24` | `,.xml.youtube.recommend-schedule.xml` | 구독 채널 Atom RSS 기반 맞춤 유튜브 영상 큐레이션 자동 생성(`211-001.youtube`) |

---

## 2. Local AI Models · Vector DB · Hybrid RAG Baseline (`914.data`)

### 2.1. 상시 운용 로컬 AI 모델 10종 상세 제원표 (클라우드 API 비용 `0원`)

| 구분 | 모델명 | 구동 런타임 / 가속 | 차원 / 파라미터 | 볼트 내 전담 역할 |
|:---|:---|:---|:---|:---|
| **임베딩 (주력)** | **`Harrier-OSS-v1-0.6B`** | HuggingFace / ONNX / CUDA | `1024d` (0.6B) | `940.llm-wiki`·`921.skill`·볼트 문서 전수 하이브리드 벡터 검색(`hsearch`, `houtlink`, `hclassify`) |
| **임베딩 (기억)** | **`nomic-embed-text-v1.5`** | Ollama (`:11434`) + `912-009.npz` | `768d` | `948.mistake` 역대 AI 실수 원장 초고속 유사도 검색(`embedding.memory.py`) |
| **교차 리랭커** | **`Jina-Reranker-v3.5`** | ONNX / CUDA Cross-Encoder | Cross-Encoder | 1차 벡터+BM25 후보군을 정밀 재순위화하여 상위 정답 정확도 극대화 |
| **확률 라우터** | **`opendecider-small`** | Ollama (`:11434`) → `:8000` | Calibrated SLM | 단일 순전파 확률 `p` 산출 (`p >= 0.85` 시 파이썬 코드 자동 실행 `hsolve`) |
| **위키 매니저 1** | **`Qwen3.8-4B`** | Ollama (`:11434`) / `:18793` | `4B` | 로컬 **`스페짱 (spe_chan)`** 주력 추론 및 옵시디언 내장 Read-Only 각주 챗봇 응답 |
| **위키 매니저 2** | **`ImaJev-2B`** | Ollama (`:11434`) | `2B` | `twiki` / `tgrep` 3-Model CoT 중간 논리 검증 및 문맥 압축 |
| **위키 매니저 3** | **`Spark-X2.6-1.7B`** | Ollama (`:11434`) | `1.7B` | 초경량 고속 요약 및 CoT 최종 브리핑 정제 |
| **비전 분류 1** | **`Kagami-24k`** | `915-007.kagami-24k/` ONNX | Vision Tagger | `430.image` 이미지 초고속 다중 태그 추론 및 Bases 갤러리 자동 분류 |
| **비전 분류 2** | **`WD-EVA02-Large`** | `915-006.wd-eva/` ONNX | Vision Tagger | 보조 서브컬처·일러스트 고정밀 속성 태깅 |
| **오디오 복원** | **`AudioSR`** | PyTorch CUDA (RTX 4060) | 48kHz Latent Diffusion | 손실 압축 음원 오프라인 초해상화 복원 (실시간 오디오는 AutoEq Harman + Equalizer APO 무지연 분리) |

### 2.2. `LanceDB` (`914-010.lance/`) 3대 하이브리드 테이블 & 무중단 증분 동기화 구조
1. **`llm_wiki.lance`**: `942.wiki` 43개 허브 + `941.raw` 정본 마크다운 본문/헤딩 청크 하이브리드 인덱스 (`18,200+` 노드).
2. **`harrier_skills_compact.lance`**: `920.prompt/921.skill` 6종 스킬(`1,033+`개) + `912-001.py` 도구 매핑 전용 압축 인덱스 (`hskill`, `hcode` 0.1초 응답).
3. **`harrier_selective_docs.lance`**: 핵심 아키텍처·규칙(`921-700.ruskill`) 정밀 검색 테이블.
- **30분 주기 무중단 갱신 루프**: `embedding.auto-sync.py`가 파일 해시 변경분만 증분 임베딩한 뒤 `POST http://127.0.0.1:18793/reload`를 호출하여 옵시디언 내장 챗봇이 재시작 없이 즉시 최신 노트를 참조합니다.

---

## 3. Herdr 6-Pane Multi-CLI & 4-Stage OpenDecider Fork Harness Baseline

### 3.1. `Herdr` 단일 창 6-패널 멀티 에이전트 편제 (`herdr-tabs.ps1` SSOT)

| 패널 역할명 | 내부 식별자 | 구동 CLI 엔진 | 전담 임무 및 권한 경계 |
|:---|:---|:---|:---|
| **`Leader`** | `agy` | Antigravity CLI | 전체 아키텍처 총괄 지휘, 멀티 탭 작업 배분(`fanout`), 최종 체크리스트 검수(`verify-only` 시 직접 수정 금지) |
| **`Builder`** | `agy2` | Antigravity CLI | 대규모 신규 기능 구현, 복합 파이프라인 설계 및 문서화 |
| **`Worker`** | `agy3` | Antigravity CLI | 병렬 리서치, 문서 정제, 데이터 마이그레이션 실행 |
| **`Drone`** | `agy4` | Antigravity CLI | 반복 배치 작업, 크롤링 후처리 및 보조 검증 |
| **`Minion`** | `cursor` | Cursor CLI | 코드 집중 수정 및 `/loop`(`cursor-loop.py prove`) 기계 검증 통과 시까지 자율 재작업 |
| **`Peon`** | `cursor2` | Cursor CLI | 병렬 테스트 코드 작성 및 독립 모듈 디버깅 루프 |

### 3.2. `.agents/skills/` 11대 CLI 네이티브 스킬 전수 목록 (`sync.cli.py` 자동 미러링)

| 순번 | 스킬 폴더명 (`.agents/skills/`) | 핵심 기능 및 자동 활성화 시점 |
|:---:|:---|:---|
| `01` | **`agent-desktop`** | Windows UIA 접근성 트리 기반 네이티브 데스크톱 앱 정밀 제어 (픽셀 추측 금지) |
| `02` | **`antigravity-blunders`** | 콘솔 깜빡임 방지, 검수자 역할 경계, 무근거 성공 단언 차단 등 실전 실패 방지 규율 |
| `03` | **`bases`** | 옵시디언 코어 데이터베이스 뷰(`.base`) 필터·수식·카드 레이아웃 설계 규격 |
| `04` | **`browseros-neo`** | 에이전트 전용 로그인 유지 브라우저(`127.0.0.1:9210/mcp`) 백그라운드 제어 표준 |
| `05` | **`canvas.json`** | 옵시디언 JSON Canvas(`.canvas`) 노드·엣지·그룹 시각 다이어그램 생성 규격 |
| `06` | **`gods-eye-view`** | 볼트 내장 3D 구체 OSINT 시각화 엔진(`914.data/gods-eye-view`) 제어 스킬 |
| `07` | **`image-gallery`** | `Kagami-24k` ONNX 분류 + 프록시 카드(`cards-sync.py`) + Bases 썸네일 갤러리 파이프라인 |
| `08` | **`loop`** | `/loop` 명시 호출 시 테스트+로그+재작업을 `Pass`까지 반복하는 자율 검증 루프 |
| `09` | **`markdown`** | 볼트 마크다운 헤딩·리스트·수식·폴더 구조 및 YAML 프론트매터 작성 표준 |
| `10` | **`test-code-inspector`** | `911.test` 7-Layer 정적·동적 코드 검수(`ruff`/`pyflakes`/`mypy`/`bandit`/`vulture`/`pytest` + `R-PY-01..10`) |
| `11` | **`youtube-inbox`** | `BrowserOS neo` MCP 기반 유튜브 인박스 재생목록 정밀 분류 및 더미 앵커 유지 프로토콜 |

---

## 4. Complete `46`-Module `915.code-harness/` Kernel & Self-Healing Catalog

볼트의 물리적 질서를 수호하는 `900.maintenance/910.codey/915.code-harness/` 내부 **`46`개 파이썬 모듈 전수 목록**입니다.

| 순번 | 모듈 파일명 (`915.code-harness/`) | 줄 수 | 대표 공개 심볼 (AST 추출) | 핵심 아키텍처 방어·복구 역할 |
|:---:|:---|---:|:---|:---|
| `01` | `,.code-harness.accent-color.py` | `47` | — | 옵시디언 테마 강조색 설정 위임 호환성 래퍼 (`setting.accent-color.py` 연결) |
| `02` | `,.code-harness.decider-engine.py` | `1423` | `resolve_vault_space_root`, `load_decider_state`, `save_decider_state` | OpenDecider 로컬 확률 판단 상태 관리, 세션 라우팅 로그 및 임계값(`P >= 0.85`) 결정 엔진 |
| `03` | `,.code-harness.embedding-registry.py` | `407` | `RegistryEntry`, `rel`, `normalize_target` | `file_id` 레지스트리 엔트리 파싱 및 임베딩 벡터 인덱스 대상 정규화 모듈 |
| `04` | `,.code-harness.fault-tolerance.py` | `2386` | `resolve_anchor_vault_root`, `purge_vault_bytecode`, `get_mitigation_log` | 백그라운드 프로세스 과부하 방지(`CombustionPressureGovernor`), 바이트코드 정화 및 범용 장애 격리 하네스 |
| `05` | `,.code-harness.file-resolver.py` | `47` | — | 레거시 파일 경로 해석 호환 래퍼 (`file-tracker.py`로 단일 위임) |
| `06` | `,.code-harness.file-tracker.py` | `396` | `is_excluded`, `iter_md_files`, `iter_all_files` | 10자리 불변 `file_id` ↔ 실제 볼트/Raw 파일 경로 O(1) 인덱스 빌더 및 탐색기 |
| `07` | `,.code-harness.folder-activity-log.py` | `500` | `resolve_path`, `rel`, `is_relative_to` | 프로파일 기반 폴더 변경 스냅샷, SHA256 무결성 검증 및 JSONL 활동 감사 로거 |
| `08` | `,.code-harness.folder-resolver.py` | `449` | `build_index`, `find_duplicate_folders`, `resolve` | 폴더 노트 `file_id` 기반 실시간 폴더 위치 추적 및 중복 폴더 탐지기 |
| `09` | `,.code-harness.folder.py` | `114` | `get_folder_hierarchy`, `resolve_parent`, `name_key_for_folder` | 폴더 계층 트리 해석, 상위/하위 이름 키 추출 및 폴더 노트(`{folder}/{folder}.md`) 경로 표준화 |
| `10` | `,.code-harness.harness.py` | `354` | — | 신규 파이썬 스크립트 공통 의무 임포트 하네스 (표준 인코딩·경로·예외 보호 계층) |
| `11` | `,.code-harness.harrier-decider.py` | `1294` | `load_id_maps`, `resolve_vault_or_raw_path`, `resolve_fid_and_stem` | `Harrier-OSS-v1-0.6B`(1024d) + `LanceDB` + `Jina-Reranker-v3.5` + `OpenDecider` 하이브리드 검색·결심 통합 엔진 |
| `12` | `,.code-harness.helper.py` | `523` | `import_id`, `gen_id`, `register` | 공통 ID 발급(`gen_id`), 레지스트리 등록·조회(`register`/`lookup`) 핵심 유틸리티 |
| `13` | `,.code-harness.id.py` | `2186` | `reset_cache`, `parse_registry_row`, `registry_outlink_target` | 볼트 전역 10자리 `file_id` 파싱, 아웃링크 키 매핑 및 레지스트리 캐시 단일 책임 모듈 |
| `14` | `,.code-harness.lookup-paths.py` | `95` | `resolve_file`, `resolve_folder`, `format_path` | CLI/스크립트용 `file_id` 및 폴더 ID → 실제 디스크 경로 고속 변환기 |
| `15` | `,.code-harness.markdown-ref-policy.py` | `634` | `RefIssue`, `rel`, `iter_md_files` | 마크다운 본문 내 깨진 위키링크, 분할 참조 및 산문 인용 규격 감사·정규화 엔진 |
| `16` | `,.code-harness.mkdn.py` | `66` | `extract_wikilinks`, `build_wikilink`, `to_wikilink_or_marker` | YAML 프론트매터 분리(`yaml_split`), 위키링크 추출·조립 단일 책임 파서 |
| `17` | `,.code-harness.mobile-folder-healer.py` | `427` | `find_vault_root`, `is_protected_canonical_rel`, `normalize_legacy_rel` | 모바일 동기화 과정에서 생기는 비정규 레거시 폴더 경로 탐지 및 정규 리프 자동 치유기 |
| `18` | `,.code-harness.mobile-sync-governor.py` | `1599` | `find_vault_root`, `discover_and_heal_base_targets`, `should_ban_file_for_mobile` | 모바일·Google Drive 동기화 제외 대상 필터링 및 Bases 뷰 타깃 자동 복구 거버너 |
| `19` | `,.code-harness.model-lifecycle.py` | `915` | `patch_truststore_ssl`, `resolve_hf_local_snapshot`, `avail_commit_gb` | 로컬 AI 모델 메모리 가용량 점검(`avail_commit_gb`), 추론 뮤텍스 락(`heavy_model_infer_lock`) 및 SSL 패치 관리 |
| `20` | `,.code-harness.outcome-verifier.py` | `1065` | `ExecutionContract`, `VerificationOutcome`, `EscalationReceipt` | 에이전트 작업 실행 계약(`ExecutionContract`) 및 산출물 실존·무결성 기계 검증기 |
| `21` | `,.code-harness.outlink-healer.py` | `1173` | `now_kst_str`, `log_line`, `load_3ai_cache` | 끊어진 `ids_outlink` 위키링크를 임베딩·레지스트리 기반으로 자동 복구하는 자가 치유기 |
| `22` | `,.code-harness.path-healer.py` | `2107` | `now_kst`, `explain_resolution`, `resolve_any_path` | 폴더 깊이 오판(`.parents[4]`) 및 이동된 경로를 상시 스캔·복구하는 토폴로지 치유 엔진 |
| `23` | `,.code-harness.plugin.style.engine.py` | `181` | `StyleSettingsManager` | 옵시디언 Style Settings 플러그인 CSS 변수 패턴 기반 자동 동기화 엔진 |
| `24` | `,.code-harness.plugin.variable.py` | `44` | — | 옵시디언 플러그인 제어용 공통 설정 상수 모듈 |
| `25` | `,.code-harness.preop-backup.py` | `393` | `find_vault_root`, `find_zstd`, `ensure_preop_backup` | 대규모 리네임·마이그레이션 직전 `zstd` 초고속 사전 스냅샷 백업 생성·복원 안전망 |
| `26` | `,.code-harness.py-update-log.py` | `328` | `rel`, `sha256_file`, `iter_py_files` | 볼트 내 모든 `.py` 코드의 SHA256 변경 이력을 `919.logs`에 자동 기록하는 코드 감사기 |
| `27` | `,.code-harness.raw-guard.py` | `2128` | `find_vault_root`, `now_kst_iso`, `today_kst_str` | 표면 폴더(`000`~`400`) 일반 `.md` 생성을 감지해 `941.raw/`로 즉시 이관하고 `,.map.*`·`,.base.*`를 자동 갱신하는 핵심 수호 데몬 |
| `28` | `,.code-harness.recovery-harness.py` | `925` | `resolve_vault_root`, `now_kst_iso`, `sha12_bytes` | 비정상 작업 중단·파일 손상 감지 시 체크포인트 및 SHA12 대조 기반 자동 복구 하네스 |
| `29` | `,.code-harness.registry-lock.py` | `288` | `RegistryLockTimeout`, `lock_depth`, `registry_lock` | 멀티 에이전트 동시 접근 시 레지스트리 파일 경합을 막는 공유 락(`RegistryLock`) 및 원자적 파일 쓰기(`atomic_write_text`) |
| `30` | `,.code-harness.registry.py` | `81` | `load_registry_text`, `save_registry_text`, `update_registry_text` | 레지스트리 마크다운 테이블 로드·저장·인덱싱 단일 책임 모듈 |
| `31` | `,.code-harness.rule-heading-id.py` | `873` | `HeadingIssue`, `HeadingRow`, `rel` | `921-700.ruskill` 규칙 문서 헤딩 블록 ID(`^...`) 자동 부여 및 정합성 감사기 |
| `32` | `,.code-harness.safety-governor.py` | `2202` | `resolve_vault_root`, `now_kst_iso`, `get_opensource_stack_status` | `file_id` 등록 중요 파일에 대한 무단 삭제(`unlink`/`rmtree`) 및 위험 명령을 커널 레벨에서 물리 차단(`R-SAFE-01..14`) |
| `33` | `,.code-harness.safety-governor.shadow.py` | `2202` | `resolve_vault_root`, `now_kst_iso`, `get_opensource_stack_status` | `safety-governor.py` 손상 대비 이중화 섀도우 미러 모듈 (무중단 방어 연속성 보장) |
| `34` | `,.code-harness.sentinel.py` | `2175` | `resolve_vault_space_root`, `clamp_escaped_vault_path`, `install_runtime_hooks` | 실수·질책 발생 시 `948.mistake` + `921.skill` 이중 기록 누락을 적발해 `COMPLIANCE_BLOCKED` 처벌을 집행하는 감시자 |
| `35` | `,.code-harness.setting.accent-color.py` | `257` | `AccentColorManager` | 볼트 상태·모드별 옵시디언 강조색(Accent Color) 코어 제어 매니저 |
| `36` | `,.code-harness.setting.graph.engine.py` | `831` | `is_knowledge_surface_dir`, `contains_knowledge_surface`, `compute_dynamic_maintenance_exclusions` | 지식 표면 폴더와 유지보수(`900.maintenance`) 폴더를 구분해 그래프 뷰 제외 필터를 동적 계산하는 엔진 |
| `37` | `,.code-harness.skill-resolver.py` | `108` | `resolve_skills_for_task`, `get_preloaded_context` | 작업 질의에 맞는 `921.skill` 문서를 시스템 차원에서 자동 탐색·선주입하는 리졸버 |
| `38` | `,.code-harness.skill-standardizer.py` | `1361` | `sha12_text`, `OnnxCrossEncoderReranker`, `SkillAIStandardizerEngine` | `Harrier` 1024d 임베딩 + ONNX 리랭커 기반 스킬 문서 구조·메타데이터 표준화 엔진 |
| `39` | `,.code-harness.smoke-verifier.py` | `96` | `capture_bg_screenshot`, `ghost_cursor_act`, `run_full_smoke` | 포커스 스틸 없는 백그라운드 스크린샷 캡처 및 UI 스모크 검증기 |
| `40` | `,.code-harness.stack.py` | `245` | `resolve_vault_root`, `load_fault_tolerance`, `get_breaker` | 서킷 브레이커(`CircuitBreaker`) 및 코드 하네스 공통 스택 초기화 로더 |
| `41` | `,.code-harness.test-gate.py` | `781` | `GateIssue`, `CommandResult`, `rel` | `911.test` → `912-001.py` → `cskill` 3단계 코드 승격 게이트 핵심 검증 로직 |
| `42` | `,.code-harness.variable.py` | `1128` | `calculate_next_heading`, `load_file_content` | `VAULT_ROOT` 앵커 탐색 및 `915.code-harness` 전역 경로·상수 단일 진실 공급원(SSOT) |
| `43` | `,.code-harness.virtual-fs.py` | `1706` | `heal_path`, `VaultMetaPathFinder`, `install` | `VaultMetaPathFinder` 파이썬 임포트 훅 및 잘못된 상위 경로 접근을 런타임에 안전 리다이렉트하는 가상 파일시스템 |
| `44` | `,.code-harness.yaml-frontmatter-layout.py` | `485` | `LayoutIssue`, `rel`, `clean_frontmatter_boundaries` | 전 도메인 마크다운 YAML 프론트매터 키 순서·경계선 정규화 린터 |
| `45` | `,.code-harness.yaml-rule-drift.py` | `1033` | `DriftIssue`, `rel`, `issue` | `921-700.ruskill` 규칙과 실제 노트 YAML 속성 간 드리프트(괴리) 전수 탐지기 |
| `46` | `,.code-harness.yaml.py` | `4336` | `list_frames`, `load_frame`, `now_iso` | `gen_vault_note_from_title()` — PMEST v2 파일명 + 표준 YAML + `941.raw` 라우팅 + `file_id` 원클릭 생성 코어 엔진 (4,336줄) |

---

## 5. Complete `79`-Plugin `.obsidian/` Ecosystem & Core Configuration Baseline

### 5.1. 자체 개발 · 로컬 패치 인하우스 플러그인 (`17`개 전수 목록)

| 순번 | 플러그인 ID | 표시 이름 | 버전 | 기본 상태 | 볼트 내 커스텀 엔지니어링 역할 |
|:---:|:---|:---|:---|:---:|:---|
| `01` | `bases-thumbnail-action` | **Bases Thumbnail Action** | `1.0.0` | `Active` | Opens external YouTube links when clicking Bases card thumbnails and suppresses note popovers on covers. |
| `02` | `catbox-uploader` | **Catbox Uploader** | `1.5.0` | `Active` | Paste/drop local images, upload with a fast Uguu-first path, and insert markdown links with diagnostics and Catbox/Litterbox/local fallback. |
| `03` | `empty-save-guard` | **Empty Save Guard** | `1.0.0` | `Active` | Blocks zero-byte writes that would wipe non-trivial files. Fixes vscode-editor JSON/YAML race condition. |
| `04` | `folder-note-opener` | **Folder Note Opener** | `1.0.0` | `Active` | Open folder note (X/X.md) in main editor on folder right-click. Bypasses folder-notes + file-tree-alternative conflicts. |
| `05` | `llm-wiki-chat` | **스페짱 · LLM Wiki Read-Only Chat** | `1.1.0` | `Active` | 스페짱(Spe-chan) — 940.llm-wiki + 921.skill 통합 읽기전용(Read-Only Jail) 로컬 AI 챗봇 (Harrier 1024d + Jina Reranker v3.5 + 3-Model CoT) |
| `06` | `maintenance-filter` | **Maintenance Filter** | `1.0.0` | `Active` | 특정 폴더를 영역별(검색/Quick Switcher/링크 제안/Graph view/Backlinks)로 독립 토글해 숨기는 커스텀 필터. |
| `07` | `media-speed-controller` | **Media Speed Controller** | `0.1.0` | `Active` | Adds 5x-capable playback speed controls for all audio and video elements in the vault. |
| `08` | `mx-inline-seek` | **MX Inline Seek** | `0.1.0` | `Active` | Route Media Extended inline YouTube timestamp links to the existing embedded player instead of opening a new tab. |
| `09` | `pdf-annotation-merger` | **PDF Annotation Merger** | `1.0.0` | `Active` | Merge original PDF documents with handwritten annotations (.annot.json) into a single annotated PDF file. |
| `10` | `resize-observer-silencer` | **ResizeObserver Silencer** | `1.0.0` | `Active` | Suppresses benign 'ResizeObserver loop completed with undelivered notifications' console errors. |
| `11` | `safe-startup-mediator` | **Safe Startup Mediator** | `1.3.5` | `Active` | Optimizes startup, indexing, outlink panes, and large markdown table rendering without silently disabling plugins or closing views. |
| `12` | `status-bar-collapse` | **Status Bar Collapse** | `1.0.0` | `Active` | 상태표시줄이 길어 우측 본문으로 넘칠 때 가로 스크롤로 가두고, 좌측 끝 < / > 토글로 접었다 펼친다. |
| `13` | `task-alarm` | **Task Alarm** | `1.0.0` | `Active` | Notify open homework in 211.tasks daily/weekly/monthly 숙제·제출물 sections. Does not scan the rest of the vault. |
| `14` | `vault-spaced-repetition` | **Vault Spaced Repetition** | `0.3.0` | `Active` | Anki-grade native spaced repetition for Obsidian. FSRS scheduler, vault-stored review log, mobile-ready, full statistics. |
| `15` | `vertical-tabs-local-guard` | **Vertical Tabs Local Guard** | `0.1.0` | `Active` | Reapplies local Vertical Tabs active-leaf patches after plugin updates. |
| `16` | `vocal-growth-live` | **Vocal Growth Live Graph** | `1.0.0` | `Active` | Real-time past-to-present Vocal Core 6 Plotly growth graph synchronizer for 325.vocal notes. |
| `17` | `vscode-guard` | **VSCode Guard** | `0.1.0` | `On-Demand` | Keeps local VSCode Editor run patches and terminal safety settings after plugin updates. |

### 5.2. 커뮤니티 · 시각화 · 생산성 플러그인 (`62`개 전수 목록)

| 순번 | 플러그인 ID | 표시 이름 | 버전 | 기본 상태 | 기능 요약 |
|:---:|:---|:---|:---|:---:|:---|
| `01` | `3d_embeds` | Embed 3D | `1.2.2` | `Active` | Embed and view 3DModels in markdown notes. Supports .stl, .obj, 3mf, fbx and .gltf/.glb files. |
| `02` | `activity-graph` | Activity Graph | `1.3.0` | `Active` | Render activity heatmaps from Dataview query results. |
| `03` | `alarm-timer` | Alarm and Timer | `1.1.0` | `Active` | Set one-time alarms for specific times and run multiple countdown timers from the sidebar. |
| `04` | `calendar-bases` | Calendar Bases | `0.2.8` | `Active` | Adds a calendar layout to bases so you can display notes with dates in an interactive calendar view. |
| `05` | `chronology` | Chronology | `1.1.14` | `Active` | Provides a calendar and a timeline of the notes creation and modification |
| `06` | `chronos` | Chronos Timeline | `4.1.2` | `Active` | Render interactive timelines in your notes from Markdown. |
| `07` | `combo-colors` | Combo Colors | `1.4.2` | `Active` | Automatically apply color to fighting game combo notations. |
| `08` | `copy-section` | Copy Section | `1.2.4` | `Active` | adds a Copy button to the top of Headed sections |
| `09` | `custom-font-loader` | Custom Font Loader | `2.1.0` | `Active` | Customize your vault with any custom font you want, including support for Android and iOS. |
| `10` | `drawio-obsidian` | Diagrams | `1.5.4` | `Active` | Draw.io diagrams for Obsidian. This plugin introduces diagrams that can be included within notes or as stand-alone files. Diagrams are created as SVG files (although .drawio extensions are also suppor |
| `11` | `emoji-shortcodes` | Emoji Shortcodes | `2.2.0` | `Active` | This Plugin enables the use of Markdown Emoji Shortcodes :smile: |
| `12` | `file-cleaner-redux` | File Cleaner Redux | `1.16.1` | `Active` | Help you to clean empty files and unused attachments in the vault. |
| `13` | `file-tree-alternative` | File Tree Alternative | `2.6.0` | `Active` | This plugin allows you to have an alternative file tree view. |
| `14` | `floating-toc` | floating toc | `2.7.1` | `Active` | This is a floating Toc plugin that hovers a table of content containing a header level on the notes sidebar. |
| `15` | `flowcharts` | Flowcharts | `1.0.0` | `Active` | Render flowcharts using flowchart.js syntax. |
| `16` | `folder-notes` | Folder notes | `1.8.26` | `Active` | Create notes within folders that can be accessed without collapsing the folder, similar to the functionality offered in Notion. |
| `17` | `freedraw-pdf` | Freedraw PDF | `0.13.7` | `Active` | Freehand PDF annotation workspace with vault-backed sidecar saves. |
| `18` | `grafika` | Grafika | `0.6.0` | `Active` | Create and analyze with AI: plots, charts and graphs with Apache ECharts, Chart.js and vis.js network and timeline graphs |
| `19` | `initiative-tracker` | Initiative Tracker | `13.0.21` | `Active` | TTRPG Initiative Tracker for Obsidian.md |
| `20` | `maps` | Maps | `0.2.2` | `Active` | Adds a map layout to bases so you can display notes as an interactive map view. |
| `21` | `maps-timeline` | Maps Timeline | `1.0.2` | `Active` | View your Google Maps Timeline location history on an interactive map — visits, routes and exact GPS tracks, with day and date-range pickers. |
| `22` | `markdown-mindmap` | Markdown Mindmap | `1.4.3` | `Active` | Render mind maps, gantt charts, kanban boards, and causal-loop diagrams from your notes' frontmatter links. Each map is one fenced code block. |
| `23` | `markwhen` | Markwhen | `0.0.7` | `Active` | Create timelines, gantt charts, calendars, and more using markwhen. |
| `24` | `md-table-to-latex` | Markdown Table to LaTeX | `1.0.1` | `Active` | Convert Markdown tables to LaTeX array/tabular environments. 将选中的 Markdown 表格转换为 LaTeX 表格。 |
| `25` | `media-extended` | Media Extended | `4.2.9` | `On-Demand` | Media(Video/Audio) Playback Enhancement for Obsidian.md |
| `26` | `media-notes` | Media Notes | `1.3.0` | `On-Demand` | Take notes on YouTube videos and podcasts with media controls and timestamps. |
| `27` | `obsidian-banners` | Banners | `1.3.3` | `Active` | Add banner images to your notes! |
| `28` | `obsidian-editor-shortcuts` | Code Editor Shortcuts | `1.14.0` | `Active` | Add keyboard shortcuts (hotkeys) commonly found in code editors such as Visual Studio Code (vscode) or Sublime Text |
| `29` | `obsidian-emoji-toolbar` | Emoji Toolbar | `1.0.0` | `Active` | Quickly search for and insert emojis into your notes. |
| `30` | `obsidian-excalidraw-plugin` | Excalidraw | `2.29.0` | `Active` | Sketch Your Mind. Edit and view Excalidraw drawings. Enter the world of 4D Visual PKM. |
| `31` | `obsidian-footnotes` | Footnote Shortcut | `0.2.0` | `Active` | Footnotes without the mouse: insert, navigate, and popup-edit them where you're typing, with a built-in linter that keeps numbering and placement tidy. |
| `32` | `obsidian-graphviz` | Obsidian Graphviz | `1.0.5` | `Active` | Render Graphviz Diagrams |
| `33` | `obsidian-hider` | Hider | `1.7.1` | `Active` | Hide UI elements such as tooltips, status, titlebar and more. |
| `34` | `obsidian-journey-plugin` | Journey | `0.0.7` | `Active` | Discover the stories between your notes. |
| `35` | `obsidian-kanban` | Kanban | `2.0.51` | `Active` | Create markdown-backed Kanban boards in Obsidian. |
| `36` | `obsidian-list-callouts` | List Callouts | `1.2.9` | `Active` | Create simple callouts in lists. |
| `37` | `obsidian-markmind` | Markmind | `3.7.4` | `Active` | This is a mindmap , outline software. |
| `38` | `obsidian-plotly` | Plotly | `0.0.6` | `Active` | Obsidian plugin, which allow user to embed Plotly charts into markdown notes. |
| `39` | `obsidian-regex-replace` | Regex Find/Replace | `1.2.0` | `Active` | Find and replace text using regular expressions. |
| `40` | `obsidian-sidebar-expand-on-hover` | Sidebar Expand on Hover | `1.0.1` | `Active` | This Obsidian plugin expands or collapses the sidebars based on mouse hovering on the ribbons. |
| `41` | `obsidian-spaced-repetition` | Spaced Repetition | `1.15.4` | `On-Demand` | Fight the forgetting curve by reviewing flashcards & entire notes using spaced repetition. Organize your flashcards into decks and learn only the cards you need to recall. |
| `42` | `obsidian-style-settings` | Style Settings | `1.0.9` | `Active` | Offers controls for adjusting theme, plugin, and snippet CSS variables. |
| `43` | `obsidian-task-progress-bar` | Task Genius | `9.13.1` | `Active` | Comprehensive task management that includes progress bars, task status cycling, and advanced task tracking features. |
| `44` | `obsidian-tasks-plugin` | Tasks | `8.6.0` | `Active` | Track tasks across your vault. Supports due dates, recurring tasks, done dates, sub-set of checklist items, and filtering. |
| `45` | `obsidian-timeline` | Timeline | `1.1.0` | `Active` | Used to build great timelines |
| `46` | `obsidian42-brat` | BRAT | `2.2.0` | `Active` | Easily install plugin beta versions for testing. |
| `47` | `recent-files-obsidian` | Recent Files | `1.7.10` | `Active` | List files by most recently opened. |
| `48` | `reverysky-map` | ReverySky 3D Graph | `1.5.1` | `Active` | Explore your vault through an immersive Unity-powered 3D graph. |
| `49` | `routine-timeline` | Routine Timeline | `0.24.0` | `Active` | Plan tasks and routines with Table, Board, Timeline and Calendar views, saved view tabs, repeating tasks, notes, and read-only Google Calendar events. |
| `50` | `rpg-manager` | RPG Manager | `4.2.1` | `Active` | Manage your Tabletop Role Playing Game Campaigns in Obsidian |
| `51` | `rss-dashboard` | RSS Dashboard | `2.7.1` | `Active` | A dashboard for organizing and consuming RSS feeds, YouTube channels, and podcasts with smart tagging, media playback, and seamless content flow. |
| `52` | `smart-link-alias` | Smart Link Alias | `1.0.0` | `Active` | Enhance your internal links management with dynamic alias customization. Display short, full, or combined titles for your notes effortlessly. |
| `53` | `solo-rpg-toolkit` | Solo RPG Toolkit | `0.7.17` | `Active` | Random generator tools geared towards solo TTRPG gameplay |
| `54` | `stock-blocks` | Stock Blocks | `1.6.1` | `Active` | Display stock prices, changes, and sparkline charts in your notes with multi-stock lists and single stock charts. |
| `55` | `tag-wrangler` | Tag Wrangler | `0.6.5` | `Active` | Rename, merge, toggle, and search tags from the tags view; create/open tag pages, and more. |
| `56` | `tasknotes` | TaskNotes | `4.14.0` | `Active` | Note-based task management with calendar, pomodoro and time-tracking integration. |
| `57` | `tasks-calendar-wrapper` | Tasks Calendar Wrapper | `0.3.7` | `Active` | Shows the tasks in your vault on a timeline view, based on the Tasks Calendar and Tasks Timeline scripts. |
| `58` | `timecodes` | Timecodes | `0.0.2` | `Active` | Converts raw text timecodes into clickable URLs if a note contains a link to a video |
| `59` | `tradingview-widget` | TradingView Widget | `1.0.3` | `Active` | Open a customizable stock view that displays charts and widgets (heatmaps, tickers, symbol summaries, advanced charts, and more) powered by TradingView's free embeddable widgets. |
| `60` | `various-complements` | Various Complements | `11.4.2` | `Active` | This plugin enables you to complete words like the auto-completion of IDE |
| `61` | `vscode-editor` | VSCode Editor | `1.0.5` | `Active` | Edit Code Files like VSCode. |
| `62` | `zoom-map` | TTRPG Tools - Maps | `2.3.0` | `Active` | A toolbox for interactive fantasy maps. |

### 5.3. `.obsidian/` 코어 설정 운영 3대 철칙
1. **표면 뷰(`{folder}.md` + `,.map.*` + `,.base.*`)와 본문(`941.raw`) 물리 분리**: 파일 탐색기 트리가 수천 개 노트로 마비되지 않도록 표면 폴더는 안내판과 데이터베이스 뷰만 렌더링.
2. **Electron `Partitions` 보존 원칙**: 캐시 정리 시 `%APPDATA%\obsidian\IndexedDB` 및 순수 Chromium 캐시만 정리하고, 플러그인 활성화 토큰(`enable-plugin-<appId>`)이 담긴 `Partitions` 폴더는 절대 이동·삭제하지 않음으로써 79개 플러그인 초기화(Restricted Mode 리셋)를 원천 방지.
3. **Bases 카드 썸네일 즉시성 보장**: 외부 스크립트가 대량 분류한 직후에도 `0개 결과`가 뜨지 않도록 `file.inFolder(...)` 조건과 프론트매터 필터를 결합하고 `cover: "[[파일명.ext]]"` 위키링크 바인딩을 강제.

---

## 6. External Browser, Chatbot, Remote Bridge, 3-Way Sync & Security Vault Baseline

| 영역 | 상시 구성 요소 | 핵심 아키텍처 및 안전 수칙 |
|:---|:---|:---|
| **에이전트 전용 격리 브라우저** | **`BrowserOS neo`** (`:9210` MCP / `:9110` CDP) | • 개인용 일상 브라우저(Brave/Chrome) 및 독립 프로필 음악 PWA(`Brave-YouTubeMusic`)와 100% 분리(`R-SAFE-14`)<br/>• 모든 자동화는 백그라운드 탭에서만 수행하여 사용자 창 포커스 탈취 `0건` 보장 |
| **브라우저 북마크 AI 챗봇** | **`AI Research Chat`** (MV3 사이드패널) + `bookmark-obsidian-sync.py` | • **Search-First 아키텍처**: 북마크 세부주제(Aspect) 벡터 검색 + CDP 숨김 탭 실본문·댓글 실시간 크롤링 + 실시간 웹 검색 병렬 결합<br/>• 단어 경계 정규식 및 안전 가드로 오탐 차단 + **브라우저 북마크 폴더 ↔ 옵시디언 폴더 1:1 양방향 동기화** |
| **디스코드 & 모바일 원격 제어** | **`HerdrBridge`** (`discord-bridge.py`) + `remote-bridge.py` | • 외출 중 디스코드 채널 메시지 또는 스마트폰 Google Drive 원격 큐(`931-001.herdr/,.remote.herdr/pending`)만으로 집 PC 6-CLI 군단에 작업 발주 및 산출물 수령 |
| **야간 5대 웹챗 자동 수확 & NLM 교차검증** | `webchat-evening-batch.py` + `NotebookLM` RPC | • 매일 저녁 **ChatGPT · Claude · Gemini · Grok · AI Mode** 대화를 무인 크롤링·평서문 정제(`LOST: 0` 검증) 후 `941.raw` 적재<br/>• 강의·회의 사실 검증 시 **`최신 음성 STT > 최신 공지 > 최신 슬라이드 > 계획서`** 승리 계층 엄수 |
| **3중 실시간 동기화** | **Google Drive** + **Syncthing** + **Git** | • **Google Drive**: 로컬→클라우드 ~8초 디바운스 + 30분 주기 전체 정합성 싱크<br/>• **Syncthing**: 스마트폰 카메라·스크린샷·녹음 `_inbox` 실시간 미러링 (**절대 Move/삭제 금지, Copy-Only 불변 원칙**)<br/>• **Git**: 커널 뮤텍스 싱글톤 데몬(`sync.git.py`)을 통한 자동 백업 |
| **외부 물리 분리 보안 금고** | `vault/private/security/` + `7-Zip` + `pCloud` | • 민감 금융·복구키·인증서는 메인 볼트 바깥 디렉터리로 물리 분리하여 Git·클라우드·AI 훅 노출 원천 차단<br/>• `7-Zip` AES-256(`-mhe=on` 파일명 헤더 암호화) + `icacls` ACL 접근 잠금(`lock.bat`) + `pCloud` 원격 암호화 백업 이중화 |
