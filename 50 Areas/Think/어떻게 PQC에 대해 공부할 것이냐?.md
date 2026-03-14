---
type: idea
status: draft
created: 2026-01-12
updated: 2026-03-15T01:47
aliases:
domain:
  - "[[Cryptography]]"
parent:
related:
  - "[[PQC]]"
source:
project:
area:
prefer-view: edit
tags:
  - area/cryptography
  - topic/pqc
---
**석사 초보자에게 적합한 PQC 논문으로 CRYSTALS-Kyber 스펙 문서를 추천합니다.** [1][2] 이 문서는 NIST 표준화된 Kyber 알고리즘의 상세 설계와 구현 지침을 제공하며, 강의 후 따라 해볼 수 있도록 명확한 파라미터와 의사코드가 포함되어 있습니다. [1] 연구실 배경인 PQC+SCA를 고려하면 Kyber 구현부터 시작해 부채널 분석으로 확장하기 좋습니다. [3][4]

## 추천 논문 1: CRYSTALS-Kyber

Kyber는 격자 기반 KEM(Key Encapsulation Mechanism)으로, NIST PQC 표준(ML-KEM)의 기반입니다. [1] 초보자가 이해하기 쉽도록 모듈 LWE 문제 설명부터 샘플링, 암호화/복호화 과정을 단계별로 다루며, PQC4all 같은 lib로 바로 구현 테스트 가능합니다. [5] 석사 선배님 조언처럼 이 스펙 따라 Python/C 구현하며 연구 재현하면 핵심 개념(NTT, 노이즈 샘플링)이 바로 잡힙니다.

![[kyber-specification-round3-20210131.pdf]]

## 추천 논문 2: SCA on Kyber/Saber

"Side-Channel Analysis on Post-Quantum Cryptography Algorithms"는 TVLA(Test Vector Leakage Assessment)로 Kyber와 Saber의 SCA 취약성을 분석합니다. [3] ARM Cortex-M4 구현에서 power/EM 측정을 통해 키 복구 과정을 보여주며, 네 연구실(DGIST PAC Lab) 테마와 딱 맞습니다. [6] 마스킹 버전도 비교해 방어 기법까지 따라 해볼 수 있어 입문 논문으로 이상적입니다. [7]

## 따라 해보기 팁
- **구현부터**: Kyber 스펙 PDF 다운로드 후 liboqs나 PQM4 라이브러리 설치, Kyber512 키 생성/캡슐화 테스트. [1]
- **SCA 실험**: ChipWhisperer 툴로 power trace 수집 후 TVLA 적용 – 초보자 튜토리얼 많음. [3]
- **다음 단계**: arXiv에서 "ML-KEM side-channel" 검색해 2025 최신 논문 확장. [8]

이 접근으로 강의 지식과 연결되며, GitHub 포트폴리오 쌓아 취업에도 유리합니다. [9] 추가 구현 코드나 튜토리얼 필요하면 말씀하세요. [10]