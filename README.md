# Isaac Lab — Go2 Locomotion

[![Status: Work in Progress](https://img.shields.io/badge/status-work%20in%20progress-yellow)](https://github.com/BrandoUlissi/isaaclab-go2-locomotion)
[![Isaac Sim 4.5](https://img.shields.io/badge/Isaac%20Sim-4.5.0-76B900)](https://developer.nvidia.com/isaac-sim)
[![Isaac Lab 2.1](https://img.shields.io/badge/Isaac%20Lab-2.1.1-76B900)](https://github.com/isaac-sim/IsaacLab)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

**Reinforcement learning baseline and extensions for Unitree Go2 quadruped locomotion, built on NVIDIA Isaac Sim 4.5 and Isaac Lab 2.1.**

> **Status — Work in Progress.** Baseline velocity-tracking policy (3 training sessions documented) is complete. A push-recovery extension is currently in development. Final README, video demo, and analysis writeup will follow.

---

## Project goal

Train a Unitree Go2 quadruped to follow velocity commands `[v_x, v_y, ω_z]` via reinforcement learning (PPO), then extend the baseline with a disturbance-rejection ("push recovery") behavior. The full pipeline runs in simulation on consumer-grade hardware (laptop with RTX 4050 6 GB).

This is a **portfolio project**, not research. The goal is to validate the full Isaac Lab pipeline end-to-end (training, logging, replay, evaluation) and to demonstrate working knowledge of:
- Sim-based RL for legged locomotion (PPO with `rsl_rl`)
- Reward design and curriculum learning in a manager-based RL environment
- Sim-to-real-ready policy formulation (proprioceptive observations only)
- Engineering discipline on resource-constrained hardware

## Current status

### Baseline (complete)

Standard task `Isaac-Velocity-Flat-Unitree-Go2-v0` trained across three sessions to identify the converged policy:

| Session | Iterations | num_envs | Wall time | Final reward | Notes |
|---|---|---|---|---|---|
| 1 | 500 | 2048 | ~7 min | 34.66 | Pipeline validation run |
| 2 | 2000 | 3072 | ~31 min | **37.99** | Official baseline (highest reward) |
| 3 | 4000 | 3072 | ~58 min | 37.26 | Convergence confirmation (no further gain) |

Session 2's `model_1999.pt` is the official baseline policy. Session 3 confirmed the policy was already converged at ~2000 iterations.

**Tracking error after training** (averaged over 800 replay steps with random commands):
- Linear velocity error: 0.137 m/s
- Angular velocity error: 0.272 rad/s
- Zero falls or body-contact terminations across full replays

Learning curves are in [`docs/images/`](docs/images/). Detailed per-session metrics, hardware peaks, and anomalies are in `docs/RUN_NOTES*.md`.

**Demo video:** download the baseline replay from [Release v0.1.0-baseline](https://github.com/BrandoUlissi/isaaclab-go2-locomotion/releases/tag/v0.1.0-baseline).

### Push recovery extension (in development)

Adding an external disturbance event manager: random lateral forces applied to the robot's base during training, gated by a curriculum that grows disturbance magnitude as the policy matures. Goal: a policy that maintains velocity tracking under perturbations several times larger than the baseline tolerates.

Expected deliverables: trained policy, success-rate-vs-disturbance-magnitude curves, side-by-side video comparison.

### Out of scope (current project)

- Vision-based observations (RGB / depth / lidar) — laptop VRAM (6 GB) cannot sustain rendering-in-the-loop during PPO training
- Rough terrain training — possible future extension but distinct from push recovery
- Real-robot deployment — no physical Go2 available
- High-level navigation, SLAM, obstacle avoidance — out of scope here; planned as a follow-up project (separate repository)

## Stack

- **NVIDIA Isaac Sim 4.5.0** — physics simulation (PhysX 5) + USD scene management
- **NVIDIA Isaac Lab 2.1.1** — RL environment framework on top of Isaac Sim
- **rsl_rl 2.3.3** — PPO implementation (ETH Zurich)
- **PyTorch 2.7.0+cu128** — neural network backend
- **Python 3.10** in a Miniforge / `conda-forge` environment

## Hardware

Trained on a thin gaming laptop:
- NVIDIA RTX 4050 Laptop GPU, 6 GB VRAM, 30 W TGP
- Intel Core i7-12650H, 16 GB RAM, 16 GB swap
- Ubuntu 22.04.5 LTS, kernel 6.8 (HWE), driver 550.163.01

Sustained training at 3072 parallel environments held VRAM at 3.9 GB (64% utilization) and produced ~84,000 environment steps/second. No thermal throttling observed during 60-minute training runs.

This setup is significantly below the typical Isaac Lab recommendation (24+ GB VRAM workstation). The project documents the configuration adjustments needed to make sustained training viable on this hardware.

## Repository structure
isaaclab-go2-locomotion/
├── CLAUDE.md          # AI-assistant project context (for development sessions)
├── README.md          # This file
├── LICENSE            # MIT
├── docs/              # Per-session run notes and plots
│   ├── RUN_NOTES.md
│   ├── RUN_NOTES_session2.md
│   ├── RUN_NOTES_session3.md
│   └── images/        # Learning curves and comparison plots
├── configs/           # YAML configs for custom scripts (currently empty)
├── scripts/           # Executable training / play / evaluation scripts (in development)
├── src/               # Python modules for custom tasks and utilities (in development)
├── notebooks/         # Analysis notebooks (in development)
└── logs/              # Training artifacts (gitignored; regenerable)

Custom code for the push-recovery extension will populate `scripts/` and `src/` in the next phase.

## Reproducibility

Baseline training runs used:
- Task: `Isaac-Velocity-Flat-Unitree-Go2-v0` (standard Isaac Lab manager-based task, unmodified)
- Algorithm: PPO via `rsl_rl`, default hyperparameters from Isaac Lab 2.1.1
- Seed: 42

A reproduction guide (full command, environment versions, expected wall time) will be added once the push-recovery extension lands. The current `docs/RUN_NOTES*.md` files contain the exact commands used in each session.

## Author

**Brando Ulissi** — M.Sc. Automation Engineering, University of Bologna

This project is part of a personal effort to add hands-on reinforcement learning to a control-engineering profile.

LinkedIn: [brando-ulissi](https://www.linkedin.com/in/brando-ulissi)

## License

MIT. See [LICENSE](LICENSE).

## Acknowledgments

- NVIDIA Isaac Lab and Isaac Sim teams
- The `rsl_rl` authors at ETH Zurich Robotic Systems Lab
- The Unitree Go2 USD model shipped with Isaac Lab
