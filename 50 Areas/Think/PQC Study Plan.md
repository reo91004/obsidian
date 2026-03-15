---
type: idea
status: draft
context:
created: 2026-01-10
updated: 2026-03-15T19:38
aliases:
  - 어떻게 PQC에 대해 공부할 것이냐?
  - PQC 공부 계획
source:
prefer-view: edit
tags:
  - topic/pqc
---

# PQC Study Plan

## 핵심 아이디어
PQC(Post-Quantum Cryptography)를 체계적으로 공부하기 위한 로드맵.

## 왜 이게 떠올랐는가
양자컴퓨팅 시대에 대비한 암호 체계를 이해하려면 수학적 기초부터 구현까지 넓은 범위를 다뤄야 한다.

## 현재 생각

### 1단계: 수학적 기초
- [[Group, Ring, Field]] — 대수 구조 기초
- 유한체(Finite Field) 연산
- 격자(Lattice) 기초

### 2단계: 부호 이론
- [[Coding Theory]] — 오류정정부호 전반
- [[Reed-Solomon Code]] — 비이진 부호
- [[Goppa Code]] — McEliece 기반

### 3단계: PQC 스킴
- [[PQC]] — 전체 개요
- [[KEM]] — 키 캡슐화
- [[HQC]] — 코드 기반 KEM
- Lattice-based: Kyber/ML-KEM
- Hash-based: SPHINCS+

### 4단계: 구현과 보안
- [[Side-Channel Attack]] — 부채널 공격
- [[Timing Attack]], [[Differential Power Analysis]]
- 상수 시간 구현

## 검증할 점
- 어떤 순서가 가장 효율적인가?
- 부호 이론과 격자 이론 중 어디에 더 집중할 것인가?

## 연결할 개념
- [[PQC]]
- [[HQC]]
- [[Coding Theory]]
- [[Shor Algorithm]]

## 다음 행동
- [ ] 각 단계별 핵심 논문/교재 선정
- [ ] 1단계 수학적 기초 노트 보강
