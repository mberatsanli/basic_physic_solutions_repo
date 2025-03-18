# Problem 1

Orbital Period and Orbital Radius

## 1. Theoretical Foundation

### Kepler’s Third Law
Kepler’s Third Law states that the square of a planet’s orbital period \( T \) is proportional to the cube of its orbital radius \( R \):

$$
T^2 \propto R^3
$$
For a circular orbit, this can be derived using Newton’s law of gravitation and centripetal force:

1. **Gravitational Force as Centripetal Force:**
   $$
   \frac{GMm}{R^2} = m \frac{v^2}{R}
   $$
  
   where:
   - \( G \) is the gravitational constant,
   - \( M \) is the mass of the central body,
   - \( m \) is the mass of the orbiting body,
   - \( R \) is the orbital radius,
   - \( v \) is the orbital velocity.

2. **Orbital Velocity from Period:**
   The orbital velocity \( v \) is related to the orbital period \( T \) as:
   $$
   v = \frac{2 \pi R}{T}
   $$

3. **Deriving Kepler’s Third Law:**
   Substituting \( v \) into the force equation and solving for \( T \), we get:
   $$
   T^2 = \frac{4 \pi^2}{GM} R^3
   $$
   This confirms that \( T^2 \propto R^3 \), where the proportionality constant depends on \( G \) and \( M \).

### Implications in Astronomy
- **Planetary Mass Calculation:** Given the period and radius of a planet’s moon, we can determine the planet’s mass.
- **Determining Distances:** If the period of a planet’s orbit around the Sun is known, its orbital radius can be estimated.
- **Satellite Orbits:** Used to design stable satellite orbits around Earth and other celestial bodies.

---

## 2. Real-World Examples

1. **The Moon’s Orbit around Earth**
   - The Moon orbits Earth with a period of \( T = 27.3 \) days.
   - The average orbital radius is about \( 3.84 \times 10^5 \) km.
   - Using Kepler’s law, we can verify the mass of Earth.

2. **Planets in the Solar System**
   - Kepler’s law allows us to compare planetary orbits.
   - Example: Earth’s orbital radius \( 1 \) AU and period \( 1 \) year help determine distances of other planets.

---

## 3. Computational Model
The following Python script simulates circular orbits and verifies Kepler’s Third Law.

```python
import numpy as np
import matplotlib.pyplot as plt

# Constants
G = 6.67430e-11  # Gravitational constant (m^3 kg^-1 s^-2)
M_sun = 1.989e30  # Mass of the Sun (kg)

# Function to calculate orbital period
def orbital_period(radius, mass=M_sun):
    return 2 * np.pi * np.sqrt(radius**3 / (G * mass))

# Generate data
radii = np.logspace(9, 12, 100)  # Orbital radii from 10^9 to 10^12 meters
periods = orbital_period(radii)

# Verify Kepler's Third Law
T_squared = periods**2
R_cubed = radii**3

# Plot T^2 vs R^3
plt.figure(figsize=(8,6))
plt.plot(R_cubed, T_squared, label="$T^2 \propto R^3$", color='b')
plt.xlabel("Orbital Radius Cubed (m^3)")
plt.ylabel("Orbital Period Squared (s^2)")
plt.title("Verification of Kepler's Third Law")
plt.legend()
plt.grid()
plt.show()

# Simulating circular orbit
def circular_orbit(radius, num_points=100):
    theta = np.linspace(0, 2*np.pi, num_points)
    x = radius * np.cos(theta)
    y = radius * np.sin(theta)
    return x, y

# Plot circular orbits for different radii
plt.figure(figsize=(8,8))
for r in [1e10, 3e10, 5e10]:
    x, y = circular_orbit(r)
    plt.plot(x, y, label=f"Radius = {r:.0e} m")

plt.scatter(0, 0, color='orange', label='Central Mass (e.g., Sun)')
plt.xlabel("x position (m)")
plt.ylabel("y position (m)")
plt.title("Simulated Circular Orbits")
plt.legend()
plt.grid()
plt.axis("equal")
plt.show()
```

This script:
- Computes orbital periods for different radii.
- Plots \( T^2 \) vs. \( R^3 \) to confirm a linear relationship.

![Verification of Kepler’s Third Law](assets/problem1.png)

---

## 4. Extensions and Limitations

- **Elliptical Orbits:** Kepler’s Law applies, but \( R \) represents the semi-major axis.
- **Relativistic Effects:** General relativity modifies Kepler’s laws in strong gravitational fields.
- **External Forces:** Perturbations from other bodies can alter orbits over time.

---

## 5. Conclusion
Kepler’s Third Law elegantly links orbital period and radius, enabling calculations in celestial mechanics. This relationship remains fundamental in astronomy, satellite engineering, and space exploration.
