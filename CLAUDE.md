# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

> 이 저장소는 한국어 프로젝트입니다. 사이트 콘텐츠, 커밋 메시지, 사용자 대상 문구는 모두 한국어로 작성합니다.

## 프로젝트 개요

Camera HAL 파트의 **2026년 AI 스터디 계획**을 안내하는 정적 웹사이트입니다. 빌드 도구 · 의존성 · 프레임워크가 전혀 없는 순수 HTML + 단일 공유 CSS + 인라인 vanilla JS 구성이며, GitHub Pages가 `docs/` 폴더를 그대로 서빙합니다.

세 콘텐츠 페이지가 하나의 순차적 학습 흐름(STEP 1 → 2 → 3)을 이룹니다.

- `docs/index.html` — 랜딩/개요, 세 페이지로 진입하는 허브
- `docs/pages/practical_ai_study.html` — STEP 1. AI Agent 실전 학습(원칙 · 모듈 · 회차 운영)
- `docs/pages/practical_ai_usecases.html` — STEP 2. 현업 AI Use Cases. **유일하게 인라인 JS를 가진 페이지** (아래 참고)
- `docs/pages/ai_expert_prep_course.html` — STEP 3. AI Expert 시험 대비 커리큘럼
- `docs/css/styles.css` — 모든 페이지가 공유하는 단일 스타일시트
- `docs/images/mascot.png`
- `.github/ISSUE_TEMPLATE/ai-item-request.yml` — "AI Item 추가 요청" GitHub 이슈 폼
- `.github/ISSUE_TEMPLATE/config.yml` — 빈 이슈 비활성화 + 라이브 페이지 링크

## 개발 · 미리보기 · 배포

빌드/린트/테스트 명령이 없습니다. **HTML/CSS를 직접 편집**하고 브라우저로 확인합니다.

```bash
# 로컬 미리보기 (정적 서버 중 택1)
python -m http.server 8000 --directory docs   # http://localhost:8000
npx serve docs

# 배포: main 브랜치에 push하면 GitHub Pages가 docs/를 자동 서빙
```

상대 경로 주의: 루트 페이지는 `css/styles.css`, `docs/pages/` 하위 페이지는 `../css/styles.css`를 참조합니다. 페이지 간 이동도 상대 경로(`../index.html`)입니다.

## 반드시 알아야 할 구조

### 1. AI 아이템은 한 페이지 안 3곳 + MBTI 데이터에 중복 정의됨

`practical_ai_usecases.html`의 18개 AI 아이템은 서로 다른 4곳에 손으로 동기화되어 있습니다. 아이템을 **추가 · 이름변경 · 삭제할 때는 네 곳을 모두 함께 수정**해야 합니다.

1. `#categories` 섹션의 카테고리 카드 요약(`.category-card` 안 `.chip`) — 카테고리별 개수도 함께 갱신
2. `#all-items` 섹션의 전체 목록(`.compact-card`, `Item NN` 배지 + 요약/MVP/입출력)
3. `#evaluation` 섹션의 평가표 행(`.score-table tbody tr`, `data-item-id="item-NN"`, `.item-name`, `.item-category`)
4. 인라인 JS의 `MBTI_RECOMMENDATIONS` 객체 — 각 MBTI의 `items` 배열이 **아이템 이름 문자열로** 추천을 참조 (이름 변경 시 깨짐)

### 2. 평가표 점수는 localStorage에 저장됨 — 키 안정성이 중요

- 점수는 브라우저별 `localStorage`에 저장(접두사 `camera_hal_ai_study_eval_v1_`).
- **안정 키는 행의 `data-item-id`** 이고, 과거 호환을 위해 `.item-name`(아이템 이름) 기반 레거시 키 폴백이 있습니다.
- 따라서 평가표 행을 추가할 때 `data-item-id`(예: `item-19`)를 반드시 부여하세요. `.item-name` 텍스트만 바꾸면 레거시 폴백 경로의 저장값이 어긋날 수 있습니다.
- CSV 내보내기는 UTF-8 BOM(`﻿`)을 붙여 Excel 한글 깨짐을 방지합니다.

### 3. CSS는 `:root` 커스텀 프로퍼티 기반 수제 디자인 시스템

`styles.css` 최상단 `:root`에 색상(`--blue`, `--red`, `--mint`, `--yellow`, `--purple` 등) · radius · shadow 토큰이 정의되어 있습니다. 새 색/간격을 하드코딩하지 말고 토큰을 사용하세요. 칩 색상은 수정자 클래스(`.chip.red`, `.chip.mint`, `.chip.yellow`, `.chip.purple`)로 지정합니다.

### 4. 네비게이션은 페이지마다 수동 관리

각 페이지가 자체 `.topnav`를 가지며, 현재 페이지 링크에 `class="active"` + `aria-current="page"`를 답니다. 페이지를 추가/이름변경하면 모든 페이지의 nav를 함께 고쳐야 합니다.

### 5. 저장소 · 발행 위치

- 로컬/원격 저장소: `TTolsun/camera-hal-ai-study`
- 라이브 사이트: `https://ttolsun.github.io/camera-hal-ai-study/`

`config.yml`의 contact URL과 usecases 페이지의 "AI Item 제안하기" 버튼 링크(`github.com/TTolsun/camera-hal-ai-study/issues/new?template=ai-item-request.yml`)는 이 저장소를 가리킵니다. 과거 이름은 `AI_Study_Plan`이었으니, 잔여 참조(`ttolsun.github.io/AI_Study_Plan`, `github.com/TTolsun/AI_Study_Plan`)가 보이면 모두 `camera-hal-ai-study`로 갱신하세요.

## 콘텐츠 규약

- 모든 페이지는 `lang="ko"`, 콘텐츠는 한국어. 기술 용어 · 코드 식별자(Camera HAL, MVP, JIRA, Gerrit 등)는 원문 유지.
- 푸터는 전 페이지 공통: `Camera HAL Part AI Study · 2026 · Designed & Built by KH`
- 새 AI 아이템의 카테고리는 이슈 폼(`ai-item-request.yml`)의 dropdown 옵션과 usecases 페이지의 카테고리 집합을 따릅니다.
