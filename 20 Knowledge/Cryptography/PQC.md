---
type: concept
status: draft
context:
created: 2026-01-12
updated: 2026-03-15T19:38
aliases:
  - Post-Quantum Cryptography
  - 양자내성암호
source:
prefer-view: edit
tags:
  - topic/pqc
---
# PQC, Post-Quantum Cryptography

## 한 줄 정의

양자컴퓨터가 존재해도 안전하다고 기대되는 난제에 기반한 **고전적(소프트웨어/수학 기반)** 암호 체계.

## 왜 중요한가

- **양자컴퓨터의 위협**: Shor 알고리즘은 RSA, ECC 등 현행 공개키 암호의 기반 문제(소인수분해, 이산로그)를 다항 시간에 풀 수 있다. 양자컴퓨터가 실용화되면 현재 인터넷 보안 인프라가 무력화된다.
- **NIST 표준화 진행**: NIST는 2016년부터 PQC 표준화를 진행 중이며, ML-KEM(Kyber), ML-DSA(Dilithium) 등을 1차 표준으로 선정하고, HQC를 추가 KEM 표준으로 선정했다.
- **"Harvest Now, Decrypt Later" 위협**: 공격자가 현재 암호문을 수집해두었다가 양자컴퓨터가 등장하면 복호화할 수 있으므로, 지금부터 전환이 필요하다.
- **고전 컴퓨터에서 구동**: QKD와 달리 양자 장비가 필요 없으며 기존 하드웨어에서 소프트웨어만으로 동작한다.

## 핵심 직관

PQC는 양자컴퓨터로도 효율적으로 풀 수 없다고 믿어지는 수학적 난제 위에 구축된다. 핵심은 **고전 컴퓨터에서 돌아가는 알고리즘**이라는 점이다.

- 통신 채널에 양자 장비가 필요 없다. 그냥 현재 CPU에서 돌아가는 알고리즘이다.
- QKD처럼 물리적으로 양자 채널이나 광학 장비를 쓰는 계열은 '양자 암호'라고 부르며, PQC와 구분한다.
- 즉 **PQC는 소프트웨어/수학 기반이다.**

## 형식적 정의 / 수식

PQC 후보군별 핵심 난제:

| 후보군 | 핵심 난제 | 한 줄 설명 |
|--------|-----------|-----------|
| **Lattice (격자)** | LWE / RLWE | $\mathbf{b} = A\mathbf{s} + \mathbf{e}$에서 $\mathbf{s}$를 복원하는 것이 어렵다 |
| **Code (코드)** | Syndrome Decoding | 랜덤 선형 코드의 신드롬 $s = eH^T$에서 희소 벡터 $e$를 찾기 어렵다 |
| **Hash (해시)** | Hash Function Security | 해시 함수의 일방향성·충돌 저항성에 기반 (주로 서명) |
| **MQ (다변수)** | Multivariate Quadratic | 유한체 위 다변수 이차 연립방정식 풀이가 NP-hard |
| **Isogeny** | Isogeny Path Finding | 타원곡선 사이의 아이소지니 경로를 찾기 어렵다 |
| **MPC-in-the-head** | MPC Simulation | MPC 시뮬레이션 기반 서명 구조 |

## 예시

- **CRYSTALS-Kyber (ML-KEM)**: 격자 기반 KEM. NIST PQC 1차 표준. Module-LWE 기반으로 키 크기가 작고 성능이 우수하다.
- **CRYSTALS-Dilithium (ML-DSA)**: 격자 기반 전자서명. NIST 1차 표준.
- **HQC**: 코드 기반 KEM. NIST 추가 표준. Syndrome Decoding 난제 + Quasi-Cyclic 구조 활용.
- **SPHINCS+**: 해시 기반 서명. 가장 보수적인 안전성 가정(해시 함수 안전성만 필요).

## 자주 헷갈리는 점

- **PQC ≠ 양자 암호(Quantum Cryptography)**: PQC는 고전 컴퓨터에서 실행되는 수학 기반 암호. QKD는 물리적 양자 채널을 사용하는 완전히 다른 접근.
- **"양자 내성"은 증명된 것이 아니라 기대**: 현재까지 알려진 양자 알고리즘으로 효율적으로 풀 수 없다는 것이지, 수학적으로 불가능함이 증명된 것은 아니다.
- **Isogeny 기반은 주의**: SIDH/SIKE는 2022년 고전 공격에 의해 깨졌다. 후보군이라고 다 안전한 것은 아니다.

## 상위 개념
- [[Cryptography]]

## 하위 개념
- [[HQC]]
- [[KEM]]

## 관련 개념
- [[Shor Algorithm]]
- [[Lattice]]

## 같이 볼 것
- [[KEM]]
- [[HQC]]