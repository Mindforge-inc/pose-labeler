# Pose 라벨링 툴

이미지에 바운딩 박스와 키포인트(스켈레톤)를 그려 Pose 추정 모델의 학습 데이터를 만드는 브라우저 기반 도구입니다.

기존 [data_labeler](https://github.com/sungeun-yoo/data_labeler) 저장소에서 박스 전용(Detection) 라벨러로 리팩토링되면서, Pose 라벨링 기능을 유지하기 위해 이 저장소로 분리되었습니다. 여기에 감마 보정 등 기능이 추가로 얹혀 있습니다.

**→ 사용법은 [사용자 가이드](docs/guide.md)를 참고하세요.**

---

## 주요 기능

- 바운딩 박스 + 키포인트(스켈레톤) 그리기 / 크기 조정 / 클래스 변경
- YOLO Pose, MF_YOLO, Sapiens 형식 라벨 저장/불러오기 및 JSON으로 저장
- 저장 ZIP을 풀어 라벨 폴더를 선택하면 작업 재개
- 감마 보정으로 어두운 이미지 라벨링 보조
- 숫자키(1~9)로 클래스 선택과 그리기 모드 동시 진입
- 클래스별 색상 커스터마이징 / 단축키 커스터마이징

## 빠른 시작

```bash
cd data_labeler
python -m http.server 8080
# 브라우저에서 http://localhost:8080 접속
```

> `index.html`을 직접 열면(`file://`) 동작하지 않습니다. 위 명령어로 실행하세요.

## Config 형식

클래스별 키포인트 라벨과 스켈레톤 연결 정보를 담은 JSON 객체를 사용합니다. 키포인트가 없는 클래스는 `labels`/`skeleton`을 빈 배열로 둡니다.

```json
{
  "person": {
    "labels": ["nose", "left_eye", "right_eye", "..."],
    "skeleton": [[0, 1], [0, 2]]
  },
  "vehicle": {
    "labels": [],
    "skeleton": []
  }
}
```

`config/` 폴더에 예제 파일들이 있습니다.

## 출력 구조

```
annotations_YYYY-MM-DD.zip
├── image1.json   ← 전체 어노테이션 (작업 재개용)
├── image1.txt    ← YOLO Pose / MF_YOLO 학습용 TXT (객체 있는 이미지만)
└── ...
```

YOLO Pose TXT 형식: `class_idx x_center y_center width height x1 y1 v1 x2 y2 v2 ...` (좌표 정규화)

## 프로젝트 구조

```
data_labeler/
├── index.html
├── styles.css
├── src/
│   ├── main.js            # 진입점
│   ├── state.js           # 전역 상태
│   ├── canvas.js          # 캔버스 렌더링
│   ├── events.js          # 이벤트 처리
│   ├── ui.js              # UI 렌더링
│   ├── file.js            # 파일 입출력
│   ├── dataExporter.js    # YOLO Pose/MF_YOLO/JSON 내보내기
│   ├── modal.js           # 모달(라벨 포맷, Sapiens 결과 등)
│   ├── colorManager.js    # 클래스별 색상 관리
│   ├── shortcutManager.js # 단축키 커스터마이징
│   ├── shortcuts.js       # 기본 단축키 정의
│   └── utils.js
├── config/                # 예제 Config 파일
└── docs/
    └── guide.md           # 사용자 가이드
```
