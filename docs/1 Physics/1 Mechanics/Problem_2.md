# Problem 2

Investigating the Dynamics of a Forced Damped Pendulum

## 1. Theoretical Foundation

### Governing Equation
The motion of a forced damped pendulum is governed by the nonlinear differential equation:

$$
\frac{d^2\theta}{dt^2} + b \frac{d\theta}{dt} + \frac{g}{L} \sin\theta = A \cos(\omega t)
$$

where:
- $\theta$ is the angular displacement,
- $b$ is the damping coefficient,
- $g$ is the acceleration due to gravity,
- $L$ is the length of the pendulum,
- $A$ is the amplitude of the external driving force,
- $\omega$ is the driving frequency.

### Approximate Solutions for Small Angles
For small angles (\( \theta \approx \sin \theta \)), the equation simplifies to:

$$
\frac{d^2\theta}{dt^2} + b \frac{d\theta}{dt} + \frac{g}{L} \theta = A \cos(\omega t)
$$

This corresponds to a damped, driven harmonic oscillator, which can be solved using standard methods. The steady-state solution takes the form:

$$
\theta(t) = \theta_0 e^{-bt/2} + C \cos(\omega t - \phi)
$$

where \( C \) and \( \phi \) depend on \( A, b, \omega \), and system parameters.

### Resonance Conditions
Resonance occurs when the driving frequency \( \omega \) is close to the natural frequency \( \omega_0 = \sqrt{g/L} \), leading to large oscillations. At resonance, energy transfer is maximized, which has practical implications in engineering and physics.

---

## 2. Analysis of Dynamics

### Influence of System Parameters

- **Damping Coefficient (\( b \))**: Higher damping suppresses oscillations and prevents chaotic motion.
- **Driving Amplitude (\( A \))**: Larger amplitudes can induce chaotic behavior and bifurcations.
- **Driving Frequency (\( \omega \))**: At specific values, resonance or chaos can emerge.

### Transition to Chaos

By varying \( A \) and \( \omega \), the system transitions from periodic oscillations to quasiperiodic and chaotic motion. These can be analyzed using:

- **Phase Diagrams**: Plots of \( \theta \) vs. \( d\theta/dt \) to visualize stability.
- **Poincaré Sections**: Discrete-time slices revealing periodicity or chaos.
- **Bifurcation Diagrams**: Showing system behavior changes as parameters vary.

---

## 3. Practical Applications

The forced damped pendulum model applies to various real-world systems:
- **Energy Harvesting Devices**: Used to optimize mechanical-to-electrical energy conversion.
- **Suspension Bridges**: Helps in understanding oscillations leading to structural failures.
- **Electrical Circuits**: Analogous to driven RLC circuits with damping and external forcing.

---

## 4. Implementation
Below is a Python script to simulate and visualize the forced damped pendulum.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.integrate import solve_ivp
from mpl_toolkits.mplot3d import Axes3D

def forced_damped_pendulum(t, y, b, g, L, A, omega):
    theta, omega_dot = y
    dtheta_dt = omega_dot
    domega_dt = -b * omega_dot - (g/L) * np.sin(theta) + A * np.cos(omega * t)
    return [dtheta_dt, domega_dt]

# Enhanced parameter set
b_values = [0.1, 0.5, 1.0]  # different damping coefficients
g = 9.81  # gravity (m/s^2)
L = 1.0   # pendulum length (m)
A = 1.2   # driving force amplitude
omega = 2.0  # driving frequency
y0 = [0.1, 0]  # initial conditions: [theta(0), omega(0)]
t_span = (0, 50)
t_eval = np.linspace(0, 50, 1000)

# Create subplots for different analyses
plt.figure(figsize=(15, 10))

# Plot 1: Compare different damping coefficients
plt.subplot(2, 2, 1)
for b in b_values:
    sol = solve_ivp(forced_damped_pendulum, t_span, y0, t_eval=t_eval, 
                    args=(b, g, L, A, omega))
    plt.plot(sol.t, sol.y[0], label=f'b = {b}')
plt.xlabel('Time (s)')
plt.ylabel('Angle (rad)')
plt.title('Effect of Damping Coefficient')
plt.legend()
plt.grid(True)

# Plot 2: Phase Space Portrait
plt.subplot(2, 2, 2)
b = 0.2  # Use moderate damping for phase space
sol = solve_ivp(forced_damped_pendulum, t_span, y0, t_eval=t_eval, 
                args=(b, g, L, A, omega))
plt.plot(sol.y[0], sol.y[1])
plt.xlabel('θ (rad)')
plt.ylabel('dθ/dt (rad/s)')
plt.title('Phase Space Portrait')
plt.grid(True)

# Plot 3: Resonance Analysis
frequencies = np.linspace(0.5, 4, 50)
amplitudes = []
b = 0.2
for w in frequencies:
    sol = solve_ivp(forced_damped_pendulum, (0, 100), y0, t_eval=np.linspace(80, 100, 200), 
                    args=(b, g, L, A, w))
    amplitudes.append(np.max(np.abs(sol.y[0])))

plt.subplot(2, 2, 3)
plt.plot(frequencies, amplitudes)
plt.xlabel('Driving Frequency ω (rad/s)')
plt.ylabel('Maximum Amplitude (rad)')
plt.title('Resonance Curve')
plt.grid(True)

# Plot 4: Energy Analysis
def system_energy(theta, omega, L, g):
    kinetic = 0.5 * L**2 * omega**2
    potential = g * L * (1 - np.cos(theta))
    return kinetic + potential

b = 0.2
sol = solve_ivp(forced_damped_pendulum, t_span, y0, t_eval=t_eval, 
                args=(b, g, L, A, omega))
energies = [system_energy(theta, omega, L, g) 
           for theta, omega in zip(sol.y[0], sol.y[1])]

plt.subplot(2, 2, 4)
plt.plot(sol.t, energies)
plt.xlabel('Time (s)')
plt.ylabel('Total Energy (J)')
plt.title('System Energy Evolution')
plt.grid(True)

plt.tight_layout()
plt.show()

# 3D Phase Space Trajectory
fig = plt.figure(figsize=(10, 8))
ax = fig.add_subplot(111, projection='3d')

b = 0.2
sol = solve_ivp(forced_damped_pendulum, t_span, y0, t_eval=t_eval, 
                args=(b, g, L, A, omega))

ax.plot(sol.y[0], sol.y[1], sol.t)
ax.set_xlabel('θ (rad)')
ax.set_ylabel('dθ/dt (rad/s)')
ax.set_zlabel('Time (s)')
ax.set_title('3D Phase Space Trajectory')
plt.show()

# Poincaré Section
plt.figure(figsize=(8, 8))
t_long = np.linspace(0, 200, 4000)
sol = solve_ivp(forced_damped_pendulum, (0, 200), y0, t_eval=t_long, 
                args=(b, g, L, A, omega))

# Sample points at driving period
period = 2*np.pi/omega
indices = [i for i in range(len(t_long)) 
          if abs((t_long[i] % period)) < 0.1]

plt.scatter(sol.y[0][indices], sol.y[1][indices], s=1)
plt.xlabel('θ (rad)')
plt.ylabel('dθ/dt (rad/s)')
plt.title('Poincaré Section')
plt.grid(True)
plt.show()
```

This script numerically solves the pendulum equation and plots \( \theta(t) \) over time.

![Effect of Damping Coefficient](assets/prob2_a1.png)

![3D Phase Space Trajectory](assets/prob2_a2.png)

![Poincaré Section](assets/prob2_a3.png)

---

## 5. Limitations and Extensions
- **Limitations**: Assumes a point mass pendulum, ignores friction and air resistance.
- **Extensions**: 
  - Nonlinear damping (e.g., air drag proportional to velocity squared).
  - Non-periodic driving forces to model irregular forcing.
  - Coupled pendulum systems for synchronization studies.

---

## 6. Conclusion
The forced damped pendulum demonstrates a wide range of behaviors, from simple harmonic motion to chaos. By adjusting damping, forcing, and frequency, we can explore resonance, stability, and chaotic dynamics, providing insights into both fundamental physics and engineering applications.