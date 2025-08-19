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

1. [Abstract](#🛰️-abstract)  
2. [Project Description](#project-description)  
   - [Motivation](#motivation)  
   - [Methodology](#methodology)  
   - [Model Assumptions](#model-assumptions)
3. [Theory & Equations](#-theory--equations)
4. [Notebook Flow (End‑to‑End)](#-notebook-flow-endtoend)
5. [Core Functions (from the Notebook)](#️-core-functions-from-the-notebook) 
6. [Project Structure](#project-structure)  
7. [Installation](#installation)  
8. [Running the Notebook](#running-the-notebook)  
9. [Results](#results)  
10. [Poster](#poster)  
11. [Future Work](#future-work)  
12. [License](#license)  
13. [Contact](#contact)  

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

Gravitational Potential at any given point is determined by the cumulative effect of all planetary masses. The instantaneous gravitational field vector at observer’s location is given by: 

$$\vec{g}(\vec{r}_{\text{observer}}, t) = -\sum_{i\in \mathcal{P}}\frac{GM_i}{\left|\vec r_{\text{observer}}(t)-\vec r_i(t)\right|^2}\cdot\frac{\vec r_{\text{observer}}(t)-\vec r_i(t)}{\left|\vec r_{\text{observer}}(t)-\vec r_i(t)\right|}$$

Where:

$$\begin{align*}        &\vec{g}(\vec{r}_{\text{observer}}, t) \text{ is the gravitational field vector at the observer's position at time } t \\        &G \text{ is the gravitational constant} \\        &M_i \text{ is the mass of the } i\text{-th planet} \\        &\vec{r}_{\text{observer}}(t) \text{ is the position vector of the observer at time } t \\        &\vec{r}_i(t) \text{ is the position vector of the } i\text{-th planet at time } t \\        &\mathcal{P} \text{ is the set of all planets contributing to the field}    \end{align*}$$

However, gravitational influence does not propagate instantaneously. Instead it travels at the finite speed of light(c) to account for this, we introduce  retarded time which reflects the time at which the original gravitational influence originated

$$ \vec{g}(\vec{r}_{\text{observer}}, t) = -\sum_{i\in \mathcal{P}}\frac{GM_i}{\left|\vec r_{\text{observer}}(t)-\vec r_i(t_{\text{ret}})\right|^2}\cdot\frac{\vec r_{\text{observer}}(t)-\vec r_i(t_{\text{ret}})}{\left|\vec r_{\text{observer}}(t)-\vec r_i(t_{\text{ret}})\right|}
$$

Where:

$$ \begin{align*}
        &t_{\text{ret}} \text{ is the retarded time given implicitly by:} \\
        &t_{\text{ret}} = t - \frac{\left|\vec r_{\text{observer}}(t)-\vec r_i(t_{\text{ret}})\right|}{c}
    \end{align*}
$$

This formulation ensures causality by incorporating the finite time it takes for the gravitational effects to propogate

### Equations of Motion (Test Particle)
\[
\dot{\mathbf{x}}(t)=\mathbf{v}(t),\qquad
\dot{\mathbf{v}}(t)=\mathbf{g}(\mathbf{x}(t),t).
\]

### Equations for trahectory over a short time
$$
\begin{aligned}\vec v_{i+1}&=\vec v_i+\vec a_i(\vec s_i)\cdot t\\\vec s_{i+1}&=\vec v_i\cdot t+\frac{\vec a_i(\vec s_i)\cdot t^2}{2}+\vec s_i
\end{aligned}
$$
The above equations are used to iteratively to find the path

### Monte Carlo Initialization
$$
\mathbf{x}_0\sim\mathcal{N}(\bar{\mathbf{x}}_0,\,\Sigma_x),\qquad
\mathbf{v}_0\sim\mathcal{N}(\bar{\mathbf{v}}_0,\,\Sigma_v).
$$

### Numerical Integration & Termination
- Fixed step \(\Delta t\approx 100\,\mathrm{s}\) with velocity/acceleration held constant within a step (semi‑implicit Euler).  
- **Stop conditions:** Earth impact (radius threshold), or end‑time \(T\).  
- **Recorded per run:** impact flag, minimum distances to Earth/Jupiter, and time of closest approach.

---

## 🧭 Notebook Flow (End‑to‑End)

1. **Fetch ephemerides** with `process_targets(targets, date_str)` using JPL Horizons for the selected date.  
2. **Transform reference frame** via `transform_to_rotated_heliocentric_frame(...)` so Earth lies at **(1 AU, 0, 0)**, the x‑axis points from Sun→Earth, and z‑axis is normal to Earth’s orbital plane.  
3. **Set baseline state** `observer_pos_init`, `observer_vel_init` (derived from the chosen asteroid/Earth state on the date).  
4. **Sample initial conditions** with `generate_initial_conditions(n, sigma_pos, sigma_vel)` to create a cloud of starting positions/velocities.  
5. **Propagate each particle** via `simulate_one(s0, v0, t_max, jupiter_flag)` using a restricted N‑body model (Sun+Earth, optionally **Jupiter**) and **retarded time** in `grav_field(...)`. Adaptive timesteps are chosen by `find_dt(...)`.  
6. **Record outcomes** (collision type, min distances) with `monte_carlo_sim_from_initials(...)` → **CSV**.  
7. **Analyze results** with `summarize_results(...)`, visualize distributions and confidence intervals with `plot_min_distance_histograms(...)`, and test robustness with `sensitivity_plot(...)` for **pos/vel perturbations** with/without Jupiter.

**CSV schema written by the notebook**
```
ParticleID, InitialSpeed(m/s), InitialPosX(m), InitialPosY(m), InitialPosZ(m),
CollisionType, MinEarthDistance(m), MinJupiterDistance(m)
```
Where `CollisionType`: 0=no collision, 1=Earth, 2=Sun, 3=Jupiter.

---

## 🛠️ Core Functions (from the Notebook)

> Below is a concise reference to the manually‑written functions used throughout `AsteroidTest2.ipynb`.

### Ephemerides & Frames
- `process_targets(targets: dict, date_str: str) -> dict` — Fetches heliocentric states via **astroquery.jplhorizons**/**astropy** and converts them to the rotated heliocentric frame; returns `{name: {'pos_km', 'vel_ms'}}`.
- `transform_to_rotated_heliocentric_frame(pos_obj, vel_obj, pos_earth, vel_earth)` — Builds an orthonormal basis from Earth’s position/velocity; rotates Sun‑centred states into this frame and translates so Earth sits at `(1 AU, 0, 0)`.
- `normalize(v)` — Unit‑vector helper.  
- `signed_angle(a, b, axis)` / `signed_angle_2d(a, b)` — Signed angle between vectors (3D about `axis`, or 2D).

### Celestial Mechanics (model)
- `r_p(t)` — Earth’s **circular** heliocentric position at time `t` with angular speed `ω`.
- `j_p(t)` — Jupiter’s circular orbit (with inclination/ascending‑node rotations applied).
- `retarded_time(t, observer_pos, tol=1e-6, max_iter=100, jupiter=0)` — Fixed‑point solve for **retarded time** so gravity respects finite propagation speed `c` (Earth or Jupiter based on the flag).
- `grav_field(t, x1, jupiter=0)` — Superposed gravitational acceleration from **Sun + Earth (+ Jupiter if requested)** at position `x1`, using **retarded positions**.
- `find_dt(v, s, velocity_threshold=30000.0, small_dt=100.0, large_dt=1000.0, t=0.0)` — Simple **adaptive timestep**: smaller steps when moving fast or near Earth; larger otherwise.

### Simulation & Monte Carlo
- `simulate_one(s0, v0, t_max=T*2, jupiter_flag=0)` — Semi‑implicit Euler propagation with adaptive `dt`. Returns trajectory sample, `CollisionType`, and **minimum distances** to Earth/Jupiter.
- `generate_initial_conditions(n_particles, sigma_pos, sigma_vel)` — Draws `n_particles` initial states from normal distributions centred at the baseline `observer_pos_init`/`observer_vel_init`.
- `monte_carlo_sim_from_initials(s0_list, v0_list, t_max_years, output_file, jupiter_flag=0)` — Runs the ensemble, logs per‑particle outcomes to CSV (see schema above).
- `summarize_results(output_file="collision_data.csv")` — Prints **counts and rates** for Earth/Sun/Jupiter collisions.
- `plot_min_distance_histograms(csv_file="collision_data.csv", bins=50, confidence=0.95)` — Plots histograms of minimum distances and reports **mean ± CI** (Student‑t).
- `sensitivity_plot(observer_pos_init, observer_vel_init, deltas, perturb_type='pos', t_max_years=5.0, jupiter_flag_vals=[0,1])` — Sweeps multiplicative perturbations in **position** or **velocity**; produces a log‑x plot of minimum Earth distance, comparing **with vs without Jupiter**.

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
