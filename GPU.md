# Submissions Guide [Scripts/Animations/Images/IPYNB]:
- **Exercise 1 — Implicit transient diffusion using dual timestepping**
	1. implicit_diffusion_1D.jl
	2. implicit_diffusion_1D.gif
	3. lecture3_ex1_mysol.ipynb
- **Exercise 2 — Operator splitting for advection-diffusion**
	1. implicit_advection_diffusion_1D.jl
	2. advection_diffusion_1D.gif
	3. lecture3_ex2_mysol.ipynb
- **Exercise 3 — Advection-diffusion in 2D**
	1. diffusion_only_2D.jl
	2. diffusion_only_2D.gif
	3. implicit_advection_diffusion_2D.jl
	4. advection_diffusion_2D.gif
	5. lecture3_ex3_mysol.ipynb
- **Exercise 4 — Optimal iteration parameters for pseudo-transient method**
	1. convergence_rate_study_1D.jl
	2. convergence_plot.png
	3. lecture3_ex4_mysol.ipynb

---

# Exercise 1 — Implicit transient diffusion using dual timestepping

![ex1-1](./implicit_diffusion_1D.gif)

The generated simulation models transient diffusion using dual time-stepping, where both physical time and pseudo-transient iterations are considered. 

The code solves for the concentration distribution `C` over 10 physical time steps and plots the error evolution with respect to the pseudo-transient iterations.

---

# Exercise 2 — Operator splitting for advection-diffusion

![ex2-1](./advection_diffusion_1D.gif)

The implementation presented here is a 1D version of an implicit advection-diffusion solver. The focus is on simulating the evolution of a concentration field, $C(x,t)$, over time and space, incorporating both diffusion and advection processes. 

The solver updates the concentration field iteratively using two main components: the diffusion step (via a pseudo-transient method) and the advection step (via an upwind scheme).

## **Diffusion Step (Implicit)**
The diffusion process is modeled implicitly using a pseudo-transient iteration loop. The diffusion equation being solved is:

$$
\frac{\partial C}{\partial t} = D \frac{\partial^2 C}{\partial x^2}
$$

where $D$ is the diffusion coefficient, and $C$ is the concentration field. The discretized form for this implementation is based on central differences for the second derivative:

$$
\frac{\partial^2 C}{\partial x^2} \approx \frac{C_{i+1} - 2C_i + C_{i-1}}{\Delta x^2}
$$

The pseudo-transient method is used to iteratively solve for the steady-state diffusion solution. Fluxes $q_x$ are updated during each iteration using the following update rule:

$$
q_x[i] \leftarrow q_x[i] - \frac{\Delta \tau}{\rho + \Delta \tau / D} \left( \frac{q_x[i]}{D} + \frac{C[i+1] - C[i]}{\Delta x} \right)
$$

where $\rho = \left( \frac{L_x}{D \cdot Re} \right)^2$ is a parameter related to the physical and numerical settings, and $\Delta \tau$ is the pseudo-time step determined by:

$$
\Delta \tau = \frac{\Delta x}{\sqrt{1/\rho}}
$$

After updating the fluxes, the concentration is updated using the flux differences:

$$
C_i \leftarrow C_i - \frac{\Delta \tau}{1 + \Delta \tau / \Delta t} \left( \frac{C_i - C_{\text{old},i}}{\Delta t} + \frac{q_x[i] - q_x[i-1]}{\Delta x} \right)
$$

This implicit update stabilizes the diffusion process over time steps.

## **Advection Step (Upwind Scheme)**
The advection equation being solved is:

$$
\frac{\partial C}{\partial t} + v \frac{\partial C}{\partial x} = 0
$$

where $v$ is the advection velocity. This implementation uses an upwind scheme to update the concentration field based on the direction of the advection velocity:

- For positive velocity $v > 0$, the upwind scheme uses backward differences:

$$
C_i \leftarrow C_i - \frac{v \Delta t}{\Delta x} (C_i - C_{i-1})
$$

- For negative velocity $v < 0$, forward differences are used:

$$
C_i \leftarrow C_i - \frac{v \Delta t}{\Delta x} (C_{i+1} - C_i)
$$

This ensures numerical stability for the advection-dominated flow by ensuring that information propagates correctly in the direction of the flow.

---
# Exercise 3 — Advection-diffusion in 2D

![ex3-1](./diffusion_only_2D.gif)
![ex3-2](./advection_diffusion_2D.gif)

## Part I: Diffusion-Only in 2D
The animation here is showing the time evolution of the concentration field `C` during `nt = 50` physical time steps. 

The figure contains 2 subplots, the first displaying the `heatmap` of the `C` field and the second the evolution of the by `nx` normalised iteration count.

## Part II: Advection-Diffusion in 2D

The animation now is showing the time evolution of the concentration field `C` during `nt = 50` physical time steps, where both diffusion and advection take place.

### Comments on Output

The plot output aligns with the physical expectations for a 2D **advection-diffusion process**. 

1. **Initial Condition**:
   - **Bright Circle at (`lx = 2.5`, `ly = 7.5`)**: This corresponds to the initial condition of a Gaussian distribution centered around this point. The code initializes the concentration `C` as a Gaussian centered at `lx/4` in the x-direction and `3ly/4` in the y-direction. This results in the initial peak being located roughly at `lx = 2.5` and `ly = 7.5`.
   - **Decreasing Intensity in Rings**: The bright center represents the highest concentration, and the rings indicate the gradual decrease in concentration as one moves away from the center. This is expected, as the Gaussian profile peaks at the center and decays exponentially outward.

2. **Evolution Over Time**:
   - **Advection Effect (Downward Diagonal Movement)**: The advection velocities are set as `vx = 10.0` (rightward) and `vy = -10.0` (downward). This causes the concentration field to be advected diagonally downward over time. The observation of the bright circle moving diagonally downward as physical time steps progress is consistent with this. The advection transports the concentration along the velocity field, resulting in the center of mass following a diagonal trajectory.
   - **Diffusion Effect (Becoming Less Sharp/Darker)**: Diffusion causes the concentration to spread out over time. As the simulation advances, the concentration profile becomes more diffuse, losing sharpness, and the peak concentration value decreases. 

---
# **Exercise 4 — Optimal iteration parameters for pseudo-transient method**

![ex4-1](./convergence_plot.png)

The plot shows that the pseudo-transient method converges fastest around a factor of 1.0 for $re$, requiring the fewest iterations. Deviation from this factor, either higher or lower, increases the number of iterations needed for convergence.





















