---
type: concept
status: draft
created: 2026-01-12
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
# KEM, Key Encapsulation Mechanism
> 공개키 암호를 "키 합의/키 전달 전용 인터페이스"로 정형화한 개념이다.
> 목적은 **양쪽이 동일한 대칭키 $K$를 안전하게 공유**하는 데 있다.

![[KEM.pdf]]

![[KEM-1768224277048.png|588x445]]

## 1. KEM의 구성 요소

KEM은 다음 세 알고리즘으로 정의된다.

$$
(\mathsf{KeyGen}, \mathsf{Encap}, \mathsf{Decap})
$$
### KeyGen

수신자가 공개키/비밀키 쌍을 생성한다.

$$
(\mathsf{PK}, \mathsf{SK}) \leftarrow \mathsf{KeyGen}(\text{seed})
$$

* $\mathsf{PK}$: 공개키
* $\mathsf{SK}$: 비밀키

### Encap (Encapsulation)

송신자가 공개키만을 사용해 **공유 키와 캡슐을 동시에 생성**한다.

$$
(K, C) \leftarrow \mathsf{Encap}(\mathsf{PK})
$$
* $K$: 공유될 대칭키
* $C$: 캡슐(ciphertext)

**중요:**
$K$는 사용자가 입력한 값이 아니라 **Encap 과정에서 무작위로 생성**된다.

### Decap (Decapsulation)

수신자가 비밀키로 캡슐을 열어 동일한 키를 복원한다.

$$
K \leftarrow \mathsf{Decap}(\mathsf{SK}, C)
$$

정상적인 경우, 송신자와 수신자가 얻는 $K$는 동일하다.

## 2. KEM 동작 방식 (Process Flow)

KEM의 동작 과정은 다음 단계로 이루어진다.

1.  **수신자 A**: 키 쌍 생성
    $$
    (\mathsf{PK}, \mathsf{SK}) \leftarrow \mathsf{KeyGen}
    $$
2.  **송신자 B**: 캡슐화 및 공유 키 생성
    $$
    (K, C) \leftarrow \mathsf{Encap}(\mathsf{PK})
    $$
3.  **전송**:
    *   네트워크로 전송되는 것은 $C$뿐이다.
    *   $K$는 전송되지 않는다.
4.  **수신자 A**: 캡슐 복호화 및 공유 키 복원
    $$
    K \leftarrow \mathsf{Decap}(\mathsf{SK}, C)
    $$

결과적으로 A와 B는 동일한 대칭키 $K$를 안전하게 공유한다.

## 3. [[PKE]]와의 차이점