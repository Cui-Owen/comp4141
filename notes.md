## Q1. FSA：NFA 构造与 Subset Construction

### 1. ε-NFA 的基本构造思路

Thompson construction 是最稳也最笨的画法，常用结构如下。

| 表达式结构 | ε-NFA 构造方式 |
|---|---|
| 单个符号 `a` | 建两个状态，起点经 `a` 到终点，终点为接受态。 |
| 连接 `R₁R₂` | 先画 `R₁` 和 `R₂`，再从 `R₁` 的接受态加一条 ε 边到 `R₂` 的起始态。 |
| 并 `R₁ ∪ R₂` | 新建一个起始态，用 ε 分别连到 `R₁` 和 `R₂` 的起点；再把两个分支的接受态用 ε 汇合到新的接受态。 |
| 星号 `R*` | 新建起始态和接受态；起始态可 ε 到子 NFA 起点，也可 ε 直接到接受态；子 NFA 的接受态 ε 回到子 NFA 起点，同时也 ε 到新的接受态。 |

这种方法的优点是机械、不会漏语言；缺点是状态数可能比较多。考试中如果题目表达式不复杂，也可以直接根据语言结构画更小的 NFA，但需要保证逻辑清楚。

### 2. 例子：`1(0∪01)*1`

这个语言的串必须以 `1` 开头、以 `1` 结尾，中间部分由若干个 `0` 或 `01` 拼接而成。可以用 5 个状态：

- `q₀ --1--> q₁`：读入开头的 `1`；
- `q₁ --0--> q₂`：开始读循环块；
- `q₂ --ε--> q₁`：表示刚刚读完一个 `0` 块；
- `q₂ --1--> q₃ --ε--> q₁`：表示刚刚读完一个 `01` 块；
- `q₁ --1--> q₄`：读入最后的 `1`，其中 `q₄` 是接受态。

可以用几个短串检查构造是否正确：

- `11`：`q₀ → q₁ → q₄`，接受。
- `101`：`q₀ → q₁ → q₂ →ε q₁ → q₄`，接受。
- `1011`：`q₀ → q₁ → q₂ → q₃ →ε q₁ → q₄`，接受。

写这类验证时，不需要列出很多例子，但至少要能说明“空循环”“走 `0` 分支”“走 `01` 分支”都被覆盖到了。

### 3. Subset Construction

Subset construction 的目标是把 NFA 转成 DFA。DFA 的每一个状态都是 NFA 状态集合的一个子集。

具体步骤如下：

1. 先计算每个 NFA 状态的 `ECLOSE`。`ECLOSE(q)` 包含 `q` 自身，以及从 `q` 出发沿 ε 边能到达的所有状态。
2. DFA 的起始状态是 `ECLOSE(q₀)`，其中 `q₀` 是 NFA 起始态。
3. 对 DFA 中的每一个状态集合 `S` 和每个输入符号 `a`，先从 `S` 中所有状态沿 `a` 边走一步，得到一批 NFA 状态；然后对这些状态整体取 ε-closure。
4. 如果某个 DFA 状态集合中包含任意一个 NFA 接受态，那么这个 DFA 状态就是接受态。

容易出错的地方：

- `ECLOSE` 要包括状态本身。
- ε 边不能反向走。
- 接受态不是只有 `{q₄}` 这种单点集合，而是所有包含 `q₄` 的子集。
- 表格中出现的新状态集合要继续展开，直到没有新集合出现为止。

---

### 补充例题：把 `1(0∪01)*1` 完整转成 DFA

前面已经画了一个 ε-NFA。考试中如果题目要求 subset construction，只写“用 subset construction”不够，最好能列出 ε-closure 和 DFA transition table。

#### 原 NFA 的转移

使用前面 5 个状态：

- `q0 --1--> q1`
- `q1 --0--> q2`
- `q2 --ε--> q1`
- `q2 --1--> q3`
- `q3 --ε--> q1`
- `q1 --1--> q4`

其中 `q0` 是起始态，`q4` 是接受态。

#### 先算每个状态的 ε-closure

这里不能省略状态本身。


a) `ECLOSE(q0) = {q0}`，因为 `q0` 没有 ε 边。

b) `ECLOSE(q1) = {q1}`，因为 `q1` 没有 ε 边。

c) `ECLOSE(q2) = {q2, q1}`，因为 `q2` 可以经 ε 到 `q1`。

d) `ECLOSE(q3) = {q3, q1}`，因为 `q3` 可以经 ε 到 `q1`。

e) `ECLOSE(q4) = {q4}`。

这一步的检查方法是：每个集合至少包含自己；然后只沿 ε 边正向走，不能把普通字符边也算进去。

#### 从 DFA 起始状态开始展开

DFA 起始状态是：

\[
A = ECLOSE(q0) = \{q0\}.
\]

下面对每个新出现的集合，分别看输入 `0` 和 `1`。

| DFA 状态 | 读 `0` 后 | 读 `1` 后 | 是否接受 |
|---|---|---|---|
| `A = {q0}` | `∅` | `{q1}` | 否 |
| `B = {q1}` | `{q1,q2}` | `{q4}` | 否 |
| `C = {q1,q2}` | `{q1,q2}` | `{q1,q3,q4}` | 否 |
| `D = {q4}` | `∅` | `∅` | 是 |
| `E = {q1,q3,q4}` | `{q1,q2}` | `{q4}` | 是 |
| `F = ∅` | `∅` | `∅` | 否 |

其中有几格要特别说明：

- 从 `{q1}` 读 `0`，先到 `{q2}`，再取 `ECLOSE(q2)`，得到 `{q1,q2}`。
- 从 `{q1,q2}` 读 `1`，`q1` 可到 `q4`，`q2` 可到 `q3`，所以先得到 `{q4,q3}`；再取 ε-closure，`q3` 还能到 `q1`，所以得到 `{q1,q3,q4}`。
- 只要集合里含有 `q4`，这个 DFA 状态就是接受态，所以 `{q4}` 和 `{q1,q3,q4}` 都是接受态。

#### 考试中怎么写才够

如果时间紧，至少写三部分：

1. 写清楚所有 ε-closure；
2. 写出 reachable subsets 的转移表；
3. 标出接受态，并说明“含有 NFA 接受态 `q4` 的子集都是 DFA 接受态”。

不要把所有 `2^5` 个子集都列出来。Subset construction 只需要展开从起始集合可达的子集。

---

## Q2. Pumping Lemma：证明语言不是 regular

### 1. Regular language 的 pumping lemma

如果 `L` 是 regular，那么存在一个 pumping length `p ≥ 1`，使得任意满足 `w ∈ L` 且 `|w| ≥ p` 的串都可以拆成

\[
w = xyz
\]

并满足：

1. `|xy| ≤ p`；
2. `|y| ≥ 1`；
3. 对所有 `i ≥ 0`，都有 `xy^i z ∈ L`。

证明一个语言不是 regular 时，通常用反证法：

1. 假设 `L` 是 regular，令 `p` 为 pumping length。
2. 选一个合适的 `w ∈ L`，并保证 `|w| ≥ p`。
3. 根据 pumping lemma，任意合法拆分 `w = xyz` 都满足 `|xy| ≤ p` 且 `|y| ≥ 1`。
4. 找一个 `i`，通常是 `0` 或 `2`，使得 `xy^i z ∉ L`，得到矛盾。

关键是：`y` 的位置和长度由 `|xy| ≤ p` 限制，但 `y` 具体是什么不能由我们指定。因此证明时要覆盖所有可能的合法拆分。

### 2. 常见语言的选词和矛盾方式

| 语言 | 可选的 `w` | pump 后的形式 | 主要矛盾 |
|---|---|---|---|
| `{a^{2^n}}` | `a^{2^p}` | `a^{2^p+s}`，其中 `1 ≤ s ≤ p` | `2^p < 2^p+s ≤ 2^p+p < 2·2^p = 2^{p+1}`，长度落在两个相邻的 2 的幂之间。 |
| `{a^{n^2}}` | `a^{p^2}` | `a^{p^2+s}` | `p^2 < p^2+s ≤ p^2+p < (p+1)^2`，长度不是完全平方数。 |
| `{a^{n!}}` | `a^{p!}` | `a^{p!+s}` | `p! < p!+s ≤ p!+p < (p+1)!`，长度落在两个相邻阶乘之间。 |
| `{a^n b^n}` | `a^p b^p` | pump `y` 会只改变 `a` 的个数 | 因为 `|xy| ≤ p`，`y` 全在前面的 `a` 段里，pump 后 `a` 和 `b` 数量不等。 |
| `{a^n b^n c^n}` | `a^p b^p c^p` | regular pumping 下 `y` 仍受前 `p` 位限制 | `y` 全在 `a` 段，pump 后三段数量不再相等。 |

注意：`s ≤ p` 的理由是 `y` 位于前 `p` 个字符之内，即 `|xy| ≤ p`，不是因为整个串长度为某个值。

### 3. 什么时候用 pump down

取 `i = 0` 会删除 `y`，适合那些“长度或数量不能减少”的语言。例如对 `{a^n b^n}`，pump down 会减少 `a` 的个数，但 `b` 的个数保持不变。对某些长度型语言，也可以用 pump down 让长度落到不合法区间。

如果语言涉及素数长度、平方长度、指数长度等，选 `i = 0` 或 `i = 2` 都可能可行，关键是要写出严格的不等式，而不是只说“显然不在语言里”。

### 4. Myhill–Nerode 方法

Myhill–Nerode 常用于证明某语言不是 regular。核心概念是“可区分”。

两个串 `x` 和 `y` 对语言 `L` 可区分，指存在某个后缀 `z`，使得：

- `xz ∈ L` 且 `yz ∉ L`；或
- `xz ∉ L` 且 `yz ∈ L`。

如果可以找到无穷多个两两可区分的串，则 `L` 有无穷多个等价类，因此不是 regular。

以 `L = {a^n b^n | n ≥ 0}` 为例：

取串族

\[
S = \{a^n \mid n ≥ 0\}.
\]

对任意 `i ≠ j`，用后缀 `z = b^i` 区分 `a^i` 和 `a^j`：

- `a^i b^i ∈ L`；
- `a^j b^i ∉ L`，因为 `a` 和 `b` 的个数不同。

因此 `a^i` 与 `a^j` 可区分。由于这样的串有无穷多个，`L` 不是 regular。

写证明时要明确四点：构造的串族是什么，区分后缀是什么，一个拼接串为什么在语言中，另一个为什么不在语言中。

---

### Regular Pumping Lemma 例题

#### 例题一：证明 `L = {a^n b^n | n ≥ 0}` 不是 regular

这是最基本的题，但很多人会在“任意拆分”上写得太快。

假设 `L` 是 regular。令 `p` 是 pumping length。选取

\[
w = a^p b^p.
\]

显然 `w ∈ L` 且 `|w| = 2p ≥ p`。

根据 pumping lemma，`w` 可以写成 `xyz`，并满足：

\[
|xy| ≤ p, \quad |y| ≥ 1.
\]

由于 `w` 的前 `p` 个字符全是 `a`，而 `xy` 的长度不超过 `p`，所以 `x` 和 `y` 都完全落在前面的 `a` 段里。因此存在某个 `s ≥ 1`，使得

\[
y = a^s.
\]

注意这里不是我们“选择”了 `y=a^s`，而是由 `|xy|≤p` 被迫推出 `y` 只能长这样。

取 `i = 0`，得到：

\[
xy^0z = xz = a^{p-s}b^p.
\]

因为 `s ≥ 1`，所以 `p-s < p`。此时 `a` 的数量小于 `b` 的数量，因此

\[
a^{p-s}b^p \notin L.
\]

这与 pumping lemma 要求“对所有 `i ≥ 0` 都在 `L` 中”矛盾。所以 `L` 不是 regular。

考试时这题不要写成：“pump 掉一些 `a` 后显然不行”。更完整的写法是写出 `y=a^s`、`s≥1`、pump 后变成 `a^{p-s}b^p`，再说明数量不等。

#### 例题二：证明 `L = {a^{n^2} | n ≥ 0}` 不是 regular

这题容易丢分的地方是不等式要写严格。

假设 `L` 是 regular，令 `p` 为 pumping length。选

\[
w = a^{p^2}.
\]

则 `w ∈ L`，因为 `p^2` 是完全平方数，并且 `|w| = p^2 ≥ p`。

由 pumping lemma，`w = xyz`，其中：

\[
|xy| ≤ p, \quad |y| ≥ 1.
\]

设 `|y| = s`。由于 `y` 位于前 `p` 个字符中，所以

\[
1 ≤ s ≤ p.
\]

取 `i = 2`，则 pump 后长度变为：

\[
|xy^2z| = p^2 + s.
\]

接下来证明这个长度不是完全平方数。因为

\[
p^2 < p^2+s ≤ p^2+p < p^2+2p+1 = (p+1)^2.
\]

所以 `p^2+s` 严格落在两个相邻平方数 `p^2` 和 `(p+1)^2` 之间，不可能是完全平方数。因此

\[
xy^2z \notin L.
\]

矛盾。所以 `L` 不是 regular。

这里 `p^2+p < (p+1)^2` 的原因是 `(p+1)^2 = p^2+2p+1`，而 `p ≥ 1`。考试中把这条不等式写出来，会比一句“不是平方数”安全很多。

#### 例题三：证明 `L = {a^i b^j | i > j}` 不是 regular

这类“不等式语言”也常见。可以选边界附近的词。

假设 `L` regular，令 `p` 为 pumping length。选

\[
w = a^{p+1}b^p.
\]

这个串在 `L` 中，因为 `a` 的数量是 `p+1`，`b` 的数量是 `p`，满足 `p+1>p`。

任意合法拆分 `w=xyz` 满足 `|xy|≤p` 和 `|y|≥1`。前 `p` 个字符全是 `a`，所以 `y=a^s`，其中 `s≥1`。

取 `i=0`，得到

\[
xy^0z = a^{p+1-s}b^p.
\]

由于 `s≥1`，有

\[
p+1-s ≤ p.
\]

于是 pump 后 `a` 的数量不再大于 `b` 的数量，所以该串不属于 `L`。矛盾。因此 `L` 不是 regular。

这题的经验是：如果语言条件是 `i>j`，选词时不要选 `a^{2p}b^p` 这种离边界太远的串，因为 pump down 一次未必能破坏条件。选 `a^{p+1}b^p` 这种刚好只多一个的串更好。

#### Regular pumping lemma 的常见失误

1. 不能先指定 `y`。你不能写“令 `y=a`”。正确写法是：由 `|xy|≤p` 推出 `y` 落在某一段，因此 `y=a^s`，其中 `s≥1`。

2. 选的 `w` 必须依赖 `p`。如果选 `w=a^{10}b^{10}`，当 pumping length 大于 20 时，这个串不满足 `|w|≥p`。

3. 矛盾必须对所有合法拆分成立。你只找到某一种拆分不行，不足以证明语言不是 regular。

4. Pumping lemma 只能证明“不是 regular”，不能证明“是 regular”。如果一个语言满足某些 pumping 行为，也不能直接推出它 regular。

---

### Myhill–Nerode 例题

#### 例题：证明 `L = {w ∈ {0,1}* | w 中 0 和 1 的数量相等}` 不是 regular

这题用 pumping lemma 可以做，但 Myhill–Nerode 更直接。

考虑无穷串族：

\[
S = \{0^n \mid n ≥ 0\}.
\]

要证明它们两两可区分。取任意 `i ≠ j`。不妨直接用后缀

\[
z = 1^i.
\]

那么：

\[
0^i1^i ∈ L,
\]

因为 0 和 1 的数量都是 `i`。但

\[
0^j1^i \notin L,
\]

因为 `j ≠ i`，0 的数量和 1 的数量不同。

所以 `0^i` 和 `0^j` 可以被后缀 `1^i` 区分。由于 `i,j` 任意，集合 `S` 中有无穷多个两两可区分的串。因此 `L` 有无穷多个 Myhill–Nerode 等价类，不是 regular。

#### 什么时候优先用 Myhill–Nerode

如果题目中的语言形式像 `a^n b^n`、数量相等、前缀记忆、需要记住任意大的数，Myhill–Nerode 经常比 pumping lemma 更简洁。

写法固定为：

1. 给出无限串族 `S`；
2. 对任意两个不同串 `x_i, x_j`，给出区分后缀 `z`；
3. 证明 `x_i z` 在语言里，而 `x_j z` 不在；
4. 得出无穷多等价类，因此不是 regular。

---

### CFL Pumping Lemma：证明语言不是 context-free

#### 1. CFL 的 pumping lemma

如果 `L` 是 context-free language，那么存在一个 pumping length `p`，使得任意 `w ∈ L` 且 `|w| ≥ p` 的串都可以拆成

\[
w = uvxyz
\]

并满足：

1. `|vxy| ≤ p`；
2. `|vy| ≥ 1`；
3. 对所有 `i ≥ 0`，都有 `uv^i x y^i z ∈ L`。

和 regular pumping lemma 相比，这里被同步 pump 的是 `v` 和 `y`，中间的 `x` 不变。`|vxy| ≤ p` 表示 `vxy` 位于原串的一个长度最多为 `p` 的连续窗口中。

证明步骤仍然是反证法：

1. 假设 `L` 是 CFL，令 `p` 为 pumping length。
2. 选取合适的 `w ∈ L`，并保证 `|w| ≥ p`。
3. 对任意拆分 `w = uvxyz`，利用 `|vxy| ≤ p` 分析 `v` 和 `y` 可能落在哪些段。
4. 取 `i = 0` 或 `i = 2`，说明 pump 后的串不在 `L` 中，得到矛盾。

#### 2. 常见语言的处理

| 语言 | 常用选词 | 反驳思路 |
|---|---|---|
| `{a^n b^n c^n}` | `a^p b^p c^p` | `vxy` 长度不超过 `p`，最多跨两段，不可能同时覆盖三段。pump 后至少有一段数量没有同步变化，三段数量不再相等。 |
| `{a^n b^n c^n d^n}` | `a^p b^p c^p d^p` | 同理，`v` 和 `y` 无法同时维持四段数量相等。 |
| `{0^{2^n}}` | `0^{2^p}` | pump 后长度介于两个相邻的 2 的幂之间，因此不在语言中。 |
| `{ww | w ∈ {0,1}*}` | `0^p1^p0^p1^p` | `vxy` 只覆盖长度有限的连续区域。pump 后通常会破坏前半和后半完全相同的结构。 |

以 `{a^n b^n c^n}` 为例，更完整的分析应当说明：

- 如果 `v` 和 `y` 只位于同一段，比如都在 `a` 段，那么 pump 后只改变 `a` 的数量。
- 如果 `vxy` 跨越 `a/b` 边界，则 pump 后只会影响 `a` 和 `b`，`c` 的数量不变。
- 如果 `vxy` 跨越 `b/c` 边界，则 pump 后只会影响 `b` 和 `c`，`a` 的数量不变。
- 因为 `|vxy| ≤ p`，它不可能同时跨过 `a/b` 和 `b/c` 两个边界。

所以无论如何，取 `i = 0` 或 `i = 2` 后，三段数量都会失衡。

---

### CFL Pumping Lemma 例题

#### 例题：证明 `L = {a^n b^n c^n | n ≥ 0}` 不是 CFL

假设 `L` 是 context-free。令 `p` 为 CFL pumping length。选

\[
w = a^p b^p c^p.
\]

显然 `w ∈ L`，且长度至少为 `p`。

由 CFL pumping lemma，存在拆分

\[
w = uvxyz
\]

满足：

\[
|vxy|≤p, \quad |vy|≥1,
\]

并且对所有 `i≥0`，`uv^i xy^i z∈L`。

关键分析是 `vxy` 的位置。原串分成三段：

\[
a^p \; b^p \; c^p.
\]

由于每一段长度都是 `p`，而 `|vxy|≤p`，所以 `vxy` 不可能同时覆盖 `a/b` 和 `b/c` 两个边界。它只能处于以下情况之一：

1. 完全在 `a` 段内；
2. 完全在 `b` 段内；
3. 完全在 `c` 段内；
4. 跨越 `a` 和 `b`；
5. 跨越 `b` 和 `c`。

不管是哪种情况，`v` 和 `y` 至少不会同时影响三段 `a,b,c` 的数量。

取 `i=0`。因为 `|vy|≥1`，删除 `v` 和 `y` 至少会减少某些字符数量。

- 如果 `v,y` 只涉及一段，那么 pump down 后只有这一段数量减少，另外两段不变，三段数量不可能继续相等。
- 如果 `v,y` 涉及 `a` 和 `b` 两段，那么 `c` 的数量仍然是 `p`，而 `a` 或 `b` 至少有一段减少，所以三段不可能都等于 `p`。
- 如果 `v,y` 涉及 `b` 和 `c` 两段，同理，`a` 的数量仍然是 `p`，而 `b` 或 `c` 至少有一段减少。

因此 `uv^0xy^0z ∉ L`，与 pumping lemma 矛盾。所以 `L` 不是 CFL。

#### 例题：证明 `L = {ww | w ∈ {0,1}*}` 不是 CFL 的思路

这题完整 case analysis 比较长，但考试中常用选词是：

\[
w = 0^p1^p0^p1^p.
\]

这个串属于 `L`，因为它可以写成：

\[
(0^p1^p)(0^p1^p).
\]

CFL pumping lemma 给出 `w=uvxyz`，其中 `|vxy|≤p`。因为每一段 `0^p` 或 `1^p` 的长度都是 `p`，所以 `vxy` 最多只能落在一个相邻边界附近，不可能同时覆盖前半串和后半串的对应位置。

取 `i=0` 或 `i=2` 后，只会改变局部一小段，而不会在两半的对应位置同步改变。因此前半部分和后半部分不再完全相同，pump 后不属于 `{ww}`。

如果要写得更严谨，可以分情况：

- `vxy` 完全在某一段内：只改变其中一段的长度，四段结构不再保持 `0^r1^s0^r1^s`。
- `vxy` 跨越第一、二段边界：只改变前半部分，后半部分不变。
- `vxy` 跨越第二、三段边界：会改变中间连接处，但两半的边界位置被破坏。
- `vxy` 跨越第三、四段边界：只改变后半部分，前半部分不变。

这题比 `{a^n b^n c^n}` 更容易写乱。考试中如果没有要求一定用 CFL pumping lemma，遇到 copy language 有时可以尝试 closure property 或其他方法；但如果要求 pumping lemma，就必须把“局部改变不能保持两半完全相同”讲清楚。

---

## Q3. CFG 设计与正确性证明

### 1. 常见 CFG 结构

CFG 题通常考的是如何用递归规则控制数量关系。下面是一些常见模式。

| 目标条件 | 一种可用的文法 |
|---|---|
| `{a^n b^m | n = m}` | `S → aSb | ε` |
| `{a^n b^m | n > m}` | `S → aSb | aA`，`A → aA | ε` |
| `{a^n b^m | n < m}` | `S → aSb | bB`，`B → bB | ε` |
| `{a^n b^m | n - m ≥ 2}` | `S → aSb | aaA`，`A → aA | ε` |
| `{a^n b^m c^m}` | `S → aS | T`，`T → bTc | ε` |
| `{a^n b^n c^m}` | `S → XY`，`X → aXb | ε`，`Y → cY | ε` |

设计文法时可以先判断数量关系是“同步增长”还是“独立增长”：

- `a^n b^n` 这种需要同步增长，用 `S → aSb`。
- 某一段可以任意多，例如 `c^m`，就单独用 `Y → cY | ε`。
- 如果要求差值至少为某个常数，例如 `n - m ≥ 2`，可以在递归结束时一次性多生成两个 `a`，剩下的额外 `a` 再交给另一个非终结符生成。

### 2. 正确性证明：两个方向都要写

证明 CFG 正确时，不能只说“这个文法显然生成目标语言”。通常要证明：

\[
L(G) \subseteq L
\]

和

\[
L \subseteq L(G).
\]

第一部分说明文法不会生成非法串；第二部分说明所有合法串都能被文法生成。

### 3. 例子：`{a^n b^m | n - m ≥ 2}`

考虑文法：

\[
S \to aSb \mid aaA,\qquad A \to aA \mid \varepsilon.
\]

证明 `L(G) ⊆ L`：

设某个推导中先使用 `S → aSb` 共 `k` 次，然后使用 `S → aaA` 结束递归，最后在 `A` 中使用 `A → aA` 共 `j` 次，并用 `A → ε` 结束，其中 `k, j ≥ 0`。

生成的串为：

\[
a^{k+2+j} b^k.
\]

这里 `n = k + 2 + j`，`m = k`，所以

\[
n - m = 2 + j ≥ 2.
\]

因此文法生成的每个串都满足目标条件。

证明 `L ⊆ L(G)`：

任取 `a^n b^m ∈ L`，即 `n - m ≥ 2`。令：

\[
k = m,\qquad j = n - m - 2.
\]

因为 `n - m ≥ 2`，所以 `j ≥ 0`。先使用 `S → aSb` 共 `k` 次，得到 `a^k S b^k`；再使用 `S → aaA`，得到 `a^{k+2} A b^k`；最后在 `A` 中使用 `A → aA` 共 `j` 次，再使用 `A → ε`。最终得到

\[
a^{k+2+j}b^k = a^n b^m.
\]

因此所有满足条件的串都能由该文法生成。

这里 `j ≥ 0` 的理由要写出来，否则证明不完整。

---

### CFG 设计例题

#### 例题：为 `L = {a^i b^j c^k | i=j 或 j=k}` 设计 CFG

这个语言是两个 CFL 的并。可以把它拆成两部分：

\[
L_1 = \{a^i b^i c^k \mid i,k≥0\},
\]

\[
L_2 = \{a^i b^j c^j \mid i,j≥0\}.
\]

于是设计文法：

```text
S  -> S1 | S2
S1 -> X C
X  -> a X b | ε
C  -> c C | ε
S2 -> A Y
A  -> a A | ε
Y  -> b Y c | ε
```

解释：

- `S1` 负责生成满足 `i=j` 的串。`X -> aXb | ε` 同步生成相同数量的 `a` 和 `b`，`C` 额外生成任意多个 `c`。
- `S2` 负责生成满足 `j=k` 的串。`A` 先生成任意多个 `a`，`Y -> bYc | ε` 同步生成相同数量的 `b` 和 `c`。

正确性证明可以这样写：

`L(G) ⊆ L`：如果推导走 `S -> S1`，生成的串形如 `a^i b^i c^k`，满足 `i=j`，所以属于 `L`。如果推导走 `S -> S2`，生成的串形如 `a^i b^j c^j`，满足 `j=k`，所以也属于 `L`。

`L ⊆ L(G)`：任取 `a^i b^j c^k ∈ L`。如果 `i=j`，走 `S -> S1`，用 `X` 生成 `a^i b^i`，用 `C` 生成 `c^k`。如果 `j=k`，走 `S -> S2`，用 `A` 生成 `a^i`，用 `Y` 生成 `b^j c^j`。因此所有合法串都能生成。

注意：如果一个串同时满足 `i=j` 和 `j=k`，它可能有两种推导。这不影响 CFG 的正确性。题目通常不要求文法 unambiguous，除非特别说明。

#### 例题：为 `L = {a^n b^m | n ≠ m}` 设计 CFG

可以把 `n≠m` 拆成两种情况：

\[
n>m \quad \text{或} \quad n<m.
\]

文法：

```text
S  -> S_more_a | S_more_b
S_more_a -> a S_more_a b | A
A -> a A | a
S_more_b -> a S_more_b b | B
B -> b B | b
```

解释：

- `S_more_a` 先用 `a S_more_a b` 同步生成相同数量的 `a` 和 `b`，最后用 `A` 至少多生成一个 `a`。
- `S_more_b` 同理，最后用 `B` 至少多生成一个 `b`。

证明时注意 `A -> aA | a` 表示至少一个 `a`，不是任意多个包括 0 个。如果写成 `A -> aA | ε`，就会把 `n=m` 的串也生成出来。

#### CFG 题的实用检查表

写完文法后，至少检查以下几件事：

1. 是否会生成不符合顺序的串。例如目标是 `a*b*c*`，文法不能让 `c` 后面又出现 `b`。
2. 是否错误包含空串。很多题默认 `n≥1` 或 `n≥0`，要看清楚。
3. “至少一个”和“任意多个”有没有区分。至少一个用 `A -> aA | a`，任意多个用 `A -> aA | ε`。
4. 如果是“或”的条件，通常用 `S -> S1 | S2`。
5. 如果是“且”的条件，通常要让同一个递归过程同时维护两个约束，不能简单并列两个文法。

---

### CNF 转换与 CYK 算法

#### 1. Chomsky Normal Form

CYK 算法通常要求 CFG 已经是 Chomsky Normal Form。CNF 的规则形式为：

- `A → BC`，其中 `B` 和 `C` 是非终结符；
- `A → a`，其中 `a` 是终结符；
- 如果语言包含空串，可以额外允许起始符号 `S → ε`。

#### 2. CNF 转换步骤

转换时建议按固定顺序做：

1. **消除 ε-rule。**  
   找出所有 nullable 非终结符，即可以推出 ε 的变量。对每条包含 nullable 变量的规则，补上省略这些变量后的版本。最后删除原来的 ε-rule。若原语言包含 ε，则保留新的起始符号推出 ε 的规则。

2. **消除 unit rule。**  
   Unit rule 是形如 `A → B` 的规则。找到所有通过 unit rule 可达的变量，把后者的非 unit 规则复制到前者下面，再删除 unit rule。

3. **拆分长度超过 2 的规则。**  
   例如 `A → B₁B₂B₃B₄` 可拆成：
   \[
   A → B₁C₁,\quad C₁ → B₂C₂,\quad C₂ → B₃B₄.
   \]
   具体新变量名不重要，但要保持规则等价。

4. **隔离长规则中的终结符。**  
   如果出现 `A → aB` 或 `A → BaC` 这类混合规则，要引入新变量，如 `T_a → a`，再把原规则中的 `a` 替换为 `T_a`。

顺序不要随意交换。尤其是 ε-rule 和 unit rule 如果没处理干净，后面的 CNF 形式会被破坏。

#### 3. CYK 算法

CYK 用动态规划判断一个串 `w = w₁w₂...wₙ` 是否属于某个 CNF 文法的语言。

定义表格：

\[
T[i,j] = \{A \mid A \Rightarrow^* w_i...w_j\}.
\]

填写方式：

1. **长度为 1 的子串：**
   \[
   T[i,i] = \{A \mid A → w_i \text{ 是规则}\}.
   \]

2. **长度大于 1 的子串：**  
   对每个区间 `[i,j]`，枚举切分点 `k`，其中 `i ≤ k < j`。如果有规则 `A → BC`，并且
   \[
   B ∈ T[i,k],\quad C ∈ T[k+1,j],
   \]
   那么把 `A` 加入 `T[i,j]`。

3. **最终判断：**  
   若起始符号 `S ∈ T[1,n]`，则 `w ∈ L(G)`；否则 `w ∉ L(G)`。

写 CYK 表时，要注意区间下标。很多错误不是算法不懂，而是把 `T[i,k]` 和 `T[k+1,j]` 的范围写错。

---

### CNF 转换与 CYK 例题

#### CNF 转换完整例子

给定文法：

```text
S -> AB | aB
A -> a | ε
B -> b | A
```

目标是转成 CNF。因为原文法可以生成空串：`S -> AB -> εε`，所以最后需要允许新起始符号推出 `ε`。

#### Step 1：加新起始符号

先加：

```text
S0 -> S
```

如果最终语言包含空串，就保留 `S0 -> ε`。

#### Step 2：消除 ε-rule

Nullable 变量：

- `A` nullable，因为 `A -> ε`；
- `B` nullable，因为 `B -> A -> ε`；
- `S` nullable，因为 `S -> AB`，而 `A`、`B` 都 nullable。

处理 `S -> AB`：因为 `A` 和 `B` 都 nullable，所以补上：

```text
S -> AB | A | B
```

原本还会产生 `S -> ε`，但空串统一交给新起始符号 `S0 -> ε` 处理。

处理 `S -> aB`：因为 `B` nullable，所以补上：

```text
S -> aB | a
```

删去 `A -> ε` 后得到：

```text
S0 -> S | ε
S  -> AB | A | B | aB | a
A  -> a
B  -> b | A
```

#### Step 3：消除 unit rule

Unit rules 有：

```text
S0 -> S
S  -> A
S  -> B
B  -> A
```

把被指向变量的非 unit 规则复制过来。

- `A` 的非 unit 规则是 `A -> a`；
- `B` 的非 unit 规则是 `B -> b` 和经 `A` 得到的 `B -> a`；
- `S` 的非 unit 规则包括 `AB`, `aB`, `a`，再加上从 `A`、`B` 得到的 `a`, `b`。

消除后可以写成：

```text
S0 -> AB | aB | a | b | ε
S  -> AB | aB | a | b
A  -> a
B  -> b | a
```

#### Step 4：隔离长规则中的终结符

`aB` 不是 CNF，因为长度为 2 的规则中不能混入终结符。引入：

```text
Ta -> a
```

把 `aB` 改成 `Ta B`。最终 CNF 为：

```text
S0 -> AB | Ta B | a | b | ε
S  -> AB | Ta B | a | b
A  -> a
B  -> b | a
Ta -> a
```

这已经满足 CNF：右边要么是两个非终结符，要么是一个终结符；起始符号额外允许推出 `ε`。

#### CYK 完整例子

给定 CNF 文法：

```text
S -> AB | BC
A -> BA | a
B -> CC | b
C -> AB | a
```

判断字符串

```text
w = baaba
```

是否属于该文法语言。

位置编号：

```text
1 2 3 4 5
b a a b a
```

#### 长度 1

根据终结符规则：

- `b` 可由 `B` 生成；
- `a` 可由 `A` 或 `C` 生成。

所以：

| 子串 | 集合 |
|---|---|
| `T[1,1]=b` | `{B}` |
| `T[2,2]=a` | `{A,C}` |
| `T[3,3]=a` | `{A,C}` |
| `T[4,4]=b` | `{B}` |
| `T[5,5]=a` | `{A,C}` |

#### 长度 2

- `T[1,2]` 对应 `ba`。左 `{B}`，右 `{A,C}`。`BA -> A`，`BC -> S`，所以 `{A,S}`。
- `T[2,3]` 对应 `aa`。`C C -> B`，所以 `{B}`。
- `T[3,4]` 对应 `ab`。`A B -> S`，`A B -> C`，所以 `{S,C}`。
- `T[4,5]` 对应 `ba`。同 `T[1,2]`，得到 `{A,S}`。

#### 长度 3

- `T[1,3]` 对应 `baa`。切分 `b|aa` 得 `{B}` 和 `{B}`，没有规则；切分 `ba|a` 得 `{A,S}` 和 `{A,C}`，也没有匹配规则。因此为空集。
- `T[2,4]` 对应 `aab`。切分 `a|ab`，左 `{A,C}`，右 `{S,C}`，其中 `CC -> B`，所以得到 `{B}`。其他切分不增加新变量。
- `T[3,5]` 对应 `aba`。切分 `ab|a`，左 `{S,C}`，右 `{A,C}`，其中 `CC -> B`，所以得到 `{B}`。

#### 长度 4

- `T[1,4]` 对应 `baab`。所有切分都没有产生可用规则，所以为空集。
- `T[2,5]` 对应 `aaba`。切分 `a|aba`，左 `{A,C}`，右 `{B}`，由 `AB -> S` 和 `AB -> C` 得 `{S,C}`。切分 `aa|ba`，左 `{B}`，右 `{A,S}`，由 `BA -> A` 得 `{A}`。切分 `aab|a`，左 `{B}`，右 `{A,C}`，由 `BA -> A`、`BC -> S` 得 `{A,S}`。合并得到 `{A,S,C}`。

#### 长度 5

`T[1,5]` 对应整个串 `baaba`。

- 切分 `b|aaba`：`{B}` 与 `{A,S,C}`，由 `BA -> A`、`BC -> S` 得 `{A,S}`。
- 切分 `ba|aba`：`{A,S}` 与 `{B}`，由 `AB -> S`、`AB -> C` 得 `{S,C}`。
- 其他切分不增加新变量。

所以：

\[
T[1,5] = \{A,S,C\}.
\]

因为起始符号 `S ∈ T[1,5]`，所以

\[
baaba ∈ L(G).
\]

CYK 写题时不必每一格都解释这么长，但至少要把表格填对，并在最后检查起始符号是否出现在右上角。

---

### PDA 设计思路

PDA 可以看作带栈的 NFA。状态通常用来记录当前处理到哪一段，栈用于保存之后需要匹配的数量信息。

#### 1. 状态和栈分别负责什么

- 状态：表示阶段，例如“正在读 `a`”“准备转入匹配阶段”“正在读 `b`”。
- 栈：保存尚未匹配的符号，例如每读一个 `a` 就压入一个 `A`，之后每读一个 `b` 就弹出一个 `A`。

一个常见的三阶段结构是：

```text
阶段 1：读第一段，每读一个符号就 push。
阶段 2：用 ε-transition 非确定性切换阶段。
阶段 3：读第二段，每读一个符号就 pop。
```

#### 2. `{a^n b^n}` 的 PDA

可用三个状态来描述：

- 在 `q₀` 中，读到 `a` 就 push 一个 `A`；
- 从 `q₀` 通过 ε-transition 转到 `q₁`，表示开始读 `b`；
- 在 `q₁` 中，读到 `b` 就 pop 一个 `A`；
- 输入读完且栈中没有未匹配的 `A` 时接受。

可以写成转移形式：

- `q₀ --(a, ε / A)--> q₀`
- `q₀ --(ε, ε / ε)--> q₁`
- `q₁ --(b, A / ε)--> q₁`

如果使用栈底符号 `$`，则一开始先压入 `$`。当匹配完所有 `A` 后，看到栈顶为 `$`，就可以转入接受态。这种写法比“栈空接受”更明确，也更不容易在形式化定义中出错。

#### 3. `{a^n b^m c^m}` 的 PDA

这里 `a` 的数量与后面无关，只需要匹配 `b` 和 `c`：

- 先在某个状态中读所有 `a`，不改栈；
- 读 `b` 时每读一个就 push 一个 `B`；
- 切换到读 `c` 的阶段；
- 每读一个 `c` 就 pop 一个 `B`；
- 输入读完且栈中没有未匹配的 `B` 时接受。

这类题的核心是先判断哪两段需要用栈匹配，哪一段可以直接跳过或单独处理。

---

### PDA 例题：完整运行过程

#### PDA 接受 `a^3b^3`

考虑语言：

\[
L = \{a^n b^n \mid n≥0\}.
\]

使用栈底符号 `$` 和栈符号 `A`。设栈左侧表示栈顶。

转移思路：

1. 开始时压入 `$`；
2. 在 `q0` 中读 `a`，每读一个压入一个 `A`；
3. 非确定性切到 `q1`；
4. 在 `q1` 中读 `b`，每读一个弹出一个 `A`；
5. 输入读完且栈顶为 `$` 时接受。

对输入 `aaabbb`，运行过程可以写成：

| 剩余输入 | 状态 | 栈内容 | 说明 |
|---|---|---|---|
| `aaabbb` | `q0` | `$` | 初始化后栈底为 `$` |
| `aabbb` | `q0` | `A$` | 读第一个 `a`，push `A` |
| `abbb` | `q0` | `AA$` | 读第二个 `a` |
| `bbb` | `q0` | `AAA$` | 读第三个 `a` |
| `bbb` | `q1` | `AAA$` | ε 转移，开始匹配 `b` |
| `bb` | `q1` | `AA$` | 读第一个 `b`，pop `A` |
| `b` | `q1` | `A$` | 读第二个 `b` |
| `ε` | `q1` | `$` | 读第三个 `b` |
| `ε` | `q_accept` | `$` | 输入空且只剩栈底，接受 |

如果输入是 `aabbb`，读完两个 `a` 后栈里只有两个 `A`，但有三个 `b` 要匹配，第三个 `b` 时没有 `A` 可弹，因此不会接受。如果输入是 `aaabb`，读完输入后栈里还剩一个 `A`，也不会接受。

这类说明比单纯写三条转移更清楚，尤其当题目要求“explain your PDA”。

---

## Q4. Decidability：有限搜索

### 1. 基本思想

如果一个问题要求“存在某个 `u` 满足条件”，并且题目给了 `|u|` 的上界，那么所有候选 `u` 的数量是有限的。此时可以逐个枚举候选串，再用已有的 decider 检查。

以

\[
L_2 = \{w \mid \exists u,\ |u| ≤ |w|,\ wu ∈ L\}
\]

为例，假设 `L` 是 decidable，且 `M` 是 `L` 的 decider。构造 `L₂` 的 decider `M'`：

```text
M' on input w:
1. Let n = |w|.
2. Enumerate all strings u with |u| ≤ n.
3. For each such u, run M on wu.
4. If M accepts any wu, accept w.
5. If all candidates have been checked and none is accepted, reject w.
```

### 2. 为什么一定停机

长度不超过 `n` 的二进制串只有有限多个，最多为：

\[
1 + 2 + 2^2 + \cdots + 2^n = 2^{n+1}-1.
\]

每次运行 `M` 都会停机，因为 `M` 是 decider。因此 `M'` 只做有限次停机计算，也一定停机。

### 3. 正确性

`M'` 接受 `w` 当且仅当存在某个 `|u| ≤ |w|`，使得 `M` 接受 `wu`。这又等价于存在这样的 `u` 使得 `wu ∈ L`。因此：

\[
M' \text{ accepts } w
\iff
w ∈ L_2.
\]

所以 `L₂` 是 decidable。

### 4. 常见变体

- 如果条件改成 `|u| ≤ 2|w|` 或 `|u| ≤ |w|^2`，仍然是有限搜索，只是枚举范围更大。
- 如果存在量词 `∃` 改成全称量词 `∀`，则检查逻辑改为：只要发现一个候选 `u` 不满足条件就 reject；所有候选都满足才 accept。
- 如果没有长度上界，例如要找任意长度的前缀或后缀，有限搜索方法不能直接使用，因为候选集合可能无限。

---

### Decidability 例题

#### 有限搜索例题

设 `L` 是 decidable。定义：

\[
L' = \{w \mid \exists u, |u|≤2|w|, uwu ∈ L\}.
\]

证明 `L'` decidable。

设 `M` 是 `L` 的 decider。构造 `M'`：

```text
M' on input w:
1. Let n = |w|.
2. Enumerate all strings u with |u| ≤ 2n.
3. For each such u:
       run M on uwu.
       if M accepts, accept.
4. If all candidates have been tested and none is accepted, reject.
```

停机性：长度不超过 `2n` 的字符串有限个。每次调用 `M` 都会停机。因此 `M'` 一定停机。

正确性：

- 如果 `M'` 接受，则某个 `u` 使得 `M` 接受 `uwu`，也就是 `uwu∈L`，所以 `w∈L'`。
- 如果 `w∈L'`，则存在 `|u|≤2|w|` 使得 `uwu∈L`。枚举时一定会枚举到这个 `u`，并且 `M` 会接受 `uwu`，所以 `M'` 接受。

因此 `L'` decidable。

#### 全称量词版本

如果定义改成：

\[
L'' = \{w \mid \forall u, |u|≤|w|, wu∈L\},
\]

构造只需要把接受/拒绝逻辑反过来：

```text
M'' on input w:
1. Enumerate all u with |u| ≤ |w|.
2. For each u, run M on wu.
3. If M rejects any wu, reject.
4. If all candidates are accepted, accept.
```

这里仍然能停机，因为候选 `u` 有有限多个。考试中看到 `∀` 不要紧张，只要量词范围有限，仍然是有限搜索。

#### 不能这样做的情况

如果题目是：

\[
PREF(L)=\{w \mid \exists u, wu∈L\},
\]

这里没有给 `u` 的长度上界。即使 `L` decidable，也不能直接说“枚举所有 `u`，找到就接受，否则拒绝”。因为如果不存在这样的 `u`，枚举永远不会结束，无法保证 reject。

这类题需要另外分析，不能套有限搜索模板。

---

### Undecidability：用规约证明不可判定

#### 1. Mapping reduction

Mapping reduction 记为

\[
A \le_m B.
\]

含义是存在一个可计算函数 `f`，使得对所有输入 `w`：

\[
w ∈ A \iff f(w) ∈ B.
\]

如果 `A` 不可判定，并且 `A ≤ₘ B`，那么 `B` 也不可判定。理由是：若 `B` 有 decider，则可以先计算 `f(w)`，再调用 `B` 的 decider，从而得到 `A` 的 decider，这与 `A` 不可判定矛盾。

常用的不可判定起点：

- \[
  A_{TM} = \{\langle M,w\rangle \mid M \text{ accepts } w\}.
  \]
- \[
  HALT = \{\langle M,w\rangle \mid M \text{ halts on } w\}.
  \]

这两个语言都可以直接引用为不可判定。

#### 2. 规约证明模板

若要证明语言 `L` 不可判定，常见写法如下：

```text
We show A_TM ≤m L.

Given ⟨M, w⟩, construct a machine M' as follows:
M' on input x:
    [describe behavior, usually simulate M on w]

The construction is computable because [brief reason].

Correctness:
⟨M, w⟩ ∈ A_TM
iff M accepts w
iff [behavior of M']
iff ⟨M'⟩ ∈ L.

Since A_TM is undecidable and A_TM ≤m L, L is undecidable.
```

重点是构造 `M'` 时要让 `M'` 的语言性质准确反映 `M` 是否接受 `w`。

#### 3. 常见构造

| 目标语言 | 规约思路 |
|---|---|
| `{⟨M⟩ | L(M) ≠ ∅}` | 给定 `⟨M,w⟩`，构造 `M'`。`M'` 忽略输入 `x`，模拟 `M(w)`；若 `M` 接受 `w`，则 `M'` 接受 `x`。于是 `M` 接受 `w` 当且仅当 `L(M')` 非空。 |
| `{⟨M⟩ | L(M) = Σ*}` | 构造 `M'` 忽略输入并模拟 `M(w)`；若 `M` 接受 `w`，则 `M'` 接受所有输入；否则不接受任何输入。 |
| `{⟨M,w⟩ | M 在 w 上停机}` | 通常从 `A_TM` 或其他已知不可判定语言规约。 |

#### 4. Rice 定理

Rice 定理可以直接引用：

对于任何非平凡的语言性质 `P`，语言

\[
\{\langle M\rangle \mid L(M) \text{ has property } P\}
\]

不可判定。

这里“非平凡”指有些 TM 的语言满足 `P`，有些 TM 的语言不满足 `P`。Rice 定理只适用于语言性质，也就是关于 `L(M)` 的性质；如果性质描述的是机器本身的结构，例如状态数、转移数、代码长度，则不能直接用 Rice 定理。

#### 5. Turing-recognizable

一个语言是 Turing-recognizable，表示存在一台 TM：

- 对属于语言的输入最终接受；
- 对不属于语言的输入可以拒绝，也可以永远不停机。

例如

\[
\{\langle M\rangle \mid L(M) ≠ ∅\}
\]

是 recognizable：可以枚举所有输入，并用 dovetailing 的方式同时模拟 `M` 在这些输入上的运行；只要发现某个输入被接受，就接受。

但它不是 decidable。直观上，若语言为空，就可能永远等不到接受证据。

常用关系：

\[
Decidable = RE \cap coRE.
\]

也就是说，一个语言及其补语言都 recognizable，当且仅当该语言 decidable。

---

### Undecidability 例题

#### 例题：证明 `NONEMPTY_TM = {⟨M⟩ | L(M) ≠ ∅}` 不可判定

从 `A_TM` 规约到 `NONEMPTY_TM`。

给定 `⟨M,w⟩`，构造一台新机器 `M'`：

```text
M' on input x:
1. Ignore x.
2. Simulate M on w.
3. If M accepts w, accept x.
4. If M rejects w, reject x.
5. If M loops on w, loop.
```

这个构造是可计算的，因为我们只是把 `M` 和 `w` 写进 `M'` 的代码里。

现在证明正确性。

如果 `⟨M,w⟩ ∈ A_TM`，也就是 `M` 接受 `w`，那么对任意输入 `x`，`M'` 都会接受。因此 `L(M') = Σ*`，特别地 `L(M') ≠ ∅`。所以 `⟨M'⟩ ∈ NONEMPTY_TM`。

反过来，如果 `⟨M,w⟩ ∉ A_TM`，也就是 `M` 不接受 `w`，那么 `M` 要么拒绝 `w`，要么在 `w` 上循环。此时 `M'` 不会接受任何输入 `x`，所以 `L(M') = ∅`。因此 `⟨M'⟩ ∉ NONEMPTY_TM`。

所以：

\[
⟨M,w⟩∈A_{TM}
\iff
⟨M'⟩∈NONEMPTY_{TM}.
\]

如果 `NONEMPTY_TM` 可判定，那么 `A_TM` 也可判定，矛盾。因此 `NONEMPTY_TM` 不可判定。

#### Rice 定理怎么用才不会误用

可以用 Rice 定理的例子：

- `{⟨M⟩ | L(M) = ∅}`，这是语言是否为空；
- `{⟨M⟩ | L(M) contains 101}`，这是语言是否包含某个串；
- `{⟨M⟩ | L(M) is regular}`，这是语言本身是否 regular。

这些都是关于 `L(M)` 的性质，而且通常是非平凡性质，因此可以用 Rice 定理。

不能直接用 Rice 定理的例子：

- `{⟨M⟩ | M has at most 10 states}`，这是机器描述的结构性质；
- `{⟨M⟩ | M ever moves left on input 0}`，这是机器运行行为的具体性质，不是单纯的语言性质；
- `{⟨M⟩ | M halts on all inputs}`，这不是只由 `L(M)` 决定的性质，因为两台机器可以识别同一个语言，但停机行为不同。

判断方法：如果两台机器 `M1` 和 `M2` 满足 `L(M1)=L(M2)`，它们是否一定同时满足这个性质？如果是，才可能是语言性质。

---

## Q5. Diagonalization

对角化常用于构造一个 decidable 语言，但它不属于某个较弱的语言类，例如 regular language。

### 1. 枚举目标对象

DFA、NFA、CFG 都有有限描述，因此可以把它们按编码长度、再按字典序排列成：

\[
M_1, M_2, M_3, \ldots
\]

可以设存在一台 TM `E`，输入 `k` 时输出第 `k` 个对象的编码 `⟨M_k⟩`。因为这些对象都是有限编码，枚举过程可以机械完成。

### 2. 定义对角语言

定义：

\[
K = \{0^k \mid 0^k \notin L(M_k)\}.
\]

也就是说，对第 `k` 个机器 `M_k`，我们专门看串 `0^k`，并让 `K` 在这个位置上与 `M_k` 的语言取相反结果。

### 3. 证明 `K` 不在目标类中

对任意 `k`，比较 `K` 和 `L(M_k)` 在串 `0^k` 上的结果：

- 如果 `M_k` 接受 `0^k`，则 `0^k ∈ L(M_k)`。根据 `K` 的定义，`0^k ∉ K`，所以 `K ≠ L(M_k)`。
- 如果 `M_k` 不接受 `0^k`，则 `0^k ∉ L(M_k)`。根据 `K` 的定义，`0^k ∈ K`，所以仍然有 `K ≠ L(M_k)`。

因此对每个 `k`，`K` 都不同于 `L(M_k)`。由于所有目标类对象都在这个枚举中出现，`K` 不属于该类。

如果枚举的是所有 DFA，就得到 `K` 不是 regular。若枚举的是所有 CFG，则得到 `K` 不是 context-free。

### 4. 证明 `K` 是 decidable

构造 decider `D`：

```text
D on input w:
1. If w is not of the form 0^k, reject.
2. Let k = |w|.
3. Run E(k) to obtain ⟨M_k⟩.
4. Decide whether M_k accepts 0^k.
5. Flip the answer:
   if M_k accepts 0^k, reject;
   otherwise accept.
```

为什么 `D` 一定停机，取决于枚举对象：

| 枚举对象 | 第 4 步如何判定 |
|---|---|
| DFA | 直接模拟 DFA，读完输入后停机。 |
| NFA | 可以用 subset construction 转成 DFA，再模拟。 |
| CFG | 可以先转成 CNF，再用 CYK 判断。 |

因此 `D` 总会停机。并且：

\[
D \text{ accepts } 0^k
\iff
M_k \text{ does not accept } 0^k
\iff
0^k ∈ K.
\]

所以 `K` 是 decidable。

常见问题是只写“取反”但没有说明 `M_k` 的接受性为什么可判定。这个步骤必须根据 DFA、NFA、CFG 分别说明。

---

### Diagonalization 补充说明

#### 为什么 `K` 不是 regular，但又 decidable

定义：

\[
K = \{0^k \mid 0^k \notin L(D_k)\},
\]

其中 `D_k` 是第 `k` 个 DFA。

这类题容易让人困惑：既然 `K` 是 decidable，为什么它不是 regular？原因是 decidable 比 regular 大得多。TM 可以先根据输入长度找到第 `k` 个 DFA，然后模拟它并取反；DFA 自己没有这种“根据输入长度查表再反对角”的能力。

证明时要分两件事写：

第一，`K` 不等于任何一个 DFA 的语言。对第 `k` 个 DFA，只看输入 `0^k`：

- 如果 `D_k` 接受 `0^k`，那么 `0^k∉K`；
- 如果 `D_k` 不接受 `0^k`，那么 `0^k∈K`。

所以 `K` 至少在这个输入上和 `L(D_k)` 不同。

第二，`K` 是 decidable。输入 `w` 后：

- 如果 `w` 不是全 0，拒绝；
- 如果 `w=0^k`，生成第 `k` 个 DFA `D_k`；
- 模拟 `D_k` 读 `0^k`；
- 若它接受，则拒绝；若它拒绝，则接受。

DFA 模拟一定停机，因此这个 decider 一定停机。

#### 小心下标

有些题会从 `k=1` 开始枚举，而 `ε=0^0` 会导致下标不一致。考试中可以简单避免：定义 `K={0^k | k≥1 and 0^k∉L(M_k)}`，并规定非正长度或非全零输入直接拒绝。只要定义和 decider 对齐即可。

---

## Q6. NP-Complete 证明

证明一个问题 NP-complete 通常分两步：

1. 证明它属于 NP；
2. 证明它是 NP-hard。

### 1. 证明属于 NP

需要给出 certificate 和 polynomial-time verifier。

例如证明 Independent Set 属于 NP：

- Certificate：一个大小为 `k` 的点集 `I ⊆ V`。
- Verifier：检查 `|I| = k`，并检查 `I` 中任意两点之间是否没有边。
- 检查最多涉及 `O(k^2)` 对点，因此是多项式时间。

写这一步时，不需要真的写 nondeterministic machine，只要说明证书是什么、验证什么、验证时间为什么是 polynomial 即可。

### 2. 证明 NP-hard

要从一个已知 NP-hard 或 NP-complete 的问题规约到目标问题：

\[
KnownProblem \le_p TargetProblem.
\]

标准结构如下：

```text
We show KnownProblem ≤p TargetProblem.

Given an instance x of KnownProblem, construct f(x) as follows:
    [describe transformation]

The transformation is polynomial-time because [reason].

Key lemma:
x is a yes-instance of KnownProblem iff f(x) is a yes-instance of TargetProblem.

Proof of lemma:
(→) ...
(←) ...

Therefore TargetProblem is NP-hard.
Together with membership in NP, TargetProblem is NP-complete.
```

注意规约方向不能写反。要证明目标问题难，应当把已知困难的问题转到目标问题。

### 3. 常见规约

#### Independent Set 到 Clique

给定 `⟨G,k⟩`，构造：

\[
f(\langle G,k\rangle) = \langle \bar{G}, k\rangle,
\]

其中 `\bar{G}` 是 `G` 的补图。

关键引理：

\[
I \text{ is an independent set of size } k \text{ in } G
\iff
I \text{ is a clique of size } k \text{ in } \bar{G}.
\]

证明：

- `→`：如果 `I` 在 `G` 中是 independent set，那么 `I` 中任意两点在 `G` 中没有边，所以它们在补图 `\bar{G}` 中都有边。因此 `I` 是 `\bar{G}` 中的 clique。
- `←`：如果 `I` 在 `\bar{G}` 中是 clique，那么 `I` 中任意两点在 `\bar{G}` 中有边，所以它们在 `G` 中没有边。因此 `I` 是 `G` 中的 independent set。

补图可以在多项式时间内构造，因此这是 polynomial-time reduction。

#### Independent Set 到 Vertex Cover

给定 `⟨G,k⟩`，构造：

\[
f(\langle G,k\rangle) = \langle G, |V|-k\rangle.
\]

关键引理：

\[
I \text{ is an independent set of size } k
\iff
V \setminus I \text{ is a vertex cover of size } |V|-k.
\]

证明：

- `→`：若 `I` 是 independent set，则没有一条边的两个端点都在 `I` 中。因此任意边至少有一个端点在 `V \setminus I` 中，所以 `V \setminus I` 是 vertex cover。
- `←`：若 `V \setminus I` 是 vertex cover，假设 `I` 中存在一条边，则该边两个端点都不在 `V \setminus I` 中，这条边就没有被 cover，矛盾。因此 `I` 是 independent set。

这个规约不改图，只把参数 `k` 改为 `|V|-k`。

### 4. 常见问题的 certificate

| 问题 | Certificate | Verifier 检查内容 |
|---|---|---|
| Independent Set | `k` 个点组成的集合 `I` | 任意两点之间没有边。 |
| Clique | `k` 个点组成的集合 `C` | 任意两点之间都有边。 |
| Vertex Cover | `k` 个点组成的集合 `C` | 每条边至少有一个端点在 `C` 中。 |
| 3SAT | 一组变量赋值 | 每个 clause 至少有一个 literal 为 true。 |

### 5. 选择规约的直觉

- 如果题目想把“互不相邻”改成“彼此相邻”，通常考虑补图，即 `IS ↔ CLIQUE`。
- 如果题目涉及“选一些点”和“覆盖所有边”，通常考虑集合补，即 `IS ↔ VC`。
- 常见规约链可以记为：
  \[
  3SAT \le_p CLIQUE \le_p IS \leftrightarrow VC.
  \]

但正式证明时不能只写规约链名称，仍然要写清楚 transformation、polynomial-time 和双向正确性。

---

### NP-complete 例题

#### 完整证明：Vertex Cover 是 NP-complete

假设已知 Independent Set 是 NP-complete。证明 Vertex Cover 是 NP-complete。

Vertex Cover 问题：给定图 `G=(V,E)` 和整数 `k`，问是否存在点集 `C⊆V`，满足 `|C|≤k`，并且每条边至少有一个端点在 `C` 中。

#### Step 1：证明 Vertex Cover 属于 NP

Certificate 是一个点集 `C`。

Verifier 做两件事：

1. 检查 `|C|≤k`；
2. 对每条边 `(u,v)∈E`，检查 `u∈C` 或 `v∈C`。

如果所有边都被覆盖，则接受。这个检查时间最多是多项式，例如 `O(|E|)` 加上集合查询时间。因此 Vertex Cover 属于 NP。

#### Step 2：证明 NP-hard

从 Independent Set 规约到 Vertex Cover。

给定 Independent Set 实例 `⟨G,k⟩`，其中 `G=(V,E)`。构造 Vertex Cover 实例：

\[
f(⟨G,k⟩)=⟨G, |V|-k⟩.
\]

也就是说，图不变，只把参数改成 `|V|-k`。这个转换显然是多项式时间。

下面证明关键引理：

\[
G \text{ has an independent set of size } k
\iff
G \text{ has a vertex cover of size } |V|-k.
\]

`→`：设 `I` 是大小为 `k` 的 independent set。令

\[
C=V\setminus I.
\]

则 `|C|=|V|-k`。要证明 `C` 是 vertex cover。任取一条边 `(u,v)∈E`。由于 `I` 是 independent set，`u` 和 `v` 不可能同时在 `I` 中。因此至少一个端点不在 `I` 中，也就是至少一个端点在 `C` 中。所以每条边都被 `C` 覆盖，`C` 是 vertex cover。

`←`：设 `C` 是大小为 `|V|-k` 的 vertex cover。令

\[
I=V\setminus C.
\]

则 `|I|=k`。要证明 `I` 是 independent set。反设 `I` 中有两个点 `u,v` 之间存在边 `(u,v)∈E`。由于 `u,v∈I`，它们都不在 `C` 中，因此这条边没有任何端点在 `C` 中，和 `C` 是 vertex cover 矛盾。所以 `I` 中任意两点没有边，`I` 是 independent set。

因此 `Independent Set ≤p Vertex Cover`。由于 Independent Set 是 NP-complete，Vertex Cover 是 NP-hard。再结合 Vertex Cover 属于 NP，所以 Vertex Cover 是 NP-complete。

#### 规约方向的判断

要证明目标问题 `B` 是 NP-hard，应写：

\[
A ≤_p B
\]

其中 `A` 是已经知道 NP-hard 的问题。不能写成 `B ≤p A`。后者只说明 `B` 不比 `A` 更难，不能证明 `B` 难。

一句实用判断：把“已知难题”变成“目标题”，才是正确方向。

---

### coNP

#### 1. 定义

\[
coNP = \{L \mid \overline{L} ∈ NP\}.
\]

也就是说，如果某个语言的补语言有多项式长度的 certificate，并且可以在多项式时间内验证，那么这个语言属于 coNP。

#### 2. 例子：VALIDITY

\[
VALIDITY = \{\varphi \mid \varphi \text{ 对所有赋值都为真}\}
\]

是典型的 coNP-complete 问题。

它的补语言是：

\[
\overline{VALIDITY}
=
\{\varphi \mid \varphi \text{ 存在某个赋值使其为假}\}.
\]

这个补语言属于 NP，因为 certificate 可以是一组使公式为假的赋值，verifier 只需代入检查即可。

#### 3. 证明一个语言属于 coNP

一般不直接给原语言的 certificate，而是证明补语言属于 NP：

```text
To show L ∈ coNP, it suffices to show L̄ ∈ NP.
Certificate for L̄: ...
Verifier for L̄: ...
Therefore L̄ ∈ NP, so L ∈ coNP.
```

#### 4. NP 与 coNP 的关系

目前不知道 `NP = coNP` 是否成立。一般认为二者不相等，但这不是已证明结论。

如果某个 NP-complete 问题同时也在 coNP 中，或者某个 coNP-complete 问题同时也在 NP 中，那么会推出 `NP = coNP`。这类结论在证明题中常作为条件推论使用。

---

### coNP 例题

#### 证明 TAUTOLOGY 属于 coNP

定义：

\[
TAUTOLOGY = \{\varphi \mid \varphi \text{ 对所有布尔赋值都为真}\}.
\]

要证明它在 coNP 中，证明它的补语言在 NP 中即可。

补语言是：

\[
\overline{TAUTOLOGY} = \{\varphi \mid \exists \text{ assignment } a, \varphi(a)=false\}.
\]

Certificate 是一个赋值 `a`。Verifier 把 `a` 代入公式 `φ`，检查结果是否为 false。公式求值显然是多项式时间。因此补语言属于 NP，所以 `TAUTOLOGY ∈ coNP`。

考试中不要给 TAUTOLOGY 本身的 certificate 说“所有赋值都为真”，因为“所有赋值”可能指数多个，不是一个多项式长度 certificate。coNP 的标准做法是看补语言。

---

## Q7. PSPACE

PSPACE 题常见形式是分析某类程序、游戏或逻辑公式的判定问题。答案通常需要分成上界和下界：

1. 证明问题属于 PSPACE；
2. 证明问题 PSPACE-hard；
3. 合起来得到 PSPACE-complete。

### 1. 状态空间大小

如果程序只有有限个变量，且每个变量取值范围有限，那么配置总数是有限的。一个配置通常包括：

- 当前执行到的程序位置；
- 所有变量的当前值；
- 必要时还包括输入指针或其他有限控制信息。

常见估计如下。

| 程序类型 | 变量范围 | 状态总数上界 |
|---|---|---|
| Boolean program | `n` 个变量，每个变量取 `0/1` | `|P| · 2^n` |
| Modular program | `k` 个变量，每个变量取 `0,1,...,n-1` | `|P| · n^k` |

如果程序运行步数超过配置总数，就一定有某个配置重复。之后程序会进入循环，不可能第一次停机。因此可以在超过该上界后直接 reject。

### 2. 证明属于 PSPACE

对固定输入 `π`，模拟程序运行，最多模拟 `T` 步，其中 `T` 是配置总数上界。模拟时不需要保存完整历史，只需要保存：

- 当前配置；
- 一个计步器；
- 程序本身。

这些信息都只需要多项式空间。因此固定输入下的停机判断在 PSPACE 中。

如果问题是“是否存在某个输入 `π` 使程序停机”，可以非确定性地猜测 `π`，再执行上述模拟。这给出的是 NPSPACE 算法。根据 Savitch 定理：

\[
NPSPACE = PSPACE.
\]

因此该问题属于 PSPACE。

这里引用 Savitch 定理是必要的；否则只能得到 NPSPACE 上界，而不是 PSPACE 上界。

### 3. 证明 PSPACE-hard：从 TQBF 规约

TQBF 是 PSPACE-complete，可以作为规约起点。TQBF 的形式是带量词的布尔公式，例如：

\[
\exists x_1 \forall x_2 \exists x_3\ \psi(x_1,x_2,x_3).
\]

规约思路是：把公式构造成一个程序，使得程序存在某个输入会停机，当且仅当原 TQBF 为真。

可以把量词理解为：

- 存在量词 `∃`：由输入 `π` 或非确定性选择来提供某个值。
- 全称量词 `∀`：程序必须枚举该变量的所有可能取值，并保证每种取值下都能继续通过检查。
- 最内层公式 `ψ`：如果 `ψ` 为真，程序正常继续；如果 `ψ` 为假，程序进入死循环。

### 4. Boolean 情况下的构造

对

\[
\varphi = \exists x_1 \forall x_2\ \psi(x_1,x_2)
\]

可以构造程序：

```text
// x1 is read from input π

x2 := 0
if not ψ(x1, x2) then
    while true do skip

x2 := 1
if not ψ(x1, x2) then
    while true do skip

halt
```

这个程序存在某个输入 `π` 会停机，当且仅当存在某个 `x₁`，使得对 `x₂ = 0` 和 `x₂ = 1`，公式 `ψ(x₁,x₂)` 都为真。也就是原公式为真。

更长的量词串可以递归展开。存在量词由输入或猜测给值，全称量词则把两个分支都检查一遍。

### 5. Modular 情况下的构造

如果变量取值为 `0,1,...,n-1`，全称量词不能只展开两次，而要用循环枚举所有值。例如：

```text
x_i := 0
counter := 0

while counter != n do
    [run subprogram for the remaining formula]
    x_i := (x_i + 1) mod n
    counter := counter + 1
```

这样可以表达“对所有 `x_i` 的取值，后续条件都成立”。

### 6. 正确性与多项式大小

证明规约正确性时要写：

\[
\exists \pi \text{ such that } P_\varphi(\pi) \text{ halts}
\iff
\varphi \text{ is true}.
\]

同时还要说明构造出的程序 `P_\varphi` 的大小是关于 `|\varphi|` 的多项式。全称量词如果用循环实现，程序不会指数爆炸；布尔变量直接展开两个分支时，也要说明整体展开仍在题目允许的构造范围内，或改用循环/子程序方式避免不必要的指数复制。

常见遗漏：

- 只证明了属于 PSPACE，没有证明 PSPACE-hard。
- 把 `∃` 和 `∀` 的程序含义写反。
- 忘记引用 Savitch 定理。
- 没说明构造是 polynomial-time 的。
- 没说明程序大小相对于公式大小是多项式的。

---

### PSPACE 例题

#### TQBF 到程序停机问题的构造细节

设公式为：

\[
\varphi = \exists x_1\; \forall x_2\; \exists x_3\; \psi(x_1,x_2,x_3).
\]

目标是构造一个程序 `P_φ`，使得：

\[
\exists \pi, P_\varphi(\pi) \text{ halts}
\iff
\varphi \text{ is true}.
\]

一种直观构造如下：

```text
// x1 and x3 are existentially chosen from input π
read x1 from π
read x3_choice_for_x2_0 from π
read x3_choice_for_x2_1 from π

// check the universal branch x2 = 0
x2 := 0
x3 := x3_choice_for_x2_0
if not ψ(x1, x2, x3) then
    while true do skip

// check the universal branch x2 = 1
x2 := 1
x3 := x3_choice_for_x2_1
if not ψ(x1, x2, x3) then
    while true do skip

halt
```

这里有一个重要细节：`∃x3` 在 `∀x2` 后面，所以 `x3` 的选择可以依赖于 `x2` 的值。也就是说，对 `x2=0` 和 `x2=1`，可以使用不同的 `x3`。如果把 `x3` 在最开始只读一次并固定给两个分支共用，就会错误地表达成：

\[
\exists x_1 \exists x_3 \forall x_2\; \psi(x_1,x_2,x_3),
\]

这和原公式不等价。

正确性说明：

- 如果 `φ` 为真，则存在某个 `x1`，使得对每个 `x2`，都存在相应的 `x3` 让 `ψ` 为真。把这些选择写进输入 `π`，程序两个分支都会通过检查并最终停机。
- 如果程序对某个 `π` 停机，则说明 `π` 中给出的 `x1` 使得对 `x2=0` 和 `x2=1` 都能找到对应的 `x3` 让 `ψ` 为真。因此原 TQBF 为真。

#### PSPACE 上界写法要包含空间估计

如果题目问某个有限变量程序是否存在输入使其停机，可以这样写上界：

1. 程序配置由程序计数器、变量值、输入指针等组成；
2. 配置总数至多指数级，但每个配置本身只需多项式空间存储；
3. 非确定性猜输入或猜存在性选择；
4. 模拟程序最多到配置数上界，超过就说明进入循环；
5. 得到 NPSPACE 算法；
6. 由 Savitch 定理 `NPSPACE = PSPACE`，所以问题属于 PSPACE。

不要只说“状态有限，所以 PSPACE”。需要明确：虽然时间可能指数级，但空间只保存当前配置和计数器，因此是多项式空间。