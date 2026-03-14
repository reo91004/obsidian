---
type: concept
status: archive
created: 2026-01-18T16:58
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

이 문서는 암호학의 두 가지 중요한 주제를 다룬다.

1.  **ZKP Advanced**: 영지식 증명의 안전성을 수학적으로 정의하는 **Simulation Paradigm**과 **Extraction**.
2.  **Hash Function**: 암호학의 '스위스 아미 나이프'라 불리는 해시 함수의 정의와 핵심 성질.

---

# Part 1: [[ZKP]] Advanced Topics

## 1️⃣ Simulation Paradigm (시뮬레이션 패러다임)

영지식(Zero-Knowledge) 성질을 어떻게 수학적으로 증명할 것인가?

> **핵심 직관**: "Verifier 혼자서도 Prover와 나눈 대화(Transcript)를 똑같이 만들어낼 수 있다면, Prover는 Verifier에게 아무런 새로운 정보(지식)를 주지 않은 것이다."

### 1. View & Simulator

- **View ($View_V(P, V)$)**: 검증자(Verifier)가 프로토콜 실행 중에 보게 되는 모든 정보의 집합.
    - 구성: `(자신의 랜덤 동전, Prover의 메시지들)`
- **Simulator ($S$)**: 비밀 정보(Witness) 없이, Verifier의 View와 구분 불가능한 가짜 View를 생성하는 알고리즘.

### 2. Indistinguishability (구분 불가능성)

실제 대화($Real$)와 시뮬레이션($Ideal$)이 얼마나 비슷한가?

- **Perfect ZK**: 두 분포가 수학적으로 **완전히 동일**하다. (예: Schnorr Protocol).
- **Computational ZK**: 계산 능력이 유한한(Polynomial time) 구분자(Distinguisher)가 두 분포를 구분할 확률이 무시할 만하다(Negligible).

### 3. Honest vs Malicious

- **Honest Verifier ZK (HVZK)**: 규칙을 잘 지키는 검증자에게만 정보를 숨긴다.
- **Malicious Verifier**: 검증자가 작정하고 이상한 값(Challenge)을 던져 정보를 캐내려 해도, 시뮬레이터가 이를 커버할 수 있어야 진정한 ZK이다.

## 2️⃣ Proof of Knowledge (PoK) & Extraction

"Prover가 진짜 비밀을 알고 있는가?" (Soundness의 강화된 개념).

### 1. Extractor (추출기, $\mathcal{E}$)

- 만약 어떤 Prover가 높은 확률로 검증을 통과한다면, 그 Prover의 내부 상태에 접근하거나 **Rewinding(되감기)** 기법을 사용하는 **Extractor**가 비밀(Witness)을 **뽑아낼(Extract) 수 있어야 한다.**
- **Rewinding**: 타임머신처럼 프로토콜의 특정 시점(Challenge 단계)으로 시간을 되돌려, 동일한 상태에서 다른 질문을 던지는 기법.

### 2. 예시: 알리바바 동굴

- Prover가 "왼쪽"으로 나오라는 명령도 수행하고, 시간을 되돌려 "오른쪽"으로 나오라는 명령도 수행할 수 있다면, Prover는 양쪽 길을 다 아는(즉, 비밀을 아는) 것이다.
- $\therefore$ **"Extractor가 비밀을 뽑아낼 수 있다" $\iff$ "Prover는 비밀을 알고 있다."**

---

# Part 2: [[Hash Function]] (암호학적 해시)

## 1️⃣ Definition & Features

**Hash Function $H: \{0, 1\}^* \to \{0, 1\}^n$**
- 임의의 길이의 입력을 받아 고정된 길이(예: 256bit)의 출력(Digest)을 반환하는 함수.
- **Efficient**: 계산 속도가 매우 빠르다.
- **Compression**: 데이터를 압축하므로 복호화는 불가능하다.

## 2️⃣ Security Properties (핵심 3요소)

암호학적 해시 함수가 되기 위한 3가지 조건. (양궁 과녁 비유)

### 1. Pre-image Resistance (역상 저항성)

- **One-wayness**: $y = H(x)$일 때, $y$만 보고 입력값 $x$를 찾는 것은 계산적으로 불가능해야 한다.
- **비유**: 과녁에 꽂힌 화살($y$)만 보고, 사수($x$)가 어디서 쏘았는지 역추적할 수 없어야 한다.

### 2. Second Pre-image Resistance (Weak Collision)

- **Target Collision**: 입력값 $x$가 **고정**되어 있을 때, $H(x) = H(x')$인 다른 입력값 $x'$ ($x \neq x'$)를 찾는 것이 어려워야 한다.
- **비유**: 이미 과녁에 꽂힌 화살($x$)과 **정확히 같은 자리**에 내가 쏜 화살($x'$)을 맞추기는 어렵다.

### 3. Collision Resistance (Strong Collision)

- **Any Collision**: 입력값이 고정되지 않음. $H(x) = H(y)$인 임의의 쌍 $(x, y)$를 찾는 것이 어려워야 한다.
- **Birthday Paradox**: $N$명의 사람이 있을 때 생일이 같은 쌍이 존재할 확률. $23$명만 있어도 50%가 넘는다.
    - 해시 공간이 $n$비트일 때, **약한 충돌**은 $2^n$번 시도해야 하지만, **강한 충돌**은 $\sqrt{2^n} = 2^{n/2}$번만 시도하면 찾을 수 있다.
    - **결론**: 공격자는 더 쉬운 Strong Collision을 노리므로, 해시 함수는 이를 버틸 수 있어야 한다. (SHA-256이 128비트 보안 강도를 갖는 이유).

## 3️⃣ Algorithms History

### 1. Broken (사용 금지)

- **MD5**: 128bit. 충돌이 매우 쉽게 발견됨.
- **SHA-1**: 160bit. 2017년 구글의 **SHAttered** 공격으로 충돌 쌍이 공개됨. 퇴출.

### 2. Standard (권장)

- **SHA-2**: (SHA-256, SHA-512). Merkle-Damgård 구조. 현재 가장 많이 사용됨 (비트코인 등).
- **SHA-3 (Keccak)**: SHA-2와 완전히 다른 **Sponge 구조**. SHA-2가 깨질 것을 대비해 만들어짐.

## 4️⃣ Applications

단순 요약이 아닌 **"비밀 키"**와의 결합이 핵심이다.

### 1. HMAC (Hash-based MAC)

- **목적**: 메시지 무결성(Integrity) + 인증(Authentication).
- **구조**: $H(Key || Message)$ 처럼 키를 섞어서 해싱.
- **특징**: 키를 가진 사람만 올바른 해시값을 만들 수 있다. (대칭키 방식).

### 2. Digital Signature (전자 서명) vs MAC

- **MAC**: 대칭키 사용. 송신자와 수신자가 같은 키를 공유하므로, "누가 보냈는지" 제3자에게 증명할 수 없다(**부인 방지 불가**).
- **Signature**: 공개키 암호 사용. 송신자의 개인키로 서명하므로 누구나 검증 가능하며, 송신자는 발송 사실을 부인할 수 없다(**Non-repudiation**).
- **Hash의 역할**: 긴 메시지 전체를 서명하면 느리므로, 해시값 $H(M)$에 서명한다.

### 3. Password Storage (Salting)

- **Rainbow Table 공격**: 미리 계산된 해시값 테이블로 비밀번호를 역추적하는 공격.
- **Salt**: 사용자마다 고유한 랜덤 값(Salt)을 붙여 $H(Pw || Salt)$로 저장하여 테이블 공격을 무력화한다.
