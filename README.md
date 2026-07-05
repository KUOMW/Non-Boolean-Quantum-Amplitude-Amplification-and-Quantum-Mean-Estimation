# Non-Boolean Quantum Amplitude Amplification and Quantum Mean Estimation with Qiskit

This project implements the **Non-Boolean Quantum Amplitude Amplification (NBQAA)** algorithm and **Quantum Mean Estimation (QME)** using **Qiskit**, following the paper:

> *Non-Boolean Quantum Amplitude Amplification and Quantum Mean Estimation*
> https://arxiv.org/pdf/2102.04975

The notebook demonstrates how to:

* Construct a **non-Boolean oracle** using phase rotations.
* Build the **selective phase-flip operator**.
* Implement the **amplitude amplification iteration operator**.
* Visualize the evolution of probability distributions after multiple amplification iterations.
* Apply **Quantum Phase Estimation (QPE)** to estimate the eigenphase of the amplification operator.
* Recover the mean value encoded by the oracle.

---

# Background

Classical amplitude amplification is designed for **Boolean functions**, where the oracle simply marks "good" and "bad" states.

In contrast, this work considers **non-Boolean functions**, where the oracle encodes function values as **quantum phases**.

Instead of estimating the fraction of marked states, the algorithm estimates the mean value

[
\mu=\frac1N\sum_x f(x),
]

using quantum phase estimation on a specially constructed amplitude amplification operator.

---

# Oracle Construction

For an 8-qubit data register,

```python
n = 8
```

the oracle applies a phase rotation to each qubit

[
U_f
===

\prod_{k=0}^{n-1}
P!\left(
\frac{\pi}{4}
\cdot
\frac{2^k}{255}
\right),
]

where `P(θ)` is the single-qubit phase gate.

In the implementation,

```python
theta = (np.pi/4) * (2**k) / 255
qc.p(theta, q_reg[k])
```

This oracle encodes the function values into the global quantum phase.

---

# Selective Phase-Flip Operator

The circuit constructs

[
S_{\Psi_0},
]

which performs a reflection about the initial state.

It is implemented by

* Hadamard transforms,
* multi-controlled X gates,
* global phase adjustment,
* inverse basis transformations.

This operator plays the same role as Grover's diffusion operator in standard amplitude amplification.

---

# Conditional Oracle

The project constructs a controlled version of the oracle,

[
CU_f,
]

which conditionally applies

* (U_f)
* (U_f^\dagger)

depending on the ancilla state.

This is required for constructing the amplification operator.

---

# Amplitude Amplification Operator

The amplification operator is

[
Q_{\text{iter}}
===============

CU_f
S_{\Psi_0}.
]

Starting from

[
|\Psi_0\rangle,
]

the notebook applies

```python
for k in range(j):
    qc.append(Q_iter, ...)
```

for different iteration counts and visualizes the resulting probability distributions.

The plots illustrate how the quantum state evolves under repeated amplitude amplification.

---

# Quantum Phase Estimation

The notebook then performs Quantum Phase Estimation (QPE).

The initial state satisfies

[
|\Psi_0\rangle
==============

\frac{|\eta_+\rangle+|\eta_-\rangle}{\sqrt2},
]

where

[
Q_{\text{iter}}
|\eta_\pm\rangle
================

e^{\pm i\theta}
|\eta_\pm\rangle.
]

Applying QPE estimates the eigenphase

[
\hat{\theta},
]

from which the mean value is recovered.

The implementation includes

* QPE register initialization
* controlled powers of the amplification operator
* inverse Quantum Fourier Transform
* phase probability distribution visualization

---

# Mean Estimation

After estimating

[
\hat{\theta},
]

the mean is computed as

```python
np.cos(theta_hat)
```

which estimates

[
\frac1{256}
\sum_{k=0}^{255}
\cos\left(
\frac{\pi}{2}
\cdot
\frac{k}{255}
\right).
]

---

# Project Structure

```
.
├── NBQAA.ipynb
├── README.md
└── figures/
```

The notebook includes

1. Oracle construction
2. Selective phase-flip operator
3. Conditional oracle
4. Amplitude amplification
5. Probability distribution visualization
6. Quantum Phase Estimation
7. Mean estimation

---

# Requirements

* Python 3.10+
* Qiskit
* NumPy
* Matplotlib

Install dependencies with

```bash
pip install qiskit numpy matplotlib
```

---

# Example Output

The notebook produces

* Circuit diagrams for each operator
* Probability distributions after multiple amplification iterations
* QPE output distribution
* Estimated phase
* Estimated mean value

---

# Reference

Yuan Su, Dong An, Nathan Wiebe

**Non-Boolean Quantum Amplitude Amplification and Quantum Mean Estimation**

https://arxiv.org/pdf/2102.04975

---

# Acknowledgements

This implementation is an educational reproduction of the algorithm presented in the original paper using the Qiskit framework. The goal is to provide a clear and readable example of how non-Boolean amplitude amplification and quantum mean estimation can be implemented and visualized in practice.
