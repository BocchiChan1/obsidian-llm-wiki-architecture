# 03. `532`개 Python 자동화 도구 카탈로그 · `VaultCli` 18종 치트시트 · 7-Layer 코드 승격 게이트

> 본 문서는 `900.maintenance/910.codey/912.vein/912-001.py/`에 수록된 **`532`개 정식 Python 스크립트(`100`개 도메인 전수 집계표)**, 클라우드 토큰을 85~95% 절감하는 **`VaultCli` 18종 단축 도구**, 그리고 불량 코드의 본진 유입을 원천 차단하는 **`911.test` 7-Layer 오픈소스 정적·동적 검수 게이트(`tcheck`)** 명세서입니다.

---

## 1. `VaultCli` 18종 토큰 절약 & 로컬 AI 단축 명령어 완전 레퍼런스

`%LOCALAPPDATA%\VaultCli\bin` 래퍼 및 `912-001.py/,.py.cli.token-tools.py`로 즉시 실행되는 18종 표준 도구입니다.

| 카테고리 | 단축 명령 | 내부 연결 엔진 | 핵심 동작 및 토큰 절감 효과 |
|:---|:---|:---|:---|
| **로컬 AI 검색·결심 (6종)** | `twiki "<질의>" [--top 6]` | `스페짱` 3-Model CoT + `942.wiki` | 클라우드 토큰 `0원`으로 43개 위키 허브 + 1-hop 아웃링크 요약 브리핑 |
| 로컬 AI 검색·결심 | `tgrep "<질의\|심볼>" [--top 6]` | 로컬 SLM + Python AST + `LanceDB` | 무차별 `grep` 대신 정확한 함수·섹션 줄 번호(`Lstart-Lend`)와 `tslice` 명령 반환 |
| 로컬 AI 검색·결심 | `hsearch "<질의>" [--table auto]` | `Harrier-0.6B` + `LanceDB` + `Jina` | `18,200+` 위키·스킬·문서 노드 하이브리드 시맨틱 검색 |
| 로컬 AI 검색·결심 | `hsolve "<질의>" [--execute]` | `Harrier` + `OpenDecider` (`P>=0.85`) | 신뢰도 85% 이상 달성 시 LLM 개입 없이 매칭된 `.py` 스크립트 즉시 자동 실행 |
| 로컬 AI 검색·결심 | `hskill "<질의>" [--top 5]` | `harrier_skills_compact.lance` | 현재 작업에 필요한 `921.skill` 매뉴얼과 `code_id` 연결 스크립트 족집게 선별 |
| 로컬 AI 검색·결심 | `hroute "<질의>" [--fast]` | `OpenDecider` + `922.baking` | 작업 성격에 맞는 상위 베이킹 프롬프트 및 실행 모드(`sharp`/`split`) 라우팅 |
| **마이크로 자동화 (5종)** | `houtlink <파일> [--apply]` | `outlink.by-embedding.py` | YAML `ids_outlink`, `rule_id`, `skill_id`, `code_id` 임베딩 기반 자동 채움 |
| 마이크로 자동화 | `hspec <파일>` | `skill-standardizer.py` | 스킬/규칙 문서 상단 `skill_spec` / `rule_spec` 머신 리더블 블록 자동 생성 |
| 마이크로 자동화 | `hclassify <제목> [--body "..."]` | `harrier-decider.py` | 신규 노트의 PMEST `personality`, `tags`, 목적지 폴더, 아웃링크 자동 판별 |
| 마이크로 자동화 | `hcode <파일>` | `skill-resolver.py` | 스킬·규칙 문서에 대응하는 `912-001.py` 실행 스크립트 자동 매칭 |
| 마이크로 자동화 | `husched [sync\|scan]` | 대학·일정 통합 파이프라인 | 음성 STT(NotebookLM) + 슬라이드 + 공지 교차검증 후 타임라인 자동 반영 |
| **구조 슬라이스·검수 (7종)** | `tskeleton <파일> [--max-lines 80]` | `token-tools.py` AST/MD 파서 | 파일 전체 로드 없이 YAML 요약 + 헤딩 트리 / Python 클래스·함수 줄 번호만 추출 (**90% 토큰 절감**) |
| 구조 슬라이스·검수 | `tslice <파일> <섹션\|시작:끝>` | `token-tools.py` 정밀 슬라이서 | 필요한 헤딩 섹션이나 함수·줄 범위만 핀셋 추출 |
| 구조 슬라이스·검수 | `tid <file_id\|stem>` | `file-tracker.py` | 10자리 불변 `file_id` ↔ 실제 디스크 파일 경로 O(1) 즉시 변환 |
| 구조 슬라이스·검수 | `tlinks <파일>` | `lookup-paths.py` | 문서의 `ids_outlink` 목록이 가리키는 실제 파일 경로 일괄 해석 |
| 구조 슬라이스·검수 | `tsha <파일...>` | `token-tools.py` 해시 검증기 | SHA12/SHA256 + `UTF-8 no BOM` + `LF` 개행 + 줄 수 1줄 즉시 증명 |
| 구조 슬라이스·검수 | `tdiff [--stat]` | 초경량 Git 상태 요약기 | 토큰 폭탄 없는 변경 파일·라인 통계 요약 |
| 구조 슬라이스·검수 | `tcheck <파일.py> [--autofix] [--promote]` | `test.protocol-gate.py` | **7-Layer 오픈소스 정적·동적 분석 + 역대 실패규칙 검수 및 본진 승격 게이트** |

---

## 2. `911.test` 7-Layer 오픈소스 코드 검수 & 3단계 승격 프로토콜 (`tcheck`)

새로 작성하거나 수정한 모든 파이썬 스크립트는 곧바로 `912-001.py`에 넣을 수 없으며, 반드시 **`911.test/` 스테이징 → `tcheck` 7-Layer 통과 → `912-001.py` 승격 및 `921-000.cskill` 자동 연결**의 3단계 게이트를 거쳐야 합니다.

### 2.1. 7-Layer 검수 파이프라인 구성
1. **Layer 1 (`AST & Encoding Gate`)**: `UTF-8 (no BOM)` + `LF` 개행 + Python 문법 파싱 무결성 검증.
2. **Layer 2 (`Ruff`)**: 고속 린트(`E`, `F`, `W`, `B`, `UP`, `SIM`, `RUF` 등) 및 포맷 규격 검증.
3. **Layer 3 (`Pyflakes`)**: 미정의 심볼·미사용 임포트 교차 정적 검증.
4. **Layer 4 (`Mypy`)**: 정적 타입 어노테이션 및 `None` 역참조 결함 탐지.
5. **Layer 5 (`Bandit`)**: 보안 취약점, 위험 셸 인젝션 및 하드코딩 시크릿 정적 스캔.
6. **Layer 6 (`Vulture` + `R-PY-01..10` Vault Historical Rules)**: 데드코드 탐지 및 역대 554건 볼트 파이썬 장애에서 추출한 10대 재발 방지 규칙(`CREATE_NO_WINDOW` 누락, `.parents[4]` 오판, `UTF-8` 인코딩 누락, `_inbox` Move 금지 등) 강제 검사.
7. **Layer 7 (`Pytest` Execution Gate)**: 단위·스모크 테스트 실제 통과 확인 후 `,.test.report.*.json` 증빙 발급.

### 2.2. 표준 `.ruff.toml` 설정 (`910.codey/911.test/.ruff.toml`)

```toml
# 933.test Open-Source Automated Error Detection Config (Ruff)
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

## 3. `912-001.py/` 정식 스크립트 `100`개 도메인 전수 집계 카탈로그 (`532`개 스크립트)

파일명 규칙 `,.py.<도메인>.<동작>.py`에 따라 분류된 전체 `100`개 도메인 실측 현황표입니다.

| 순번 | 도메인 키 (`<domain>`) | 스크립트 수 | 대표 스크립트 예시 (`912-001.py/`) | 대표 기능 요약 |
|:---:|:---|---:|:---|:---|
| `001` | **`university`** | `41` | `,.py.university.3d-proto-lighthouse-deck.py`, `,.py.university.3d-proto-remote-deck.py`, `,.py.university.414-week-note.py` | file_id: "py741e8c0b" |
| `002` | **`herdr`** | `33` | `,.py.herdr.activate-session-cli.py`, `,.py.herdr.agent-state-hook.py`, `,.py.herdr.auto-compact.py` | file_id: "pyc13bbe2b" |
| `003` | **`test`** | `31` | `,.py.test.composer-lock.py`, `,.py.test.cursor-and-sidebar.py`, `,.py.test.discord-pane-map-ws.py` | composer_ready / chrome_broken fixtures from 2026-11-09 herdr CLI input-lock. |
| `004` | **`yaml`** | `27` | `,.py.yaml.add.description.py`, `,.py.yaml.apology.py`, `,.py.yaml.bulk.update.py` | file_id: "py5a32b663" |
| `005` | **`tool`** | `25` | `,.py.tool.911.pattern.extract.py`, `,.py.tool.agent-desktop-stream.py`, `,.py.tool.agent-desktop.py` | file_id: "py4d43c97e" |
| `006` | **`plugin`** | `20` | `,.py.plugin.anki-sync-suite.py`, `,.py.plugin.dataview.export.py`, `,.py.plugin.graph.hybrid-detector.py` | file_id: "py0f0deee9" |
| `007` | **`market`** | `18` | `,.py.market.alert-watch.py`, `,.py.market.arena-snaplog.py`, `,.py.market.cycle-mine.py` | file_id: "pyb4ec37ef" |
| `008` | **`crawl`** | `17` | `,.py.crawl.chatbot.py`, `,.py.crawl.grok-sortbase-login.py`, `,.py.crawl.grok-sortbase.py` | file_id: "py33cad5f8" |
| `009` | **`skill`** | `15` | `,.py.skill.ai-standardizer.py`, `,.py.skill.body-links.fill.py`, `,.py.skill.code-freshness.py` | ,.py.skill.ai-standardizer.py — CLI Orchestrator for AI Reranker + Harrier Embedding + Lan |
| `010` | **`prod-svc`** | `13` | `,.py.prod-svc.cumulative-master-presentation.py`, `,.py.prod-svc.final-exam-prep-presentation.py`, `,.py.prod-svc.hw1-build-presentation.py` | file_id: "py8ec3f308" |
| `011` | **`rag`** | `12` | `,.py.rag.actions.py`, `,.py.rag.chunker.py`, `,.py.rag.cli.py` | file_id: "py343d7f47" |
| `012` | **`rule`** | `12` | `,.py.rule.fix.clean-yaml-v2.py`, `,.py.rule.fix.encoding-bulk.py`, `,.py.rule.fix.encoding-simple.py` | 설정: Vault 루트 |
| `013` | **`game`** | `11` | `,.py.game.deadlock.audio-guard.py`, `,.py.game.deadlock.chrome-throttle.py`, `,.py.game.deadlock.launch-modded.py` | file_id: "py1c1e80a6" |
| `014` | **`sync`** | `11` | `,.py.sync.cli.py`, `,.py.sync.embedding.py`, `,.py.sync.gdrive-latency-retest.py` | file_id: "pyf9fe55a6" |
| `015` | **`misc`** | `10` | `,.py.conflict-marker-purge.py`, `,.py.dbfolder-to-bases.py`, `,.py.dead-link-purge.py` | vault 전수에서 git merge conflict marker 제거 (HEAD 쪽 채택). |
| `016` | **`image`** | `9` | `,.py.image.benchmark-kagami.py`, `,.py.image.cards-sync.py`, `,.py.image.classify-and-sort.py` | file_id: "pydc3e3b33" |
| `017` | **`memory`** | `9` | `,.py.memory.checkpoint.py`, `,.py.memory.create.py`, `,.py.memory.harness.py` | ,.py.memory.checkpoint.py — sleep-time memory snapshots (948.mistake rollback). |
| `018` | **`mkdn`** | `9` | `,.py.mkdn.clean.outlink.py`, `,.py.mkdn.edit.py`, `,.py.mkdn.edit.sort-list.grok.py` | file_id: "pycdcf2ad3" |
| `019` | **`notebooklm`** | `9` | `,.py.notebooklm.artifact-extract.py`, `,.py.notebooklm.auth-headless.py`, `,.py.notebooklm.auth-hydrate.py` | file_id: "py3825baa9" |
| `020` | **`outlink`** | `9` | `,.py.outlink.by-embedding.py`, `,.py.outlink.code-wikilink.purge.py`, `,.py.outlink.common.py` | file_id: "pya9bb3e73" |
| `021` | **`browser`** | `8` | `,.py.browser.bookmark-obsidian-sync.py`, `,.py.browser.brave-logon-launch.py`, `,.py.browser.brave-tabgroup-open.py` | 1:1 Obsidian <-> Brave <-> BrowserOS(BrowserClaw) Bookmark Sync, Graph View & Live Site Cr |
| `022` | **`youtube`** | `7` | `,.py.youtube.classifier.py`, `,.py.youtube.decision-model.py`, `,.py.youtube.inbox-live.py` | file_id: "bfemtnejbp" |
| `023` | **`baking`** | `6` | `,.py.baking.create.py`, `,.py.baking.decider.py`, `,.py.baking.folder-samples.py` | file_id: "py739f5c7c" |
| `024` | **`embedding`** | `6` | `,.py.embedding.auto-sync.py`, `,.py.embedding.download-harrier.py`, `,.py.embedding.harrier-lancedb.py` | file_id: "33a10549b5" |
| `025` | **`folder`** | `6` | `,.py.folder.discover.py`, `,.py.folder.id.py`, `,.py.folder.init.py` | file_id: "pyca2d08eb" |
| `026` | **`audit`** | `5` | `,.py.audit.body-structure.py`, `,.py.audit.ids-outlink-map.py`, `,.py.audit.ruskill-and-py-tools.py` | file_id: "py551ccece" |
| `027` | **`cli`** | `5` | `,.py.cli.agy-usage.py`, `,.py.cli.install-token-tools.py`, `,.py.cli.install-vaultcli-shims.py` | Antigravity CLI (agy) token usage & quota reverse-calculation SSOT utility. |
| `028` | **`maintenance`** | `5` | `,.py.maintenance.autonomous-loop.py`, `,.py.maintenance.routine-everytime.py`, `,.py.maintenance.rsi-engine.py` | Audit the vault maintenance entry surfaces in a repeatable loop. |
| `029` | **`mcp`** | `5` | `,.py.mcp.antigravity.py`, `,.py.mcp.grok-research.py`, `,.py.mcp.memory.py` | file_id: "pye8c5909c" |
| `030` | **`registry`** | `5` | `,.py.registry.code-aliases.gemini.py`, `,.py.registry.fast-rebuild.py`, `,.py.registry.ghost-outlink-healer.py` | file_id: "py76416899" |
| `031` | **`app`** | `4` | `,.py.app.brave-startup-tabs.py`, `,.py.app.pcloud-drive.py`, `,.py.app.steam-pcloud-archive.py` | file_id: "9c7cd77c93" |
| `032` | **`obsidian`** | `4` | `,.py.obsidian.asar-and-cache-heal.py`, `,.py.obsidian.history-pattern-evolution.py`, `,.py.obsidian.self-mgmt.py` | file_id: "py7da74ebb" |
| `033` | **`openclaw`** | `4` | `,.py.openclaw.discord-slash-manage.py`, `,.py.openclaw.memory.sync.py`, `,.py.openclaw.pixiv-mcp-server.py` | file_id: "py9ce51c58" |
| `034` | **`rename`** | `4` | `,.py.rename.folder.py`, `,.py.rename.pmest-v2-slug-clean.py`, `,.py.rename.safe.py` | `rename` 도메인 자동화 도구군 |
| `035` | **`rss`** | `4` | `,.py.rss.chzzk-feed.py`, `,.py.rss.google-news-body.py`, `,.py.rss.schedule-feeds-add.py` | CHZZK RSS for the Obsidian RSS Dashboard. |
| `036` | **`snippets`** | `4` | `,.py.snippets.100check.py`, `,.py.snippets.check.py`, `,.py.snippets.copy.py` | 경로 설정 |
| `037` | **`agent`** | `3` | `,.py.agent.handoff-trigger.py`, `,.py.agent.logger.py`, `,.py.agent.workspace-sync.py` | Builder<->Sub handoff trigger (read-only watcher for a 932.tasks handoff file). |
| `038` | **`backup`** | `3` | `,.py.backup.kopia-snapshot.py`, `,.py.backup.obsidian-engine-and-vault-tar.py`, `,.py.backup.zip-pcloud.py` | Obsidian vault → Kopia dedup incremental snapshot (PRIMARY backup) on pCloud Drive (P:). |
| `039` | **`code`** | `3` | `,.py.code.ontology-manager.py`, `,.py.code.safe-edit.py`, `,.py.code.sync.prompts.py` | file_id: "pye02cca17" |
| `040` | **`code-harness`** | `3` | `,.py.code-harness.daily-mesh-audit.py`, `,.py.code-harness.recovery-engine.py`, `,.py.code-harness.strengthen-trigger.py` | file_id: "pydlymsh01" |
| `041` | **`cursor`** | `3` | `,.py.cursor.hidden-exec.py`, `,.py.cursor.no-window-spawn.py`, `,.py.cursor.startup-index.py` | GUI-host relay: pythonw runs a console exe with CREATE_NO_WINDOW. |
| `042` | **`fiction`** | `3` | `,.py.fiction.novel-os.py`, `,.py.fiction.oa-planet-map.py`, `,.py.fiction.rpg-structure.py` | file_id: "py93e613f9" |
| `043` | **`gemini`** | `3` | `,.py.gemini.cascade-watchdog.py`, `,.py.gemini.hook.shell-guard.py`, `,.py.gemini.hook.write-guard.py` | file_id: "py2544fc8c" |
| `044` | **`graph`** | `3` | `,.py.graph.color-and-map.py`, `,.py.graph.semantic-note-links.py`, `,.py.graph.tag-selector.py` | file_id: "py40936635" |
| `045` | **`hook`** | `3` | `,.py.hook.cursor-output-summarize.py`, `,.py.hook.memory-sync.py`, `,.py.hook.tasks-sync.py` | file_id: "pya559e718" |
| `046` | **`log`** | `3` | `,.py.log.folder-activity.py`, `,.py.log.py-update.py`, `,.py.log.session-export.py` | file_id: "py56e288ac" |
| `047` | **`md`** | `3` | `,.py.md.heading-restructure.py`, `,.py.md.remove-section.py`, `,.py.md.restore-from-git.py` | file_id: "py36505347" |
| `048` | **`schedule`** | `3` | `,.py.schedule.checker.py`, `,.py.schedule.variable-router.py`, `,.py.schedule.windows-toast-notify.py` | Set up paths relative to this script's location |
| `049` | **`vault`** | `3` | `,.py.vault.agents-map.py`, `,.py.vault.cli.py`, `,.py.vault.prod.filetree-gen.py` | 4-element AGENTS.md contract. exit 0 = map + wiring + no forbidden OSS. |
| `050` | **`vocal`** | `3` | `,.py.vocal.acoustic-analyzer.py`, `,.py.vocal.daily-analysis.py`, `,.py.vocal.growth-graph.py` | file_id: "vocacoustc" |
| `051` | **`audio`** | `2` | `,.py.audio.discord-call-recorder.py`, `,.py.audio.dolby-underrun.py` | User-controlled stereo meeting/call audio recorder and faster-whisper speaker-separated ST |
| `052` | **`chrome`** | `2` | `,.py.chrome.bookmark.cleanup.py`, `,.py.chrome.bookmark.plan.py` | Chrome bookmarks cleanup: typo rename + duplicate sibling folder merge. |
| `053` | **`data-mining`** | `2` | `,.py.data-mining.build-earthquake-manual-pdf.py`, `,.py.data-mining.build-proposal-v3.py` | file_id: "pydmeqman01" |
| `054` | **`doc`** | `2` | `,.py.doc.pdf-annotation-merge.py`, `,.py.doc.teleocr-parse.py` | file_id: "pyacccd474" |
| `055` | **`fix`** | `2` | `,.py.fix.category-320spaces.py`, `,.py.fix.heading-level.py` | Fix wrong category YAML in 320.spaces (folder-note links -> 121.arts). |
| `056` | **`id`** | `2` | `,.py.id.helper.py`, `,.py.id.lookup-paths.py` | file_id: "pyd37b9c3e" |
| `057` | **`migrate`** | `2` | `,.py.migrate.spme-facets-and-tags.py`, `,.py.migrate.university-tags-category.py` | file_id: "pymigspme1" |
| `058` | **`outlink-manual`** | `2` | `,.py.outlink-manual.921.skill.py`, `,.py.outlink-manual.995.name.py` | 수동 판단 outlink — 921.skill 전 폴더 (cskill/txskill/taskill/pskill/wfskill/maskill). |
| `059` | **`pdf`** | `2` | `,.py.pdf.convert.to-md.py`, `,.py.pdf.merge-annotations.py` | file_id: "pyad2b1290" |
| `060` | **`routine`** | `2` | `,.py.routine.discord-routine-notify.py`, `,.py.routine.stellive-schedule.py` | file_id: "pyrtndscnt01" |
| `061` | **`setting`** | `2` | `,.py.setting.graph.colors.py`, `,.py.setting.theme.accent-color.py` | file_id: "py856b030b" |
| `062` | **`spacelife`** | `2` | `,.py.spacelife.library-return-router.py`, `,.py.spacelife.youtube-schedule-sync.py` | file_id: "py2044a3a0" |
| `063` | **`stem-leaf`** | `2` | `,.py.stem-leaf.lint.bat.py`, `,.py.stem-leaf.lint.py` | file_id: "py0c566bb2" |
| `064` | **`sys`** | `2` | `,.py.sys.apply-pagefile-and-heap.py`, `,.py.sys.window-topmost.py` | ,.py.sys.apply-pagefile-and-heap.py — Configure Windows Desktop Heap and PagingFiles |
| `065` | **`system`** | `2` | `,.py.system.cmd-flash-guard.py`, `,.py.system.printer-spooler.py` | ,.py.system.cmd-flash-guard.py |
| `066` | **`voice`** | `2` | `,.py.voice.m4a-recover.py`, `,.py.voice.song-intake.py` | file_id: "pycf495c1d" |
| `067` | **`ai`** | `1` | `,.py.ai.model-update.py` | ,.py.ai.model-update.py — End-to-End Local AI Model Benchmark, Wiring, Pruning & Lifecycle |
| `068` | **`anki`** | `1` | `,.py.anki.push.py` | file_id: "pyd412d01e" |
| `069` | **`antigravity`** | `1` | `,.py.antigravity.oauth-login.py` | file_id: "pyaa0674b5" |
| `070` | **`base`** | `1` | `,.py.base.generate.py` | file_id: "py04c293d1" |
| `071` | **`catbox`** | `1` | `,.py.catbox.picture.plan.py` | file_id: "py428a8ce9" |
| `072` | **`chzzk`** | `1` | `,.py.chzzk.recommend.py` | file_id: "pyczkrec01" |
| `073` | **`claw`** | `1` | `,.py.claw.mobile-bridge.py` | Disabled Claw mobile bridge for vault CLI BAT sessions. |
| `074` | **`cmd`** | `1` | `,.py.cmd.lag.audit.py` | cmd 렉 원인을 shell, git, 생성 로그, sync 출력량으로 나눠 계측한다. |
| `075` | **`colab`** | `1` | `,.py.colab.sync-notebooks.py` | file_id: "clbsync001" |
| `076` | **`copilot`** | `1` | `,.py.copilot.bridge.py` | file_id: "py90cbbec3" |
| `077` | **`discord`** | `1` | `,.py.discord.geminibot-research.py` | file_id: "py5a6f0cdf" |
| `078` | **`display`** | `1` | `,.py.display.aspect-restore.py` | Restore laptop panel 16:10 (2560x1600@165Hz), un-crush Herdr window geometry, and enforce  |
| `079` | **`geo`** | `1` | `,.py.geo.gods-eye-view.py` | file_id: "py71a9125b" |
| `080` | **`grok`** | `1` | `,.py.grok.startup-index.py` | Build Grok startup skill index (YAML frontmatter or compact) for baking STEP 2. |
| `081` | **`harness`** | `1` | `,.py.harness.connectivity-smoke.py` | file_id: "pyaa21ab8d" |
| `082` | **`inbox`** | `1` | `,.py.inbox.activity-log.py` | Log file activity under 000.inbox (flat root; no 090.log subfolder). |
| `083` | **`intent`** | `1` | `,.py.intent.constraint-gate.py` | file_id: "pyd62890cd" |
| `084` | **`kakao`** | `1` | `,.py.kakao.quick-send.py` | file_id: "pyb8c22314" |
| `085` | **`llm`** | `1` | `,.py.llm.notebooklm-artifact-extract.py` | Primary entry for ,.cskill.py.llm.notebooklm-artifact-extract. |
| `086` | **`llm-wiki`** | `1` | `,.py.llm-wiki.engine.py` | ,.py.llm-wiki.engine.py — Open-Source Enhanced LLM-Wiki Compiler Engine |
| `087` | **`lms`** | `1` | `,.py.lms.canvas.crawl.py` | file_id: "pyaf1cca31" |
| `088` | **`markdown`** | `1` | `,.py.markdown.ref-policy.py` | file_id: "py7759698a" |
| `089` | **`mp3`** | `1` | `,.py.mp3.convert.to-md.py` | file_id: "py77dbad98" |
| `090` | **`observability`** | `1` | `,.py.observability.summary.py` | ,.py.observability.summary.py — aggregate herdr ledger + memory/sleep logs. |
| `091` | **`outbox`** | `1` | `,.py.outbox.map-sync.py` | file_id: "py6d5740be" |
| `092` | **`panopto`** | `1` | `,.py.panopto.capture.deprecated-playwright.py` | Deprecated: this prototype launches a separate Playwright/Chrome profile. |
| `093` | **`smoke`** | `1` | `,.py.smoke.bg-screenshot-and-herdr.py` | Zero-interference background window screenshot, Ghost Cursor, and full combinatorial smoke |
| `094` | **`sortbase`** | `1` | `,.py.sortbase.cloud-sync.py` | Sortbase Supabase Cloud Direct Sync Engine. |
| `095` | **`steam`** | `1` | `,.py.steam.sale-top30-daily.py` | file_id: "pystmsl030" |
| `096` | **`streamer`** | `1` | `,.py.streamer.live-hud.py` | ,.test.streamer-live-hud.py — 치지직·유튜브 실시간 LIVE 방송 온에어(On-Air) HUD 추출기 (시범). |
| `097` | **`syncthing`** | `1` | `,.py.syncthing.watcher.py` | file_id: "pyf3c1d004" |
| `098` | **`timeline`** | `1` | `,.py.timeline.rest-linter.py` | ,.test.timeline-rest-linter.py — TaskNotes 타임라인 휴강·일정 무결성 및 고스트 이벤트 린터 (시범). |
| `099` | **`txt`** | `1` | `,.py.txt.convert.to-md.py` | file_id: "pyb282880a" |
| `100` | **`wfskill`** | `1` | `,.py.wfskill.consolidate.py` | file_id: "pyf39bde04" |
