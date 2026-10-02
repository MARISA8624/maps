# 퍼스트 페러다이스 지도

세계관 "퍼스트 페러다이스"의 지도입니다. 여섯 대륙, 큰 나라 12개와 작은 나라 20개, 던전, 유적, 자연 지형을 한 장의 SVG로 그리고, 끌어서 옮기거나 확대해서 볼 수 있습니다.

## 파일

| 파일 | 내용 |
|---|---|
| `index.html` | 지도 전체 (HTML + CSS + JavaScript가 한 파일에 들어 있음). 외부 라이브러리 없음 |
| `data.js` | 표식과 대륙의 배치 데이터. `window.FP_DATA = { M: 표식, C: 대륙 }` |

## 실행

- `index.html`을 브라우저로 열면 됩니다. 빌드 과정이 없습니다.
- GitHub Pages로 올리려면 저장소 설정의 Pages에서 이 폴더가 있는 브랜치를 고르면 됩니다.
- 글꼴은 Google Fonts에서 불러옵니다. 인터넷이 없으면 기본 글꼴로 보입니다.

## 저장 방식

- 이 지도는 원래 Claude 아티팩트로 만들었고, 거기서는 아티팩트의 공유 데이터베이스에 저장합니다.
- 아티팩트 밖(이 저장소)에서는 **보는 사람의 브라우저(localStorage)에만** 저장됩니다. 편집해도 `data.js`는 바뀌지 않습니다.
- 한 번이라도 편집한 브라우저는 그 뒤로 자기 저장본을 씁니다. `data.js`의 배치로 돌아가려면 브라우저 개발자 도구에서 `localStorage.removeItem("fp-map-data-v1")`을 실행하고 새로 고칩니다.
- 편집한 배치를 저장소에 반영하려면 개발자 도구에서 `copy(localStorage.getItem("fp-map-data-v1"))`로 복사한 뒤 `data.js`의 `window.FP_DATA=` 뒤에 붙여 넣습니다.

## 데이터 형식

좌표는 가로 10000 × 세로 7600의 지도 좌표입니다.

표식(`M`)의 주요 필드:

| 필드 | 뜻 |
|---|---|
| `kind` | 종류. `nation` 큰 나라, `small` 작은 나라, `region` 지방, `capital` 수도, `city` 도시, `village` 마을, `apex` 최상위 던전, `dungeon` 일반 던전, `ruin` 유적, `nature` 산지·숲·강, `shelter` 악마 대피소, `place` 지명, `sea` 바다 이름 |
| `name`, `note` | 이름과 설명 |
| `x`, `y`, `sc` | 위치. `sc: 5`는 현재 좌표 배율 |
| `of` | 소속 나라의 id (지방·수도·도시·마을·작은 나라) |
| `grade`, `icon`, `rank` | 던전의 등급, 아이콘, 위험 순위 |
| `tt`, `zr`, `zrot` | 자연 지형의 종류(`hill` `forest` `river` `peak` `range` `ice` `frozen`), 크기, 방향 |
| `tsh`, `tr`, `trot`, `tasp`, `tcol`, `tb` | 나라 영토의 모양, 크기, 방향, 가로세로 비, 색, 국경 여유 |

## 코드 구조 (`index.html` 안)

- 위쪽 `<style>`: 색 변수(밝은 화면·어두운 화면), 지도와 패널의 모양
- `<svg id="map">`: `#world`(지도 좌표로 그리는 층)와 `#ov`(화면 좌표로 그리는 이름표·아이콘 층)
- `<script>`
  - `KIND`, `ICONS`, `TERR`: 표식 종류, 던전 아이콘, 나라별 영토 모양
  - `terrShape()`: 나라 영토의 모양과 효과 (태양, 성운, 다이아몬드 장벽, 강, 벚꽃 등)
  - `drawTerritories()`, `drawBorders()`, `drawRegions()`: 영토, 국경선, 지방 경계
  - `drawNature()`, `drawMountains()`, `drawZones()`, `drawSea()`: 자연 지형, 눈 산맥, 던전 분위기, 바다 효과
  - `renderOv()`: 확대 정도에 맞춰 이름표와 아이콘을 배치
  - `schedule()`, `cull()`: 다시 그리기 예약, 화면 밖 숨기기
  - `initStore()`, `startLocal()`: 저장소 연결 (아티팩트 데이터베이스 또는 localStorage)
