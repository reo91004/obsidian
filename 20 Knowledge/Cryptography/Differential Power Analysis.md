---
type: concept
status: stable
context:
created: 2026-01-20
updated: 2026-03-15T19:39
aliases:
  - DPA
  - 차분 전력 분석
  - PACS 07 DPA
source: PACS Lecture
prefer-view: edit
tags:
---

# 0️⃣ Introduction to [[DPA]] (Differential Power Analysis)

**DPA (Differential Power Analysis)**는 전력 소비의 **데이터 의존성(Data Dependency)**을 통계적으로 분석하여 비밀 키를 복구하는 공격 기법이다. 이전 세션에서 다룬 [[SPA]]가 **연산 의존성(Operation Dependency)**을 육안으로 관찰하는 방식이었다면, DPA는 수천~수만 개의 트레이스를 수집하여 **통계적 분석**을 수행한다.

## SPA vs DPA

| 특성            | [[SPA]]                   | [[DPA]]           |
| ------------- | ------------------------- | ----------------- |
| **활용하는 정보**   | Operation Dependency      | Data Dependency   |
| **필요 트레이스 수** | 매우 적음 (1~수 개)             | 많음 (수백~수만)        |
| **분석 방법**     | 시각적 검사, Cross-Correlation | 통계적 검정 (T-Test 등) |
| **전제 조건**     | 명령어 시퀀스 차이 존재             | 데이터에 따른 전력 차이 존재  |

#### **SPA (Operation Dependency)**

- **핵심:** "지금 CPU가 **무슨 일**을 하고 있는가?"
- **원리:** 덧셈을 할 때와 곱셈을 할 때, 혹은 메모리를 읽을 때와 쓸 때 사용하는 회로가 다르다. 따라서 전력 소비 패턴이 눈에 띄게 다르다.
- **조건 (명령어 시퀀스 차이):** 방금 실습한 비밀번호 예제처럼, 값이 틀리면 `return` 해버리고(경로 A), 맞으면 `next`로 넘어가는(경로 B) 식이다. 실행하는 **코드의 흐름(Flow)** 자체가 바뀌기 때문에 파형이 크게 달라진다.

#### **DPA (Data Dependency)**

- **핵심:** "지금 CPU가 **어떤 값**을 처리하고 있는가?"
- **원리:** 똑같은 '데이터 이동(MOV)' 명령어를 실행하더라도, 옮기는 숫자가 `0`(00000000)이냐 `255`(11111111)냐에 따라 전력 소비량이 아주 미세하게 다르다(비트가 뒤집힐 때 전력을 소모하므로).
- **조건 (데이터에 따른 전력 차이):** 실행하는 명령어는 똑같다. 하지만 그 안에서 처리되는 **데이터 값(0 또는 1)** 에 따라 발생하는 미세한 전력 차이를 통계적으로 찾아낸다.

## 왜 DPA인가?

1.  **자동화된 공격**: 육안 검사나 전문 지식 없이 통계적으로 키를 복구할 수 있다.
2.  **노이즈 극복**: 개별 트레이스에서는 보이지 않는 미세한 차이도 다수의 트레이스를 평균하면 드러난다.
3.  **범용성**: 소프트웨어/하드웨어 구현 모두에 적용 가능하다.
4.  **Divide and Conquer**: 128비트 키를 한 번에 추측하지 않고, 8비트씩 16번 공격하여 복잡도를 $2^{128}$에서 $16 \times 2^8$로 줄인다.

# 1️⃣ 전력 소비의 구성 요소

## 1. 전력 값의 분해

암호 장치의 전력 소비 트레이스에서 **매 샘플 포인트**의 값은 다음과 같이 분해할 수 있다:

$$
P = P_{\text{const}} + P_{\text{op}} + P_{\text{data}} + P_{\text{el.noise}}
$$

| 성분                    | 의미            | 특성                                 |
| --------------------- | ------------- | ---------------------------------- |
| $P_{\text{const}}$    | **상수 성분**     | 반도체의 정적 전력 소비. 데이터/연산과 무관. DC 오프셋. |
| $P_{\text{op}}$       | **연산 의존 성분**  | 실행되는 명령어(ADD, XOR, MOV 등)에 따라 변화   |
| $P_{\text{data}}$     | **데이터 의존 성분** | 처리되는 데이터 값(피연산자)에 따라 변화            |
| $P_{\text{el.noise}}$ | **전자 노이즈**    | 아날로그 신호의 디지털 변환 과정에서 발생하는 랜덤 노이즈   |

## 2. 전자 노이즈의 특성

동일한 데이터, 동일한 연산으로 측정을 반복해도 **전력 트레이스는 완전히 동일하지 않다**.

- **원인**: 아날로그 신호를 디지털로 샘플링하는 과정에서 발생하는 전자 노이즈
- **분포**: 단일 샘플 포인트의 히스토그램을 그리면 **가우시안(정규) 분포**를 따른다.

$$
P_{\text{el.noise}} \sim \mathcal{N}(0, \sigma^2)
$$

특정 샘플 포인트(예: 362 ns)에서 여러 트레이스의 값을 수집하여 히스토그램을 그리면:

- **평균($\mu$)**: 샘플 평균으로 추정
- **표준편차($\sigma$)**: 샘플 표준편차로 추정
- 이 두 파라미터로 해당 포인트의 분포를 완전히 특성화할 수 있다.

> **중요**: 노이즈가 가우시안을 따른다는 가정은 DPA 분석을 단순화하는 핵심 가정이다. 실제로는 완벽한 가우시안이 아닐 수 있지만, 이 가정으로 분석이 크게 용이해진다.

## 3. 연산 의존성 vs 데이터 의존성

### Operation Dependency ($P_{\text{op}}$)

데이터를 고정하고 연산만 바꿔가며 측정하면:

- `ADD` 명령어: 특정 평균과 분산을 가진 분포
- `XOR` 명령어: 다른 평균과 분산을 가진 분포
- `MOV` 명령어: 또 다른 분포

각 연산의 전력 분포는 **서로 다른 평균**을 가지며, 이것이 [[SPA]] 공격의 기반이다.

### Data Dependency ($P_{\text{data}}$)

연산을 고정하고(예: `MOV`) 데이터만 바꿔가며 측정하면:

- 데이터 값에 따라 전력 소비량이 미세하게 변화한다.
- 특히 데이터의 **[[Hamming Weight]]**와 전력 소비 사이에 상관관계가 있다.

> **핵심**: 연산 의존성과 데이터 의존성은 **장치마다, 샘플 포인트마다** 다르다. 특정 장치의 특정 시점에서 관찰된 관계가 다른 장치나 다른 시점에 그대로 적용되지 않는다.

## 4. [[Hamming Weight]] 분포와 전력 소비

### 균등 분포 데이터의 전력 히스토그램

연산을 고정하고, **균등 분포(Uniformly Distributed)**의 랜덤 8비트 데이터를 처리하면서 특정 샘플 포인트의 전력 값을 히스토그램으로 그리면:

```
       HW=0  HW=1  HW=2  HW=3  HW=4  HW=5  HW=6  HW=7  HW=8
        ▲     ▲     ▲     ▲     ▲     ▲     ▲     ▲     ▲
       tiny small  med  large MAX  large  med  small tiny
         \   |    /   \   |   /   \    |   /
          \___|___/     \___|___/     \___|___/
              Combined Histogram (9 overlapping Gaussians)
```

**9개의 정규분포가 중첩**된 형태가 관찰된다:
- Hamming Weight가 0 또는 8인 경우: 가장 드물다 (확률 $1/256$)
- Hamming Weight가 4인 경우: 가장 흔하다 (확률 $\binom{8}{4}/256 = 70/256$)

### Hamming Weight별 분류

전력 값을 Hamming Weight에 따라 9개 그룹으로 분류하면, 각 그룹은 **서로 다른 평균**을 가진 정규분포를 형성한다.

| Hamming Weight | 확률 | 전력 분포 평균 |
|----------------|------|---------------|
| 0 | $1/256$ | $\mu_0$ |
| 1 | $8/256$ | $\mu_1$ |
| 2 | $28/256$ | $\mu_2$ |
| 3 | $56/256$ | $\mu_3$ |
| 4 | $70/256$ | $\mu_4$ |
| 5 | $56/256$ | $\mu_5$ |
| 6 | $28/256$ | $\mu_6$ |
| 7 | $8/256$ | $\mu_7$ |
| 8 | $1/256$ | $\mu_8$ |

> **누출이 심한 장치**: 위 그림처럼 9개의 피크가 명확히 구분된다.
> **최신 ARM 프로세서**: 피크들이 겹쳐서 육안으로 구분하기 어렵지만, **분류 후 개별 플롯**하면 여전히 평균 차이가 존재한다. 공격이 불가능한 것이 아니라, 더 많은 트레이스가 필요하다는 의미이다.

### 비례 vs 역비례 관계

특정 장치의 특정 샘플 포인트에서:

- Hamming Weight가 높을수록 전력 소비가 **높을 수도** 있고
- Hamming Weight가 높을수록 전력 소비가 **낮을 수도** 있다 (역비례)

> 이 관계는 **샘플 포인트마다 다르다**. 한 포인트에서 비례 관계가 관찰되어도 다른 포인트에서는 역비례일 수 있다. 따라서 공격은 **모든 샘플 포인트에 대해 개별적으로** 수행해야 한다.

## 5. Exploitable Part와 Switching Noise

### 공격 관점에서의 재분류

공격자는 전력의 모든 성분을 활용하지 않는다. 공격에서 **실제로 활용하는 부분**과 **무시하는 부분**으로 재분류한다:

$$
P_{\text{op}} + P_{\text{data}} = P_{\text{exp}} + P_{\text{sw}}
$$

| 성분 | 의미 |
|------|------|
| $P_{\text{exp}}$ (Exploitable) | 공격에서 활용하는 부분 |
| $P_{\text{sw}}$ (Switching Noise) | 공격에서 무시하는 부분 |

### 공격 유형에 따른 분류

**SPA 공격**:
- $P_{\text{exp}} = P_{\text{op}}$ (연산 의존 부분)
- $P_{\text{sw}} = P_{\text{data}}$ (데이터 의존 부분은 무시)

**DPA 공격 (LSB 기반)**:
- $P_{\text{exp}}$ = 중간값의 LSB에 의존하는 부분
- $P_{\text{sw}}$ = 연산 의존 부분 + 다른 7비트에 의존하는 부분

### 최종 전력 모델

$$
P = P_{\text{const}} + P_{\text{exp}} + P_{\text{sw}} + P_{\text{el.noise}}
$$

- $P_{\text{sw}} + P_{\text{el.noise}}$를 합쳐서 **총 노이즈**로 모델링한다.
- 총 노이즈는 가우시안 분포를 따른다고 가정한다.

$$
P_{\text{sw}} + P_{\text{el.noise}} \sim \mathcal{N}(\mu_{\text{noise}}, \sigma_{\text{noise}}^2)
$$

# 2️⃣ [[SNR]] (Signal-to-Noise Ratio)

## 1. SNR의 정의

전력 분석 공격에서 **SNR**은 다음과 같이 정의된다:

$$
\text{SNR} = \frac{\text{Var}(\text{Signal})}{\text{Var}(\text{Noise})} = \frac{\text{Var}(P_{\text{exp}})}{\text{Var}(P_{\text{sw}} + P_{\text{el.noise}})}
$$

- **Signal의 분산**: Exploitable part가 얼마나 변하는가 (그룹 간 차이)
- **Noise의 분산**: 무시하는 부분과 전자 노이즈의 변동폭 (그룹 내 변동)

SNR이 높을수록:

- 신호가 노이즈보다 강하다.
- 더 적은 트레이스로 공격이 성공한다.

## 2. SNR 계산 방법

### Step 1: 트레이스 분류

공격 모델(예: Hamming Weight, LSB)에 따라 트레이스를 **그룹으로 분류**한다.

Hamming Weight 모델의 경우:

- 그룹 0: HW = 0인 트레이스들
- 그룹 1: HW = 1인 트레이스들
- ...
- 그룹 8: HW = 8인 트레이스들

### Step 2: 평균 트레이스 계산

각 그룹에서 **평균 트레이스(Mean Trace)**를 계산한다:

$$
\mathbf{m}_i = \frac{1}{|G_i|} \sum_{t \in G_i} \mathbf{t}
$$

평균을 취하면 노이즈(전자 노이즈 + Switching Noise)가 상쇄되고, **신호 성분만 남는다**.

### Step 3: 신호의 분산 (Variance of Means)

평균 트레이스들 $\mathbf{m}_0, \mathbf{m}_1, \ldots, \mathbf{m}_8$ 사이의 **분산**을 계산한다:

$$
\text{Var}(\text{Signal}) = \text{Var}(\mathbf{m}_0, \mathbf{m}_1, \ldots, \mathbf{m}_8)
$$

이것은 "그룹 간 평균이 얼마나 다른가"를 측정한다.

### Step 4: 노이즈의 분산 (Mean of Variances)

각 그룹 내에서 **분산 트레이스**를 계산한다:

$$
\mathbf{v}_i = \text{Var}(\text{traces in } G_i)
$$

그런 다음 분산 트레이스들의 **평균**을 계산한다:

$$
\text{Var}(\text{Noise}) = \text{Mean}(\mathbf{v}_0, \mathbf{v}_1, \ldots, \mathbf{v}_8)
$$

### 최종 SNR 공식

$$
\boxed{\text{SNR} = \frac{\text{Variance of Means}}{\text{Mean of Variances}}}
$$

> **기억법**: "Variance of Means over Mean of Variances"

## 3. SNR 예시

### Hamming Weight 기반 SNR

**누출이 심한 구형 장치**:
- 평균 트레이스들이 육안으로 구분될 정도로 다르다.
- SNR 피크 값: ~30

### LSB 기반 SNR

동일한 장치에서 LSB만 고려하면:

- 2개 그룹만 사용 (HW는 9개 그룹)
- 다른 7비트의 영향이 Switching Noise로 작용
- SNR 피크 값: ~0.15

> **비교**: Hamming Weight 기반 SNR(30)이 LSB 기반 SNR(0.15)보다 **200배** 높다. 이는 공격 시 필요한 트레이스 수에 직접적인 영향을 미친다.

## 4. SNR의 활용

### 키 없이 SNR 계산하기

**중요한 발견**: S-box 출력에 대한 SNR과 평문에 대한 SNR은 **동일하다**.

- S-box 출력 = S-box(평문 $\oplus$ 키)
- 특정 키에 대해, S-box 출력의 값 분포는 평문의 값 분포와 **일대일 대응**
- 그룹의 **내용물은 같고**, 그룹 **번호만 다르다**

따라서:

- **키를 몰라도** 평문 값으로 그룹을 나누어 SNR을 계산할 수 있다.
- 이 SNR은 전력 트레이스의 어느 시점에서 **평문/S-box 출력이 처리되는지** 알려준다.
- 평문 입력, AddRoundKey 출력, SubBytes 출력, ShiftRows, MixColumns 등의 위치를 모두 파악 가능하다.

# 3️⃣ [[DPA]] (Differential Power Analysis) 공격

## 1. 공격 개요

DPA는 다음과 같은 상황에서 수행된다:

**공격자가 아는 것**:
- 평문 또는 암호문
- 해당 암호화/복호화의 전력 트레이스

**공격자가 모르는 것**:
- 비밀 키

**목표**:
- 통계적 분석을 통해 비밀 키를 복구

### 공격 가능한 조합

| 연산 | 아는 것 | 공격 대상 라운드 |
|------|---------|-----------------|
| 암호화 | 평문 | 첫 번째 라운드 |
| 암호화 | 암호문 | 마지막 라운드 |
| 복호화 | 암호문 | 첫 번째 라운드 |
| 복호화 | 평문 | 마지막 라운드 |

## 2. Classical DPA 알고리즘

### 공격 설정

- $N$개의 트레이스와 대응하는 평문 $P_1, P_2, \ldots, P_N$
- 각 트레이스는 $T$개의 샘플 포인트를 가짐
- 특정 샘플 포인트 $t$에서의 전력 값: $V_1[t], V_2[t], \ldots, V_N[t]$

### 알고리즘

```
for each key candidate K in [0, 255]:
    # Step 1: 중간값 계산
    for i = 1 to N:
        intermediate[i] = S-box(P[i] XOR K)
        selection_bit[i] = LSB(intermediate[i])

    # Step 2: 두 그룹으로 분류
    Group_0 = {V[i] : selection_bit[i] == 0}
    Group_1 = {V[i] : selection_bit[i] == 1}

    # Step 3: 통계 검정
    DoM[K] = |Mean(Group_1) - Mean(Group_0)|
    # 또는
    T_stat[K] = T-Test(Group_0, Group_1)

# Step 4: 최대값 선택
Correct_Key = argmax(DoM) or argmax(|T_stat|)
```

### 시각적 표현

```
평문 바이트:  [12] [3D] [A5] [7C] [02] ...
                ↓    ↓    ↓    ↓    ↓
              K=00 추측 → S-box 계산
                ↓    ↓    ↓    ↓    ↓
S-box 출력:   [C9] [27] [06] [10] [77] ...
                ↓    ↓    ↓    ↓    ↓
LSB 추출:     [ 1] [ 1] [ 0] [ 0] [ 1] ...
                ↓    ↓    ↓    ↓    ↓
              G1   G1   G0   G0   G1  → 그룹 분류

전력 값:      [V1] [V2] [V3] [V4] [V5] ...
                ↓         ↓
              Mean(G1) - Mean(G0) → 차이 계산
```

## 3. 왜 S-box 출력인가? (입력이 아닌 이유)

### S-box 입력의 LSB 문제

S-box 입력 = 평문 $\oplus$ 키

$$
\text{Input} = (P_7, P_6, \ldots, P_1, P_0) \oplus (K_7, K_6, \ldots, K_1, K_0)
$$

LSB만 고려하면:

$$
\text{LSB(Input)} = P_0 \oplus K_0
$$

**문제점**:
- $K_0 = 0$이면: LSB = $P_0$ → 평문에 따라 그룹 결정
- $K_0 = 1$이면: LSB = $\overline{P_0}$ → 그룹이 **반대로** 결정

키를 바꿔봤자 **그룹의 내용물만 교환**될 뿐, 평균 차이의 **절대값**은 동일하다.

$$
|\text{DoM}(K=0)| = |\text{DoM}(K=1)| = \cdots = |\text{DoM}(K=255)|
$$

따라서 어떤 키가 정답인지 구분할 수 없다. (극단적으로 $K_0$ 한 비트만 알 수 있음)

### S-box의 비선형성

S-box는 **비선형 함수**이다 (Galois Field에서의 역원 + Affine 변환).

S-box 출력의 한 비트는 **모든 입력 비트**에 의존한다:

$$
\text{LSB}(S(x)) = f(x_7, x_6, x_5, x_4, x_3, x_2, x_1, x_0)
$$

따라서:

- 키 후보 $K$가 틀리면 → 잘못된 그룹 분류 → 평균 차이 작음
- 키 후보 $K$가 맞으면 → 올바른 그룹 분류 → 평균 차이 큼 (피크 발생)

## 4. T-Test vs Difference of Means

### Difference of Means (DoM)

$$
\text{DoM} = |\bar{X}_1 - \bar{X}_0|
$$

단순히 두 그룹의 평균 차이를 계산한다.

### T-Test (Welch's t-test)

$$
t = \frac{\bar{X}_1 - \bar{X}_0}{\sqrt{\frac{s_1^2}{n_1} + \frac{s_0^2}{n_0}}}
$$

평균 차이를 **분산으로 정규화**한다.

**T-Test의 장점**:
- 임계값(threshold)을 정의할 수 있다 ($|t| > 4.5$ 등)
- 통계적 유의성을 판단할 수 있다.
- DoM은 스케일에 의존하지만, T-Test는 정규화되어 있다.

## 5. 소프트웨어 구현 공격 예시

### 실험 설정

- **장치**: 소프트웨어 AES 구현
- **트레이스 수**: 400개
- **샘플 포인트**: 110,000개

### MATLAB 코드 개요

```matlab
% 공격 대상: 키 바이트 1
target_byte = 1;

for K = 0:255
    % S-box 출력의 LSB 계산
    sbox_out = SBOX(plaintext(:, target_byte) XOR K);
    lsb = bitand(sbox_out, 1);

    % 그룹 분류
    idx_0 = find(lsb == 0);
    idx_1 = find(lsb == 1);

    % T-Test 계산
    T_stat(K+1, :) = t_test(traces(idx_0, :), traces(idx_1, :));
end

% 최대값 키 선택
[max_val, correct_K] = max(max(abs(T_stat), [], 2));
```

### 결과

- **400개 트레이스**만으로 정확한 키 복구 성공
- 올바른 키 후보(파란색)가 다른 후보들보다 명확히 높은 T-값을 보임
- 다른 비트(예: 2번째 비트)로 공격해도 동일한 키가 복구됨 (다른 시간대에서 피크 발생)

## 6. 하드웨어 구현 공격

### 소프트웨어 vs 하드웨어의 차이

**소프트웨어 (Pre-charged Bus)**:
- 매 클럭 사이클마다 버스가 0으로 리셋된다.
- 새 값이 버스에 로드될 때 **Hamming Weight**에 비례하는 전력 소비
- **단일 비트 모델**이 잘 작동한다.

**하드웨어 (Combinational Circuit)**:
- 레지스터의 값이 연속적으로 변화한다.
- 전력 소비는 **두 연속 값 사이의 변화량(Hamming Distance)**에 비례한다.
- **Bit Flip 모델**이 더 효과적이다.

### Bit Flip 모델

레지스터에 연속으로 저장되는 두 값 $X$와 $Y$ 사이의 **비트 플립**을 고려한다:

$$
\text{Selection Bit} = \text{LSB}(X) \oplus \text{LSB}(Y)
$$

- 비트가 변하면(0→1 또는 1→0): Selection = 1
- 비트가 그대로면(0→0 또는 1→1): Selection = 0

### AES 직렬화 하드웨어 예시

직렬화(Serialized) AES 구현에서:

```
State Register: [1][2][3][4]
                [5][6][7][8]
                [9][10][11][12]
                [13][14][15][16]
```

SubBytes 완료 후, MixColumns를 위해 **바이트 1**과 **바이트 5**가 연속으로 레지스터를 통과한다.

- 바이트 1: $S(P_1 \oplus K_1)$
- 바이트 5: $S(P_5 \oplus K_5)$

**Bit Flip 기반 공격**:

$$
\text{Selection} = \text{LSB}(S(P_1 \oplus K_1)) \oplus \text{LSB}(S(P_5 \oplus K_5))
$$

이 공격은 **$K_1$과 $K_5$를 동시에 추측**해야 한다:

- 첫 번째 공격: $K_1, K_5$ 모두 추측 → $2^{16}$ 후보
- 이후 공격: $K_5$는 알려진 상태로 $K_9$만 추측 → $2^8$ 후보

### 하드웨어 공격 결과

- **트레이스 수**: 10,000개
- **샘플 포인트**: 5,000개
- **단일 비트 모델**: 공격 성공하지만 피크가 불명확
- **Bit Flip 모델**: 훨씬 명확한 피크, 더 적은 트레이스로 성공 가능

# 4️⃣ DPA의 한계와 확장

## 1. Binary Decision의 한계

Classical DPA는 **이진 분류(Binary Decision)**에 기반한다:

- LSB = 0 또는 1
- 또는 임의의 이진 조건: $x = 0$ vs $x \neq 0$

**문제점**: 중간값의 다른 7비트 정보를 **완전히 무시**한다.

| 모델 | 그룹 수 | 활용 정보 | SNR |
|------|---------|-----------|-----|
| LSB | 2 | 1비트 | 낮음 (~0.15) |
| Hamming Weight | 9 | ~3비트 | 중간 (~30) |
| Full Value | 256 | 8비트 | 높음 |

## 2. 다중 그룹 분석의 어려움

T-Test는 **두 그룹 비교**에 최적화되어 있다.

9개 이상의 그룹을 비교하려면:

- **SNR 방식**: 평균들의 분산을 계산 (이미 학습함)
- **Chi-Square Test**: 전체 분포를 비교 (평균뿐만 아니라)
- **Correlation**: Leakage Model과의 상관계수 계산

## 3. CPA (Correlation Power Analysis)로의 확장

다음 세션에서 다룰 [[CPA]]는:

- **Leakage Model** 사용: 예를 들어 Hamming Weight
- **Pearson Correlation Coefficient** 계산
- 단일 비트가 아닌 **여러 비트의 정보**를 동시에 활용
- 더 적은 트레이스로 더 강력한 공격 가능

$$
\rho = \frac{\text{Cov}(V, \text{HW}(S(P \oplus K)))}{\sigma_V \cdot \sigma_{\text{HW}}}
$$

# 5️⃣ Summary

## 전력 소비 모델

$$
P = P_{\text{const}} + P_{\text{op}} + P_{\text{data}} + P_{\text{el.noise}}
$$

- **SPA**: $P_{\text{op}}$ 활용
- **DPA**: $P_{\text{data}}$ 활용

## SNR (Signal-to-Noise Ratio)

$$
\text{SNR} = \frac{\text{Variance of Means}}{\text{Mean of Variances}}
$$

- 공격의 **난이도 지표**
- 키 없이도 평문 기반으로 계산 가능
- 누출 지점(Leakage Point) 탐지에 활용

## Classical DPA

| 단계 | 설명 |
|------|------|
| 1. 키 추측 | 모든 키 후보 $K \in [0, 255]$ |
| 2. 중간값 계산 | $S(P_i \oplus K)$ |
| 3. 이진 분류 | LSB에 따라 Group 0, Group 1 |
| 4. 통계 검정 | T-Test 또는 Difference of Means |
| 5. 키 선택 | 최대 통계량을 가진 $K$ |

## 소프트웨어 vs 하드웨어

| 구현 | 효과적인 모델 | 이유 |
|------|--------------|------|
| 소프트웨어 | Hamming Weight / 단일 비트 | Pre-charged Bus |
| 하드웨어 | Bit Flip / Hamming Distance | Glitch 기반 전력 소비 |

## 핵심 교훈

> **"DPA는 통계의 힘으로 노이즈에 묻힌 미세한 정보를 추출한다. 개별 트레이스에서는 보이지 않는 차이도 수천 개의 트레이스를 평균하면 드러난다."**

- **S-box 출력**을 타겟으로 삼는 이유: 비선형성으로 인해 모든 입력 비트가 출력에 영향
- **키 없이 SNR 계산** 가능: 평문과 S-box 출력의 그룹 구성이 동일
- **샘플 포인트마다 개별 공격**: 각 시점의 데이터 의존성이 다름
- **Binary Decision의 한계**: CPA로 극복 가능