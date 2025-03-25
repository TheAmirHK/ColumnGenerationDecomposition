# Column Generation Decomposition
Column Generation is a decomposition technique used for solving large-scale linear programming (LP) problems efficiently. It was first introduced by George B. Dantzig and Philip Wolfe in 1960 in their seminal paper on Dantzig-Wolfe decomposition. However, the idea of column generation was formally developed in Gilmore and Gomory’s work (1961, 1963) on the cutting stock problem "A Linear Programming Approach to the Cutting Stock Problem".

This decompositon as like other methods follows genuily intresting logics. It works by iteratively adding variables (columns) to the Restricted Master Problem (RMP), instead of solving the full problem at once.
1. The process follows these key steps:<br>
2. Start with a small set of decision variables (feasible columns).<br>
3. Solve the RMP using Linear Programming (LP).<br>
4. Extract dual values (shadow prices) from the RMP.<br>
5. Solve a Pricing Problem to find new variables (columns) that improve the objective.<br>
6. If an improving column exists, add it to the RMP and repeat.<br>
7. Terminate when no new improving columns are found (optimality is reached).<br>

The main reason behind all this is to reduce computational complexity by avoiding explicit enumeration of all possible variables.<br>

So if it caught your attention and wanna know more, read more:<br>
Dantzig, G. B., & Wolfe, P. (1960): *Decomposition principle for linear programs* – First mention of Dantzig-Wolfe decomposition.<br>
Gilmore, P. C., & Gomory, R. E. (1961, 1963): *A linear programming approach to the cutting-stock problem* – First Column Generation application.<br>
Desrosiers, J., & Lübbecke, M. E. (2005): *A Primer in Column Generation* – Modern overview of Column Generation and Branch-and-Price.<br>

---

## Explaination with an example
I will explain the method with an example. imagine you have a general form of problem as follow:

$$min \quad 10x_1 + 22x_2 + y_1 + 7y_2 + 9y_3$$
$$S.t.$$
$$x_1 + 6x_2 + y_1 - 5y_3 \geq 15$$
$$-2x_2 + 2y_1 + y_2 \leq 8$$

Since column generation is typically applied to problems with a large number of variables, let's assume that:

- $x_1, x_2$ are part of the **master problem** (initial variables).
- $y_1, y_2, y_3$ are generated **dynamically** via the pricing subproblem.

**Step 1: Restricted Master Problem (RMP)**

Start with a subset of variables (e.g., only $x_1, x_2$) and solve:

$$min 10x_1 + 22x_2$$
$$S.t.$$
$$x_1 + 6x_2 \geq 15 \quad  (ignoring \quad y_1, y_3)$$
$$-2x_2 \leq 8 \quad  (ignoring \quad y_1, y_2)$$


This gives an initial solution (likely infeasible for the full problem).

**Step 2: Pricing Subproblem**

Compute **reduced costs** for the missing variables $y_1, y_2, y_3$ to check if adding them improves the solution.

Let $\pi_1, \pi_2$ be the **dual variables** of the constraints in the RMP.

- **Reduced cost for $y_1$**:
$$c_{y_1} - \pi_1(1) - \pi_2(2) = 1 - \pi_1 - 2\pi_2$$

- **Reduced cost for $y_2$**:
$$c_{y_2} - \pi_2(1) = 7 - \pi_2$$

- **Reduced cost for $y_3$**:
$$c_{y_3} - \pi_1(-5) = 9 + 5\pi_1$$

If any **reduced cost is negative**, adding that variable can improve the solution.


**Step 3: Iterate**

1. Solve RMP $\rightarrow$ get duals $\pi_1, \pi_2$.
2. Solve pricing subproblem $\rightarrow$ check if any has a **negative reduced cost**.
3. If yes, add the best $y$ to RMP and repeat.
4. If no, **terminate** (current solution is optimal).
