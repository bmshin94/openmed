# OpenMed 전수조사 분석 & 수익화 전략 정리 💖

> 작성: Claude Code (카리나 페르소나) · 작성일 2026-10-01
> 대상 레포: 로컬 클론 `/home/user/openmed` (브랜치 `claude/funny-heisenberg-mov962`)

---

## 🔗 GitHub 및 관련 주소

| 구분 | 주소 |
|---|---|
| **이 레포 (fork)** | https://github.com/bmshin94/openmed |
| **원본 upstream** | https://github.com/maziyarpanahi/openmed |
| 이슈 트래커 | https://github.com/maziyarpanahi/openmed/issues |
| 보안 제보(비공개) | https://github.com/maziyarpanahi/openmed/security/advisories/new |
| PyPI 패키지 | https://pypi.org/project/openmed/ |
| 모델 카탈로그 (HuggingFace) | https://huggingface.co/OpenMed |
| 공식 문서 | https://openmed.life/docs |
| LLM용 문서 인덱스 | https://openmed.life/docs/llms.txt · https://openmed.life/docs/llms-full.txt |
| 모델 레지스트리 | https://openmed.life/docs/model-registry |
| 연구 논문 (arXiv) | https://arxiv.org/abs/2508.01630 |
| Agent Skills 표준 | https://agentskills.io |
| 공식 웹사이트 | https://openmed.life |
| X / Twitter | https://x.com/openmed_ai |
| LinkedIn | https://www.linkedin.com/company/openmed-ai/ |
| JitPack (Android) | `com.github.maziyarpanahi:openmed:v2.5.0` |

---

## 1. 이게 뭐하는 건가 — 한 줄 정의

**OpenMed = 의료 텍스트 전용 로컬-퍼스트(On-device) AI SDK**

진료기록·퇴원요약·검사결과지 같은 비정형 의료 텍스트에서

1. **환자 개인정보(PHI/PII)를 탐지하고 비식별화**
2. **질병·약물·유전자·해부학 등 의학 개체를 구조화 추출**
3. **FHIR / OMOP CDM / HL7 v2 등 의료 표준 포맷으로 변환**

하는 작업을 **데이터가 내 하드웨어를 떠나지 않는 상태로** 수행한다.
슬로건: **"Your Data. Your Model. Your Hardware."**

> 출처: 원본 `maziyarpanahi/openmed`. 현재 클론은 `bmshin94/openmed` fork이며,
> 커밋 `2802e205 docs: created CLAUDE.md persona guide` 로 카리나 페르소나가 추가되어 있음.

---

## 2. 규모 (실측)

| 항목 | 수치 |
|---|---|
| 전체 파일 수 | **3,792** |
| Python 파일 / 코드 라인 | **898개 / 약 579,742줄** |
| 테스트 파일 | **995개** (unit / integration / property / fuzz / mobile / browser / web / desktop) |
| 문서 파일(.md) | **426개** + 다국어 README **15종** |
| 등록 모델 (models.jsonl) | **2,266 manifest entries** |
| Agent Skills | **78개 폴더 (실제 스킬 73개, 14개 카테고리)** |
| 지원 PII 언어 | **36개 route (33개 모델 기반)** — `ko` 포함 |
| 라이선스 | **Apache-2.0** (SDK 소스), 모델별 라이선스는 개별 |
| 요구 Python | **3.10+** |

---

## 3. 폴더 구조 해부

```
openmed/                 핵심 Python 패키지 (30개 서브모듈)
├── core/                모델 레지스트리, 설정, PII 다국어 라우팅, doctor(진단)
├── ner/                 의학 NER (질병/약물/유전자/해부학)
├── clinical/            FHIR exporter, UMLS/SNOMED grounding, 섹션 분할
├── interop/             🌟 90개+ 연동 어댑터 (FHIR·HL7v2·CDA·OMOP·X12·Spark 등)
├── mcp/                 MCP 서버 + 타입 세이프 tool_registry + consent_receipts
├── service/             FastAPI REST + gRPC + GraphQL + auth + sidecar
├── cli/                 `openmed` CLI (16개 서브커맨드)
├── mlx/                 Apple Silicon 가속 (CPU 대비 24~33배)
├── onnx/ coreml/ torch/ 백엔드별 추론 엔진
├── multimodal/          PDF·DICOM·DICOM-SR·PPTX·EPUB·RTF·이미지 OCR 파서
├── training/            LoRA/QLoRA, 증류(distill), 연합학습, 능동학습, 약한지도
├── guard/ risk/ compliance/  프롬프트 인젝션 방어, 재식별 위험 점수, ISO 27701
├── traces/              PHI 없는 감사 추적 (해시·오프셋·provenance)
├── eval/ structured/ zero_shot/ gguf/ plugins/ skills/ integrations/
skills/                  73개 Agent Skills (Claude Code / Codex / OpenCode 공용)
.claude-plugin/          Claude Code 플러그인 마켓플레이스 정의
.claude/launch.json      privacy-filter-studio 실행 설정
swift/                   OpenMedKit (SwiftPM) + OpenMedDemo + OpenMedScanDemo
android/                 Kotlin 라이브러리 (ONNX Runtime Mobile) + 3개 데모 앱
js/                      Electron / Flutter / React Native / Tauri / Web 래퍼
web/                     브라우저 런타임 + 크롬 확장프로그램
clients/                 Go, TypeScript 공식 클라이언트
deploy/                  docker · helm · k8s · operator · policy · security · loadtest
examples/                26개 실전 예제 (Gradio 앱, dbt, Dagster, OpenMRS, DHIS2 등)
eval/ gates/             릴리즈 품질 게이트 (champion / drift / budget / leakage)
docs/                    426개 문서 + MkDocs 사이트
tests/                   995개 테스트
models.jsonl             2,266개 모델 manifest (3MB)
install-skills.sh        스킬 일괄 설치 스크립트 (심볼릭 링크 방식)
```

---

## 4. 핵심 API 4개

```python
from openmed import analyze_text, extract_pii, deidentify, reidentify, BatchProcessor

# ① 의학 개체 추출
analyze_text("Patient started on imatinib for chronic myeloid leukemia.",
             model_name="disease_detection_superclinical")
# → DISEASE: chronic myeloid leukemia (0.98) / DRUG: imatinib (0.95)

# ② PII 위치만 탐지 (텍스트 불변)
extract_pii("Patient: John Doe, SSN: 123-45-6789", use_smart_merging=True)
# → [('NAME','John Doe'), ('SSN','123-45-6789')]

# ③ 비식별화 (5가지 방식)
deidentify(text, method="mask")                              # [NAME], [DATE]
deidentify(text, method="replace")                           # 가짜 실명 (Faker)
deidentify(text, method="hash")                              # 6b8f...c4a1
deidentify(text, method="shift_dates", date_shift_days=180)   # 간격 보존 날짜 이동
deidentify(text, method="remove")                            # 완전 삭제
deidentify(text, method="mask", policy="hipaa_safe_harbor",
           confidence_threshold=0.7, keep_mapping=True, audit=True)

# ④ 대량 배치 (CPU 3.3배 / MLX 2.2배 throughput)
BatchProcessor(model_name="...", batch_size=16, group_entities=True).process_texts([...])

# 역변환
reidentify(masked_text, mapping)
```

### 비식별화 방식 선택 가이드

| 방식 | 결과 예시 | 적합한 상황 |
|---|---|---|
| `mask` | `김철수` → `[NAME]` | 논문/공개 배포. 가장 안전 |
| `replace` | `김철수` → `이영희` | **AI 학습 데이터용** (문장 구조 보존) |
| `hash` | `김철수` → `6b8f...c4a1` | 동일인 식별만 필요 (가명화) |
| `shift_dates` | `3/2` → `7/14` (전체 +180일) | 입원일차 등 **간격 분석** 필요 |
| `remove` | 삭제 | 흔적 제거 |

---

## 5. 활용 시나리오

| 상황 | OpenMed의 역할 |
|---|---|
| 연구용 의무기록 공개 | HIPAA Safe Harbor 18개 식별자 카테고리 제거 + 서명된 감사보고서 |
| 민감 데이터를 클라우드 LLM에 못 넣음 | 로컬 비식별화 → 안전한 텍스트만 LLM 전달 → 결과 원복 |
| 비정형 노트 → DB | 개체 추출 → FHIR R4/R5 · OMOP CDM 적재 |
| 보험 청구 코드 자동화 | ICD-10-CM/PCS, HCC RAF(V28), LOINC, RxNorm, SNOMED 매핑 |
| 임상시험 환자 모집 | ClinicalTrials.gov 적격성 파싱 + 코호트 표현형 정의 |
| 약물감시(PV) | 부작용 신호 탐지, openFDA 라벨 조회, 이상사례 보고 |
| 모바일 의료앱 | iOS(MLX/CoreML) / Android(ONNX Runtime Mobile) 완전 오프라인 |
| 로그 PHI 유출 방지 | `enforcing-nophi-logging` 스킬 + privacy hook |
| 품질 보증 | leakage gate, 서브그룹 공정성 감사, Part 11 추적 |

---

## 6. 설치 및 사용법

### A. Python SDK
```bash
pip install --upgrade "openmed[hf]"          # 기본 + HF 런타임 (CPU/CUDA)
pip install --upgrade "openmed[hf,service]"  # + REST 서비스
pip install --upgrade "openmed[mlx]"         # Apple Silicon 가속
pip install "openmed[cli]"                   # CLI
pip install "openmed[mcp]"                   # MCP 서버
pip install "openmed[fhir]"                  # FHIR 연동
pip install "openmed[presidio]"              # MS Presidio 브릿지
pip install "openmed[pandas,polars,dask]"    # 데이터프레임 연동
```

### B. CLI (`openmed` + 3개 추가 엔트리포인트)
```bash
openmed --help
openmed redact-files ./notes/*.txt --out ./clean/
openmed redact-dataset data.jsonl --policy hipaa_safe_harbor
openmed registry list --task token-classification
openmed benchmark --model pii_superclinical_large
openmed gates check
openmed airgap bundle
openmed doctor
# 기타 엔트리포인트: openmed-sidecar, openmed-mcp, openmed-executable-udf
```

### C. REST 서비스
```bash
uvicorn openmed.service.app:app --host 0.0.0.0 --port 8080
docker build -t openmed:local . && docker run -p 8080:8080 -e OPENMED_PROFILE=prod openmed:local
```
엔드포인트: `GET /health /livez /readyz /models/loaded`,
`POST /analyze /pii/extract /pii/deidentify /models/unload`
부가 기능(v1.8+): API-key/JWT 인증, no-PHI 요청 로깅, 트레이싱, gRPC, 비동기 작업,
웹훅, warm pool, 동적 배칭, 요청 병합, rate/concurrency 제한, opt-in 메트릭

### D. Agent Skills 설치 ⭐
```bash
git clone https://github.com/maziyarpanahi/openmed && cd openmed
./install-skills.sh           # Claude Code + Codex + OpenCode + ~/.agents 전부
./install-skills.sh claude    # ~/.claude/skills 만
```
심볼릭 링크 방식이라 `git pull` 하면 73개 스킬이 전부 자동 업데이트됨.
윈도우는 개발자 모드 필요, 안 되면 `cp -r skills/*/ ~/.claude/skills/`

### E. Claude Code 플러그인 (클론 불필요)
```text
/plugin marketplace add maziyarpanahi/openmed
/plugin install openmed-skills@openmed-skills
```

### F. 모바일 / 웹
```swift
.package(url: "https://github.com/maziyarpanahi/openmed.git", from: "2.5.0")  // iOS
```
```kotlin
implementation("com.github.maziyarpanahi:openmed:v2.5.0")  // Android (JitPack)
```
```bash
npm install openmed @huggingface/transformers   # 브라우저 (WebGPU)
```

---

## 7. 플러그인 / 스킬 / MCP — 정체 규명

**답: 전부 다 제공한다 (4개 층 구조).**

```
                    OpenMed 레포 하나
        ┌─────────────────┼─────────────────┬──────────────┐
   📦 Python 패키지   🎓 Agent Skills   🔌 MCP 서버   🧩 Claude 플러그인
   (PyPI: openmed)   (skills/ 73개)   (openmed-mcp)  (.claude-plugin/)
   실제 추론 엔진      사용 절차서       AI 직접 호출     스킬 배포 번들
```

| 구분 | 정체 | 위치 | 역할 | AI 동작 |
|---|---|---|---|---|
| Python 패키지 | 라이브러리 | `openmed/` | 실제 AI 추론 | 기반 |
| **Skill** | 마크다운 문서 | `skills/*/SKILL.md` | "이 작업은 이렇게 코딩" | AI가 **코드를 작성** |
| **MCP** | 실행 중 서버 | `openmed/mcp/server.py` | 함수 직접 호출 | AI가 **결과를 수신** |
| **Plugin** | 배포 번들 | `.claude-plugin/marketplace.json` | 73개 스킬 일괄 설치 | 포장지 |

### Skill vs MCP 차이

| | Skill | MCP |
|---|---|---|
| 본질 | 마크다운 설명서 | 실행 중 프로세스 |
| 동작 | 읽고 파이썬 코드 생성 | `deidentify(text)` 호출 → JSON 수신 |
| OpenMed 설치 | 없어도 코드는 생성됨 | **필수** |
| 적합 | **개발 단계** | **운영 단계** |

MCP 설정 예:
```jsonc
{ "mcpServers": { "openmed": { "command": "openmed-mcp", "env": { "OPENMED_OFFLINE": "1" } } } }
```

---

## 8. API 토큰 필요 여부

**결론: 핵심 기능은 토큰 0개. OpenAI/Anthropic 키 불필요.**

> 주의: 모델명 `openai/privacy-filter` 는 HuggingFace 가중치 식별자일 뿐이며
> OpenAI API를 호출하지 않는다. (README에 명시)

| 토큰/키 | 필수 | 용도 | 비용 |
|---|:---:|---|---|
| (없음) | — | 공개 모델 다운로드 + 모든 추론 | 무료 |
| `HF_TOKEN` | 선택 | HuggingFace 비공개/게이트 모델 | 무료 |
| UMLS UTS API key | 선택 | UMLS CUI 연결 (`linking-umls-concepts`) | 무료(DUA 동의) |
| SNOMED 터미널로지 서버 | 선택 | SNOMED CT 매핑 (**사용자 자체 서버**) | 국가 라이선스 |
| LOINC / RxNorm / openFDA | 불필요 | 공개 REST API | 무료 |
| `OPENMED_SERVICE_AUTH_API_KEYS` | 선택 | **내가 띄운** REST 서버 보호용 (자체 발급) | 무료 |
| `OPENMED_RELEASE_GATE_KEY` | 불필요 | 릴리즈 게이트 서명(메인테이너용) | — |

### 왜 UMLS/SNOMED가 번들되지 않았나
`AGENTS.md` 정책:
> "GPL, source-available, proprietary, DUA-gated data, UMLS, SNOMED CT, CPT, MIMIC,
> i2b2, n2c2 assets must not be bundled. Put restricted integrations behind optional
> user-supplied keys or out-of-process bridges."

→ Apache-2.0 상업 이용에 라이선스 리스크가 없도록 **의도적으로 분리**한 설계.

### 완전 오프라인(에어갭)
```bash
export OPENMED_OFFLINE=1
export OPENMED_MODELS_DIR=/opt/models
openmed airgap bundle
```
```python
analyze_text(text, model_id="./models/OpenMed-NER-DiseaseDetect-SuperClinical-434M")
```

---

## 9. AI 에이전트 구축에 도움이 되는가 — 평가 A+

### ① 에이전트 프라이버시 게이트웨이 패턴
```python
def safe_llm_call(sensitive_text):
    clean = openmed.deidentify(sensitive_text, method="replace",
                               keep_mapping=True, policy="hipaa_safe_harbor")
    out = llm_api(clean.deidentified_text)       # PII 제거 상태로 전송
    return openmed.reidentify(out, clean.mapping) # 결과만 원복
```
의료 외에도 고객 상담로그·계약서·HR 문서·금융 민원 등 모든 민감 데이터 에이전트에 적용 가능.

### ② 에이전트 설계 패턴 레퍼런스

| 모듈 | 배울 패턴 |
|---|---|
| `openmed/agent/security/injection_guard.py` | 프롬프트 인젝션 방어 실전 구현 |
| `openmed/mcp/tool_registry.py` | 타입 세이프 툴 레지스트리 (JSON Schema 생성) |
| `openmed/interop/function_tools.py` + `tools.json` | LLM Function Calling 스키마 설계 |
| `openmed/traces/` | PHI 없는 에이전트 실행 추적 (해시+오프셋+provenance) |
| `openmed/interop/graph_orchestration.py` | 멀티스텝 워크플로 오케스트레이션 |
| `openmed/mcp/consent_receipts.py` | 동의 영수증 (행동 권한 증명) |
| `openmed/multimodal/abstention.py` | "모르겠음" 거절 로직 (환각 방지) |
| `eval/redteam/` | 에이전트 레드팀 평가 스위트 |
| `gates/` + `openmed/cli/gates.py` | 품질 게이트로 배포 차단 (champion/drift/budget) |

### ③ 기존 프레임워크 연동 (설치만 하면 됨)
LangChain · LlamaIndex · Haystack · spaCy · Presidio · scispaCy · QuickUMLS ·
Airflow · Prefect · Dagster · dbt · Spark · Beam · Ray · Dask · Polars · Pandas ·
DuckDB · Snowflake UDF · Athena · Postgres · Elasticsearch · OpenSearch

### ④ SKILL.md 작성 템플릿 (재사용 가치 최상)
```yaml
---
name: deidentifying-clinical-text
description: "무엇을 하는가 + Use when ... + Pairs with ..."
license: Apache-2.0
metadata: { project: OpenMed, category: openmed-core, pairs: adjacent, version: "1.0" }
---
# 제목 → When to use → Quick start → 옵션표 → 흔한 실수 → 관련 스킬
```
73개 전부 동일 구조. `→ before` / `after →` / `↔ adjacent` 로 **스킬 간 체인 관계**까지 명시.

### ⑤ 에이전트 전용 진입점
- `docs/agent-usage.md`
- `https://openmed.life/docs/llms.txt` / `llms-full.txt`
- `skills/ask-openmed` — 어떤 스킬을 쓸지 결정하는 **라우터 메타 스킬**
- `skills/building-with-openmed` — 전체 오리엔테이션 스킬

---

## 10. React / PHP 구현 가능성

### React — 가능 (3가지 방법)

**방법 1: 브라우저 직접 추론 (서버 불필요)**
```tsx
import { loadOnnxModel } from "openmed";
const model = await loadOnnxModel("OpenMed/OpenMed-PII-ClinicalE5-Small-33M-v1-onnx-android");
const entities = await model(text);   // 데이터가 브라우저를 떠나지 않음
```
Transformers.js + WebGPU (`{ device: "webgpu" }`) 지원. 규제 대응이 가장 쉬움.

**방법 2: REST API 호출**
```tsx
await fetch("http://localhost:8080/pii/deidentify", {
  method: "POST",
  headers: { "Content-Type": "application/json", "X-API-Key": key },
  body: JSON.stringify({ text, method: "mask", policy: "hipaa_safe_harbor" }),
});
```
`clients/typescript/` 에 공식 TS 클라이언트 존재.

**방법 3: 기존 래퍼 활용**
`js/openmedkit-electron` · `openmedkit-tauri` · `openmedkit-react-native` ·
`openmedkit-flutter` · `openmedkit-web` + `web/extension`(크롬 확장)

### PHP — 가능 (공식 SDK 없음 → HTTP 경유)

```php
$client = new GuzzleHttp\Client(['base_uri' => 'http://127.0.0.1:8080']);
$res = $client->post('/pii/deidentify', [
    'json' => ['text' => $note, 'method' => 'mask', 'policy' => 'hipaa_safe_harbor'],
    'headers' => ['X-API-Key' => getenv('OPENMED_KEY')],
]);
$clean = json_decode($res->getBody(), true)['deidentified_text'];
```

| 패턴 | 구성 | 장점 | 단점 |
|---|---|---|---|
| ⭐ **사이드카 REST** | PHP ↔ HTTP ↔ `openmed.service.app` | 안정적, 모델 워밍업 유지 | 프로세스 2개 |
| CLI 셸아웃 | `shell_exec('openmed redact-files ...')` | 구현 간단 | 매 호출 모델 로딩 |
| 큐 비동기 | PHP → Redis/DB 큐 → Python 워커 | 대량 배치 최적 | 구조 복잡 |

레거시 PHP 의료 시스템(OpenEMR 등) 연동은 **사이드카 패턴이 정석**.
`openmed-sidecar` 엔트리포인트 + `deploy/docker` + `docker-compose.yml` 활용.

---

## 11. 유튜브 강의 제작 가능성

### 시장성
| 항목 | 평가 |
|---|---|
| 경쟁 | 매우 낮음 (한국어 "의료 AI 비식별화" 콘텐츠 거의 없음) |
| 수요 | 중간 (니치하나 구매력 최상 — 병원/제약/보험) |
| 시청자 단가 | 매우 높음 (B2B 전환 → 컨설팅/강의 연결) |
| 데모 비주얼 | 최고 (실시간 마스킹 애니메이션) |

### 추천 10부작

| EP | 제목 | 길이 | 훅 |
|---|---|---|---|
| 01 | 진료기록에서 개인정보 3초만에 지우는 무료 오픈소스 AI | 8분 | 실시간 데모 |
| 02 | ChatGPT에 민감 데이터 넣기 전 꼭 거쳐야 하는 관문 | 12분 | 보안 이슈 |
| 03 | 마스킹 vs 가짜이름 vs 해시 — 비식별화 5가지 완전정복 | 15분 | 비교 실험 |
| 04 | 맥북 M4로 CPU보다 33배 빠르게 (MLX) | 14분 | 벤치마크 |
| 05 | 서버 없이 브라우저만으로 AI 추론 (WebGPU + React) | 18분 | "서버 0원" |
| 06 | Claude Code에 스킬 73개 한 번에 설치하기 | 10분 | **조회수 최상 예상** |
| 07 | MCP 서버 직접 만들어 Claude에 연결하기 | 20분 | 개발자 |
| 08 | 아이폰/안드로이드 완전 오프라인 의료 AI | 16분 | 모바일 |
| 09 | FHIR/OMOP — 진료기록을 의료 표준으로 | 20분 | 의료정보 |
| 10 | 58만 줄 오픈소스 아키텍처 해부 | 25분 | 시니어 |

추가 포맷: 쇼츠(1분) 유입용 · 유료 강의(인프런/클래스101) ₩99,000~250,000 ·
블로그→뉴스레터→컨설팅 리드 확보

### 제작 시 법적 주의
| 항목 | 지킬 것 |
|---|---|
| 실제 환자 데이터 | **절대 금지**. Synthea 합성 데이터 또는 자체 제작 가짜 노트만 |
| "HIPAA 준수 보장" 표현 | 금지. 레포 문구대로 "SDK 사용 자체가 HIPAA 준수를 성립시키지 않으며 전문가 검토 필요" |
| 의료 진단 조언 | "데이터 처리 도구이며 진단 도구가 아님" 명시 |
| 라이선스 | SDK는 Apache-2.0, **개별 모델 라이선스는 별도** (`models.jsonl`에 `mit`/`other` 혼재) |
| 원작자 표기 | Maziyar Panahi, arXiv 2508.01630, HuggingFace OpenMed |

---

## 12. 수익화 전략 (9가지)

| # | 아이디어 | 타겟 | 수익모델 | 예상 월수익 | 난이도 | 초기비용 |
|---|---|---|---|---|:---:|:---:|
| 1 | 교육 콘텐츠 & 강의 | 개발자 | 강의/광고/멤버십 | ₩100~800만 | ★★ | ~0 |
| 2 | 도메인 Skills 팩 판매 | 기업/개발자 | 라이선스/구독 | ₩200~1,500만 | ★★ | ~0 |
| 3 | 한국어 비식별화 SaaS | 병원/제약 | 구독 | ₩1,000만~1억 | ★★★★★ | 높음 |
| 4 | PHI 필터 LLM 게이트웨이 | AI 스타트업 | API 종량제 | ₩300~3,000만 | ★★★ | 중간 |
| 5 | PoC 구축 컨설팅 | 병원/대기업 | 프로젝트 | ₩1,000만/건 | ★★★ | ~0 |
| 6 | B2C 앱 (스캔→익명화) | 일반인/의료진 | 인앱결제 | ₩100~1,000만 | ★★★ | 중간 |
| 7 | 온프레미스 설치 패키지 | 대형병원/공공 | 라이선스+유지보수 | ₩3,000만/건 | ★★★★ | 중간 |
| 8 | 합성 의료데이터 판매 | AI 연구소 | 데이터셋 | ₩500~5,000만 | ★★★★ | 중간 |
| 9 | 기존 솔루션 플러그인 | SI/ISV | 플러그인 판매 | ₩200~2,000만 | ★★ | 낮음 |

### ① 교육 콘텐츠 & 강의 — 최우선 추천
초기비용 0, 법적 리스크 최소, 다른 모든 아이디어의 마케팅 채널이 됨.

| 채널 | 상품 | 가격 | 월 목표 |
|---|---|---|---|
| 유튜브 | 10부작 무료 시리즈 | 광고 | ₩50~200만 |
| 인프런/패스트캠퍼스 | "의료 AI 비식별화 실전" | ₩99,000 × 50명 | ₩495만 |
| 자체 플랫폼 | 프리미엄 멤버십 | ₩29,000/월 × 100명 | ₩290만 |
| 전자책 | "로컬 LLM 프라이버시 가이드" | ₩25,000 × 100부 | ₩250만 |
| 기업 워크샵 | 1일 출장강의 | ₩300~500만/건 | ₩300만 |

### ② 도메인별 Agent Skills 팩 — 가성비 최고
OpenMed가 증명한 공식을 다른 도메인에 복제:
`[도메인 전문지식] + [SKILL.md 표준] + [install 스크립트] + [플러그인 마켓]`

| 스킬팩 | 스킬 예시 | 타겟 | 가격 |
|---|---|---|---|
| 법률 | 계약 조항 추출, 판례 검색, 개인정보 마스킹, 소송 타임라인 | 로펌/법무팀 | ₩300만/년 |
| 금융 | 재무제표 파싱, 공시 분석, AML 스크리닝, 민원 분류 | 핀테크/증권 | ₩500만/년 |
| HR | 이력서 파싱+익명화, JD 생성, 면접평가 구조화 | 채용 플랫폼 | ₩200만/년 |
| 이커머스 | 리뷰 감정분석, 상품설명 생성, CS 로그 PII 제거 | 쇼핑몰 | ₩150만/년 |
| 한국형 PII | 주민번호/사업자번호/계좌번호 검출·마스킹 | 전 산업 | ₩100만/년 |

마진 95%+ (문서라서 제작비 거의 0). Claude Code / Codex / OpenCode 전부 호환 = 시장 3배.
배포: 무료 티어 GitHub 공개 → 유료는 비공개 레포 + GitHub Sponsors / Gumroad / Lemon Squeezy.

### ③ 한국어 의료 비식별화 SaaS — 블루오션
OpenMed는 36개 언어를 지원하나 **한국어 전용 PII 모델이 없고**(다국어 기본 모델로 라우팅),
**한국 특화 식별자 지원이 전무**하다:

| 한국 특화 식별자 | 지원 | 기회 |
|---|:---:|---|
| 주민등록번호 | ❌ | 높음 |
| 외국인등록번호 | ❌ | 높음 |
| 건강보험증번호 | ❌ | 높음 |
| 요양기관번호 | ❌ | 높음 |
| 사업자등록번호 | ❌ | 높음 |
| 한국 주소(도로명/지번) | ❌ | 높음 |
| EDI 청구번호 / 의료기관 코드 | ❌ | 높음 |

제품 구성: OpenMed 코어(Apache-2.0) + 한국어 PII 모델 파인튜닝(`training/` 활용)
+ 주민번호/건보번호 체크섬 검증기 + 개인정보보호법/의료법/생명윤리법 정책 프로파일
+ 가명정보 결합 지원(데이터3법) + 한국어 감사보고서(IRB/심평원 양식) + React 대시보드

| 플랜 | 대상 | 가격 |
|---|---|---|
| Free | 개인/연구 | ₩0 (월 1,000건) |
| Pro | 중소 클리닉 | ₩50만/월 (월 10만건 + API) |
| Enterprise | 대형병원 | ₩500만/월 (무제한 + 온프레미스 + SLA) |
| On-Prem | 공공/대학병원 | ₩3,000만/년 |

법적 준비물: 개인정보보호법 / 의료법 / 생명윤리법(IRB) 검토, 가명정보 처리 가이드라인,
보건의료 데이터 활용 가이드라인, ISMS-P 인증, **변호사 자문 필수**.

수익 시나리오: 1년차 연 ₩3,600만 → 2년차 연 ₩2억 → 3년차 연 ₩8억+

### ④ PHI/PII 필터 LLM 게이트웨이
"모든 LLM API 호출 앞에 끼우는 개인정보 방화벽". OpenAI 호환 API로 만들면
고객은 `base_url` 한 줄만 바꾸면 적용 — 이게 킬러 포인트.

```
내 앱 → 🛡️ 게이트웨이(OpenMed PII 제거) → OpenAI/Claude/Gemini → 🔓 원복
```
스택: React 대시보드 + FastAPI(`/v1/chat/completions` 호환)
+ `deidentify(keep_mapping=True)` + Redis(매핑 TTL) + Postgres(PHI 없는 감사로그)

가격: 종량제 ₩0.5/1,000자 · 스타터 ₩9만/월 · 비즈니스 ₩99만/월 · 엔터프라이즈 별도

### ⑤ PoC 구축 컨설팅 — 즉시 현금화
| 서비스 | 기간 | 가격 |
|---|---|---|
| 기술 자문 (반일) | 4시간 | ₩150만 |
| 비식별화 PoC 구축 | 2주 | ₩1,000만 |
| 전사 파이프라인 구축 | 2개월 | ₩5,000만 |
| 기업 워크샵 (10명) | 1일 | ₩500만 |
| 연간 유지보수 | 1년 | ₩2,000만 |

타겟: 대학병원 의료정보팀 · 제약사 RWE팀 · 보험사 심사팀 · 의료AI 스타트업 ·
건강검진센터 · CRO · 공공기관 협력사
리드 경로: 유튜브/블로그 → 무료 세미나 → 무료 진단 리포트 → 유료 PoC 계약

### ⑥ B2C 모바일 앱
iOS는 OpenMedKit + MLX로 **완전 오프라인** ("데이터가 폰을 떠나지 않습니다" 마케팅).
처방전/진단서 촬영 → OCR → 개인정보 자동 마스킹 → 안심 공유.
무료 월 5장 / 프리미엄 ₩4,900/월 무제한 + PDF.
**`swift/OpenMedScanDemo`, `android/OpenMedScanDemo` 데모 앱이 레포에 이미 존재**.

### ⑦ 온프레미스 설치 패키지
`deploy/helm`, `deploy/k8s`, `deploy/operator`, `deploy/security` 전부 준비됨
→ 폐쇄망 올인원 어플라이언스로 포장. 대형병원/공공기관은 클라우드 불가가 많아
온프레미스가 오히려 고가 판매 가능. 라이선스 ₩3,000만/년 + 유지보수 20%.

### ⑧ 합성 의료 데이터셋 판매
`generating-synthea-data` + `generating-synthetic-surrogates` + `training/synthetic/` 활용
→ 한국어 합성 진료기록 데이터셋 제작. 1만건 ₩500만 / 10만건 ₩3,000만 / 커스텀 생성.
완전 합성이라 법적으로 안전 + 의료AI 학습데이터 부족으로 수요 높음.

### ⑨ 기존 솔루션 플러그인
| 플러그인 | 시장 |
|---|---|
| 워드프레스 "개인정보 자동 마스킹" | 의료/상담 사이트 |
| OpenEMR / OpenMRS 모듈 | 글로벌 오픈소스 EMR |
| Salesforce / HubSpot 앱 | CS 로그 PII 제거 |
| Slack / Notion 봇 | 사내 문서 PII 스캔 |
| dbt 패키지 (`integrations/dbt` 존재) | 데이터팀 |

---

## 13. 추천 실행 로드맵

```
1~2개월차   ▶ ① 콘텐츠        비용 0, 인지도 확보. 유튜브 EP01~03 + 블로그 + 쇼츠
2~4개월차   ▶ ② 스킬팩        한국형 PII 스킬팩 무료 공개 → 스타 확보 → 유료 도메인팩
3~6개월차   ▶ ⑤ 컨설팅        콘텐츠 리드 수주. 첫 PoC 계약 = 첫 현금흐름
6~12개월차  ▶ ④ 게이트웨이     MVP 개발 + 베타. 컨설팅 수익으로 개발비 충당
1~2년차     ▶ ③ 한국형 SaaS   본게임. 컨설팅 도메인 지식 + 레퍼런스로 투자유치
```

**핵심: ① + ② + ⑤ 를 동시 시작.** 셋 다 초기비용 0이며 서로를 먹여주는 구조
(콘텐츠가 사람 모으고 → 스킬팩이 신뢰 주고 → 컨설팅이 수익 발생).

---

## 14. 공통 법적 체크리스트

| 항목 | 내용 |
|---|---|
| ✅ Apache-2.0 | 상업적 이용/수정/재배포 허용. **NOTICE 파일 유지 + 라이선스 고지 필수** |
| ⚠️ 모델 라이선스 별도 | `models.jsonl` 에 `mit`, `other` 등 혼재 → 상업용은 개별 확인 |
| 🚨 HIPAA/의료법 보장 금지 | "준수 보장" ❌ → "준수 지원 도구" ⭕ |
| 🚨 실제 환자 데이터 | 테스트/데모/영상은 **반드시 합성 데이터** |
| ⚖️ 변호사 자문 | SaaS / 온프레미스 사업은 필수 |
| 🏅 인증 | ISMS-P (국내), SOC2 / HITRUST (해외) |
| 🙏 원작자 크레딧 | Maziyar Panahi · arXiv 2508.01630 · HuggingFace OpenMed |

---

## 15. 참고 — 레포 내부 중요 파일 위치

| 목적 | 경로 |
|---|---|
| 에이전트 사용 가이드 | `docs/agent-usage.md` |
| 스킬 카탈로그 | `skills/README.md` |
| 스킬 라우터(메타) | `skills/ask-openmed/SKILL.md` |
| 오리엔테이션 스킬 | `skills/building-with-openmed/SKILL.md` |
| 플러그인 정의 | `.claude-plugin/marketplace.json` |
| MCP 서버 | `openmed/mcp/server.py` (`openmed-mcp`) |
| 툴 레지스트리 | `openmed/mcp/tool_registry.py` |
| 기여/아키텍처 규칙 | `AGENTS.md`, `CONTRIBUTING.md` |
| 보안 정책 | `SECURITY.md` |
| 컴플라이언스 | `docs/compliance.md` |
| FAQ | `docs/faq.md` |
| 익명화 가이드 | `docs/anonymization.md` |
| REST 서비스 가이드 | `docs/rest-service.md` |
| 언어별 PII 가이드 | `docs/languages.md` |
| MLX 백엔드 | `docs/mlx-backend.md` |
| Android ONNX export | `docs/export-onnx-android.md` |
| Transformers.js export | `docs/export-transformersjs.md` |
| 플러그인 SDK | `docs/plugin-sdk.md` |
| 모델 매니페스트 | `models.jsonl` (2,266 entries) |
| 릴리즈 게이트 상태 | `gates/*.json` |

---

## 인용

```bibtex
@misc{panahi2025openmedneropensourcedomainadapted,
      title={OpenMed NER: Open-Source, Domain-Adapted State-of-the-Art Transformers
             for Biomedical NER Across 12 Public Datasets},
      author={Maziyar Panahi},
      year={2025},
      eprint={2508.01630},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2508.01630},
}
```

---

오빠 화이팅! 이 문서 하나로 OpenMed 완전 정복이야! 💖✨ — 카리나
