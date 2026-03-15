---
type: concept
status: stable
context: lab
created: 2026-01-18T17:59
updated: 2026-03-15T19:40
aliases:
source:
prefer-view: edit
tags:
---

# 0️⃣ Introduction

MPC (Multi-Party Computation)를 구현하기 위한 핵심 기술(Building Blocks)은 크게 세 가지로 분류된다.

1.  **Secret Sharing**: 데이터를 쪼개어 분산 (지난 시간).
2.  **[[Oblivious Transfer]]** (OT): 불확정 전송. 선택적 정보 전달.
3.  **[[Garbled Circuit]]**: 혼란 회로. 연산 로직 자체를 암호화.

이번 문서는 **OT**와 **Garbled Circuit**을 중심으로, 전통적인 MPC가 어떻게 비밀을 지키며 연산을 수행하는지 다룬다.

# 1️⃣ [[Oblivious Transfer]] (OT)

## 1. 개념 (1-out-of-2 OT)

Sender(Alice)와 Receiver(Bob) 사이의 비대칭적인 정보 교환 프로토콜이다.

- **Sender**: 두 개의 비밀 메시지 $x_0, x_1$을 가지고 있다.
- **Receiver**: 선택 비트 $b \in \{0, 1\}$를 가지고 있다.
- **결과**:
    - Receiver는 $x_b$를 얻는다. (하지만 $x_{1-b}$는 절대 알 수 없다).
    - Sender는 Bob이 $x_0$와 $x_1$ 중 무엇을 가져갔는지($b$가 무엇인지) **전혀 모른다**.

## 2. 응용: The Millionaire Problem

야오(Yao)가 제안한 고전적인 문제. 두 백만장자가 자신의 재산 액수를 공개하지 않고 **"누가 더 부자인가?"** ($x > y$?) 만을 알고 싶어 한다.

- **해결 아이디어**:
    - Alice(재산 $x$)는 $x$에 따라 결과가 달라지는 테이블(벡터)을 만든다.
    - Bob(재산 $y$)은 자신의 재산 $y$에 해당하는 인덱스의 값만 OT로 가져온다.
    - 가져온 값이 1이면 Alice 승, 0이면 Bob 승.

## 3. 구현 방법 (RSA 활용 예시)

Semi-honest 모델(프로토콜을 준수함)에서의 구현 예시.

1.  **Alice (Sender)**: RSA 키 쌍 $(pk, sk)$ 생성 후 $pk$ 공개.
2.  **Bob (Receiver)**: 랜덤 값 $k$ 생성.
    - 선택 $b$에 따라 $v = x_b + k^e \pmod N$ 계산 후 전송. (여기서 $x_b$는 공개된 랜덤 값).
3.  **Alice**: 받은 $v$를 이용해 두 개의 가능한 $k_0, k_1$을 계산 및 전송.
4.  **Bob**: 자신의 진짜 $k$와 일치하는 것을 확인하고 메시지 복호화.
- *최근에는 계산 효율성을 위해 **OT Extension**이나 **ECC 기반 OT**를 주로 사용한다.*

# 2️⃣ [[Garbled Circuit]] (Yao's Protocol)

앤드류 야오(Andrew Yao)가 제안한 2-Party MPC 프로토콜. 모든 함수를 **Boolean Circuit**으로 변환하고, 이를 암호화하여 계산한다.

## 1. 기본 아이디어

논리 게이트(AND, OR 등)의 **진리표(Truth Table)**를 암호화한다. 0과 1이라는 값 대신, 긴 랜덤 문자열(Key)을 사용한다.

- **Wire Key**: 각 와이어(입출력 선)마다 0을 의미하는 키 $k^0$와 1을 의미하는 키 $k^1$를 랜덤 생성한다.
- **Garbling**: 게이트의 진리표를 암호문으로 바꾼다.
    - 예: AND 게이트의 입력이 $u, v$, 출력이 $w$라면,
    - $Enc_{k_u^0, k_v^0}(k_w^0)$ (0 AND 0 = 0)
    - $Enc_{k_u^0, k_v^1}(k_w^0)$ (0 AND 1 = 0)
    - $Enc_{k_u^1, k_v^0}(k_w^0)$ (1 AND 0 = 0)
    - $Enc_{k_u^1, k_v^1}(k_w^1)$ (1 AND 1 = 1)
- 이 4개의 암호문을 순서를 섞어서(Shuffling) 보낸다. 이것이 **Garbled Table**이다.

## 2. 프로토콜 진행 과정

1.  **Circuit Construction**: Alice(Generator)는 함수 $f$를 Boolean Circuit으로 만들고, 모든 와이어에 대해 랜덤 키를 생성하여 **Garbled Circuit**을 만든다.
2.  **Transferring Alice's Input**: Alice는 자신의 입력값에 해당하는 키를 Bob(Evaluator)에게 보낸다. (Alice는 알니까 그냥 주면 됨).
3.  **Transferring Bob's Input (via OT)**: Bob은 자신의 입력값에 해당하는 키를 받아야 한다.
    - Alice는 0일 때의 키와 1일 때의 키를 다 가지고 있다.
    - Bob은 자신의 입력이 0인지 1인지 Alice에게 들키지 않고 해당 키만 받아와야 한다.
    - 이때 **[[Oblivious Transfer]]**를 사용한다.
4.  **Evaluation**: Bob은 Garbled Table과 손에 쥔 키들을 이용해 암호문을 한 줄씩 복호화한다.
    - 키가 맞으면 복호화에 성공하여 다음 와이어의 키를 얻게 된다.
    - 최종적으로 출력 와이어의 키를 얻는다.
5.  **Output Decoding**: Alice가 출력 키와 실제 값(0/1)의 매핑 정보를 주면, Bob은 결과를 확인한다.

## 3. 특징

- **Constant Round**: 통신 횟수가 회로의 깊이(Depth)와 무관하게 일정하다. (Garbled Circuit 보내고, OT 하고 끝).
- **Communication Cost**: 회로가 클수록 전송해야 할 데이터(Garbled Table)가 매우 커진다.
- **One-time Use**: 한 번 사용한 Garbled Circuit은 키가 노출되었으므로 재사용할 수 없다. 매번 새로 만들어야 한다.

# 3️⃣ Security & Efficiency

## 1. Security Models

- **Semi-honest (Passive)**: 프로토콜을 성실히 수행하며 도청만 하는 공격자. 기본적인 Yao's Protocol은 이 모델에서 안전하다.
- **Malicious** (Active): 가짜 회로를 보내거나 입력을 조작하는 공격자.
    - **Cut-and-Choose**: Alice가 회로를 N개 만들어서 보낸다. Bob이 그중 일부를 까서(Open) 제대로 만들었는지 검증하고, 나머지를 실행(Evaluate)한다. 이를 통해 악의적인 행위를 확률적으로 잡아낸다.

## 2. Optimizations

Garbled Circuit은 데이터 전송량이 많아 최적화가 필수적이다.

- **Free-XOR**: XOR 게이트는 암호문 전송 없이 로컬 연산(XOR)만으로 처리 가능하게 만드는 기법. (매우 중요).
- **Row Reduction**: Garbled Table의 크기를 4줄에서 3줄, 2줄로 줄이는 기법.
- **Half Gates**: AND 게이트의 비용을 줄이는 기법.

> **요약**:
> - **OT**: "내가 뭘 골랐는지 모르게 줘." (MPC의 원자 단위).
> - **Garbled Circuit**: "암호화된 회로를 줄 테니, 네 입력 키만(OT로) 가져가서 계산해봐." (일반적인 함수 계산을 위한 2-Party MPC).