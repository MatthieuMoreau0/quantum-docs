---
author: SoniaLopezBravo
description: Learn about eigenvectors, eigenvalues, and matrix exponentials, the fundamental tools used to describe and simulate quantum algorithms.
ms.author: sonialopez
ms.date: 05/23/2024
ms.service: azure-quantum
ms.subservice: core
ms.topic: concept
no-loc: ['Q#', '$$v', '$$', "$$", '$', "$", $, $$, "$$$", '$$$', $$$, '\cdots', 'bmatrix', '\ddots', '\equiv', '\sum', '\begin', '\end', '\sqrt', '\otimes', '{', '}', '\text', '\phi', '\kappa', '\psi', '\alpha', '\beta', '\gamma', '\delta', '\omega', '\bra', '\ket', '\boldone', '\mathbf{1}', '\\\\', '\\', '=', '\frac', '\text', '\mapsto', '\dagger', '\to', '\begin{cases}', '\end{cases}', '\operatorname', '\braket', '\id', '\expect', '\defeq', '\variance', '\dd', '&', '\begin{align}', '\end{align}', '\Lambda', '\lambda', '\Omega', '\mathrm', '\left', '\right', '\qquad', '\times', '\big', '\langle', '\rangle', '\bigg', '\Big', '|', '\mathbb', '\vec', '\in', '\texttt', '\ne', '<', '>', '\leq', '\geq', '~~', '~', '\begin{bmatrix}', '\end{bmatrix}', '\_']
title: Advanced Matrix Concepts
uid: microsoft.quantum.concepts.matrix-advanced
---

# Advanced matrix concepts in quantum computing

This article explores the concepts of [*eigenvalues, eigenvectors*,](https://en.wikipedia.org/wiki/Eigenvalues_and_eigenvectors) and [*exponentials*](https://en.wikipedia.org/wiki/Matrix_exponential). These concepts form a fundamental set of matrix tools that are used to describe and implement quantum algorithms. For the basics of vectors and matrices as they apply to quantum computing, see [Linear algebra for quantum computing](xref:microsoft.quantum.overview.algebra) and [Vectors and matrices](xref:microsoft.quantum.concepts.vectors). 

## Eigenvalues and eigenvectors 

Let $M$ be a square matrix and $v$ be a vector that isn't the all zeros vector (for example, the vector with all entries equal to $0$).

The vector $v$ is an [*eigenvector*](https://en.wikipedia.org/wiki/Eigenvalues_and_eigenvectors) of  $M$ if $Mv = cv$ for some number $c$. The integer $c$ is the [*eigenvalue*](https://en.wikipedia.org/wiki/Eigenvalues_and_eigenvectors) corresponding to the eigenvector $v$. In general, a matrix $M$ may transform a vector into any other vector. However, an eigenvector is special because it's left unchanged except for being multiplied by a number. Note that, if $v$ is an eigenvector with eigenvalue $c$, then $av$ is also an eigenvector (for any nonzero $a$) with the same eigenvalue.

For example, for the identity matrix, every vector $v$ is an eigenvector with eigenvalue $1$.

As another example, consider a [*diagonal matrix*](https://en.wikipedia.org/wiki/Diagonal_matrix) $D$, which only has non-zero entries on the diagonal:

$$
\begin{bmatrix}
d_1 & 0 & 0 \\\\ 0 & d_2 & 0 \\\\ 0 & 0 & d_3
\end{bmatrix}.
$$

The vectors

$$\begin{bmatrix}1 \\\\ 0 \\\\ 0 \end{bmatrix}, \begin{bmatrix}0 \\\\ 1 \\\\ 0\end{bmatrix} \text{and} \begin{bmatrix}0 \\\\ 0 \\\\ 1\end{bmatrix}$$

are eigenvectors of this matrix with eigenvalues  $d_1$, $d_2$, and $d_3$, respectively. If $d_1$, $d_2$, and $d_3$ are distinct numbers, then these vectors (and their multiples) are the only eigenvectors of the matrix $D$. In general, for a diagonal matrix it's easy to read off the eigenvalues and eigenvectors. The eigenvalues are all the numbers appearing on the diagonal, and their respective eigenvectors are the unit vectors with one entry equal to $1$ and the remaining entries equal to $0$.

Note in the above example that the eigenvectors of $D$ form a basis for $3$-dimensional vectors. A basis is a set of vectors such that any vector can be written as a linear combination of them. More explicitly, $v_1$, $v_2$, and $v_3$ form a basis if any vector $v$ can be written as $v=a_1 v_1 + a_2 v_2 + a_3 v_3$ for some numbers $a_1$, $a_2$, and $a_3$.

In quantum computing, there are essentially only two matrices that you encounter: Hermitian and unitary. Recall that a Hermitian matrix (also called self-adjoint) is a complex square matrix equal to its own complex conjugate transpose, while a unitary matrix is a complex square matrix whose *inverse* equals its complex conjugate transpose.

There's a general result known as the [*spectral theorem*](https://en.wikipedia.org/wiki/Spectral_theorem), which implies the following: for any Hermitian or unitary matrix $M$, there exists a unitary $U$ such that $M=U^\dagger D U$ for some diagonal matrix $D$. Furthermore, the diagonal entries of $D$ will be the eigenvalues of $M$, and columns of $U^\dagger$ will be the corresponding eigenvectors.
This factorization is known as [*spectral decomposition* or *eigendecomposition*](https://en.wikipedia.org/wiki/Eigendecomposition_of_a_matrix).

## Matrix exponentials

A [*matrix exponential*](https://en.wikipedia.org/wiki/Matrix_exponential) can also be defined in exact analogy to the exponential function.  The matrix exponential of a matrix $A$ can be expressed as

$$
e^A=\mathbf{1} + A + \frac{A^2}{2!}+\frac{A^3}{3!}+\cdots
$$

This is important because quantum mechanical time evolution is described by a unitary matrix of the form $e^{iB}$ for Hermitian matrix $B$. For this reason, performing matrix exponentials is a fundamental part of quantum computing and as such Q# offers intrinsic routines for describing these operations.
There are many ways in practice to compute a matrix exponential on a classical computer, and in general numerically approximating such an exponential is fraught with peril.  See [*Cleve Moler and Charles Van Loan. "Nineteen dubious ways to compute the exponential of a matrix." SIAM review 20.4 (1978): 801-836*](https://doi.org/10.1137/S00361445024180) for more information about the challenges involved.

The easiest way to understand how to compute the exponential of a matrix is through the eigenvalues and eigenvectors of that matrix. Specifically, the spectral theorem discussed above says that for every Hermitian or unitary matrix $A$ there exists a unitary matrix $U$ and a diagonal matrix $D$ such that $A=U^\dagger D U$.  Because of the properties of unitarity, $A^2 = U^\dagger D^2 U$ and similarly for any power $p$ $A^p = U^\dagger D^p U$.  If one substitutes this into the operator definition of the operator exponential:

$$
e^A= U^\dagger \left(\mathbf{1} +D +\frac{D^2}{2!}+\cdots \right)U= U^\dagger \begin{bmatrix}\exp(D_{11}) & 0 &\cdots &0\\\\ 0 & \exp(D_{22})&\cdots& 0\\\\ \vdots &\vdots &\ddots &\vdots\\\\ 0&0&\cdots&\exp(D_{NN}) \end{bmatrix} U.
$$

In other words, if you transform to the eigenbasis of the matrix $A$, then computing the matrix exponential is equivalent to computing the ordinary exponential of the eigenvalues of the matrix.  As many operations in quantum computing involve performing matrix exponentials, this trick of transforming into the eigenbasis of a matrix to simplify performing the operator exponential appears frequently. It's the basis behind many quantum algorithms such as Trotter–Suzuki-style quantum simulation methods discussed later in this guide.

Another useful property holds for [*involutory matrices*](https://en.wikipedia.org/wiki/Involutory_matrix).
An involutory matrix $B$ is both unitary and Hermitian, that is, $B=B^{-1}=B^\dagger$. Then, an involutory matrix is a  square matrix equal to its own inverse, $B^2=\mathbf{1}$.
By applying this property to the above expansion of the matrix exponential, grouping the $\mathbf{1}$ and the $B$ terms together, and applying [Maclaurin's theorem to the cosine and sine functions](https://en.wikibooks.org/wiki/Trigonometry/Power_Series_for_Cosine_and_Sine), the identity

$$e^{iBx}=\mathbf{1} \cos(x)+ iB\sin(x)$$

holds for any real value $x$. This trick is especially useful because it allows you to reason about the actions that matrix exponentials have, even if the dimension of $B$ is exponentially large, for the special case when $B$ is involutory.

## Next steps

- [The qubit](xref:microsoft.quantum.concepts.qubit)
- [Multiple qubits](xref:microsoft.quantum.concepts.multiple-qubits)
- [Dirac notation](xref:microsoft.quantum.concepts.dirac)
- [Pauli measurements](xref:microsoft.quantum.concepts.pauli)
- [T gates and T factories](xref:microsoft.quantum.concepts.tfactories)
