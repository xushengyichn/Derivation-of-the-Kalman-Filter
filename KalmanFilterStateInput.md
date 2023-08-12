# Derivation of the Kalman Filter Formula

## State Space Equation

$$
\begin{aligned}
x_k &= Ax_{k-1} + Bu_{k-1} + w_{k-1}\\
z_k &= Hx_k + v_k
\end{aligned}
$$

In the above equations, $x_k$ is the state at time $k$, $x_{k-1}$ is the state at time $k-1$, $u_{k-1}$ is the input at time $k-1$, $z_k$ is the observed value at time $k$, $w_{k-1}$ is process noise, and $v_{k}$ is measurement noise.

$A$ is the State Transition Matrix, which describes how the system state evolves or changes between time steps.

$B$ is the Input Matrix, which relates external inputs (such as control signals or disturbances) to changes in the system state.

$H$ is the Observation Matrix, which defines how to extract observations or measurements from the system state.

We assume that

$w \sim N(0,Q)$ and $v \sim N(0,R)$, which means $w$ and $v$ are independent random variables that follow a normal distribution.

*We are observing the state space equation where $A$, $B$, $H$, $u_{k-1}$, and $z_k$ are known.*

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
\hat{x}_k^-=A\hat{x}_{k-1}+Bu_{k-1} 
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
x_k -\hat{x}_k&=x_k -\left(\hat{x}_k^-+K_k\left(  z_k-   H \hat{x}_k^-   \right) \right)\\
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
 Ax_{k-1} + Bu_{k-1} + w_{k-1}
-\left(A\hat{x}_{k-1}+Bu_{k-1} \right)\\
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



|                  | predict                                |                         | update                                                       |
| ---------------- | -------------------------------------- | ----------------------- | ------------------------------------------------------------ |
| Prior            | $\hat{x}_k^-=A\hat{x}_{k-1}+Bu_{k-1} $ | Kalman Gain             | $K_k=\frac{P_k^{-}H^T}{HP_k^-H^T+R}$                         |
| Prior covariance | $P_k^-=AP_{k-1}A^{T}+Q$                | Posterior               | $\hat{x}_k=\hat{x}_k^-+K_k\left(  z_k-   H \hat{x}_k^-   \right)   $ |
|                  |                                        | Update error covariance | $P_k=(I-K_k  H)P_k^-$                                        |

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

It is the same as Kalman Filter with no input, please read KalmanFilterNoInput.md

 

