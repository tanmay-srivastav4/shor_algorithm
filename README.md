# ⚛️ Quantum Cryptanalysis: Shor's Algorithm Breaks RSA

This repository demonstrates a full quantum-classical cryptanalytic attack against the **RSA Public-Key Cryptosystem** using an implementation of **Shor's Algorithm** built with the **Qiskit** framework for the IBM Quantum service. The project successfully factors the RSA modulus $N=15$ and decrypts a ciphertext.

-----

## 💡 Introduction

The security of modern public-key cryptography, such as **RSA**, is predicated on the computational difficulty of the **Integer Factorization Problem (IFP)** for classical computers. The time required for classical algorithms to factor a large number $N$ grows exponentially with the size of $N$.

**Shor's Algorithm**, developed by Peter Shor in 1994, is a quantum algorithm that solves the IFP in **polynomial time**, providing an exponential speedup that could eventually render current RSA encryption obsolete. This project implements the full pipeline to showcase this threat.

-----

## ⭐ Key Features

  * **Full Cryptanalytic Pipeline:** The project executes all steps required to break RSA:
    1.  Classical pre-processing (reduction to order-finding).
    2.  Quantum Order Finding subroutine.
    3.  Classical post-processing (factor extraction).
    4.  RSA decryption.
  * **Quantum Core:** Utilizes the **Quantum Phase Estimation (QPE)** protocol, which contains the modular exponentiation operation necessary for the period-finding logic.
  * **Exponential Speedup Mechanism:** Achieved through the **Inverse Quantum Fourier Transform ($\text{QFT}^{-1}$)**, which efficiently detects the period encoded in the quantum state's phases.
  * **IBM Quantum Integration:** The code is written in **Qiskit** and configured to connect to the **IBM Quantum service** for simulation (using `aer_simulator`) or execution on real quantum hardware.

-----

## 💻 Tech Stack

| Category | Tool / Library | Role |
| :--- | :--- | :--- |
| **Quantum Framework** | **Qiskit** | Core Python library for building, compiling, and running quantum circuits. |
| **Quantum Service** | **IBM Quantum** | Cloud platform providing access to quantum resources and simulators. |
| **Circuit Implementation** | **Controlled Modular Exponentiation** | Complex unitary gate sequence responsible for the periodic function calculation. |
| **Classical Post-processing** | **Python** (`numpy`, `fractions`) | Handles the Greatest Common Divisor (GCD), modular inverse, and the **Continued Fractions Algorithm**. |
| **Project Format** | **Jupyter Notebook** | Provides the interactive, documented, and sequential execution environment (`shors_algorithm_final (1).ipynb`). |

-----

## 🧠 How It Works

Shor's algorithm is a three-stage hybrid process, with the quantum step detecting the period $\mathbf{r}$ that is mathematically linked to the factors $p$ and $q$.

### 1\. Classical Pre-processing

The problem of factoring the RSA modulus $N=15$ is reduced to finding the **order $r$** of a modular exponentiation function, $f(x) = a^x \pmod N$.

  * **Parameters:** **$N=15$** (the modulus) and $\mathbf{a=2}$ (a random base coprime to $N$).
  * **Goal:** Find the smallest integer **$r$** such that $2^r \equiv 1 \pmod{15}$.

### 2\. Quantum Order Finding

This stage is executed on the quantum computer (or simulator).

  * **Circuit Design:** A **12-qubit circuit** is constructed (8 control qubits and 4 target qubits).
      * The **Control Register** is put into superposition using Hadamard gates.
      * The **Controlled Modular Exponentiation** gates entangle the control and target registers based on the function $f(x)$.
      * The **Inverse Quantum Fourier Transform ($\text{QFT}^{-1}$)** is applied to the control register to transform the periodic state into peaks centered at multiples of $1/r$.
      * 
  * **Measurement:** Measuring the final state of the control register yields a value $m$ that approximates $\frac{k}{r}$.

### 3\. Factor Extraction & Decryption

This returns to classical computation to complete the cryptanalysis.

  * **Extract Period $r$:** The classical **Continued Fractions Algorithm** is applied to the measured value $m$ to extract the true order $\mathbf{r=4}$.
  * **Factor $N$:** The factors $p$ and $q$ are computed using the greatest common divisor (GCD):
    $$\mathbf{p}, \mathbf{q} = \text{gcd}(a^{r/2} \pm 1, N) \implies \text{gcd}(2^{4/2} \pm 1, 15) = \mathbf{(3, 5)}$$
  * **Decrypt Message:** The factors are used to calculate Euler's Totient $\Phi(N)=(3-1)(5-1)=8$ and the private key $d$ (modular inverse of $e=3$ modulo 8, giving $\mathbf{d=3}$). The ciphertext $\mathbf{C=3}$ is then decrypted to the original message $\mathbf{M=12}$.

-----

## ▶️ Get It Running

### 1\. Install Dependencies

You will need a Python environment with Qiskit and associated packages.

```bash
pip install qiskit qiskit-aer numpy qiskit-ibm-runtime
```

### 2\. Connect the IBM Quantum Service

To run the circuit using IBM's quantum computing resources, you must connect your environment by providing your API token.

1.  **Get Your Token:** Obtain your personal API token from the [IBM Quantum platform](https://quantum.ibm.com/).

2.  **Save Credentials:** Run the following command **once** in your terminal or a Python script to securely save your account:

    ```python
    from qiskit_ibm_runtime import QiskitRuntimeService
    # Replace with your actual token
    QiskitRuntimeService.save_account(channel="ibm_quantum", token="YOUR_API_TOKEN_HERE")
    ```

3.  **Connect Backend in Code:** The notebook will then be able to initialize the service and connect to a backend (e.g., `service.get_backend('aer_simulator')`) to run the quantum circuit.
```

### 3\. Run the Notebook

Open the file `shors_algorithm_final (1).ipynb` and execute the cells sequentially. The output will show:

1.  The setup of the problem parameters.
2.  The quantum circuit construction and execution.
3.  The classical post-processing to determine the order $r$.
4.  The final factors $p$ and $q$.
5.  The derived private key $d$ and the decrypted message $M$.







