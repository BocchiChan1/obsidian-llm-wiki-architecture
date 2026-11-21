# 03. 보편 Python 자동화 도메인 아키텍처 · `VaultCli` 17종 치트시트 · 7-Layer 코드 승격 게이트

> 본 문서는 클라우드 토큰을 85~95% 절감하는 **`VaultCli` 17종 단축 도구**, 불량 코드의 본진 유입을 원천 차단하는 **`911.test` 7-Layer 오픈소스 정적·동적 검수 게이트(`tcheck`)**, 그리고 `900.maintenance/910.codey/912.vein/912-001.py/`의 **보편 24대 자동화 도메인 아키텍처** 명세서입니다.

---

## 1. `VaultCli` 17종 토큰 절약 & 로컬 AI 단축 명령어 완전 레퍼런스

`VaultCli` 래퍼 및 `912-001.py/,.py.cli.token-tools.py`로 즉시 실행되는 17종 표준 도구입니다.

| 카테고리 | 단축 명령 | 내부 연결 엔진 | 핵심 동작 및 토큰 절감 효과 |
|:---|:---|:---|:---|
| **로컬 AI 검색·결심 (6종)** | `twiki "<질의>" [--top 6]` | 로컬 SLM + `942.wiki` | 클라우드 토큰 `0원`으로 온톨로지 위키 허브 + 1-hop 아웃링크 요약 브리핑 |
| 로컬 AI 검색·결심 | `tgrep "<질의\|심볼>" [--top 6]` | 로컬 SLM + Python AST + `LanceDB` | 무차별 `grep` 대신 정확한 함수·섹션 줄 번호(`Lstart-Lend`)와 `tslice` 명령 반환 |
| 로컬 AI 검색·결심 | `hsearch "<질의>" [--table auto]` | 고밀도 임베딩 + `LanceDB` + 리랭커 | 위키·스킬·문서 노드 하이브리드 시맨틱 검색 |
| 로컬 AI 검색·결심 | `hsolve "<질의>" [--execute]` | 임베딩 + `OpenDecider` (`P>=0.85`) | 신뢰도 85% 이상 달성 시 LLM 개입 없이 매칭된 `.py` 스크립트 즉시 자동 실행 |
| 로컬 AI 검색·결심 | `hskill "<질의>" [--top 5]` | `skills_compact.lance` | 현재 작업에 필요한 `921.skill` 매뉴얼과 `code_id` 연결 스크립트 선별 |
| 로컬 AI 검색·결심 | `hroute "<질의>" [--fast]` | `OpenDecider` + `922.baking` | 작업 성격에 맞는 상위 베이킹 프롬프트 및 실행 모드(`sharp`/`split`) 라우팅 |
| **마이크로 자동화 (4종)** | `houtlink <파일> [--apply]` | `outlink.by-embedding.py` | YAML `ids_outlink`, `rule_id`, `skill_id`, `code_id` 임베딩 기반 자동 채움 |
| 마이크로 자동화 | `hspec <파일>` | `skill-standardizer.py` | 스킬/규칙 문서 상단 `skill_spec` / `rule_spec` 머신 리더블 블록 자동 생성 |
| 마이크로 자동화 | `hclassify <제목> [--body "..."]` | `harrier-decider.py` | 신규 노트의 PMEST `personality`, `tags`, 목적지 폴더, 아웃링크 자동 판별 |
| 마이크로 자동화 | `hcode <파일>` | `skill-resolver.py` | 스킬·규칙 문서에 대응하는 `912-001.py` 실행 스크립트 자동 매칭 |
| **구조 슬라이스·검수 (7종)** | `tskeleton <파일> [--max-lines 80]` | `token-tools.py` AST/MD 파서 | 파일 전체 로드 없이 YAML 요약 + 헤딩 트리 / Python 클래스·함수 줄 번호만 추출 (**90% 토큰 절감**) |
| 구조 슬라이스·검수 | `tslice <파일> <섹션\|시작:끝>` | `token-tools.py` 정밀 슬라이서 | 필요한 헤딩 섹션이나 함수·줄 범위만 핀셋 추출 |
| 구조 슬라이스·검수 | `tid <file_id\|stem>` | `file-tracker.py` | 10자리 불변 `file_id` ↔ 실제 디스크 파일 경로 O(1) 즉시 변환 |
| 구조 슬라이스·검수 | `tlinks <파일>` | `lookup-paths.py` | 문서의 `ids_outlink` 목록이 가리키는 실제 파일 경로 일괄 해석 |
| 구조 슬라이스·검수 | `tsha <파일...>` | `token-tools.py` 해시 검증기 | SHA12/SHA256 + `UTF-8 no BOM` + `LF` 개행 + 줄 수 1줄 즉시 증명 |
| 구조 슬라이스·검수 | `tdiff [--stat]` | 초경량 Git 상태 요약기 | 토큰 폭탄 없는 변경 파일·라인 통계 요약 |
| 구조 슬라이스·검수 | `tcheck <파일.py> [--autofix] [--promote]` | `test.protocol-gate.py` | **7-Layer 오픈소스 정적·동적 분석 + 실패 예방 규칙 검수 및 본진 승격 게이트** |

---

## 2. `911.test` 7-Layer 오픈소스 코드 검수 & 3단계 승격 프로토콜 (`tcheck`)

새로 작성하거나 수정한 모든 파이썬 스크립트는 곧바로 `912-001.py`에 넣을 수 없으며, 반드시 **`911.test/` 스테이징 → `tcheck` 7-Layer 통과 → `912-001.py` 승격 및 `921-000.cskill` 자동 연결**의 3단계 게이트를 거쳐야 합니다.

### 2.1. 7-Layer 검수 파이프라인 구성
1. **Layer 1 (`AST & Encoding Gate`)**: `UTF-8 (no BOM)` + `LF` 개행 + Python 문법 파싱 무결성 검증.
2. **Layer 2 (`Ruff`)**: 고속 린트(`F`, `E9`, `B`, `PLE`, `RUF` 등) 및 코드 결함 패턴 검증.
3. **Layer 3 (`Pyflakes`)**: 미정의 심볼·미사용 임포트 교차 정적 검증.
4. **Layer 4 (`Mypy`)**: 정적 타입 어노테이션 및 `None` 역참조 결함 탐지.
5. **Layer 5 (`Bandit`)**: 보안 취약점, 위험 셸 인젝션 및 하드코딩 시크릿 정적 스캔.
6. **Layer 6 (`Vulture` + `R-PY-01..10` Vault Guard Rules)**: 데드코드 탐지 및 10대 운영 안전 규칙(`CREATE_NO_WINDOW` 누락 차단, `.parents[N]` 하드코딩 금지, `encoding="utf-8"` 강제, `_inbox` Move 금지, `file_id` 삭제 방지 등) 강제 검사.
7. **Layer 7 (`Pytest` Execution Gate)**: 단위·스모크 테스트 실제 통과 확인 후 `,.test.report.*.json` 증빙 발급.

### 2.2. 표준 `.ruff.toml` 설정 (`910.codey/911.test/.ruff.toml`)

```toml
# 911.test Open-Source Automated Error Detection Config (Ruff)
target-version = "py312"
line-length = 140

[lint]
# F = Pyflakes (undefined names, unused/missing imports, syntax errors)
# E9 = AST/Syntax errors
# B = flake8-bugbear (mutable defaults, loop variable capture, bug patterns)
# PLE = Pylint Errors (bad argument counts, nonlocal/global errors)
# RUF = Ruff-specific bug detection rules
select = ["F", "E9", "B", "PLE", "RUF012", "RUF013", "RUF015"]
ignore = ["F401", "B007", "B904", "B905"]
```

---

## 3. `912-001.py/` 보편 24대 자동화 도메인 아키텍처 카탈로그

모든 스크립트는 **` ,.py.<domain>.<action>.py `** 명명 규약을 따릅니다. 아래는 어떤 볼트 환경에서도 그대로 재사용할 수 있는 24개 핵심 도메인 계층입니다.

| 순번 | 도메인 키 (`<domain>`) | 대표 스크립트 네이밍 패턴 (`912-001.py/`) | 보편 자동화 역할 |
|:---:|:---|:---|:---|
| `01` | **`llm-wiki`** | `,.py.llm-wiki.engine.py` | `941.raw` 스캔 및 `942.wiki` 온톨로지·리파이너리 허브 자동 컴파일 |
| `02` | **`embedding`** | `,.py.embedding.auto-sync.py`, `,.py.embedding.lancedb-sync.py`, `,.py.embedding.memory.py` | 증분 벡터 임베딩 갱신, `LanceDB` 동기화, 768d NPZ 실수 원장 검색 |
| `03` | **`rag`** | `,.py.rag.chunker.py`, `,.py.rag.cli.py`, `,.py.rag.actions.py` | 마크다운 헤딩 기반 의미 청킹 및 하이브리드 RAG 검색 액션 |
| `04` | **`outlink`** | `,.py.outlink.by-embedding.py`, `,.py.outlink.common.py`, `,.py.outlink.code-wikilink.purge.py` | 임베딩 기반 `ids_outlink` 자동 추천 및 코드 블록 오탐 위키링크 정리 |
| `05` | **`registry`** | `,.py.registry.register-path.py`, `,.py.registry.fast-rebuild.py`, `,.py.registry.ghost-outlink-healer.py` | 10자리 불변 `file_id` 발급, 인덱스 재빌드, 끊어진 아웃링크 자동 치유 |
| `06` | **`yaml`** | `,.py.yaml.mistake.py`, `,.py.yaml.bulk.update.py`, `,.py.yaml.required-check.py` | YAML 프론트매터 일괄 검증·갱신 및 `948.mistake` 표준 기록 |
| `07` | **`rename`** | `,.py.rename.folder.py`, `,.py.rename.vault.py`, `,.py.rename.pmest-v2-slug-clean.py` | 폴더·파일 원자적 리네임, 전역 위키링크 치환, PMEST v2 슬러그 정규화 |
| `08` | **`folder`** | `,.py.folder.init.py`, `,.py.folder.discover.py`, `,.py.folder.rename.sync.py` | 폴더 노트(`{folder}.md`) 초기화, 폴더 트리 탐색, 표면-Raw 동기화 |
| `09` | **`audit`** | `,.py.audit.ruskill-and-py-tools.py`, `,.py.audit.ids-outlink-map.py`, `,.py.audit.body-structure.py` | 볼트 규격 준수 감사, 아웃링크 무결성 검증, 문서 구조 린트 |
| `10` | **`rule`** | `,.py.rule.fix.encoding-bulk.py`, `,.py.rule.fix.clean-yaml-v2.py` | `UTF-8 no BOM + LF` 일괄 교정 및 `921-700.ruskill` 규칙 위반 자동 수정 |
| `11` | **`test`** | `,.py.test.protocol-gate.py`, `,.py.test.smoke-runner.py` | `911.test` 7-Layer 정적·동적 코드 승격 게이트(`tcheck`) 및 스모크 테스트 |
| `12` | **`skill`** | `,.py.skill.ai-standardizer.py`, `,.py.skill.code-freshness.py`, `,.py.skill.body-links.fill.py` | 6종 스킬 메타데이터 표준화, 연결 `.py` 신선도 검증, 본문 참조 링크 자동 연결 |
| `13` | **`baking`** | `,.py.baking.decider.py`, `,.py.baking.create.py` | `OpenDecider` 기반 작업 목적별 진입 프롬프트(`922.baking`) 자동 라우팅 |
| `14` | **`memory`** | `,.py.memory.harness.py`, `,.py.memory.checkpoint.py`, `,.py.memory.create.py` | `948.mistake` + `921.skill` 이중 자동 캡처(`auto-capture`) 및 체크포인트 롤백 |
| `15` | **`sync`** | `,.py.sync.cli.py`, `,.py.sync.git.py`, `,.py.sync.embedding.py` | `921.skill` → 멀티 CLI 미러 동기화, 싱글톤 Git 자동 동기화, 임베딩 동기화 |
| `16` | **`backup`** | `,.py.backup.snapshot.py`, `,.py.backup.archive-tar.py` | 증분 중복제거 스냅샷 백업 및 엔진·볼트 무손실 아카이빙 |
| `17` | **`graph`** | `,.py.graph.color-and-map.py`, `,.py.graph.semantic-note-links.py` | `951.map` 자동 아웃링크 지도 생성 및 도메인별 그래프 색상 매핑 |
| `18` | **`base`** | `,.py.base.generate.py` | 각 표면 폴더의 옵시디언 Bases(`.base`) 데이터베이스 뷰 자동 생성 |
| `19` | **`md`** | `,.py.md.heading-restructure.py`, `,.py.md.restore-from-git.py` | 마크다운 헤딩 계층 정규화 및 Git 기반 안전 복원 |
| `20` | **`pdf`** | `,.py.pdf.convert.to-md.py`, `,.py.pdf.merge-annotations.py` | PDF 텍스트 추출 및 사이드카 필기 주석 병합 |
| `21` | **`cli`** | `,.py.cli.token-tools.py`, `,.py.cli.install-vaultcli-shims.py` | 17종 토큰 절감 CLI 엔진(`tskeleton`/`tslice`/`tsha`) 및 전역 명령 래퍼 설치 |
| `22` | **`agent`** | `,.py.agent.handoff-trigger.py`, `,.py.agent.workspace-sync.py`, `,.py.agent.logger.py` | 멀티 CLI 핸드오프 감시, `932.tasks` 자동 로깅, 세션 감사 기록 |
| `23` | **`hook`** | `,.py.intent.constraint-gate.py`, `,.py.hook.tasks-sync.py` | 사용자 제약 위반 차단(`PreToolUse` 훅) 및 작업 원장 자동 동기화 |
| `24` | **`observability` / `system`** | `,.py.observability.summary.py`, `,.py.maintenance.autonomous-loop.py`, `,.py.vault.agents-map.py`, `,.py.outbox.map-sync.py` | 에이전트 원장 집계, 자율 표면 무결성 감시 루프, `AGENTS.md` 계약 검증, `800.outbox` 출고 동기화 |
