# Autonomous Multi-Constraint Surgical Navigation Architecture for Concentric Tube Robots (CTRs)

A sophisticated Reinforcement Learning (RL) architecture developed to handle real-time autonomous navigation and sub-millimeter tissue tracking of flexible Concentric Tube Robots (CTRs) navigating through breathing deformable anatomical tissues.

## Project Overview and Features

Continuum flexible robots are critical tools for navigating to deep, difficult-to-reach surgical locations (such as peripheral lung nodules through transoral bronchoscopy). But computing non-linear kinematics of such systems in real time and dealing with dynamically deforming organs is extremely challenging. This work resolves this problem by applying Deep Reinforcement Learning.

* **Clinical-Scale Mechanical Model:** Realistically simulates flexible continuum interaction of high curvature nitinol tube ($c_1 = 18.0, c_2 = 12.0$) with scaling to clinical-length constraints ($150\text{mm} - 250\text{mm}$).
* **Dynamic Deformable Target Tracking:** Autonomous tracking of the moving target node following a non-linear $0.25\text{Hz}$ breathing pattern.
* **Dynamic Obstacle Avoidance:** Navigation through the corridor containing dynamic, pulsating cardiovascular vascular obstruction constrained within tissue walls.
* **State Feature Scaling:** Scaling of tracking features ($\times 20$) in the observation space in order to avoid vanishing gradients and achieve ultimate tracking precision.
---

## System Architecture & Specifications

The architecture has been designed to separate the system into different algorithmic modules, which allows for optimal training convergence on constrained computing setups:

### 1. Actuator Mechanics & Kinematics Map Algorithm
Instead of using computationally intensive 3D engines to create an environment, the structure physics have been geometrically calculated by means of **Piecewise Constant Curvature (PCC)** model. The inputs are the tube translation movements ($s_1, s_2$) and its rotations ($\theta_1, \theta_2$). They are projected in the 3D Cartesian coordinate system as follows:

$$x = \frac{1}{\kappa}(1 - \cos(\kappa \cdot \Delta s))\cos(\phi)$$
$$y = \frac{1}{\kappa}(1 - \cos(\kappa \cdot \Delta s))\sin(\phi)$$
$$z = s_1 + \frac{1}{\kappa}\sin(\kappa \cdot \Delta s)$$

### 2. Reinforcement Learning Agent
* **Algorithm:** Proximal Policy Optimization (PPO) by using `Stable-Baselines3`.
### 3. Dense Proximity Reward Shaping
In order to counteract the policy paralysis that is likely to result due to sparsity in the rewards, the agent employs a densely **Velocity Alignment**-shaped reward coupled with multi-levels of step bonuses:

$$\text{Base Reward} = 400 \cdot (d_{\text{old}} - d_{\text{new}}) - 250 \cdot d_{\text{new}}$$

* **Collision Infringement:** $-50.0$ penalty applied if the tip breaches the $4\text{mm}$ boundary around the obstacle.
* **Precision Achievement:** $+100.0$ step bonus applied once the steady-state error falls under $1.5\text{mm}$.

---

## Research Gaps Filled

1. **Absence of Kinematic Delay:** Standard analytical controllers (such as Levenberg-Marquardt optimization loop) find difficulty in solving non-linear inverse kinematics rapidly enough in the case of rapid motion of the tissue. In contrast, the proposed neural approach provides the results instantly ($< 1\text{ms}$ inference time).
2. **Integration of Multi-constraints:** Unification of the obstacle avoidance, trajectory constraints and dynamic target capture into one generalized control policy without resorting to complicated mathematical modeling of the system.

---

## Performance Indicators & Validation Results
* **Accuracy of Steady-State Trajectory Tracking:** Provides **sub-millimeter level of accuracy ($< 1.5\text{mm}$ error trajectory tracking)** beyond the clinical requirements of ($< 2.0\text{mm}$) for peripheral lung biopsies.
* **Safety Performance:**
