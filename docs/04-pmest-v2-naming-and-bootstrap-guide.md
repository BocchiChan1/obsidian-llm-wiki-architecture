# 04. PMEST v2 + UDC 파일 명명법 · 불변 `file_id` · 원자적 리네임 & 4단계 부트스트랩 가이드

> 본 문서는 AI가 파일을 열어보지 않고도 파일명만으로 도메인·대상·공간·주제·행위·문서역할을 100% 해독하게 만드는 **랑가나단(Ranganathan) PMEST v2 + UDC 단일 기호 문법**, **10자리 불변 `file_id`와 8대상 연쇄 최신화 프로토콜**, 그리고 본 저장소를 클론한 뒤 **기존 인프라와 충돌(`0%`) 없이 4단계로 도입하는 실전 가이드**입니다.

---

## 1. PMEST v2 + UDC 단일 기호 파일명 규격

```text
,{상위P},,{하위P}[{대상}][~{공간}][;{물질}][!{에너지}][@{방법론_또는_정보성종류}].{역할type}.md
```

| 인용 순서 | 기호 | 패싯 (YAML 매핑) | 정의 및 작성 규칙 | 예시 |
|:---:|:---:|:---|:---|:---|
| `1` | `,` / `,,` | **Personality (`personality`)** | 최상위 학문/도메인 및 하위 세부 분과 | `,f_sci,,cs` · `,occ,,work` · `,arts,,game` |
| `2` | `{ }` | **Target (`target`)** | 인물·캐릭터·창작물/게임 IP 고유 대상 (`entity-canonical-dict.json` 정본 영문) | `{neuro_sama}` · `{nte}` · `{oa}` |
| `3` | `~` | **Space (`space`)** | 국가·장소·소속사·기관·플랫폼 | `~stellive` · `~hololive` |
| `4` | `;` | **Matter (`matter`)** | 문서가 다루는 핵심 재료·단일 개념·피행위 대상 | `;llm-wiki-architecture` · `;bookmark` |
| `5` | `!` | **Energy (`energy`)** | 행위·과정·문제 해결·기능 동작 | `!bug-fix` · `!sync` · `!broadcast` |
| `6` | `@` | **Method (`method`)** | 학술 방법론(`@comparative`, `@structural`, `@causal`) 또는 정보 종류(`@spec`, `@guide`, `@dic`, `@lecture`) — 일반 문헌 노트는 생략 | `@spec` · `@guide` |
| `7` | `.type.md` | **Role (`type`)** | 단일 원소(`len(type) == 1`) 역할 타입. 일반 노트는 `.note.md` | `.note.md` · `.cskill.md` · `.rule.md` |

- **패싯 내부 결합 연산자 3원칙**:
  - `_` (언더바): 단일 개념 내부의 띄어쓰기 연결 (`neuro_sama`)
  - `-` (하이픈): 동일 패싯 내 벽-그림(전체-부분) 중첩 (`llm-wiki-architecture`)
  - `+` (플러스): 동일 패싯 내 대등한 두 대상 알파벳순 병렬 연결 (`{choki+hanako_nana}`)

---

## 2. 링크 영구 불변 원리 (`10-char file_id`) & `8대상 연쇄 최신화 매트릭스`

1. **10자리 불변 `file_id` + `ids_outlink`**: 모든 문서는 생성 시 `registry.register-path.py`를 통해 고유 10자리 ID를 부여받으며, 파일명을 전면 개편하거나 폴더를 이동해도 `tid <file_id>` 및 `file-tracker.py`가 O(1) 해시 테이블로 최신 디스크 경로를 즉시 반환합니다.
2. **탐색기 수동 이름 변경 금지**: 폴더·파일 리네임은 반드시 `,.py.rename.folder.py OLD NEW --rename-files` 및 `,.py.rename.vault.py --batch "OLD>NEW"`로 수행하여 폴더 노트(`{folder}.md`), 맵(`,.map.*`), 베이스(`,.base.*`), 본문 위키링크를 단일 트랜잭션으로 갱신합니다.
3. **8대상 연쇄 최신화 체크리스트**:
   - ① `948.mistake` (변경 배경 기록) → ② `stem-leaf.yaml` (번호·약어 사전) → ③ `filename` (디스크·`941.raw` 경로) → ④ `{folder}.md` / `,.map.*` / `,.base.*` → ⑤ `912-001.py` & `915.code-harness` 코드 상수 → ⑥ 루트 및 L1 `AGENTS.md` → ⑦ `921.skill` 및 `.agents/skills` 미러 → ⑧ `921-700.ruskill` SSOT 법전.

---

## 3. 빠른 시작 (4-Step Bootstrap Quickstart)

```bash
# Step 1: 저장소 클론 (000.inbox ~ 950.UI 표준 골격 + 지연 로딩 AGENTS.md 즉시 확보)
git clone https://github.com/BocchiChan1/obsidian-llm-wiki-architecture.git space
cd space

# Step 2: 옵시디언(Obsidian)에서 space/ 폴더를 Vault로 열기
# - 표면 폴더(000~400)는 뷰 전용으로 유지하고 본문 노트는 900.maintenance/940.llm-wiki/941.raw/ 하위에 격리

# Step 3: 로컬 AI (Ollama :11434 + LanceDB) 및 VaultCli 단축 도구 연결
# - docs/01-baseline-system-configuration.md 의 포트 및 모델 구성을 참조하여 필요한 모듈부터 단계적 활성화

# Step 4: 외부 프로덕션 서버·관제 시스템과는 오직 000.inbox/ ↔ 800.outbox/ 파일 드롭으로만 느슨하게 연동 (충돌 0%)
```
