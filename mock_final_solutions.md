# COMP4141 Mock Final Exam — Reference Solutions

These solutions correspond to the revised mock final exam. They are written in a fairly detailed style, so that students can use them for revision rather than only for checking final answers.

---

## Question 1 (Finite State Automata). (2 + 3 = 5 marks)

We need automata for the language

$$
(01 \cup 10)^*(0 \cup 11)
$$

over the alphabet $\Sigma = \{0,1\}$.

The language consists of zero or more two-symbol blocks, each block being either $01$ or $10$, followed by a final suffix which is either $0$ or $11$.

Examples in the language include

$$
0,\quad 11,\quad 010,\quad 1011,\quad 01100,\quad 10011.
$$

Examples not in the language include

$$
\epsilon,\quad 1,\quad 01,\quad 10,\quad 001,\quad 111.
$$

### Part 1: An $\epsilon$-NFA

A compact NFA is enough, since every NFA is also an $\epsilon$-NFA with no $\epsilon$-transitions.

Use states

$$
q_0,q_1,q_2,q_3,q_f.
$$

The start state is $q_0$, and the only accepting state is $q_f$.

The intended meaning is:

- $q_0$: we are at the boundary between blocks of $(01\cup 10)^*$, and may either read another block or move into the final suffix;
- $q_1$: we have read the first symbol $0$ of a block $01$;
- $q_2$: we have read the first symbol $1$ of a block $10$;
- $q_3$: we have read the first $1$ of the final suffix $11$;
- $q_f$: accepting state.

Define the transitions as follows:

$$
q_0 \xrightarrow{0} q_1, \qquad q_1 \xrightarrow{1} q_0
$$

for the block $01$, and

$$
q_0 \xrightarrow{1} q_2, \qquad q_2 \xrightarrow{0} q_0
$$

for the block $10$.

For the final suffix $0$, add

$$
q_0 \xrightarrow{0} q_f.
$$

For the final suffix $11$, add

$$
q_0 \xrightarrow{1} q_3, \qquad q_3 \xrightarrow{1} q_f.
$$

Thus the full transition list is:

$$
\begin{aligned}
q_0 &\xrightarrow{0} q_1,\ q_f,\\
q_0 &\xrightarrow{1} q_2,\ q_3,\\
q_1 &\xrightarrow{1} q_0,\\
q_2 &\xrightarrow{0} q_0,\\
q_3 &\xrightarrow{1} q_f.
\end{aligned}
$$

All unspecified transitions go to no state.

This automaton is nondeterministic at $q_0$: on input $0$, it can either treat the $0$ as the beginning of a block $01$ or as the final suffix $0$; on input $1$, it can either treat the $1$ as the beginning of a block $10$ or as the first symbol of the final suffix $11$.

That nondeterminism exactly matches the regular expression.

### Part 2: Subset construction

Since the NFA above has no $\epsilon$-transitions, all $\epsilon$-closures are trivial:

$$
\operatorname{ECLOSE}(q)=\{q\}
$$

for every state $q$.

The DFA start state is

$$
\{q_0\}.
$$

A DFA state is accepting if it contains $q_f$.

Compute the reachable subset states.

Let

$$
A=\{q_0\}.
$$

From $A$:

- on input $0$:

  $$
  \delta(q_0,0)=\{q_1,q_f\},
  $$

  so define

  $$
  B=\{q_1,q_f\};
  $$

- on input $1$:

  $$
  \delta(q_0,1)=\{q_2,q_3\},
  $$

  so define

  $$
  C=\{q_2,q_3\}.
  $$

From $B=\{q_1,q_f\}$:

- on $0$, neither $q_1$ nor $q_f$ has a $0$-transition, so we go to $\emptyset$;
- on $1$, $q_1$ goes to $q_0$, while $q_f$ has no transition, so we go to $\{q_0\}=A$.

Define

$$
D=\emptyset.
$$

From $C=\{q_2,q_3\}$:

- on $0$, $q_2$ goes to $q_0$, while $q_3$ has no $0$-transition, so we go to $A$;
- on $1$, $q_3$ goes to $q_f$, while $q_2$ has no $1$-transition, so we go to $\{q_f\}$.

Define

$$
E=\{q_f\}.
$$

From $E=\{q_f\}$, both inputs go to $D=\emptyset$.

From $D=\emptyset$, both inputs stay in $D$.

So the DFA is:

| DFA state | NFA subset | On 0 | On 1 | Accepting? |
|---|---:|---:|---:|---:|
| $A$ | $\{q_0\}$ | $B$ | $C$ | No |
| $B$ | $\{q_1,q_f\}$ | $D$ | $A$ | Yes |
| $C$ | $\{q_2,q_3\}$ | $A$ | $E$ | No |
| $D$ | $\emptyset$ | $D$ | $D$ | No |
| $E$ | $\{q_f\}$ | $D$ | $D$ | Yes |

The start state is $A$. The accepting states are $B$ and $E$, since those are exactly the subset states containing $q_f$.

### Quick correctness check

The string $0$ follows

$$
A \xrightarrow{0} B,
$$

so it is accepted.

The string $11$ follows

$$
A \xrightarrow{1} C \xrightarrow{1} E,
$$

so it is accepted.

The string $010$ follows

$$
A \xrightarrow{0} B \xrightarrow{1} A \xrightarrow{0} B,
$$

so it is accepted: this corresponds to block $01$ followed by final suffix $0$.

The string $01$ follows

$$
A \xrightarrow{0} B \xrightarrow{1} A,
$$

which is not accepting. This is correct, because $01$ is only a full block and has no final suffix.

---

## Question 2 (Regular Languages). (5 marks)

Consider

$$
L=\{a^{2^n}\mid n\in\mathbb N\}.
$$

We prove that $L$ is not regular.

### Proof using the pumping lemma

Suppose, for contradiction, that $L$ is regular.

Then by the pumping lemma, there exists a pumping length $p\geq 1$ such that every string $w\in L$ with $|w|\geq p$ can be written as

$$
w=xyz
$$

with

$$
|xy|\leq p,\qquad |y|>0,
$$

and

$$
xy^i z\in L
$$

for every $i\geq 0$.

Choose

$$
w=a^{2^p}.
$$

Clearly $w\in L$ and $|w|=2^p\geq p$.

By the pumping lemma, write

$$
w=xyz
$$

where $|xy|\leq p$ and $|y|>0$.

Since $w$ consists only of $a$'s, there exist integers $r,s,t$ such that

$$
x=a^r,\qquad y=a^s,\qquad z=a^t,
$$

where

$$
r+s+t=2^p,
$$

and from the pumping lemma conditions we have

$$
r+s\leq p,\qquad s>0.
$$

In particular,

$$
1\leq s\leq p.
$$

Now pump up once, using $i=2$:

$$
xy^2z = a^{2^p+s}.
$$

We claim that $2^p+s$ is not a power of $2$.

Since $s>0$,

$$
2^p < 2^p+s.
$$

Also, since $s\leq p$ and $p<2^p$ for $p\geq 1$,

$$
2^p+s \leq 2^p+p < 2^p+2^p = 2^{p+1}.
$$

Therefore

$$
2^p < 2^p+s < 2^{p+1}.
$$

There is no power of $2$ strictly between two consecutive powers of $2$. Hence $2^p+s$ is not of the form $2^n$.

So

$$
xy^2z=a^{2^p+s}\notin L.
$$

This contradicts the pumping lemma, which says that $xy^i z\in L$ for every $i\geq 0$.

Therefore $L$ is not regular.

### Common mistake

It is not enough to say that the gaps between powers of $2$ get larger. The pumping lemma proof must show that for every valid decomposition $w=xyz$, one of the pumped strings leaves the language. The bound $1\leq |y|\leq p$ is what lets us prove that $2^p+|y|$ lies strictly between $2^p$ and $2^{p+1}$.

---

## Question 3 (Context-Free Languages). (5 marks)

Consider

$$
L=\{a^i b^j c^k\mid i,j,k\in\mathbb N,\ i=j\text{ or }j=k\}.
$$

We prove that $L$ is context-free.

### Main idea

Write $L$ as the union of two simpler context-free languages:

$$
L_1=\{a^i b^j c^k\mid i=j\},
$$

and

$$
L_2=\{a^i b^j c^k\mid j=k\}.
$$

Then

$$
L=L_1\cup L_2.
$$

The language $L_1$ is

$$
\{a^i b^i c^k\mid i,k\in\mathbb N\},
$$

and the language $L_2$ is

$$
\{a^i b^j c^j\mid i,j\in\mathbb N\}.
$$

Both are context-free. Since context-free languages are closed under union, $L$ is context-free.

To make the proof concrete, we can give a grammar directly.

### Grammar

Let

$$
G=(V,\Sigma,R,S)
$$

where

$$
\Sigma=\{a,b,c\},
$$

and the productions are:

$$
S\to S_1\mid S_2.
$$

The nonterminal $S_1$ generates words where the number of $a$'s equals the number of $b$'s:

$$
S_1\to T C,
$$

$$
T\to aTb\mid \epsilon,
$$

$$
C\to cC\mid \epsilon.
$$

The nonterminal $S_2$ generates words where the number of $b$'s equals the number of $c$'s:

$$
S_2\to A U,
$$

$$
A\to aA\mid \epsilon,
$$

$$
U\to bUc\mid \epsilon.
$$

### Proof that the grammar is correct

First, consider $S_1$.

The nonterminal $T$ generates exactly the language

$$
\{a^i b^i\mid i\in\mathbb N\}.
$$

Indeed, each use of

$$
T\to aTb
$$

adds one $a$ at the front and one $b$ at the back, and the derivation eventually stops with $T\to\epsilon$.

The nonterminal $C$ generates exactly

$$
\{c^k\mid k\in\mathbb N\}.
$$

Therefore $S_1\to TC$ generates exactly

$$
\{a^i b^i c^k\mid i,k\in\mathbb N\}.
$$

These are exactly the words in $a^*b^*c^*$ satisfying $i=j$.

Next, consider $S_2$.

The nonterminal $A$ generates exactly

$$
\{a^i\mid i\in\mathbb N\}.
$$

The nonterminal $U$ generates exactly

$$
\{b^j c^j\mid j\in\mathbb N\}.
$$

Therefore $S_2\to AU$ generates exactly

$$
\{a^i b^j c^j\mid i,j\in\mathbb N\}.
$$

These are exactly the words in $a^*b^*c^*$ satisfying $j=k$.

Since $S$ can choose either $S_1$ or $S_2$, the whole grammar generates exactly

$$
\{a^i b^j c^k\mid i=j\text{ or }j=k\}.
$$

Thus $L$ is context-free.

### Alternative proof using closure

A shorter proof is:

1. $\{a^i b^i c^k\mid i,k\in\mathbb N\}$ is context-free.
2. $\{a^i b^j c^j\mid i,j\in\mathbb N\}$ is context-free.
3. CFLs are closed under union.
4. Therefore their union is context-free.

The grammar above gives an explicit construction of that union.

---

## Question 4 (Decidability). (3 + 2 = 5 marks)

Let $L\subseteq\{0,1\}^*$ be decidable.

### Part 1

We need to show that

$$
L_2=\{w\mid \text{there exists }u\text{ with }|u|\leq |w|\text{ and }wu\in L\}
$$

is decidable.

Since $L$ is decidable, there exists a Turing machine $M$ that decides $L$.

Construct a Turing machine $M_2$ deciding $L_2$ as follows.

On input $w$:

1. Let $n=|w|$.
2. Enumerate all binary strings $u$ with $|u|\leq n$.
3. For each such $u$, run $M$ on the string $wu$.
4. If $M$ accepts $wu$ for at least one such $u$, accept.
5. If no such $u$ works, reject.

### Termination

There are only finitely many binary strings $u$ with $|u|\leq n$.

In fact, there are

$$
1+2+2^2+\cdots+2^n=2^{n+1}-1
$$

such strings.

For each one, $M$ halts because $M$ is a decider for $L$.

Therefore $M_2$ always halts.

### Correctness

The machine $M_2$ accepts $w$ exactly when it finds some $u$ with $|u|\leq |w|$ such that $wu\in L$.

That is exactly the definition of $w\in L_2$.

Hence $M_2$ decides $L_2$, so $L_2$ is decidable.

### Part 2

Now consider

$$
L_3=\{w\mid \text{there exists }u\text{ such that }wu\in L\}.
$$

Question: Is $L_3$ necessarily decidable whenever $L$ is decidable?

The answer is no.

The reason is that the length bound in Part 1 was essential. Without the condition $|u|\leq |w|$, the machine may need to search infinitely many possible extensions $u$.

We now give a counterexample.

### Counterexample

Let $A$ be the halting problem language:

$$
A=\{x\in\{0,1\}^*\mid \text{the Turing machine encoded by }x\text{ halts on input }x\}.
$$

The language $A$ is undecidable.

We will define a decidable language $L$ whose prefix-extension language $L_3$ can decide $A$.

Use the following computable binary coding. For a binary string $x$, define

$$
\operatorname{code}(x)
$$

by replacing each $0$ in $x$ with $00$, and each $1$ in $x$ with $11$. Then use $01$ as a delimiter. Since $01$ never appears inside $\operatorname{code}(x)$, the delimiter is unambiguous.

Define

$$
L=\{\operatorname{code}(x)01\,1^t\mid \text{the Turing machine encoded by }x\text{ halts on input }x\text{ within }t\text{ steps}\}.
$$

Here $t$ is represented in unary by $1^t$.

### Why $L$ is decidable

Given an input string $y$, we can decide whether $y\in L$ as follows:

1. Check whether $y$ has the form

   $$
   \operatorname{code}(x)01\,1^t
   $$

   for some binary string $x$ and some $t\geq 0$.

   This is decidable because the delimiter $01$ is uniquely recognizable.

2. If $y$ is not of that form, reject.
3. If $y=\operatorname{code}(x)01\,1^t$, simulate the Turing machine encoded by $x$ on input $x$ for exactly $t$ steps.
4. Accept if it halts within those $t$ steps; otherwise reject.

This procedure always halts, because it only simulates for a finite number of steps. Therefore $L$ is decidable.

### Why $L_3$ is undecidable

Recall

$$
L_3=\{w\mid \exists u\text{ such that }wu\in L\}.
$$

For any binary string $x$, consider

$$
w_x=\operatorname{code}(x)01.
$$

Then

$$
w_x\in L_3
$$

if and only if there exists some suffix $u$ such that

$$
w_xu\in L.
$$

By the definition of $L$, this happens exactly when there exists some $t$ such that

$$
\operatorname{code}(x)01\,1^t\in L.
$$

That happens exactly when the Turing machine encoded by $x$ halts on input $x$ within some finite number of steps.

Therefore

$$
w_x\in L_3 \quad\Longleftrightarrow\quad x\in A.
$$

If $L_3$ were decidable, then we could decide the halting problem $A$ as follows:

1. Given $x$, compute $w_x=\operatorname{code}(x)01$.
2. Run the decider for $L_3$ on $w_x$.
3. Accept iff the decider says $w_x\in L_3$.

This would decide $A$, contradicting the undecidability of the halting problem.

Therefore $L_3$ is not necessarily decidable, even when $L$ is decidable.

### Key lesson

The bounded version $L_2$ is decidable because it only requires a finite search over possible $u$.

The unbounded version $L_3$ can encode an unbounded search over time, which is exactly the source of undecidability in the halting problem.

---

## Question 5 (Diagonalization). (10 marks)

We need to show that there exists a decidable language

$$
K\subseteq\{0,1\}^*
$$

such that $K$ is not equal to $L(G)$ for any context-free grammar $G$.

In other words, we want a decidable language that is not context-free, and we must prove this using diagonalization.

### Step 1: Enumerate all context-free grammars

Every context-free grammar is a finite object. It can be encoded as a finite binary string.

There are only countably many finite binary strings, so there are only countably many context-free grammars.

Moreover, we can effectively enumerate all context-free grammars over the alphabet $\{0,1\}$:

$$
G_0,G_1,G_2,\ldots
$$

This means there is a Turing machine which, given $k$, outputs the grammar $G_k$.

It is not important if some grammar appears more than once in the list. What matters is that every context-free grammar appears at least once.

### Step 2: Define the diagonal language

Define

$$
K=\{0^k\mid 0^k\notin L(G_k)\}.
$$

So for each grammar $G_k$, we look at the specific string $0^k$ and define $K$ to do the opposite of $G_k$ on that string.

If $G_k$ generates $0^k$, then $K$ does not contain $0^k$.

If $G_k$ does not generate $0^k$, then $K$ does contain $0^k$.

This is the diagonalization step.

### Step 3: $K$ differs from every context-free language

Fix any $k$.

We compare $K$ with $L(G_k)$ on the single string $0^k$.

By definition,

$$
0^k\in K \quad\Longleftrightarrow\quad 0^k\notin L(G_k).
$$

Therefore $K$ and $L(G_k)$ disagree about whether $0^k$ belongs to the language.

Hence

$$
K\neq L(G_k).
$$

Since this holds for every $k$, $K$ is not equal to the language generated by any context-free grammar in the enumeration.

Because every context-free grammar appears somewhere in the enumeration, $K$ is not context-free.

### Step 4: $K$ is decidable

It remains to prove that $K$ is decidable.

The important fact we use is:

> Membership for context-free grammars is decidable.

That is, given a context-free grammar $G$ and a string $w$, there is an algorithm that decides whether $w\in L(G)$.

For example, one can convert $G$ to Chomsky normal form and then use the CYK algorithm.

Now construct a decider $M_K$ for $K$.

On input $w$:

1. Check whether $w$ is of the form $0^k$ for some $k\geq 0$.
2. If not, reject.
3. If $w=0^k$, compute the $k$-th grammar $G_k$ in the enumeration.
4. Use the CFG membership algorithm to decide whether

   $$
   0^k\in L(G_k).
   $$

5. If $0^k\in L(G_k)$, reject.
6. If $0^k\notin L(G_k)$, accept.

### Termination

Every step halts:

- checking whether $w$ is of the form $0^k$ is decidable;
- computing $G_k$ from the effective enumeration halts;
- CFG membership testing halts;
- the final accept/reject step is immediate.

Therefore $M_K$ is a decider.

### Correctness

If $w$ is not of the form $0^k$, then by the definition of $K$, $w\notin K$, so rejecting is correct.

If $w=0^k$, then the machine accepts exactly when

$$
0^k\notin L(G_k),
$$

which is exactly the definition of membership in $K$.

Hence $M_K$ decides $K$.

So $K$ is decidable but not context-free.

### Common mistake

Do not only say “there are countably many CFGs and countably many decidable languages.” That does not prove the result, because both sets are countable. The proof needs a constructive diagonal language and a proof that this language is itself decidable.

---

## Question 6 (NP-completeness). (10 marks)

We need to prove that

$$
\text{VERTEX\_COVER}=\{\langle G,k\rangle\mid G\text{ has a vertex cover of size at most }k\}
$$

is NP-complete.

A vertex cover of an undirected graph $G=(V,E)$ is a subset $C\subseteq V$ such that every edge has at least one endpoint in $C$.

We prove two things:

1. VERTEX_COVER is in NP.
2. VERTEX_COVER is NP-hard.

### Part 1: VERTEX_COVER is in NP

A certificate for membership is a subset

$$
C\subseteq V
$$

with

$$
|C|\leq k.
$$

Given $\langle G,k\rangle$ and a proposed certificate $C$, the verifier checks:

1. $C\subseteq V$;
2. $|C|\leq k$;
3. for every edge $\{u,v\}\in E$, at least one of $u$ or $v$ is in $C$.

This can be done in polynomial time by scanning through the edge list.

Therefore VERTEX_COVER is in NP.

### Part 2: VERTEX_COVER is NP-hard

We reduce from INDEPENDENT_SET.

Use the standard version:

$$
\text{INDEPENDENT\_SET}=\{\langle G,k\rangle\mid G\text{ has an independent set of size at least }k\}.
$$

This problem is NP-complete.

Given an instance

$$
\langle G,k\rangle
$$

of INDEPENDENT_SET, where $G=(V,E)$, define

$$
f(\langle G,k\rangle)=\langle G, |V|-k\rangle.
$$

So the graph stays the same, and the parameter changes from $k$ to $|V|-k$.

Clearly $f$ is computable in polynomial time.

### Key lemma

For any graph $G=(V,E)$ and any subset $I\subseteq V$,

$$
I\text{ is an independent set}
$$

if and only if

$$
V\setminus I\text{ is a vertex cover}.
$$

#### Proof of the lemma

First suppose $I$ is an independent set.

Let $\{u,v\}\in E$ be any edge.

Since $I$ is independent, it is impossible for both $u$ and $v$ to be in $I$.

Therefore at least one of $u$ or $v$ is not in $I$.

Equivalently, at least one of $u$ or $v$ is in $V\setminus I$.

Thus every edge has at least one endpoint in $V\setminus I$, so $V\setminus I$ is a vertex cover.

Conversely, suppose $V\setminus I$ is a vertex cover.

We show that $I$ is independent.

Take any two vertices $u,v\in I$.

If $\{u,v\}$ were an edge, then neither endpoint of this edge would belong to $V\setminus I$.

That would contradict the fact that $V\setminus I$ is a vertex cover.

Therefore no two vertices in $I$ are adjacent, so $I$ is an independent set.

This proves the lemma.

### Correctness of the reduction

We show

$$
\langle G,k\rangle\in \text{INDEPENDENT\_SET}
$$

if and only if

$$
f(\langle G,k\rangle)=\langle G,|V|-k\rangle\in\text{VERTEX\_COVER}.
$$

#### Forward direction

Suppose $G$ has an independent set $I$ of size at least $k$.

Then by the lemma, $V\setminus I$ is a vertex cover.

Its size is

$$
|V\setminus I|=|V|-|I|\leq |V|-k.
$$

Therefore $G$ has a vertex cover of size at most $|V|-k$.

So

$$
\langle G,|V|-k\rangle\in\text{VERTEX\_COVER}.
$$

#### Reverse direction

Suppose

$$
\langle G,|V|-k\rangle\in\text{VERTEX\_COVER}.
$$

Then $G$ has a vertex cover $C$ with

$$
|C|\leq |V|-k.
$$

By the lemma, $V\setminus C$ is an independent set.

Its size is

$$
|V\setminus C|=|V|-|C|\geq k.
$$

Therefore $G$ has an independent set of size at least $k$.

So

$$
\langle G,k\rangle\in\text{INDEPENDENT\_SET}.
$$

### Conclusion

We have shown that

$$
\text{INDEPENDENT\_SET}\leq_p \text{VERTEX\_COVER}.
$$

Since INDEPENDENT_SET is NP-hard, VERTEX_COVER is NP-hard.

Since VERTEX_COVER is also in NP, it is NP-complete.

### Note on “at most $k$”

For vertex cover, the standard decision problem uses “size at most $k$”. This matches the reduction above exactly. If the problem were written with “size exactly $k$”, it would require an extra argument, because a smaller vertex cover does not automatically have exactly size $k$ unless we are allowed to add arbitrary vertices without exceeding $|V|$.

---

## Question 7 (Complexity Classification). (10 marks)

We are given a modular program $P$ with parameter $n$.

Each variable ranges over

$$
\{0,1,\ldots,n-1\}.
$$

The program uses assignments, sequential composition, conditionals, and while-loops, with arithmetic performed modulo $n$.

The decision problem is:

> Given $P$ and $n$, does there exist an initial assignment $\pi$ such that $P$ terminates when run from $\pi$?

The answer is:

$$
\boxed{\text{PSPACE-complete}.}
$$

We prove:

1. the problem is in PSPACE;
2. the problem is PSPACE-hard.

### Part 1: The problem is in PSPACE

Suppose $P$ has $m$ variables.

A configuration of the computation consists of:

1. the current control location, meaning which part of the program is currently being executed;
2. the current values of all variables.

There are at most polynomially many control locations in the size of $P$.

Each variable has $n$ possible values, so the variables have

$$
n^m
$$

possible assignments.

Therefore the number of configurations is at most

$$
O(|P|\cdot n^m).
$$

This number may be exponential in the input size, but it can be counted using only polynomially many bits, because

$$
\log(|P|\cdot n^m)=O(\log |P|+m\log n).
$$

A single configuration can also be stored in polynomial space:

- the control location needs $O(\log |P|)$ bits;
- the values of the $m$ variables need $O(m\log n)$ bits.

Now fix an initial assignment $\pi$.

To decide whether $P$ terminates from $\pi$, simulate the program step by step while maintaining a counter up to

$$
|P|\cdot n^m.
$$

If $P$ terminates before the counter exceeds this bound, accept.

If the simulation exceeds this many steps without terminating, then some configuration must have repeated. Since the program is deterministic once the initial assignment is fixed, repeating a configuration implies that the program will loop forever. So in that case, reject.

This uses only polynomial space.

For the actual problem, we ask whether there exists an initial assignment $\pi$.

A nondeterministic polynomial-space algorithm can:

1. guess the initial values of all variables;
2. simulate $P$ from that assignment using the procedure above.

This places the problem in NPSPACE.

By Savitch's theorem,

$$
\text{NPSPACE}=\text{PSPACE}.
$$

Therefore the problem is in PSPACE.

### Equivalent graph reachability view

Another way to see the same upper bound is to consider the configuration graph of $P$.

- Each node is a configuration.
- There is an edge from configuration $C$ to configuration $C'$ if one execution step takes $C$ to $C'$.
- Terminal configurations are accepting targets.

The question asks whether some initial assignment gives a path to a terminal configuration.

This reachability problem is over an exponentially large graph whose nodes can be represented in polynomial space. Reachability in such implicitly represented graphs is decidable in PSPACE.

### Part 2: PSPACE-hardness

We show that the problem is PSPACE-hard by reducing from the acceptance problem for deterministic polynomial-space Turing machines.

The following problem is PSPACE-complete:

> Given a deterministic Turing machine $M$ and input $x$, where $M$ uses at most polynomial space on inputs of length $|x|$, decide whether $M$ accepts $x$.

Given such an instance $\langle M,x\rangle$, we construct a modular program $P_{M,x}$ such that

$$
P_{M,x}\text{ terminates from some initial assignment}
$$

if and only if

$$
M\text{ accepts }x.
$$

We use parameter

$$
n=2.
$$

So every modular variable is Boolean-valued: it holds either $0$ or $1$.

Even though the modular program syntax only has addition, subtraction, equality tests, assignments, conditionals, and loops, this is enough to simulate Boolean computation.

For example, with $n=2$:

- $1-x$ computes $\neg x$;
- an if-statement can compute conjunction:

  ```text
  if x = 0 then { z := 0 }
  else {
      if y = 0 then { z := 0 }
      else { z := 1 }
  }
  ```

  This sets $z$ to $x\land y$.

- disjunction can be computed similarly.

Therefore modular programs with $n=2$ can simulate Boolean circuits and Boolean control logic.

### Encoding configurations of $M$

Since $M$ uses polynomial space, say at most $p(|x|)$ tape cells, a configuration of $M$ can be represented by polynomially many Boolean variables:

- variables for the tape contents;
- variables for the head position;
- variables for the current state.

For example, we may use one-hot encodings:

- a variable $H_i$ says whether the head is at tape cell $i$;
- variables $S_q$ say whether the current state is $q$;
- variables $T_{i,a}$ say whether tape cell $i$ currently stores symbol $a$.

The number of such variables is polynomial in $|x|$.

The transition function of $M$ is local: it only depends on the current state, the current head position, and the symbol under the head. Thus one transition of $M$ can be implemented by a polynomial-size block of modular program code.

Because assignments are sequential, the program can use temporary variables to store the next configuration before copying it back into the current configuration variables.

### Structure of the modular program

The program $P_{M,x}$ has the following high-level structure:

```text
Check that the initial assignment encodes the initial configuration of M on x.
If it does not, enter an infinite loop.

while current_state is neither accept nor reject do {
    compute the next configuration of M;
}

if current_state is accept then {
    halt;
} else {
    enter an infinite loop;
}
```

The infinite loop can be written, for example, as

```text
while 1 != 0 do { }
```

or using a harmless assignment inside the loop:

```text
while 1 != 0 do { z := z }
```

### Why the initial-assignment issue matters

The problem asks whether there exists an initial assignment from which the program terminates.

If we did not control the initial assignment, the program might terminate from some meaningless assignment that does not correspond to the real initial configuration of $M$.

So the first part of the program checks whether the initial assignment correctly encodes the initial configuration of $M$ on input $x$.

If the assignment is not the correct initial configuration, the program deliberately loops forever.

Thus the only assignments from which termination is possible are assignments encoding the correct initial configuration.

### Correctness of the reduction

If $M$ accepts $x$, then starting from the assignment encoding the initial configuration of $M$ on $x$, the modular program simulates $M$ step by step until the accepting state is reached. Then the program halts. Hence there exists an initial assignment from which $P_{M,x}$ terminates.

If $M$ does not accept $x$, then two cases are possible:

1. the initial assignment does not encode the correct initial configuration, in which case the program loops immediately;
2. the initial assignment does encode the correct initial configuration, in which case the program simulates $M$ and eventually reaches rejection or fails to accept. In either case, the constructed program does not terminate.

For a polynomial-space decider $M$, the computation eventually accepts or rejects; we make the reject case loop forever. Thus the program terminates from some initial assignment exactly when $M$ accepts $x$.

So

$$
M\text{ accepts }x
$$

if and only if

$$
\exists \pi\text{ such that }P_{M,x}\text{ terminates from }\pi.
$$

The construction of $P_{M,x}$ is polynomial in the size of $\langle M,x\rangle$, because it uses only polynomially many variables and polynomially many lines of program code.

Therefore the modular-program termination problem is PSPACE-hard.

### Conclusion

The problem is in PSPACE and PSPACE-hard.

Therefore it is PSPACE-complete.

This is the strongest standard classification currently available. In particular, a polynomial-time algorithm for this problem would imply

$$
\text{P}=\text{PSPACE},
$$

which is a major open problem and is widely believed to be false.

---

## Marking Scheme Summary

| Question | Marks | Main expected components |
|---|---:|---|
| Q1.1 | 2 | Correct NFA/$\epsilon$-NFA for $(01\cup10)^*(0\cup11)$ |
| Q1.2 | 3 | Correct subset construction, accepting subsets, complete DFA transition table |
| Q2 | 5 | Pumping lemma setup, good choice of word, bound on $|y|$, contradiction between consecutive powers of $2$ |
| Q3 | 5 | Recognising the language as a union of two CFLs, correct grammar or PDA construction, proof of both inclusions |
| Q4.1 | 3 | Finite enumeration of all $u$ with $|u|\leq |w|$, termination, correctness |
| Q4.2 | 2 | Correct answer that $L_3$ need not be decidable, with a convincing counterexample or reduction from halting |
| Q5 | 10 | Effective enumeration of CFGs, diagonal language, proof it differs from every CFL, proof the diagonal language is decidable |
| Q6 | 10 | NP membership, reduction from Independent Set, complement lemma, polynomial-time reduction, iff proof |
| Q7 | 10 | PSPACE upper bound via configurations, existential assignment handled by NPSPACE = PSPACE, PSPACE-hardness via polynomial-space TM simulation, conclusion PSPACE-complete |

