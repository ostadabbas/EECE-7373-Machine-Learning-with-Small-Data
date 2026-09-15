# EECE 7373: Machine Learning with Small Data

## Leveraging the Explorer Cluster -- Now With Agentic Workflows

### Instructor: Prof. Sarah Ostadabbas
#### Northeastern University, Department of Electrical and Computer Engineering

---

## Overview

This repository contains the materials for **EECE 7373: Machine Learning with Small Data**. The
course uses Northeastern's **Explorer Cluster** (Rocky Linux 9.3, NVIDIA H200 GPUs) for
hands-on ML experiments, and increasingly expects students to operate that cluster together with
an AI coding agent (Claude Code, Codex, etc.) rather than by typing every command by hand.

Topics covered:

- Setting up a Python environment (venv or conda) on Explorer
- Requesting GPU resources interactively (`srun`) and via batch jobs (`sbatch`)
- Monitoring and managing SLURM jobs
- Experiment tracking with Weights & Biases
- **Agentic cluster workflows**: passwordless SSH, secrets management, `SKILL.md`, and having an
  agent submit/monitor jobs for you

---

## Contents

- **`SKILL.md`** -- a generic, copy-and-adapt template for how an AI agent operates a SLURM
  cluster on your behalf. Read this first if you want an agent to help you on Explorer.
- **`Slides/`** -- lecture PDFs
- **`Exercises/`** -- hands-on notebooks, roughly in the order you'd work through them:
  - **Environment setup**
    - `conda_environment_setup_part1.ipynb` -- Explorer environment setup; shows **venv (recommended)
      and conda** side by side, current CUDA/PyTorch versions
    - `local_environment_setup.ipynb` / `interactive_local_setup.ipynb` -- platform-aware setup for
      your own laptop (Windows/macOS/Linux)
    - `interactive_pytorch_mps.ipynb` -- Apple Silicon (MPS) setup for local development
  - **Agentic cluster workflow (new)**
    - `agentic_cluster_workflow.ipynb` -- generate an SSH key, set up passwordless access, create a
      gitignored `.env`, copy/adapt `SKILL.md`, and run an agent-style submit/poll/tail/report loop
  - **Requesting compute**
    - `srun_interactive_gpu_guide.ipynb` -- interactive GPU sessions (incl. an H200 example)
    - `sbatch_job_submission_guide.ipynb` -- batch jobs, job arrays, parameter sweeps, and
      **chaining short jobs with `--dependency`** (the cluster is often busy -- prefer this over one
      long job)
    - `job_monitoring_management_guide.ipynb` -- `squeue`/`sacct`/`seff`, job control
  - **PyTorch and GPU training**
    - `interactive_pytorch_gpu.ipynb`, `pytorch_gpu_training_part2.ipynb`
  - **CIFAR-10 tutorials**
    - `interactive_cifar10.ipynb`, `cifar10_classification_assignment.ipynb`,
      `cifar10_training_inference_part3.ipynb`, `cifar10_training_inference.py`
  - **Weights & Biases**
    - `interactive_wandb_tutorial.ipynb`, `wandb_monitoring_part3.ipynb`,
      `Intro_to_Weights_&_Biases.ipynb`, `wandb_monitoring_script.py`
- **`DDC-Mini-Project/`** -- transfer-learning mini-project (`ddc_transfer_learning_assignment.ipynb`)
- **`H200-CU128.md`, `A100-CU128.md`, `V100-CU121.md`** -- per-GPU environment install recipes
- **`README.md`** -- this file

> A few W&B and CIFAR-10 notebooks overlap in content (multiple intros to the same basics) --
> that's a known consolidation candidate, not a bug; use whichever one your session references.

---

## Prerequisites

1. **Access to the Explorer Cluster**: [Request access through ServiceNow](https://rc.northeastern.edu/getting-started/)
2. Basic knowledge of **Python** and **machine learning**
3. An AI coding agent (Claude Code or Codex) if you want to follow the agentic workflow --
   otherwise every step also works run by hand

---

## How to Use

1. Clone the repository:
    ```bash
    git clone https://github.com/ostadabbas/EECE-7398-Machine-Learning-with-Small-Data.git
    ```

2. Set up passwordless SSH and read [`SKILL.md`](SKILL.md) -- this is the fastest path whether
   you're working by hand or with an agent (`Exercises/agentic_cluster_workflow.ipynb` walks
   through it cell by cell).

3. Choose your environment path in `Exercises/conda_environment_setup_part1.ipynb` -- **venv is
   recommended** (simpler, faster, easier for an agent to manage), conda is shown as an
   alternative.

4. Work through the exercises in the order listed above: environment $\to$ agentic setup
   $\to$ requesting compute $\to$ PyTorch/GPU $\to$ CIFAR-10 $\to$ W&B.

5. If your project involves the FiDIP case study (a separate project repo), see that project's
   `STUDENT_GUIDE.md` for the required agentic-research artifacts (`SKILL.md`, `AGENT.md`,
   `MANIFEST.md`) -- the same `SKILL.md` pattern from this repo applies there too.

---

## Additional Resources

- [RC Explorer Cluster Documentation](https://rc-docs.northeastern.edu)
- [Conda Documentation](https://docs.conda.io/projects/conda/en/latest/)
- [Python `venv` Documentation](https://docs.python.org/3/library/venv.html)
- [PyTorch Documentation](https://pytorch.org/docs/stable/)
- [Weights and Biases Documentation](https://docs.wandb.ai/)

For any issues or questions, reach out to the Northeastern RC support team via
[ServiceNow](https://rc.northeastern.edu/help/support/), or during office hours.

---

## License

This repository is licensed under the MIT License.
