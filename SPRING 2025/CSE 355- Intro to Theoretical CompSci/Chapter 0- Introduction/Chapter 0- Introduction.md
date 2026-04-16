# 0.1 Automata, Computability and Complexity

_**“What are the fundamental capabilities and limitations of computers?”**_

  

## Complexity Theory

1. Computation problems vary in difficulty

_**What makes some problems computationally hard and others easy?**_

1. Researchers have a created a way to categorize a problem into hard or easy, even if we cannot solve them
2. When faced with a tough problem:
    1. We can identify the root cause and make it easier
    2. opt for an approximate but easierl solution
    3. work with the some problems that are only difficult in the worst case scenario
    4. et cetera

  

1. Cryptography is a field where complexity theory is heavily applied in a reverse way, i.e, instead of making problems easier, cryptographers prefer computationally difficult problems for more secure systems.

  

## Computability Theory

1. Very close to complexity theory. Complexity theory is concerned with methods to classify problems as easy or hard to solve, while computability theory classifies problems as solvable and unsolvable.

  

## Automata Theory

1. Deals with the definition and properties of **Mathematical models of computation**
2. Good place to start because the theories of computability and complexity require a precise  
    _definition of a computer_.

  

# 0.2 Mathematical Notions and Terminology

## Sets

1. Definition of a set
2. Subset: $A \subset B$
3. Subset and equal: $A\subseteq B$
4. Proper subset: if $A \subset B$ and $A \neq B$, denoted by $A\subsetneq B$
5. If we want to take the frequency of occurences in account, we call it a **multiset**
6. Set with one element is a **singleton set.**
7. Set with two elements is an **undordered pair.**
8. Unions, Intersections, Venn diagrams
9. Power set of A: set of all subsets of A
10. Cartesian Product $A\times B$ = set of all ordered pairs where first term is member of A and second term is member of B.

  

## Sequences and Tuples

1. A **sequence** of objects is a list of these objects in some _order_. We usually designate a sequence by writing the list within parentheses. For example, the sequence 7, 21, 57 would be written (7, 21, 57).
2. Finite sequences are called **Tuples.** Sequence with k elements is a _k-tuple._
3. A 2 - tuple is an **ordered pair.**
4. **Cartesian Product of K sets:** Set of all K tuples where the ith term of the tuples is from the ith set.

  

## Functions and Relations

1. Functions: also called a mapping. Input output relationships.
    
    $f(a) = b$ (f maps a to b)
    
2. The notation for a function with domain D and range R is: $f : D \rightarrow R$
3. **Onto:** A function that uses all elements in the range.
4. Functions can be represented in many ways: procedure for computing an output from an input, a table that lists all inputs with their outputs:
    
    |n|f(n)|
    |---|---|
    |0|1|
    |1|2|
    |2|3|
    |3|4|
    |4|0|
    

  

1. When we do modular arithmetic, we define sets like
    
    $Z_m = {0,1,2,....,m-1}$
    
    set of all values that can come from modulo m
    

  

1. Sometimes 2 d table is used if domain of a function comes from the cartesian product of two sets.
2. When the domain of a function is the cartesian product of k different sets (way of saying set of k - tuples), the input to the function is of course K tuples. Each term in the tuples is an **argument.**
3. A function with k arguments is an k-ary function and K is the **arity** of the function.
4. A **Predicate** or a **Property** is function with only {TRUE, FALSE} as its range.
5. A property who’s domain is a set of k-tuples is called a **relation (k-ary relation)**. Example: <, >, =
6. We usually write a binary relation in infix notation
7. Otherwise, b

9. $R(a_1, a_2,...,a_k) =$ TRUE is a k - ary relation.

  

## Graphs

1. Set of points with lines connecting some of the points (undirected). Points are called nodes or vertices.
2. Number of edges at a particular nodes is the **degree** of the node
3. No more than 1 edge between two nodes.
4. **Self - loop**: edge from the node to the same node
5. Edges can be represented by unordered pairs
6. V is set of nodes of G, E is set of edges of G, we say G = (V, E)

![[image 57.png|image 57.png]]

A labeled Graph: Cheapest nonstop fares between cities

  

1. Graph G is a subgraph of Graph H, if Nodes of G are a subset of the nodes of H and the edges of G are the edges of H on the correspondig nodes.

  

1. **Path:** Sequence of nodes connected by edges. Simple path is a path that doesn’t repeat any nodes.
2. A graph is said to **Connected** if every two nodes have a path between them (dont have to be directly connected)

  

1. **Cycle:** Path that starts and ends at the same node
2. **Simple Cycle:** Atleast 3 nodes and only the first and last nodes are repeated
3. **Tree:** A connected graph with no simple cycles.

  

1. **Directed Graph:** has arrows instead of lines and has a sense of direction.
2. **Number of arrows going out = outdegree, number of arrows going in = indegree**
3. This time, edges are represented as ordered pairs (tuples) and not sets.
4. **Directed Path:** A path in which all the arrows point in the same direction.
5. **Strongly Connected Graph**: When a directed path connects every two nodes.
6. If R is a binary relation whose domain is D × D, a labeled graph $G = (D,E)$ represents R, where $E = \{(x, y)| xRy\}$.

  

## Strings and Languages

1. **Alphabet:** set of symbols
2. A string over an alphabet is a finite sequence of symbols from that alphabet written next to each other without commas.
3. Lenth of a string |string| is the number of symbols it contains
4. Empty string is written as $\epsilon$
5. Reverse of a string w = $w^R$
6. Shortlex order: where shorter strings preceed longer strings
7. **Language:** Set of strings

  

## Boolean Logic

1. Conjunction AND, disjuntion OR, Negation, Implication, XOR, ↔, and Boolean combinations, operations, laws

  

# 0.3 Definitions, Theorems and Proofs

1. Definitions: Accurate aritight description of an object or a notion that clearly tells what constitutes that object and what does not.
2. Mathematical Statements: Typically state an object has a certain property
3. Proof: Strict, beyond doubt, logical argument that a mathematical statement is true.
4. Theorem: A proven mathematical statement.
5. Lemmas: A mini theorem that supports a more significant theorem’s proof.
6. Corollary: A related statement to a theorem.

  

## Finding Proofs

General discussion on how to approach proofs.

Some example Proofs

  

# 0.4 Types of Proof

A k-regular graph is a graph where every vertex has exactly k neighbors, meaning each vertex has a degree of k.

## Proof by construction

1. To prove that an object exists, we usually define what the object is made up of and then how to construct it.
2. Example: For each even number n above 2, there exists a 3-regular graph with n nodes.

  

## Proof by contradiction

1. Assume the opposite is true and then prove it false.
2. Example: prove $\sqrt{2}$ is an irrational number.

  

## Proof by induction

1. Basis: Prove that Property(for some i) is true
2. Inductive: Prove that assuming property(i) is true (induc. hypo), property(i+1) is true.