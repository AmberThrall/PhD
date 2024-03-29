---
id: Steinhaus Filtration and Stable Paths in the Mapper
aliases: []
tags:
  - papers
---

**Questions:**
- What is the problem we are trying to solve?
- What does the "birth time" of a simplex $\sigma$ mean?
*Answer:* Smallest time $t$ such that $\sigma$ is created in the filtration.
- How to pick the intervals for Mapper construction?
- How does interleaved filtration relate to stability?

Full paper can be found at: https://arxiv.org/abs/1906.08256.

# Introduction

Two important concepts in TDA are [[Persistent Homology|persistentence]] and the Mapper construction.

It is typically unclear as to how one should interpret higher dimensional homology. One method is via persistence diagrams. 

In most TDA applications we are under the assumption that the data has a metric. However, assigning a meaningful metric is not always clear, e.g., Netflix movie recommendations.

Mapper construction is defined to be the nerve of a cover of data: 

![[SS_2024-03-08_1709943173.png#invert]]

We begin with a set of overlapping intervals covering the possible values of some parameter (shown here as the $y$-axis). Next we cluster the data set such that each cluster falls into an interval. We then take the nerve of the clusters.

The full algorithm is given [[2024-03-19 | here]].

The choice of intervals may drastically affect the resulting complex. For example, consider the two constructions below:

![[SS_2024-03-08_1709944069.png#invert]]

By slightly shrinking an interval, the single point that was in both yellow and cyan regions is now no longer in the yellow region. This results in the yellow-cyan edge in the nerve being removed. From a path perspective, the path from yellow to cyan goes from one edge to six edges.

# Cover Filtrations

We construct a filtration given by a distance on covers.

> [!def] Steinhaus Distance
> Given a measure $\mu$, the **Steinhaus distance** between two sets $A$ and $B$ is 
> $$
>   d_{St}(A,B) = 1 - \frac{\mu(A\cap B)}{\mu(A\cup B)} = \frac{\mu(A\cup B)-\mu(A\cap B)}{\mu(A\cup B)}. 
> $$

The Steinhaus distance is bounded on $[0,1]$. Indeed, since $A\cap B\subseteq A\cup B$, the ratio of measures is between 0 and 1. In a sense, the Steinhaus distance measures "how little" the two sets intersect. When they don't intersect at all, we get a distance of one. When $A=B$, the distance is zero.

Naturally, this can be extended to a collection of sets:
$$
    \mu_{St}(\{U_i\}) = 1 - \frac{\mu(\bigcap U_i)}{\mu(\bigcup U_i)}.
$$

When constructing the nerve of our cover, we keep track of the Steinhaus distance.

> [!def] Steinhaus Nerve
> The **Steinhaus nerve** of a cover $\cal{U}$, denoted $\textup{Nrv}_{St}(\cal{U})$, is the nerve of $\cal{U}$ where each simplex is assigned a weight given by
> $$
>   w_\sigma = d_{St}(\{U_i\in\cal{U}|i\in\sigma\}).
> $$

We claim that $w_\tau\le w_\sigma$ whenever $\tau\preceq\sigma$. Notice that if $\tau\preceq\sigma$ and $\sigma$ is generated from the cover $\{U_i\}_{i\in I}$, then $\tau$ must be generated by the subcover $\{U_i\}_{i\in J}$, $J\subset I$. Thus, when taking the unions and intersections:
$$
    \mu\left(\bigcap_{i\in J}U_i\right) \ge \mu\left(\bigcap_{i\in I}U_i\right)~\text{ and }~
    \mu\left(\bigcup_{i\in J}U_i\right) \le \mu\left(\bigcup_{i\in I}U_i\right).
$$

As a result we get the filtration
$$
    \emptyset = K^{t_0} \subset K^{t_1} \subset K^{t_2} \subseteq \dots
$$
where $K^{t_i}:=\{\sigma\in\textup{Nrv}_{St}(\cal{U})\mid w_\sigma\le t_i\}$ for $t_i\in[0,1]$.

This construction can have upto $2^{|\cal{U}|}-1$ vertices and dimension $|\cal{U}|-1$. Computationally, we need to compute the volume of both intersections an unions of sets. If $C_\text{Unn}$ and $C_\text{Int}$ are the computational cost of computing the volumes of unions and intersections, respectively, then each simplex costs $C_\text{Unn}+C_\text{Int}$. Resulting in a worst-case scenario of $(C_\text{Unn}+C_\text{Int})(2^{|\cal{U}|}-1)$. 

# Stability

We focus on stability in terms of changes in the cover.

> [!def] Bottleneck metric on covers
> Let $\cal{U}$ and $\cal{V}$ be two finite covers of a finite set $X$ such that $|\cal{U}|=|\cal{V}|$. Let $\cal{M}(\cal{U},\cal{V})$ be the set of all possible matchings. Then the **bottleneck distance** between two covers is defined as 
> $$
>   d_B(\cal{U},\cal{V}) = \min_{M\in\cal{M}(\cal{U},\cal{V})}\left\{\max_{(U,V)\in M}\mu(U\Delta V)\right\}
> $$
> where $\Delta$ is the [[Symmetric Difference|symmetric difference]].

One may verify that $d_B$ is a metric on the space of finite covers of a finite set.

The following theorem, shows that for two covers of the same size, the Steinhaus nerve are *$\alpha/m$ interleaved*, i.e.,
$$
    d_{St}(V_I)-\frac{\alpha}{m}\le d_{St}(U_I) \le d_{St}(V_I) + \frac{\alpha}{m}
$$
where $V_I\subset\cal{V}$ and $U_I\subset\cal{U}$.

> [!thm] 
> Suppose that $\cal{U}=\{U_i\}$ and $\cal{V}=\{V_i\}$ are two covers of $X$ with $|\cal{U}|=|\cal{V}|$ and $d_B(\cal{U},\cal{V})\le\alpha$. Given $m=\min\{\min_{U\in\cal{U}}\mu(U),\min_{V\in\cal{V}}\mu(V)\}$, $\textup{Nrv}_{St}(\cal{U})$ and $\textup{Nrv}_{St}(\cal{V})$ are $\alpha/m$ interleaved filtrations.

The idea of the theorem is the following question: what is the largest change in Steinhaus distance possible between collections $U_I$ and $V_J$?

When $|\cal{U}|>|\cal{V}|$, then there is some vertex $v\in\textup{Nrv}_{St}(\cal{U})$ not present in $\textup{Nrv}_{St}(\cal{V})$. As a result they cannot be interleaved.

# Equivalence

Let $\text{\v{C}}_r(X)$ be the cover of $X$ by balls of radius $r$. Then the Cech complex is the nerve of this cover. The Cech filtration is the sequence of complexes given by letting $r\rightarrow\infty$.

**Conjecture**: Given finite $X\subset\R^n$ and $R>\textup{diam}(X)$, the Cech filtration is isomorphic to the Steinhaus filtration constructed from $\text{\v{C}}_R(x)$ under the Lebesgue measure.

The paper starts with proving the 1-dimensional case.
