# 01. 상시 운영 기본 시스템 구성 백과사전 (Universal Baseline System Configuration)

> 본 문서는 자율형 옵시디언 LLM-Wiki 볼트를 무중단으로 운영하기 위한 **OS 무창 데몬·스케줄러(`913.shell`) · 로컬 AI 모델 및 벡터 DB(`914.data`) · 멀티 CLI 오케스트레이션 및 `OpenDecider` 4-Stage · `46`개 자율 방어 코드 하네스 모듈 패턴(`915.code-harness`) · `.obsidian` 추천 플러그인 아키텍처 · 격리 브라우저/동기화/보안 백업**의 보편 레퍼런스 명세서입니다.

---

## 1. OS · Shell · Windowless Daemon & Scheduled Automation Baseline (`913.shell`)

### 1.1. `No-Console-Flash` 무창(Windowless) 백그라운드 데몬 표준
데스크톱 환경에서 백그라운드 워처·동기화 스크립트가 실행될 때 콘솔 창이 깜빡이면 작업 몰입이 깨지고 포커스를 탈취당합니다. 이를 물리적으로 차단하기 위해 다음 4대 원칙을 전역 강제합니다:

1. **서브프로세스 플래그 강제 (Windows)**: 모든 Python `subprocess` 호출 시 `CREATE_NO_WINDOW (0x08000000)` 및 `STARTUPINFO(dwFlags=STARTF_USESHOWWINDOW, wShowWindow=0)` 필수 지정.
2. **GUI 서브시스템 바이너리 사용**: 옵시디언 플러그인·Electron·백그라운드 워처에서 파이썬 호출 시 `python.exe` 대신 콘솔 할당이 없는 **`pythonw.exe`**(`windowsHide: true`, `shell: false`) 호출 (Linux/macOS는 표준 background service / `systemd --user` / `launchd` 사용).
3. **무창 래퍼 경유 스케줄링**: Windows 작업 스케줄러에 `.bat`/`.cmd`를 직접 등록하지 않고 `wscript.exe //B //Nologo "<VAULT_ROOT>\900.maintenance\910.codey\913.shell\913-002.vbs\,.vbs.<task>.vbs"` (`WshShell.Run ..., 0, False`) 형태로만 등록.
4. **커널 네임드 뮤텍스 싱글톤(Singleton Lock)**: 주기적 스케줄러 중복 실행을 막기 위해 `ObsidianVaultGitSyncSingleton`, `cdp_lock`, `heavy_model_infer_lock`, `RegistryLock` 등 OS/파일 락으로 단일 인스턴스만 유지.

### 1.2. 로컬 루프백(`127.0.0.1`) 권장 서비스 포트 맵 (Local TCP Port Map)

모든 포트는 루프백(`127.0.0.1`) 전용이며 환경변수로 자유롭게 재지정할 수 있습니다.

| 바인딩 주소 및 포트 | 권장 프로세스 / 데몬 | 핵심 프로토콜 및 아키텍처 역할 |
|:---|:---|:---|
| **`127.0.0.1:8000`** | `opendecider serve` (Calibrated SLM) | **Stage 1 System-One 확률 라우터** (`/v1/systemone`): 단일 순전파 신뢰도 `p >= 0.85` 시 LLM 없이 파이썬 코드로 즉시 직행(`sharp`) |
| **`127.0.0.1:9110`** | Isolated Agent Browser CDP (`cdp_lock` 보호) | 에이전트 전용 격리 브라우저의 숨김 백그라운드 탭 DOM 추출 전용 포트 |
| **`127.0.0.1:9210`** | Isolated Agent Browser MCP (`/mcp`) | 멀티 CLI가 공용으로 연결하는 **Streamable HTTP 브라우저 MCP 서버** |
| **`127.0.0.1:11434`** | `Ollama` 싱글톤 | 로컬 경량 Instruct LLM, 확률 라우터 SLM, 768d 임베딩 모델 상시 서빙 |
| **`127.0.0.1:18793`** | `llm-wiki-readonly-chat.py` (Read-Only 웜 데몬) | **`sys.addaudithook` 커널 레벨 Read-Only 감옥** 위에서 BM25F + `LanceDB` 하이브리드 검색 및 `/reload` 무중단 인덱스 핫리로드 수행 |
| **`127.0.0.1:27129`** | `Obsidian Local Reload / Bridge` | 옵시디언 로컬 플러그인 상태 점검 및 무간섭 브릿지 |

### 1.3. `913.shell` 셸 계층 구조 & 보편 스케줄러 블루프린트 (10대 표준 작업)
- **`913-001.bat/`**: CLI 단축 명령어 래퍼 및 원클릭 운영 런처 (Windows)
- **`913-002.vbs/`**: 콘솔 깜빡임 `0건`을 보장하는 무창 실행 래퍼 (Windows)
- **`913-003.xml/`**: Windows 작업 스케줄러 등록용 정규 XML (`Logon + SessionUnlock` 트리거 등)
- **`913-004.ps1/` & `913-005.sh/`**: PowerShell 및 POSIX(`bash`/`zsh`) 크로스플랫폼 오케스트레이션 스크립트
- **`913-006.icon/`**: 작업별 전용 아이콘 자산

| 순번 | 표준 스케줄러 작업명 | 권장 주기 / 트리거 | 핵심 자동화 역할 |
|:---:|:---|:---|:---|
| `01` | `obsidian-vector-db-sync` | 매 `30분` (`PT30M`) | 파일 해시 변경분만 무창 증분 벡터 임베딩 갱신(`embedding.auto-sync.py`) 후 `:18793/reload` 무중단 핫리로드 |
| `02` | `registry-auto-id` | 로그온 + 주기 스캔 | 신규 생성 마크다운 문서의 10자리 불변 `file_id` 누락 스캔 및 자동 발급 |
| `03` | `ghost-outlink-healer` | 일일 1회 | 끊어진 유령 아웃링크(`ids_outlink`) 탐지 및 벡터·레지스트리 기반 자동 치유 |
| `04` | `sync-cli-constitution` | 변경 감지 / 일일 | `920.prompt/921.skill` SSOT → `AGENTS.md`/`CLAUDE.md`/`GEMINI.md`·`.agents/skills`·`.cursor/rules` 정기 동기화 |
| `05` | `code-harness-daily-audit` | 일일 1회 | 46개 코드 하네스 메쉬 및 `921-700.ruskill` 규격 준수 여부 자동 감사 |
| `06` | `autonomous-surface-loop` | 상시 / 주기 워치 | `AGENTS.md`, `922.baking`, `921.skill`, 표면 폴더(`000`~`400`) 무결성 감시 |
| `07` | `mistake-embed-sync` | 변경 감지 폴링 | `948.mistake` 실수 원장 `nomic-embed-text-v1.5` 768d NPZ 임베딩 갱신 |
| `08` | `inbox-activity-log` | 주기 스캔 | `000.inbox` 및 `400.resource` 유입 파일 활동 감사 로깅 (`490.log`) |
| `09` | `vault-git-and-snapshot-backup` | 주기 / 일일 | 싱글톤 락 기반 Git 자동 백업 및 외장 증분 암호화 스냅샷 생성 |
| `10` | `web-digest-collector` | 일일 (선택) | 지정된 RSS·웹 리서치·다이제스트 수집 및 `941.raw` 표준 마크다운 정제 적재 |

---

## 2. Local AI Models · Vector DB · Hybrid RAG Baseline (`914.data`)

### 2.1. 보편 로컬 AI 역할별 모델 스택 (클라우드 API 비용 `0원`)

특정 벤더나 하드웨어에 종속되지 않도록 **역할(Role) 중심의 오픈소스 모델 계층**으로 설계합니다.

| 역할 구분 | 대표 오픈소스 모델 예시 | 권장 런타임 / 가속 | 차원 / 규격 | 볼트 내 전담 역할 |
|:---|:---|:---|:---|:---|
| **주력 고밀도 임베딩** | `Harrier-OSS-v1-0.6B` (또는 `BGE-M3` / `E5`) | ONNX / CUDA / CPU | `1024d` | `940.llm-wiki`·`921.skill`·볼트 문서 전수 하이브리드 벡터 검색(`hsearch`, `houtlink`, `hclassify`) |
| **경량 원장 임베딩** | `nomic-embed-text-v1.5` | Ollama (`:11434`) + `912-009.npz` | `768d` | `948.mistake` 역대 AI 실수 원장 초고속 유사도 검색(`embedding.memory.py`) |
| **교차 리랭커** | `Jina-Reranker-v3.5` (또는 `BGE-Reranker-v2`) | ONNX / CUDA Cross-Encoder | Cross-Encoder | 1차 벡터+BM25 후보군을 정밀 재순위화하여 상위 정답 정확도 극대화 |
| **확률 라우터 (SLM)** | `opendecider-small` (또는 경량 분류 SLM) | Ollama (`:11434`) → `:8000` | Calibrated SLM | 단일 순전파 확률 `p` 산출 (`p >= 0.85` 시 LLM 없이 파이썬 코드 즉시 자동 실행 `hsolve`) |
| **주력 위키 어시스턴트** | 4B급 로컬 Instruct LLM (예: `Qwen` 4B 계열) | Ollama (`:11434`) / `:18793` | `~4B` (Q4/Q8) | 옵시디언 내장 Read-Only 각주 챗봇 응답 및 위키 허브 브리핑(`twiki`) |
| **보조 검증·압축 SLM** | 1.5B~2B급 초경량 로컬 SLM | Ollama (`:11434`) | `1.5B~2B` | `twiki` / `tgrep` 다단계 로컬 추론 검증, 문맥 압축 및 고속 요약 |

### 2.2. `LanceDB` (`914-010.lance/`) 3대 하이브리드 테이블 & 무중단 증분 동기화 구조
1. **`llm_wiki.lance`**: `942.wiki` 허브 + `941.raw` 정본 마크다운 본문/헤딩 청크 하이브리드 인덱스 (Dense Vector + BM25 Full-Text).
2. **`skills_compact.lance`**: `920.prompt/921.skill` 6종 스킬 + `912-001.py` 도구 매핑 전용 압축 인덱스 (`hskill`, `hcode` 0.1초 응답).
3. **`selective_docs.lance`**: 핵심 아키텍처·헌법(`921-700.ruskill`) 정밀 검색 테이블.
- **무중단 핫리로드 루프**: `embedding.auto-sync.py`가 파일 해시 변경분만 증분 임베딩한 뒤 `POST http://127.0.0.1:18793/reload`를 호출하여 옵시디언 내장 Read-Only 챗봇이 재시작 없이 즉시 최신 노트를 참조합니다.

---

## 3. Multi-CLI Orchestration & 4-Stage OpenDecider Fork Harness Baseline

### 3.1. 역할 분리 멀티 CLI 패널 편제 패턴

단일 세션에 모든 작업을 몰아넣지 않고, 터미널 멀티플렉서(`tmux` / Windows Terminal / 멀티 패인 CLI 호스트) 안에서 **역할과 권한 경계를 분리**합니다.

| 패널 역할명 | 권장 CLI / 모델 계층 | 전담 임무 및 권한 경계 |
|:---|:---|:---|
| **`Leader`** | 고추론 모델 CLI | 전체 아키텍처 총괄 지휘, 병렬 작업 배분(`fanout`), 최종 체크리스트 검수 (`verify-only` 모드 시 직접 코드 수정 금지) |
| **`Builder`** | 코딩 특화 모델 CLI | 대규모 신규 기능 구현, 복합 파이프라인 설계 및 문서화 |
| **`Worker`** | 고속 멀티태스크 CLI | 병렬 리서치, 문서 정제, 데이터 마이그레이션 실행 |
| **`Verifier`** | 테스트·루프 전용 CLI | 단위/통합 테스트 작성 및 `/loop` 기계 검증 통과(`PASS`) 시까지 자율 재작업 |

### 3.2. `.agents/skills/` 보편 CLI 네이티브 스킬 목록 (`sync.cli.py` 자동 미러링)

| 순번 | 스킬 폴더명 (`.agents/skills/`) | 핵심 기능 및 자동 활성화 시점 |
|:---:|:---|:---|
| `01` | **`bases`** | 옵시디언 코어 데이터베이스 뷰(`.base`) 필터·수식·카드 레이아웃 설계 규격 |
| `02` | **`browser-automation`** | 사용자의 일상 브라우저와 분리된 격리 브라우저(`127.0.0.1:9210/mcp`) 백그라운드 제어 표준 |
| `03` | **`canvas.json`** | 옵시디언 JSON Canvas(`.canvas`) 노드·엣지·그룹 시각 다이어그램 생성 규격 |
| `04` | **`loop`** | `/loop` 명시 호출 시 테스트+로그+재작업을 `Pass`까지 반복하는 자율 검증 루프 |
| `05` | **`markdown`** | 볼트 마크다운 헤딩·리스트·수식·폴더 구조 및 YAML 프론트매터 작성 표준 |
| `06` | **`test-code-inspector`** | `911.test` 7-Layer 정적·동적 코드 검수(`ruff`/`pyflakes`/`mypy`/`bandit`/`vulture`/`pytest` + `R-PY-01..10`) |

---

## 4. Complete `46`-Module `915.code-harness/` Kernel & Self-Healing Architecture Catalog

볼트의 물리적 질서와 데이터 무결성을 수호하는 `900.maintenance/910.codey/915.code-harness/` 계층의 **`46`개 표준 방어·복구 모듈 패턴**입니다.

| 순번 | 모듈 파일명 (`915.code-harness/`) | 대표 공개 심볼 | 핵심 아키텍처 방어·복구 역할 |
|:---:|:---|:---|:---|
| `01` | `,.code-harness.accent-color.py` | — | 옵시디언 테마 강조색 설정 위임 호환성 래퍼 (`setting.accent-color.py` 연결) |
| `02` | `,.code-harness.decider-engine.py` | `resolve_vault_root`, `load_decider_state`, `save_decider_state` | OpenDecider 로컬 확률 판단 상태 관리, 세션 라우팅 로그 및 임계값(`P >= 0.85`) 결정 엔진 |
| `03` | `,.code-harness.embedding-registry.py` | `RegistryEntry`, `rel`, `normalize_target` | `file_id` 레지스트리 엔트리 파싱 및 임베딩 벡터 인덱스 대상 정규화 모듈 |
| `04` | `,.code-harness.fault-tolerance.py` | `resolve_anchor_vault_root`, `purge_vault_bytecode`, `get_mitigation_log` | 백그라운드 프로세스 과부하 방지(`PressureGovernor`), 바이트코드 정화 및 범용 장애 격리 하네스 |
| `05` | `,.code-harness.file-resolver.py` | — | 레거시 파일 경로 해석 호환 래퍼 (`file-tracker.py`로 단일 위임) |
| `06` | `,.code-harness.file-tracker.py` | `is_excluded`, `iter_md_files`, `iter_all_files` | 10자리 불변 `file_id` ↔ 실제 볼트/Raw 파일 경로 O(1) 인덱스 빌더 및 탐색기 |
| `07` | `,.code-harness.folder-activity-log.py` | `resolve_path`, `rel`, `is_relative_to` | 프로파일 기반 폴더 변경 스냅샷, SHA256 무결성 검증 및 JSONL 활동 감사 로거 |
| `08` | `,.code-harness.folder-resolver.py` | `build_index`, `find_duplicate_folders`, `resolve` | 폴더 노트 `file_id` 기반 실시간 폴더 위치 추적 및 중복 폴더 탐지기 |
| `09` | `,.code-harness.folder.py` | `get_folder_hierarchy`, `resolve_parent`, `name_key_for_folder` | 폴더 계층 트리 해석, 상위/하위 이름 키 추출 및 폴더 노트(`{folder}/{folder}.md`) 경로 표준화 |
| `10` | `,.code-harness.harness.py` | — | 신규 파이썬 스크립트 공통 의무 임포트 하네스 (표준 인코딩·경로·예외 보호 계층) |
| `11` | `,.code-harness.harrier-decider.py` | `load_id_maps`, `resolve_vault_or_raw_path`, `resolve_fid_and_stem` | 고밀도 임베딩 + `LanceDB` + Cross-Encoder 리랭커 + `OpenDecider` 하이브리드 검색·결심 통합 엔진 |
| `12` | `,.code-harness.helper.py` | `import_id`, `gen_id`, `register` | 공통 ID 발급(`gen_id`), 레지스트리 등록·조회(`register`/`lookup`) 핵심 유틸리티 |
| `13` | `,.code-harness.id.py` | `reset_cache`, `parse_registry_row`, `registry_outlink_target` | 볼트 전역 10자리 `file_id` 파싱, 아웃링크 키 매핑 및 레지스트리 캐시 단일 책임 모듈 |
| `14` | `,.code-harness.lookup-paths.py` | `resolve_file`, `resolve_folder`, `format_path` | CLI/스크립트용 `file_id` 및 폴더 ID → 실제 디스크 경로 고속 변환기 |
| `15` | `,.code-harness.markdown-ref-policy.py` | `RefIssue`, `rel`, `iter_md_files` | 마크다운 본문 내 깨진 위키링크, 분할 참조 및 산문 인용 규격 감사·정규화 엔진 |
| `16` | `,.code-harness.mkdn.py` | `extract_wikilinks`, `build_wikilink`, `to_wikilink_or_marker` | YAML 프론트매터 분리(`yaml_split`), 위키링크 추출·조립 단일 책임 파서 |
| `17` | `,.code-harness.mobile-folder-healer.py` | `find_vault_root`, `is_protected_canonical_rel`, `normalize_legacy_rel` | 멀티 기기 동기화 과정에서 생기는 비정규 레거시 폴더 경로 탐지 및 정규 리프 자동 치유기 |
| `18` | `,.code-harness.mobile-sync-governor.py` | `find_vault_root`, `discover_and_heal_base_targets`, `should_ban_file_for_mobile` | 모바일·클라우드 동기화 제외 대상 필터링 및 Bases 뷰 타깃 자동 복구 거버너 |
| `19` | `,.code-harness.model-lifecycle.py` | `patch_truststore_ssl`, `resolve_hf_local_snapshot`, `avail_commit_gb` | 로컬 AI 모델 메모리 가용량 점검(`avail_commit_gb`), 추론 뮤텍스 락(`heavy_model_infer_lock`) 및 SSL 패치 관리 |
| `20` | `,.code-harness.outcome-verifier.py` | `ExecutionContract`, `VerificationOutcome`, `EscalationReceipt` | 에이전트 작업 실행 계약(`ExecutionContract`) 및 산출물 실존·무결성 기계 검증기 |
| `21` | `,.code-harness.outlink-healer.py` | `now_iso_str`, `log_line`, `load_embedding_cache` | 끊어진 `ids_outlink` 위키링크를 임베딩·레지스트리 기반으로 자동 복구하는 자가 치유기 |
| `22` | `,.code-harness.path-healer.py` | `now_iso`, `explain_resolution`, `resolve_any_path` | 폴더 깊이 오판(`.parents[N]`) 및 이동된 경로를 상시 스캔·복구하는 토폴로지 치유 엔진 |
| `23` | `,.code-harness.plugin.style.engine.py` | `StyleSettingsManager` | 옵시디언 Style Settings 플러그인 CSS 변수 패턴 기반 자동 동기화 엔진 |
| `24` | `,.code-harness.plugin.variable.py` | — | 옵시디언 플러그인 제어용 공통 설정 상수 모듈 |
| `25` | `,.code-harness.preop-backup.py` | `find_vault_root`, `find_zstd`, `ensure_preop_backup` | 대규모 리네임·마이그레이션 직전 `zstd` 초고속 사전 스냅샷 백업 생성·복원 안전망 |
| `26` | `,.code-harness.py-update-log.py` | `rel`, `sha256_file`, `iter_py_files` | 볼트 내 모든 `.py` 코드의 SHA256 변경 이력을 `919.logs`에 자동 기록하는 코드 감사기 |
| `27` | `,.code-harness.raw-guard.py` | `find_vault_root`, `now_iso`, `today_str` | 표면 폴더(`000`~`400`) 일반 `.md` 생성을 감지해 `941.raw/`로 즉시 이관하고 `,.map.*`·`,.base.*`를 자동 갱신하는 핵심 수호 데몬 |
| `28` | `,.code-harness.recovery-harness.py` | `resolve_vault_root`, `now_iso`, `sha12_bytes` | 비정상 작업 중단·파일 손상 감지 시 체크포인트 및 SHA12 대조 기반 자동 복구 하네스 |
| `29` | `,.code-harness.registry-lock.py` | `RegistryLockTimeout`, `lock_depth`, `registry_lock` | 멀티 에이전트 동시 접근 시 레지스트리 파일 경합을 막는 공유 락(`RegistryLock`) 및 원자적 파일 쓰기(`atomic_write_text`) |
| `30` | `,.code-harness.registry.py` | `load_registry_text`, `save_registry_text`, `update_registry_text` | 레지스트리 마크다운 테이블 로드·저장·인덱싱 단일 책임 모듈 |
| `31` | `,.code-harness.rule-heading-id.py` | `HeadingIssue`, `HeadingRow`, `rel` | `921-700.ruskill` 규칙 문서 헤딩 블록 ID(`^...`) 자동 부여 및 정합성 감사기 |
| `32` | `,.code-harness.safety-governor.py` | `resolve_vault_root`, `now_iso`, `get_opensource_stack_status` | `file_id` 등록 중요 파일에 대한 무단 삭제(`unlink`/`rmtree`) 및 위험 명령을 커널 레벨에서 물리 차단(`R-SAFE-01..14`) |
| `33` | `,.code-harness.safety-governor.shadow.py` | `resolve_vault_root`, `now_iso`, `get_opensource_stack_status` | `safety-governor.py` 손상 대비 이중화 섀도우 미러 모듈 (무중단 방어 연속성 보장) |
| `34` | `,.code-harness.sentinel.py` | `resolve_vault_root`, `clamp_escaped_vault_path`, `install_runtime_hooks` | 실수·질책 발생 시 `948.mistake` + `921.skill` 이중 기록 누락을 적발해 `COMPLIANCE_BLOCKED` 처벌을 집행하는 감시자 |
| `35` | `,.code-harness.setting.accent-color.py` | `AccentColorManager` | 볼트 상태·모드별 옵시디언 강조색(Accent Color) 코어 제어 매니저 |
| `36` | `,.code-harness.setting.graph.engine.py` | `is_knowledge_surface_dir`, `contains_knowledge_surface`, `compute_dynamic_maintenance_exclusions` | 지식 표면 폴더와 유지보수(`900.maintenance`) 폴더를 구분해 그래프 뷰 제외 필터를 동적 계산하는 엔진 |
| `37` | `,.code-harness.skill-resolver.py` | `resolve_skills_for_task`, `get_preloaded_context` | 작업 질의에 맞는 `921.skill` 문서를 시스템 차원에서 자동 탐색·선주입하는 리졸버 |
| `38` | `,.code-harness.skill-standardizer.py` | `sha12_text`, `OnnxCrossEncoderReranker`, `SkillAIStandardizerEngine` | 고밀도 임베딩 + ONNX 리랭커 기반 스킬 문서 구조·메타데이터 표준화 엔진 |
| `39` | `,.code-harness.smoke-verifier.py` | `capture_bg_window`, `ghost_cursor_act`, `run_full_smoke` | 포커스 스틸 없는 백그라운드 창 캡처 및 UI 스모크 검증기 |
| `40` | `,.code-harness.stack.py` | `resolve_vault_root`, `load_fault_tolerance`, `get_breaker` | 서킷 브레이커(`CircuitBreaker`) 및 코드 하네스 공통 스택 초기화 로더 |
| `41` | `,.code-harness.test-gate.py` | `GateIssue`, `CommandResult`, `rel` | `911.test` → `912-001.py` → `cskill` 3단계 코드 승격 게이트 핵심 검증 로직 |
| `42` | `,.code-harness.variable.py` | `calculate_next_heading`, `load_file_content` | `VAULT_ROOT` 앵커 탐색 및 `915.code-harness` 전역 경로·상수 단일 진실 공급원(SSOT) |
| `43` | `,.code-harness.virtual-fs.py` | `heal_path`, `VaultMetaPathFinder`, `install` | `VaultMetaPathFinder` 파이썬 임포트 훅 및 잘못된 상위 경로 접근을 런타임에 안전 리다이렉트하는 가상 파일시스템 |
| `44` | `,.code-harness.yaml-frontmatter-layout.py` | `LayoutIssue`, `rel`, `clean_frontmatter_boundaries` | 전 도메인 마크다운 YAML 프론트매터 키 순서·경계선 정규화 린터 |
| `45` | `,.code-harness.yaml-rule-drift.py` | `DriftIssue`, `rel`, `issue` | `921-700.ruskill` 규칙과 실제 노트 YAML 속성 간 드리프트(괴리) 전수 탐지기 |
| `46` | `,.code-harness.yaml.py` | `list_frames`, `load_frame`, `now_iso` | `gen_vault_note_from_title()` — PMEST v2 파일명 + 표준 YAML + `941.raw` 라우팅 + `file_id` 원클릭 생성 코어 엔진 |

---

## 5. `.obsidian/` Recommended Plugin Architecture & Core Engineering Rules

### 5.1. 인하우스 커스텀 가드·위키 플러그인 패턴 (8종)

| 순번 | 플러그인 패턴 ID | 핵심 엔지니어링 역할 |
|:---:|:---|:---|
| `01` | `llm-wiki-chat` | 옵시디언 사이드패널에서 `:18793` Read-Only 로컬 위키 데몬과 통신하여 근거 각주(`[1]`, `[2]`) 달린 답변 제공 |
| `02` | `maintenance-filter` | `900.maintenance`·`941.raw` 같은 시스템 폴더를 검색 / Quick Switcher / 링크 제안 / 그래프 뷰 / 백링크 영역별로 독립 토글해 숨김 |
| `03` | `empty-save-guard` | 에디터 경쟁 상태(race condition)로 인해 내용이 있는 파일이 `0B` 빈 파일로 덮어쓰이는 사고를 원천 차단 |
| `04` | `safe-startup-mediator` | 대형 볼트 기동·인덱싱·아웃링크 패인·대형 마크다운 표 렌더링 지연을 플러그인 비활성화 없이 최적화 |
| `05` | `folder-note-opener` | 폴더 클릭 시 `{folder}/{folder}.md` 폴더 노트를 충돌 없이 메인 에디터로 열기 |
| `06` | `status-bar-collapse` | 상태표시줄 항목이 많아 본문을 침범할 때 좌측 토글로 접었다 펼치는 레이아웃 가드 |
| `07` | `vault-spaced-repetition` | FSRS 스케줄러 기반 볼트 내장 복습 로그 및 통계 제공 |
| `08` | `pdf-annotation-merger` | 원본 PDF와 사이드카 주석(`.annot.json`)을 단일 주석 PDF로 무손실 병합 |

### 5.2. 보편 커뮤니티·코어 추천 플러그인 (16종)

| 분류 | 추천 플러그인 ID | 볼트 아키텍처 내 활용 목적 |
|:---|:---|:---|
| **구조·데이터베이스** | `folder-notes`, 코어 `Bases`, `dataview`, `tag-wrangler` | 표면 폴더 `{folder}.md` 안내판 + `941.raw` 노트의 실시간 표/카드 데이터베이스 뷰 |
| **작업·일정 관리** | `obsidian-tasks-plugin`, `obsidian-kanban`, `calendar-bases`, `chronos` | `200.plan` 작업·마감·타임라인·칸반 보드 시각화 |
| **시각화·다이어그램** | `obsidian-excalidraw-plugin`, `drawio-obsidian`, `obsidian-graphviz`, `markdown-mindmap` | 아키텍처 스케치, SVG 다이어그램, 마인드맵 및 개념도 작성 |
| **편집·탐색·학습** | `floating-toc`, `obsidian-footnotes`, `obsidian-regex-replace`, `obsidian-spaced-repetition` | 장문 탐색, 각주 관리, 정규식 일괄 치환, 망각곡선 복습 |

### 5.3. `.obsidian/` 코어 설정 운영 3대 철칙
1. **표면 뷰(`{folder}.md` + `,.map.*` + `,.base.*`)와 본문(`941.raw`) 물리 분리**: 파일 탐색기 트리가 대량 노트로 마비되지 않도록 표면 폴더는 안내판과 데이터베이스 뷰만 렌더링.
2. **Electron `Partitions` 보존 원칙**: 캐시 정리 시 `IndexedDB` 및 순수 Chromium 캐시만 정리하고, 플러그인 활성화 토큰이 담긴 `Partitions` 폴더는 절대 이동·삭제하지 않음으로써 플러그인 제한 모드(Restricted Mode) 초기화 사고 원천 방지.
3. **Bases 카드 썸네일 즉시성 보장**: 외부 스크립트가 대량 분류한 직후에도 빈 뷰가 뜨지 않도록 `file.inFolder(...)` 조건과 프론트매터 필터를 결합하고 `cover: "[[파일명.ext]]"` 위키링크 바인딩을 표준화.

---

## 6. Isolated Browser, Remote Queue, 3-Way Sync & Off-Vault Encrypted Backup

| 영역 | 권장 구성 패턴 | 핵심 아키텍처 및 안전 수칙 |
|:---|:---|:---|
| **에이전트 전용 격리 브라우저** | Dedicated MCP/CDP Browser (`:9210` / `:9110`) | • 사용자의 일상 브라우저와 프로필·프로세스를 100% 분리(`R-SAFE-14`)<br/>• 모든 자동화는 백그라운드 탭에서만 수행하여 사용자 창 포커스 탈취 `0건` 보장 |
| **브라우저 북마크 동기화** | `bookmark-obsidian-sync.py` + `Search-First` 사이드패널 | • 북마크 벡터 검색 + 백그라운드 본문 수집 + 실시간 웹 검색 병렬 결합<br/>• 브라우저 북마크 폴더 ↔ 옵시디언 폴더 1:1 양방향 동기화 |
| **비동기 원격 작업 큐** | `931.session/.../.remote/pending` + `remote-bridge.py` | • 모바일/외부 기기에서 파일 드롭 또는 웹훅으로 작업 지시를 투입하면 로컬 멀티 CLI가 실행 후 `800.outbox/`로 결과 배출 |
| **3중 실시간 동기화** | **Git** + **P2P/Cloud Sync Mirror** + **Pre-Op Snapshot** | • **Git**: 싱글톤 락 데몬(`sync.git.py`)을 통한 버전 관리 및 롤백<br/>• **외부/모바일 미러**: `_inbox/` 실시간 미러링 (**절대 Move/삭제 금지, Copy-Only 불변 원칙**)<br/>• **사전 스냅샷**: 대규모 마이그레이션 직전 `preop-backup.py`(`zstd`) 자동 백업 |
| **외부 물리 분리 보안 금고** | `<EXTERNAL_PRIVATE_VAULT>` + `AES-256` + `OS ACL` | • 민감 금융·복구키·인증서는 메인 볼트 바깥 디렉터리로 물리 분리하여 Git·AI 훅 노출 원천 차단<br/>• 파일명 헤더 암호화(`7-Zip -mhe=on` 또는 `Kopia` 암호화 스냅샷) + OS ACL 접근 잠금 이중화 |
