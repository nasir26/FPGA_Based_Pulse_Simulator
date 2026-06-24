# FPGA-Based Quantum Pulse Simulator

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

High-fidelity time-domain quantum pulse simulator accelerated on FPGA,
solving the Lindblad master equation for open quantum systems under
microwave drive.

**Author**: Nasir Ali — Centre for Development of Advanced Computing (C-DAC), Noida

## Overview

Gate-level quantum simulation is insufficient for modelling realistic qubit
hardware. Pulse-level simulation solves the time-dependent Schrödinger /
Lindblad equation directly:

$$\dot{\rho} = -\frac{i}{\hbar}[H(t), \rho] + \sum_k \gamma_k \mathcal{D}[L_k]\rho$$

This FPGA kernel integrates the equation using a 4th-order Magnus expansion
or Runge-Kutta-4 at each time step.

## Architecture

```
Xilinx Alveo U55C
  ├── Hamiltonian engine: H(t) = H_drift + ∑ Ω_k(t)·H_k
  ├── Runge-Kutta-4 stepper (II=4 pipeline)
  ├── Lindblad collapse operator application
  └── HBM2: density matrix storage (d² × 16 B, complex128)
```

## Supported Noise Channels

- T1 relaxation (amplitude damping)
- T2 dephasing (phase damping)
- Cross-talk (parasitic ZZ coupling)
- Control noise (pulse distortion)

## Requirements

```bash
source /opt/xilinx/xrt/setup.sh
pip install numpy scipy qiskit-dynamics
```

## Usage

```python
from fpga_pulse_sim import FPGAPulseSim, DriveChannel, Gaussian

sim = FPGAPulseSim(n_qubits=5, T1=100e-6, T2=80e-6)
schedule = sim.build_x_gate(qubit=0, amp=0.1, duration=160)
result = sim.run(schedule, shots=8192)
print(result.get_counts())
```

---

## Citation

If you use this work in your research, please cite:

```bibtex
@misc{nasirali_fpga_based_pulse_sim,
  author    = {Nasir Ali},
  title     = {FPGA Based Pulse Simulator},
  year      = {2026},
  publisher = {GitHub},
  url       = {https://github.com/nasir26/FPGA_Based_Pulse_Simulator},
  note      = {Centre for Development of Advanced Computing (C-DAC), Noida, India}
}
```

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.\
© 2026 Nasir Ali, C-DAC Noida. All rights reserved.
