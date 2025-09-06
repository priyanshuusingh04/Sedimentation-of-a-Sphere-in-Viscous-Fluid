# 🧪 CFD Simulation of Sphere Sedimentation in Viscous Fluids

## 📌 Project Overview
This project investigates the **sedimentation of a single sphere in a viscous fluid** using two OpenFOAM solvers:

1. **DPMFoam** – Lagrangian particle tracking solver with manual force computation  
2. **overPimpleDyMFoam** – Overset mesh solver with built-in force computation  

The study aims to compare solver methodologies, validate simulation results against theoretical correlations (Stokes’ Law & Schiller-Naumann), and analyze drag, velocity, and particle displacement characteristics.

---

## 📂 Repository Structure

```plaintext
Sphere-Sedimentation-Simulation/
│── case_files/            # OpenFOAM case directories for DPMFoam & overPimpleDyMFoam
│── scripts/               # Post-processing Python or ParaView scripts
│── images/                # Figures, velocity profiles, drag plots
│── report/                # Detailed project report (PDF/DOCX)
│── README.md              # Project documentation
```
# 🌐 Computational Domain & Mesh

- **Domain Size:** 0.1 m × 0.1 m × 0.16 m (scaled from 100 × 100 × 160 mm)  
- **Mesh Type:** Structured hexahedral grid, 70 × 70 × 100 cells  
- **Mesh Quality:** Verified in OpenFOAM (skewness, orthogonality, aspect ratio)  
- **Purpose:** Minimize wall effects while capturing flow around the settling sphere  

**Figures:**  
- Complete domain mesh  
- Refined mesh region near the sphere  

---

# ⚙️ Solver Methodology

## 1. DPMFoam
- **Type:** Lagrangian particle tracking with two-way coupling  

### Governing Equations

**Continuity equation:**
```math
∇⋅U = 0

## Momentum Equation

**Momentum equation (fluid phase):**
```math
∂U/∂t + ∇⋅(UU) = −∇p/ρ + ν∇²U + Sp/ρ

## ⚖️ Particle Motion (Newton’s Second Law)
\[
m_p \cdot \frac{dU_p}{dt} = F_g - F_d - F_b
\]

Where:  
- \(m_p\): Particle mass  
- \(U_p\): Particle velocity  
- \(F_g\): Gravitational force  
- \(F_d\): Drag force  
- \(F_b\): Buoyancy force  

**Drag Force Calculation**: Manual approach using particle acceleration and force balance.

---

## ⚙️ Solver Settings

### 🧪 DPMFoam
| Parameter        | Value             |
|------------------|-------------------|
| Time step        | 2×10⁻⁴ s         |
| End time         | 1.5 s             |
| Write interval   | Every 150 steps   |
| Coupling type    | Two-way           |
| Flow regime      | Laminar           |
| Turbulence model | None              |
| Tracking         | Lagrangian        |

---

### 🧪 overPimpleDyMFoam
- Type: **Overset mesh solver with dynamic mesh capabilities**  
- Mesh Technology: **Background + Component (overset) mesh**  
- Force Computation: **Direct using function objects (pressure & viscous contributions)**  

**Sample force calculation in `controlDict`:**
```foam
forces1
{
    type            forces;
    libs            ("libforces.so");
    writeControl    timeStep;
    timeInterval    1;
    log             true;
    patches         ("sphere");
    rhoInf          true;
    origin          (0 0 0);
    rotation
    {
        type        axes;
        e3          (0 0 1);
        e1          (1 0 0);
    }
}

## Solver Settings (overPimpleDyMFoam)

| Parameter      | Value                  |
|----------------|-----------------------|
| Time step      | 2×10⁻⁴ s (adaptive)  |
| End time       | 4.0 s                 |
| Write interval | 0.1 s                 |
| Adaptive step  | Yes                   |

---

## 🌊 Boundary Conditions

### DPMFoam
- **Velocity:** noSlip at all walls  
- **Pressure:** zeroGradient at walls, fixedValue at top  

### overPimpleDyMFoam
- **Velocity:** movingWallVelocity on sphere, fixedValue elsewhere  
- **Pressure:** zeroGradient at most boundaries, fixedValue at front/back  

---

## 🧴 Material Properties

### Particle Properties
| Property           | Value       |
|------------------|------------|
| Diameter          | 15 mm      |
| Density           | 1120 kg/m³ |
| Initial z-position| 0.12 m     |
| Initial velocity  | 0 m/s      |

### Fluid Properties
| Property             | Value            |
|---------------------|-----------------|
| Density (ρ)         | 970 kg/m³       |
| Kinematic viscosity (ν)| 3.85×10⁻⁴ m²/s|
| Dynamic viscosity (µ)| 0.37345 Pa·s   |
| Transport model      | Newtonian       |
| Turbulence model     | Laminar         |

---

## 📊 Results Summary

### DPMFoam
- **Terminal Velocity:** −0.0784 m/s  
- **Drag Force:** 0.002544 N  
- **Drag Coefficient:** 4.8319  
- **Particle Displacement:** 0.12 m  

### overPimpleDyMFoam
- **Terminal Velocity:** −0.0395 m/s  
- **Drag Force:** 0.0027829 N  
- **Drag Coefficient:** 20.7730  
- **Velocity Distribution:** Captures wake & boundary layer  

---

## 📑 Comparative Table

| Parameter               | DPMFoam    | overPimpleDyMFoam |
|-------------------------|------------|------------------|
| Terminal Velocity (m/s) | −0.0784    | −0.0395          |
| Reynolds Number         | 1.5637     | 1.540            |
| Drag Coefficient        | 4.8319     | 20.7730          |
| Terminal Drag Force (N) | 0.002544   | 0.0027829        |

**Observation:**  
- DPMFoam predicts faster settling with less drag  
- overPimpleDyMFoam predicts slower settling with higher drag  
- Reynolds numbers agree within 1.6% of Schiller–Naumann predictions  

---

## 🧾 Key Conclusions
- Both solvers effectively simulate sphere sedimentation at low Re.  
- **DPMFoam:** Efficient for multi-particle studies, lower computational cost, but requires manual force computation.  
- **overPimpleDyMFoam:** Built-in force analysis, handles moving boundaries well, but needs higher computational resources.  
- Deviations from Stokes and Schiller–Naumann correlations highlight their limits at intermediate Reynolds numbers.  

---

## 🔧 Practical Implications
- Sedimentation tank design and particle separation processes.  
- Pharmaceutical particle processing.  
- Wastewater treatment applications.  
- Solver selection depends on problem complexity, computational resources, and desired accuracy.
---

