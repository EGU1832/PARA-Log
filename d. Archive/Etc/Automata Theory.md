#### Reference
[오토마타 이론 공부 (1) - 기본적인 형식 .. : 네이버블로그 (naver.com)](https://blog.naver.com/bestowing/221636494349)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/Automata_theory.svg/300px-Automata_theory.svg.png)

#### Automatoa Theory (오토마타 이론)
> 계산 능력이 있는 추상 기계와 그 기계르 ㄹ이용해서 풀 수 있는 문제들을 연구하는 컴퓨터 과학의 분야
- 대상의 어떤 기능에 주목하여 입력과 내부 출력 각 신호의 상호관계를 수학 모델로 옮기고 이 모델을 수학적으로 고찰, 결론을 유도한다.
- 계산이론, 인공지능, 컴퓨터 구조 설계, 컴파일러 설계, 파싱 등의 중요 요소이다.
- At least finite state, input -state 전이-> output

#### Automaton (오토마톤)
> 이산 시간 동안 주어진 입력에 의존해 작동하는 수학적인 기계
- 문자열 = $\Sigma$ 기호/문자{ 알파벳 } -> state 전이
- curr state, input, next state는 함수 또는 관계로 주어진다.
- input의 끝 or specific state에서 정지 -> 문자열 수용/거부

# 1. Basic Formal Language

#### Formal Language (형식 언어)

**Symbol(심볼)**:
한글의 자음과 모음, 10진수의 0-9
$a, b, c... \ / \ 0, 1, 2...$

**Alphabet(알파벳)**:
symbol의 집합 `비어있지 않고 유한해야 한다.`
$\Sigma_{Eng} = \{a, b, c, d, ..., z\} \ / \ \Sigma_{Binary} = \{0, 1\}$

**String(문자열)**:
특정 Alphabet으로부터 선택하여 열거한 유한한 심볼들
$\Sigma_{Eng} = \{a, b, c, d, ..., z\}: foo, bar..$ 
$\Sigma_{Binary} = \{0, 1\}: 01, 100..$
$\epsilon:= \text{empty string}$

Power of alphabet
$\Sigma^k: \text{모든 가능한 String}$
$\Sigma^0 = \epsilon$

Kleene Star
특정 Alphabet으로 만들 수 있는 모든 String의 집합
$\Sigma^* = \Sigma^0 \cup \Sigma^1 \cup \Sigma^2 \cup... = \bigcup\limits_{i = 0}^{\infty} \Sigma^i$

Kleene Plus
Kleene Star에서 empty String($\epsilon$)을 제외한 집합
$\Sigma^+ = \Sigma^* - \{\epsilon\}$

**Language(언어)**:
특정 Alphabet의 $\Sigma^*$의 부분집합인 String의 집합만이 그 Alphabet의 Language가 될 수 있다.
$\text{L is a language over alphabet } \Sigma, \text{ only if} L \subseteq \Sigma^*$
$L = \{\epsilon, 01, 10, 0011, 0101, 1010, ...\}$
$L = \emptyset$

단, $L$이 $\epsilon$으로만 이루어진 Language라 해도 비어있는 Language는 아니다.
$if \ L_1 = \{\epsilon\} \ and \ L_2 = \emptyset, \ then \ L_1 \neq L_2$

> Language는 $\Sigma ^*$의 부분 집합이다.

#### Operation of String (문자열의 연산)

String이 길이는 다음과 같이 나타낸다.
$|hello| = 5$
$|\epsilon| = 0$

**Concatenate 연산**:
두 개의 String을 합하는 연산
$Let \ a = hello,\ b = world, \ then \ ab = a\cdot b = helloworld$

어떤 String이던 앞 뒤로 empty String($\epsilon$)을 포함하고 있다고 말할 수 있다.
$hello = \epsilon hello = hello \epsilon - \epsilon hello \epsilon$

**Additional Concept**
- Prefix(접두사), Suffix(접미사)
	$z = xy$를 만족하면 $x$는 $z$의 prefix이고 $y$는 $z$의 suffix이다.
- Substring: String의 일부분
	$x$와 $y$ 모두 $z$의 substring이다.
- Reversal: String을 뒤집은 것
	$hello \rightarrow_{reversal} olleh$
- Palindrome: String을 reversal해도 원래 String과 같은 형태인 String
	$pop, wow..$

#### Operation of Language (언어의 연산)

Language는 집합의 개념과 동일하다.

**Union**:
합집합의 개념과 유사하다.
$L_1 \cup L_2 = \{ u \ | \ u \in L_1 \ or \ u \in L_2\}$

**Intersection**:
교집합의 개념과 유사하다.
$L_1 \cap L_2 = \{ u \ | \ u \in L_1 \ and \ u \in L_2\}$

**Complementation**:
여집합의 개념과 유사하다.
$L^{\prime} = \Sigma ^* - L = \{u \ | \ u \notin L\}$

**Concatenation**:
밑의 설명 참고
$L_1 \cdot L_2 = \{uv \ | \ u \in L_1 \ and \ v \in L_2\}$
$ex)$ $L_1 = \{a, b, c, d\}, \ L_2 = \{z\}$
	$L_1 \cdot L_2 = \{az, bz, cz, dz\}$

**Reversal**:
기존 Language의 모든 String을 reversal 처리한 Language이다.
$L^R = \{ u^R \ | \ u \in L\}$

# 2. DFA (Deterministic Finite Automata)

![](https://upload.wikimedia.org/wikipedia/commons/b/b4/Mealy.png)

#### Finite Automata (유한 오토마타)
> 유한 오토마다(finite-state machine, FSM)는 유한한 개수의 State를 가질 수 있는 오토마타, 즉 추상 기계라고 할 수 있다.
- 오토마타의 State는 기계 장치의 목적에 따라 다양하다.
- 화살표는 한 State에서 다른 State로 transition(전이)되는 것을 표기하기 위해 사용된다.

#### Finite Automata & Formal Language


#### Types of Finite Automata

