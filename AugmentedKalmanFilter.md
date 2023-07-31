# Derivation of the Augmented Kalman Filter 

## State Space Equation

$$
\begin{aligned}
x_k &= Ax_{k-1} + Bp_{k-1} + w_{k-1}\\
y_k &= Gx_k +Jp_k+ v_k
\end{aligned}
$$

In the above equations, $x_k$ is the state at time $k$, $x_{k-1}$ is the state at time $k-1$, $p_{k-1}$ is the input at time $k-1$, $y_k$ is the observed value at time $k$, $w_{k-1}$ is process noise, and $v_{k}$ is measurement noise.

$A$ is the State Transition Matrix, which describes how the system state evolves or changes between time steps.

$B$ is the Input Matrix, which relates external inputs (such as control signals or disturbances) to changes in the system state.

$G$ is the Observation Matrix, which defines how to extract observations or measurements from the system state.

$J$ is the Transmission Matrix, which directly relates the control input to the output, describing how the control input affects the output independently of the system state.

We assume that

$w \sim N(0,Q)$ and $v \sim N(0,R)$, which means $w$ and $v$ are independent random variables that follow a normal distribution.

==$p$ is unknown and assume $p_{k+1}=p_k+\eta_k$,$E(\eta\eta^T)=S$==

*We are observing the state space equation where $A$, $B$, $G$,$J$, and $y_k$ are known.*

*$x$, $w$, and $v$ are unknown*

## Augmented state equation

The following derivation is based on 

*Lourens, E., et al. "An augmented Kalman filter for force identification in structural dynamics." Mechanical systems and signal processing 27 (2012): 446-460.*

Given
$$
x_k^a=\begin{bmatrix}
x_k \\
p_k
\end{bmatrix}
$$
an augmented state equation is obtained
$$
x_{k}^a=A_ax_{k-1}^a+\xi_{k-1}
$$
The matrix $\mathbf{A}_{\mathrm{a}} \in \mathbb{R}^{\left(n_{\mathrm{s}}+n_{\mathrm{p}}\right) \times\left(n_{\mathrm{s}}+n_{\mathrm{p}}\right)}$, is defined as 
$$
A_a = \begin{bmatrix}
A & B \\
0 & I
\end{bmatrix}
$$
the noise vector $\xi_k \in \mathbb{R}^{\left(n_{\mathrm{s}}+n_{\mathrm{p}}\right)}$
$$
\xi=\begin{bmatrix}
w\\
\eta
\end{bmatrix}
$$
Regarding the measurement equation
$$
y_k=G_ax^a_{k}+v_k
$$
where
$$
G_a=\begin{bmatrix}
G & J
\end{bmatrix}
$$

## Derivation process

After augmenting the force into the state variable, it becomes the normal form of Kalman Filter. 

We directly quote the results from Kalman Filter with no input (File: KalmanFilterNoInput.md).

$$
\begin{aligned}
x_k &= Ax_{k-1} + w_{k-1}\\
z_k &= Hx_k + v_k
\end{aligned}
$$

|                  | predict                      |                         | update                                                       |
| ---------------- | ---------------------------- | ----------------------- | ------------------------------------------------------------ |
| Prior            | $\hat{x}_k^-=A\hat{x}_{k-1}$ | Kalman Gain             | $K_k=\frac{P_k^{-}H^T}{HP_k^-H^T+R}$                         |
| Prior covariance | $P_k^-=AP_{k-1}A^{T}+Q$      | Posterior               | $\hat{x}_k=\hat{x}_k^-+K_k\left(  z_k-   H \hat{x}_k^-   \right)$ |
|                  |                              | Update error covariance | $P_k=(I-K_k  H)P_k^-$                                        |

In our problem, $A= A_a$, $w_{k-1}=\xi_{k-1}$,$H=G_a$,$v_k=v_k$
$$
Q=Q_a=\begin{bmatrix}
Q & 0\\
0 & S
\end{bmatrix}
$$


Therefore, the new calculation table is

|                  | predict                        |                         | update                                                       |
| ---------------- | ------------------------------ | ----------------------- | ------------------------------------------------------------ |
| Prior            | $\hat{x}_k^-=A_a\hat{x}_{k-1}$ | Kalman Gain             | $K_k=\frac{P_k^{-}G_a^T}{G_aP_k^-G_a^T+R}$                   |
| Prior covariance | $P_k^-=A_aP_{k-1}A_a^{T}+Q_a$  | Posterior               | $\hat{x}_k=\hat{x}_k^-+K_k\left(  z_k-  G_a \hat{x}_k^-   \right)$ |
|                  |                                | Update error covariance | $P_k=(I-K_k  G_a)P_k^-$                                      |

