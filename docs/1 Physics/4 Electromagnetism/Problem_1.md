import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
from matplotlib.animation import FuncAnimation
import matplotlib.gridspec as gridspec

def simulate_uniform_B(q, m, B, v0, r0, dt, steps):
    """Enhanced simulation with energy and momentum tracking"""
    r = np.zeros((steps, 3))
    v = np.zeros((steps, 3))
    E_kinetic = np.zeros(steps)
    angular_momentum = np.zeros((steps, 3))
    magnetic_moment = np.zeros(steps)
    
    r[0] = r0
    v[0] = v0
    
    # Initial values
    E_kinetic[0] = 0.5 * m * np.sum(v[0]**2)
    angular_momentum[0] = m * np.cross(r[0], v[0])
    
    B_mag = np.linalg.norm(B)
    v_perp = v[0] - np.dot(v[0], B) * B / B_mag**2
    magnetic_moment[0] = 0.5 * m * np.sum(v_perp**2) / B_mag
    
    for i in range(1, steps):
        F = q * np.cross(v[i-1], B)
        a = F / m
        v[i] = v[i-1] + a * dt
        r[i] = r[i-1] + v[i] * dt
        
        # Calculate conserved quantities
        E_kinetic[i] = 0.5 * m * np.sum(v[i]**2)
        angular_momentum[i] = m * np.cross(r[i], v[i])
        v_perp = v[i] - np.dot(v[i], B) * B / B_mag**2
        magnetic_moment[i] = 0.5 * m * np.sum(v_perp**2) / B_mag
    
    return r, v, E_kinetic, angular_momentum, magnetic_moment

def simulate_EB_fields(q, m, E, B, v0, r0, dt, steps):
    """Enhanced E×B drift simulation with additional physics parameters"""
    r = np.zeros((steps, 3))
    v = np.zeros((steps, 3))
    drift_velocity = np.zeros((steps, 3))
    gyroradius = np.zeros(steps)
    total_energy = np.zeros(steps)
    
    r[0] = r0
    v[0] = v0
    
    B_mag = np.linalg.norm(B)
    drift_velocity_theoretical = np.cross(E, B) / (B_mag**2)
    
    for i in range(1, steps):
        F = q * (E + np.cross(v[i-1], B))
        a = F / m
        v[i] = v[i-1] + a * dt
        r[i] = r[i-1] + v[i] * dt
        
        # Calculate drift velocity and gyroradius
        v_perp = v[i] - np.dot(v[i], B) * B / (B_mag**2)
        drift_velocity[i] = np.cross(E, B) / (B_mag**2)
        gyroradius[i] = m * np.linalg.norm(v_perp) / (abs(q) * B_mag)
        
        # Calculate total energy (kinetic + potential)
        total_energy[i] = 0.5 * m * np.sum(v[i]**2) + q * np.dot(E, r[i])
    
    return r, v, drift_velocity, gyroradius, total_energy

# Parameters
q = 1.6e-19  # electron charge
m = 9.1e-31  # electron mass
B = np.array([0, 0, 0.1])  # magnetic field in T
E = np.array([1000, 0, 0])  # electric field in V/m
v0 = np.array([1e6, 1e6, 0])  # initial velocity
r0 = np.array([0, 0, 0])  # initial position
dt = 1e-11
steps = 1000

# Run simulations
r_B, v_B, E_kin, L, mu = simulate_uniform_B(q, m, B, v0, r0, dt, steps)
r_EB, v_EB, v_drift, rg, E_total = simulate_EB_fields(q, m, E, B, v0, r0, dt, steps)

# Create comprehensive visualization with 8 subplots
plt.style.use('seaborn-darkgrid')
fig = plt.figure(figsize=(20, 15))
gs = gridspec.GridSpec(4, 2)

# 1. 3D trajectory in B field
ax1 = fig.add_subplot(gs[0, 0], projection='3d')
ax1.plot(r_B[:, 0], r_B[:, 1], r_B[:, 2], 'b-', label='Trajectory')
ax1.set_title('3D Particle Trajectory (B field)')
ax1.set_xlabel('X (m)')
ax1.set_ylabel('Y (m)')
ax1.set_zlabel('Z (m)')
ax1.legend()

# 2. Energy conservation in B field
ax2 = fig.add_subplot(gs[0, 1])
t = np.arange(steps) * dt
ax2.plot(t, E_kin / E_kin[0], 'g-', label='Normalized Kinetic Energy')
ax2.set_title('Energy Conservation (B field)')
ax2.set_xlabel('Time (s)')
ax2.set_ylabel('E/E₀')
ax2.legend()

# 3. E×B drift trajectory
ax3 = fig.add_subplot(gs[1, 0], projection='3d')
ax3.plot(r_EB[:, 0], r_EB[:, 1], r_EB[:, 2], 'r-', label='E×B Drift')
ax3.set_title('3D Particle Trajectory (E×B fields)')
ax3.set_xlabel('X (m)')
ax3.set_ylabel('Y (m)')
ax3.set_zlabel('Z (m)')
ax3.legend()

# 4. Total energy in E×B fields
ax4 = fig.add_subplot(gs[1, 1])
ax4.plot(t, E_total / E_total[0], 'm-', label='Normalized Total Energy')
ax4.set_title('Total Energy Conservation (E×B fields)')
ax4.set_xlabel('Time (s)')
ax4.set_ylabel('E_total/E₀')
ax4.legend()

# 5. Magnetic moment conservation
ax5 = fig.add_subplot(gs[2, 0])
ax5.plot(t, mu / mu[0], 'c-', label='Normalized Magnetic Moment')
ax5.set_title('Magnetic Moment Conservation')
ax5.set_xlabel('Time (s)')
ax5.set_ylabel('μ/μ₀')
ax5.legend()

# 6. Angular momentum components
ax6 = fig.add_subplot(gs[2, 1])
ax6.plot(t, L[:, 0], 'b-', label='Lx')
ax6.plot(t, L[:, 1], 'r-', label='Ly')
ax6.plot(t, L[:, 2], 'g-', label='Lz')
ax6.set_title('Angular Momentum Components')
ax6.set_xlabel('Time (s)')
ax6.set_ylabel('Angular Momentum (kg⋅m²/s)')
ax6.legend()

# 7. Phase space plot (x-vx)
ax7 = fig.add_subplot(gs[3, 0])
ax7.plot(r_B[:, 0], v_B[:, 0], 'k.', markersize=1, label='Phase Space')
ax7.set_title('Phase Space (x-vx)')
ax7.set_xlabel('Position x (m)')
ax7.set_ylabel('Velocity vx (m/s)')
ax7.legend()

# 8. Gyroradius evolution
ax8 = fig.add_subplot(gs[3, 1])
ax8.plot(t, rg * 1e6, 'y-', label='Gyroradius')
ax8.set_title('Gyroradius Evolution')
ax8.set_xlabel('Time (s)')
ax8.set_ylabel('Radius (μm)')
ax8.legend()

plt.tight_layout()
plt.show()

# Additional theoretical analysis and formulas:
"""
Key Physics Formulas and Concepts:

1. Cyclotron Frequency:
   ω_c = qB/m

2. Larmor Radius:
   r_L = mv_⊥/(qB)

3. E×B Drift Velocity:
   v_d = (E×B)/B²

4. Magnetic Moment (First Adiabatic Invariant):
   μ = mv_⊥²/(2B)

5. Drift Velocities:
   - Gradient B drift: v_∇B = (mv_⊥²/2qB³)(B×∇B)
   - Curvature drift: v_c = (mv_∥²/qB³)R_c×B
   - Polarization drift: v_p = (m/qB²)dE_⊥/dt

6. Energy Conservation:
   d/dt(½mv²) = qE·v

7. Canonical Momentum:
   P = mv + qA (A is vector potential)

8. Magnetic Mirror Force:
   F_∥ = -μ∇_∥B

9. Mirror Ratio and Loss Cone:
   R = B_max/B_min
   sin²(θ) > 1/R (trapping condition)

10. Relativistic Effects:
    - Relativistic mass: m = γm₀
    - Lorentz factor: γ = 1/√(1-v²/c²)
    - Relativistic cyclotron frequency: ω_c = qB/(γm₀)
""" 