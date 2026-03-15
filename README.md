---
type: note
status: stable
context:
created: 2026-03-14T01:24
updated: 2026-03-15T19:51
aliases:
source:
prefer-view: read
tags:
---

# Obsidian Vault 운영 가이드

이 문서는 이 Vault를 오래, 일관되게, 덜 헷갈리게 사용하기 위한 운영 기준이다.

핵심 원칙은 단순하다.

- 폴더는 **보관 위치와 작업 흐름**을 위한 구조로만 사용한다.
- 링크는 **개념 관계와 지식 구조**를 표현하는 주된 도구로 사용한다.
- `type`, `status`, `context`는 **표준 분류 필드**로 고정해서 사용한다.
- 태그는 폴더 경계를 횡단하는 **교차 주제(topic)** 분류에만 사용한다.
- YAML properties는 **구조화된 메타데이터**를 담는 표준 필드로 사용한다.
- `prefer-view`는 템플릿의 기본 속성으로 넣되, 설명용 분류가 아니라 **보기 모드 힌트**로만 사용한다.

---

# 1. 이 Vault의 철학

이 Vault는 프로그래밍, 연구, 수학 공부, 논문 정리, 개념 정리, 트러블슈팅 기록을 모두 함께 담는 지식 저장소다.

이런 종류의 Vault는 폴더를 깊게 파는 순간 금방 무거워진다. 예를 들어 `Cryptography/HQC/Coding Theory/Reed-Muller/...`처럼 주제를 폴더로 끝없이 쪼개면, 노트를 쓰기도 전에 어디에 저장해야 하는지부터 고민하게 된다.

그래서 이 Vault는 **폴더보다 링크를 더 신뢰한다.**

예를 들어 `HQC`, `Reed-Solomon Code`, `Reed-Muller Code`, `Syndrome Decoding`, `Finite Field`는 서로 상위/하위/관련 관계를 맺지만, 그 관계는 폴더보다 `[[내부 링크]]`로 표현하는 편이 훨씬 유연하다.

---

# 2. 폴더 구조

권장 폴더 구조는 아래와 같다.

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
  Think/
  Seminars/
60 Journal/
  Daily/
70 MOCs/
80 Templates/
90 Archive/
Attachments/
```

## 2.1 각 폴더의 의미

### `00 Inbox/`

아직 제대로 정리하지 않은 임시 노트를 두는 곳이다.

### `10 Projects/`

끝이 있는 작업을 둔다.

예시:

- HQC 구현 실험
- 논문 재현 프로젝트
- 개인 앱 개발
- 세미나 준비
- 연구 과제별 작업 노트

### `20 Knowledge/`

오래 살아남는 개념 지식을 둔다.

예시:

- `HQC`
- `Reed-Solomon Code`
- `Reed-Muller Code`
- `Finite Field`
- `Syndrome Decoding`
- `Concurrency`
- `Memory Safety`
- `FFT`

### `30 Papers/`

논문별 읽기 노트를 둔다.

### `40 Experiments/`

실험 로그와 결과 기록을 둔다.

### `50 Areas/`

장기적으로 관리하는 관심 영역과 활동을 묶는다.

예시:

- 세미나 발표 준비 및 기록
- 사고 정리 (Think)
- 스터디 그룹 활동

### `60 Journal/Daily/`

그날그날의 작업 로그와 배운 점을 적는다.

### `70 MOCs/`

Map of Contents 허브 노트를 둔다.

### `80 Templates/`

새 노트를 생성할 때 사용할 템플릿을 둔다.

### `90 Archive/`

더 이상 자주 보지 않지만 보관할 가치가 있는 노트를 옮긴다.

### `Attachments/`

이미지, PDF, 스크린샷, 기타 첨부파일을 둔다.

---

# 3. 폴더 운영 규칙

## 3.1 폴더는 얕게 유지한다

폴더는 너무 깊게 만들지 않는다.

좋은 예:

```text
20 Knowledge/
  Cryptography/
    HQC.md
    Code-based Cryptography.md
  Coding Theory/
    Reed-Solomon Code.md
    Reed-Muller Code.md
```

피해야 할 예:

```text
20 Knowledge/
  Mathematics/
    Algebra/
      Finite Fields/
        Applications/
          Coding Theory/
            PQC/
              HQC/
```

## 3.2 하위 폴더는 프로젝트 안에서만 제한적으로 사용한다

프로젝트 폴더 안에서는 하위 폴더를 써도 괜찮다.

예시:

```text
10 Projects/
  HQC-Implementation/
    meetings/
    logs/
    tasks/
    references/
```

## 3.3 주제 계층은 폴더가 아니라 링크로 표현한다

예를 들어 `HQC`는 아래 모든 것과 연결된다.

- `[[Code-based Cryptography]]`
- `[[Post-Quantum Cryptography]]`
- `[[Reed-Solomon Code]]`
- `[[Reed-Muller Code]]`
- `[[Syndrome Decoding]]`
- `[[Quasi-Cyclic Code]]`

---

# 4. 링크 규칙

링크는 이 Vault의 핵심이다.

## 4.1 언제 링크를 쓰는가

다음 중 하나라도 해당하면 태그보다 링크를 우선한다.

- 나중에 설명 노트가 될 수 있는 개념
- 다른 노트들이 많이 참조할 가능성이 있는 주제
- 상위/하위/관련 관계가 중요한 대상
- 사람, 기술, 이론, 알고리즘, 수학 개념, 스킴 이름

예시:

- `[[HQC]]`
- `[[Reed-Solomon Code]]`
- `[[Reed-Muller Code]]`
- `[[Finite Field]]`
- `[[Syndrome Decoding]]`
- `[[Memory Safety]]`
- `[[Transformer]]`
- `[[Rust]]`

## 4.2 링크 섹션 표준

개념 노트에는 가능하면 아래 섹션을 유지한다.

- 상위 개념
- 하위 개념
- 관련 개념
- 같이 볼 것

## 4.3 MOC를 적극 사용한다

MOC는 링크 허브다. 폴더보다 유연하다.

예시:

```md
# Coding Theory MOC

## 기초

- [[Error-Correcting Codes]]
- [[Generator Matrix]]
- [[Parity-Check Matrix]]

## 주요 코드

- [[Reed-Solomon Code]]
- [[Reed-Muller Code]]

## 암호와의 연결

- [[Syndrome Decoding]]
- [[Quasi-Cyclic Code]]
- [[HQC]]
```

## 4.4 링크 이름 규칙

노트 제목은 가능하면 아래 원칙을 따른다.

- 개념은 단수형, 대표 명칭 사용
- 약어보다 풀네임 우선, 약어는 alias로 처리
- 영어/한글 혼용 시 자주 쓰는 표기를 제목으로, 나머지는 alias로 처리

---

# 5. `type`, `status`, `tags`의 차이

이 세 개는 역할이 다르다.

## 5.1 `type`

`type`은 **이 노트가 무엇인지**를 나타내는 고정 분류다.

이 노트를 나중에 어떻게 읽고, 어떻게 다시 쓸 것인가에 따라 결정한다.

- `note` — 위 어디에도 딱 맞지 않는 일반 노트
- `concept` — 설명하고 정리할 문서
- `paper` — 특정 논문 1편을 읽은 기록
- `project` — 해야 할 일과 목표가 있는 작업
- `experiment` — 결과와 설정을 남기는 실험 기록
- `idea` — 번뜩인 발상이나 아직 정제 안 된 제안
- `meeting` — 회의나 대화 기록
- `problem` — 문제 풀이 기록
- `moc` — 여러 노트를 묶는 허브
- `daily` — 일일 기록

즉, `type`은 노트의 **본질적인 종류**다. 한 노트에는 보통 하나만 들어간다.

예를 들어:

- `HQC.md`는 `type: concept`
- `OT-PCA 논문 정리.md`는 `type: paper`
- `260313 삼성.md`는 `type: meeting`

## 5.2 `status`

`status`는 **이 노트가 얼마나 정리되었는지**를 나타내는 고정 분류다.

허용값:

- `draft` — 작성 중이거나 아직 미완인 문서
- `stable` — 충분히 정리되어 완성된 문서

즉, `status`는 노트의 **완성도**다. 2개 값만 사용한다.

예를 들어:

- 방금 만든 노트는 `status: draft`
- 충분히 다듬어졌으면 `status: stable`

아카이브 상태는 `90 Archive/` 폴더가 이미 표현하므로 별도 status 값이 불필요하다.

## 5.3 `context`

`context`는 **이 노트가 어떤 소속/맥락에서 작성되었는지**를 나타내는 분류다.

허용값:

- `lab` — 연구실 관련
- `company` — 회사/산학 관련
- `personal` — 개인 공부/프로젝트

폴더는 주제별 구조이고, `context`는 조직별 맥락을 분리한다. 하나의 노트가 여러 맥락에 속할 수 있으므로 폴더가 아닌 property로 관리한다.

## 5.4 `tags`

`tags`는 **폴더 경계를 횡단하는 교차 주제**를 표현할 때만 쓰는 보조 분류다.

태그의 핵심 원칙:

- **`topic/` 접두사만 사용한다** — 폴더나 다른 property와 중복되지 않는 교차 주제만 태그로 관리한다.
- **`area/` 태그는 사용하지 않는다** — 폴더 구조(`20 Knowledge/Cryptography/` 등)가 이미 분야를 표현한다.
- **`kind/` 태그는 사용하지 않는다** — `type` property가 이미 노트의 성격을 표현한다.

태그로 적합한 예:

- `topic/hqc` — Cryptography, Coding Theory, Papers 등 여러 폴더에 걸침
- `topic/pqc` — 여러 분야를 관통하는 주제
- `topic/rs-code` — 교차 주제

태그에 넣지 않을 것:

- 폴더가 이미 표현하는 분야 (area/cryptography 등)
- `type`이 이미 표현하는 성격 (kind/reading-note 등)
- `type`, `status`, `context` 값의 중복

좋은 예:

```yaml
---
type: concept
status: draft
context: lab
tags:
  - topic/hqc
---
```

피해야 할 예:

```yaml
---
type: concept
status: draft
context: lab
tags:
  - area/cryptography
  - kind/reading-note
  - topic/hqc
---
```

---

# 6. 표준 태그 목록

이 Vault에서 태그는 `topic/` 접두사만 사용한다. 폴더 경계를 횡단하는 교차 주제만 태그로 관리한다.

```text
topic/hqc
topic/pqc
topic/rs-code
topic/rm-code
topic/finite-field
topic/syndrome-decoding
topic/quasi-cyclic
topic/side-channel
```

모두를 항상 다 쓰라는 뜻은 아니다. 보통 한 노트에 0~2개 정도면 충분하다. 필요에 따라 새로운 `topic/` 태그를 추가할 수 있다.

---

# 7. YAML properties 표준

이 Vault의 표준 frontmatter는 아래 구조를 따른다.

```yaml
---
type:
status:
context:
created:
updated:
aliases:
source:
prefer-view:
tags:
---
```

## 7.1 각 속성의 의미와 허용값

### `type`

노트의 유형을 나타낸다.

허용값:

- `note`
- `concept`
- `paper`
- `project`
- `experiment`
- `idea`
- `meeting`
- `problem`
- `moc`
- `daily`

### `status`

노트의 완성도를 나타낸다.

허용값:

- `draft` — 작성 중이거나 미완
- `stable` — 충분히 정리됨

### `context`

노트의 소속 맥락을 나타낸다.

허용값:

- `lab` — 연구실
- `company` — 회사/산학
- `personal` — 개인

### `created`

노트 생성 날짜.

형식:

- `YYYY-MM-DD`

### `updated`

마지막 실질 수정 날짜.

형식:

- `YYYY-MM-DD`

### `aliases`

노트의 별칭 목록.

사용 예:

- 약어
- 한국어/영어 다른 표기
- 자주 쓰는 변형

### `source`

외부 출처 또는 참고 자료.

형태 예시:

- 논문 제목
- 책 제목
- 명세 문서명
- URL

### `prefer-view`

노트의 기본 보기 모드 힌트. Set View Mode per Note 플러그인이 사용한다.

허용값:

- `read`
- `edit`

### `tags`

교차 주제 분류용 태그 목록.

권장 접두:

- `topic/` — 폴더 경계를 횡단하는 주제만 사용

## 7.2 노트 간 관계는 본문에서 표현한다

상위 개념, 관련 개념, 하위 개념 등 노트 사이의 관계는 frontmatter가 아니라 **본문의 링크 섹션**에서 표현한다.

이유:

- Obsidian의 그래프와 백링크는 링크가 frontmatter에 있든 본문에 있든 동일하게 작동한다.
- 본문에 적으면 내용과 함께 보이므로 맥락을 파악하기 쉽다.
- 한 곳에서만 관리하면 중복 유지보수가 사라진다.

즉, 아래처럼 사용한다.

```md
## 상위 개념

- [[Cryptography]]
- [[Coding Theory]]

## 관련 개념

- [[Reed-Solomon Code]]
- [[HQC]]

## 같이 볼 것

- [[Syndrome Decoding]]
```

## 7.3 최소 권장 속성

모든 노트에 모든 속성을 강제하지 않는다. 최소 권장 세트는 아래다.

```yaml
---
type:
status:
context:
created:
updated:
prefer-view:
tags:
---
```

---

# 8. 노트 생성 규칙

## 8.1 새 노트는 기본적으로 Inbox에서 시작한다

새 생각이 떠오르면 일단 `00 Inbox/`에 만든다.

그다음 아래 중 하나로 옮긴다.

- 오래 살아남는 개념이면 `20 Knowledge/`
- 프로젝트 관련이면 `10 Projects/`
- 논문 요약이면 `30 Papers/`
- 실험이면 `40 Experiments/`
- 관심 영역이나 활동 기록이면 `50 Areas/`

## 8.2 질문성 문서 처리

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

## 8.3 아이디어는 idea → concept로 승격시킨다

처음엔 `idea` 노트로 적어도 괜찮다. 이후 이해가 정리되면 개념 노트로 승격한다.

## 8.3 논문 노트와 개념 노트는 분리한다

예를 들어 `HQC Specifications`를 읽으며 정리한 문서는 `paper` 노트다.

반면 `HQC` 자체를 설명하는 문서는 `concept` 노트다.

---

# 9. 템플릿 운영 기준

템플릿은 너무 많이 만들지 않는다. 기본은 아래로 충분하다.

- `00-default` — 일반 노트 (`note`), 질문 노트 포함
- `concept` — 개념 정리
- `paper` — 논문 읽기
- `project` — 프로젝트
- `experiment` — 실험 기록
- `idea` — 아이디어, 정제 전 제안
- `meeting` — 회의/대화 기록
- `problem` — 문제 풀이
- `moc` — 허브 문서
- `daily` — 일일 기록

---

# 10. 템플릿 예시

## 10.1 concept 템플릿

```md
---
type: concept
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
source:
prefer-view: edit
tags:
---

# {{title}}

## 한 줄 정의

## 왜 중요한가

## 핵심 직관

## 형식적 정의 / 수식

## 예시

## 자주 헷갈리는 점

## 상위 개념

-

## 하위 개념

-

## 관련 개념

-

## 같이 볼 것

- [[]]
```

## 10.2 paper 템플릿

```md
---
type: paper
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
source:
prefer-view: edit
tags:
---

# {{title}}

## 서지 정보

## 한 줄 요약

## 문제 설정

## 핵심 아이디어

## 방법

## 장점

## 한계

## 내가 배운 점

## 연결되는 개념

- [[]]

## 나중에 다시 볼 질문

- [ ]
```

## 10.3 project 템플릿

```md
---
type: project
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
source:
prefer-view: edit
tags:
---

# {{title}}

## 목표

## 범위

## 현재 상태

## 해야 할 일

- [ ]

## 관련 노트

- [[]]

## 참고 자료

-
```

## 10.4 meeting 템플릿

```md
---
type: meeting
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
prefer-view: edit
tags:
---

# {{title}}

## 참석자

## 논의 내용

## 결정 사항

## 다음 행동

- [ ]

## 연결할 노트

- [[]]
```

## 10.5 idea 템플릿

```md
---
type: idea
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
source:
prefer-view: edit
tags:
---

# {{title}}

## 핵심 아이디어

## 왜 이게 떠올랐는가

## 현재 생각

## 검증할 점

## 연결할 개념

- [[]]

## 다음 행동

- [ ]
```

## 10.6 problem 템플릿

```md
---
type: problem
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
source:
prefer-view: edit
tags:
---

# {{title}}

## 문제

## 접근

## 풀이

## 배운 점

## 관련 노트

- [[]]
```

## 10.7 daily 템플릿

```md
---
type: daily
status: draft
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
prefer-view: edit
tags:
---

# {{date:YYYY-MM-DD}}

## 오늘 한 일

## 배운 것

-

## 생긴 질문

-

## 진행 중인 프로젝트 / 활동

## 연결할 노트

- [[]]
```

## 10.8 moc 템플릿

```md
---
type: moc
status: stable
context:
created: "{{date:YYYY-MM-DD}}"
updated: "{{date:YYYY-MM-DD}}"
aliases:
prefer-view: read
tags:
---

# {{title}}

## 핵심 개념

- [[]]

## 하위 주제

- [[]]

## 관련 프로젝트

- [[]]

## 관련 문헌

- [[]]
```

---

# 11. 실제 예시

## 11.1 HQC 개념 노트 예시

```md
---
type: concept
status: draft
context: lab
created: 2026-03-14
updated: 2026-03-14
aliases:
  - Hamming Quasi-Cyclic
source:
prefer-view: edit
tags:
  - topic/hqc
---

# HQC

## 한 줄 정의

코드 기반 공개키 암호/KEM 계열 방식.

## 상위 개념

- [[Post-Quantum Cryptography]]
- [[Code-based Cryptography]]

## 관련 개념

- [[Reed-Solomon Code]]
- [[Reed-Muller Code]]
- [[Syndrome Decoding]]
- [[Quasi-Cyclic Code]]
```

## 11.2 아이디어 노트 예시

```md
---
type: idea
status: draft
context:
created: 2026-03-14
updated: 2026-03-14
aliases:
source:
prefer-view: edit
tags:
  - topic/rm-code
---

# 왜 RM 코드는 다수결 decoding이 가능한가?

## 핵심 아이디어

RM 코드의 다수결 복호 원리를 정리해볼 수 있지 않을까?

## 왜 이게 떠올랐는가

강의에서 "다수결 원리로 복호가 가능하다"고 했는데, 직관이 불명확하다.

## 현재 생각

아직 정리 중.
```

## 11.3 회의 노트 예시

```md
---
type: meeting
status: draft
context: company
created: 2026-03-14
updated: 2026-03-14
aliases:
prefer-view: edit
tags:
  - topic/hqc
---

# 260314 HQC 구현 회의

## 참석자

- 홍길동, 김철수

## 논의 내용

FAFFT 파라미터 적용 방안 논의.

## 결정 사항

다음 주까지 파라미터 벤치마크 수행.

## 다음 행동

- [ ] FAFFT 파라미터 벤치마크
```

## 11.4 MOC 예시

```md
---
type: moc
status: stable
context:
created: 2026-03-14
updated: 2026-03-14
prefer-view: read
tags:
---

# Coding Theory MOC

## 기초

- [[Error-Correcting Codes]]
- [[Generator Matrix]]
- [[Parity-Check Matrix]]

## 주요 코드

- [[Reed-Solomon Code]]
- [[Reed-Muller Code]]

## 암호와의 연결

- [[Syndrome Decoding]]
- [[Quasi-Cyclic Code]]
- [[HQC]]
```

## 11.5 질문 노트 → 개념 승격 예시

처음 질문이 생겼을 때:

```md
---
type: note
status: draft
context:
created: 2026-03-14
updated: 2026-03-14
aliases:
source:
prefer-view: edit
tags:
---

# 왜 RM 코드는 다수결 decoding이 가능한가?

## 한 줄 요약

아직 모름.

## 핵심 내용

RM 코드는 다수결 원리로 복호가 가능하다고 하는데, 직관이 불명확하다.
```

정리가 끝나서 개념 문서로 승격한 뒤:

```md
---
type: concept
status: stable
context:
created: 2026-03-14
updated: 2026-03-15
aliases:
source:
prefer-view: edit
tags:
---

# 왜 RM 코드는 다수결 decoding이 가능한가?

## 한 줄 정의

RM 코드의 구조적 특성으로 인해 다수결 복호가 가능하다.

## 핵심 직관

(정리된 내용)
```
