# The University of New South Wales

# COMP4141

# Mock Final Exam

# Theory of Computation

Time Allowed: 2 hours + 10 mins reading time

Number of Questions to Answer: Seven

Total Number of Marks: 50

Answer all questions in the answer booklet.

Allowed materials: one A4 double sided sheet of notes only.

Questions have been sorted in increasing order of difficulty. Your opinion may vary.

---

## Question 1 (Finite State Automata). (2 + 3 = 5 marks)

Answer the following by drawing state transition diagrams for the automata requested.

1. Give an $\epsilon$-NFA that accepts the language

   $$
   (01 \cup 10)^*(0 \cup 11).
   $$

2. Convert this automaton to a deterministic finite state automaton over the alphabet $\Sigma = \{0,1\}$.

---

## Question 2 (Regular Languages). (5 marks)

Consider the language

$$
\{a^{2^n} \mid n \in \mathbb{N}\}.
$$

Either prove that this language is regular, or prove that it is not.

---

## Question 3 (Context-Free Languages). (5 marks)

Consider the language

$$
L = \{a^i b^j c^k \mid i,j,k \in \mathbb{N},\ i=j \text{ or } j=k\}.
$$

Either prove that this language is context-free, or prove that it is not.

---

## Question 4 (Decidability). (3 + 2 = 5 marks)

Let $L \subseteq \{0,1\}^*$ be a decidable language.

1. Show that the language

   $$
   L_2 = \{w \mid \text{there exists } u \text{ with } |u| \leq |w| \text{ and } wu \in L\}
   $$

   is decidable.

2. Now consider the language

   $$
   L_3 = \{w \mid \text{there exists } u \text{ such that } wu \in L\}.
   $$

   Is $L_3$ necessarily decidable whenever $L$ is decidable? Give a proof or counterexample.

Informally describe any automata that you need for the proof.

---

## Question 5 (Diagonalization). (10 marks)

Show using diagonalization that there exists a decidable language $K \subseteq \{0,1\}^*$ such that $K$ is not equal to $L(G)$ for any context-free grammar $G$.

Your proof should explicitly justify both of the following points:

1. why the diagonal language you construct is decidable;
2. why it differs from every context-free language.

---

## Question 6 (NP-completeness). (10 marks)

A vertex cover of a graph $G = (V,E)$ is a subset $C \subseteq V$ such that for every edge $\{u,v\} \in E$, at least one of $u$ or $v$ is in $C$. The size of a vertex cover $C$ is the number of vertices it contains.

For example, in a triangle graph with vertices $\{a,b,c\}$ and edges $\{a,b\}, \{b,c\}, \{a,c\}$, the set $\{a,b\}$ is a vertex cover of size $2$.

Show that the problem

$$
\text{VERTEX\_COVER} = \{\langle G,k \rangle \mid G \text{ has a vertex cover of size at most } k\}
$$

is NP-complete.

---

## Question 7 (Complexity Classification). (10 marks)

A modular program is a program in which all variables hold values from $\{0,1,\ldots,n-1\}$ for a given parameter $n$, and which may use the following constructions:

- Expressions $e$ may be formed from variables using the operators $+$ (addition mod $n$), $-$ (subtraction mod $n$), and constants $0,1,\ldots,n-1$.
- If $x$ is a variable and $e$ is an expression, then $x := e$ is a program.
- If $P$ and $Q$ are programs, then $P; Q$ is a program, using sequential composition.
- If $P$ and $Q$ are programs and $e_1,e_2$ are expressions, then

  $$
  \text{if } e_1 = e_2 \text{ then } \{P\} \text{ else } \{Q\}
  $$

  is a program.

- If $P$ is a program and $e_1,e_2$ are expressions, then

  $$
  \text{while } e_1 \neq e_2 \text{ do } \{P\}
  $$

  is a program.

Modular programs run starting from some given initial assignment $\pi$ to their variables, with each variable assigned a value in $\{0,\ldots,n-1\}$, and update this assignment during execution. They may terminate or not, depending on the initial assignment.

What is the computational complexity of the problem of deciding whether, for a given modular program $P$ with parameter $n$, there exists an assignment $\pi$ such that $P$ terminates when run from initial assignment $\pi$? Give reasons to believe, with proof, that your answer is as good as is possible given the current state of knowledge.

---

End of Exam

