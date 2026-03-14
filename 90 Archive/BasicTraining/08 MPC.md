---
type: concept
status: archive
created: 2026-01-18T17:54
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

# 0️⃣ Introduction to [[MPC]]

**MPC** (Multi-Party Computation)는 서로 신뢰하지 않는 다수의 참여자(Party)들이 각자의 비밀 입력값(Private Input)을 공개하지 않은 채로, 공동의 함수(Function) 결과를 계산하는 암호학적 프로토콜이다.

- **예시**:
    - **연봉 평균 계산**: 직원들이 자신의 연봉을 공개하지 않으면서 전체 평균 연봉을 계산하고 싶을 때.
    - **Dating Game**: 서로의 호감 여부(Yes/No)를 상대방에게 직접 말하지 않고, 둘 다 Yes일 때만 매칭되게 하고 싶을 때 (Secure AND).
- **핵심 목표**:
    - **Privacy (프라이버시)**: 입력값은 절대 노출되지 않아야 한다.
    - **Correctness (정확성)**: 계산 결과는 정확해야 한다.

# 1️⃣ Security Models

MPC의 보안성은 공격자(Adversary)가 얼마나 강력한지에 따라 다르게 정의된다.

## 1. Ideal vs Real World

- **Ideal Model (TTP, Trusted Third Party)**: 신뢰할 수 있는 제3자(TTP)가 존재한다고 가정한다. 모두가 TTP에게 비밀을 주고, TTP가 계산해서 결과만 알려주는 상황. 가장 완벽한 보안이다.
- **Real Model (현실 모델)**: TTP가 없다. 참여자끼리 프로토콜을 통해 TTP가 있는 것과 동일한 효과를 내야 한다.

## 2. Adversary Types

- **Semi-honest (Passive)**: 프로토콜 규칙은 정직하게 따르지만, 주고받는 메시지를 분석하여 비밀을 알아내려는 호기심 많은 공격자.
- **Malicious (Active)**: 프로토콜을 위반하고 거짓 데이터를 보내거나 계산을 방해하는 공격자.
    - SPDZ 프로토콜은 **$n-1$명**이 Malicious여도(나 빼고 다 적이어도) 안전하도록 설계되었다.

# 2️⃣ [[SPDZ]] Protocol (Speedz)

2012년 Crypto 학회에서 발표된 효율적인 MPC 프로토콜이다. **Preprocessing Model**을 도입하여 연산 속도를 획기적으로 높였다.

## 1. Structure: Offline & Online Phase

SPDZ는 전체 과정을 두 단계로 나눈다.

1.  **Offline Phase (Preprocessing)**:
    - 입력값(Input)과는 무관한 **랜덤 데이터(Randomness)**들을 미리 생성하여 나눠 갖는다.
    - **Beaver Triplet** ($a, b, c$) 생성, MAC Key 공유 등이 일어난다.
    - 시간이 오래 걸리는 무거운 작업(동형암호 등)은 여기서 미리 다 해둔다.
2.  **Online Phase**:
    - 실제 비밀 입력값을 가지고 연산을 수행한다.
    - 미리 준비된 재료를 소모(Consume)하기만 하면 되므로 **매우 빠르다**. (정보 이론적 속도).

## 2. Linear Secret Sharing Scheme (LSSS)

SPDZ는 **Additive Secret Sharing**을 기반으로 한다.

- 비밀 $x$는 $x = x_1 + x_2 + \dots + x_n \pmod p$ 형태로 쪼개져 각자 $x_i$를 갖는다.
- **표기법**: $[x]$는 $x$의 쉐어(Share)들이 분산되어 있음을 의미한다.

# 3️⃣ Operations on Shares

MPC는 모든 함수를 **덧셈(Addition)**과 **곱셈(Multiplication)**의 조합(Circuit)으로 변환하여 계산한다.

## 1. Addition (덧셈)

LSSS의 특성상 덧셈은 **로컬 연산(Local Operation)**만으로 가능하다. 통신이 필요 없다.

- $[x] + [y] = [x+y]$
- 각자 자신이 가진 조각 $x_i, y_i$를 더하면, 그 결과가 곧 $x+y$의 조각이 된다.
- 상수 곱셈($c \cdot [x]$)도 로컬 연산으로 가능하다.

## 2. Multiplication (곱셈): [[Beaver Triplet]]

곱셈은 로컬 연산으로 불가능하다. $(x_1+\dots)(y_1+\dots) \ne \sum x_i y_i$.

이를 해결하기 위해 Beaver Triplet이라 불리는 미리 계산된 랜덤 쌍을 사용한다.

- **준비물 (Offline)**: $c = a \cdot b$를 만족하는 랜덤 값 $a, b, c$의 쉐어 $[a], [b], [c]$를 나눠 갖는다.
- **연산 (Online)**: 두 비밀 $[x], [y]$를 곱하고 싶을 때:
    1.  각자 $\epsilon_i = x_i - a_i$와 $\delta_i = y_i - b_i$를 계산한다.
    2.  $\epsilon_i, \delta_i$를 모두 공개(Open)하여 $\epsilon = x-a$, $\delta = y-b$를 복원한다. (이 값들은 랜덤 $a, b$에 의해 마스킹되어 있어 $x, y$를 노출하지 않는다).
    3.  다음 수식을 이용해 $[z] = [xy]$를 계산한다:
    $$ [z] = [c] + \epsilon \cdot [b] + \delta \cdot [a] + \epsilon \cdot \delta $$

   - 위 식은 모두 덧셈과 상수 곱셈이므로 로컬 연산으로 가능하다. (단, $[c]$는 Beaver Triplet의 일부).
- **특징**: 곱셈 1번당 Triplet 1세트가 소모된다.

# 4️⃣ Verification with MAC (Malicious Security)

Malicious 공격자가 거짓 쉐어를 제출하여 결과를 엉망으로 만드는 것을 막기 위해, 모든 비밀 값에 인증 코드(MAC)를 붙여 관리한다.

## 1. Information-Theoretic MAC

- 비밀 값 $x$에 대해, 전역 비밀 키 $\alpha$를 이용해 MAC $\gamma(x) = \alpha \cdot x$를 정의한다.
- 하지만 $\alpha$ 자체도 비밀이어야 하므로, $\alpha$ 역시 쉐어 $[\alpha]$로 나뉘어 있다.
- 각 참여자는 값의 쉐어 $x_i$와 MAC의 쉐어 $\gamma(x)_i$를 모두 관리한다.
    - 관계식: $\sum \gamma(x)_i = (\sum \alpha_i) \cdot (\sum x_i)$.

## 2. Verification Procedure

연산이 모두 끝난 후, 결과가 올바른지 검증한다.

1.  랜덤 챌린지 $r_1, \dots, r_k$를 생성한다.
2.  검증할 값들 $a_1, \dots, a_k$에 대해 선형 결합 $a = \sum r_j a_j$와 그에 대응하는 MAC $\gamma = \sum r_j \gamma_j$를 계산한다.
3.  모든 참여자가 자신의 쉐어를 이용해 다음 식을 확인한다:
    $$ \alpha \cdot a - \gamma \approx 0 $$
   - 이를 쉐어 레벨에서 풀면: $\sum_{i} (\alpha_i \cdot a - \gamma_i) = 0$.
4.  각자 $\sigma_i = \alpha_i \cdot a - \gamma_i$를 계산해서 커밋(Commit)하고 공개한다.
5.  $\sum \sigma_i = 0$이면 통과, 아니면 누군가 사기를 친 것이므로 **Abort**한다.

# 5️⃣ Offline Phase Implementation (SHE)

그렇다면 오프라인 단계에서 Beaver Triplet ($a, b, c=ab$)은 어떻게 안전하게 만드는가? 여기서 Somewhat Homomorphic Encryption (SHE)가 사용된다.

- **SHE (서머트 동형암호)**: 덧셈은 무한히, 곱셈은 제한된 횟수(여기선 1번)만 가능한 암호. (Fully HE보다 훨씬 빠르다).
- **과정**:
    1.  각자 랜덤 $a_i, b_i$를 생성하고 암호화하여 전송한다. ($Enc(a_i), Enc(b_i)$).
    2.  동형암호의 가산 성질을 이용해 $Enc(a) = \sum Enc(a_i)$, $Enc(b) = \sum Enc(b_i)$를 만든다.
    3.  동형암호의 승산 성질(1회 허용)을 이용해 $Enc(c) = Enc(a) \cdot Enc(b)$를 계산한다.
    4.  이 암호문들을 다시 각자의 쉐어로 변환(Resharing)하여 나눠 갖는다.
- 이렇게 하면 아무도 $a, b, c$의 실제 값은 모른 채, 그 쉐어들만 안전하게 나눠 가질 수 있다.

> **요약**: SPDZ는 **동형암호**(SHE)를 이용해 오프라인에서 재료(Triplet)를 준비하고, 온라인에서는 Beaver Triplet과 MAC을 이용해 빠르고 안전하게(Malicious Security) 연산하는 현대적인 MPC 프로토콜이다.
