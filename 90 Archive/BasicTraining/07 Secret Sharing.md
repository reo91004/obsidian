---
type: concept
status: archive
created: 2026-01-18T17:46
updated: 2026-03-15T01:47
aliases:
domain:
  - "[[Cryptography]]"
parent:
related:
source:
project:
area:
prefer-view: edit
tags:
  - area/cryptography
---

# 0️⃣ Introduction

**Secret Sharing** (비밀 분산)은 하나의 비밀(Secret)을 여러 조각(Share)으로 나누어 여러 참여자에게 분배하는 기술이다. 단순히 비밀을 쪼개는 것이 아니라, **특정 조건** (Threshold)을 만족하는 수의 조각이 모였을 때만 비밀을 복구할 수 있고, 그보다 적은 수의 조각으로는 비밀에 대한 **어떠한 정보도 얻을 수 없는** 강력한 보안 성질을 가진다.

이 기술은 안전한 키 저장(Key Management), 부채널 공격 방어(Side-Channel Countermeasure), 그리고 현대 암호학의 꽃인 **MPC** (Multi-Party Computation)의 핵심 빌딩 블록으로 사용된다.

# 1️⃣ Motivating Example: The Dating Game (Secure AND)

Secret Sharing과 MPC의 개념을 이해하기 위해 1989년 Eurocrypt에서 소개된 "데이팅 게임" 문제를 살펴보자.

## 1. 문제 상황

- **Alice**와 **Bob**이 소개팅을 했다.
- 서로 마음에 들면 "Yes(1)", 아니면 "No(0)"를 선택한다.
- **목표**: 두 사람 모두 "Yes"일 때만(AND 연산) 서로의 마음을 확인하고, 한 명이라도 "No"라면 상대방이 무엇을 선택했는지 **몰라야 한다**.
    - 내가 "No"를 했는데 상대가 "Yes"를 했다는 사실을 알게 되면, 상대방은 자존심이 상할 수 있기 때문이다.

## 2. 해결책 (Card Game Analogy)

카드를 이용해 이 논리적 AND 연산을 비밀스럽게 수행할 수 있다. (5장: King 3장, Queen 2장).

1.  **Rule**: "Yes"면 King을 Queen 위에, "No"면 Queen을 King 위에 둔다.
2.  **Process**:
    - 각자 자신의 선택대로 카드를 겹쳐서 뒤집어 놓는다.
    - 뒤집힌 카드 뭉치를 **랜덤하게 섞는다** (Shuffling). (누가 어떤 카드를 냈는지 모르게 하기 위함).
3.  **Reveal**: 카드를 오픈했을 때, 특정 패턴(예: King이 연속 3번)이 나오면 둘 다 "Yes"인 것이고, 아니면 "No"인 것이다.
4.  **결과**: 섞였기 때문에 결과가 "No"일 때, 누구 때문에 "No"가 되었는지(상대가 Yes였는지 No였는지) 알 수 없다.

> 이처럼 각자의 입력값(Secret)을 숨긴 채 원하는 결과값만 계산해내는 것이 **MPC**이며, 그 기초에 Secret Sharing이 있다.

# 2️⃣ Basic Secret Sharing Schemes

## 1. $(n, n)$-Secret Sharing (XOR 방식)

가장 단순한 형태는 $n$개의 조각으로 나누고, $n$개가 **모두** 모여야만 복구되는 방식이다.

- **방법**: 비밀 $M$을 $n$명에게 나누려면, $n-1$개의 랜덤 값 $R_1, ..., R_{n-1}$을 생성한다.
- **Shares**:
    - $S_1 = R_1$
    - $S_2 = R_2$
    - ...
    - $S_n = M  igoplus R_1  igoplus R_2  igoplus ...  igoplus R_{n-1}$ (마지막 조각에 비밀을 섞음)
- **복구**: 모든 $S_i$를 XOR($ igoplus$)하면 $R$들이 2번씩 더해져 사라지고(Cancel out), $M$만 남는다.
- **보안성**: 하나라도 없으면 $M$은 완벽한 랜덤 값처럼 보인다. (One-Time Pad와 유사).

## 2. $(t, n)$-Threshold Secret Sharing

더 일반적이고 유용한 방식은 $n$개의 조각 중 **$t$개** (Threshold, 문턱값)만 모으면 복구할 수 있는 방식이다. ($t \le n$).

- **특징**:
    - $t$개 이상의 조각이 모이면: 비밀 $M$을 완벽하게 복구.
    - $t-1$개의 조각이 모이면: 비밀 $M$에 대한 **어떠한 정보도 얻을 수 없음** (Information Theoretic Security).
- **예시**: 드래곤볼 7개를 다 모아야 용신이 나오듯, 10개의 조각 중 5개만 모이면 비밀이 열리는 금고.

# 3️⃣ [[Shamir's Secret Sharing]] (SSS)

이스라엘의 암호학자 아디 샤미르(Adi Shamir)가 제안한 방식으로, **다항식** (Polynomial)의 성질을 이용한다.

## 1. 직관적 이해: 직선의 방정식 ($t=2$)

- **원리**: 2개의 점이 있으면 하나의 직선을 그릴 수 있다.
- **방법**:
    1.  비밀 $S$를 $y$절편(상수항)으로 하는 1차 함수(직선) $y = ax + S$를 만든다. ($a$는 랜덤 기울기).
    2.  이 직선 위의 점들을 조각으로 나눠준다. (예: $(1, y_1), (2, y_2), ...$).
- **복구**: 아무 점 2개만 있으면 직선을 그릴 수 있고, $y$절편($S$)을 찾을 수 있다.
- **보안성**: 점 1개만으로는 이 점을 지나는 직선이 무한히 많으므로, $y$절편이 무엇인지 전혀 알 수 없다.

## 2. 일반화: 다항식 보간법 ($t-1$차식)

$t$개의 점이 있으면 $t-1$차 다항식을 유일하게 결정할 수 있다는 원리를 이용한다.

- **Setup (Dealing)**:
    1.  비밀 $S$를 상수항($a_0$)으로 하는 $t-1$차 다항식 $f(x)$를 랜덤하게 생성한다.
        $$ f(x) = a_{t-1}x^{t-1} + \ldots + a_1x + S $$
    2.  $n$명의 참여자에게 각각 함숫값 $S_i = f(i)$를 계산하여 나눠준다. (Share: $(i, f(i))$).
- **Reconstruction**:
    - $t$개의 점 $(x_i, y_i)$를 모으면, **[[Lagrange Interpolation]]** (라그랑주 보간법)을 통해 원래의 다항식 $f(x)$를 복원할 수 있다.
    - 복원된 식에 $x=0$을 대입하면 비밀 $S = f(0)$을 얻는다.

$$ f(x) = \sum_{j=1}^{t} y_j \cdot L_j(x), \text{ where } L_j(x) = \sum_{i=1, i \neq j}^{t} \frac{x - x_i}{x_j - x_i} $$

# 4️⃣ Connection with [[Reed-Solomon Code]]

강의 후반부의 핵심은 **Secret Sharing이 결국 Reed-Solomon (RS) Code와 동일하다**는 통찰이다.

## 1. Erasure Correction 관점

- **RS Code**: 데이터에 오류 정정 부호를 추가하여, 일부가 손실되거나(Erasure) 깨져도(Error) 원본을 복구하는 기술.
- **Secret Sharing과의 관계**:
    - 비밀을 다항식의 계수(Coefficient)로 보고, 쉐어들을 다항식의 함숫값(Evaluation)으로 본다.
    - $n$개의 쉐어 중 $t$개를 안다는 것은, 나머지 $n-t$개가 **소거** (Erasure)된 상황과 같다.
    - RS Code는 Erasure를 복구하는 능력이 탁월하다. 즉, $t$개의 값만 살아있으면 나머지 값을(그리고 원래의 계수 $S$를) 복구할 수 있다.
    - 따라서 **Shamir's Secret Sharing은 RS Code의 Erasure Decoding 문제와 수학적으로 동일하다.**

## 2. Linear Secret Sharing Scheme (LSSS)

이 모든 과정은 선형 대수(Linear Algebra)로 표현 가능하다.

- **Vandermonde Matrix**: 다항식의 함숫값을 구하는 과정은 행렬 곱셈으로 표현된다.
$$  \begin{pmatrix} 1 & 1 & \ldots & 1 \\ 1 & 2 & \ldots & 2^{t-1} \\ \vdots & \vdots & \ddots & \vdots \\ 1 & n & \ldots & n^{t-1} \end{pmatrix}  \begin{pmatrix} S \\ a_1 \\ \vdots \\ a_{t-1} \end{pmatrix} =  \begin{pmatrix} y_1 \\ y_2 \\ \vdots \\ y_n \end{pmatrix} $$
- **복구**: $t$개의 점을 선택한다는 것은 위 행렬에서 $t$개의 행(Row)을 뽑아 부분 행렬을 만드는 것이다. Vandermonde Matrix의 성질에 의해 이 부분 행렬은 항상 역행렬이 존재(Invertible)하므로, 연립방정식을 풀어 비밀 $S$를 구할 수 있다.

# 5️⃣ Security Models (Advanced)

- **Passive Adversary** (Semi-honest): 프로토콜은 따르지만, 주고받는 정보를 보며 비밀을 알아내려는 공격자. $t$개 미만의 정보로는 수학적으로 비밀을 알 수 없음이 증명됨.
- **Active Adversary** (Malicious): 거짓된 쉐어를 제출하여 복구를 방해하는 공격자.
    - RS Code는 단순히 지워진 것(Erasure)뿐만 아니라 틀린 값(Error)도 고칠 수 있다.
    - 따라서 충분히 많은 쉐어가 있다면(일반적으로 $n > 3 	imes 	ext{adversary}$), 악의적인 참여자가 있어도 올바른 비밀을 복구할 수 있다. (비잔틴 장군 문제와 연결됨).

> **요약**: Secret Sharing은 다항식 위에서의 점 찾기 놀이와 같다. $t$개의 점을 찾으면 다항식(비밀)이 보이고, 그 전까지는 무한한 가능성 속에 숨겨져 있다. 이것이 현대 MPC와 블록체인 등에서 신뢰를 분산시키는 핵심 기술이다.
