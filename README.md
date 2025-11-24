# ⚛️ Quantum Cryptanalysis: Shor's Algorithm Breaks RSA

This repository demonstrates a full quantum-classical cryptanalytic attack against the **RSA Public-Key Cryptosystem** using an implementation of **Shor's Algorithm** built with the **Qiskit** framework for the IBM Quantum service. The project successfully factors the RSA modulus $N=15$ and decrypts a ciphertext.

-----

## 💡 Introduction

The security of modern public-key cryptography, such as **RSA**, fundamentally relies on the difficulty of the **Integer Factorization Problem (IFP)** for classical computers. However, in 1994, Peter Shor proposed a quantum algorithm that can solve the IFP in **polynomial time**, an exponential speedup over the best-known classical methods.

This project implements the core components of Shor's algorithm to showcase how a sufficiently powerful quantum computer could render current RSA encryption obsolete.

-----

## ⭐ Key Features

  * **Hybrid Quantum-Classical Architecture:** Combines classical number theory pre- and post-processing with a quantum circuit at the core.
  * **Quantum Order Finding:** Implements the Quantum Phase Estimation (QPE) protocol for the modular exponentiation function, which provides the exponential speedup.
  * **Qiskit Modular Exponentiation:** Utilizes complex, multi-qubit **Controlled-U gates** to realize $U|x\rangle = |a^x \pmod N \rangle$.
  * **Continued Fractions Algorithm:** The classical routine used to extract the critical **order $r$** from the quantum measurement results.
  * **Full Cryptanalysis:** Completes the loop by using the factors to calculate the private key and decrypt the message.

-----

## 💻 Tech Stack

| Category | Tool / Library | Purpose |
| :--- | :--- | :--- |
| **Quantum Framework** | **Qiskit** | Core library for quantum circuit creation and manipulation. |
| **Execution** | **QiskitRuntimeService** / **Aer** | Manages communication with IBM Quantum hardware or local quantum simulators. |
| **Classical Computing** | **Python** / **NumPy** | Handles number theory, GCD calculations, and classical post-processing. |
| **Project Format** | **Jupyter Notebook** | Provides an interactive, step-by-step documentation and execution environment. |

-----

## 🧠 How It Works

Shor's algorithm consists of two main parts: a classical reduction and a quantum order-finding subroutine.

### 1\. Classical Reduction

The goal is to factor a composite number $N$ (**$N=15$**).

  * **Select Base:** Choose a random integer **$a$** (e.g., **$a=2$**) such that $\text{gcd}(a, N) = 1$.
  * **Find Order:** The IFP is mathematically reduced to finding the **order** $\mathbf{r}$ (period) of the function $f(x) = a^x \pmod N$, where $r$ is the smallest positive integer such that $a^r \equiv 1 \pmod N$.

### 2\. Quantum Order Finding

The quantum computer efficiently finds $r$ using the principle of quantum interference.

  * **Circuit Construction:** The circuit uses a **Control Register (8 qubits)** initialized in a superposition and a **Target Register (4 qubits)** initialized to $|1\rangle$.
  * **Modular Exponentiation:** A series of highly complex **Controlled-$U$ gates** are applied, creating an entangled state where the periods of the function are encoded in the phases of the control register .
  * **Phase Extraction:** The **Inverse Quantum Fourier Transform ($\text{QFT}^{-1}$)** converts these phase-encoded periods into measurable probability amplitudes. The result of the final measurement $m$ is proportional to $\frac{k}{r}$.

### 3\. Factor Extraction and Decryption

  * **Extract $r$:** The classical **Continued Fractions** algorithm is applied to the measured value $m$ to extract the true order $\mathbf{r=4}$.
  * **Factor $N$:** The factors $\mathbf{p=3}$ and $\mathbf{q=5}$ are quickly calculated using $r$:
    $$\mathbf{p}, \mathbf{q} = \text{gcd}(a^{r/2} \pm 1, N)$$
  * **Decrypt Message:** The public exponent $e=3$ and the found factors $p, q$ are used to calculate the private key $\mathbf{d=3}$. The ciphertext $C=3$ is then decrypted to the original message $\mathbf{M=12}$.

-----

## ▶️ Get It Running

Follow these steps to set up the environment and execute the project notebook.

### 1\. Install Dependencies

Ensure you have a Python environment ready with the required libraries:

```bash
pip install qiskit qiskit-aer numpy qiskit-ibm-runtime
```

### 2\. Configure IBM Quantum Access (Optional)

To connect the Qiskit code to the IBM Quantum service (for running on real hardware or cloud simulators), you need an API key.

```bash
# Obtain your token from quantum.ibm.com and run this command once
from qiskit_ibm_runtime import QiskitRuntimeService
QiskitRuntimeService.save_account(channel="ibm_quantum", token="YOUR_API_TOKEN_HERE")
```

### 3\. Run the Notebook

Open the file `shors_algorithm_final (1).ipynb` and execute the cells sequentially. The output will show:

1.  The setup of the problem parameters.
2.  The quantum circuit construction and execution.
3.  The classical post-processing to determine the order $r$.
4.  The final factors $p$ and $q$.
5.  The derived private key $d$ and the decrypted message $M$.