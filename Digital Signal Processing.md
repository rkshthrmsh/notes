# Digital Signal Processing
#dsp

- A *signal* is the description of the evolution of a physical phenomenon.
- Digital signals have two key ideas:
	- Discretization of time: Signal is only sampled at fixed intervals.
		- Samples replace idealized models
		- Simple math replaces calculus
	- Discretization of amplitudes: Signal values are represented using a predetermined set of possible levels.
		- Allows the usage of general-purpose storage
		- Enables the usage of general purpose amplitudes for analysis and synthesis
		- Noise can be controlled to a great extent compared to analog signals
			- Any signal $x$ transmitted across a communication channel undergoes attenuation and noise addition. This is unavoidable and can be represented as $\hat{x(t)} = x(t) / G + \sigma(t)$ for both analog signals, where $1/G$ is the attenuation and $\sigma$ is the noise. 
			- At the receiving end, the signal is reconstructed by amplifying it with a gain factor of $G$. For analog signals, this implies that even the noise gets amplified leading to signal degradation. On the other hand, digital signals can be thresholded after amplification to regenerate the exact signal that was transmitted. This difference becomes even more stark over long communication channels, where repeaters need to be used. The repeaters consistently degrade the analog signals.

## The Sampling Theorem
The Sampling Theorem serves as a bridge between the analog and digital models.
$$x(t) = \sum_{n=-\infty}^{\infty}x[n]sinc(\frac{t-nT_s}{T_s})$$
Where, $x(t)$ is the analog signal and $x[n]$ is the digitally sampled counterpart.

## Discrete-Time Signals
Discrete time signals are a sequence of complex numbers that are denoted as $x[n]$. Formally, they are a mapping $x: \mathbb{Z} -> \mathbb{R}$.

### Basic Discrete-Time Signals
- Delta: $x[n] = \delta[n]$
- Unit step: $x[n] = u[n]$
- Exponential decay: $x[n] = |a|^n u[n]$ for $n < 0$.
- Sinusoid: $x[n] = sin(\omega_0n + \theta$)

### Signal Classes
- Finite-length signals: $x[n], n = 0, 1, ..., N - 1$
- Infinite-length signals: $x[n], n \in \mathbb{Z}$
- Periodic signals: $\tilde{x}[n] = \tilde{x}[n + kN], n, k, N \in \mathbb{Z}$
	- same information as finite-length of length N
	- act as a bridge between finite and infinite lengths
- Finite-support sequence: 
$$ \bar{x}[n] = \left\{ 
  \begin{array}{ c l }
    x[n] & \quad \textrm{if } 0 \leq n < 1 \\
    0    & \quad \textrm{otherwise}
  \end{array}
\right.$$
	- same information as finite-length of length N
	- act as a bridge between finite and infinite lengths

### Elementary operators
- Scaling: $y[n] = \alpha x[n]$
- Sum: $z[n] = y[n]+x[n]$
- Product: $z[n] = y[n] \cdot x[n]$
- Shift by k (delay): $y[n] = x[n - k]$
	- Shifting of finite-length signal can be cast either to a finite-support or a periodic signal, which is the natural way of interpreting shift

### Energy and power
- Energy:
$$ E_x = \sum_{n=-\infty}^{\infty} |x[n]|^2$$
	- Periodic sequences have infinite energy
- Power
$$P_x = \lim_{N->\infty}\frac{1}{2N + 1}\sum_{n=-\infty}^{\infty} |x[n]|^2$$