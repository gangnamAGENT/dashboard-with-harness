# AI 데이터 분석 하네스

`runs/latest/input/` 의 CSV 입력을 분석해 대시보드와 보고서를 생성합니다.

@.claude/rules/input-contract.md
@.claude/rules/analysis-principles.md
@.claude/rules/design-system.md
@DESIGN.md

## 입력 규칙

- 입력 위치는 `runs/latest/input/`
- 지원 형식은 `.csv`
- CSV는 1개 이상 둘 수 있습니다
- 모든 CSV에는 헤더 행이 있어야 합니다
- 원본 CSV는 수정하지 않습니다

## 핵심 실행 순서

1. 입력 인벤토리 생성
2. 입력 프로파일 작성
3. 질문 설계
4. 지표·핵심 분석
5. 시각화
6. 최종 보고

필요할 때만 아래를 추가합니다.

- 소스 정렬·집계 준비
- 시나리오·예측 패키지

## 필수 산출물

- `runs/latest/outputs/00_input_manifest.json`
- `runs/latest/outputs/00_file_briefs/*.json`
- `runs/latest/outputs/01_dataset_profile.md`
- `runs/latest/outputs/02_eda_report.md`
- `runs/latest/outputs/03_problem_definition.md`
- `runs/latest/outputs/04_kpi_summary.md`
- `runs/latest/outputs/05_analysis_report.md`
- `runs/latest/outputs/06_dashboard.html`
- `runs/latest/outputs/07_executive_report.md`

## 프로젝트 규약

- 입력 모드 정의와 강등 규칙은 `.claude/rules/input-contract.md` 만 따릅니다
- 관계가 애매하면 파일을 억지로 합치지 않습니다
- 교차 파일 KPI는 정합 조건이 맞을 때만 허용합니다
- 패널마다 근거 소스를 남깁니다
- 제목, 탭, 버튼 문구는 짧고 기능 중심으로 씁니다
