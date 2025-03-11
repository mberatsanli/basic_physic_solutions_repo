# Problem 1

Here's the solution in **raw Markdown** format for your assignment:

---

## Problem 1: Interference Patterns on a Water Surface

### Motivation:

Interference occurs when waves from different sources overlap, creating new patterns. On a water surface, this can be easily observed when ripples from different points meet, forming distinctive interference patterns. These patterns can show us how waves combine in different ways, either reinforcing each other or canceling out.

Studying these patterns helps us understand wave behavior in a simple, visual way. It also allows us to explore important concepts, like the relationship between wave phase and the effects of multiple sources. This task offers a hands-on approach to learning about wave interactions and their real-world applications, making it an interesting and engaging way to dive into wave physics.

---

### Task:

A circular wave on the water surface, emanating from a point source located at $(x_0, y_0)$, can be described by the Single Disturbance equation:

$$
\eta(x, y, t) = \frac{A}{\sqrt{r}} \cdot \cos\left(kr - \omega t + \phi\right)
$$

where:

- $\eta(x, y, t)$ is the displacement of the water surface at point $(x, y)$ and time $t$,
- $A$ is the amplitude of the wave,
- $k = \frac{2\pi}{\lambda}$ is the wave number, related to the wavelength $\lambda$,
- $\omega = 2\pi f$ is the angular frequency, related to the frequency $f$,
- $r = \sqrt{(x - x_0)^2 + (y - y_0)^2}$ is the distance from the source to the point $(x, y)$,
- $\phi$ is the initial phase.

---

### Solution Steps:

#### 1. **Select a Regular Polygon:**
We will choose an **equilateral triangle** as our regular polygon. The three vertices of the triangle will act as the points where the wave sources are located.

#### 2. **Position the Sources:**
The wave sources are placed at the vertices of the regular triangle. The coordinates of the three vertices will be as follows:

- Vertex 1: $(x_1, y_1) = (1, 1)$
- Vertex 2: $(x_2, y_2) = (-0.5, -1)$
- Vertex 3: $(x_3, y_3) = (-0.5, -1)$

#### 3. **Write the Wave Equations:**
For each source, the wave function will be described as:

$$
\eta_1(x, y, t) = \frac{A}{\sqrt{r_1}} \cdot \cos(k r_1 - \omega t + \phi)
$$

$$
\eta_2(x, y, t) = \frac{A}{\sqrt{r_2}} \cdot \cos(k r_2 - \omega t + \phi)
$$

$$
\eta_3(x, y, t) = \frac{A}{\sqrt{r_3}} \cdot \cos(k r_3 - \omega t + \phi)
$$

Where $r_1$, $r_2$, and $r_3$ are the distances from each source to the observation point $(x, y)$.

#### 4. **Superposition of Waves:**
To find the total wave displacement, we sum the displacements from each source:

$$
\eta_{\text{sum}}(x, y, t) = \eta_1(x, y, t) + \eta_2(x, y, t) + \eta_3(x, y, t)
$$

#### 5. **Analyze the Interference Patterns:**
We will analyze the interference pattern formed by the superposition of these waves. Constructive interference will occur where the waves amplify each other, and destructive interference will occur where the waves cancel each other out.

#### 6. **Visualization:**
We will use Python to plot the interference patterns on a water surface. The following code implements the calculations and visualization:

```python
import numpy as np
import matplotlib.pyplot as plt

# Parameters
A = 1  # Amplitude
lambda_ = 1  # Wavelength
f = 1  # Frequency
k = 2 * np.pi / lambda_  # Wave number
omega = 2 * np.pi * f  # Angular frequency
phi = 0  # Initial phase

# Source coordinates (regular triangle)
x0 = np.array([1, -0.5, -0.5])
y0 = np.array([1, -1, -1])

# Distance function
def distance(x, y, x0, y0):
    return np.sqrt((x - x0)**2 + (y - y0)**2)

# Wave equation
def wave(x, y, t, x0, y0):
    r = distance(x, y, x0, y0)
    return A / np.sqrt(r) * np.cos(k * r - omega * t + phi)

# Time and spatial grid
t = 0  # Initial time
x_range = np.linspace(-2, 2, 400)
y_range = np.linspace(-2, 2, 400)
X, Y = np.meshgrid(x_range, y_range)

# Total wave displacement
eta_sum = np.zeros_like(X)

# Add the contributions from all sources
for i in range(3):
    eta_sum += wave(X, Y, t, x0[i], y0[i])

# Plotting the interference pattern
plt.figure(figsize=(8, 6))
plt.contourf(X, Y, eta_sum, 20, cmap='RdBu')
plt.colorbar(label='Displacement')
plt.title("Interference Pattern (Constructive and Destructive Interference)")
plt.xlabel("x")
plt.ylabel("y")
plt.show()
```

### Result:

The result is a graphical representation of the interference pattern formed by the superposition of waves emitted from the three sources at the vertices of the equilateral triangle. The pattern shows areas of constructive interference (where the waves amplify each other) and destructive interference (where the waves cancel each other out).

The contour plot displays these regions clearly, with brighter areas indicating constructive interference (amplification) and darker areas indicating destructive interference (cancellation). This type of visualization helps in understanding the wave interaction and superposition principle in a simple and intuitive way.

---

### Conclusion:

In this task, we analyzed the interference patterns formed by waves originating from sources placed at the vertices of a regular polygon (an equilateral triangle in this case). By summing the contributions from each wave source and visualizing the resulting wave field, we were able to identify the regions of constructive and destructive interference. This exercise provides a hands-on way to study wave superposition and interference in a simple setup, helping us to grasp important concepts in wave physics.

