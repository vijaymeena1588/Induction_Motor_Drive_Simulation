# Simulink Induction Motor Drive Simulation

This repository contains a Simulink model for a detailed simulation of an induction motor drive. The project is designed to demonstrate the behavior and control of a three-phase induction motor using a modern **Field-Oriented Control (FOC)** or **Vector Control** strategy. This simulation is a valuable tool for designing, analyzing, and testing motor control systems.

---

### Project Overview

The core purpose of this model is to simulate the complete motor drive system, from the power supply and conversion to the motor's mechanical output and a responsive closed-loop control system. The simulation is structured to show the performance of the drive under a mechanical load and how the FOC controller manages the motor's speed and torque.

---

### System Architecture

The top-level Simulink model is composed of several key blocks that represent the physical components of the motor drive system:

* **Continuous block:** Handles the simulation's time-stepping and continuous-time mode, which is essential for modeling dynamic electrical systems.
* **Main circuit block:** Represents the power stage of the drive, likely a DC voltage source, which provides power to the inverter.
* **PWM Inverter block:** A **Pulse Width Modulation (PWM) inverter** that converts DC power into variable-frequency, variable-voltage AC power to drive the motor.
* **Induction machine model block:** A three-phase induction machine model that simulates the motor's electromechanical behavior. It takes AC power and a mechanical load and outputs key performance metrics.
* **Load block:** Simulates the **mechanical load** connected to the motor shaft, configured as a step function to test the drive's dynamic response.
* **Scopes and Displays:** Used to visualize and analyze the simulation results, such as motor currents, speed, and torque.
* **Control Subsystem:** The "brain" of the drive, implementing the Field-Oriented Control algorithm.

---

### Core Component Breakdown

The following sections provide a more detailed look into the main subsystems of the model.

#### PWM Inverter
The **PWM Inverter** is a critical power electronics component that bridges the DC power source and the AC motor.
* **Control Logic:** Takes reference signals from the FOC controller and generates six gating pulses to control the inverter's switches.
* **Inverter Bridge:** Consists of six power electronic switches (e.g., IGBTs) in a three-phase configuration that chop the DC voltage into a series of pulses.
* **Output Stage:** An **LC filter** smooths the pulsed output of the inverter bridge into a cleaner, near-sinusoidal three-phase AC voltage waveform.

#### Induction Machine Model
This subsystem is the core of the motor simulation, where the complex three-phase AC behavior is simplified using a two-axis rotating reference frame. This approach allows the motor's dynamics to be represented by **DC quantities** in a steady state, which is much simpler to control.
* **Park Transform:** A mathematical tool that converts three-phase AC voltages into two DC components: the d-axis voltage ($V_d$) and q-axis voltage ($V_q$). This is the foundation of FOC.
* **Machine Model Circuit:** Implements the fundamental dynamic equations of the induction motor in the d-q rotating frame. It takes voltages from the Park transform and calculates resulting currents, electromagnetic torque, and rotor speed.
* **Inverse Park Transform:** Converts the d-q axis currents back into the three-phase currents that are used by a real-world motor.

#### Field-Oriented Control (FOC) Subsystem
The FOC subsystem implements a **cascaded control loop** for high-performance operation.
* **Outer Speed Control Loop:** Compares a speed reference with the actual motor speed. A PI controller processes the error and outputs a **reference for the q-axis current ($I_q^*$)**, which is directly proportional to torque.
* **Inner Current Control Loop:** A faster loop that compares the actual d-q currents with their references. The errors are processed by two separate PI controllers, which output the **d-q axis voltage commands ($V_d$ and $V_q$)**.
* **Flux Controller:** A key part of the FOC system that sets the reference value for the **d-axis current ($I_d^*$)**. This controller ensures the motor operates in a **constant flux region** (below base speed) and a **field-weakening region** (above base speed) to extend its operating range.

#### Load Block
The Load block is configured as a **step function** to test the motor drive's transient response. It applies a sudden mechanical load to the motor shaft at a specified time, and the control system must respond by adjusting the motor's torque to maintain the desired speed.

---

### Simulation Results and Analysis

The simulation plots provide a complete picture of the motor's performance.

* **Three-Phase Currents ($I_{abc}$):** Shows a large inrush current spike at the start, followed by a stable, sinusoidal waveform in steady-state operation.
* **Motor Speed (RPM):** Starts at zero, accelerates rapidly, and then settles to a constant speed, demonstrating effective speed control.
* **Electromagnetic Torque ($N_m$):** A large peak starting torque is visible for acceleration, which then settles to a smaller, constant value that matches the mechanical load.
* **D-Q Currents ($I_{qd}$):**
    * **Q-axis current ($I_q$)**: Shows a high positive peak for starting torque, then settles to a constant value proportional to the steady-state load.
    * **D-axis current ($I_d$)**: Settles to a constant value, confirming that the motor's magnetic flux is being held constant, which is a key principle of FOC.

This project demonstrates a well-structured simulation environment for studying the behavior of an induction motor under a modern control scheme. It allows users to tune parameters and observe the effects on motor performance, a fundamental step in designing and validating motor control systems.
