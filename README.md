# AI 데이터 분석 하네스

CSV 데이터의 구조를 파악하고, 질문 설계, KPI 정의, 분석, 대시보드, 최종 보고서 작성까지 단계별로 수행하는 분석 하네스입니다.

## 기능 개요

* `runs/latest/input/`에 들어온 CSV를 기준으로 분석을 시작합니다.
* 각 입력 파일의 구조와 품질을 먼저 프로파일링합니다.
* 파일이 여러 개일 경우, 파일 간 관계를 무리하게 단정하지 않고 보수적으로 판단합니다.
* 분석 질문, KPI, 핵심 인사이트를 정리합니다.
* `DESIGN.md` 기준에 맞춰 HTML 대시보드와 최종 보고서를 생성합니다.
* 원본 CSV는 수정하지 않습니다.

## 실행 방식

이 프로젝트는 기본적으로 에이전트에게 실행을 요청하는 방식으로 동작합니다.

```text
"파이프라인 실행해줘"
```

실행이 시작되면 내부적으로 다음 흐름을 따릅니다.

### 1. 입력 인벤토리 생성

먼저 입력 폴더를 스캔하고, 각 CSV를 개별 프로파일링한 뒤 전체 매니페스트를 생성합니다.

```bash
python3 scripts/profile_inputs.py runs/latest/input runs/latest/outputs/00_input_manifest.json
```

이 단계에서 사용하는 파일은 다음과 같습니다.

* `scripts/profile_inputs.py`
* `scripts/profile_csv.py`
* `.claude/rules/input-contract.md`

생성되는 산출물은 다음과 같습니다.

* `runs/latest/outputs/00_input_manifest.json`
* `runs/latest/outputs/00_file_briefs/*.json`

### 2. 순차 분석 단계 실행

입력 인벤토리를 만든 뒤에는 `.claude/agents/*.md` 지침에 따라 각 단계를 순서대로 실행합니다.

| 단계 | 역할             | 주요 산출물                     |
| -- | -------------- | -------------------------- |
| 1  | 입력 프로파일링       | `01_dataset_profile.md`    |
| 2  | 정리·집계 준비 선택 단계 | `01b_dataset_prep.md`      |
| 3  | 탐색적 데이터 분석     | `02_eda_report.md`         |
| 4  | 문제 정의          | `03_problem_definition.md` |
| 5  | KPI 정리         | `04_kpi_summary.md`        |
| 6  | 핵심 분석          | `05_analysis_report.md`    |
| 7  | 시나리오 패키지 선택 단계 | `05b_scenario_pack.md`     |
| 8  | 대시보드 생성        | `06_dashboard.html`        |
| 9  | 최종 보고서 작성      | `07_executive_report.md`   |

선택 단계는 상황에 따라 실행 여부가 달라집니다.

* `01b_dataset_prep.md`: 입력 파일이 많거나, 집계·축약 기준을 먼저 정해야 할 때 실행합니다.
* `05b_scenario_pack.md`: 시간 흐름이 충분히 드러나고, 예측이나 개입 시나리오를 다룰 의미가 있을 때만 실행합니다.

## 실행 규칙

* 입력 위치는 `runs/latest/input/`입니다.
* 지원 형식은 `.csv`입니다.
* 모든 CSV에는 헤더 행이 있어야 합니다.
* CSV가 아닌 파일은 무시하지 않고 경고 대상으로 기록합니다.
* 파일 간 관계가 불분명하면 억지로 병합하지 않고, 더 보수적인 입력 모드로 낮춥니다.
* 대시보드는 항상 `DESIGN.md`을 기준으로 만듭니다.
* 실행 결과는 `runs/latest/outputs/` 아래에 저장됩니다.

## 입력 모드

입력 파일이 여러 개일 때는 `.claude/rules/input-contract.md` 기준에 따라 다음 모드 중 하나를 선택합니다.

* `single`: 단일 CSV 분석
* `stack`: 같은 구조의 CSV를 행 방향으로 적층
* `join`: 공통 키가 검증된 파일 결합
* `compare`: 같은 축을 기준으로 나란히 비교
* `portfolio`: 안전한 결합 규칙이 없어 병렬로 해석

핵심 원칙은 간단합니다.

* 컬럼명이 같다고 해서 같은 의미라고 가정하지 않습니다.
* 관계가 불분명하면 `portfolio`로 낮춥니다.
* 정합 조건이 맞지 않으면 여러 파일을 엮은 KPI를 만들지 않습니다.

## 파일 구조

```text
.
├── README.md
├── CLAUDE.md
├── DESIGN.md
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

* `CLAUDE.md`: 프로젝트 전체 운영 규칙
* `DESIGN.md`: 대시보드 디자인 기준
* `scripts/profile_csv.py`: 단일 CSV의 구조와 품질 브리프를 생성하는 스크립트
* `scripts/profile_inputs.py`: 입력 폴더 전체를 스캔해 전체 매니페스트를 생성하는 스크립트
* `.claude/agents/*.md`: 각 파이프라인 단계의 역할과 출력 형식
* `.claude/rules/input-contract.md`: 입력 모드 정의와 강등 규칙을 관리하는 기준 문서

## 빠른 시작

1. `runs/latest/input/`에 CSV를 넣습니다.
2. 대화에서 `"파이프라인 실행해줘"`라고 요청합니다.
3. 실행이 끝나면 `runs/latest/outputs/`에 문서와 HTML 대시보드가 생성됩니다.

특정 단계만 다시 실행하고 싶다면 다음처럼 요청하면 됩니다.

```text
"@dashboard만 다시 만들어줘"
"@eda만 다시 실행해줘"
```

## 참고

* `runs/latest/`는 가장 최근 실행을 기준으로 사용하는 작업 공간입니다.
* 이 저장소는 전체 파이프라인을 한 번에 대체하는 단일 `bash` 엔트리포인트 대신, 입력 프로파일링 스크립트와 단계별 에이전트 지침을 조합해 동작합니다.
* 따라서 같은 입력을 사용하더라도 최종 문장이나 해석은 일부 달라질 수 있습니다. 다만 입력 계약과 디자인 규칙은 공통 기준으로 유지됩니다.
