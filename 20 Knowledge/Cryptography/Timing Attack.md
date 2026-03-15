---
type: concept
status: stable
context:
created: 2026-01-19
updated: 2026-03-15T19:38
aliases:
  - 타이밍 공격
  - PACS 03 Timing Attacks-Software
source: PACS Lecture
prefer-view: edit
tags:
---

# 0️⃣ Introduction to [[Timing Attack]] on Software

**Timing Attack (타이밍 공격)**은 소프트웨어 구현이 처리하는 데이터에 따라 **실행 시간(클럭 사이클 수)**이 달라지는 특성을 이용한 부채널 공격이다. 지난 세션에서는 암호와 무관한 단순한 예제(`strcomp` 함수로 PIN 검증)를 다뤘다면, 이번 세션에서는 **실제 암호 알고리즘(AES)**에 대한 타이밍 공격을 다룬다.

## 왜 소프트웨어 타이밍 공격인가?

1.  **AES의 설계 배경**: AES는 처음부터 **8비트 마이크로컨트롤러**에서 효율적으로 동작하도록 설계되었다. 오늘날에도 저가형 IoT 장치, 스마트카드 등에서 소프트웨어로 구현된다.
2.  **메모리 제약**: S-box는 룩업 테이블로 구현할 수 있지만, **[[Mix Columns]]**를 완전히 룩업 테이블(T-table)로 구현하기에는 메모리가 부족한 경우가 많다. 이때 CPU 명령어로 직접 계산해야 한다.
3.  **조건문의 함정**: Galois Field 곱셈을 구현할 때 사용되는 **조건문**이 타이밍 누출의 원인이 된다.

# 1️⃣ AES [[Mix Columns]]와 [[Galois Field]]

## 1. Mix Columns 연산

[[Mix Columns]]는 AES의 4개 연산 중 하나로, 열(column) 단위로 바이트들을 섞는 역할을 한다.

- **입력**: State의 한 열 (4바이트: $s_0, s_1, s_2, s_3$)
- **출력**: 새로운 열 (4바이트)
- **연산**: 고정된 행렬을 곱한다.

$$
\begin{pmatrix} s'_0 \\ s'_1 \\ s'_2 \\ s'_3 \end{pmatrix}
=
\begin{pmatrix}
02 & 03 & 01 & 01 \\
01 & 02 & 03 & 01 \\
01 & 01 & 02 & 03 \\
03 & 01 & 01 & 02
\end{pmatrix}
\begin{pmatrix} s_0 \\ s_1 \\ s_2 \\ s_3 \end{pmatrix}
$$

예를 들어 첫 번째 출력 바이트:

$$
s'_0 = (02 \cdot s_0) \oplus (03 \cdot s_1) \oplus (01 \cdot s_2) \oplus (01 \cdot s_3)
$$

여기서 **덧셈(+)은 XOR**, **곱셈은 Galois Field 곱셈**이다. `01`과의 곱셈은 그냥 원래 값이고, XOR는 모든 마이크로컨트롤러에서 사용 가능하다. 문제는 **`02`와 `03` 곱셈**이다.

## 2. [[Galois Field]] $GF(2^8)$의 다항식 표현

Galois Field에서 8비트 값을 **다항식**으로 표현한다:

$$
b_7b_6b_5b_4b_3b_2b_1b_0 \quad \Leftrightarrow \quad b_7x^7 + b_6x^6 + \cdots + b_1x + b_0
$$

**예시**: `0x38` = `00111000` (2진수)

$$
0 \cdot x^7 + 0 \cdot x^6 + 1 \cdot x^5 + 1 \cdot x^4 + 1 \cdot x^3 + 0 \cdot x^2 + 0 \cdot x + 0 = x^5 + x^4 + x^3
$$

## 3. `02` 곱셈 (×2, xtime)

`02`(16진수) = `00000010`(2진수) = $x$ (다항식)

따라서 **`02`와 곱하는 것은 $x$를 곱하는 것**과 같다. $x$를 곱하면 모든 항의 차수가 1씩 증가한다:

$$
(b_7x^7 + \cdots + b_1x + b_0) \cdot x = b_7x^8 + b_6x^7 + \cdots + b_1x^2 + b_0x
$$

**문제점**: $x^8$ 항이 생기면 8비트를 초과한다. 이때 **기약다항식(Irreducible Polynomial)**으로 **모듈러 연산**을 수행해야 한다.

### AES의 기약다항식

$$
m(x) = x^8 + x^4 + x^3 + x + 1 = \texttt{0x11B}
$$

- `0x11B` = `1 0001 1011` (2진수)
- 실제 사용 시 $x^8$을 빼면 `0x1B` = `0001 1011`

### Reduction 규칙

$x^8$이 결과에 나타나면(즉, 원래 값의 **MSB가 1**이면), `0x1B`로 XOR해야 한다.

$$
\text{xtime}(a) =
\begin{cases}
a \ll 1 & \text{if } a_7 = 0 \\
(a \ll 1) \oplus \texttt{0x1B} & \text{if } a_7 = 1
\end{cases}
$$

### 예제: `0x38 × 02`

```
0x38 = 0011 1000
Left shift: 0111 0000 0 (9비트가 됨)
MSB(원래값) = 0 → Reduction 불필요
결과: 0111 0000 = 0x70
```

### 예제: `0x70 × 02`

```
0x70 = 0111 0000
Left shift: 1110 0000 0
MSB(원래값) = 0 → Reduction 불필요
결과: 1110 0000 = 0xE0
```

### 예제: `0xE0 × 02`

```
0xE0 = 1110 0000
Left shift: 1 1100 0000 (9비트!)
MSB(원래값) = 1 → Reduction 필요!
0x1B  = 0001 1011
XOR:    1100 0000
      ⊕ 0001 1011
      ───────────
        1101 1011 = 0xDB
```

## 4. `03` 곱셈 (×3)

`03` = `02` + `01` (Galois Field에서 덧셈은 XOR)

$$
03 \cdot a = (02 \cdot a) \oplus a
$$

따라서 **×2 연산 후 원래 값과 XOR**하면 된다.

## 5. C 코드 구현 (취약한 버전)

```c
// xtime.c
unsigned char xtime(unsigned char input) {
    unsigned char output;
    output = input << 1;           // Left shift
    if (input & 0x80) {            // MSB가 1이면
        output = output ^ 0x1B;    // Reduction
    }
    return output;
}

unsigned char mul3(unsigned char input) {
    return xtime(input) ^ input;   // ×2 후 원래값과 XOR
}
```

> **핵심 취약점**: `if (input & 0x80)` 조건문이 **입력의 MSB**에 따라 실행 여부가 달라진다. MSB가 1이면 XOR 연산이 추가로 수행되어 **클럭 사이클이 더 소요**된다.

# 2️⃣ [[Timing Attack]] 원리와 공격 절차

## 1. 공격 가정

| 가정        | 설명                                                 |
| --------- | -------------------------------------------------- |
| **측정 가능** | 전체 암호화 시간(클럭 사이클 수)만 측정 가능. 개별 연산의 시간은 측정 불가.      |
| **평문**    | Known Plaintext (알려진 평문). Chosen Plaintext일 필요 없음. |
| **타겟**    | 첫 번째 라운드의 S-box 출력 → Mix Columns 입력                |

## 2. 타이밍 누출 지점

AES의 연산 순서를 따라가 보면:

```
AddRoundKey → SubBytes → ShiftRows → MixColumns
```

- **ShiftRows**는 바이트 위치만 바꾸므로 값 자체는 변하지 않는다.
- 따라서 **MixColumns의 입력 = SubBytes의 출력 = S-box 출력**이다.

Mix Columns에서 모든 S-box 출력은 **×2 연산**을 거친다:

$$
\begin{aligned}
s'_0 &= (02 \cdot S[p_0 \oplus k_0]) \oplus \cdots \\
s'_1 &= \cdots \oplus (02 \cdot S[p_1 \oplus k_1]) \oplus \cdots \\
&\vdots
\end{aligned}
$$

각 ×2 연산의 **입력 MSB가 1이면 추가 클럭 사이클이 소요**된다.

## 3. 공격 절차: [[Divide and Conquer]]

전체 16바이트 키를 한 번에 찾는 것은 불가능하다 ($2^{128}$ 경우의 수). 대신 **바이트 단위로 개별 공격**한다.

### Step 1: 데이터 수집

$n$개의 서로 다른 평문 $P_1, \ldots, P_n$에 대해 타이밍 $T_1, \ldots, T_n$을 측정한다.

### Step 2: 키 추측 및 그룹 분류 (첫 번째 바이트 $k_0$ 공격)

```
for k = 0 to 255:
    Group0[k] = empty
    Group1[k] = empty

    for i = 1 to n:
        sbox_out = S[P_i[0] XOR k]     # S-box 출력 예측
        msb = (sbox_out >> 7) & 1      # MSB 추출

        if msb == 0:
            Group0[k].append(T_i)
        else:
            Group1[k].append(T_i)

    Delta[k] = |mean(Group1[k]) - mean(Group0[k])|
```

### Step 3: 정답 키 선택

**정답 키인 경우**:
- Group1의 모든 트레이스는 **실제로 MSB=1인 S-box 출력**을 가졌다.
- Group0의 모든 트레이스는 **실제로 MSB=0인 S-box 출력**을 가졌다.
- 따라서 **두 그룹의 평균 차이가 최대**가 된다.

**오답 키인 경우**:
- 잘못된 키로 예측한 MSB는 실제 MSB와 무관하다.
- 두 그룹에 MSB=0과 MSB=1이 **랜덤하게 섞인다**.
- 따라서 두 그룹의 평균 차이가 **거의 0에 가깝다**.

### 왜 많은 측정이 필요한가?

타이밍 $T_i$에는 **타겟 S-box 외의 노이즈**가 포함된다:

- 첫 번째 라운드의 **15개의 다른 S-box**
- **9개 라운드**의 Mix Columns (마지막 라운드는 Mix Columns 없음)
- 기타 연산들

이 노이즈를 평균으로 상쇄하려면 **충분히 많은 샘플**이 필요하다.

## 4. MATLAB 예제 코드

```matlab
% timing_attack.m
n = 10000;           % 측정 횟수
target_byte = 1;     % 첫 번째 바이트 공격

Delta = zeros(1, 256);

for k = 0:255
    % S-box 출력 예측
    sbox_out = Sbox(bitxor(P(1:n, target_byte), k) + 1);

    % MSB 추출 (8번째 비트)
    MSB = bitget(sbox_out, 8);

    % 그룹 분류
    i0 = find(MSB == 0);    % MSB가 0인 인덱스들
    i1 = find(MSB == 1);    % MSB가 1인 인덱스들

    % 그룹 평균 계산
    m0 = mean(T(i0));
    m1 = mean(T(i1));

    % 차이 저장
    Delta(k+1) = abs(m1 - m0);
end

plot(0:255, Delta);
xlabel('Key Candidate');
ylabel('|mean(Group1) - mean(Group0)|');
```

> **결과**: 정답 키에서 **하나의 피크**가 명확하게 나타난다. 그러나 측정 횟수가 부족하면 다른 키 후보들도 높은 값을 가질 수 있어 **확신할 수 없다**.

# 3️⃣ [[Student's t-Test]]를 이용한 통계적 분석

## 1. 문제: "충분한 측정"이란?

단순히 평균 차이만 보면:

- 5,000개 측정: 정답 키가 가장 높지만, 다른 후보도 비슷하게 높다.
- 2,000개 측정: 어떤 키가 정답인지 **확신할 수 없다**.

> **해결책**: 통계적 검정을 통해 "두 그룹이 **유의미하게** 다른가?"를 판단한다.

## 2. [[귀무가설]] (Null Hypothesis)

통계적 검정은 **귀무가설**을 세우고, 이를 기각하거나 채택한다.

- **귀무가설 ($H_0$)**: "두 그룹(Group0, Group1)은 **같은 모집단**에서 추출되었다. 즉, 평균이 같다."
- **대립가설 ($H_1$)**: "두 그룹은 **다른 모집단**에서 추출되었다. 즉, 평균이 다르다."

> **목표**: 귀무가설을 **기각**하면, 두 그룹이 유의미하게 다르다고 할 수 있다 → 키 후보가 정답일 가능성이 높다.

## 3. 표본 통계량

두 집합 $Q_0$, $Q_1$이 주어졌을 때:

| 기호 | 의미 | 수식 |
|------|------|------|
| $n_0, n_1$ | 각 집합의 원소 개수 | - |
| $\mu_0, \mu_1$ | **표본 평균** (Sample Mean) | $\mu = \frac{1}{n}\sum_{i=1}^{n} x_i$ |
| $s_0^2, s_1^2$ | **표본 분산** (Sample Variance) | $s^2 = \frac{1}{n-1}\sum_{i=1}^{n}(x_i - \mu)^2$ |
| $s_0, s_1$ | **표본 표준편차** | $s = \sqrt{s^2}$ |

### 분산의 직관적 의미

분산은 **데이터가 평균으로부터 얼마나 퍼져 있는지**를 나타낸다:

$$
s^2 = \frac{1}{n-1}\sum_{i=1}^{n}(x_i - \mu)^2
$$

- 각 데이터 $x_i$가 평균 $\mu$에서 **얼마나 떨어져 있는지** 계산한다.
- 편차를 **제곱**하여 양수로 만든다.
- 평균을 낸다 (엄밀히는 $n-1$로 나눈다).

## 4. t-통계량 (t-Statistic)

Welch's t-test에서 t-통계량은 다음과 같이 계산된다:

$$
t = \frac{\mu_1 - \mu_0}{\sqrt{\frac{s_0^2}{n_0} + \frac{s_1^2}{n_1}}}
$$

**직관적 해석**:
- **분자**: 두 그룹 평균의 차이 (신호)
- **분모**: 표준 오차 (노이즈)
- **t-값이 클수록**: 두 그룹이 유의미하게 다르다.

## 5. 자유도 (Degrees of Freedom)

Welch-Satterthwaite 방정식:

$$
\nu = \frac{\left(\frac{s_0^2}{n_0} + \frac{s_1^2}{n_1}\right)^2}{\frac{(s_0^2/n_0)^2}{n_0-1} + \frac{(s_1^2/n_1)^2}{n_1-1}}
$$

자유도는 t-분포의 모양을 결정하지만, **샘플 수가 충분히 크면(≈1000 이상)** 크게 중요하지 않다.

## 6. t-분포와 p-값

### t-분포

자유도 $\nu$가 주어지면 t-분포의 확률밀도함수(PDF)가 결정된다:

$$
f(t; \nu) = \frac{\Gamma\left(\frac{\nu+1}{2}\right)}{\sqrt{\nu\pi}\,\Gamma\left(\frac{\nu}{2}\right)} \left(1 + \frac{t^2}{\nu}\right)^{-\frac{\nu+1}{2}}
$$

이 분포는 **정규분포와 유사**하지만, 꼬리가 더 두껍다 (특히 $\nu$가 작을 때).

### p-값 계산

p-값은 **귀무가설이 참일 때, 관측된 t-값보다 극단적인 값이 나올 확률**이다:

$$
p = 2 \int_{|t|}^{\infty} f(x; \nu) \, dx
$$

또는 누적분포함수(CDF) $F$를 사용하면:

$$
p = 2 \cdot F(-|t|; \nu)
$$

### p-값의 해석

| p-값 | 해석 |
|------|------|
| 크다 (예: 0.5) | 귀무가설 채택 → 두 그룹은 같다 |
| 작다 (예: 0.001) | 귀무가설 기각 → 두 그룹은 **유의미하게** 다르다 |

## 7. 실용적 임계값: $|t| > 4.5$

복잡한 p-값 계산 대신, 실용적인 **임계값**을 사용한다:

$$
\text{자유도 } \nu > 1000 \text{이고 } |t| > 4.5 \text{이면, } p < 0.00001
$$

> **의미**: t-값의 절댓값이 4.5보다 크면, 두 그룹이 같을 확률은 **0.001% 미만**이다. 매우 강력한 증거로 귀무가설을 기각할 수 있다.

## 8. 공격에 적용

```matlab
% t_test_attack.m
n = 10000;
target_byte = 1;

T_stat = zeros(1, 256);

for k = 0:255
    sbox_out = Sbox(bitxor(P(1:n, target_byte), k) + 1);
    MSB = bitget(sbox_out, 8);

    i0 = find(MSB == 0);
    i1 = find(MSB == 1);

    m0 = mean(T(i0));    m1 = mean(T(i1));
    v0 = var(T(i0));     v1 = var(T(i1));

    % t-통계량 계산
    T_stat(k+1) = (m1 - m0) / sqrt(v0/length(i0) + v1/length(i1));
end

plot(0:255, abs(T_stat));
hold on;
yline(4.5, 'r--', 'Threshold');
xlabel('Key Candidate');
ylabel('|t-statistic|');
```

### 결과 해석

| 측정 횟수 | 결과 |
|-----------|------|
| 10,000 | 정답 키의 t-값이 **임계값 4.5를 훨씬 초과**. 다른 후보는 모두 4.5 미만. |
| 5,000 | 정답 키의 t-값이 **4.5를 약간 초과**. 아슬아슬하지만 확신 가능. |
| 2,000 | 모든 후보가 **4.5 미만**. 정답을 확신할 수 없음. 더 많은 측정 필요. |

> **핵심**: t-test를 사용하면 "몇 개의 측정이 필요한가?"라는 질문에 **정량적으로** 답할 수 있다.

## 9. 수렴 분석

측정 횟수에 따른 t-값의 변화를 추적하면:

```matlab
% convergence.m
for num_samples = 50:50:10000
    % 각 측정 횟수에서 t-값 계산
    ...
end

% 256개의 곡선 (각 키 후보별)
% 정답 키의 곡선만 시간이 지남에 따라 급격히 상승
% 다른 후보들은 임계값 아래에서 진동
```

# 4️⃣ [[Constant-Time]] 구현의 어려움

## 1. 단순한 해결책의 함정

"조건문에 `else`를 추가하면 되지 않을까?"

```c
// xtime_v2.c - 여전히 취약!
unsigned char xtime(unsigned char input) {
    unsigned char output = input << 1;
    if (input & 0x80) {
        output = output ^ 0x1B;
    } else {
        asm("nop");  // 아무것도 안 함
    }
    return output;
}
```

**문제점**:
1. 컴파일러 **최적화**가 `nop`를 제거할 수 있다.
2. 어셈블리 레벨에서 여전히 **다른 명령어 시퀀스**가 실행된다.

## 2. AVR 어셈블리 분석

AVR 마이크로컨트롤러는 저가형 임베디드 시스템에서 널리 사용된다.

### 버전 1: `brcc` 사용 (취약)

```asm
; xtime_asm_v1.asm
    mov  output, input     ; 1 clock
    lsl  output            ; 1 clock (left shift, MSB → Carry)
    brcc else_label        ; 1 clock (Carry=0) / 2 clocks (Carry=1)
    ldi  temp, 0x1B        ; 1 clock
    eor  output, temp      ; 1 clock
else_label:
    nop                    ; 1 clock
    ret
```

**데이터시트 확인**:

| 명령어    | 설명                      | 클럭 사이클                        |
| ------ | ----------------------- | ----------------------------- |
| `brcc` | Branch if Carry Cleared | **1** (분기 안 함) / **2** (분기 함) |

**클럭 사이클 계산**:
- **MSB = 0** (Carry = 0 → 분기함): `mov(1) + lsl(1) + brcc(2) + nop(1) + ret = 5 + α`
- **MSB = 1** (Carry = 1 → 분기 안 함): `mov(1) + lsl(1) + brcc(1) + ldi(1) + eor(1) + nop(1) + ret = 6 + α`

> **여전히 1 클럭 차이가 발생한다!**

### 버전 2: `sbrc` 사용 (상수 시간)

```asm
; xtime_asm_v2.asm
    mov  output, input     ; 1 clock
    lsl  output            ; 1 clock
    ldi  temp, 0x00        ; 1 clock (temp = 0으로 초기화)
    sbrc input, 7          ; 1/2/3 clocks (조건에 따라)
    ldi  temp, 0x1B        ; 1 clock (또는 스킵됨)
    eor  output, temp      ; 1 clock
    ret
```

**`sbrc` (Skip if Bit in Register Cleared) 동작**:

| 상황 | 클럭 사이클 |
|------|-------------|
| 조건 거짓 (비트=1) → 스킵 안 함 | 1 |
| 조건 참 (비트=0) → 다음 명령어(1 word) 스킵 | 2 |
| 조건 참 (비트=0) → 다음 명령어(2 words) 스킵 | 3 |

**핵심**: `ldi`는 **2 words** 명령어이므로, 스킵 시 3 클럭 소요.

**클럭 사이클 계산**:
- **MSB = 0** (비트 7 = 0 → 스킵함): `mov(1) + lsl(1) + ldi(1) + sbrc(3) + eor(1) + ret = 7 + α`
- **MSB = 1** (비트 7 = 1 → 스킵 안 함): `mov(1) + lsl(1) + ldi(1) + sbrc(1) + ldi(1) + eor(1) + ret = 7 + α`

> **두 경우 모두 동일한 클럭 사이클!** 이제야 상수 시간이다.

## 3. 조건문 없는 구현 (권장)

아예 조건문을 사용하지 않는 방법:

```c
// xtime_constant.c
unsigned char xtime(unsigned char input) {
    unsigned char mask = (input >> 7) * 0x1B;  // MSB가 1이면 0x1B, 0이면 0x00
    return (input << 1) ^ mask;
}
```

**동작**:
- `input >> 7`: MSB만 추출 (0 또는 1)
- `(input >> 7) * 0x1B`: MSB가 1이면 `0x1B`, 0이면 `0x00`
- 조건문 없이 항상 같은 연산 수행

## 4. 여전히 남는 위험

### 컴파일러 최적화

- GCC의 `-O1`, `-O2`, `-O3` 옵션은 코드를 **재구성**할 수 있다.
- 보안 관점에서는 **최적화를 끄고**, 생성된 어셈블리를 **직접 검토**해야 한다.

### 분기 예측 (Branch Prediction)

현대 CPU는 **분기 예측기**를 사용하여 성능을 최적화한다:

- 조건문 결과를 **미리 예측**하고 해당 코드를 **선제 실행**한다.
- 예측이 맞으면 빠르고, 틀리면 파이프라인을 비우고 다시 실행한다.
- 이로 인해 **어셈블리가 상수 시간이어도 실제 실행 시간은 다를 수 있다**.

### 곱셈 최적화

일부 CPU는 곱셈 시 **피연산자가 0이면 빠르게 종료**한다:

- `0 * 0x1B = 0` → 빠름
- `1 * 0x1B = 0x1B` → 정상 속도

이로 인해 조건문 없는 구현도 **타이밍 누출**이 발생할 수 있다.

### 문서화되지 않은 동작

CPU의 일부 동작은 **문서에 명시되지 않은** 경우가 있다. 시뮬레이션 결과와 실제 하드웨어 동작이 다를 수 있다.

## 5. 최선의 해결책: 하드웨어 구현

### [[AES-NI]] (AES New Instructions)

Intel이 2010년경 도입한 **하드웨어 AES 명령어**:

```asm
aesenc  xmm1, xmm2    ; AES 한 라운드 암호화
aesenclast xmm1, xmm2 ; 마지막 라운드
aesdec  xmm1, xmm2    ; AES 한 라운드 복호화
```

**장점**:
1. **속도**: 소프트웨어 대비 수십 배 빠름
2. **보안**: 하드웨어 회로이므로 **완벽한 상수 시간**
3. **범용성**: 운영체제(Windows, Linux 등)에서 널리 사용

> **핵심**: AES-NI는 타이밍 공격에 대한 **근본적인 해결책**이다. 다음 세션에서 캐시 기반 타이밍 공격과 함께 더 자세히 다룬다.

# 5️⃣ Summary

## 핵심 원리

| 항목 | 내용 |
|------|------|
| **취약점** | Mix Columns의 ×2 연산에서 **MSB 기반 조건문** |
| **공격 방식** | Known Plaintext, 전체 암호화 시간 측정 |
| **통계 기법** | Student's t-test ($|t| > 4.5$이면 유의미) |
| **방어** | 조건문 제거, 하드웨어 구현 (AES-NI) |

## 교훈

> **"비밀 정보에 의존하는 조건문(if/for/while)을 절대 사용하지 마라."**

- **비밀 정보**: 키, 키에 의존하는 중간값 (S-box 출력 등)
- **조건문**: 직접적인 `if`뿐만 아니라, 컴파일러가 생성하는 분기도 포함
- **검증 방법**:
  1. 어셈블리 코드 직접 검토
  2. 실제 하드웨어에서 타이밍 측정
  3. 통계적 분석으로 누출 여부 확인

## 다음 세션 예고

- **캐시 타이밍 공격**: 캐시 히트/미스에 의한 타이밍 차이
- **AES T-table 공격**: 룩업 테이블 접근 패턴 분석
- **AES-NI**: 하드웨어 기반 방어

# 🖇️ 링크

```cardlink
url: https://youtu.be/LhjFp-YBGpw?si=NiQheWuN8IM-zyfQ
title: "Physical Attacks and Countermeasures - Session 3 - Timing Attacks-Software"
description: "Physical Attacks and Countermeasures - Session 3 - Amir Moradi"
host: youtu.be
favicon: https://www.youtube.com/s/desktop/191847ec/img/favicon_32x32.png
image: https://i.ytimg.com/vi/LhjFp-YBGpw/maxresdefault.jpg
```