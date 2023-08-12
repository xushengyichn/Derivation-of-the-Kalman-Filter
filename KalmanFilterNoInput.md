# Derivation of the Kalman Filter Formula

## State Space Equation

$$
\begin{aligned}
x_k &= Ax_{k-1} + w_{k-1}\\
z_k &= Hx_k + v_k
\end{aligned}
$$

In the above equations, $x_k$ is the state at time $k$, $x_{k-1}$ is the state at time $k-1$, $u_{k-1}$ is the input at time $k-1$, $z_k$ is the observed value at time $k$, $w_{k-1}$ is process noise, and $v_{k}$ is measurement noise.

$A$ is the State Transition Matrix, which describes how the system state evolves or changes between time steps.

$H$ is the Observation Matrix, which defines how to extract observations or measurements from the system state.

We assume that

$w \sim N(0,Q)$ and $v \sim N(0,R)$, which means $w$ and $v$ are independent random variables that follow a normal distribution.

*We are observing the state space equation where $A$, $H$, $u_{k-1}$, and $z_k$ are known.*

*$x$, $w$, and $v$ are unknown*

## Derivation Process

### The Covariance of the Noise

$$
Q=E[ww^{T}]
$$

Specifically, if $x$ contains two states, i.e.,
$$
x_k=\begin{bmatrix}
x_1 \\
x_2
\end{bmatrix}
$$
then
$$
w_{k-1}=\begin{bmatrix}
w_1 \\
w_2
\end{bmatrix}
$$
The covariance of $w$ is
$$
\begin{align}
Q=E[ww^{T}]&=E\left[ \begin{bmatrix}w_1 \\w_2\end{bmatrix} \begin{bmatrix}w_1 &w_2\end{bmatrix}\right]\\
&=E\left[ \begin{bmatrix} w_1^2 & w_1 w_2 \\ w_2 w_1 & w_2^2\end{bmatrix}\right]\\
&=\begin{bmatrix} E[w_1^2 ]& E[w_1 w_2] \\ E[w_2 w_1] & E[w_2^2]\end{bmatrix}\\
&=\begin{bmatrix} \sigma_{w_1}^2& \sigma_{w_1}\sigma_{w_2} \\ \sigma_{w_2}\sigma_{w_1} & \sigma_{w_2}^2\end{bmatrix}
\end{align}
$$
Similarly, The covariance of $v$ is 
$$
\begin{align}
R=E[vv^{T}]&=E\left[ \begin{bmatrix}v_1 \\v_2\end{bmatrix} \begin{bmatrix}v_1 &v_2\end{bmatrix}\right]\\
&=E\left[ \begin{bmatrix} v_1^2 & v_1 v_2 \\ v_2 v_1 & v_2^2\end{bmatrix}\right]\\
&=\begin{bmatrix} E[v_1^2 ]& E[v_1 v_2] \\ E[v_2 v_1] & E[v_2^2]\end{bmatrix}\\
&=\begin{bmatrix} \sigma_{v_1}^2& \sigma_{v_1}\sigma_{v_2} \\ \sigma_{v_2}\sigma_{v_1} & \sigma_{v_2}^2\end{bmatrix}
\end{align}
$$

### The Prior Distribution

Given that the mean of the noise is 0, it doesn't influence the prediction 

==NB: prior prediction==
$$
\hat{x}_k^-=A\hat{x}_{k-1}
\tag{1}
$$
where $\hat{x}_k^-$ is the prior estimation of $x_k$
$$
z_k=H\hat{x}_{k,measure}
$$
where $\hat{x}_{k,measure}$ is the estimation of $x_k$ obtained from measurement. We can calculate it using
$$
\hat{x}_{k,measure}=H^{-1}z_k
\tag{2} 
$$
It is important to note that we now have two different predictions of $x_k$ — one using system transformation and the other using measurement.

The task now is to determine the optimal estimate of $x_k$

The data fusion technique can be used as follows:
$$
\hat{x}_k=\hat{x}_k^-+G\left(  \hat{x}_{k,measure} -     \hat{x}_k^-     \right) 
\tag{3}
$$
==Note that $G \in [0,1]$. If $G=0$, then $x_k=\hat{x}_k^-$. If $G=1$, then $x_k=\hat{x}_{k,measure}$==

By substituting equation (2) into equation (3), we obtain
$$
\hat{x}_k=\hat{x}_k^-+G\left(  H^{-1}z_k-    \hat{x}_k^-   \right)
\tag{4} 
$$
Next, we let $G=K_kH$ and substitute into equation (4), resulting in

==NB: posterior estimation==


$$
\begin{align}
\hat{x}_k&=\hat{x}_k^-+K_kH\left(  H^{-1}z_k-    \hat{x}_k^-   \right)\\
&=\hat{x}_k^-+K_k\left(  z_k-   H \hat{x}_k^-   \right)   \\
\end{align}
\tag{5}
$$

==Note that $K_k \in [0,H^{-1}]$. If $K_k=0$, then $x_k=\hat{x}_k^-$. If $K_k=H^{-1}$, then $x_k=\hat{x}_{k,measure}=H^{-1}z_k$==

==Objective==: Find a $K_k$ such that $\hat{x}_k \rightarrow x_k$

Define error function $e_k$ such that $e_k \sim N(0,P)$. The error function relates to the difference between the actual state $x_k$ and its estimate $\hat{x}_k$.
$$
e_k = x_k -\hat{x}_k
$$

$$
\begin{align}
P&=E[ee^{T}]\\
&=E[(x_k -\hat{x}_k)(x_k -\hat{x}_k)^{T}]
\end{align}
$$

Find the optimal solution that minimizes the trace of the error covariance $P_k$, which means that we should minimize the trace of P ($\mathrm{tr}(P)=\sigma_{e_1}^2+\sigma_{e_2}^2$)

The derivative process is as follows:
$$
\begin{align}
e_k=x_k -\hat{x}_k&=x_k -\left(\hat{x}_k^-+K_k\left(  z_k-   H \hat{x}_k^-   \right) \right)\\
&=x_k -\hat{x}_k^--K_k  z_k +   K_kH \hat{x}_k^-   \\
&=x_k -\hat{x}_k^--K_k \left(Hx_k + v_k \right) +   K_kH \hat{x}_k^-   \\
&=x_k -\hat{x}_k^--K_k  Hx_k - K_kv_k   +   K_kH \hat{x}_k^-   \\
&=\left(x_k -\hat{x}_k^-\right)-K_k  H\left(x_k -\hat{x}_k^-\right) - K_kv_k     \\
&=\left(I-K_k  H\right)\left(x_k -\hat{x}_k^-\right) - K_kv_k     \\
\end{align}
$$
Given
$$
e_k^- = x_k -\hat{x}_k^-\\
x_k -\hat{x}_k=\left(I-K_k  H\right)e_k^-- K_kv_k     \\
$$

$$
\begin{align}
P_k&=E[e_k e_k^{T}]\\
&=E[(\left(I-K_k  H\right)e_k^-- K_kv_k  )(\left(I-K_k  H\right)e_k^-- K_kv_k  )^{T}]\\
&=E[\left(\left(I-K_k  H\right)e_k^-- K_kv_k \right )\left(e_k^{-T}\left(I-K_k  H\right)^T- v_k^TK_k^T  \right)]\\
&=E\left[     (\left(I-K_k  H\right)e_k^-  e_k^{-T}\left(I-K_k  H\right)^T-      K_kv_k     e_k^{-T}\left(I-K_k  H\right)^T -   \left(I-K_k  H\right)e_k^-   v_k^TK_k^T +   K_kv_k v_k^TK_k^T    \right]\\
&= E\left[  (\left(I-K_k  H\right)e_k^-  e_k^{-T}\left(I-K_k  H\right)^T \right]
-   E\left[   K_kv_k     e_k^{-T}\left(I-K_k  H\right)^T \right]
-  E\left[ \left(I-K_k  H\right)e_k^-   v_k^TK_k^T \right]
+  E\left[ K_kv_k v_k^TK_k^T  \right]
\end{align}
$$

in the equation $ E\left[   K_kv_k     e_k^{-T}\left(I-K_k  H\right)^T \right]$ and $E\left[ \left(I-K_k  H\right)e_k^-   v_k^TK_k^T \right]$,$\left(I-K_k  H\right)$ and $K_k^T$ are constant
$$
\begin{align}
E\left[ \left(I-K_k  H\right)e_k^-   v_k^TK_k^T \right]&=\left(I-K_k  H\right)E\left[ e_k^-   v_k^T\right]K_k^T\\
&=\left(I-K_k  H\right)   E\left[ e_k^-   \right]  E\left[   v_k^T\right]  K_k^T\\
&=0
\end{align}
$$

$$
E\left[ \left(I-K_k  H\right)e_k^-   v_k^TK_k^T \right]=0
$$

$$
\begin{align}
P_k&=E\left[  (\left(I-K_k  H\right)e_k^-  e_k^{-T}\left(I-K_k  H\right)^T \right]+
  E\left[ K_kv_k v_k^TK_k^T  \right]\\
  &=\left(I-K_k  H\right)E\left[e_k^-  e_k^{-T}\right]\left(I-K_k  H\right)^T+K_kE\left[ v_k v_k^T  \right]K_k^T\\
  &=\left(I-K_k  H\right)P_k^-\left(I-K_k  H\right)^T+K_kRK_k^T\\
  &=\left(P_k^--K_k  HP_k^-\right)\left(I-  H^TK_k^T\right)+K_kRK_k^T\\
  &=P_k^--K_k  HP_k^--P_k^-H^TK_k^T+K_k  HP_k^-H^TK_k^T+K_kRK_k^T\\
\end{align}
\tag{6}
$$

in the equation $K_k  HP_k^- $and $ P_k^-H^TK_k^T$,$((P_k^-H^T)K_k^T)^T=K_k(P_k^-H^T)^T=K_kHP_k^{-T}$

Due to $P_k^{-T}=P_k$ (covariance matrix), the trace of $K_k  HP_k^- $and $ P_k^-H^TK_k^T$ is the same
$$
\mathrm{tr}(P_k)=\mathrm{tr}(P_k^-)-2\mathrm{tr}(K_k  HP_k^-)+\mathrm{tr}(K_k  HP_k^-H^TK_k^T)+\mathrm{tr}(K_kRK_k^T)
$$
Then we need to calculate $\frac{d\mathrm{tr}(P_k)}{dK_k}=0$ to find the extreme point

some necessarily theorem
$$
\frac{d\mathrm{tr}(AB)}{dA}=B^T \\
\frac{d\mathrm{tr}(ABA^T)}{dA}=2AB 
$$
==NB: Kalman Gain==
$$
\begin{align}
\frac{d\mathrm{tr}(P_k)}{dK_k}=0-2(HP_k^-)^T+2K_kHP_k^-H^T+2K_kR&=0\\
(HP_k^-)^T-K_kHP_k^-H^T-K_kR&=0\\
P_k^{-T}H^T-K_kHP_k^-H^T-K_kR&=0\\
P_k^{-}H^T-K_kHP_k^-H^T-K_kR&=0\\
P_k^{-}H^T&=K_kHP_k^-H^T+K_kR\\
P_k^{-}H^T&=K_k(HP_k^-H^T+R)\\
K_k&=\frac{P_k^{-}H^T}{HP_k^-H^T+R}\\
\end{align}
$$
Now we have computed Kalman gain, $K_k$, which determines the extent to which the observation is incorporated into the new state estimate. $K_k=\frac{P_k^{-}H^T}{HP_k^-H^T+R}$



Predict the current state $\hat{x}_k^-$ based on the previous state and input, and compute the prior covariance $P_k^-$.
$$
\begin{align}
P_k^-&=E\left[  e_k^-  e_k^{-T} \right]\\
\end{align}
$$

$$
\begin{align}
e_k^-=x_k -\hat{x}_k^-&=
 Ax_{k-1} + w_{k-1}
-\left(A\hat{x}_{k-1} \right)\\
&=A\left(x_{k-1}-\hat{x}_{k-1} \right)+ w_{k-1}\\
&=Ae_{k-1}+ w_{k-1}
\end{align}
$$

$$
\begin{align}
P_k^-&=E\left[  e_k^-  e_k^{-T} \right]\\
&=E\left[  (Ae_{k-1}+ w_{k-1})  (Ae_{k-1}+ w_{k-1})^{T} \right]\\
&=E\left[  (Ae_{k-1}+ w_{k-1})  (e_{k-1}^{T}A^{T}+ w_{k-1}^{T}) \right]\\
&=E\left[  Ae_{k-1}e_{k-1}^{T}A^{T}+ w_{k-1}e_{k-1}^{T}A^{T}  +  Ae_{k-1}w_{k-1}^{T} +w_{k-1} w_{k-1}^{T} \right]\\
&=E\left[  Ae_{k-1}e_{k-1}^{T}A^{T}\right]
+ E\left[w_{k-1}e_{k-1}^{T}A^{T}  \right]
+ E\left[ Ae_{k-1}w_{k-1}^{T} \right]
+E\left[w_{k-1} w_{k-1}^{T} \right]\\
&=E\left[  Ae_{k-1}e_{k-1}^{T}A^{T}\right]
+0
+ 0
+E\left[w_{k-1} w_{k-1}^{T} \right]\\
&=AE\left[  e_{k-1}e_{k-1}^{T}\right]A^{T}
+E\left[w_{k-1} w_{k-1}^{T} \right]\\
&=AP_{k-1}A^{T}
+Q\\
\end{align}
$$



|                  | predict                       |                         | update                                                       |
| ---------------- | ----------------------------- | ----------------------- | ------------------------------------------------------------ |
| Prior            | $\hat{x}_k^-=A\hat{x}_{k-1} $ | Kalman Gain             | $K_k=\frac{P_k^{-}H^T}{HP_k^-H^T+R}$                         |
| Prior covariance | $P_k^-=AP_{k-1}A^{T}+Q$       | Posterior               | $\hat{x}_k=\hat{x}_k^-+K_k\left(  z_k-   H \hat{x}_k^-   \right)   $ |
|                  |                               | Update error covariance | $P_k=(I-K_k  H)P_k^-$                                        |

Adjust the prediction based on the current observation to obtain the new state estimate $\hat{x}_k$ and update the error covariance $P_k$.
$$
\begin{align}
P_k&=P_k^--K_k  HP_k^--P_k^-H^TK_k^T+K_k  HP_k^-H^TK_k^T+K_kRK_k^T\\
&=P_k^--K_k  HP_k^--P_k^-H^TK_k^T+K_k ( HP_k^-H^T+R)K_k^T\\
\end{align}
$$
Then substitute Kalman gain into the above equation
$$
\begin{align}
P_k&=P_k^--K_k  HP_k^--P_k^-H^TK_k^T+K_k ( HP_k^-H^T+R)K_k^T\\
&=P_k^--K_k  HP_k^--P_k^-H^TK_k^T+\frac{P_k^{-}H^T}{HP_k^-H^T+R} ( HP_k^-H^T+R)K_k^T\\
&=P_k^--K_k  HP_k^--P_k^-H^TK_k^T+P_k^{-}H^TK_k^T\\
&=P_k^--K_k  HP_k^-\\
&=(I-K_k  H)P_k^-\\
\end{align}
$$
This process repeats for each time step, aiming to reduce the estimation error over time.





## steady state

When calculating the Kalman Gain, matrix inversion is required. 

Given its time-consuming nature, it's advisable to minimize inversion computations, especially when handling large datasets.



The Kalman Gain plays a pivotal role in the Kalman Filter, determining how much emphasis to place on the estimated state versus new measurements as they arrive. For a stable system, where both the system model (including state transitions and observation models) and its associated noise statistics (i.e., the noise covariances) remain constant, and if the initial estimate of the error covariance is reasonable, the Kalman Gain will converge to a constant value after several iterations.

Here's why this convergence happens:

1. **Error Covariance Update**: Throughout the iterations of the Kalman filter, the estimate of the error covariance matrix is repeatedly updated. It first projects it forward to the next time point based on the prediction step and then adjusts it using the new measurements during the update step.
2. **Stable System and Noise**: For a fixed system model and noise characteristics, the estimate of the error covariance matrix eventually reaches a stable value. This in turn implies that the Kalman Gain, which is a function of this error covariance, will also stabilize.
3. **Equilibrium Point**: When the error covariance stabilizes, it hits an equilibrium between two opposing processes: the evolution of the system, which tends to increase the error covariance, and the incorporation of new observation information, which tends to reduce it. At this equilibrium, the Kalman Gain also stabilizes.

In essence, the convergence of the Kalman Gain to a constant is a result of the interplay between the system model, noise characteristics, and the evolution of error covariance, reaching a balanced state under these conditions.



In Chinese:

卡尔曼增益在卡尔曼滤波器中扮演着关键的角色，它决定了在新的测量值到达时，对当前估计状态与新测量的权重分配。对于一个稳定的系统，如果系统的模型（包括状态转移和观测模型）及其对应的噪声统计性质（即噪声的协方差）保持不变，并且初始化的误差协方差估计是合理的，那么经过若干次迭代后，卡尔曼增益会收敛到一个常数。

这种收敛现象发生的原因有：

1. **误差协方差的更新**：在卡尔曼滤波的迭代过程中，估计误差的协方差矩阵会被反复更新。首先，基于预测步骤将其推进到下一个时间点，然后在更新步骤中利用新的测量值对其进行调整。
2. **稳定的系统和噪声**：对于一个固定的系统模型和噪声特性，估计误差的协方差矩阵最终会趋于一个稳定的值。这也意味着卡尔曼增益，作为这误差协方差的函数，也会稳定下来。
3. **平衡点达成**：当误差协方差稳定时，它找到了两个相反过程之间的平衡点：系统的进化趋向于增加误差协方差，而新的观测信息则趋向于减少它。在这个平衡点上，卡尔曼增益也随之稳定。

简而言之，卡尔曼增益收敛到一个常数是因为系统模型、噪声特性和误差协方差之间的相互作用，在这些条件下达到了一种平衡状态。



Once the Kalman filter converges, we have \( $P_k = P_k^-$ \) and \( $K_k$ \) becomes a constant value. 
To reduce computational overhead and save time, we can employ the Riccati equation to determine \( $P_{ss}$ \) and minimize matrix inversion calculations. 
The detailed derivation is presented below:



### simplified recursive algorithm

From the following three equations
$$
P_k^-=AP_{k-1}A^{T}+Q \\
K_k=\frac{P_k^{-}H^T}{HP_k^-H^T+R} \\
P_k=(I-K_k  H)P_k^-
$$
we remove the time series number $k$
$$
P=APA^{T}+Q \\
K=\frac{PH^T}{HPH^T+R} \\
P=(I-KH)P
$$
substitute the third equation into the first one and the $K$ is replaced by second equation
$$
\begin{align}
P&=A(I-\frac{PH^T}{HPH^T+R}H)PA^{T}+Q \\
P&=APA^{T}-A\frac{PH^T}{HPH^T+R}HPA^{T}+Q\\
APA^{T}-P-A\frac{PH^T}{HPH^T+R}HPA^{T}+Q&=0\\
APA^{T}-P-APH^T(HPH^T+R)^{-1}HPA^{T}+Q&=0\\
\end{align}
$$
idare function in MATLAB can solve the discrete-time algebraic Riccati equations

[Implicit solver for discrete-time algebraic Riccati equations - MATLAB idare - MathWorks Nordic](https://se.mathworks.com/help/control/ref/idare.html)

Syntax:

```matlab
[X,K,L] = idare(A,B,Q,R,S,E)
```

$$
\begin{equation}
\mathbb{A}^T \mathbb{X} \mathbb{A} - \mathbb{E}^T \mathbb{X} \mathbb{E} - \left( \mathbb{A}^T \mathbb{X} \mathbb{B} + \mathbb{S} \right) \left( \mathbb{B}^T \mathbb{X} \mathbb{B} + \mathbb{R} \right)^{-1} \left( \mathbb{A}^T \mathbb{X} \mathbb{B} + \mathbb{S} \right)^T + \mathbb{Q} = 0
\end{equation}
$$

Compare with the definition in matlab and the equation we derived

Here
$$
\begin{align}
\mathbb{A}&=A^T \\
\mathbb{B}&=H^T \\
\mathbb{Q}&=Q \\
\mathbb{R}&=R \\
\mathbb{S}&=0 \\
\mathbb{E}&=I \\
\end{align}
$$

$$
\mathbb{X}=P_{ss}
$$



When matrices `R`, `S` and `E` are omitted or set to `[]`, `idare` uses the following default values:

- `R = I`
- `S = 0`
- `E = I`



After solving the $P_{ss}$, the calculation process of Kalman Filter can be simplified to  
$$
K_{ss}=\frac{P_{ss}H^T}{HP_{ss}H^T+R}
$$

|       | predict                       |           | update                                                       |
| ----- | ----------------------------- | --------- | ------------------------------------------------------------ |
| Prior | $\hat{x}_k^-=A\hat{x}_{k-1} $ | Posterior | $\hat{x}_k=\hat{x}_k^-+K_{ss}\left(  z_k-   H \hat{x}_k^-   \right)   $ |