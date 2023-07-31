# Derivation of the Kalman Filter Formula

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

$w \sim N(0,Q)$ and $v \sim N(0,R)$, which means $w$ and $v$ are random variables that follow a normal distribution.

$E(wv)=S$, which represents the covariance between the random variables $w$ and $v$.

*We are observing the state space equation where $A$, $B$, $G$,$J$, $p_{k-1}$, and $y_k$ are known.*

*$x$, $w$, and $v$ are unknown*

## Derivation Process

### Decorrelation of the Noise

Decorrelation of the noise is a necessary technique to ensure that the state space model meets the required conditions for the application of the Kalman Filter.

Rewrite the measurement equation as 
$$
\begin{aligned}
y_{k-1} &= Gx_{k-1} +Jp_{k-1}+ v_{k-1}\\
y_{k-1}-Gx_{k-1} -Jp_{k-1}- v_{k-1} &= 0
\end{aligned}
$$
Update state transition equation as follows
$$
\begin{aligned}
x_k &= Ax_{k-1} + Bp_{k-1} + w_{k-1}\\
&= Ax_{k-1} + Bp_{k-1} + w_{k-1}+T\left(y_{k-1}-Gx_{k-1} -Jp_{k-1}- v_{k-1}\right)\\
&= (A-TG)x_{k-1}+(B-TJ)p_{k-1}+ (w_{k-1}-Tv_{k-1})+Ty_{k-1}\\
&=A^*x_{k-1}+B^*p_{k-1}+w_{k-1}^*+Ty_{k-1}
\end{aligned}
$$
where
$$
\begin{aligned}
A^*&= A-TG\\
B^* &= B-TJ\\
w_{k-1}^*&=w_{k-1}-Tv_{k-1}\\

\end{aligned}
$$
Compute the updated statistical properties of the noise
$$
\begin{align}
E(w_{k-1}^*)   &=   E(w_{k-1}-Tv_{k-1}) \\
               &=   E(w_{k-1})-TE(v_{k-1})\\
               &=   0-T\times0\\
               &=   0\\
               
E(w_{k-1}^*w_{k-1}^{*T})   &=   E\left[(w_{k-1}-Tv_{k-1})(w_{k-1}-Tv_{k-1})^T\right] \\
                           &=   E\left[(w_{k-1}-Tv_{k-1})(w_{k-1}^T-v_{k-1}^TT^T)\right] \\
                           &=   E\left(w_{k-1}w_{k-1}^T-Tv_{k-1}w_{k-1}^T-w_{k-1}v_{k-1}^TT^T+Tv_{k-1}v_{k-1}^TT^T\right)\\
                           &=   Q-TS^T-ST^T+TRT^T\\
                           
E(w_{k-1}^*v_{k-1}^{T})	&= E\left[(w_{k-1}-Tv_{k-1})v_{k-1}^T\right] \\
						&= E\left(w_{k-1}v_{k-1}^T-Tv_{k-1}v_{k-1}^T\right) \\
						&= S-TR
\end{align}
$$
In order to meet the requirement of Kalman Filter (Random variables should be independent), $E(w_{k-1}^*v_{k-1}^{T})=0$
$$
\begin{align}
E(w_{k-1}^*v_{k-1}^{T})&=0\\
S-TR&=0\\
TR&=S\\
T&=SR^{-1}
\end{align}
$$
Therefore, by setting $T=SR^{-1}$ and utilizing the updated transition equation, the Kalman Filter requirement is fulfilled.
$$
\begin{aligned}
x_k &= Ax_{k-1} + Bp_{k-1} + w_{k-1}\\
&= Ax_{k-1} + Bp_{k-1} + w_{k-1}+T\left(y_{k-1}-Gx_{k-1} -Jp_{k-1}- v_{k-1}\right)\\
&= (A-TG)x_{k-1}+(B-TJ)p_{k-1}+ (w_{k-1}-Tv_{k-1})+Ty_{k-1}\\
&=A^*x_{k-1}+B^*p_{k-1}+w_{k-1}^*+Ty_{k-1}
\end{aligned}
$$





### The Prior Distribution

Given that the mean of the noise is 0, it doesn't influence the prediction 

==NB: prior prediction==
$$
\begin{align}
\hat{x}_k^-&=A^*\hat{x}_{k-1}+B^*p_{k-1}+Ty_{k-1}\\ 
&=(A-TG)\hat{x}_{k-1}+(B-TJ)p_{k-1}+Ty_{k-1}\\ 
&=A\hat{x}_{k-1}+Bp_{k-1}+T\left(y_{k-1}-Jp_{k-1}-G\hat{x}_{k-1}\right)\\ 
&=A\hat{x}_{k-1}+Bp_{k-1}+SR^{-1}\left(y_{k-1}-Jp_{k-1}-G\hat{x}_{k-1}\right)\\ 


\end{align}
\tag{1}
$$
where $\hat{x}_k^-$ is the prior estimation of $x_k$
$$
y_k=G\hat{x}_{k,measure}+Jp_k
$$
where $\hat{x}_{k,measure}$ is the estimation of $x_k$ obtained from measurement. We can calculate it using
$$
\hat{x}_{k,measure}=G^{-1}(y_k-Jp_k)
\tag{2}
$$
It is important to note that we now have two different predictions of $x_k$ — one using system transformation and the other using measurement.

The task now is to determine the optimal estimate of $x_k$

The data fusion technique can be used as follows:
$$
\hat{x}_k=\hat{x}_k^-+H\left(  \hat{x}_{k,measure} -     \hat{x}_k^-     \right) 
\tag{3}
$$
==Note that $H \in [0,1]$. If $H=0$, then $x_k=\hat{x}_k^-$. If $H=1$, then $x_k=\hat{x}_{k,measure}$==

By substituting equation (2) into equation (3), we obtain
$$
\hat{x}_k=\hat{x}_k^-+H\left(  G^{-1}(y_k-Jp_k)-    \hat{x}_k^-   \right)
\tag{4}
$$
Next, we let $H=K_kG$ and substitute into equation (4), resulting in

==NB: posterior estimation==


$$
\begin{align}
\hat{x}_k&=\hat{x}_k^-+K_kG\left(  G^{-1}(y_k-Jp_k)-    \hat{x}_k^-   \right)\\
&=\hat{x}_k^-+K_k\left(  (y_k-Jp_k)-   G \hat{x}_k^-   \right)   \\
&=\hat{x}_k^-+K_k\left(  y_k-Jp_k-   G \hat{x}_k^-   \right)   \\
\end{align}
\tag{5}
$$

==Note that $K_k \in [0,H^{-1}]$. If $K_k=0$, then $x_k=\hat{x}_k^-$. If $K_k=H^{-1}$, then $x_k=\hat{x}_{k,measure}=G^{-1}y_k$==

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
x_k -\hat{x}_k&=x_k -\left(\hat{x}_k^-+K_k\left(  y_k-Jp_k-   G \hat{x}_k^-   \right) \right)\\
&=x_k -\hat{x}_k^--K_k  y_k-K_kJp_k +   K_kG \hat{x}_k^-   \\
&=x_k -\hat{x}_k^--K_k \left(Gx_k + Jp_k+v_k \right) -K_kJp_k+   K_kG \hat{x}_k^-   \\
&=x_k -\hat{x}_k^--K_k  Gx_k - K_kv_k   +   K_kG \hat{x}_k^-   \\
&=\left(x_k -\hat{x}_k^-\right)-K_k  G\left(x_k -\hat{x}_k^-\right) - K_kv_k     \\
&=\left(I-K_k  G\right)\left(x_k -\hat{x}_k^-\right) - K_kv_k     \\
\end{align}
$$
Given
$$
e_k^- = x_k -\hat{x}_k^-\\
x_k -\hat{x}_k=\left(I-K_k  G\right)e_k^-- K_kv_k     \\
$$

$$
\begin{align}
    P_k&=E[e_k e_k^{T}]\\
    &=E[(\left(I-K_k  G\right)e_k^-- K_kv_k  )(\left(I-K_k  G\right)e_k^-- K_kv_k  )^{T}]\\
    &=E[\left(\left(I-K_k  G\right)e_k^-- K_kv_k \right )\left(e_k^{-T}\left(I-K_k  G\right)^T- v_k^TK_k^T  \right)]\\
    &=E\left[     (\left(I-K_k  G\right)e_k^-  e_k^{-T}\left(I-K_k  G\right)^T-      K_kv_k     e_k^{-T}\left(I-K_k  G\right)^T -   \left(I-K_k  G\right)e_k^-   v_k^TK_k^T +   K_kv_k v_k^TK_k^T    \right]\\
    &= E\left[  (\left(I-K_k  G\right)e_k^-  e_k^{-T}\left(I-K_k  G\right)^T \right]
    -   E\left[   K_kv_k     e_k^{-T}\left(I-K_k  G\right)^T \right]
    -  E\left[ \left(I-K_k  G\right)e_k^-   v_k^TK_k^T \right]
    +  E\left[ K_kv_k v_k^TK_k^T  \right]
    \end{align}
$$

in the equation $ E\left[   K_kv_k     e_k^{-T}\left(I-K_k  G\right)^T \right]$ and $E\left[ \left(I-K_k  G\right)e_k^-   v_k^TK_k^T \right]$,$\left(I-K_k  G\right)$ and $K_k^T$ are constant
$$
\begin{align}
    E\left[ \left(I-K_k  G\right)e_k^-   v_k^TK_k^T \right]&=\left(I-K_k  G\right)E\left[ e_k^-   v_k^T\right]K_k^T\\
    &=\left(I-K_k  G\right)   E\left[ e_k^-   \right]  E\left[   v_k^T\right]  K_k^T\\
    &=0
    \end{align}
$$

$$
E\left[ \left(I-K_k  GG\right)e_k^-   v_k^TK_k^T \right]=0
$$

$$
\begin{align}
    P_k&=E\left[  (\left(I-K_k  G\right)e_k^-  e_k^{-T}\left(I-K_k  G\right)^T \right]+
      E\left[ K_kv_k v_k^TK_k^T  \right]\\
      &=\left(I-K_k  G\right)E\left[e_k^-  e_k^{-T}\right]\left(I-K_k  G\right)^T+K_kE\left[ v_k v_k^T  \right]K_k^T\\
      &=\left(I-K_k  G\right)P_k^-\left(I-K_k  G\right)^T+K_kRK_k^T\\
      &=\left(P_k^--K_k  GP_k^-\right)\left(I-  G^TK_k^T\right)+K_kRK_k^T\\
      &=P_k^--K_k  GP_k^--P_k^-G^TK_k^T+K_k  GP_k^-G^TK_k^T+K_kRK_k^T\\
    \end{align}
    \tag{6}
$$

in the equation $K_k  GP_k^- $and $ P_k^-G^TK_k^T$,$((P_k^-G^T)K_k^T)^T=K_k(P_k^-G^T)^T=K_kGP_k^{-T}$

Due to $P_k^{-T}=P_k$ (covariance matrix), the trace of $K_k  GP_k^- $and $ P_k^-G^TK_k^T$ is the same
$$
\mathrm{tr}(P_k)=\mathrm{tr}(P_k^-)-2\mathrm{tr}(K_k  G P_k^-)+\mathrm{tr}(K_k  G P_k^-H^TK_k^T)+\mathrm{tr}(K_kRK_k^T)
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
    \frac{d\mathrm{tr}(P_k)}{dK_k}=0-2(GP_k^-)^T+2K_kGP_k^-G^T+2K_kR&=0\\
    (GP_k^-)^T-K_kGP_k^-G^T-K_kR&=0\\
    P_k^{-T}G^T-K_kGP_k^-G^T-K_kR&=0\\
    P_k^{-}G^T-K_kGP_k^-G^T-K_kR&=0\\
    P_k^{-}G^T&=K_kGP_k^-G^T+K_kR\\
    P_k^{-}G^T&=K_k(GP_k^-G^T+R)\\
    K_k&=\frac{P_k^{-}G^T}{GP_k^-G^T+R}\\
    \end{align}
$$
Now we have computed Kalman gain, $K_k$, which determines the extent to which the observation is incorporated into the new state estimate. $K_k=\frac{P_k^{-}G^T}{GP_k^-G^T+R}$



Predict the current state $\hat{x}_k^-$ based on the previous state and input, and compute the prior covariance $P_k^-$.
$$
\begin{align}
P_k^-&=E\left[  e_k^-  e_k^{-T} \right]\\
\end{align}
$$

$$
\begin{align}
e_k^-=x_k -\hat{x}_k^-&=
A^*x_{k-1}+B^*p_{k-1}+w_{k-1}^*+Ty_{k-1}
-\left(A^*\hat{x}_{k-1}+B^*p_{k-1}+Ty_{k-1}\right)\\
&=A^*\left(x_{k-1}-\hat{x}_{k-1} \right)+ w_{k-1}^*\\
\end{align}
$$

$$
\begin{align}
P_k^-&=E\left[  e_k^-  e_k^{-T} \right]\\
&=E\left[  (A^*e_{k-1}+ w_{k-1}^*)  (A^*e_{k-1}+ w_{k-1}^*)^{T} \right]\\
&=E\left[  (A^*e_{k-1}+ w_{k-1}^*)  (e_{k-1}^{T}A^{*T}+ w_{k-1}^{*T}) \right]\\
&=E\left[  A^*e_{k-1}e_{k-1}^{T}A^{*T}+ w_{k-1}^*e_{k-1}^{T}A^{*T}  +  A^*e_{k-1}w_{k-1}^{*T} +w_{k-1}^* w_{k-1}^{*T} \right]\\
&=E\left[  A^*e_{k-1}e_{k-1}^{T}A^{*T}\right]
+ E\left[w_{k-1}^*e_{k-1}^{T}A^{*T}  \right]
+ E\left[ A^*e_{k-1}w_{k-1}^{*T} \right]
+E\left[w_{k-1}^* w_{k-1}^{*T} \right]\\
&=E\left[  A^*e_{k-1}e_{k-1}^{T}A^{*T}\right]
+0
+ 0
+E\left[w_{k-1}^* w_{k-1}^{*T} \right]\\
&=A^*E\left[  e_{k-1}e_{k-1}^{T}\right]A^{*T}
+E\left[w_{k-1}^* w_{k-1}^{*T} \right]\\
&=A^*P_{k-1}A^{*T}+Q -TS^T-ST^T+TRT^T\\
&=A^*P_{k-1}A^{*T}+Q-SR^{-1}S^T-S(SR^{-1})^T+SR^{-1}R(SR^{-1})^T\\
&=A^*P_{k-1}A^{*T}+Q-SR^{-1}S^T
\end{align}
$$



|                  | predict                                                      |                         | update                                                       |
| ---------------- | ------------------------------------------------------------ | ----------------------- | ------------------------------------------------------------ |
| Prior            | $\hat{x}_k^-=A\hat{x}_{k-1}+Bp_{k-1}+SR^{-1}\left(y_{k-1}-Jp_{k-1}-G\hat{x}_{k-1}\right) $ | Kalman Gain             | $K_k=\frac{P_k^{-}G^T}{GP_k^-G^T+R}$                         |
| Prior covariance | $P_k^-=A^*P_{k-1}A^{*T}+Q-SR^{-1}S^T$                        | Posterior               | $\hat{x}_k=\hat{x}_k^-+K_k\left(  y_k-Jp_k-   G \hat{x}_k^-   \right)   $ |
|                  |                                                              | Update error covariance | $P_k=(I-K_k  G)P_k^-$                                        |

Adjust the prediction based on the current observation to obtain the new state estimate $\hat{x}_k$ and update the error covariance $P_k$.
$$
\begin{align}
    P_k&=P_k^--K_k  GP_k^--P_k^-G^TK_k^T+K_k  GP_k^-G^TK_k^T+K_kRK_k^T\\
    &=P_k^--K_k  GP_k^--P_k^-G^TK_k^T+K_k ( GP_k^-G^T+R)K_k^T\\
    \end{align}
$$
Then substitute Kalman gain into the above equation
$$
\begin{align}
    P_k&=P_k^--K_k  GP_k^--P_k^-G^TK_k^T+K_k ( GP_k^-G^T+R)K_k^T\\
    &=P_k^--K_k  GP_k^--P_k^-G^TK_k^T+\frac{P_k^{-}G^T}{GP_k^-G^T+R} ( GP_k^-G^T+R)K_k^T\\
    &=P_k^--K_k  GP_k^--P_k^-G^TK_k^T+P_k^{-}G^TK_k^T\\
    &=P_k^--K_k  GP_k^-\\
    &=(I-K_k  G)P_k^-\\
    \end{align}
$$
This process repeats for each time step, aiming to reduce the estimation error over time.









