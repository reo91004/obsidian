---
type: concept
status: archive
created: 2026-01-18T18:01
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

# 0️⃣ Introduction to [[Lattice]]

**Lattice-based Cryptography** (격자 기반 암호)는 현대 암호학의 '슈퍼스타'와 같은 존재이다. 양자 컴퓨터를 대비하는 PQC의 핵심일 뿐만 아니라, 동형암호(FHE) 등 암호학의 오랜 난제들을 해결하는 **Versatile Tool** (다재다능한 도구)이다.

## 왜 Lattice인가?

1.  **Post-Quantum Cryptography (PQC)**: 소인수분해(RSA)나 이산대수(ECC)는 양자 컴퓨터의 쇼어(Shor) 알고리즘으로 풀리지만, 격자 문제는 풀리지 않는다고 알려져 있다.
2.  **Worst-case Hardness**: 격자 암호의 가장 강력한 특징. "평균적인 경우(Average-case)를 푸는 것이 최악의 경우(Worst-case)를 푸는 것만큼 어렵다"는 **Reduction**이 증명되어 있다. 즉, **운 좋게 풀리는 취약한 키(Weak Key)가 존재하지 않는다.** (Average-case $\le$ Worst-case).
3.  **Efficiency**: 기본 연산이 행렬과 벡터의 덧셈/곱셈(선형 대수)으로 이루어져 있어 구현이 매우 효율적이다. (특히 Ring 구조 사용 시 파라미터 사이즈와 속도 개선).
4.  **Versatility**: 단순 암호화를 넘어 **[[FHE]]** (완전 동형 암호), IBE, ABE 등 다양한 고급 암호 스킴을 구현할 수 있다.

# 1️⃣ What is Lattice? (격자란 무엇인가?)

## 1. 정의

격자 (Lattice)는 $n$차원 실수 공간 $\mathbb{R}^n$ 위의 **이산적인 점** (Discrete Points)들의 집합으로, 덧셈에 대해 닫혀 있는 부분군(Additive Subgroup)이다.

- 수학적으로는 $n$개의 선형 독립인 기저 벡터 (Basis Vector) $\mathbf{v}_1, \dots, \mathbf{v}_n$들의 **정수 선형 결합** (Integer Linear Combination)으로 생성된다.
$$ L = \left\{ \sum_{i=1}^n a_i \mathbf{v}_i \mid a_i \in \mathbb{Z} \right\} $$

## 2. Good Basis vs Bad Basis

동일한 격자점($L$)을 생성하는 기저(Basis)는 무수히 많다.

- **Good Basis**: 벡터들이 서로 **직교** (Orthogonal)에 가깝고 길이가 **짧다**. 격자의 구조를 파악하고 문제를 풀기 쉽다.
- **Bad Basis**: 벡터들이 서로 **평행**에 가깝고 길이가 **매우 길다**. 격자의 구조를 파악하기 어렵다.
- **암호학적 활용**: **Trapdoor** 방식으로 사용한다.
    - **Secret Key**: Good Basis (문제 풀기 쉬움).
    - **Public Key**: Bad Basis (랜덤하게 생성된 행렬, 문제 풀기 어려움).

## 3. [[Successive Minima]] ($\lambda_i$)

격자의 기하학적 성질을 정의하는 중요한 파라미터. 단순히 길이 순서가 아니라 **선형 독립성**이 기준이다.

- **$\lambda_1(L)$**: 원점(0)을 제외하고 **가장 짧은 벡터**의 길이.
- **$\lambda_i(L)$**: 기존 $1 \sim (i-1)$번째 벡터들과 **선형 독립(Linearly Independent)**인 벡터들 중 가장 짧은 벡터의 길이.
- 즉, $\lambda_n$은 격자 공간을 형성하는 $n$개의 독립적인 축들의 최소 길이를 의미한다. 이를 이용해 격자점들이 포함되는 최소한의 **구(Sphere)** 크기를 가늠할 수 있다.

# 2️⃣ Hard Problems (격자 난제)

격자 암호는 다음 문제들이 계산적으로 매우 어렵다(NP-hard 등)는 사실에 기반한다. 특히 **Approximate Factor ($\gamma$)** 값에 따라 난이도가 결정된다.

## 1. [[SVP]] (Shortest Vector Problem)

- **문제**: 격자 내에서 0이 아닌 **가장 짧은 벡터**($\mathbf{v}$ s.t. $|\|\mathbf{v}\|| = \lambda_1$)를 찾아라.
- **난이도 ($\gamma$)**:
    - **$\\gamma = 1$ (Exact)**: NP-hard.
    - **$\\gamma \approx 2^n$ (Exponential)**: LLL 알고리즘으로 다항 시간 내 풀이 가능 (Easy).
    - **$\\gamma \approx \text{poly}(n)$ (Polynomial)**: 암호학적 난제 (Crypto Hardness). LLL로 풀리지 않으며 지수 시간이 소요됨.

## 2. [[SIVP]] (Shortest Independent Vectors Problem)

- **문제**: 격자에서 $n$개의 **선형 독립(Linearly Independent)**인 벡터들의 집합 {$\mathbf{v}_1, \dots, \mathbf{v}_n$}을 찾아라. 단, 가장 긴 벡터의 길이가 $\lambda_n$의 $\gamma$배 이하여야 한다.
$$ \max_{i} \|\mathbf{v}_i\| \le \gamma \cdot \lambda_n(L) $$
- **중요성**: **LWE 문제의 안전성 기반**. "LWE 문제를 풀 수 있다면 SIVP 문제(Worst-case)를 풀 수 있다"는 양자 리덕션(Quantum Reduction)이 존재한다.

## 3. [[CVP]]와 [[BDD]]

### CVP (Closest Vector Problem)

- **문제**: 격자 위에 있지 않은 임의의 타겟 점 $\mathbf{t} \in \mathbb{R}^n$이 주어졌을 때, 이와 **가장 가까운 격자점**을 찾아라.
- **특징**: Bad Basis만 주어진 경우, 타겟 점 주변의 격자점을 찾는 것은 매우 어렵다. 일반적으로 SVP보다 어렵다고 알려져 있다.

### BDD (Bounded Distance Decoding)

- **문제**: CVP의 변형으로, 타겟 점 $\mathbf{t}$가 격자점으로부터 **일정 거리(예: $\lambda_1/2$) 이내**에 있다는 **약속(Promise)**이 주어진 문제.
- **Coding Theory Analogy**:
    - **Lattice Point** = Valid **Codeword**.
    - **Target Point** = Noisy Received Signal.
    - **Sphere Packing**: 각 격자점을 중심으로 서로 겹치지 않는 구(Sphere)들이 있다고 할 때, 타겟 점이 어느 구 안에 들어가는지를 찾는 문제(Decoding)와 같다.
- **암호학적 의미**: LWE의 복호화 과정은 노이즈를 제거하고 원래의 격자점을 찾는 **BDD** 문제와 동일하다. 랜덤 기저(Bad Basis)에서는 이 구의 중심을 찾기 어렵다.

## 4. Lattice Reduction ([[LLL Algorithm]])

- 1982년 Lenstra, Lenstra, Lovász가 제안.
- Bad Basis를 입력받아 **"적당히 짧은(Moderately Short)"** 기저(Better Basis)를 찾아내는 알고리즘.
- $\gamma \approx 2^n$ 수준의 근사해를 다항 시간에 찾을 수 있다.
- **보안 파라미터 설정**: 공격자가 LLL이나 그 변형(BKZ)을 써도 $2^{128}$ 이상의 연산량이 들도록 차원 $n$을 충분히 크게 잡아야 한다.

# 3️⃣ [[LWE]] (Learning With Errors)

2005년 Oded Regev가 제안. 현대 격자 암호의 표준 문제.

## 1. 기원: [[LPN]] (Learning Parity with Noise)

LWE는 코딩 이론의 **LPN** 문제에서 확장되었다.

- **LPN**: $\mathbb{Z}_2$ (0/1 비트) 상의 선형 방정식 + **Bernoulli Noise** (비트 반전).
- **LWE**: $\mathbb{Z}_q$ (큰 정수환) 상의 선형 방정식 + **Gaussian Noise** (작은값).
- *참고*: 코딩 이론의 HQC 같은 암호는 LPN과 유사한 구조를 가진다.

## 2. 정의 (Noisy Linear Equation)

$$ \mathbf{b} = \mathbf{A}\mathbf{s} + \mathbf{e} \pmod q $$

- $\mathbf{A}$: $m \times n$ 랜덤 행렬 (Public).
- $\mathbf{s}$: $n \times 1$ 비밀 벡터 (Secret).
- $\mathbf{e}$: 작은 에러 벡터 (Error, Gaussian 분포).
- $\mathbf{b}$: $m \times 1$ 결과 벡터 (LWE Sample).

**문제**: $\mathbf{A}, \mathbf{b}$를 알 때 $\mathbf{s}$를 구할 수 있는가? (에러가 없으면 가우스 소거법으로 쉽지만, 있으면 매우 어렵다).

## 3. Variants

- **Search LWE**: $\mathbf{s}$를 찾는 문제.
- **Decision LWE**: 주어진 $(\mathbf{A}, \mathbf{b})$가 LWE 샘플인지, 아니면 완전 랜덤(Uniform)인지 **구분(Distinguish)**하는 문제. (LWE 샘플은 **Pseudorandom**하다).
- **[[LWR]] (Learning With Rounding)**: 에러 $e$를 랜덤하게 더하는 대신, **Rounding(반올림/버림)**을 통해 결정론적(Deterministic)으로 노이즈를 발생시키는 방식.
    $$ b = \lfloor \frac{p}{q} \cdot \mathbf{A}\mathbf{s} \rceil $$
    - 에러 샘플링 과정이 없어 구현이 더 효율적이다.

# 4️⃣ Cryptography Construction (암호 구현)

기본 원리: **One-Time Pad**. LWE 샘플($\approx$ 난수)에 메시지를 더해 숨긴다.

## 1. Encoding Principle (중요!)

암호화 수식: $\text{Cipher} \approx \text{Random} + \text{Error} + \text{Message}$

여기서 **Error ($e$)**와 **Message ($\mu$)**가 섞이지 않게 하는 것이 핵심이다.

1.  **에러 ($e$)의 위치: 하위 비트 (LSB)**
    - $e$는 아주 작은 값 (예: $\{-1, 0, 1\}$).
    - $q$가 클 때, 이 작은 숫자는 2진수로 보면 **오른쪽 끝(LSB)**에서만 변화를 준다.
    - *교수님 표현*: **"밑에서만 살랑거리는 작은 애들"** (하위 비트 몇 개만 0과 1로 왔다 갔다 함).
2.  **메시지 ($\mu$)의 위치: 상위 비트 (MSB)**
    - 메시지 $\mu$는 0 또는 1 (1비트 정보).
    - 이를 그냥 더하면(+0 or +1) 에러와 섞여서 구분이 안 된다.
    - 따라서 아주 큰 수인 **$\frac{q}{2}$를 곱해서 왼쪽 끝(MSB)으로 보내버린다.**
    - *비유*: **트럭(메시지)**과 **개미(에러)**. 트럭은 너무 커서 개미가 밟혀도 티가 안 나고, 서로 노는 영역이 달라서(자릿수가 달라서) 간섭하지 않는다.

### 왜 $\frac{q}{2}$인가? (Separation)

복호화 시, 공유된 비밀(Shared Secret)을 제거하면 남는 값은 다음과 같다.

$$ \text{Decrypted Value} \approx e + \mu \cdot \frac{q}{2} $$

- **Case 1: $\mu = 0$**
    - 값: $e$ (아주 작은 노이즈)
    - 위치: **0 근처**
    - 비트: `00...0001` (상위 비트 0)
- **Case 2: $\mu = 1$**
    - 값: $\frac{q}{2} + e$ (중간 지점)
    - 위치: **$q/2$ 근처**
    - 비트: `10...0001` (최상위 비트가 1이 됨)

> 즉, 결과값이 0 근처인지 $q/2$ 근처인지만 확인하면 메시지를 완벽하게 복구할 수 있다. 이것이 에러와 메시지가 **"안 겹친다"**는 의미이다.

## 2. Regev Encryption (Public Key Protocol)

위 원리를 적용한 실제 공개키 암호 프로토콜.

- **KeyGen (Alice)**:
    - **SK**: $\mathbf{s}$ (비밀 벡터).
    - **PK**: $(\mathbf{A}, \mathbf{b} = \mathbf{A}\mathbf{s} + \mathbf{e})$.
- **Encryption (Bob $\to$ Alice)**:
    - 앨리스의 PK $(\mathbf{A}, \mathbf{b})$를 이용한다.
    - 1회용 랜덤 벡터 $\mathbf{r}$ (작은 값, 0 or 1)을 생성한다.
    - **Hint ($\mathbf{u}$)**: $\mathbf{u} = \mathbf{A}^T \mathbf{r}$. (격자점 정보를 보냄).
    - **Payload ($v'$)**: $\mathbf{b}^T \mathbf{r}$ (LWE 샘플의 조합)에 메시지를 인코딩하여 더한다.
      $$ v' = \mathbf{b}^T \mathbf{r} + \mu \cdot \lfloor q/2 \rfloor $$
    - **Ciphertext**: $(\mathbf{u}, v')$.
- **Decryption (Alice)**:
    - 비밀키 $\mathbf{s}$와 힌트 $\mathbf{u}$를 이용해 마스크를 벗겨낸다.
      $$ v' - \mathbf{s}^T \mathbf{u} = (\mathbf{b}^T \mathbf{r} + \mu \frac{q}{2}) - \mathbf{s}^T (\mathbf{A}^T \mathbf{r}) $$
    - 식을 전개하면 $\mathbf{b} = \mathbf{A}\mathbf{s} + \mathbf{e}$ 이므로:
      $$ = (\mathbf{s}^T \mathbf{A}^T + \mathbf{e}^T)\mathbf{r} + \mu \frac{q}{2} - \mathbf{s}^T \mathbf{A}^T \mathbf{r} $$
      $$ = \mathbf{e}^T \mathbf{r} + \mu \frac{q}{2} $$
    - **최종 판별**: 남은 값 $\mathbf{e}^T \mathbf{r}$은 "작은 값 $\times$ 작은 값"이므로 여전히 작은 노이즈이다.
    - 따라서 결과값이 $q/2$에 가까우면 1, 0에 가까우면 0으로 복호화한다.

## 3. Attacks (공격 기법)

1.  **Algebraic Attack (Linearization)**:
    - 만약 에러 $e$의 범위가 너무 작다면 (예: $e \in \{0, 1, -1\}$), $e(e-1)(e+1)=0$ 같은 식을 이용해 선형화(Linearization) 공격이 가능하다.
    - 이를 막기 위해 충분한 크기의 $n$과 에러 분산이 필요하다.
2.  **Lattice Reduction (Primal/Dual Attack)**:
    - 암호문을 해독하는 것을 CVP/SVP 문제로 변환하여 LLL/BKZ 알고리즘으로 공격한다.
    - 이 공격 비용이 $2^{128}$ 이상이 되도록 파라미터를 설정한다.

![[10 Lattice-1768787584213.jpg]]

> **요약**: 격자 암호는 **"선형 시스템에 노이즈를 섞으면(LWE), 평균적인 경우에도 풀기 어렵다"**는 사실을 이용한다. 암호화 시 메시지는 **트럭($q/2$)**처럼 상위 비트에, 에러는 **개미($e$)**처럼 하위 비트에 배치하여 서로 간섭하지 않게 하는 것이 핵심 원리이다.
