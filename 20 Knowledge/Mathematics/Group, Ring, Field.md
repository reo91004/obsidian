---
type: concept
status: stable
created: 2026-01-14
updated: 2026-03-15T01:47
aliases:
  - Finite Field
  - 유한체
domain:
  - "[[Mathematics]]"
parent:
related:
source:
project:
area:
prefer-view: edit
tags:
  - area/mathematics
  - topic/finite-field
---
## 0. 이 문서의 목표

- “유한체가 뭔가”에서 끝나는 게 아니라, **왜 $| \mathbb{F}^\times | = q-1$이고 왜 순환군인지**, **왜 $q=p^m$ 꼴만 가능한지**, **어떻게 실제로 계산하는지**까지 한 흐름으로 정리한다.

---

## 1. 기호·표기 사전 (처음에 막히는 것들)

- 집합/원소
    - $a \in S$: $a$는 집합 $S$의 원소
    - $S \subseteq T$: 부분집합
- 함수/사상
    - $f: A \to B$: $A$에서 $B$로 가는 함수
    - $f$가 **동형(isomorphism)**: 구조(연산)를 보존하면서 일대일 대응
- 합동(congruence)
    - $a \equiv b \pmod n$는 $n \mid (a-b)$와 동치
    - $\bmod$는 “나머지”가 아니라 **동치류에서의 동일성**이라는 관점이 안전하다.
- 환/다항식
    - $\mathbb{Z}$: 정수환, $\mathbb{Z}/n\mathbb{Z}$: 정수의 $n$에 대한 몫환
    - $\mathbb{F}_p[x]$: 체 $\mathbb{F}_p$ 위 다항식 환
- 몫구조(quotient)
    - $\mathbb{F}_p[x]/(f(x))$: $f(x)$로 생성되는 아이디얼로 나눈 몫환
    - 직관: “$f(x)=0$이라고 두고 다항식을 **$m-1$차 이하로 줄여서** 계산”
- “위수 / 차수” 구분 (헷갈림의 근원)
    - 군 $G$의 **위수**: $|G|$ (원소 개수)
    - 원소 $g \in G$의 **위수(=원소의 차수)**: $\operatorname{ord}(g)$ = $g^k=e$가 되는 최소 양의 정수 $k$
    - 다항식의 **차수(degree)**: $\deg f(x)$ (최고차항 지수)

---

## 2. Group / Ring / Field: 유한체로 가기 위한 최소 공리 세트

### 2.1 군(Group)

- 군 $(G,\cdot)$는 다음을 만족
    - 결합법칙: $(ab)c=a(bc)$
    - 항등원: $\exists e$ s.t. $ea=ae=a$
    - 역원: $\forall a, \exists a^{-1}$ s.t. $aa^{-1}=a^{-1}a=e$
- 가환군(Abelian group): $ab=ba$

**핵심 개념들**

- 부분군 $H \le G$
- 코셋 $gH=\{gh:h\in H\}$
- 라그랑주 정리: $|H| \mid |G|$ 그리고 $|g|=\operatorname{ord}(g) \mid |G|$
- 군 준동형 $\varphi:G\to G'$
    - $\ker(\varphi)$ (커널), $\operatorname{Im}(\varphi)$ (상)
    - 제1동형정리: $G/\ker(\varphi) \cong \operatorname{Im}(\varphi)$

유한체에서 군이 두 번 나온다.

- $(\mathbb{F}_q, +)$는 가환군
- $(\mathbb{F}_q^\times, \cdot)$는 가환군 (여기서 $\mathbb{F}_q^\times=\mathbb{F}_q\setminus\{0\}$)

### 2.2 환(Ring)

- 환 $(R,+,\cdot)$는
    - $(R,+)$가 아벨 군
    - 곱셈 결합법칙
    - 분배법칙: $a(b+c)=ab+ac$, $(a+b)c=ac+bc$
- 가환환: $ab=ba$
- 단위원(1)이 있는 환: $\exists 1$ s.t. $1a=a1=a$

**핵심 개념들**

- 영인수(zero divisor): $ab=0$인데 $a\neq 0$, $b\neq 0$인 경우 존재
- 적분환(integral domain): 가환환 + 단위원 + 영인수 없음
- 아이디얼(ideal) $I \triangleleft R$
    - $I$는 덧셈 부분군이고, $rI \subseteq I$, $Ir \subseteq I$
- 몫환 $R/I$
    - “$I$에 속하는 것들은 0으로 취급”하는 구조

유한체를 실제로 만드는 공식이 **몫환**을 쓴다:  
$\mathbb{F}_{p^m} \cong \mathbb{F}_p[x]/(f(x))$ (단, $f$는 기약다항식)

### 2.3 체(Field)

- 체 $(\mathbb{F},+,\cdot)$는
    - $(\mathbb{F},+)$ 아벨 군
    - $(\mathbb{F}^\times,\cdot)$ 아벨 군
    - 분배법칙
- 체의 특성(characteristic)
    - $\operatorname{char}(\mathbb{F})$는 $1+1+\cdots+1$ (총 $k$번)이 $0$이 되는 최소 양의 정수 $k$
    - 그런 $k$가 없으면 0이라 둔다.
    - 체에서는 $\operatorname{char}(\mathbb{F})$는 **항상 소수** 또는 0이다.
- 소체(prime subfield)
    - $\operatorname{char}(\mathbb{F})=p$이면 $\mathbb{F}$ 안에 $\mathbb{F}_p \cong \mathbb{Z}/p\mathbb{Z}$가 들어 있다.

---

## 3. 유한체의 “큰 정리” 3개 (이걸 외워야 길이 열린다)

### 3.1 유한체의 크기는 $q=p^m$ 꼴뿐

- 어떤 유한체 $\mathbb{F}$가 있으면 $\operatorname{char}(\mathbb{F})=p$ (소수)이고
- $\mathbb{F}$는 $\mathbb{F}_p$ 위의 $m$차원 벡터공간이라서
$$
|\mathbb{F}| = p^m.
$$

직관: 덧셈군 $(\mathbb{F},+)$는 $p$-군이고, 더 강하게 $(\mathbb{F},+)\cong (\mathbb{Z}/p\mathbb{Z})^m$가 된다.

### 3.2 같은 크기의 유한체는 “본질적으로 하나”

- $|\mathbb{F}|=p^m$인 유한체는 **동형까지 유일**하다.
- 그래서 보통 “그 유한체”를 $\mathbb{F}_{p^m}$ 혹은 $GF(p^m)$로 쓴다.

### 3.3 곱셈군 $\mathbb{F}_q^\times$는 순환군

- $q=p^m$일 때
$$
|\mathbb{F}_q^\times| = q-1
$$
    그리고 $\mathbb{F}_q^\times$는 **순환군**이다.
    
- 즉 어떤 $\alpha \in \mathbb{F}_q^\times$가 존재해서
$$
\mathbb{F}_q^\times = \langle \alpha \rangle = \{\alpha^0,\alpha^1,\dots,\alpha^{q-2}\}.
$$
    
- 이런 $\alpha$를 **원시원소(primitive element)** 또는 생성원(generator)이라 부른다.

---

## 4. $\mathbb{F}_p$와 $\mathbb{F}_{p^m}$의 “구성법” (실제로 손으로 만드는 법)

### 4.1 소체: $\mathbb{F}_p = \mathbb{Z}/p\mathbb{Z}$

- 원소: $\{0,1,\dots,p-1\}$
- 덧셈/곱셈: 정수 연산 후 $\bmod p$
- 예: $\mathbb{F}_5$에서 $3^{-1}$은 $3\cdot 2=6 \equiv 1 \pmod 5$이므로 $3^{-1}=2$

### 4.2 확장체: $\mathbb{F}_{p^m} \cong \mathbb{F}_p[x]/(f(x))$

- $f(x)\in \mathbb{F}_p[x]$가 **기약다항식(irreducible)**이고 $\deg f=m$이면,
$$
\mathbb{F}_{p^m} \cong \mathbb{F}_p[x]/(f(x)).
$$
    
- 몫환 관점
    - $\mathbb{F}_p[x]$의 원소는 다항식
    - $(f(x))$는 $f(x)$의 배수들의 집합(아이디얼)
    - “$f(x)$로 나눈 나머지”만 남긴다.
- 표현(다항식 기저)
    - 모든 원소는
$$
a_0 + a_1 x + \cdots + a_{m-1}x^{m-1}
$$
        꼴로 유일하게 표현된다. ($a_i\in \mathbb{F}_p$)
        
    - 원소 개수는 $p^m$

### 4.3 예시: $\mathbb{F}_4$

- $p=2, m=2$
- $\mathbb{F}_2[x]$에서 2차 기약다항식은 $x^2+x+1$
- 그래서
$$
\mathbb{F}_4 \cong \mathbb{F}_2[x]/(x^2+x+1).
$$
    
- $\alpha = x \bmod (x^2+x+1)$라고 두면, 관계식
$$
\alpha^2+\alpha+1=0 \quad\Rightarrow\quad \alpha^2 = \alpha+1.
$$
    
- 원소는 $\{0,1,\alpha,\alpha+1\}$

곱셈 예:

- $(\alpha+1)\alpha = \alpha^2+\alpha = (\alpha+1)+\alpha = 1$
- 따라서 $\alpha^{-1}=\alpha+1$

---

## 5. 원시원소/기약다항식/원시다항식/최소다항식: 용어가 한꺼번에 섞이는 지점

### 5.1 기약다항식(irreducible polynomial)

- $\mathbb{F}_p[x]$에서 $f(x)$가 기약이면 더 낮은 차수의 다항식들로 곱분해가 안 된다.
- $f$가 기약이면 $\mathbb{F}_p[x]/(f)$는 **체**가 된다. (몫환이 체가 되는 정확한 조건)

### 5.2 원시원소(primitive element)

- $\alpha \in \mathbb{F}_{p^m}^\times$가
$$
\operatorname{ord}(\alpha)=p^m-1
$$
    이면 원시원소다.
    
- 원시원소는 곱셈군 전체를 생성한다.

### 5.3 원시다항식(primitive polynomial)

- $\deg f=m$인 기약다항식 $f$에 대해,
- $f$의 근(확장체에서의 한 근) $\alpha$가 원시원소이면 $f$를 원시다항식이라 부른다.

요약:

- 기약다항식: 체를 만들기 위한 최소 조건
- 원시다항식: “만들어진 체에서 $x$가 곱셈군 생성자가 되게” 하는 강한 조건

### 5.4 최소다항식(minimal polynomial)

- 확장체 $E/\mathbb{F}_p$에서 원소 $\beta\in E$에 대해
    - $\beta$를 근으로 갖는 $\mathbb{F}_p[x]$의 기약다항식 중 차수가 최소인 유일한 다항식
    - 이를 $m_\beta(x)$라 쓴다.
- 성질
    - $\deg m_\beta = [\mathbb{F}_p(\beta):\mathbb{F}_p]$ (생성된 부분체의 확장차수)
    - $\beta$의 켤레(conjugates)는 Frobenius로 생성된다(아래 6.2 참조).

---

## 6. 부분체/확장체/프로베니우스: 유한체가 “정말로” 구조적인 이유

### 6.1 부분체의 분류: 약수 정리

- $\mathbb{F}_{p^m}$의 부분체는 정확히
$$
\mathbb{F}_{p^d} \quad (d \mid m)
$$
    꼴이다.
    
- 특히 각 $d \mid m$에 대해 그런 부분체는 **유일**하다.

예: $\mathbb{F}_{2^4}$의 부분체는

- $\mathbb{F}_2$, $\mathbb{F}_{2^2}=\mathbb{F}_4$, $\mathbb{F}_{2^4}=\mathbb{F}_{16}$

### 6.2 프로베니우스(Frobenius) 자동사상

- $\mathbb{F}_{p^m}$에서
$$
\varphi(a)=a^p
$$
    는 체의 자기동형(automorphism)이다.
    
- 반복하면
$$
a^{p^m}=a \quad (\forall a\in \mathbb{F}_{p^m})
$$
    즉 모든 원소가 $x^{p^m}-x$의 근이다.

이게 최소다항식/켤레를 계산할 때 핵심 도구가 된다.

### 6.3 Trace와 Norm (확장체에서 자주 쓰는 불변량)

확장 $\mathbb{F}_{p^m}/\mathbb{F}_p$에 대해

- 트레이스:
$$
\operatorname{Tr}_{\mathbb{F}_{p^m}/\mathbb{F}_p}(a)=a+a^p+a^{p^2}+\cdots+a^{p^{m-1}}
$$
    
- 노름:
$$
\operatorname{N}_{\mathbb{F}_{p^m}/\mathbb{F}_p}(a)=a\cdot a^p\cdot a^{p^2}\cdots a^{p^{m-1}}=a^{\frac{p^m-1}{p-1}}
$$
    성질
    
- $\operatorname{Tr}$는 $\mathbb{F}_p$-선형
- $\operatorname{N}$은 곱셈 준동형

암호/코딩에서 “선형 조건”이나 “부분체로 내리는 맵”으로 많이 등장한다.

---

## 7. 유한체 연산: 손계산과 구현 관점이 만나는 지점

### 7.1 $\mathbb{F}_p$에서

- 덧셈/곱셈: 정수 연산 후 $\bmod p$
- 역원: 확장 유클리드 알고리즘으로
$$
ax+py=1 \Rightarrow x \equiv a^{-1}\pmod p
$$

### 7.2 $\mathbb{F}_{p^m}=\mathbb{F}_p[x]/(f)$에서

- 덧셈: 계수별 덧셈 ($\bmod p$)
- 곱셈:
    
    1. 다항식 곱 수행
    2. $f(x)$로 나눈 나머지로 줄임 (즉 $\bmod f(x)$)

핵심은 “$\bmod$가 2개”라는 사실이다.

- 계수는 $\bmod p$
- 차수 줄이기는 $\bmod f(x)$

### 7.3 역원 계산(다항식 버전 확장 유클리드)

- $a(x)\in \mathbb{F}_p[x]/(f)$의 역원은
$$
a(x)s(x)+f(x)t(x)=1
$$
    를 만족하는 $s(x)$를 찾아서 $a(x)^{-1}\equiv s(x)\pmod{f(x)}$로 얻는다.

---

## 8. 예제 문제풀이 (풀이를 끝까지 전개)

### 예제 1) $\mathbb{F}_5$에서 역원과 방정식

1. $4^{-1}$ 구하기

- $4\cdot 4=16\equiv 1\pmod 5$  
    따라서 $4^{-1}=4$

2. 방정식 $3x+2=0$ in $\mathbb{F}_5$

- $3x\equiv -2 \equiv 3 \pmod 5$
- $3^{-1}=2$ (왜냐하면 $3\cdot 2=6\equiv 1$)
- $x\equiv 2\cdot 3=6\equiv 1$  
    따라서 해는 $x=1$

### 예제 2) $\mathbb{F}_4=\mathbb{F}_2[\alpha],\ \alpha^2=\alpha+1$에서 곱셈

- $(\alpha+1)(\alpha+1)$ 계산
$$
(\alpha+1)^2=\alpha^2+2\alpha+1.
$$
    $\mathbb{F}_2$에서는 $2\alpha=0$이므로
$$
(\alpha+1)^2=\alpha^2+1=(\alpha+1)+1=\alpha.
$$
    결론: $(\alpha+1)^2=\alpha$

### 예제 3) $\mathbb{F}_{2^3}=\mathbb{F}_2[x]/(x^3+x+1)$에서 역원

- $f(x)=x^3+x+1$ (기약)
- $\alpha = x \bmod f(x)$, 즉 $\alpha^3=\alpha+1$

목표: $(\alpha^2+\alpha)^{-1}$ 구하기.  
확장 유클리드 대신 “곱해서 1 되는지”를 짧게 찾는 방식으로 간다.

- 후보로 $\alpha$를 곱해 본다:
$$
(\alpha^2+\alpha)\alpha=\alpha^3+\alpha^2=(\alpha+1)+\alpha^2=\alpha^2+\alpha+1 \neq 1.
$$
    
- 후보로 $\alpha+1$:
$$
(\alpha^2+\alpha)(\alpha+1)=\alpha^3+\alpha^2+\alpha^2+\alpha = \alpha^3+\alpha.
$$
    $\alpha^3=\alpha+1$이므로
$$
\alpha^3+\alpha=(\alpha+1)+\alpha=1.
$$
    결론:
$$
(\alpha^2+\alpha)^{-1}=\alpha+1.
$$

### 예제 4) 원소의 위수와 원시원소 판정

$\mathbb{F}_4^\times$의 크기는 $3$이다.  
따라서 0이 아닌 원소 $\alpha,\alpha+1,1$은 모두 $a^3=1$을 만족한다.

- 위수 관점: $\mathbb{F}_4^\times$는 위수 3의 군이므로 순환군이고,
- $1$의 위수는 1, 나머지 두 원소는 위수 3이다.
- 따라서 $\alpha$와 $\alpha+1$은 둘 다 원시원소다.

### 예제 5) 최소다항식 감 잡기: “프로베니우스 궤도”로 보기

상황: $\mathbb{F}_{2^4}$에서 어떤 원소 $\beta$가 있다고 하자.  
$\mathbb{F}_2$ 위에서 $\beta$의 켤레는

$$
\beta,\ \beta^2,\ \beta^{2^2}=\beta^4,\ \beta^{2^3}=\beta^8
$$

처럼 $a\mapsto a^2$ 반복으로 생성된다.

- 만약 $\beta^{2^2}=\beta$라면 $\beta\in \mathbb{F}_{2^2}=\mathbb{F}_4$에 이미 들어가서 최소다항식 차수는 1 또는 2가 된다.
- 반대로 $\beta,\beta^2,\beta^4,\beta^8$이 모두 다르면 최소다항식 차수는 4가 된다.

정리: “$\beta$가 어느 부분체에 내려앉는지”가 최소다항식 차수를 결정한다.

---

## 9. 암호학/코딩이론에서 유한체가 나오는 대표 패턴

- 대칭키
    - AES의 바이트 연산은 $\mathbb{F}_{2^8}$ 구조를 쓴다.
    - 특히 S-box는 $\mathbb{F}_{2^8}$에서의 역원 + 아핀변환 조합으로 설명된다.
- 공개키
    - ECC는 주로 $\mathbb{F}_p$ 또는 $\mathbb{F}_{2^m}$ 위에서 타원곡선을 다룬다.
- 코딩이론
    - 리드-솔로몬은 $\mathbb{F}_{q}$에서의 다항식 평가로 구성된다.
    - BCH/Goppa 류는 확장체에서의 최소다항식/켤레 구조가 핵심으로 등장한다.

---

## 10. 자주 터지는 오해/함정 체크

- “$\bmod$가 하나”라고 생각하는 실수
    - $\mathbb{F}_{p^m}=\mathbb{F}_p[x]/(f)$에서는 **계수는 $\bmod p$**, **차수는 $\bmod f(x)$**다.
- “위수(order)”와 “차수(degree)” 혼동
    - 원소의 위수 $\operatorname{ord}(a)$는 곱셈 군에서 $a^k=1$의 최소 $k$
    - 다항식 차수 $\deg f$는 최고차항 지수
- $0$의 역원을 찾으려는 실수
    - $\mathbb{F}_q^\times$는 0을 뺀 집합이다.
- “$a^{q-1}=1$을 모든 $a$에 적용"
    - $a\neq 0$일 때만 성립한다. $a=0$이면 $0^{q-1}=0$이다.

---

## 11. 요약 치트시트

- 유한체 존재 조건: 크기 $q$가 **$q=p^m$** 꼴일 때만
- 구성:
$$
\mathbb{F}_{p^m}\cong \mathbb{F}_p[x]/(f(x)),\ \deg f=m,\ f\ \text{기약}
$$
    
- 덧셈군:
$$
(\mathbb{F}_{p^m},+)\cong (\mathbb{Z}/p\mathbb{Z})^m
$$
    
- 곱셈군:
$$
\mathbb{F}_{p^m}^\times\ \text{는 순환군},\ |\mathbb{F}_{p^m}^\times|=p^m-1
$$
    
- 부분체:
$$
\mathbb{F}_{p^d}\subseteq \mathbb{F}_{p^m}\iff d\mid m
$$
    
- 프로베니우스:
$$
a\mapsto a^p,\quad a^{p^m}=a
$$

---

## 12. 연습문제 (해설 포함)

### 문제 1) $\mathbb{F}_7$에서 $5^{-1}$을 구하라.

- 해설: $5x\equiv 1\pmod 7$.  
    $5\cdot 3=15\equiv 1\pmod 7$이므로 $5^{-1}=3$.

### 문제 2) $\mathbb{F}_5$에서 $x^2=4$의 해를 모두 구하라.

- 해설: $x\in\{0,1,2,3,4\}$ 대입
    - $2^2=4$, $3^2=9\equiv 4$  
        결론: 해는 $x=2,3$.

### 문제 3) $\mathbb{F}_4=\mathbb{F}_2[\alpha]$, $\alpha^2=\alpha+1$에서 $\alpha^3$를 구하라.

- 해설:
$$
\alpha^3=\alpha\alpha^2=\alpha(\alpha+1)=\alpha^2+\alpha=(\alpha+1)+\alpha=1.
$$
    결론: $\alpha^3=1$.

### 문제 4) $\mathbb{F}_4$에서 $(\alpha+1)^{-1}$을 구하라.

- 해설: 예제에서 이미
$$
(\alpha+1)\alpha=1
$$
    결론: $(\alpha+1)^{-1}=\alpha$.

### 문제 5) $\mathbb{F}_{2^3}=\mathbb{F}_2[x]/(x^3+x+1)$에서 $\alpha^4$를 $\{1,\alpha,\alpha^2\}$로 표현하라. (여기서 $\alpha=x\bmod f$)
- 해설: $\alpha^3=\alpha+1$이므로
$$
\alpha^4=\alpha\alpha^3=\alpha(\alpha+1)=\alpha^2+\alpha.
$$
    결론: $\alpha^4=\alpha^2+\alpha$.

### 문제 6) $q=p^m$에서 왜 항상 $|\mathbb{F}_q^\times|=q-1$인가?

- 해설: $\mathbb{F}_q^\times=\mathbb{F}_q\setminus\{0\}$ 정의로 즉시 $q-1$개.

### 문제 7) $\mathbb{F}_{p^m}$에서 $a^{p^m}=a$를 설명하라.

- 해설: 모든 원소는 $x^{p^m}-x$의 근이며, 이는 유한체의 표준 성질이다(프로베니우스 반복).  
    직관은 “프로베니우스가 유한 집합 위의 치환이고, 그 거듭제곱이 항등이 된다”로 잡아도 된다.

### 문제 8) $\mathbb{F}_{2^4}$의 부분체 개수를 구하라.

- 해설: 부분체는 $\mathbb{F}_{2^d}$이고 $d\mid 4$이므로 $d\in\{1,2,4\}$.  
    결론: 3개.

### 문제 9) $\mathbb{F}_{2^m}$에서 왜 $(a+b)^2=a^2+b^2$인가?

- 해설:
$$
(a+b)^2=a^2+2ab+b^2.
$$
    특성 2에서는 $2=0$이라서 $2ab=0$.  
    결론: $(a+b)^2=a^2+b^2$.

### 문제 10) $\mathbb{F}_p[x]/(f(x))$가 체가 되려면 $f$에 어떤 조건이 필요한가?

- 해설: $f$가 $\mathbb{F}_p[x]$에서 **기약**이어야 한다.  
    이유: $\mathbb{F}_p[x]$에서 $(f)$가 극대 아이디얼(maximal ideal)일 때 몫환이 체가 된다.![[KEM.pdf]]