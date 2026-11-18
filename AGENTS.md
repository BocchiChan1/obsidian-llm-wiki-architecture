---
title: "Vault Space Global Constitution (AGENTS.md)"
type: "cli"
---
# Vault Space — Global Agent Instructions & Lazy Load Map

### 0. Lazy Load Map (`규칙 갱생` — 도메인 작업 시 해당 L1 `AGENTS.md` 단 1개만 열람)

루트 헌법은 전역 안전 프로토콜만 보유합니다. 세부 폴더 작업 시 전 트리를 스캔하지 말고 아래 **L1 `AGENTS.md`**만 지연 로딩하십시오.

| 작업 도메인 | 열람할 L1 헌법 파일 |
|:---|:---|
| 임시 수집 · 직통 Raw 분류 | `000.inbox/AGENTS.md` |
| 영구 지식 도서관 (46개 학문 리프 · 인문 · 직무) | `100.vault/AGENTS.md` |
| 일일 루틴 · 실시간 작업지(`221.workpaper`) · 타임라인 | `200.plan/AGENTS.md` |
| 창작 프로젝트(`310`) · 일상/실천 공간(`320`) | `300.areas/AGENTS.md` |
| 비-마크다운 원본 보존소 (`Copy-Only`) | `400.resource/AGENTS.md` |
| 최종 제출 산출물 단일 출고장 | `800.outbox/AGENTS.md` |
| 코드 · 46대 하네스 · 6종 스킬 · LLM-Wiki · UI | `900.maintenance/AGENTS.md` |
| CLI 네이티브 스킬 미러 인덱스 | `.agents/AGENTS.md` |

## 전역 핵심 프로토콜 (Core Protocols)

1. **GUARDIAN (무삭제 원칙)**: 기존 지식·데이터의 임의 삭제·요약 축소 금지. 수정 전 반드시 대상 섹션을 먼저 확인(`tskeleton` → `tslice`).
2. **인코딩 표준**: 모든 `.md` 및 `.py` 파일은 **`UTF-8 (no BOM)` + `LF`** 개행을 엄수 (`tsha <파일>`로 검증).
3. **표면 뷰와 `941.raw` 본문 물리 분리**: `100.vault`, `200.plan`(`221.workpaper` 제외), `300.areas`, `400.resource` 표면 폴더에는 `{folder}.md`, `,.map.*`, `,.base.*`, `AGENTS.md`만 유지하며, 일반 마크다운(`.md`) 노트는 `900.maintenance/940.llm-wiki/941.raw/<원래경로>/`에 생성 (`gen_vault_note_from_title()` 및 `raw-guard.py` 상시 동기화).
4. **무분별한 전 트리 `grep` / 전체 `Read` 금지 — 18종 전용 단축 도구 1순위**:
   - 로컬 AI·벡터 검색: `twiki`, `tgrep`, `hsearch`, `hsolve`, `hskill`, `hroute`
   - 마이크로 자동화: `houtlink`, `hspec`, `hclassify`, `hcode`, `husched`
   - 토큰 90% 절감 열람·검수: `tskeleton`, `tslice`, `tid`, `tlinks`, `tsha`, `tdiff`, `tcheck`
5. **PMEST v2 + UDC 단일 기호 파일명 규격**:
   - 공식: `,{상위P},,{하위P}[{대상}][~{공간}][;{물질}][!{에너지}][@{방법론_또는_정보성종류}].{역할type}.md`
   - 일반 문헌 노트는 `.note.md` (`type: ["note"]`)로 종결하며 모든 노트는 10자리 불변 `file_id`를 보유합니다.
6. **실수 발생 시 이중 기록 게이트 (`sentinel.py`)**: 코드·구조 결함 수정 시 반드시 `940.llm-wiki/948.mistake` 원장 기록과 관련 `920.prompt/921.skill` 재발 방지 규칙 갱신을 같은 턴에 함께 수행합니다.
7. **No-Console-Flash & 격리 브라우저 원칙**: Windows 백그라운드 작업은 `pythonw.exe` + `CREATE_NO_WINDOW (0x08000000)` + `.vbs` 래퍼를 사용하여 콘솔 창 깜빡임을 원천 차단하고, 웹 자동화는 에이전트 전용 `BrowserOS neo` (`127.0.0.1:9210/mcp`) 백그라운드 탭만 사용합니다.
