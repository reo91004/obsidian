---
type: concept
status: draft
context:
created: 2026-01-12
updated: 2026-03-15T19:38
aliases:
  - Key Encapsulation Mechanism
  - 키 캡슐화 메커니즘
source:
prefer-view: edit
tags:
---
# KEM, Key Encapsulation Mechanism

## 한 줄 정의

공개키 암호를 "키 합의/키 전달 전용 인터페이스"로 정형화한 메커니즘. 양쪽이 동일한 대칭키 $K$를 안전하게 공유하는 것이 목적이다.

## 왜 중요한가

- **PQC 표준이 KEM 형태**: NIST PQC 표준화에서 선정된 알고리즘(ML-KEM/Kyber, HQC 등)이 모두 KEM 인터페이스를 채택했다.
- **PKE보다 안전한 인터페이스**: PKE는 사용자가 임의 메시지를 넣을 수 있어 오용 가능성이 높다. KEM은 키 생성을 내부에서 처리하므로 IND-CCA2 안전성을 달성하기 쉽다.
- **하이브리드 암호의 핵심 빌딩 블록**: 실제 통신에서는 KEM으로 대칭키를 공유한 뒤, AES 등 대칭 암호로 데이터를 암호화하는 KEM+DEM 구조를 사용한다.

## 핵심 직관

> "열쇠를 상자에 넣어서 보내는 것"

PKE가 "상대방의 자물쇠로 **내가 고른 메시지**를 잠그는 것"이라면, KEM은 "상대방의 자물쇠로 **자동 생성된 열쇠**를 캡슐에 넣어 보내는 것"이다. 키를 직접 암호화하는 것이 아니라, Encap 과정에서 키와 캡슐이 동시에 생성된다.

## 형식적 정의 / 수식

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

## 예시

### KEM 동작 흐름

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

### KEM vs PKE 비교

| 구분          | KEM                    | PKE               |
| ----------- | ---------------------- | ----------------- |
| 입력          | 공개키만                   | 공개키 + 평문 메시지      |
| 출력          | $(K, C)$ — 키와 캡슐 동시 생성 | $C$ — 암호문만        |
| 키 선택        | 내부에서 무작위 생성            | 사용자가 메시지를 선택      |
| IND-CCA2 달성 | 자연스러움 (FO 변환 등)        | 추가 패딩 필요 (OAEP 등) |
| PQC 적합성     | 표준 인터페이스               | 복호화 실패 시 오류 누출 위험 |

## 자주 헷갈리는 점

- **KEM은 메시지를 암호화하지 않는다**: KEM은 대칭키를 공유하는 수단이다. 실제 데이터 암호화는 DEM(Data Encapsulation Mechanism, 예: AES-GCM)이 담당한다.
- **$K$는 송신자가 고르는 것이 아니다**: Encap이 내부적으로 랜덤하게 생성한다. 이것이 PKE와의 결정적 차이다.
- **KEM ≠ 키 교환(Key Exchange)**: Diffie-Hellman 같은 키 교환은 양쪽이 모두 능동적으로 참여하지만, KEM은 수신자가 공개키를 미리 게시하면 송신자가 단방향으로 캡슐을 보내는 구조다.

## 첨부 자료

![[KEM.pdf]]

![[KEM-1768224277048.png|588x445]]

## 상위 개념
- [[Cryptography]]

## 하위 개념
- [[HQC]]

## 관련 개념
- [[PQC]]
- [[PKE]]

## 같이 볼 것
- [[HQC]]
- [[PQC]]