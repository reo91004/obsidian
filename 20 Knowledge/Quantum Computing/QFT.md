---
type: concept
status: draft
context:
created: 2026-01-12
updated: 2026-03-15T19:39
aliases:
  - Quantum Fourier Transform
source:
prefer-view: edit
tags:
---
# QFT (양자 푸리에 변환)

## 한 줄 정의

양자 상태의 진폭을 주파수 영역으로 변환하는 양자 연산.

## 왜 중요한가

- [[Shor Algorithm]]의 핵심 서브루틴으로, 주기 찾기에 필수적이다.
- 고전 DFT가 $O(N \log N)$인 반면, QFT는 $O(\log^2 N)$ 게이트로 수행 가능하다 (지수적 속도 향상).
- 양자 위상 추정(Quantum Phase Estimation) 등 다양한 양자 알고리즘의 기반이 된다.

## 핵심 직관

우선 푸리에 변환의 직관은, 파형을 주파수 영역으로 변환하는 것이다.

고전 이산 푸리에 변환(DFT)은 $N$개의 복소수 벡터를 주파수 성분으로 분해한다. QFT는 이것의 양자 버전으로, $N = 2^n$개의 양자 상태 진폭에 대해 동일한 변환을 수행한다.

핵심 차이는 **효율성**이다. 고전 FFT는 $O(N \log N)$이지만, QFT는 $n = \log_2 N$개의 큐비트에 대해 $O(n^2)$ 게이트만 필요하다. 이 지수적 속도 향상은 양자 상태의 중첩을 활용하기 때문이다.

## 형식적 정의 / 수식

$N = 2^n$일 때, QFT는 계산 기저 상태 $|j\rangle$를 다음과 같이 변환한다:

$$QFT|j\rangle = \frac{1}{\sqrt{N}}\sum_{k=0}^{N-1} e^{2\pi ijk/N}|k\rangle$$

이를 텐서곱 형태로 풀면:

$$QFT|j\rangle = \frac{1}{\sqrt{2^n}} \bigotimes_{\ell=1}^{n} \left(|0\rangle + e^{2\pi i j / 2^\ell}|1\rangle\right)$$

이 분해가 중요한 이유는, 각 큐비트에 대해 Hadamard 게이트와 제어 위상 게이트(controlled phase gate)만으로 구현 가능함을 보여주기 때문이다.

**제어 위상 게이트:**

$$R_k = \begin{pmatrix} 1 & 0 \\ 0 & e^{2\pi i / 2^k} \end{pmatrix}$$

## 예시

**2-qubit QFT 회로** ($N = 4$):

1. 첫 번째 큐비트에 Hadamard 게이트 $H$ 적용
2. 두 번째 큐비트를 제어 비트로 하여 첫 번째 큐비트에 $R_2$ (제어 위상) 적용
3. 두 번째 큐비트에 Hadamard 게이트 $H$ 적용
4. 두 큐비트 SWAP

변환 결과:
- $|00\rangle \to \frac{1}{2}(|00\rangle + |01\rangle + |10\rangle + |11\rangle)$
- $|01\rangle \to \frac{1}{2}(|00\rangle + i|01\rangle - |10\rangle - i|11\rangle)$
- $|10\rangle \to \frac{1}{2}(|00\rangle - |01\rangle + |10\rangle - |11\rangle)$
- $|11\rangle \to \frac{1}{2}(|00\rangle - i|01\rangle - |10\rangle + i|11\rangle)$

## 자주 헷갈리는 점

- **QFT 자체가 빠른 것이지, 결과를 읽는 것이 빠른 것은 아니다**: QFT 후 측정하면 하나의 결과만 얻는다. Shor 알고리즘처럼 주기 정보가 측정 결과에 드러나도록 설계해야 의미가 있다.
- **고전 FFT를 대체하는 것이 아니다**: QFT는 양자 상태의 진폭을 변환하는 것이지, 고전 데이터를 입력받아 변환하는 것이 아니다.

## 상위 개념
- [[Quantum Computing]]

## 하위 개념

## 관련 개념
- [[Shor Algorithm]]
- [[Qubit]]

## 같이 볼 것