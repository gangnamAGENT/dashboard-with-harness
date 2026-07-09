# 입력 계약 규칙

이 문서는 `runs/latest/outputs/00_input_manifest.json` 의 단일 기준 문서입니다.
입력 모드 정의, 허용 필드, 강등 규칙은 여기만 따릅니다.

## 목적

- 입력 파일 목록을 표준 형식으로 기록합니다
- 파일 간 관계를 보수적으로 분류합니다
- 뒤 단계가 같은 계약을 공유하도록 만듭니다

## 매니페스트 필수 필드

- `schema_version`
- `input_count`
- `input_mode`
- `inputs`
- `relationship_candidates`
- `global_profile`
- `warnings`

## 입력 모드 정의

- `single`: 단일 파일 분석
- `stack`: 같은 의미와 같은 집계 수준을 가진 파일을 행 방향으로 묶는 분석
- `join`: 공통 키와 정합 조건이 확인된 파일을 결합하는 분석
- `compare`: 같은 축이나 같은 지표를 파일 간 나란히 비교하는 분석
- `portfolio`: 안전한 결합 규칙이 없어서 파일별로 병렬 해석하는 분석

## 입력별 필수 필드

각 `inputs[]` 항목은 아래를 포함해야 합니다.

- `file_name`
- `path`
- `brief_path`
- `shape`
- `time_support`
- `metric_columns`
- `dimension_columns`
- `dashboard_profile`
- `warnings`

## 관계 후보 필드

각 `relationship_candidates[]` 항목은 아래를 포함해야 합니다.

- `left`
- `right`
- `relation`
- `confidence`
- `reason`

## 강등 규칙

아래 중 하나라도 불명확하면 더 보수적인 모드로 강등합니다.

- 공통 키의 의미
- 단위
- 행 수준
- 시간 축
- 컬럼 의미

강등 순서:

- `join` → `compare`
- `compare` → `portfolio`
- `stack` → `portfolio`

## 교차 파일 KPI 허용 조건

아래가 모두 맞을 때만 허용합니다.

- 공통 키 의미가 분명함
- 단위가 같음
- 행 수준이 맞음
- 기간 축이 맞음
- 계산식이 파일 간 의미를 유지함

## 기본 원칙

- 컬럼명이 같다고 같은 의미라고 단정하지 않습니다
- 관계가 애매하면 `portfolio` 를 택합니다
- 매니페스트가 없는 상태에서 뒤 단계로 가지 않습니다
- 여러 문서에 모드 정의를 복제하지 않습니다
