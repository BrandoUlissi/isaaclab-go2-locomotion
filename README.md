# Isaac Lab Go2 Locomotion

Reinforcement learning training pipeline for quadruped locomotion on the Unitree Go2 robot, built on NVIDIA Isaac Sim 4.5 and Isaac Lab 2.1.

**Status**: Work in progress. Setup phase complete, training underway.

## Hardware tested on

- NVIDIA RTX 4050 Laptop GPU (6 GB VRAM)
- Ubuntu 22.04 LTS
- Driver 550.163.01, PyTorch 2.7.0+cu128

## Stack

- Isaac Sim 4.5.0
- Isaac Lab 2.1.1
- rsl_rl 2.3.3 (PPO implementation)
- Python 3.10 (Miniforge conda environment)

## Project structure
isaaclab-go2-locomotion/
├── src/             # Python source (custom tasks, utilities)
├── scripts/         # Training, evaluation, inference scripts
├── configs/         # YAML configuration files
├── logs/            # Training logs and videos (gitignored, regenerable)
├── docs/            # Screenshots, plots, diagrams
└── notebooks/       # Analysis notebooks

## Author

Brando Ulissi — M.Sc. Automation Engineering, University of Bologna.

## License

MIT. See LICENSE file.
