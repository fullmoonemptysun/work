![[Lect2.pdf]]

1. Essence of NFAs : 0 or many ways to proceed.
2. Acceptance overrules rejection. If any path for a string leads to accept, it is an accept.
3. NFAs do not correspond to a physical machine we can build. It is used mathematically.
4. Number of element in the power set of a set with n elements = 2^n
5. NFA fix closure over concat, because epsilon paths are optional and may not be taken
6. If R is a regular Expr and A = L(R) then A is regular, we can go from regexes to NFAs
7. A regex describes a language.

### **Subset Construction (Powerset Construction) for NFA to DFA**

1. **States**: Q′=P(Q)Q' = P(Q)Q′=P(Q) → DFA states are subsets of NFA states.
2. **Transition Function**: δ′(R,a)={q∣q∈δ(r,a) for some r∈R}\delta'(R, a) = \{ q \mid q \in \delta(r, a) \text{ for some } r \in R \}δ′(R,a)={q∣q∈δ(r,a) for some r∈R} → DFA moves to all states reachable from any r∈Rr \in Rr∈R on input aaa.
3. **Start State**: q0′={q0}q'_0 = \{ q_0 \}q0′={q0} → DFA starts from a set containing NFA’s start state.
4. **Accepting States**: F′={R∈Q′∣R intersects F}F' = \{ R \in Q' \mid R \text{ intersects } F \}F′={R∈Q′∣R intersects F} → DFA accepts if any state in RRR is an accepting NFA state.

### **Key Idea**:

- Each DFA state represents a **set of possible NFA states**.
- The DFA **tracks all possible moves** of the NFA simultaneously.
- Ensures determinism by considering all transitions explicitly.