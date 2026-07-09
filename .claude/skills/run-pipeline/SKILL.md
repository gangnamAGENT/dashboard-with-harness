---
name: run-pipeline
description: `runs/latest/input/` 의 CSV 입력을 점검하고 표준 산출물까지 순차 실행해야 할 때 사용합니다.
argument-hint: (없음 — `runs/latest/input/` 에 CSV 파일이 1개 이상 있어야 합니다)
tools: Read, Write, Bash, Glob, Agent
---

# /run-pipeline

## 언제 쓰나

- 입력 폴더의 CSV를 기준으로 전체 분석 파이프라인을 돌릴 때
- 표준 산출물을 다시 생성할 때
- 입력이 바뀌어 초기 인벤토리부터 다시 점검해야 할 때

## 실행 전 확인

1. `runs/latest/input/` 에 `.csv` 파일이 1개 이상 있는지 확인합니다
2. CSV가 없으면 즉시 중단합니다
   > "runs/latest/input/ 폴더에 CSV 파일을 넣어주세요."
3. 실행 ID 파일을 생성합니다
   ```bash
   date +%s > runs/latest/.run_id
   ```

## 1. 입력 인벤토리

아래 명령을 실행합니다.

```bash
python3 scripts/profile_inputs.py runs/latest/input runs/latest/outputs/00_input_manifest.json
```

생성 산출물:

- `runs/latest/outputs/00_input_manifest.json`
- `runs/latest/outputs/00_file_briefs/*.json`

정확한 필드와 강등 규칙은 `.claude/rules/input-contract.md` 를 따릅니다.

## 2. 실행 순서

```text
@data-ingestion
@semantic-prep   (필요할 때만)
@eda
@problem
@metrics
@analysis
@scenario-pack  (필요할 때만)
@dashboard
@report
```

## 3. 옵션 단계 판단

### `@semantic-prep`

아래 중 하나라도 해당하면 실행합니다.

- 입력 파일이 2개 이상
- `input_mode` 가 `stack`, `join`, `portfolio`
- `prefer_aggregation = true`
- 정렬, 축약, 집계 규칙을 먼저 정해야 함

### `@scenario-pack`

아래를 모두 만족할 때만 실행합니다.

- 시간 축이 해석 가능함
- 예측 대상 소스가 분명함
- 분석 질문이 미래나 개입 효과와 직접 연결됨

## 4. 체크포인트 규칙

- 산출물이 있고 `.run_id` 보다 새로우면 건너뜁니다
- 산출물이 없거나 오래됐으면 다시 실행합니다
- `00_input_manifest.json` 과 `00_file_briefs/*.json` 은 입력 기준으로 항상 새로 만듭니다

## 5. 중단 규칙

- 매니페스트가 없으면 다음 단계로 가지 않습니다
- 관계가 애매하면 `portfolio` 로 강등합니다
- 정합 조건이 없는 교차 파일 KPI는 허용하지 않습니다

## 6. 완료 확인

실행이 끝나면 실제 생성된 산출물만 보여줍니다.
