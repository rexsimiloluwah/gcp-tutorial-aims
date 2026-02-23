# A Practical Guide to Running AI/ML Experiments on GCP

A hands-on tutorial for beginners who want to use Google Cloud Platform (GCP) for large-scale machine learning experiments or AI workloads.

## 🎯 What You'll Learn 
- How to use the GCP console and the `gcloud` CLI
- How to provision and manage virtual machines and GPU/TPU resources on GCP 
- How to structure reproducible AI/ML experiments 
- When to use virtual machines vs. managed services like Vertex AI
- How to predict and manage GPU memory
- How to avoid expensive cloud mistakes with cost monitoring and budget alerts 
- How to fine-tune large models efficiently 

## 📋 Prerequisites 
- Required:
  - Python programming 
  - Comfort with terminal/command line 
  - Basic knowledge of machine learning/deep learning
- Not required:
  - GCP experience (we start from scratch!)
  - Deep learning expertise
  - Cloud computing background 
- You'll also need:
  - A Google Cloud account (there is a free tier available with $300 credits)
  - A text editor (VS Code, Sublime Text, Cursor, etc.)
  - Terminal access

## 🕥 Duration 

4-6 hours (can be split into multiple sessions)

- Core tutorial: 3-4 hours 
- Optional appendices: 1-2 hours additional 

## 📚 Tutorial Structure 

### Core Path

| Part | Title | Time | Learning Objective
|---|---|---|--|
| 0 | Introduction | 15 mins  | Gain a strong motivation for reading this tutorial |
| 1 | GCP Foundations  | 30 mins | Understand how GCP and interact with GCP via the console and `gcloud` CLI |
| 2 | Compute Engine & Virtual Machines | 45 mins | Understand how to create and manage virtual machines in GCP using the Compute Engine service |
| 3 | Cost Management | 30 mins | Understand budget alerts and best practices for cost management |
| 4 | Vertex AI Training Jobs | 35 mins | Understand how to use the managed Vertex AI service to submit ML training jobs |
| 5 | Vertex AI Notebooks | 30 mins | Understand how to use the managed Jupyter notebooks on Vertex AI |
| 6 | Structuring Reproducible ML Experiments | 60 mins | Learn how to structure, track, and version reproducible ML experiments |

### Deep Dives (Optional)
| Appendix | Title | Time | Focus |
|---|---|---|---|
| A | GPU Memory Math | 45 minss | Understand the basics of GPUs, when to use them, how to predict memory usage, avoid OOMs, etc. |
| B | When to Use TPUs | 30 mins | Understand the basics of TPUs, use cases, and when to choose TPUs over GPUs |
| C | Fine-Tuning LLMs | 30 mins | Learn how to fine-tune large language models (LLMs)|

##  💡 What Makes This Tutorial Different 

This tutorial teaches you what you need to run AI/ML experiments on GCP, emphasizing:

- Systems thinking for ML infrastructure
- Cost-conscious cloud practices
- How to structure reproducible AI/ML experiments for real-world projects.
- Hands-on practice with every learned concept.

The content is also structured to be used on-demand so you don't have to read the entire tutorial.

### 🎓 Who This is For 

This tutorial was particularly designed for **AIMS AI for Science students** who do not have a background in cloud computing or software engineering, helping them use GCP more effectively for their research projects.

However, it should be generally helpful for:
- Beginner AI/ML students and researchers
- Anyone curious about using GCP for running AI/ML experiments

## 🧠 How to Use This Tutorial 

### Option 1: Linear (Recommended for beginners)

Start at Part 0 and work through sequentially.

### Option 2: Jump to What You Need 
- Need a quick-start guide to using GCP? → **Parts 1 & 2**
- Need to understand costs? → **Part 3** 
- Want to learn how to use managed services? → **Part 4 & 5**
- Need to learn how to structure AI/ML experiments? → **Part 6** 
- Fighting out-of-memory (OOM) errors on GPUs? → **Appendix A** 
- Considering TPUs? → **Appendix B** 
- Want to fine-tune large models like LLMs? → **Appendix C**

## 🫱🏿‍🫲🏾 Contributing 

This tutorial is fully **open-source**! We welcome:

- 🐛 Bug reports 
- 💡 Suggestions for improvements 
- 📝 Additional sections, examples, or exercises 
- 🌎 Translations 

See CONTRIBUTING.md for details 

## 📄 License

This tutorial is released under the MIT Liecense. See LICENSE for details.

## 🙏🏾 Acknowledgements 

---

**Let's get started!** → [Part 1: GCP Foundations]()


