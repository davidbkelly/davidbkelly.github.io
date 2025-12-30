---
title: "From Windkessels to Closed-Loop Circulation: Building and Verifying 0D Models for Cardiac Simulation in OpenFOAM"
date: 2025-12-30
permalink: /posts/2012/08/from-windkessels-to-closed-loop-circulation-building-and-verifying-0d-models-for-cardiac-simulation/
tags:
  - OpenFOAM
  - cardiovascular-modelling
  - lumped-parameter-models
  - windkessel-model
  - 0D-3D-coupling
  - closed-loop-simulation
---

Zero-dimensional (0D) lumped-parameter models play a central role in cardiovascular modelling by capturing essential pressure–flow–volume relationships at low computational cost. Although they lack spatial resolution, their numerical efficiency and physiological interpretability make them particularly well suited for coupling with high-fidelity three-dimensional (3D) simulations.

In this post, I describe my recent work developing and verifying Windkessel-based 0D models, and how these components are assembled into a minimal closed-loop system that can be coupled to a 3D left ventricle model in OpenFOAM.

## 0D models and Windkessels in cardiovascular simulation

Windkessel models are among the most widely used 0D representations of the vasculature. By combining resistive and capacitive elements, they approximate the downstream circulation seen by the heart and capture key haemodynamic effects such as vascular resistance, arterial compliance, and pressure storage.

Two- and three-element Windkessels are commonly used as outlet boundary conditions in cardiac and vascular simulations. Their simplicity makes them ideal for providing physiologically meaningful afterload in coupled 0D–3D frameworks without introducing unnecessary numerical complexity.

Beyond simple Windkessel representations, full cardiac 0D lumped-parameter models extend this concept to describe the entire circulation, including cardiac chambers, valves, and systemic and pulmonary vascular beds. These models represent the heart and vessels as interconnected networks of resistances, compliances, and inertial elements, allowing pressure, flow, and volume to evolve dynamically throughout the cardiac cycle. Compared to standalone Windkessels, full 0D systems capture preload, afterload, and ventricular–vascular interaction in a unified framework, making them particularly valuable for studying global haemodynamic behaviour and closed-loop feedback mechanisms.

![Full cardiac 0D lumped-parameter system](/images/LPM_System.png)

## Verifying a three-element Windkessel (WK3)

Before deploying a Windkessel model in a coupled setting, it is essential to verify its behaviour in isolation. To this end, a three-element Windkessel (WK3) outlet boundary condition was implemented and tested using a simple benchmark case: a straight, rigid cylindrical pipe with a prescribed sinusoidal inflow.

Because the governing equation of the WK3 model admits a closed-form analytical solution, this setup provides a clear verification test. The numerical outlet pressure computed in OpenFOAM was compared directly against the analytical solution, capturing both the transient response and the steady-state periodic behaviour.

![WK3 verification: analytical vs OpenFOAM pressure](/images/WK3%20Verification.png)

The excellent agreement between numerical and analytical results confirmed the correctness of the governing equations, discretisation, and boundary-condition implementation.

## A minimal closed-loop system: compliance and resistance

With the WK3 boundary condition verified, the next step was to introduce feedback by constructing a minimal closed-loop circulation. This closed loop consists of a compliance chamber and a resistive element, forming a reduced two-element Windkessel configuration operating in closed loop.

In the current implementation, the 0D model prescribes an inlet flow rate and an outlet pressure that are applied as boundary conditions in the 3D OpenFOAM simulation. The 3D model then computes the corresponding inlet pressure and outlet flow rate, which are fed back to update the 0D system at the next time step. This establishes a fully bidirectional coupling between the 0D and 3D domains.

![Toy closed-loop compliance–resistor system](/images/2WK_LV_Eqns.png)

Although deliberately simple, this configuration allows the numerical stability and physical consistency of the closed-loop coupling to be examined in a controlled setting.

## Verifying the closed loop: volume conservation

A key requirement of any closed-loop cardiovascular model is consistency between pressure, flow, and volume. To verify this, volume conservation was assessed by directly comparing the change in volume of the 3D left ventricle with the corresponding volume change of the 0D compliance chamber.

This comparison provides a clear and physically meaningful check that the volume exchanged between the 3D and 0D components is consistent across the coupling interface, and that no artificial gain or loss of volume is introduced by the numerical scheme.

![Volume conservation: 3D left ventricle vs 0D compliance chamber](/images/volume_change_checkMesh_vs_compliance.png)

Across parameter variations, excellent agreement was observed, providing strong confidence that the closed-loop system is numerically stable and conserves volume across the 0D–3D interface.

## Next steps: towards a fully closed circulation

Having established and verified this minimal closed-loop system, the next step is to extend the framework to a more complete circulation. This involves adding further systemic and pulmonary components and embedding the 3D ventricle within a fully closed-loop lumped-parameter network.

![Towards a fully closed 0D–3D circulation](/images/LPM_LV_image.png)

By building the system incrementally—verifying individual components, introducing closed-loop feedback, and then expanding the network—the aim is to develop a robust and extensible 0D–3D coupling framework suitable for more realistic cardiac simulations.

---

## Contact and further information

If you’d like more details about this work, or are interested in discussing 0D–3D coupling, Windkessel models, or cardiovascular simulation in OpenFOAM, feel free to get in touch.

- 📧 **Email:** [david.kelly15@ucdconnect.ie](mailto:david.kelly15@ucdconnect.ie)
- 🔗 **LinkedIn:** [linkedin.com/in/david-kelly](https://www.linkedin.com/in/david-kelly/)

I’m always happy to chat about modelling approaches, numerical verification, or potential collaborations.
