---
type: concept
status: draft
context:
created: 2026-01-12
updated: 2026-03-15T19:39
aliases:
  - Qubit
  - 큐비트
source:
prefer-view: edit
tags:
---
# Qubit

## 한 줄 정의

양자 정보의 기본 단위로, $|0\rangle$과 $|1\rangle$의 중첩 상태를 가질 수 있는 양자 비트.

## 왜 중요한가

- 양자 컴퓨팅의 모든 연산은 큐비트 위에서 이루어진다.
- $n$개의 큐비트는 $2^n$개의 상태를 동시에 표현할 수 있어, 특정 문제에서 지수적 병렬성을 제공한다.
- 얽힘(entanglement)과 중첩(superposition)을 통해 고전 컴퓨터로 불가능한 알고리즘을 실현한다.

## 핵심 직관

고전 비트는 0 또는 1 중 하나의 확정된 상태만 가진다.

$$b \in \{0, 1\}$$

큐비트는 측정 전까지 $|0\rangle$과 $|1\rangle$의 **중첩** 상태에 있다.

$$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle,\quad \alpha,\beta\in\mathbb{C},\ |\alpha|^2+|\beta|^2=1$$

측정하면 $|0\rangle$이 $|\alpha|^2$의 확률로, $|1\rangle$이 $|\beta|^2$의 확률로 관측되며, 상태는 **붕괴**한다.

## 형식적 정의 / 수식

큐비트의 상태는 2차원 복소 힐베르트 공간 $\mathbb{C}^2$의 단위 벡터이다.

$$|0\rangle = \begin{pmatrix} 1 \\ 0 \end{pmatrix}, \quad |1\rangle = \begin{pmatrix} 0 \\ 1 \end{pmatrix}$$

**Bloch 구 표현:** 전역 위상을 무시하면 임의의 단일 큐비트 상태를 다음과 같이 쓸 수 있다.

$$|\psi\rangle = \cos\frac{\theta}{2}|0\rangle + e^{i\phi}\sin\frac{\theta}{2}|1\rangle$$

여기서 $\theta \in [0, \pi]$, $\phi \in [0, 2\pi)$이며, 이는 단위 구 위의 한 점에 대응한다.

**다중 큐비트:** $n$-큐비트 시스템의 상태는 $2^n$차원 힐베르트 공간에 존재한다.

$$|\psi\rangle = \sum_{i=0}^{2^n-1} \alpha_i |i\rangle, \quad \sum_{i=0}^{2^n-1}|\alpha_i|^2 = 1$$

## 예시

주요 단일 큐비트 게이트:

| 게이트 | 행렬 | 동작 |
|--------|------|------|
| **X** (NOT) | $\begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix}$ | $\|0\rangle \leftrightarrow \|1\rangle$ 반전 |
| **Z** | $\begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix}$ | $\|1\rangle$에 위상 $-1$ 부여 |
| **H** (Hadamard) | $\frac{1}{\sqrt{2}}\begin{pmatrix} 1 & 1 \\ 1 & -1 \end{pmatrix}$ | $\|0\rangle \to \frac{\|0\rangle+\|1\rangle}{\sqrt{2}}$ |

Hadamard 게이트는 확정 상태를 균등 중첩으로 만들어, 양자 병렬성의 출발점이 된다.

## 자주 헷갈리는 점

- **중첩 $\neq$ "동시에 0이면서 1"**: 중첩은 확률적 상태이며, 측정 시 하나의 결과만 얻는다.
- **측정은 비가역적**: 측정하면 상태가 붕괴하여 원래 중첩 정보를 잃는다.
- **복제 불가 (No-Cloning Theorem)**: 임의의 미지 큐비트 상태를 완벽히 복제할 수 없다.
- **큐비트 $\neq$ 확률적 비트**: 고전 확률과 달리 진폭은 복소수이므로 **간섭(interference)**이 일어난다.

## 상위 개념
- [[Quantum Computing]]

## 하위 개념

## 관련 개념
- [[Toffoli Gate]]
- [[QFT]]

## 같이 볼 것

## Ref
```cardlink
url: https://wikidocs.net/250770
title: "901. 큐비트 (Qubit)"
description: "# 큐비트 (Qubit)  ## 1. 큐비트란? 큐비트(양자 비트)는 양자 정보의 기본 단위로, 양자 세계에서의 비트입니다. 기존의 비트는 0 또는 1의 상태를 가질 수 있지만,…"
host: wikidocs.net
image: https://static.wikidocs.net/images/book/dalle_x2IjHhY_3CdZ9qU_eYhoEz1_eXtmxE6_0hrJPkF_HNcF1Kn.png
```

```cardlink
url: https://dohunkim.tistory.com/19
title: "2. 디랙 표기법 (브라-켓 표기법)"
description: "디랙 표기법(Dirac notation)은 양자 상태를 표현하기 위해 사용되는 방법 중 하나입니다. 1939년 폴 디랙이 고안하였으며, 브라-켓 표기법(Bra-ket notation)이라고도 합니다. 정의 $a, b$가 복소수 성분을 가지는 2차원 벡터일 때, (N차원 벡터로 확장 가능) 켓 (ket) $ \\qquad \\text{Ket : } \\quad \\left| a \\right> = \\begin{pmatrix} a_1 \\\\ a_2 \\end{pmatrix} $ 브라 (bra) $ \\qquad \\text{Bra : } \\quad \\left< b \\right| = \\left| b \\right>^\\dagger = \\begin{pmatrix} b_1 \\\\ b_2 \\end{pmatrix}^\\dagg.."
host: dohunkim.tistory.com
favicon: https://t1.daumcdn.net/tistory_admin/favicon/tistory_favicon_32x32.ico
image: https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2FDXgHz%2FbtqThYBIiCm%2FAAAAAAAAAAAAAAAAAAAAAN5q4JfaZfgSzgorppg9m7Ovr3vDZ44KpoZNJIbBkJVF%2Fimg.jpg%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1769871599%26allow_ip%3D%26allow_referer%3D%26signature%3DvmmbJG9lQa5HYwAgQFgsEGgjCHA%253D
```