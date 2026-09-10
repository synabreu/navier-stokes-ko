# navier-stokes-ko

OpenAI가 공개한 *Finite time blowup for Navier-Stokes* 원본 PDF와 관련 발표문을 Codex를 이용해 한국어로 번역·정리한 저장소이다.

## 파일 안내

| 파일 | 설명 |
| --- | --- |
| [`README.md`](./README.md) | 저장소의 목적, 파일 구성, 원문 링크를 안내하는 문서이다. |
| [`navier-stokes-ko-complete.md`](./navier-stokes-ko-complete.md) | 166쪽 분량의 증명 논문 전체를 한국어 평서체로 옮긴 Markdown 번역본이다. 원문의 절, 정리, 보조정리, 식 번호와 수식을 포함하며 수식 이미지는 문서 안에 내장되어 있다. |
| [`navier-stokes-ko-complete.pdf`](./navier-stokes-ko-complete.pdf) | 전체 한국어 번역본을 읽기와 다운로드에 편한 PDF 형식으로 만든 파일이다. Markdown 완성본과 같은 내용을 담고 있다. |
| [`openai-navier-stokes-korean-summary-v2.1.md`](./openai-navier-stokes-korean-summary-v2.1.md) | OpenAI 발표의 핵심 주장, 연구 과정, 주요 수치와 의미를 쉽게 읽을 수 있도록 정리한 한국어 상세 요약 v2.1이다. 공식 번역문은 아니다. |
| [`figure-1-vortex-core.png`](./figure-1-vortex-core.png) | 특이점 발생 시각에 가까워질수록 수축하고 강해지는 소용돌이 핵심부를 설명하는 그림이다. v2.1 요약문에 사용된다. |
| [`figure-2-pulse-geometry.png`](./figure-2-pulse-geometry.png) | 고리형 펄스의 배치와 반지름·방위각·축 방향 섭동 속도를 설명하는 그림이다. v2.1 요약문에 사용된다. |
| [`ns-proof-explainer-v2.mp4`](./ns-proof-explainer-v2.mp4) | 방정식, 와도 늘어남, 다중 스케일 펄스, 유한시간 캐스케이드, 오차 추정과 증명 사슬을 94초로 설명하는 1280×720 무음 모션그래픽이다. |
| [`ns-proof-explainer-v2-notes.md`](./ns-proof-explainer-v2-notes.md) | 영상의 핵심 수식, 장면별 해설, 출처, 증명 범위와 한계를 정리한 문서이다. |
| [`ns-proof-explainer-v2-source.zip`](./ns-proof-explainer-v2-source.zip) | Pillow와 FFmpeg로 영상을 다시 만들 수 있는 Python 소스, 요구사항, 실행 안내를 담은 압축 파일이다. |

## 증명 해설 영상 v2

영상은 3차원 비압축성 나비에–스토크스 방정식에서 와도 구조가 단계적으로 더 작은 공간척도로 집중되는 구성, 각 단계의 주효과가 오차항보다 우세하도록 추정하는 과정, 그리고 초기 데이터에서 특이점 결론까지 이어지는 논리 의존 관계를 시각화한다.

이 영상은 전체 논문을 대체하는 증명이 아니다. 공개된 논문의 주장을 이해하기 위한 교육용 로드맵이며, 독립적인 수학 검토나 밀레니엄 문제의 공식 수용 여부를 확정하지 않는다. 정확한 가정과 추정은 원문 및 저장소의 전체 번역본을 확인해야 한다.

## 영상 재생성

```bash
unzip ns-proof-explainer-v2-source.zip -d ns-proof-explainer-v2-source
cd ns-proof-explainer-v2-source
python3 -m pip install -r requirements.txt
python3 generate_video.py
```

Python 3.10 이상, Pillow, FFmpeg, DejaVu Sans 글꼴이 필요하다. 생성 결과는 압축을 푼 디렉터리의 상위 경로에 저장된다.

## 원문

- 증명 논문: [OpenAI, *Finite time blowup for Navier-Stokes*](https://cdn.openai.com/pdf/32d9f210-8b73-45e0-91bc-82a30aef8a9a/navier-stokes.pdf)
- 발표문: [On the Navier–Stokes Millennium Prize Problem](https://openai.com/index/navier-stokes-solution/)

## 번역 기준

번역문은 원문의 구조와 수식을 보존하고 본문을 한국어 평서체로 옮겼다. 별도의 해설이나 평가를 덧붙이지 않았다.

## 안내

이 저장소의 번역, 요약과 영상은 참고용이며 공식 번역이나 공식 검증 자료가 아니다. 수학적 내용의 정확한 해석과 인용에는 원문을 함께 확인해야 한다.
