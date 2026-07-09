# AI 데이터 분석 하네스

CSV 데이터 구조를 분석하고, 질문 설계부터 KPI, 분석, 대시보드, 최종 보고서까지 순차적으로 만드는 분석 하네스입니다.

## 무엇을 하는가

- `runs/latest/input/` 에 들어온 CSV를 기준으로 분석을 시작합니다.
- 입력 파일별 구조와 품질을 먼저 프로파일링합니다.
- 파일이 여러 개면 관계를 보수적으로 분류합니다.
- 질문, KPI, 핵심 인사이트를 정리합니다.
- `DESIGN.md` 기준의 HTML 대시보드와 최종 보고서를 생성합니다.
- 원본 CSV는 수정하지 않습니다.

## 어떻게 구동되는가

이 프로젝트의 기본 실행 방식은 에이전트 호출입니다.

```text
"파이프라인 실행해줘"
```

실행이 시작되면 내부적으로 아래 흐름을 따릅니다.

### 1. 입력 인벤토리 생성

가장 먼저 입력 폴더를 스캔하고, 각 CSV를 개별 프로파일링한 뒤 전역 매니페스트를 만듭니다.

```bash
python3 scripts/profile_inputs.py runs/latest/input runs/latest/outputs/00_input_manifest.json
```

이 단계에서 함께 쓰이는 파일:

- `scripts/profile_inputs.py`
- `scripts/profile_csv.py`
- `.claude/rules/input-contract.md`

생성 산출물:

- `runs/latest/outputs/00_input_manifest.json`
- `runs/latest/outputs/00_file_briefs/*.json`

### 2. 순차 분석 단계 실행

입력 인벤토리 이후 단계는 `.claude/agents/*.md` 지침을 따라 순서대로 실행됩니다.

| 단계 | 역할 | 주요 산출물 |
|---|---|---|
| 1 | 입력 프로파일 | `01_dataset_profile.md` |
| 2 | 정리·집계 준비 (옵션) | `01b_dataset_prep.md` |
| 3 | 탐색 분석 | `02_eda_report.md` |
| 4 | 문제 정의 | `03_problem_definition.md` |
| 5 | KPI 정리 | `04_kpi_summary.md` |
| 6 | 핵심 분석 | `05_analysis_report.md` |
| 7 | 시나리오 패키지 (옵션) | `05b_scenario_pack.md` |
| 8 | 대시보드 생성 | `06_dashboard.html` |
| 9 | 최종 보고 | `07_executive_report.md` |

옵션 단계는 항상 실행되지 않습니다.

- `01b_dataset_prep.md`: 입력 수가 많거나, 집계/축약 규칙을 먼저 정해야 할 때 실행
- `05b_scenario_pack.md`: 시간축이 충분하고 미래/개입 질문이 실제로 의미 있을 때만 실행

## 실행 규칙

- 입력 위치는 `runs/latest/input/` 입니다.
- 지원 형식은 `.csv` 입니다.
- 모든 CSV에는 헤더 행이 있어야 합니다.
- CSV 외 파일은 무시하지 않고 경고 대상으로 남깁니다.
- 파일 간 관계가 애매하면 억지로 합치지 않고 더 보수적인 입력 모드로 강등합니다.
- 대시보드는 항상 `DESIGN.md` 와 `dashboard-sample.html` 을 기준으로 만듭니다.
- 실행 결과는 `runs/latest/outputs/` 아래에 모입니다.

## 입력 모드

입력이 여러 개일 때는 `.claude/rules/input-contract.md` 기준으로 아래 모드 중 하나를 선택합니다.

- `single`: 단일 CSV 분석
- `stack`: 같은 구조의 CSV를 행 방향으로 적층
- `join`: 공통 키가 검증된 파일 결합
- `compare`: 같은 축을 기준으로 나란히 비교
- `portfolio`: 안전한 결합 규칙이 없어 병렬 해석

핵심 원칙은 간단합니다.

- 컬럼명이 같다고 같은 의미라고 가정하지 않습니다.
- 관계가 애매하면 `portfolio` 로 강등합니다.
- 정합 조건이 맞지 않으면 교차 파일 KPI를 만들지 않습니다.

## 파일 구조

```text
.
├── README.md
├── CLAUDE.md
├── DESIGN.md
├── dashboard-sample.html
├── scripts/
│   ├── profile_csv.py
│   └── profile_inputs.py
├── .claude/
│   ├── agents/
│   │   ├── data-ingestion.md
│   │   ├── eda.md
│   │   ├── problem.md
│   │   ├── metrics.md
│   │   ├── analysis.md
│   │   ├── dashboard.md
│   │   ├── report.md
│   │   ├── semantic-prep.md
│   │   └── scenario-pack.md
│   ├── rules/
│   │   ├── input-contract.md
│   │   ├── analysis-principles.md
│   │   └── design-system.md
│   └── skills/
│       ├── run-pipeline/
│       ├── visualize/
│       └── dashboard-design/
└── runs/
    └── latest/
        ├── input/
        │   └── README.md
        └── outputs/
            ├── 00_input_manifest.json
            ├── 00_file_briefs/
            ├── 01_dataset_profile.md
            ├── 01b_dataset_prep.md
            ├── 02_eda_report.md
            ├── 03_problem_definition.md
            ├── 04_kpi_summary.md
            ├── 05_analysis_report.md
            ├── 05b_scenario_pack.md
            ├── 06_dashboard.html
            └── 07_executive_report.md
```

## 주요 파일 설명

- `CLAUDE.md`: 이 프로젝트의 상위 운영 규칙
- `DESIGN.md`: 대시보드 디자인 기준
- `dashboard-sample.html`: 최종 HTML 대시보드의 베이스 템플릿
- `scripts/profile_csv.py`: 단일 CSV 구조/품질 브리프 생성기
- `scripts/profile_inputs.py`: 입력 폴더 전체를 스캔해 전역 매니페스트 생성
- `.claude/agents/*.md`: 각 파이프라인 단계의 역할과 출력 형식
- `.claude/rules/input-contract.md`: 입력 모드 정의와 강등 규칙의 단일 기준

## 빠른 시작

1. `runs/latest/input/` 에 CSV를 넣습니다.
2. 대화에서 `"파이프라인 실행해줘"` 라고 요청합니다.
3. 완료되면 `runs/latest/outputs/` 에 문서와 HTML 대시보드가 생성됩니다.

특정 단계만 다시 돌리고 싶다면, 예를 들어 아래처럼 요청하면 됩니다.

```text
"@dashboard만 다시 만들어줘"
"@eda만 다시 실행해줘"
```

## 참고

- `runs/latest/` 는 가장 최근 실행을 기준으로 사용하는 작업 공간입니다.
- 이 저장소에는 전체 파이프라인을 완전히 대체하는 단일 `bash` 엔트리포인트가 있는 것이 아니라, 입력 프로파일링 스크립트와 단계별 에이전트 지침이 결합된 구조입니다.
- 따라서 같은 입력이라도 최종 서술 문구와 해석은 일부 달라질 수 있지만, 입력 계약과 디자인 규칙은 공통으로 유지됩니다.
