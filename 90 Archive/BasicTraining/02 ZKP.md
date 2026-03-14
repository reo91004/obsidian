---
type: concept
status: archive
created: 2026-01-18T16:49
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

# 0️⃣ Introduction to [[ZKP]]

**Zero-Knowledge Proof (영지식 증명)**는 현대 암호학에서 가장 혁신적이고 중요한 개념 중 하나이다. **"신뢰할 수 없는 환경에서, 내가 비밀 정보를 알고 있다는 사실만 증명하고 비밀 정보 자체는 절대 노출하지 않는 방법"**이다.

## 왜 필요한가? (Why ZKP?)

1.  **Privacy (프라이버시)**: 내 비밀번호, 주민등록번호, 자산 정보 등을 상대방에게 보내지 않고도 내가 '정당한 사용자'임을 증명할 수 있다.
2.  **Scalability (확장성)**: 블록체인 등에서 방대한 연산의 결과가 '올바르다'는 것만 압축적으로 증명하여 검증 비용을 획기적으로 줄인다 (zk-Rollup).
3.  **Authentication (인증)**: 서버에 패스워드를 전송하지 않고도 로그인이 가능하다.

> **핵심 아이디어**: "증명은 하되, 답(비밀)은 알려주지 말자."

# 1️⃣ Core Concepts (핵심 개념)

## 1. 구성 요소 (Actors)

-   **Prover (증명자, $\mathcal{P}$)**: "나 비밀 알고 있어!"라고 주장하는 주체. (무한한 계산 능력을 가졌다고 가정하기도 함).
-   **Verifier (검증자, $\mathcal{V}$)**: "진짜? 한번 증명해봐."라고 의심하고 확인하는 주체. (제한된 시간, 즉 다항 시간 내에 검증해야 함).

## 2. 세 가지 필수 조건 (Properties)

ZKP가 되기 위해서는 다음 세 가지 조건을 반드시 만족해야 한다.

1.  **Completeness (완전성)**:
    -   "참은 참으로 통한다."
    -   Prover가 정직하고 주장이 사실이라면, Verifier는 **항상(확률 1)** 이를 받아들여야 한다.
2.  **Soundness (건전성)**:
    -   "거짓은 통하지 않는다."
    -   Prover가 거짓말을 하거나 비밀을 모른다면, Verifier를 속일 확률은 **무시할 수 있을 만큼 작아야(Negligible)** 한다.
3.  **Zero-Knowledge (영지식성)**:
    -   "비밀은 새어나가지 않는다."
    -   증명 과정이 끝난 후, Verifier는 "이 주장이 참이다"라는 사실 외에는 **어떠한 추가 정보(비밀)**도 얻을 수 없어야 한다.

# 2️⃣ Analogies & Intuition (직관적 이해)

복잡한 수식 이전에 직관적인 예시를 통해 이해해보자.

## 1. 알리바바와 동굴 (Ali Baba's Cave)

-   **상황**: 동굴 안에 마법 주문("열려라 참깨")을 외쳐야만 열리는 비밀 문이 있다. Prover(Peggy)는 Verifier(Victor)에게 주문을 알려주지 않고, 자신이 주문을 안다는 사실만 증명하고 싶다.
-   **프로토콜**:
    1.  Victor는 동굴 밖에서 기다린다.
    2.  Peggy는 갈림길 A, B 중 하나로 들어가 숨는다.
    3.  Victor가 동굴 입구로 와서 소리친다. **"A(또는 B)로 나와!"**
    4.  Peggy는 주문을 알기에 문을 열고 Victor가 요구한 방향으로 나온다.
-   **분석**:
    -   주문을 모르면 운 좋게 맞출 확률은 50%이다.
    -   이 과정을 20번 반복하면, 사기를 칠 확률은 $(1/2)^{20} \approx 0.000001$이 된다. (**Soundness**)
    -   Victor는 Peggy가 나오는 모습만 봤지 주문은 듣지 못했다. (**Zero-Knowledge**)

## 2. 그래프 3-색칠 문제 ([[Graph 3-Coloring]])

-   **문제**: 복잡한 그래프의 인접한 노드가 서로 다른 색이 되도록 3가지 색(R, G, B)으로 칠할 수 있는 해(Solution)를 알고 있다.
-   **ZKP 방법**:
    1.  **Commit**: Prover는 색깔을 무작위로 뒤섞고(Permutation), 모든 노드의 색을 가려서 Verifier에게 보여준다.
    2.  **Challenge**: Verifier는 임의의 인접한 두 노드를 고른다.
    3.  **Response**: Prover는 그 두 노드의 색만 공개한다.
    4.  **Verify**: 두 색이 서로 다른지 확인한다.
-   **핵심**: 매번 색을 랜덤하게 뒤섞기 때문에 (예: 빨강 $\rightarrow$ 파랑), Verifier는 부분적인 색깔 정보만으로는 전체 패턴(비밀)을 절대 알 수 없다.

# 3️⃣ ZKP Protocol (수학적 구현)

실제 암호학에서는 **[[Interactive Proof System]]**을 사용한다. 대표적인 예시로 **이차 잉여(Quadratic Residuosity)** 문제를 보자.

## 이차 잉여 문제 (Quadratic Residuosity)

-   **설정**: $N$은 두 큰 소수의 곱. $y$가 $x^2 \equiv y \pmod N$을 만족하는 $x$가 존재하는가?
-   **비밀 정보 (Witness)**: $x$ (Prover만 알고 있음).

### 프로토콜 단계 ($\Sigma$-Protocol)

1.  **Commitment (약속)**:
    -   Prover는 랜덤한 값 $r$을 뽑아 $s \equiv r^2 \pmod N$을 계산해 보낸다.
    -   (자신의 랜덤성을 미리 '약속'하는 단계).
2.  **Challenge (도전)**:
    -   Verifier는 랜덤한 비트 $b \in \{0, 1\}$을 던져 Prover에게 보낸다.
3.  **Response (응답)**:
    -   만약 $b=0$이면, Prover는 $z = r$을 보낸다.
    -   만약 $b=1$이면, Prover는 $z = r \cdot x \pmod N$을 보낸다.
4.  **Verification (검증)**:
    -   $b=0$일 때: $z^2 \equiv s \pmod N$ 인지 확인. (즉, $r^2 = s$).
    -   $b=1$일 때: $z^2 \equiv s \cdot y \pmod N$ 인지 확인. (즉, $(rx)^2 = r^2 x^2 = s \cdot y$).

### 안전성 분석

-   **Soundness**: $x$를 모르면 $b=0$과 $b=1$을 동시에 만족하는 답을 미리 준비할 수 없다. 50% 확률로 들킨다. 반복하면 확률은 0에 수렴한다.
-   **Zero-Knowledge**: $z$ 값은 랜덤 값 $r$ 때문에 매번 달라진다. $z$만 봐서는 $x$를 유추할 수 없다. (One-Time Pad와 유사).

# 4️⃣ Simulator & Simulation Paradigm

"어떻게 아무 정보도 안 줬다는 것을 증명할까?" 이는 **시뮬레이션(Simulation)** 개념으로 정의된다.

## Simulation Paradigm의 정의

> 만약 Verifier가 Prover 없이 혼자서도 **"Prover와 대화한 것과 똑같은 기록(Transcript)"**을 만들어낼 수 있다면, Verifier는 Prover에게서 **아무런 새로운 정보도 얻지 못한 것**이다.

-   **Simulator ($S$)**: 비밀 정보($x$) 없이, Verifier와 대화한 것처럼 보이는 가짜 기록을 생성하는 알고리즘.
-   **Indistinguishability**: 실제 Prover와의 대화 기록(View)과 Simulator가 만든 가짜 기록이 **통계적으로 구별 불가능(Statistically Indistinguishable)**하다면, 이는 Zero-Knowledge임이 증명된 것이다.
    -   "너한테 들은 얘기나, 나 혼자 방구석에서 상상해서 쓴 소설이나 똑같다면, 너는 나에게 아무 정보도 주지 않은 것이다."

# 5️⃣ Summary

> **Zero-Knowledge Proof**는 "신뢰"라는 사회적 비용을 "수학적 검증" 비용으로 치환하는 기술이다.

-   **핵심 3요소**: Completeness(진실), Soundness(엄격), Zero-Knowledge(비밀보장).
-   **작동 원리**: 대화형(Interactive) 구조에서 랜덤성(Randomness)을 이용해 정보를 숨긴 채 논리적 정합성만 확인한다.
-   **응용**: 블록체인(Zcash, zk-EVM), 신원 인증(DID), 프라이버시 보호 데이터 분석 등.
