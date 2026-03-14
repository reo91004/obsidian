---
type: concept
status: draft
created: 2026-01-12
updated: 2026-03-15T01:47
aliases:
  - Shor's Algorithm
domain:
  - "[[Quantum Computing]]"
parent:
related:
  - "[[QFT]]"
source:
project:
area:
prefer-view: edit
tags:
  - area/computer-science
---

## 그래서, 쇼어 알고리즘이 뭔 상관인데?

찾아본 바로는, ==주기성==때문에 그렇다 한다.

예를 들어, RSA를 보자.

$$
a^x\equiv b \pmod N
$$

여기서 우리는 $a$를 order가 최대한 큰 수 or 원시근으로 선정하며 $x$를 비밀 키로 둔다. $b$를 공개 키로 생각하며, $N$을 큰 소수로 사용한다.

$x$를 정방향 계산하여 $b$를 산출하는 건 쉽지만, 역으로 $x$를 찾는 건 쉽지 않다.

$$
\text{Given } N=pq,\ \text{find } p,q
$$

그래서, Shor 알고리즘은 이걸 그대로 풀지 않고 주기 문제로 바꾼다.

임의의 (a)를 골라서 함수

$$
f(x) = a^x \bmod N
$$

를 생각한다.

* $f(x)$는 **주기적**이다.
* 어떤 최소의 $r$이 존재해서 $f(x+r)=f(x)$, 즉 $a^r \equiv 1 \pmod N$로 만들며, 여기서 $r$이 바로 **주기**다.

### 근데 왜 주기만 알면 인수분해가 되는데?

$$
a^r \equiv 1 \pmod N
\Rightarrow
(a^{r/2}-1)(a^{r/2}+1)\equiv 0 \pmod N
$$

이기 때문에,

$$
\gcd(a^{r/2}\pm 1, N)
$$

가 되며, 둘 중 하나가 확률적으로 $p$ 또는 $q$가 된다. 뭐 그렇겠지..? 곱해서 $(a^{r/2}−1)(a^{r/2}+1)=k⋅N$ 가 될 테니까.

==물론 여기까지는 양자랑 아무 상관 없다.==

여기서 고전 컴퓨터는 패턴이 보이지 않기 때문에 ($N$때문에 비선형성이 됨) 주기라는 정보를 관측할 방법이 없다. 허나,

$$
\sum_{x=0}^{Q-1} |x\rangle |f(x)\rangle
$$

양자 컴퓨터에서는 이런 식으로, 모든 $x$에 대해 동시에 f$(x)$를 계산(병렬)한다.

여기서부터 이해가 잘 안 갈 수 있는데, 첫 번째 레지스터에 [[QFT]]를 적용한다.

- 시간 영역 → 주파수 영역
- 값 → 주기

로 변환함으로써, ==이 함수가 어떤 주기를 갖는지를 간섭 패턴으로 변환==한다.
