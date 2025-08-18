# Restricted Multi-Body Monte Carlo Analysis of Earth–Impact Risk
# 🚀 Asteroid Impact Risk Analysis

![Python](https://img.shields.io/badge/Python-v3.10%2B-blue)  
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)  
![Matplotlib](https://img.shields.io/badge/Matplotlib-Latest-blue)  
![Numpy](https://img.shields.io/badge/Numpy-Latest-blue)  
![License](https://img.shields.io/badge/License-MIT-brightgreen)  

**Authors:**  
- Suriya Raman (24233242)  
- Jonas Robin Nadar (24203206)  

This project investigates the **orbital dynamics and Earth-impact risk of asteroids** using **Monte Carlo simulations**. We compute thousands of particle trajectories, analyze close approaches with Earth, Jupiter, and the Sun, and estimate collision probabilities. Sensitivity studies explore how small perturbations in initial conditions affect asteroid paths.

---

## 📖 Table of Contents

1. [Abstract](#abstract)  
2. [Project Description](#project-description)  
   - [Motivation](#motivation)  
   - [Methodology](#methodology)  
   - [Model Assumptions](#model-assumptions)
3. [Theory & Equations](#-theory--equations)  
4. [Project Structure](#project-structure)  
5. [Installation](#installation)  
6. [Running the Notebook](#running-the-notebook)  
7. [Results](#results)  
8. [Poster](#poster)  
9. [Future Work](#future-work)  
10. [License](#license)  
11. [Contact](#contact)  

---

## 🛰️ Abstract

Asteroid impacts are rare but potentially catastrophic. Predicting them requires accounting for uncertainty in orbital dynamics, since tiny variations in initial position/velocity can lead to drastically different outcomes.  

This project uses **Monte Carlo simulations** to:  
- Estimate **impact probabilities**  
- Analyze **minimum distance distributions**  
- Assess the role of **Jupiter’s gravitational influence**  

---

## 📌 Project Description

### Motivation
Asteroid impacts are a low-probability but high-consequence hazard. Our goal is to explore **how uncertainties in initial orbital data affect Earth-impact predictions**.

### Methodology
1. **Asteroid & Planet Data** – Gather orbital and physical parameters  
2. **Coordinate Transformation** – Convert to model reference frame  
3. **Monte Carlo Sampling** – Generate ~1000 initial conditions from normal distributions  
4. **Trajectory Propagation** – Simulate dynamics under restricted multi-body model  
5. **Outcome Analysis** – Compute impact probability, minimum distances, and confidence intervals  

### Model Assumptions
- Planets move in **circular orbits** with constant angular velocity  
- Test particles are **massless**  
- Gravity propagates at finite speed using **retarded time**  
- Restricted **N-body Newtonian** model applied  
- Short intervals (~100s): assume constant velocity and acceleration  


---

## 🧠 Theory & Equations

> This section distills the **Theory and Equations** from the A0 poster and formalizes them for the README.

### Gravitational Potential and Field (Restricted N‑Body)
Let $\mathbf{x}(t)\$ be the asteroid position and \(\mathbf{r}_i(t)\) the position of body *i* with mass \(M_i\). With finite propagation speed \(c\), the **retarded time** for body *i* is
\[
t_r^{(i)}\;=\;t\;-\;\frac{\left\|\,\mathbf{x}(t) - \mathbf{r}_i\!\left(t_r^{(i)}\right)\,\right\|}{c}.
\]

The **gravitational potential** at \(\mathbf{x},t\) is
\[
\Phi(\mathbf{x},t)\;=\;-\sum_{i=1}^{N} \frac{G M_i}{\left\|\,\mathbf{x}-\mathbf{r}_i\!\left(t_r^{(i)}\right)\,\right\|},
\]
and the **gravitational field** is
\[
\mathbf{g}(\mathbf{x},t)\;=\;-\nabla\Phi(\mathbf{x},t)\;=\;\sum_{i=1}^{N} G M_i\, \frac{\mathbf{r}_i\!\left(t_r^{(i)}\right)-\mathbf{x}}{\left\|\,\mathbf{r}_i\!\left(t_r^{(i)}\right)-\mathbf{x}\,\right\|^{3}}.
\]

### Equations of Motion (Test Particle)
\[
\dot{\mathbf{x}}(t)=\mathbf{v}(t),\qquad
\dot{\mathbf{v}}(t)=\mathbf{g}(\mathbf{x}(t),t).
\]

### Planet Ephemerides (Circular‑Orbit Approximation)
For each body *i*,
\[
\mathbf{r}_i(t)=R_i\begin{bmatrix}
\cos(\omega_i t+\phi_i)\\
\sin(\omega_i t+\phi_i)\\
0
\end{bmatrix}.
\]

### Monte Carlo Initialization
\[
\mathbf{x}_0\sim\mathcal{N}(\bar{\mathbf{x}}_0,\,\Sigma_x),\qquad
\mathbf{v}_0\sim\mathcal{N}(\bar{\mathbf{v}}_0,\,\Sigma_v).
\]

### Numerical Integration & Termination
- Fixed step \(\Delta t\approx 100\,\mathrm{s}\) with velocity/acceleration held constant within a step (semi‑implicit Euler).  
- **Stop conditions:** Earth impact (radius threshold), or end‑time \(T\).  
- **Recorded per run:** impact flag, minimum distances to Earth/Jupiter, and time of closest approach.

*Notes from the poster:* finite‑speed gravity via retarded time ensures causality; for short interactions the delay is often negligible, but we include it for completeness.  

---

## 📂 Project Structure

```plaintext
asteroid-impact-risk/
├── AsteroidTest2.ipynb                      # Jupyter Notebook with simulations & plots
├── A0 Poster.pdf                            # Research poster summarizing project
├── collision_no_jupiter.csv                 # Simulation output with no Jupiter influence
├── collision_with_jupiter.csv               # Simulation output with Jupiter influence
├── requirements.txt                         # Required Libraries
└── README.md                                # Documentation
```

---

## ⚙️ Installation

Ensure you have Python **3.10+** and Jupyter installed.

```bash
git clone https://github.com/ACM40960/project-in-maths-modelling-3-body-problem
cd project-in-maths-modelling-3-body-problem
pip install -r requirements.txt
```

Typical dependencies:
- numpy
- matplotlib  
- numba
- matplotlib
- pandas
- astroquery
- astropy
- scipy
  

---

## ▶️ Running the Notebook

To explore simulations:

```bash
jupyter notebook AsteroidTest2.ipynb
```

Inside the notebook, you can:
- Run Monte Carlo trajectory simulations  
- Visualize 2D/3D asteroid orbits  
- Analyze sensitivity to initial perturbations  

---

## 📊 Results

- Closest approach to Earth (simulation): **8.7297 × 10⁸ m**  
- NASA prediction: **1.0522 × 10⁹ m**  
- Jupiter’s role: **minor** for this NEO (Earth’s influence dominates)  
- Small perturbations (≤10⁻⁴) leave Earth distance unchanged; larger perturbations affect trajectories significantly  

---

## 🖼️ Poster

Our **A0 poster** summarizes the motivation, methodology, results, and future work.  

📄 [**Download Poster (PDF)**](A0%20Poster.pdf)  

---

## 🔮 Future Work

- Extend to **more planets** (Saturn, Venus, Mars)  
- Replace **circular with elliptical orbits**  
- Improve accuracy with **adaptive time-stepping**  
- Validate against **larger NASA datasets**  
- Use **machine learning** to accelerate Monte Carlo runs  

---

## 📜 License

This project is licensed under the **MIT License**.  

---

## 📬 Contact

For questions:  
- [suriya.soundararajansundar@ucdconnect.ie](mailto:suriya.soundararajansundar@ucdconnect.ie)  
- [jonas.nadar@ucdconnect.ie](mailto:jonas.nadar@ucdconnect.ie)  
