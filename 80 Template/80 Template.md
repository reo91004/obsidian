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
- 질문성 문서는 `00-default.md`로 만들고 정리 후 `concept`으로 승격한다

## 필드 의미

- `type`: 노트의 정체성. 허용값: `note`, `concept`, `paper`, `project`, `experiment`, `idea`, `meeting`, `problem`, `moc`, `daily`
- `status`: 완성도. 허용값: `draft`, `stable`
- `context`: 소속 맥락. 허용값: `lab`, `company`, `personal`
- `aliases`: 다른 제목, 약어, 한글/영문 표기
- `source`: 논문, 책, 강의, URL, 메모 출처
- `prefer-view`: 보기 모드 힌트. MOC는 `read`, 일반 작업 노트는 `edit`
- `tags`: 교차 주제 태그. `topic/*`만 사용

## 추천 태그 형식

- `topic/hqc`
- `topic/pqc`
- `topic/rs-code`
- `topic/rm-code`
- `topic/finite-field`
- `topic/side-channel`

## 질문 노트 처리 기준

질문은 `type: note`로 만들고, 정리가 끝나면 `type: concept`으로 승격한다.

새 질문 노트:

```yaml
type: note
status: draft
```

정리가 끝나서 개념 문서로 승격되면:

```yaml
type: concept
status: stable
```
