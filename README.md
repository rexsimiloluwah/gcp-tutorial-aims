# A Practical Guide to Running AI/ML Experiments on GCP

A hands-on tutorial for beginners who want to use Google Cloud Platform (GCP) for large-scale machine learning experiments or AI workloads.

## 📑 Table of Contents

- [🎯 What You'll Learn](#-what-youll-learn)
- [📋 Prerequisites](#-prerequisites)
- [🕥 Duration](#-duration)
- [📚 Tutorial Structure](#-tutorial-structure)
  - [Core Path](#core-path)
  - [Appendix (Optional)](#appendix-optional)
- [💡 What Makes This Tutorial Different](#-what-makes-this-tutorial-different)
- [🎓 Who This is For](#-who-this-is-for)
- [🧠 How to Use This Tutorial](#-how-to-use-this-tutorial)
- [🛠️ Common Issues](#️-common-issues)
  - [GPU quota exceeded](#gpu-quota-exceeded)
  - [Resource not available in zone](#resource-not-available-in-zone)
  - [Invalid image / image family not found](#invalid-image--image-family-not-found)
  - [Permission denied / API not enabled](#permission-denied--api-not-enabled)
  - [`gcloud` uses the wrong project or account](#gcloud-uses-the-wrong-project-or-account)
  - [SSH into VM fails](#ssh-into-vm-fails)
  - [CUDA / GPU drivers not found on VM](#cuda--gpu-drivers-not-found-on-vm)
  - [Out-of-memory (OOM) errors during training](#out-of-memory-oom-errors-during-training)
  - [Unexpected costs / forgot to shut down a VM](#unexpected-costs--forgot-to-shut-down-a-vm)
- [🫱🏿‍🫲🏾 Contributing](#-contributing)
- [📄 License](#-license)

## 🎯 What You'll Learn

- How to use the GCP console and the `gcloud` CLI
- How to provision and manage virtual machines and GPU resources on GCP
- How to run large-scale ML experiments (like fine-tuning large language models) on GCP VMs
- When to use virtual machines vs. managed services like Vertex AI
- How to predict and manage GPU memory
- How to avoid expensive cloud mistakes with cost monitoring and budget alerts

## 📋 Prerequisites

- Required:
  - Python programming
  - Comfort with terminal/command line
  - Basic knowledge of machine learning/deep learning
- Not required:
  - GCP experience (we start from scratch!)
  - Deep learning expertise
  - Cloud computing background
- You will also need:
  - A Google Cloud account (there is a free tier available with $300 credits)
  - A text editor (VS Code, Sublime Text, Cursor, etc.)
  - Terminal access

## 🕥 Duration

4-6 hours (can be split into multiple sessions)

- Core tutorial: 3-4 hours
- Optional appendices: 25-30 minutes additional

## 📚 Tutorial Structure

### Core Path

| Part | Title | Time | Learning Objective |
|---|---|---|---|
| 1 | [GCP Foundations](part1-gcp-foundations/index.md) | 30 mins | Understand GCP and interact with it via the console and `gcloud` CLI |
| 2 | [Running Experiments on GCP VMs](part2-running-experiments-on-gcp-vms/index.md) | 45 mins | Learn how to run AI/ML experiments on GCP virtual machines with GPU support |
| 3 | [Vertex AI](part3-vertex-ai/index.md) | 65 mins | Understand how to use the managed Vertex AI service for training jobs and notebooks |
| 4 | [Cost Management](part4-cost-management/index.md) | 30 mins | Understand budget alerts and best practices for cost management |

### Appendix (Optional)

| Appendix | Title | Time | Focus |
|---|---|---|---|
| A | GPU Memory Maths | 45 mins | Understand how to estimate GPU memory requirements, avoid OOM errors, and choose the right GPU for your needs |

## 💡 What Makes This Tutorial Different

This tutorial teaches you what you need to run AI/ML experiments on GCP, emphasizing:

- Systems thinking for ML infrastructure
- Cost-conscious cloud practices
- How to structure reproducible AI/ML experiments for real-world projects
- Hands-on practice with every learned concept

The content is also structured to be used on-demand so you do not have to read the entire tutorial.

## 🎓 Who This is For

This tutorial was particularly designed for **AIMS AI for Science students** who do not have a background in cloud computing or software engineering, helping them use GCP more effectively for their research projects.

However, it should be generally helpful for:

- Beginner AI/ML students and researchers
- Anyone curious about using GCP for running AI/ML experiments

## 🧠 How to Use This Tutorial

### Option 1: Linear (Recommended for beginners)

Start from Part 1 and work through sequentially.

### Option 2: Jump to What You Need

- Need a quick-start guide to using GCP? → **[Part 1](part1-gcp-foundations/index.md)**
- Want to run ML experiments on a GPU VM? → **[Part 2](part2-running-experiments-on-gcp-vms/index.md)**
- Want to learn how to use managed services like Vertex AI? → **[Part 3](part3-vertex-ai/index.md)**
- Need to understand how to monitor costs to manage your GCP credits properly? → **[Part 4](part4-cost-management/index.md)**

## 🛠️ Common Issues

A few issues you're likely to hit while following this tutorial, and how to fix them.

### GPU quota exceeded

When creating a GPU VM, you may see an error like:
`Quota 'NVIDIA_T4_GPUS' exceeded. Limit: 0.0 in region ...`

New GCP accounts have a default GPU quota of `0`. You need to request a quota increase:

- Go to **IAM & Admin → Quotas & System Limits** in the GCP console.
- Filter by the GPU type you want (e.g. `NVIDIA T4 GPUs`) and the region.
- Select the quota and click **Edit Quotas** to request an increase (usually 1 is enough to start).
- Approval can take a few minutes to a few hours.

### Resource not available in zone

Error like:
`The zone 'projects/.../zones/europe-west4-a' does not have enough resources available to fulfill the request.`

GPUs (especially A100s, H100s, L4s) are not available in every zone, and popular zones run out of capacity. Try:

- A different zone in the same region (e.g. `europe-west4-b`, `europe-west4-c`).
- A different region entirely.
- List zones where a given GPU type is available:
  ```bash
  gcloud compute accelerator-types list --filter="name:nvidia-tesla-t4"
  ```

### Invalid image / image family not found

Error like:
`Could not fetch image resource: The resource 'projects/.../global/images/family/...' was not found.`

Image family names change over time. List the available families for a project before creating an instance:

```bash
# Deep Learning VM images
gcloud compute images list --project deeplearning-platform-release --filter="family:*"

# Standard Debian/Ubuntu images
gcloud compute images list --filter="family:debian-12"
```

Then pass the correct `--image-family` and `--image-project` to `gcloud compute instances create`.

### Permission denied / API not enabled

Error like:
`Compute Engine API has not been used in project ... before or it is disabled.`

Enable the required APIs for your project:

```bash
gcloud services enable compute.googleapis.com
gcloud services enable aiplatform.googleapis.com   # Vertex AI
gcloud services enable notebooks.googleapis.com    # Vertex AI Workbench
```

### `gcloud` uses the wrong project or account

If commands fail unexpectedly or target the wrong project, check your active configuration:

```bash
gcloud auth list
gcloud config list
gcloud config set project YOUR_PROJECT_ID
gcloud auth application-default login
```

### SSH into VM fails

If `gcloud compute ssh` hangs or is refused:

- Make sure the VM is in the `RUNNING` state (`gcloud compute instances list`).
- Wait ~30-60 seconds after boot — SSH isn't available immediately.
- Confirm firewall rules allow SSH on port 22 (the `default-allow-ssh` rule is usually enough).

### CUDA / GPU drivers not found on VM

If `nvidia-smi` fails on a fresh VM, you most likely used a generic OS image instead of a Deep Learning VM image. Recreate the VM with a Deep Learning image, e.g. `--image-family=common-cu129-ubuntu-2204-nvidia-580 --image-project=deeplearning-platform-release`, or install the NVIDIA drivers manually.

### Out-of-memory (OOM) errors during training

Your model + optimizer state + activations don't fit on the GPU. See **Appendix A: GPU Memory Maths** for how to estimate requirements and pick the right GPU, and consider gradient accumulation, mixed precision, or a smaller batch size.

### Unexpected costs / forgot to shut down a VM

**Stop VMs when you're done** — you're billed for GPUs even when idle:

```bash
gcloud compute instances stop INSTANCE_NAME --zone=ZONE
```

Set up a budget alert (see Part 4) so you're notified before burning through your $300 free credits.

## 🫱🏿‍🫲🏾 Contributing

This tutorial is fully **open-source**! We welcome:

- 🐛 Bug reports
- 💡 Suggestions for improvements
- 📝 Additional sections, examples, or exercises
- 🌎 Translations

See [CONTRIBUTING.md](./CONTRIBUTING.md) for details.

## 📄 License

This tutorial is released under the CC-BY-4.0 License. See `LICENSE` for details.

---

**Let's get started!** → [Part 1: GCP Foundations](part1-gcp-foundations/index.md)
