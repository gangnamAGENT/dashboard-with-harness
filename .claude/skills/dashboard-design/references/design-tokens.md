# 디자인 토큰과 구성 패턴

## 글꼴

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=Inter:wght@400;500;600;700&family=Space+Grotesk:wght@500;600;700&display=swap" rel="stylesheet">
```

## 루트 토큰

```css
:root {
  --bg-root: #110d1d;
  --bg-void: #150f23;
  --bg-elevated: #1f1633;
  --bg-panel: rgba(32, 22, 52, 0.82);
  --bg-panel-strong: rgba(42, 28, 68, 0.92);
  --line-soft: rgba(207, 207, 219, 0.14);
  --line-mid: rgba(207, 207, 219, 0.24);
  --glass-white: rgba(255, 255, 255, 0.08);
  --glass-violet: rgba(106, 95, 193, 0.12);
  --text-primary: #ffffff;
  --text-secondary: #d9d7e4;
  --text-muted: #9f97b4;
  --text-mono: #b8b2ca;
  --accent-violet: #6a5fc1;
  --accent-violet-deep: #422082;
  --accent-lime: #c2ef4e;
  --accent-coral: #ffb287;
  --state-good: #76e2b3;
  --state-warn: #ffcb8d;
  --state-bad: #ff8fae;
  --state-info: #96a6ff;
}
```

## 기본 구조

```text
페이지 셸
└─ 분석 탭
└─ 패널
   └─ 상단 헤드
   └─ 요약
   └─ 지표
   └─ 메인
   └─ 보조 영역
   └─ 결정표
   └─ 액션
```

## 구성 규칙

- 탭은 1~3개
- 스토리 칸은 4개 고정
- KPI 카드는 3~5개
- 메인은 큰 차트 1개 + 짧은 해석 영역
- 보조 카드는 2개 이하
- 표는 스크롤 가능하게
- 여러 소스일 때는 패널마다 소스 메타를 둔다

## 텍스트 규칙

- 페이지 키커는 1단어
- 탭 라벨은 2~4자
- 패널 제목은 2~8자
- 섹션 제목은 한 단어
- 버튼 제목은 2~6자
- 긴 설명은 제목 뒤가 아니라 본문에 둔다
- 소스 메타 예시는 `3개 파일`, `비교 2종`, `결합 2종`, `포트폴리오`

## SVG 규칙

- 배경은 투명
- 선과 경계는 `--line-soft`, `--line-mid`
- 메인 시리즈는 보라 또는 라임
- 경고선은 코랄 또는 앰버
- 범주 라벨은 8개를 넘기지 않음
- SVG 안 글꼴은 `Inter` 또는 `IBM Plex Mono`
