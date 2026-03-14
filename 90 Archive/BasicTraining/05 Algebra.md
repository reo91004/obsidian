---
type: concept
status: archive
created: 2026-01-18T17:32
updated: 2026-03-15T01:47
aliases:
domain:
  - "[[Mathematics]]"
parent:
related:
source:
project:
area:
prefer-view: edit
tags:
  - area/mathematics
---

# 0️⃣ Introduction

암호학(Cryptography)을 깊이 이해하기 위해서는 **대수학(Abstract Algebra)**적 기초가 필수적이다. 암호 시스템은 대부분 수학적 난제 위에 설계되며, 그 난제들은 Group, Ring, Field와 같은 구조 위에서 정의되기 때문이다.

대수학은 **집합(Set)**과 **연산(Operation)**으로 이루어진 **구조(Structure)**에 관한 학문이다. 조건이 까다로워질수록 구조는 다음과 같이 구체화된다.

> **Group (군) $\to$ Ring (환) $\to$ Field (체)**

우리의 최종 목표는 암호학(AES, RSA, ECC 등)에서 주로 쓰이는 **유한체(Finite Field)**, 즉 원소의 개수가 유한한 Field를 이해하는 것이다.

# 1️⃣ Group (군)

가장 기본이 되는 대수적 구조이다. 하나의 연산에 대해 잘 정의된 집합이다.

## 1. 정의

공집합이 아닌 집합 $G$와 이항 연산(Binary Operation) $\cdot$ 이 있을 때, 다음 4가지 조건을 만족하면 **Group**이라고 한다.

1.  **Closure (닫혀있음)**: 임의의 $a, b \in G$에 대해, 연산 결과 $a \cdot b$도 반드시 $G$에 속해야 한다.
2.  **Associativity (결합법칙)**: 연산 순서를 바꿔도 결과가 같아야 한다. $(a \cdot b) \cdot c = a \cdot (b \cdot c)$.
3.  **Identity (항등원)**: "아무런 변화를 주지 않는 원소". 모든 $a \in G$에 대해 $a \cdot e = a$를 만족하는 유일한 $e \in G$가 존재해야 한다. (예: 덧셈의 항등원은 0, 곱셈의 항등원은 1).
4.  **Inverse (역원)**: "항등원으로 되돌리는 원소". 모든 $a \in G$에 대해 $a \cdot a^{-1} = e$를 만족하는 유일한 $a^{-1} \in G$가 존재해야 한다. (예: 3의 덧셈 역원은 -3).

## 2. Abelian Group (아벨군)

- 위 4가지 조건에 **Commutativity (교환법칙)** ($a \cdot b = b \cdot a$)까지 만족하면 **Abelian Group**이라고 한다.
- **예시**:
  - **정수 집합 $(\mathbb{Z}, +)$**: 덧셈에 대해 아벨군이다.
  - **정수 집합 $(\mathbb{Z}, \times)$**: 곱셈에 대해서는 **군이 아니다**. 역원(예: $1/3$)이 정수가 아니기 때문이다. (0의 역원도 문제).
  - **실수 집합 $(\mathbb{R}, \times)$**: 0을 제외하면 군이 된다. (0은 곱셈 역원이 없음).

## 3. Subgroup & Lagrange's Theorem

- **Subgroup (부분군)**: 군 $G$의 부분집합 $H$가 $G$와 같은 연산에 대해 군의 조건을 만족하면, $H$는 $G$의 부분군이다.
- **Order (위수)**:
  - **Group Order $|G|$**: 집합 $G$의 원소의 개수.
  - **Element Order**: $a^n = e$ (항등원)가 되는 가장 작은 양의 정수 $n$.
- **[[Lagrange's Theorem]]**: 유한군 $G$의 부분군 $H$의 크기 $|H|$는 항상 전체 군의 크기 $|G|$의 **약수(Divisor)**이다.
  - **Coset (잉여류)**: 부분군 $H$에 원소 $g$를 연산하여 만든 집합 $gH = \{gh | h \in H\}$.
  - 증명 아이디어: Coset들이 전체 $G$를 겹치지 않고 균등하게 분할(Partition)하기 때문이다. 즉, $|G| = k \times |H|$.

# 2️⃣ Field (체)

연산이 2개(보통 덧셈, 곱셈) 정의된 구조이다. 우리가 익숙한 **사칙연산(+, -, x, /)이 모두 자유로운 구조**라고 보면 된다.

## 1. 정의

집합 $F$와 두 연산 $(+, \cdot)$에 대해 다음을 만족하면 **Field**이다.

1.  **Additive Group**: $(F, +)$는 **Abelian Group**이다. (항등원 0). $\to$ 뺄셈 가능.
2.  **Multiplicative Group**: $F \setminus \{0\}$은 곱셈($\cdot$)에 대해 **Abelian Group**이다. (항등원 1). $\to$ 나눗셈 가능.
    - **핵심**: 0을 제외한 **모든** 원소가 곱셈 역원을 가져야 한다.
3.  **Distributivity (분배법칙)**: 덧셈과 곱셈이 서로 잘 어울려야 한다. $a \cdot (b + c) = a \cdot b + a \cdot c$.

## 2. 예시와 반례

- **Field O**: 유리수($\mathbb{Q}$), 실수($\mathbb{R}$), 복소수($\mathbb{C}$).
- **Field X**: 정수($\mathbb{Z}$). (곱셈 역원 부재).
- **타원곡선(Elliptic Curve)**: 기하학적으로 정의된 특수한 덧셈 연산을 통해 Group을 이룬다. (Field 위에서 정의된 Group).

# 3️⃣ Finite Field (Galois Field)

암호학의 핵심 무대이다. 원소의 개수가 유한한 체를 말하며, 갈루아(Évariste Galois)의 이름을 따서 **Galois Field, $GF(q)$**라고 부른다. (갈루아는 20살에 결투로 요절하기 전날 밤 이 이론을 정리했다고 전해진다).

## 1. Prime Field ($GF(p)$)

원소의 개수 $q$가 소수(Prime) $p$인 경우, 단순히 **정수 모듈러 연산**만으로 Field를 만들 수 있다.

- **구성**: $\mathbb{Z}_p = \{0, 1, ..., p-1\}$.
- **연산**: 덧셈과 곱셈 모두 $p$로 나눈 나머지(Modulo $p$)를 취한다.
- **Characteristic (표수)**: 곱셈 항등원(1)을 몇 번 더해야 덧셈 항등원(0)이 되는가?
  - $GF(p)$의 표수는 $p$이다. ($1+1+...+1$ ($p$번) $\equiv 0$).
- **왜 소수여야 하는가?**
  - 만약 $n$이 합성수(예: 4)라면, $2 \times 2 = 4 \equiv 0 \pmod 4$.
  - 0이 아닌 두 수(2, 2)를 곱했는데 0이 나왔다. 이런 수를 **Zero Divisor (0인자)**라고 한다.
  - Zero Divisor가 있으면 역원이 존재할 수 없다. 따라서 **소수 $p$일 때만 $GF(p)$가 존재한다.**

## 2. Fermat's Little Theorem (페르마 소정리)

- 유한체 $GF(q)$의 0이 아닌 원소 $a$에 대해:
  $$ a^{q-1} \equiv 1 \pmod q $$
- 양변에 $a$를 곱하면: $a^q \equiv a \pmod q$.
- **의미**: 유한체 안에서는 거듭제곱을 계속하면 언젠가 자기 자신으로 돌아오며, 그 주기는 $q-1$의 약수이다. (라그랑주 정리의 응용).

# 4️⃣ Extension Field ($GF(p^m)$)

## 1. $GF(4)$는 존재하는가?

- $\mathbb{Z}_4$는 Field가 아니다(2의 역원이 없어서).
- 그렇다면 원소가 4개인 Field는 없는가? **있다.**
- **정리**: 유한체의 원소 개수 $q$는 항상 **소수의 거듭제곱 형태 ($q = p^m$)**여야 한다. ($p=2, m=2 \implies q=4$).
- 이를 **확장체(Extension Field)**라고 하며, 정수가 아닌 **다항식(Polynomial)**을 이용해 구성한다.
- **표수**: $GF(p^m)$의 표수도 여전히 $p$이다. (예: $GF(4)$의 원소는 2번 더하면 0이 된다).

## 2. Construction with Polynomials (다항식을 이용한 구성)

실수에서 $x^2+1=0$의 해 $i$를 도입해 복소수($\mathbb{C}$)를 만들듯, 유한체에서도 해가 없는 방정식을 이용해 차원을 확장한다.

1.  **Polynomial Ring**: 계수가 $GF(p)$인 다항식들의 집합.
2.  **Irreducible Polynomial (기약 다항식)**:
    - 해당 필드에서 인수분해되지 않는 다항식.
    - 예: $GF(2)$에서 $x^2+x+1$.
      - 0 대입 $\to 1$, 1 대입 $\to 1+1+1 \equiv 1$. (근이 없으므로 기약).
    - 반면 $x^2+1 = (x+1)^2$ 이므로 기약이 아님 ($1+1 \equiv 0$ 이므로).
3.  **Extension**: $m$차 기약 다항식 $P(x)$를 찾아, 다항식들을 $P(x)$로 나눈 **나머지들의 집합**을 만든다.
    - $GF(p^m) \cong GF(p)[x] / P(x)$.

## 3. Example: $GF(2^3) = GF(8)$

- **기약 다항식**: $P(x) = x^3 + x + 1$ (in $GF(2)$).
- **원소**: $a_2 x^2 + a_1 x + a_0$ 꼴 (계수 $\in \{0, 1\}$). 총 $2^3=8$개.
- **연산**:
  - **덧셈**: 다항식 덧셈 (계수는 mod 2, 즉 XOR). 벡터 덧셈과 같다.
  - **곱셈**: 다항식 곱셈 후 $P(x)$로 나눈 나머지.
- **Freshman's Dream**: 표수가 $p$인 필드에서는 $(A+B)^p = A^p + B^p$가 성립한다. (중간항들이 $p$의 배수가 되어 사라짐).

## 4. Representation (표현법)

확장체 원소를 표현하는 방법은 크게 두 가지가 있으며, 상호 변환 가능하다.

1.  **Additive (Polynomial/Vector) Representation**:
    - $\{0, 1, x, x+1, ...\}$ 또는 벡터 $(0,0,1), (0,1,1)$ 등.
    - **덧셈** 연산에 유리하다. (단순 XOR).
2.  **Multiplicative (Power) Representation**:
    - $\{0, 1, \alpha, \alpha^2, ..., \alpha^{q-2}\}$. ($\alpha$는 Primitive Element).
    - **곱셈** 연산에 유리하다. (지수끼리 덧셈: $\alpha^i \cdot \alpha^j = \alpha^{(i+j) \pmod{q-1}}$).
    - **덧셈**을 하려면? "Zech's Logarithm" 테이블 등을 이용해야 해서 불편하다.

# 5️⃣ Primitive Element & Minimal Polynomial

## 1. Primitive Element (원시 원소)

- **정의**: 유한체 $GF(q)$의 0이 아닌 모든 원소를 자신의 거듭제곱으로 만들어낼 수 있는 원소. **Generator(생성자)**라고도 한다.
- **성질**: $GF(q)$의 곱셈군은 항상 **Cyclic Group(순환군)**이므로, Primitive Element가 반드시 존재한다.
- **활용**: 이산 대수 문제(DLP)의 기반이 된다.

## 2. Minimal Polynomial (최소 다항식)

- 원소 $\beta \in GF(p^m)$에 대해, $\beta$를 근으로 갖는 $GF(p)$ 계수의 다항식 중 차수가 가장 낮은 모닉 다항식 $m_\beta(x)$.
- **성질**:
  - $m_\beta(x)$는 기약 다항식이다.
  - **Conjugates (켤레)**: $\beta$가 근이면 $\beta^p, \beta^{p^2}, ...$도 모두 근이다. (Frobenius Automorphism 성질).
  - 이 켤레근들을 **Cyclotomic Coset**이라고 부른다.
  - $x^{p^m}-x$ (모든 원소를 근으로 갖는 식)는 모든 Minimal Polynomial들의 곱으로 인수분해된다.

## 3. Primitive Polynomial (원시 다항식)

- 근이 Primitive Element인 기약 다항식.
- 이 다항식으로 필드를 확장하면, $x$ 자체가 Primitive Element가 되어 연산(특히 지수 표현)이 매우 편리해진다.
- 예: $x^4+x+1$은 Primitive이지만, $x^4+x^3+x^2+x+1$은 Irreducible이지만 Primitive는 아니다. (주기가 짧음).

> **요약**: 암호학자들은 $GF(2^8)$ (AES) 처럼 컴퓨터가 처리하기 쉬운(Byte 단위) 필드를 선호한다. 이때 다항식 연산(덧셈=XOR, 곱셈=Shift&Mod)을 통해 고속 암호화가 가능하다.
