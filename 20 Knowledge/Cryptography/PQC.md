---
type: concept
status: draft
created: 2026-01-12
updated: 2026-03-15T01:47
aliases:
  - Post-Quantum Cryptography
  - 양자내성암호
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
  - topic/pqc
---
# PQC, Post-Quantum Cryptography

> ==양자컴퓨터가 존재해도 안전하다고 기대되는 난제에 기반한 고전적 암호===를 뜻한다.

- 여기서 고전적이라는 단어는 중요한데, **PQC는 통신 채널에 양자 장비가 필요가 없다.** 그냥 현재 CPU에서 돌아가는 알고리즘이며, 양자 안전을 목표로 하지만 구현은 고전 컴퓨터에서 한다. 
- QKD처럼 물리적으로 양자 채널이나 광학 장비를 쓰는 계열은 '양자 암호'라고 부르며, PQC와 구분한다.
- 즉 **PQC는 소프트웨어/수학 기반이다.**

### 후보군에는 뭐가 있는데?
- **Lattice(격자 기반)**: LWE/RLWE 계열 난제 기반. (NIST에서 핵심 축)
- **Code(코드 기반)**: Syndrome Decoding 같은 디코딩 난제 기반. (McEliece/BIKE/HQC 같은 계열)
- **Hash(해시 기반)**: 주로 서명에서 강함(SPHINCS+ 류).
- **MQ(다변수 다항식)**: 다변수 연립방정식 풀이 난제 기반.
- **Isogeny(아이소지니)**: 타원곡선 사이의 아이소지니 경로 난제.
- **MPC(MPC-in-the-head 등)**: MPC 기반 서명 등으로 이어지는 계열.