# Franka Robot Manipulation with Isaac Lab

<div align="center">

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![Isaac Lab](https://img.shields.io/badge/Isaac%20Lab-Latest-green.svg)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-ee4c2c.svg)
![License](https://img.shields.io/badge/license-BSD--3-blue.svg)

*A comprehensive reinforcement learning project for robotic manipulation using the Franka Emika Panda robot*

[Features](#-features) • [Installation](#-installation) • [Usage](#-usage) • [Tasks](#-tasks) • [Results](#-results)

</div>

---

## 🎯 Project Overview

This project implements three progressive manipulation tasks for the **Franka Emika Panda** robot using **Deep Reinforcement Learning** in NVIDIA Isaac Lab. The tasks increase in complexity, demonstrating the robot's ability to perform increasingly sophisticated manipulation skills.

### Implemented Tasks

| Task | Description | Complexity | Status |
|------|-------------|------------|--------|
| **Reach** | Navigate end-effector to target positions in 3D space | ⭐ Basic | ✅ Completed |
| **Lift** | Grasp and lift a cube to a target height | ⭐⭐ Intermediate | ✅ Completed |
| **Stack** | Pick and stack multiple cubes on top of each other | ⭐⭐⭐ Advanced | ✅ Completed |

## ✨ Features

- **🤖 Custom Robot Environments**: Tailored configurations for Franka Panda robot
- **🎓 Progressive Learning**: Three tasks with increasing difficulty
- **🏆 Reward Engineering**: Carefully designed reward functions with stage-based curriculum
- **💾 Checkpoint Management**: Automatic saving of best performing agents
- **📊 TensorBoard Integration**: Real-time training monitoring
- **⚡ GPU-Accelerated**: Leveraging NVIDIA Isaac Sim's physics engine
- **🔧 Modular Design**: Clean separation of MDPs (observations, rewards, terminations)

## 🛠️ Installation

### Prerequisites

- **NVIDIA Isaac Lab** (see [installation guide](https://isaac-sim.github.io/IsaacLab/main/source/setup/installation/index.html))
- **Python** 3.8 or higher
- **CUDA-capable GPU** (recommended: RTX 3060 or better)
- **Ubuntu** 20.04+ or equivalent Linux distribution

### Setup Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/TruongThinhDang/franka_isaaclab.git
   cd franka_isaaclab
   ```

2. **Install the package**
   ```bash
   # If using Isaac Lab's conda environment
   python -m pip install -e source/franka_isaaclab
   
   # Or using Isaac Lab's shell wrapper
   # ./isaaclab.sh -p -m pip install -e source/franka_isaaclab
   ```

3. **Verify installation**
   ```bash
   python scripts/list_envs.py
   ```

## 🎮 Usage

### Training

Train different tasks using the SKRL PPO implementation:

```bash
# Train the Reach task
python scripts/skrl/train.py --task=Template-Reach-v0

# Train the Lift task
python scripts/skrl/train.py --task=Template-Lift-v0

# Train the Stack task
python scripts/skrl/train.py --task=Template-Stack-v0
```

**Training arguments:**
- `--task`: Environment ID to train
- `--num_envs`: Number of parallel environments (default: varies by task)
- `--seed`: Random seed for reproducibility

### Inference

Test trained models in play mode:

```bash
# Play with trained Lift model
python scripts/skrl/play.py --task=Template-Lift-Play-v0 \
    --checkpoint=logs/skrl/franka_lift/*/checkpoints/best_agent.pt

# Play with trained Stack model
python scripts/skrl/play.py --task=Template-Stack-Play-v0 \
    --checkpoint=logs/skrl/franka_stack/*/checkpoints/best_agent.pt
```

### Environment Testing

Test environments with dummy agents:

```bash
# Test with random actions
python scripts/random_agent.py --task=Template-Lift-v0 --num_envs=16

# Test with zero actions (useful for debugging)
python scripts/zero_agent.py --task=Template-Lift-v0 --num_envs=16
```

## 📚 Tasks

### 1. Reach Task (`Template-Reach-v0`)

**Objective:** Move the end-effector to a randomly placed target position.

- **Observation Space:** 24 dimensions (joint positions/velocities, end-effector pose, target position)
- **Action Space:** 7 dimensions (joint position commands)
- **Reward Components:**
  - Distance to target (tanh-kernel)
  - Action penalties
- **Training Time:** ~24,000 timesteps

### 2. Lift Task (`Template-Lift-v0`)

**Objective:** Grasp a cube and lift it above a target height.

- **Observation Space:** Joint states + cube position/orientation + end-effector pose
- **Action Space:** 7 joint positions + gripper command
- **Reward Components:**
  - End-effector to object distance
  - Object lift height reward
  - Grasp stability bonus
  - Action smoothness penalty
- **Training Time:** ~36,000 timesteps
- **Success Criteria:** Cube lifted > 0.5m for sustained period

### 3. Stack Task (`Template-Stack-v0`)

**Objective:** Pick up cube 1 and stack it on top of cube 2.

- **Observation Space:** Joint states + 2 cube poses + end-effector pose
- **Action Space:** 7 joint positions + gripper command
- **Multi-Stage Reward System:**
  1. **Stage 1:** Reach cube 1
  2. **Stage 2:** Grasp and lift cube 1
  3. **Stage 3:** Move cube 1 towards cube 2
  4. **Stage 4:** Align and stack cubes
  5. **Stage 5:** Release and verify stack stability
- **Training Time:** ~36,000+ timesteps
- **Advanced Features:**
  - Custom event handlers for grasp detection
  - Stage-based curriculum learning
  - Stack stability verification

## 📊 Results

### Training Performance

| Task | Total Steps | Best Reward | Success Rate | Checkpoint |
|------|-------------|-------------|--------------|------------|
| Reach | 24,000 | N/A | High | ✅ Available |
| Lift | 36,000 | N/A | High | ✅ Available |
| Stack | 36,000+ | N/A | Progressive | ✅ Available |

### Trained Models

Pre-trained checkpoints are available in `logs/skrl/`:

```
logs/skrl/
├── reach_franka/2025-12-06_19-58-37_ppo_torch/
│   └── checkpoints/best_agent.pt
├── franka_lift/2025-12-07_19-34-59_ppo_torch/
│   └── checkpoints/best_agent.pt
└── (Additional training runs...)
```

## 🏗️ Project Structure

```
franka_isaaclab/
├── source/franka_isaaclab/
│   └── franka_isaaclab/
│       ├── __init__.py
│       ├── assets/
│       │   └── robots/
│       │       └── franka.py          # Franka robot configuration
│       └── tasks/
│           └── manager_based/
│               ├── reach/             # Reach task
│               │   ├── joint_pos_env_cfg.py
│               │   ├── reach_env_cfg.py
│               │   ├── agents/
│               │   │   └── skrl_ppo_cfg.yaml
│               │   └── mdp/
│               │       └── rewards.py
│               ├── lift/              # Lift task
│               │   ├── joint_pos_env_cfg.py
│               │   ├── lift_env_cfg.py
│               │   ├── agents/
│               │   │   └── skrl_ppo_cfg.yaml
│               │   └── mdp/
│               │       ├── observations.py
│               │       ├── rewards.py
│               │       └── terminations.py
│               └── stack/             # Stack task
│                   ├── stack_joint_pos_env_cfg.py
│                   ├── stack_env_cfg.py
│                   ├── agents/
│                   │   └── skrl_ppo_cfg.yaml
│                   └── mdp/
│                       ├── franka_stack_events.py
│                       ├── observations.py
│                       ├── rewards.py
│                       └── terminations.py
├── scripts/
│   ├── list_envs.py
│   ├── random_agent.py
│   ├── zero_agent.py
│   └── skrl/
│       ├── train.py
│       └── play.py
├── logs/                              # Training logs and checkpoints
├── outputs/                           # Hydra outputs
├── .gitignore
└── README.md
```

## 🔬 Technical Details

### MDP Components

Each task is decomposed into modular MDP components:

- **Observations** (`mdp/observations.py`): Custom observation functions
- **Rewards** (`mdp/rewards.py`): Multi-stage reward shaping
- **Terminations** (`mdp/terminations.py`): Success/failure conditions
- **Events** (`mdp/*_events.py`): Custom event handlers (e.g., grasp detection)

### PPO Configuration

Training uses Proximal Policy Optimization (PPO) via SKRL:

- **Policy Network:** Multi-layer perceptron (configurable)
- **Learning Rate:** Adaptive with scheduler
- **Batch Size:** Optimized per task
- **Parallel Environments:** 16-64 environments simultaneously
- **GPU Acceleration:** Full physics and neural network on GPU

## 🎓 Key Learning Outcomes

This project demonstrates:

1. ✅ **Custom RL Environment Design** in Isaac Lab framework
2. ✅ **Reward Engineering** for complex manipulation tasks
3. ✅ **Curriculum Learning** with progressive task difficulty
4. ✅ **Multi-Stage Task Decomposition** for the stacking problem
5. ✅ **Integration** with modern RL libraries (SKRL, Gymnasium)
6. ✅ **GPU-Accelerated Simulation** for fast training
7. ✅ **Checkpoint Management** and model versioning

## 🔧 Customization

### Creating New Tasks

1. Create new task directory in `source/franka_isaaclab/franka_isaaclab/tasks/manager_based/`
2. Define environment configuration (`*_env_cfg.py`)
3. Implement MDP components in `mdp/` directory
4. Register environment in `__init__.py`
5. Add PPO configuration in `agents/skrl_ppo_cfg.yaml`

### Modifying Rewards

Edit reward functions in `mdp/rewards.py` for each task. All rewards use the Isaac Lab manager-based interface.

## 📝 License

This project is licensed under the BSD-3-Clause License - see the LICENSE file for details.

## 🙏 Acknowledgments

- Built on [NVIDIA Isaac Lab](https://github.com/isaac-sim/IsaacLab)
- Reinforcement learning via [SKRL](https://github.com/Toni-SM/skrl)
- Inspired by robotic manipulation research and IsaacLab tutorials

## 📧 Contact

**Truong Thinh Dang**

- GitHub: [@TruongThinhDang](https://github.com/TruongThinhDang)
- Project Link: [https://github.com/TruongThinhDang/franka_isaaclab](https://github.com/TruongThinhDang/franka_isaaclab)

---

<div align="center">

**⭐ If you find this project useful, please consider giving it a star! ⭐**

</div>
