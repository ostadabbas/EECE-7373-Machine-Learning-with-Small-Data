---
name: explorer-cluster
description: Operate Northeastern's Explorer SLURM cluster for EECE 7398 coursework -- passwordless SSH, submit/monitor/cancel SLURM jobs, sync code, connect W&B. Use whenever you (or your AI agent) need to run something "on the cluster", check job status, or debug a training run.
---

# Explorer Cluster Ops (EECE 7398)

This file is written so a coding agent (Claude Code, Codex, etc.) can read it and operate the
cluster on your behalf -- submit jobs, watch them, tail logs, and report back -- without you
typing every SLURM command by hand. It's a **template**: the pattern (connection facts, common
commands, active runs, guardrails) generalizes to any SLURM cluster (Explorer, Discovery, AICR,
a lab server), not just this course's.

## Quickstart: make this your own project's SKILL.md

1. Copy this file into your project as `.claude/skills/SKILL.md` (project-local) or
   `~/.claude/skills/<cluster-name>/SKILL.md` (global, if you use the same cluster across
   multiple projects).
2. Set up passwordless SSH once (below), then replace every `your-username`,
   `your-project`, and path placeholder in this file with your real values.
3. Fill in **Filesystem layout** and **Common operations** with your project's actual paths and
   `sbatch` script names.
4. Keep it updated as you go -- add an **Active runs** table (run name, purpose, status, key
   path) once you have real jobs going. Treat this file as required infrastructure your agent
   reads every session, not one-off documentation.
5. Point your agent at it explicitly the first time (e.g. "read `.claude/skills/SKILL.md` before
   doing anything on the cluster") -- most agents also auto-discover skills in that path.
6. There's a runnable, cell-by-cell version of the setup steps below (SSH key, `.env`,
   copying this template) in [`Exercises/agentic_cluster_workflow.ipynb`](Exercises/agentic_cluster_workflow.ipynb).

## Connection facts

- **Host:** `login.explorer.northeastern.edu`
- **Username:** your Northeastern username (e.g. `lastname.f`)
- **Auth:** set up **passwordless SSH** once (see below) so an agent never has to type a password
  or approve a 2FA prompt -- it can't do either.
- **Host alias** (add to `~/.ssh/config`):
  ```
  Host explorer
      HostName login.explorer.northeastern.edu
      User your-username
      ServerAliveInterval 60
  ```

## One-time passwordless SSH setup

```bash
# 1. generate a key (no passphrase, so it can be used unattended)
ssh-keygen -t ed25519 -N "" -f ~/.ssh/id_ed25519

# 2. install it on Explorer (one password prompt -- the last one)
ssh-copy-id -i ~/.ssh/id_ed25519.pub your-username@login.explorer.northeastern.edu

# 3. verify -- should NOT ask for anything
ssh explorer 'echo passwordless works'
```

## Secrets: `.env` + `.gitignore`

Keep any tokens (GitHub, W&B API key) in a `.env` at your project root, and add it to
`.gitignore` **before** you create it so it can never be committed:

```bash
echo ".env" >> .gitignore
cat > .env <<'EOF'
WANDB_API_KEY=...
GITHUB_TOKEN=...
EOF
```

Never print or paste `.env` contents into a chat, commit, or log.

## Filesystem layout

| Storage | Path | Use for |
|---|---|---|
| Home | `/home/$USER` | code, small files (75 GB quota) |
| Performant | `/projects/<your-lab-or-course-dir>` | datasets, checkpoints |
| Scratch | `/scratch/$USER` | temporary job data only |

## Common operations

```bash
# submit a job
ssh explorer 'cd ~/your-project && sbatch scripts/train.sbatch'

# check status
ssh explorer 'squeue -u $USER --format="%.10i %.12P %.20j %.2t %.12M %R"'

# job history
ssh explorer 'sacct -u $USER --starttime=now-2days --format=JobID,JobName,State,ExitCode,Elapsed'

# tail a running log
ssh explorer 'tail -f /scratch/$USER/your-project/logs/train_<JOBID>.out'

# cancel a job (confirm with the user first!)
ssh explorer 'scancel <JOBID>'

# sync code: push locally, pull on the cluster
git push
ssh explorer 'cd ~/your-project && git pull --ff-only'
```

## Weights & Biases

Put `WANDB_API_KEY` in `.env`, call `wandb.init(...)` / `wandb.log(...)` in your training script as
usual. An agent can then pull run status via the `wandb` CLI/API instead of you checking the
dashboard by hand, and summarize how a run is trending alongside the raw SLURM state.

## Cluster is often busy: keep jobs short + chain them

Prefer several short jobs linked with `--dependency` over one long one -- shorter requests get
scheduled sooner, and a crash only loses one stage instead of everything.

```bash
j1=$(sbatch --parsable scripts/stage1_prep.sbatch)
j2=$(sbatch --parsable --dependency=afterok:$j1 scripts/stage2_train.sbatch)
j3=$(sbatch --parsable --dependency=afterok:$j2 scripts/stage3_eval.sbatch)
```

Checkpoint your training script periodically so stage 2/3 can resume instead of restarting.

## Guardrails

- Never echo, log, or commit `.env` contents or any token.
- Always confirm with the user before `scancel`-ing a running job.
- Use `git pull --ff-only` on the cluster to avoid surprise merge commits.
- Don't run compute-heavy work directly on the login node -- always go through `sbatch`/`srun`.

## See also

- Course README and `Exercises/` notebooks in this repo (environment setup, PyTorch/GPU, W&B)
- `H200-CU128.md`, `A100-CU128.md`, `V100-CU121.md` in this repo for per-GPU environment recipes
- RC docs: https://rc-docs.northeastern.edu / rchelp@northeastern.edu
