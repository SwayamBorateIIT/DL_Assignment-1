# Q5 — Lamp Placement in a Dark Room
## Theory, Mathematics, Gradients, Optimization, and Quiz Guide

---

# 1. Problem Statement

We have a unit-square room:

$$
[0,1]^2
$$

We want to place $N$ lamps in the room.

For this assignment, the main case is:

$$
N=3
$$

Each lamp has a 2D position:

$$
l_i=(x_i,y_i)
$$

where

$$
0\le x_i\le1,\qquad0\le y_i\le1
$$

The goal is to place the lamps so that:

1. The whole room is bright.
2. The lamps do not collapse onto the same point.

---

# 2. Lamp Brightness

A lamp at position $l$ contributes brightness at room point $p$:

$$
B_l(p)
=
\exp
\left(
-\frac{\|p-l\|^2}{2\sigma^2}
\right)
$$

where:

- $p=(p_x,p_y)$ is a point in the room.
- $l=(l_x,l_y)$ is the lamp position.
- $\sigma$ controls the spread of the lamp.

---

# 3. Squared Euclidean Distance

For two 2D points:

$$
p=(p_x,p_y)
$$

and

$$
l=(l_x,l_y)
$$

the squared distance is:

$$
\|p-l\|^2
=
(p_x-l_x)^2+(p_y-l_y)^2
$$

There is NO square root.

For example:

$$
p=(0.5,0.5)
$$

and

$$
l=(0.3,0.3)
$$

Then:

$$
\|p-l\|^2
=
(0.5-0.3)^2+(0.5-0.3)^2
$$

$$
=0.2^2+0.2^2
$$

$$
=0.04+0.04
$$

$$
=0.08
$$

---

# 4. Why Use a Gaussian?

The brightness function is:

$$
B_l(p)
=
e^{-\|p-l\|^2/(2\sigma^2)}
$$

At the lamp itself:

$$
p=l
$$

so:

$$
\|p-l\|^2=0
$$

and therefore:

$$
B_l(l)=e^0=1
$$

So the lamp is brightest at its own position.

As the distance increases:

$$
\|p-l\|^2 \uparrow
$$

the exponent becomes more negative:

$$
-\frac{\|p-l\|^2}{2\sigma^2}\downarrow
$$

and therefore brightness decreases.

---

# 5. Effect of Sigma

The parameter $\sigma$ controls how far the lamp's light spreads.

The brightness is:

$$
B_l(p)
=
e^{-\|p-l\|^2/(2\sigma^2)}
$$

## Large sigma

If $\sigma$ is large:

$$
2\sigma^2
$$

is large, so the exponent is less negative.

Therefore the lamp has a wider spread.

### Large sigma:

- Light spreads far.
- Room is easier to illuminate.
- Lamp locations matter less.
- Brightness becomes more uniform.

---

## Small sigma

If $\sigma$ is small:

$$
2\sigma^2
$$

is small.

The exponent becomes very negative even for moderate distances.

Therefore:

- Light is concentrated near the lamp.
- Large dark regions can appear.
- Lamp placement becomes more important.

---

# 6. Brightness from Multiple Lamps

If there are $N$ lamps:

$$
l_1,l_2,\ldots,l_N
$$

then total brightness at point $p$ is:

$$
B(p)
=
\sum_{k=1}^{N}B_{l_k}(p)
$$

Therefore:

$$
B(p)
=
\sum_{k=1}^{N}
\exp
\left(
-\frac{\|p-l_k\|^2}{2\sigma^2}
\right)
$$

The contributions ADD together.

---

# 7. Room Grid

We cannot calculate brightness at every continuous point of the room.

Instead, we create a grid:

$$
G=\{p_1,p_2,\ldots,p_M\}
$$

For example, a $100\times100$ grid gives:

$$
M=10,000
$$

sample points.

The average room brightness is:

$$
\text{Average Brightness}
=
\frac{1}{|G|}
\sum_{p\in G}B(p)
$$

or:

$$
\text{Average Brightness}
=
\frac{1}{|G|}
\sum_{p\in G}
\sum_{k=1}^{N}
B_{l_k}(p)
$$

This is our main brightness term.

---

# 8. Why Do We Need Repulsion?

If we only maximize brightness, the optimizer may put multiple lamps in similar locations.

We therefore add a repulsion penalty.

For every pair of lamps:

$$
(l_i,l_j)
$$

we calculate:

$$
\exp
\left(
-\frac{\|l_i-l_j\|^2}{2\rho^2}
\right)
$$

The complete repulsion term is:

$$
R(l_1,\ldots,l_N)
=
\sum_{i<j}
\exp
\left(
-\frac{\|l_i-l_j\|^2}{2\rho^2}
\right)
$$

---

# 9. Understanding Repulsion

Suppose two lamps are very close.

Then:

$$
\|l_i-l_j\|^2\approx0
$$

Therefore:

$$
e^0=1
$$

So the penalty is large.

If the lamps are far apart:

$$
\|l_i-l_j\|^2\gg0
$$

then:

$$
e^{-\text{large number}}\approx0
$$

So there is almost no penalty.

---

# 10. Number of Pairwise Repulsion Terms

For $N$ lamps, the number of unique pairs is:

$$
\binom{N}{2}
=
\frac{N(N-1)}{2}
$$

For 3 lamps:

$$
\frac{3(2)}{2}=3
$$

The pairs are:

$$
(l_1,l_2)
$$

$$
(l_1,l_3)
$$

$$
(l_2,l_3)
$$

Therefore:

$$
R
=
R_{12}+R_{13}+R_{23}
$$

---

# 11. Effect of Rho

The repulsion function is:

$$
R_{ij}
=
e^{-d^2/(2\rho^2)}
$$

where:

$$
d=\|l_i-l_j\|
$$

## Large rho

Repulsion acts over a larger distance.

The lamps strongly prefer to be farther apart.

---

## Small rho

Repulsion is only significant when lamps are very close.

Lamps can be moderately close without much penalty.

---

# 12. Full Objective

The objective is:

$$
J
=
\text{Average Brightness}
-
\lambda R
$$

Therefore:

$$
J(l_1,\ldots,l_N)
=
\frac{1}{|G|}
\sum_{p\in G}
\sum_{k=1}^{N}
\exp
\left(
-\frac{\|p-l_k\|^2}{2\sigma^2}
\right)
-
\lambda
\sum_{i<j}
\exp
\left(
-\frac{\|l_i-l_j\|^2}{2\rho^2}
\right)
$$

We want:

$$
\boxed{\max J}
$$

---

# 13. Meaning of Lambda

$$
\lambda
$$

controls how strongly we care about repulsion.

The objective is:

$$
J=B-\lambda R
$$

where:

- $B$ = average brightness
- $R$ = repulsion
- $\lambda$ = repulsion weight

---

## Lambda = 0

$$
J=B
$$

There is no repulsion.

The optimizer only cares about brightness.

---

## Large Lambda

Repulsion becomes very important.

The optimizer strongly prefers lamps to spread out.

---

## Small Lambda

Brightness dominates.

The optimizer may allow lamps to be closer together if doing so improves brightness.

---

# 14. Recommended Parameters

The assignment suggests:

$$
\sigma=0.25
$$

$$
\rho=0.20
$$

$$
\lambda=1.0
$$

These provide a good balance between room coverage and lamp separation.

---

# 15. Optimization

We want:

$$
\max J
$$

But PyTorch optimizers such as Adam are designed to MINIMIZE a loss.

Therefore define:

$$
L=-J
$$

and minimize:

$$
\boxed{L=-J}
$$

This is one of the most important points in the assignment.

---

# 16. Why Minimizing -J Maximizes J

Suppose:

$$
J=5
$$

then:

$$
-J=-5
$$

Suppose another solution has:

$$
J=10
$$

then:

$$
-J=-10
$$

A minimizer prefers:

$$
-10<-5
$$

Therefore minimizing $-J$ is equivalent to maximizing $J$.

---

# 17. Gradient Descent

Suppose the parameters are represented by:

$$
\theta
$$

For this problem:

$$
\theta
=
\begin{bmatrix}
x_1 & y_1 & x_2 & y_2 & x_3 & y_3
\end{bmatrix}
$$

The gradient is:

$$
\nabla_\theta L
$$

Gradient descent updates:

$$
\theta_{t+1}
=
\theta_t
-
\eta\nabla_\theta L
$$

where:

- $\eta$ = learning rate
- $\nabla_\theta L$ = gradient of the loss

Because:

$$
L=-J
$$

we have:

$$
\nabla L=-\nabla J
$$

Therefore:

$$
\theta_{t+1}
=
\theta_t+\eta\nabla J
$$

So the parameters move in the direction that increases $J$.

---

# 18. What Does the Gradient Mean?

The gradient tells us:

> If I move a lamp slightly in the x or y direction, how does the objective change?

For lamp $l_i=(x_i,y_i)$:

$$
\nabla_{l_i}J
=
\begin{bmatrix}
\frac{\partial J}{\partial x_i}\\
\frac{\partial J}{\partial y_i}
\end{bmatrix}
$$

The two components tell us:

$$
\frac{\partial J}{\partial x_i}
$$

how the objective changes if the lamp moves in x.

And:

$$
\frac{\partial J}{\partial y_i}
$$

how the objective changes if the lamp moves in y.

---

# 19. Derivative of the Brightness Term

Consider one lamp:

$$
B_l(p)
=
\exp
\left(
-\frac{\|p-l\|^2}{2\sigma^2}
\right)
$$

Let:

$$
d^2=\|p-l\|^2
$$

Then:

$$
B=e^{-d^2/(2\sigma^2)}
$$

We want:

$$
\nabla_l B
$$

First:

$$
\nabla_l\|p-l\|^2
=
2(l-p)
$$

Using the chain rule:

$$
\nabla_l B
=
B
\left(
-\frac{1}{2\sigma^2}
\right)
2(l-p)
$$

Therefore:

$$
\boxed{
\nabla_l B
=
\frac{p-l}{\sigma^2}B
}
$$

or:

$$
\boxed{
\nabla_l B
=
\frac{p-l}{\sigma^2}
\exp
\left(
-\frac{\|p-l\|^2}{2\sigma^2}
\right)
}
$$

---

# 20. Interpretation of the Brightness Gradient

The gradient:

$$
\nabla_l B
=
\frac{p-l}{\sigma^2}B
$$

points from the lamp toward the room point $p$.

Therefore:

> A lamp is encouraged to move toward regions where it can increase brightness.

The strength is also multiplied by:

$$
B
$$

So nearby points have a stronger influence than very distant points.

---

# 21. Gradient of Average Brightness

The average brightness is:

$$
A
=
\frac{1}{|G|}
\sum_{p\in G}
\sum_{k=1}^{N}
B_{l_k}(p)
$$

For lamp $l_i$:

$$
\nabla_{l_i}A
=
\frac{1}{|G|}
\sum_{p\in G}
\nabla_{l_i}B_{l_i}(p)
$$

Therefore:

$$
\boxed{
\nabla_{l_i}A
=
\frac{1}{|G|}
\sum_{p\in G}
\frac{p-l_i}{\sigma^2}
B_{l_i}(p)
}
$$

This is the brightness force acting on lamp $i$.

---

# 22. Gradient of Repulsion

For a pair of lamps:

$$
R_{ij}
=
\exp
\left(
-\frac{\|l_i-l_j\|^2}{2\rho^2}
\right)
$$

The gradient with respect to $l_i$ is:

$$
\boxed{
\nabla_{l_i}R_{ij}
=
-\frac{l_i-l_j}{\rho^2}
R_{ij}
}
$$

or:

$$
\nabla_{l_i}R_{ij}
=
\frac{l_j-l_i}{\rho^2}
R_{ij}
$$

---

# 23. Why Does Repulsion Push Lamps Apart?

Remember:

$$
J=A-\lambda R
$$

Therefore:

$$
\nabla_{l_i}J
=
\nabla_{l_i}A
-
\lambda\nabla_{l_i}R
$$

For the repulsion term:

$$
\nabla_{l_i}R_{ij}
=
-\frac{l_i-l_j}{\rho^2}R_{ij}
$$

Therefore:

$$
-\lambda\nabla_{l_i}R_{ij}
=
\lambda
\frac{l_i-l_j}{\rho^2}
R_{ij}
$$

This vector points from:

$$
l_j \rightarrow l_i
$$

So it pushes lamp $i$ away from lamp $j$.

This is why the repulsion term prevents collapse.

---

# 24. Total Gradient

For lamp $l_i$:

$$
\boxed{
\nabla_{l_i}J
=
\nabla_{l_i}A
-
\lambda
\sum_{j\ne i}
\nabla_{l_i}R_{ij}
}
$$

The lamp therefore experiences two competing effects:

### Brightness force

Move toward positions that improve room illumination.

### Repulsion force

Move away from other lamps.

The final position is a compromise between these forces.

---

# 25. Example of the Forces

Imagine two lamps are very close:

$$
l_1\approx l_2
$$

Then:

$$
\|l_1-l_2\|^2\approx0
$$

and:

$$
R_{12}\approx1
$$

Therefore repulsion is strong.

The gradient pushes the lamps apart.

If:

$$
\|l_1-l_2\|
$$

becomes large, then:

$$
R_{12}\approx0
$$

and the repulsion force becomes very small.

---

# 26. Adam Optimizer

The assignment can use SGD, Momentum, Adam, etc.

Your code uses:

$$
\boxed{\text{Adam}}
$$

Adam is an adaptive gradient-based optimizer.

Instead of simply using:

$$
\theta_{t+1}
=
\theta_t-\eta g_t
$$

Adam keeps running estimates of:

1. The first moment of the gradient.
2. The second moment of the gradient.

Let:

$$
g_t=\nabla_\theta L_t
$$

---

# 27. Adam First Moment

Adam computes:

$$
m_t
=
\beta_1m_{t-1}
+
(1-\beta_1)g_t
$$

This is approximately a moving average of the gradient.

It provides momentum.

---

# 28. Adam Second Moment

Adam computes:

$$
v_t
=
\beta_2v_{t-1}
+
(1-\beta_2)g_t^2
$$

This tracks the magnitude of recent gradients.

---

# 29. Bias Correction

Because $m_0$ and $v_0$ start at zero, Adam performs bias correction:

$$
\hat m_t
=
\frac{m_t}{1-\beta_1^t}
$$

and:

$$
\hat v_t
=
\frac{v_t}{1-\beta_2^t}
$$

---

# 30. Adam Update

The final update is approximately:

$$
\boxed{
\theta_{t+1}
=
\theta_t
-
\eta
\frac{\hat m_t}
{\sqrt{\hat v_t}+\epsilon}
}
$$

In our problem:

$$
\theta
=
[l_1,l_2,l_3]
$$

so Adam updates all six coordinates.

---

# 31. Why Adam Is Useful Here

The objective is nonlinear and non-convex.

Different coordinates can have gradients of different magnitudes.

Adam:

- Uses momentum.
- Adapts the learning rate for each parameter.
- Usually converges faster than plain SGD.
- Works well for this type of differentiable optimization.

---

# 32. PyTorch Implementation

We create lamp positions:

```python
lamps = torch.rand(
    num_lamps,
    2,
    device=DEVICE
)

lamps.requires_grad_(True)
```
