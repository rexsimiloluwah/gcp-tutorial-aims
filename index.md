# A Practical Guide to Running AI/ML Experiments on GCP

A hands-on tutorial for beginners who want to use Google Cloud Platform (GCP) for large-scale machine learning experiments or AI workloads.

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
| 1 | GCP Foundations | 30 mins | Understand GCP and interact with it via the console and `gcloud` CLI |
| 2 | Running Experiments on GCP VMs | 45 mins | Learn how to run AI/ML experiments on GCP virtual machines with GPU support |
| 3 | Vertex AI | 65 mins | Understand how to use the managed Vertex AI service for training jobs and notebooks |
| 4 | Cost Management | 30 mins | Understand budget alerts and best practices for cost management |

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

Start at Part 1 and work through sequentially.

### Option 2: Jump to What You Need

- Need a quick-start guide to using GCP? → **Part 1**
- Want to run ML experiments on a GPU VM? → **Part 2**
- Want to learn how to use managed services? → **Part 3**
- Need to understand costs before spending credits? → **Part 4**
- Fighting out-of-memory (OOM) errors on GPUs? → **Appendix A**

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