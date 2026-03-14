# Obsidian 템플릿 묶음

## 추천 폴더 구조

```text
00 Inbox/
10 Projects/
20 Knowledge/
  Computer Science/
  Mathematics/
  Cryptography/
  Coding Theory/
30 Papers/
40 Experiments/
50 Areas/
60 Journal/
  Daily/
70 MOCs/
80 Templates/
90 Archive/
Attachments/
```

## 50 Areas에는 무엇을 넣는가

`50 Areas/`는 장기적으로 관리하는 삶의 영역, 역할, 반복 활동, 세미나 이력, 연구 커뮤니티 활동 등을 둔다.

예시:

- `50 Areas/Research/`
- `50 Areas/Programming/`
- `50 Areas/Seminars/`
- `50 Areas/Teaching/`
- `50 Areas/Career/`

여기에는 끝이 있는 단기 과제보다, 오래 유지되는 관심사와 책임, 반복 활동을 둔다.

## 템플릿 사용 원칙

- 구조가 정해지지 않은 새 노트는 `00-default.md`
- 개념 정리는 `concept.md`
- 논문 읽기 노트는 `paper.md`
- 종료 시점이 있는 일은 `project.md`
- 실험 로그는 `experiment.md`
- 회의나 대화 기록은 `meeting.md`
- 번뜩인 발상이나 정제 전 제안은 `idea.md`
- 문제 풀이 기록은 `problem.md`
- 허브 문서는 `moc.md`
- 일일 기록은 `daily.md`
- 질문성 문서는 `00-default.md`로 만들고 `tags: kind/question`을 붙인다

## 필드 의미

- `type`: 노트의 정체성. 허용값: `note`, `concept`, `paper`, `project`, `experiment`, `idea`, `meeting`, `problem`, `moc`, `daily`
- `status`: 작업 상태. 허용값: `inbox`, `draft`, `review`, `stable`, `archive`
- `aliases`: 다른 제목, 약어, 한글/영문 표기
- `domain`: 큰 지식 분야 링크. 예: `[[Cryptography]]`, `[[Mathematics]]`
- `parent`: 직접 상위 개념 또는 상위 허브 노트
- `related`: 함께 연결해둘 가치가 있는 관련 노트
- `source`: 논문, 책, 강의, URL, 메모 출처
- `project`: 연결된 프로젝트 노트
- `area`: 장기적 관심 영역
- `prefer-view`: Current View가 참고하는 frontmatter 값. MOC는 보통 `reading`, 일반 작업 노트는 `live`
- `tags`: 보조 분류용 태그. `area/*`, `topic/*`, `kind/*`, `priority/*` 정도만 사용

## 추천 태그 형식

- `area/cryptography`
- `area/programming`
- `area/mathematics`
- `topic/hqc`
- `topic/reed-muller`
- `kind/seminar`
- `kind/troubleshooting`
- `kind/reading-note`
- `kind/question`
- `kind/question-origin`

## 질문 노트 처리 기준

질문은 `type`이 아니라 `tags`로 처리한다. 질문은 대체로 영구적인 문서 형식이라기보다 정리 전 단계인 경우가 많기 때문이다.

새 질문 노트:

```yaml
type: note
status: inbox
tags:
  - kind/question
```

정리가 끝나서 개념 문서로 승격되면:

```yaml
type: concept
status: review
tags:
  - kind/question-origin
```
