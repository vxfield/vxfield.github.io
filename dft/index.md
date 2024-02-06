---
title: Density Functional Theory (DFT) Tutorial
author: Victor XField
created_at: 2024-01-27
updated_at: 2024-02-01
toc:
  depth_from: 2
  depth_to: 6
  ordered: false
---
# Density Functional Theory Tutorial

Copyright 2024 [Victor XField](https://www.linkedin.com/in/xfield/) - [MIT License](license.md)

> ***Note to Prof. Wilkes:***
  When I sent you the proposal for Project 1 about writing a  Density Functional Theory calculator in two weeks, I was really underestimating what I would need to cover. That was my first mistake. <br/>
  My second mistake is how long this page grew to be. I wanted to make sure I had all the pre-requisites to understand the DFT algorithm, and I used this page to record that.
  Unfortunately, there is so much to cover to make sense out of it, that I was only able to get to the beginning of the *Hartree-Fock method* by the due date. <br/>
  You can ignore the *Introduction to Quantum Mechanics* section and focus on the *Hartree-Fock method*.

> ***Note:*** This tutorial was written as I took Prof. Jeffrey Wilkes's course [PHYS 542: Numerical Methods in Physics](https://phys.washington.edu/courses/2024/winter/phys/542/a) at the University of Washington's [MS in Physics](https://www.physicsmasters.uw.edu/) program in 2024.

## Table of Contents {ignore=true}
[TOC]

## 1: Introduction

This is a tutorial on Density Functional Theory (DTF), a method used in computational physics/chemistry to model many-body quantum-mechanical systems such as atoms, molecules and condensed phases of matter.

In ancient times, people known as alchemists learned to manipulate and transform matter by mixing different elements, heating them up, cooling them down, and changing other environment conditions. The process was extremely laborious and time-consuming, requiring a lot of trial-and-error.

But even today, many things can only be discovered with a trial-and-error approach, executed by experienced researchers in expensive labs.

Density Functional Theory has the potential to drastically cut the cost and time of these discoveries by narrowing-down promising candidates that should be tested in the lab.

For example, DFT can help with:

1. Predicting the electronic structure of atoms and molecules, such as ground state, chemical potential, orbitals, electronic bands, magnetic properties, charge densities, etc.
1. Simulating forces between atoms and molecules and their binding energies.
1. Discovering efficient synthesis of chemical compounds and catalysts, reducing costs and waste in the chemical industry.
1. Discovering/designing new molecules that interact with other molecules in specific ways, developing new pharmaceutical drugs, creating lighter and stronger materials.

### 1.1: Prerequisite Knowledge

Although knowing the basic principles of electromagnetism, atomic theory, quantum-mechanics, and numerical methods can help, this tutorial assumes you know very little about these topics.

I'll try to explain things from first principles, and build the understanding from there.

If you already know the fundamentals about Quantum Mechanics and electron spin orbitals, feel free to jump to [3: Hartree-Fock method](#3-hartree-fock-method).

## 2: Introduction to Quantum Mechanics

### 2.1: Modelling a simple atom

Let's start with the basics, a single Hydrogen atom. First with some classical principles, then with quantum mechanics.

In the 1900s, Rutherford and colleagues discovered that the atom has positive charges (protons) in the nucleus and negative charges (electrons) orbiting it.

<img alt="Rutherford atom" src="https://upload.wikimedia.org/wikipedia/commons/9/92/Rutherford_atom.svg" width="200px" />

**Figure 1: Rutherford model**

The electron and proton are have electrical charges, so they will feel the *Coulomb* force, where $\epsilon_0$ is just a constant, $q$ and $Q$ are the electrical charges of the electron and the proton, and $r$ is the distance between the two.

$$
F_{Coulomb} = {1 \over 4 \pi \epsilon_0} {q * Q \over r^2}
$$

The charge of the electron and the proton have the same magnitude $e = 1.602 \times 10^{-19} \;Coulombs$, but the electron is negative.

So the total force is also negative, creating an attraction:

$$
F_{electron-proton} = - {1 \over 4 \pi \epsilon_0} {e^2 \over r^2}
$$

A classical particle would normally move in a straight line, so we could think the electron would do the same.
Except it's being pulled by the Coulomb force towards the proton.

But for some "mysterious" reason, instead of it just snapping into the proton like two magnets, it keeps its distance and find an equilibrium, orbiting the proton like the Moon orbits the Earth.

A classical model of this would be the balancing of a centripetal and a centrifugal force.

$$
|F_{centripetal}| = |F_{centrifugal}|
$$

The centripetal force is the Coulomb force pulling the electron towards the nucleus.

The centrifugal force is an inertial force of the electron wanting to keep its velocity unchanged and in a straight line. It's given by the mass $m$ of the electron, the orbital velocity $v$ and the radius $r$.

$$
F_{centrifugal} = {m v^2 \over r}
$$

Resulting in

$$
{1 \over 4 \pi \epsilon_0} {e^2 \over r^2} = {m_{e} v^2 \over r}
$$

Another way to model this is by the energy of the system.

$$
E_{total} = E_{kinetic} + E_{potential}
$$

The kinetic energy is from the electron's *momentum* (${mass} \times {velocity}$).

$$
E_{kinetic} = {1 \over 2} m_e v^2
$$

The potential energy is given by the electromagnetic field (result of the Coulomb force).

$$
E_{potential} = {1 \over 4 \pi \epsilon_0} {e^2 \over r}
$$

Giving

$$
E_{total} = {1 \over 2} m_e v^2 + {1 \over 4 \pi \epsilon_0} {e^2 \over r}
$$

But experiments shown that the Rutherford model was not quite right.

In the 1910s, it was found that the electron can actually orbit the nucleus at different distances, but not *any* distance.

Bohr proposed that only certain radiuses were allowed, and the electron can leap from one orbit to the next by absorbing or emitting a certain *quantum* of energy (a *photon*).

![Bohr atom](https://upload.wikimedia.org/wikipedia/commons/1/17/Bohr_atom_animation_2.gif)
**Figure 2: Bohr model**

Then, finally in the 1920s, Heisenberg, de Broglie, and Schrodinger formulated a new mathematical model of particles (including protons and electrons) that helped explain why the would not just go into the nucleus and why it's only allowed at certain orbits.

Even more, this new model proposed that particles behave as waves and that the electron is not like a little ball orbiting the nucleus, that we could know for certain where it is at a particular time.

There is a wave (modeled via a mathematical function) that gives the probability of finding the electron at a particular position. And like a water wave can move up and down in a swimming pool, the same goes with the "electron wave".

That lead to the current quantum-mechanical model of the atom, in which there is an "electron cloud" around the nucleus that is more dense at places that we are more likely to find an electron.

This cloud can be spherical, but it can also have some weird shapes, depending on which orbital (and energy) the electron is occupying.

![Hydrogen Electron Wave Function](https://upload.wikimedia.org/wikipedia/commons/e/e7/Hydrogen_Density_Plots.png)
**Figure 3: Quantum-mechanical model of the Hydrogen atom**

And similar to the classical model, Schrodinger proposed a formulation based on the energy of the system, and that there is a function $\Psi$ that when applied the energy operator $\^H$ to it would result in the same function $\Psi$ times the energy $E$ of the system.

$$
\^H \, \Psi(x) = E \, \Psi(x)
$$

$\Psi$ is a function that allows one to calculate several things that can be observed in the system, most notably the likelihood of finding a particle, in our case the electron, at a given position $x$ (and in time $t$, in case of the time-dependent Schrodinger equation that we will see later).

More specifically, $\Psi$ is a wave function. It describes how the system evolves with time, and how the energy transfers between kinetic and potential energies.

### 2.2: Wave functions

For example, a block suspended by a spring will have its energy varying between its kinetic energy (when the block is moving, it has velocity and momentum) and its potential energy (when the block is at either extremes of the motion, it stops and the energy is in a combination of gravitational potential energy and the spring extension/compression energy).

![Simple Harmonic Oscillator](https://upload.wikimedia.org/wikipedia/commons/e/ea/Simple_Harmonic_Motion_Orbit.gif)
**Figure 4: Simple Harmonic Oscillator**

This type of system has a periodic behavior and is easier to model it via sine and cosine functions, which are easily observed when you look at *phase space* of the system and circular "orbit" in the position+velocity space.

A classical wave function has the form of

$$
\Psi_{classical} = A \, \cos(\omega t + \phi)
$$

where, $A$ is the amplitude of the movement, $\omega$ is frequency at which the energy moves from kinetic to potential energy, $t$ is the moment in time, and $\phi$ is the phase, which represents the initial conditions (what was the initial position and momentum of the object).

But in Quantum Mechanics, it was hypothesized (*de Broglie hypothesis*) that every object in the universe has a wave associated to it. But it's not just that the idea that an object can move in a wave-like motion, the idea is that the object itself is a wave. It's hard to makes sense of it because, as far as we know, we can only see objects as definitive, concrete particles and the wave aspect of the objects can only be used to calculate probabilities related to their observable properties.

The *de Broglie hypothesis* was confirmed thru a variety of experiments, most famously the *double-slit experiment* in which a single electron was released at a time, and it would go thru two slits (yes, at the same time, it would go thru both!), and then it would hit the detector screen at a random position. But when this experiment is repeated many times, it became clear that the positions were not uniformly random, but instead they respected a *probability density* that matched what a wave would do if going thru the slits.

<img alt="Double-slit Experiment" src="https://upload.wikimedia.org/wikipedia/commons/c/cd/Double-slit.svg" width="600px" />

<img alt="Double-slit Detector Screen" src="https://upload.wikimedia.org/wikipedia/commons/b/bf/Electron_buildup_movie_from_%22Controlled_double-slit_electron_diffraction%22_Roger_Bach_et_al_2013_New_J._Phys._15_033018.gif" width="600px" height="300px" />

**Figure 5: Double-slit Experiment**

It was also postulated, and proven thru experiments, that that the *probability* $P[x]$ of finding a particle in position $x$ is given by the square of the wave function $\Psi(x)$. This is known as the *Born rule*.

$$
P[x] = |\Psi(x)|^2
$$

Since $\Psi(x)$ is a complex function, the $|z|^2$ operator is the complex number $z$ multiplied by its complex conjugate $z^*$ (or $\overline{z}$)

$$
|z|^2 = z \, z^* = z \, \overline{z}
\\
z = a + i \, b
\\
z^* = \overline{z} = a - i \, b
$$

More accurately, the probability of finding the particle between positions $c$ and $d$ is

$$
P[c \leq x \leq d] = \int_c^d{|\Psi(x)|^2 \, dx}
$$

and the probability of finding the particle somewhere in space must be 100% (this is called normalization)

$$
P[-\infty \leq x \leq \infty] = 1 = \int_{-\infty}^{\infty}{|\Psi(x)|^2 \, dx}
$$

The *de Broglie hypothesis* combined with the *Born rule*, and the fact that the wave function is time-periodic, implies that the *general* solution for the *time-dependent* *Schrodinger Equation* is comprised of complex numbers (several explanations can be found in Karam 2020) and has the form

$$
\Psi(x, t) = \sum_n{A_n \, e^{-i \, E_n \, t \over \hbar} \, \psi_{E_n}(x)}
$$

where $x$ is the particle's position, $t$ is the moment in time, $A_n$ are complex numbers indicating the amplitude for a given quantized energy level $n$, $i$ is the imaginary number $i=\sqrt{-1}$, $E_n$ is the energy at energy level $n$, $\hbar$ is a constant and $\psi_{E_n}(x)$ is the time-independent wave function for energy level $n$.

Also, from these results it follows that it's not possible to know both position and momentum of the particle at the same time with arbitrary accuracy (proved by gamma ray experiments).
This is know as the *Heisenberg's Uncertainty Principle*

$$
\sigma_x \sigma_p \geq {\hbar \over 2}
$$

where $\sigma_x$ is the standard deviation of position $x$ and $\sigma_p$ is the standard deviation of position $p$.

A particle, such an electron, bound by a potential (such as the Coulomb potential of a proton) behaves similar to that block on the spring.

![Quantum Harmonic Oscillator](https://upload.wikimedia.org/wikipedia/commons/9/90/QuantumHarmonicOscillatorAnimation.gif)
**Figure 6: Double-slit Experiment**
In *A* and *B* we have a classical object attached to a spring. In *C-H* we have different solutions for the *Quantum Harmonic Oscillator* (blue line is the real part, red line the imaginary part). *C-F* correspond to different energy levels, and *G* and *H* are linear combinations of multiple energy levels.

### 2.3: Schrodinger Equation

We digressed a bit on the idea of wave function, but it was a concept important to grasp.

Now, let's get back to the *Schrodinger Equation* we started with.

To recap, there is a function $\Psi$ that when applied the energy operator $\^H$ to it would result in the same function $\Psi$ times the energy $E$ of the system.

$$
\^H \, \Psi(x) = E \, \Psi(x)
$$

$\Psi$ is a complex-number wave function. It describes how the system evolves with time, one can use it to calculate the probabilities of measuring the values of observable properties of the system.

$\^H$ is the energy operator, known as the *Hamiltonian*, and it's comprised of a kinetic and a potential energy operator.

$$
\^H = \^T + \^V
$$

$$
\^T = {-\hbar \over 2m} {\partial^2 \over \partial x^2 }
$$

$$
\^V = V(x)
$$

$\^T$ is the kinetic energy operator. $\hbar$ is a constant, $m$ is the mass of the electron, ${\partial^2 \over \partial x^2}$ is the second partial derivative with respect to position.

$\^V$ is the potential energy operator, which is just a function that returns the potential energy at a given position $x$ (and in time, in case of the time-dependent Schrodinger equation).

The (time-independent) Schrodinger Equation is then

$$
\left[{-\hbar \over 2m} {\partial^2 \over \partial x^2 } + V(x)\right] \Psi(x) = E \, \Psi(x)
$$

So, if we want to find the likelihood of finding the position of the electron in the Hydrogen atom, we need to find the wave function $\Psi(x)$ for it.

### 2.4: Solutions to the Schrodinger Equation

> ***TODO:***  Write an overview about the analytical solution for the S.E., expectation values, energy, and the infeasibility of resolving many-body systems analytically.

### 2.5: Spin and Electron Orbitals

> ***TODO:***  Write an introduction to electron spin and orbitals.
    - orbital = 1-electron wavefunction
    - occupied and virtual (unoccupied) orbital
    - spatial orbital $\phi(\vec{r_1})$ for position $\vec{r}=(x,y,z)$
    - spin function $\sigma(\omega_1)$ for spin $\omega=|\alpha\rangle$ or $\omega=|\beta\rangle$
    - $\langle\alpha|\alpha\rangle=\langle\beta|\beta\rangle=1$, $\langle\alpha|\beta\rangle=\langle\beta|\alpha\rangle=0$
    - spin orbital $\chi(\vec{x_1})=\phi(\vec{r_1}) \, \sigma(\omega_1)$ for $\vec{x}=(\vec{r},\omega$)

## 3: Hartree-Fock Method

### 3.1: Molecular Hamiltonian

Let's start with a simple molecule $H_2$.

We have two Hydrogen atoms, each with a proton and an electron.

![Hydrogen molecule](https://upload.wikimedia.org/wikipedia/commons/0/09/Hydrogen.jpg)

As usual the Hamiltonian for this molecule is comprised of two main terms

$$
\^H = \^T + \^V
$$

$\^T$ represents the kinetic energy of the system, and is the sum of each individual particle's kinetic energy

$$
\^T =
    -{\hbar^2 \over 2 m_{electron1}} \nabla^2_{electron1}
    -{\hbar^2 \over 2 m_{electron3}} \nabla^2_{electron2}
    -{\hbar^2 \over 2 m_{proton1}} \nabla^2_{proton1}
    -{\hbar^2 \over 2 m_{proton2}} \nabla^2_{proton2}
$$

For brevity, we might as well just use some indices to iterate thru all these particles.

Also, instead of considering each individual particle inside the nucleus (protons and potentially neutrons), we are going to assume that the nucleus is a single point particle with charge $Z$ (number of protons). This is a very useful simplification that cut the number of particles we need to deal with by half. Later we will see why it's okay to do this.

We have $N$ electrons (2 in our example) identified by indices $i,j,k,...$.
And we have $M$ nuclei (2 protons in our example) identified by indices $A,B,C,...$
So $\^T$ becomes

$$
\^T =
    -\sum_{i=1}^N{\hbar^2 \over 2 m_e} \nabla^2_i
    -\sum_{A=1}^M{\hbar^2 \over 2 m_A} \nabla^2_A
$$

where $m_e$ is the mass of the electron, $m_A$ the mass of the nuclei, and $\nabla^2_i$ is the *Laplacian* operator

$$
\nabla^2_i =
    {\partial^2 \over \partial x_i^2}
    + {\partial^2 \over \partial y_i^2}
    + {\partial^2 \over \partial z_i^2}
$$

$\^V$ represents the *Coulomb* potential energy of the system.
We have nuclei that repel each other ($\^V_{NN}$), nuclei and electrons that attract each other  ($\^V_{Ne}$) and finally electrons that repel each other  ($\^V_{ee}$).

$$
\^V = \^V_{NN} + \^V_{Ne} + \^V_{ee}
$$

$$
\^V_{NN} =
    \sum_{A=1}^M \sum_{B=A+1}^M
    {1 \over 4 \pi \epsilon_0}
    {Z_A Z_B e^2 \over r_{A,B}}
$$

$$
\^V_{Ne} =
    - \sum_{A=1}^M \sum_{i=1}^N
    {1 \over 4 \pi \epsilon_0}
    {Z_A e^2 \over r_{A,i}}
$$

$$
\^V_{ee} =
    \sum_{i=1}^N \sum_{j=i+1}^N
    {1 \over 4 \pi \epsilon_0}
    {e^2 \over r_{i,j}}
$$

where $r_{i,j}$ is the distance between particles $i$ and $j$

The Hamiltonian is then

$$
\^H =
    \underbrace{- \sum_{i=1}^N{\hbar^2 \over 2 m_e} \nabla^2_i}_{\^T_e}
    \underbrace{- \sum_{A=1}^M{\hbar^2 \over 2 m_A} \nabla^2_A}_{\^T_N}
    \underbrace{+ \sum_{A=1}^M \sum_{B=A+1}^M
    {1 \over 4 \pi \epsilon_0}
    {Z_A Z_B e^2 \over r_{A,B}}}_{\^V_{NN}}
    \underbrace{- \sum_{A=1}^M \sum_{i=1}^N
    {1 \over 4 \pi \epsilon_0}
    {Z_A e^2 \over r_{A,i}}}_{\^V_{Ne}}
    \underbrace{+ \sum_{i=1}^N \sum_{j=i+1}^N
    {1 \over 4 \pi \epsilon_0}
    {e^2 \over r_{i,j}}}_{\^V_{ee}}
$$

Note: We are ignoring electron-spin for now, and we will it add it later.

### 3.2: Atomic Units

To make the math easier to read and compute, we are going to change the unit system from the *SI units* to the *Atomic units*.

This is also convenient because we are dealing with such small scales that measuring mass in kilograms is way out of proportion.

| Quantity         | Atomic unit value     | Atomic unit value                                        |
| ---------------- | --------------------- | -------------------------------------------------------- |
| Mass             | $m_e = 1$             | $9.109 \times 10^{-31}\text{ kg}$                        |
| Length           | $a_0 = 1$             | $5.292 \times 10^{-11}\text{ m}$                         |
| Charge           | $e = 1$               | $1.602 \times 10^{-19}\text{ C}$                         |
| Angular momentum | $\hbar = 1$           | $1.055 \times 10^{-34}\text{ J.s}$                       |
| Permittivity     | $4\pi\epsilon_0 = 1$  | $1.113 \times 10^{-10} \, {\text{C}^2 \over \text{J.m}}$ |

With these *Atomic units* and the following conventions

$$
\sum_{i} \equiv \sum_{i=1}^N \;,\;
\sum_{i<j} \equiv \sum_{i} \sum_{j=i+1}\;,\;
\sum_{iA} \equiv \sum_{i} \sum_{A}
$$

our Hamiltonian gets a bit cleaner

$$
\^H =
    \underbrace{- \sum_{i}{1 \over 2} \nabla^2_i}_{\^T_e}
    \underbrace{- \sum_{A}{1 \over 2 M_A} \nabla^2_A}_{\^T_N}
    \underbrace{+ \sum_{A<B}{Z_A Z_B \over r_{A,B}}}_{\^V_{NN}}
    \underbrace{- \sum_{iA}{Z_A \over r_{A,i}}}_{\^V_{Ne}}
    \underbrace{+ \sum_{i<j}{1 \over r_{i,j}}}_{\^V_{ee}}
$$

### 3.3: Born-Oppenheimer Approximation

As we saw before, analytically solving the *Schrodinger Equation* for more than two particles is pretty much unfeasible, as the problem scales exponentially with the number of particles.

So we need to solve the problem using a numerical method, and we also need to make some approximations otherwise even a numerical method may become too complex to be computed in a reasonable time.

The first approximation we already applied was considering all the particles in the nucleus as a single point charge.
The rationale for that is that the electrons are relatively far from the nucleus to perceived it as single point charge (*Gauss Law*).

The second approximation we will do, is called the Born-Oppenheimer approximation, in which we consider that the nucleus is not moving relative to all the electrons that are zipping around it,
The rationale for that us that the mass of the nucleus is 4 orders of magnitude larger than of the electron's.
From the electrons perspective, the nucleus is too heavy to move, and it's velocity (and kinetic energy) is practically zero.

That further simplifies our Hamiltonian

$$
\^H =
    \underbrace{- \sum_{i}{1 \over 2} \nabla^2_i}_{\^T_e}
    \underbrace{- \sum_{A}{1 \over 2 M_A} \nabla^2_A}_{\^T_N=0}
    \underbrace{+ \sum_{A<B}{Z_A Z_B \over r_{A,B}}}_{\^V_{NN}=\text{constant}}
    \underbrace{- \sum_{iA}{Z_A \over r_{A,i}}}_{\^V_{Ne}}
    \underbrace{+ \sum_{i<j}{1 \over r_{i,j}}}_{\^V_{ee}}
$$

Since the nuclear repulsion is a constant, it only impacts the total energy of the system, but it does not affect the wave function itself.

So we can focus on solving the *electronic* wave function with its correspinding *electronic* Hamiltonian

$$
\^H_{elec} |\psi_{elec}\rangle = E_{elec} |\psi_{elec}\rangle
$$

$$
\^H_{elec} =
    \underbrace{- \sum_{i}{1 \over 2} \nabla^2_i}_{\^T_e}
    \underbrace{- \sum_{iA}{Z_A \over r_{A,i}}}_{\^V_{Ne}}
    \underbrace{+ \sum_{i<j}{1 \over r_{i,j}}}_{\^V_{ee}}
$$

### 3.4: Hartree Products

Even with the Born-Oppenheimer approximation, the electronic equation is still hard to solve because:

1. There are $N(N-1) \over 2!$ interacting pair of electrons that repel each other in $\^V_{ee}$. For a simple $\text{H}_2$ molecule that is only one pair, but for a $\text{O}_2$ molecule that is 56 pairs!
2. We need to use a numerical method to solve the Schrodinger differential equation. For an $\text{O}_2$ molecule, we have 16 electrons in total, each one with 3 spatial dimensions. For example, if we discretize each dimension with 10 points, we would need to compute $10^{3 \times (8+8)}=10^{48}$ operations. The fastest high-performance computers can do around $10^{18}$ operations per second. So we would need $10^{30}$ seconds, but the age of the universe is "only" $10^{17}$ seconds!

So another assumption we are going to make is to consider that electrons don't interact with each other. This is not a good assumption because electrons do interact with each other, but we will start with this, and later try adjust our Hamiltonian by bringing the electron interaction back.

If we pretend that's the case, then our Hamiltonian gets much simpler because the electron repulsion term $\^V_{ee}$ disappear, and the remaining terms are independent for each electron.

$$
\^H_{elec} =
    \underbrace{- \sum_{i}{1 \over 2} \nabla^2_i}_{\^T_e}
    \underbrace{- \sum_{iA}{Z_A \over r_{A,i}}}_{\^V_{Ne}}
    \underbrace{+ \sum_{i<j}{1 \over r_{i,j}}}_{\^V_{ee}=0}
$$

Now, instead of solving a single equation with $3 \times N$ spatial dimensions, we can solve $N$ separate equations with only 3 spatial dimensions each, which is much simpler to do.

The spin-orbital $\chi(\vec{x}_i)$ becomes an eigenfunction of $\^h(i)$, with $\mathcal{E}$ as the orbital energy.

$$
\^h(i) =
    \underbrace{-{1 \over 2} \nabla^2_i}_{\^T_{e_i}}
    \underbrace{- \sum_{iA}{Z_A \over r_{A,i}}}_{\^V_{Ne_i}}
$$

$$
\^h(i) \chi(\vec{x}_i) = \mathcal{E} \chi(\vec{x}_i)
$$

Our total electronic Hamiltonian $\^H_{elec}$ becomes the sum of each individual electron Hamiltonian $\^h(i)$

$$
\^H_{elec} =
    \sum_{i=1}^N \^h(i)
$$

And the general electronic wave function $\Psi_{elec}$ is the product of each independent and uncorrelated electron wave function $\chi_i(\vec{x}_i)$.

This known as the *Hartree Product* of the spin-orbitals in the system.

$$
\Psi_{elec}(\{\vec{x}_i\}) = \prod_{i=1}^N \chi_i(\vec{x}_i)
$$

### 3.5: Anti-Symmetry and Slater Determinants

One problem with the *Hartree Product* is that it does not respect the *Anti-Symmetry Principle*.

In nature, as observed by multiple experiments, it's impossible to distinguish between two electrons. They all look the same.

The way we modelled the product of the spin-orbitals in the *Hartree Product*, we tied the wave function $\chi_1$ of electron $1$ with the position $\vec{x}_1$, and so on for all $\chi_i(\vec{x}_i)$.

This is a problem because it assumes that we know that electron $1$ is at position $\vec{x}_1$, $2$ is at $\vec{x}_2$, etc.
But in reality we can't distinguish them, and it means that electron $1$ could be at position $\vec{x}_2$, or at any other  $\vec{x}_i$.

The way to correctly model this *anti-symmetry principle* is to change the sign of the total electronic wave function every time we exchange electrons in our model.

For example, if we start with this wave function and exchange electrons $1$ and $2$

$$
\Psi_{elec}(\vec{x}_1, \vec{x}_2) = \chi_1(\vec{x}_1) \chi_2(\vec{x}_2)
$$

We need to add a negative sign to the wave function

$$
\Psi_{elec}(\vec{x}_2, \vec{x}_1) = - \chi_1(\vec{x}_2) \chi_2(\vec{x}_1)
$$

So our total electronic wave function needs to have a linear combination of every *Hartree Product* permutation with the correct signs applied to them.

In our two-electron example, it would look like this (electron 1 could be at either position $\vec{x}_1$ or $\vec{x}_2$)

$$
\Psi_{elec}(\vec{x}_1, \vec{x}_2) =
    {1 \over \sqrt{2}}
    \left[
    \chi_1(\vec{x}_1) \chi_2(\vec{x}_2)
    - \chi_2(\vec{x}_1) \chi_1(\vec{x}_2)
    \right]
$$

A feature of this linear combination is that it enforces the *Pauli Exclusion Principle*, which states that two fermions cannot simultaneously have the same occupy the same quantum state (in our case, two electrons cannot occupy the same orbital at the same time).
If both $\chi_1$ and $\chi_2$ are the same function, meaning that they are the same spin-orbital, the two terms will cancel and the total electronic wave function will be zero (i.e. zero probability of that happening). The same will happen if the two electrons are in the same position.

To represent each possible permutation of *Hartree Product* we need $N!$ terms with the correct signs, which quickly gets insanely large to write it down.
Instead we can use *Slater Determinant* to represent all these permutations in a compact form.

$$
\Psi_{elec}(\vec{x}_1, \vec{x}_2) =
    {1 \over \sqrt{2}}
    \left|
        \begin{array}{cc}
            \chi_1(\vec{x}_1) & \chi_2(\vec{x}_1) \\
            \chi_1(\vec{x}_2) & \chi_2(\vec{x}_2) \\
        \end{array}
    \right|
$$

Or for a general case

$$
\Psi_{elec}(\vec{x}_1, \vec{x}_2, ..., \vec{x}_N) =
    {1 \over \sqrt{N!}}
    \left|
        \begin{array}{cccc}
            \chi_1(\vec{x}_1) & \chi_2(\vec{x}_1) & ... & \chi_N(\vec{x}_1) \\
            \chi_1(\vec{x}_2) & \chi_2(\vec{x}_2) & ... & \chi_N(\vec{x}_2) \\
            \vdots & \vdots & \ddots & \vdots \\
            \chi_1(\vec{x}_N) & \chi_2(\vec{x}_N) & ... & \chi_N(\vec{x}_N) \\
        \end{array}
    \right|
$$

The *Slater Determinant* also enforces the *Pauli Exclusion Principle*, similar to what we saw before with the linear combination.

$$
\langle \Psi_A | \Psi_B \rangle =
    \left\{
        \begin{array}{l}
            1 \text{ if all } \{\chi_i\} \text{ are the same} \\
            0 \text{ otherwise}
        \end{array}
    \right.
$$

### 3.6: Excited States and Excited Determinants

Now that we have the *Slater Determinant* that account for the linear combination of all permutations of single-electron wave functions we are ready to start solving the electronic Schrodinger Equation, right? Well, no so fast...

We applied the *Slater Determinant* with the goal of not knowing which electron is which and at which location it is. A unidentifiable electron can be in any orbit.

But how many orbits did we consider? If you check our total wave function we have $N$ orbitals.

$$
\Psi_{elec}(\{\vec{x}_i\}) = \prod_{i=1}^N \chi_i(\vec{x}_i)
$$

That is alright for an atom/molecule in its ground state, but it's very likely that electrons will get excited and occupy other orbitals too, as we saw in the Bohr model of the atom.

So, for a more accurate estimation, we need to account for a few orbitals above the ground state, although in theory there could be infinite orbitals that can be occupied.

For example, if we have 2 electrons, our ground state wave function will be (using the ket-notation)

$$
|\Psi_{0}\rangle = | \chi_1, \chi_2 \rangle
$$

But if electron $1$ gets excited to orbital $4$, it will look like this

$$
|\Psi_1^4\rangle = | \chi_4, \chi_2 \rangle
$$

A general ground and singly-excited state (electron $a$ excited to orbital $r$) looks like

$$
|\Psi_0\rangle = | \chi_1, \chi_2, ..., \chi_a, ..., \chi_N \rangle
\\
|\Psi_a^r\rangle = | \chi_1, \chi_2, ..., \chi_r, ..., \chi_N \rangle
$$

Since we could have multiple electron excitations, as each one can potentially jump to a different orbital, so our total electronic wave function can be in a $\text{M-fold}$ excited state with $N$ electrons jumping to potentially $K$ orbitals.

$$
|\Psi_{a\,b\,...\,g_M}^{r\,s\,...\,e_M}\rangle
$$

We can also split the total wave function by a linear combination of the different levels of energy states.

$$
|\Psi\rangle =
    \underbrace{C_0 |\Psi_0\rangle}_{\text{ground state}}
    + \underbrace{\sum_{ar} C_a^r |\Psi_a^r\rangle}_{\text{single excited states}}
    + \underbrace{\sum_{a<b,\,r<s} C_{a\,b}^{r\,s} |\Psi_{a\,b}^{r\,s}\rangle}_{\text{double excited states}}
    + ...
$$

In the ground state, we know that all $N$ electrons are in the bottom $N$ orbitals, so we have $N$ orbital wave functions in total to solve.

As we allow excited states, the number of orbital wave functions grows exponentially at $N^{2M}$ for $N$ electrons and $M$ excitations (the factor of $2$ is because of to spin orbitals for each energy level).

The *Hartree-Fock method* focus on solving only the ground state determinant, and then other methods can be used to solve the excited determinants known as *Configuration Interactions*. They come as combinations of *Singles*, *Doubles*, *Triples*, etc., abbreviated as *CIS*, *CISD*, *CISDT*, etc. up to a *Full CI*.

| Method | Acronym | States |
| - | - | - |
| Hartree-Fock | HF | $ \|\Psi_0\rangle$ |
| Configuration Interaction Doubles | CID | $ \|\Psi_0\rangle, \|\Psi_{a\,b}^{r\,s}\rangle $ |
| Configuration Interaction Singles and Doubles | CISD | $ \|\Psi_0\rangle, \|\Psi_{a}^{r}\rangle, \|\Psi_{a\,b}^{r\,s}\rangle $ |
| Configuration Interaction Singles, Doubles and Triples | CISDT | $ \|\Psi_0\rangle, \|\Psi_{a}^{r}\rangle, \|\Psi_{a\,b}^{r\,s}\rangle, \|\Psi_{a\,b\,c}^{r\,s\,t}\rangle $ |
| ... | CISDTQ | ... |
| ... | CISDTQ6 | ... |
| ... | CISDTQ7 | ... |
| $\vdots$ | $\vdots$ | $\vdots$ |
| Full Configuration Interaction | Full CI | ... |

### 3.7: Restricted Determinants

So far we have been treating each spin-orbital as a separate wave function $\chi_i$, but in the most common cases every pair of $\chi_i$ shares the same spatial orbital, since two electrons can share the same spatial orbitals by having opposite $\alpha$ and $\beta$ spins.

We can then use what's called a *restricted determinant* for those pairs of electrons.
The *restricted determinants* are most used when solving systems with even-numbered electrons and/or *closed shells*, such as organic molecules.

In that case, it's convenient to use the following notation:

$$
| \chi_{i} \chi_{i+1}\rangle = | \psi_i \overline\psi_i \rangle
$$

In which $\psi_i$ is a short-hand for $\psi_i |\alpha\rangle$ and $\overline\psi_i$ a short-hand for $\psi_i |\beta\rangle$, both for a $\psi_i$ spatial wave function.

This way, the ground wave function can be represented as:

$$
| \Psi_0 \rangle =
    |
        \psi_1 \overline\psi_1
        \psi_2 \overline\psi_2
        ...
        \psi_{N/2} \overline\psi_{N/2}
    \rangle
$$

We could also have *unrestricted determinants* in which a $\alpha, \beta$ pair of electrons can have different spatial orbitals.
This useful for *open shell* systems such as metals and catalysts, but more complex than the *restricted determinants*.

There are 3 *variants* of the *Hartree-Fock method*:

| Variant Acronym | Variant | Applications |
| - | - | - |
| RHF | Restricted Hartree-Fock | Closed-shell or even-numbered systems |
| UHF | Unrestricted Hartree-Fock | Open-shell or odd-numbered systems |
| ROHF | Restricted Open-shell Hartree-Fock | Orbital analysis |

### 3.8: One-Electron Integrals

Now back to our total electronic Hamiltonian we had

$$
\^H_{elec} =
    \underbrace{- \sum_{i}{1 \over 2} \nabla^2_i}_{\^T_e}
    \underbrace{- \sum_{iA}{Z_A \over r_{A,i}}}_{\^V_{Ne}}
    \underbrace{+ \sum_{i<j}{1 \over r_{i,j}}}_{\^V_{ee}}
$$

The first two terms ($\^T_e$ and $\^V_{Ne}$) are only dependent on an individual one-electron kinetic energy and potential energy due to the nuclear attractions. The second term ($\^V_{ee}$) is a pair-wise sum of potential energies due to each electron repulsion against every other electron.

So what we can do is group the Hamiltonian terms into two operators, $\^O_{1}$ for the one-electron terms and $\^O_{2}$ for the two-electron term.

$$
\^H_{elec} =
    \underbrace{
        - \sum_{i}{1 \over 2} \nabla^2_i
        - \sum_{iA}{Z_A \over r_{A,i}}
    }_{\^O_{1}}
    \underbrace{
        + \sum_{i<j}{1 \over r_{i,j}}
    }_{\^O_{2}}
$$

$$
\^H_{elec} = \sum_{i} \^O_1^{(i)} + \sum_{i<j} \^O_2^{(ij)}
$$

The expected ground energy of the system is computed by integration of the Hamiltonian sandwiched between the complex conjugate of the wave function and the regular wave function (remember that $x$ contains the three spatial coordinates and one spin coordinates).

$$
E = \int dx \; \Psi_0^*(x) \, \^H \, \Psi_0(x)
$$

Or in *Dirac notation*

$$
E = \langle \Psi_0 | \^H | \Psi_0 \rangle
$$

The total energy of the system can also be split in two parts, for each $\^O_i$ operator.

$$
E =
    \underbrace{
        \sum_{i} \langle \Psi_0 | \^O_1^{(i)} | \Psi_0 \rangle
    }_{E_1}
    +
    \underbrace{
        \sum_{i<j} \langle \Psi_0 | \^O_2^{(ij)} | \Psi_0 \rangle
    }_{E_2}
$$

$$
E = E_1 + E_2
$$

The one-electron operator $\^O_{1}$ is just a sum of the one-electron Hamiltonian $\^h_i$ that we saw before when we assumed there was no electron-electron interaction.
$\^h_i$ is known as the *Core Hamiltonian*, and it is the easier to compute.

$$
\^O_1^{(i)} = \^h_i =
    -{1 \over 2} \nabla^2_i
    - \sum_{iA}{Z_A \over r_{A,i}}
$$

The single one-electron energy, known as the *core energy*  is

$$
h_i = \langle \chi_i | \^h_i | \chi_i \rangle
$$

or in integral form (remember that $\chi_i$ is the electron spin-orbital wave function)

$$
h_i = \int dx_i \; \chi_i^*(x_i) \, \^h_i \, \chi_i(x_i)
$$

Then the total of all one-electron energies is

$$
E_1 =
    \sum_{i} \langle \chi_i | \^h_i | \chi_i \rangle
$$

This allow us to calculate the individual energy contribution of each electron separately. Instead of having to do one integration with $4N$ dimensions, we can do $N$ integrations with $4$ dimensions, a much simpler process.

### 3.9: Two-electron Integrals

Now it comes the hard part! Solving the *many-body* *Schrodinger Equation* for when we have electron-electron interactions (in our $\^O_2$ operator).

$$
\^H_{elec} =
    \underbrace{
        - \sum_{i}{1 \over 2} \nabla^2_i
        - \sum_{iA}{Z_A \over r_{A,i}}
    }_{\^O_{1}}
    \underbrace{
        + \sum_{i<j}{1 \over r_{i,j}}
    }_{\^O_{2}}
$$

The $\^O_2$ operator is the total potential energy due to electron-electron repulsions.

$$
\^O_{2} = \^V_{ee} = {1 \over r_{i,j}}
$$

The total expectation value for the two-electron energies is

$$
E_2 =
    \sum_{i<j} \left\langle \Psi_0 \left| {1 \over r_{i,j}} \right| \Psi_0 \right\rangle
$$

For a given pair of electrons expectation value comes to be the following integrals (after some algebra). Note the exchange of the spin-orbitals ${\color{blue}
\chi_i}$ and ${\color{red}
\chi_j}$ on the second integral.

$$
\begin{align*}
v(i,j) =&
    \iint
        dx_i \, dx_j \;
        {\color{blue} \chi_i^*}({\color{blue} x_i}) \,
        {\color{red}\chi_j^*}({\color{red} x_j})
        {1 \over r_{i,j}}
        {\color{blue} \chi_i}({\color{blue} x_i}) \,
        {\color{red}\chi_j}({\color{red} x_j})
    \\ -&
    \iint
        dx_i \, dx_j \;
        {\color{blue} \chi_i^*}({\color{blue} x_i}) \,
        {\color{red}\chi_j^*}({\color{red} x_j})
        {1 \over r_{i,j}}
        {\color{red} \chi_j}({\color{blue} x_i}) \,
        {\color{blue}\chi_i}({\color{red} x_j})
\end{align*}
$$

Or in *Dirac notation* as

$$
\begin{align*}
v(i,j) &=
    \left \langle
    {\color{blue} i}
    {\color{red} j}
    \left |
    {1 \over r_{i,j}}
    \right |
    {\color{blue} i}
    {\color{red} j}
    \right \rangle
    -
    \left \langle
    {\color{blue} i}
    {\color{red} j}
    \left |
    {1 \over r_{i,j}}
    \right |
    {\color{red} j}
    {\color{blue} i}
    \right \rangle
\end{align*}
$$

Sometimes shorthanded as

$$
v(i,j) =
    \left \langle
    {\color{blue} i}
    {\color{red} j}
    ||
    {\color{blue} i}
    {\color{red} j}
    \right \rangle
$$

And the total expectation value for the two-electron energies is

$$
E_2 =
    \sum_{i<j}
    v(i,j)
$$




## References

- Griffiths, D. J. (2023). Introduction to Electrodynamics. [&#128279;](https://www.cambridge.org/highereducation/books/introduction-to-electrodynamics/FD23E188E2BDCDB40199CFE3386EC08F)
- Griffiths, D. J. (2018). Introduction to Quantum Mechanics. [&#128279;](https://www.cambridge.org/highereducation/books/introduction-to-quantum-mechanics/990799CA07A83FC5312402AF6860311E)
- Karam, R. (2020). Why are complex numbers needed in quantum mechanics? Some answers for the introductory level. [&#128279;](https://doi.org/10.1119/10.0000258)
- Čertík, O., Pask, J. E., Vackář, J. (2013). dftatom: A robust and general Schrödinger and Dirac solver for atomic structure calculations. [&#128279;](https://arxiv.org/abs/1209.1752v2)
- Thijssen, J. (2007). Computational Physics. [&#128279;](https://www.cambridge.org/core/books/computational-physics/BEE73B0139D4A9993193B57CDC62096E)
- Cramer, C. (2013). Essentials of Computational Chemistry [&#128279;](https://www.wiley.com/Essentials+of+Computational+Chemistry:+Theories+and+Models,+2nd+Edition-p-9781118712276)
- TMP Chem (2018). Hartree-Fock Theory. [&#128279;](https://www.youtube.com/playlist?list=PLm8ZSArAXicIijiVIx0yfk2ZOK-16ycji)

### Images

All images are MIT or Public Domain licensed from [Wikimedia Commons](https://commons.wikimedia.org/)



<!--
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
-->

<!-- markdownlint-configure-file
{
    "no-multiple-blanks": false,
    "no-inline-html": {
        "allowed_elements": [
            "script",
            "link",
            "br",
            "img"
        ]
    }
}
-->
