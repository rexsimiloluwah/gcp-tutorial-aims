# Introduction

## Running AI Experiments on GCP VMs

When a machine learning experiment outgrows your laptop, because the model is too large, training takes too long, or you need a GPU, the natural next step is to move it to the cloud.
GCP virtual machines (VMs) give you on-demand access to powerful hardware, including modern GPUs like the NVIDIA L4, without the cost of owning physical hardware.

Running experiments on a remote VM is conceptually simple: it is just a Linux machine you can SSH into and run Python code on. But there are a few things worth understanding before you get started.

### Reproducible Environments 

- Code that works locally often fails on a VM due to different Python versions or package versions.
- Hence it is important to use package managers like `uv` or `poetry` to automate the installation, configuration, and management of software dependencies, ensuring consistency and reproducibility.

### Persistent Storage 

- VM disks are ephemeral. When you delete the VM, everything else is deleted.
- It is important to always write outputs, checkpoints, and logs to a persistent object storage like Google Cloud Storage (GCS) during or after training so they persist independently of the VM.
- Think of the VM as temporary compute and GCS as permanent storage.

### Long-Running Jobs 

- Training machine learning models can take anywhere from minutes, for small models like XGBoost, to hours or even days for models like transformers or large language models.
- Keeping an SSH connection open for the entire duration is fragile. A dropped connection can kill the training process.
- You can use a tool like `tmux` to keep your process running on the VM even if your SSH session disconnects.

### Experiment Monitoring and Logging 

- Experiment monitoring is the practice of logging and visualizing metrics as a model trains, giving you a live record of how your experiment is progressing.
- On a remote VM, you cannot just watch your terminal, so you need a tool that logs metrics to a dashboard you can access from anywhere.
- Tools like **Weights & Biases**, **Tensorboard**, or **MLFlow** automatically track metrics like loss, accuracy, and learning rate, hardware utilization, and maintain a history of every experiment you run.
- This gives you a centralized record of all your experiments, making it easy to compare runs, spot regressions, and share results with others.

### Cost Management 

- VMs are billed by the second. An idle VM costs the same as a busy one.
- Always stop/delete your VM when an experiment is done.

:::{note}
GPU VMs are significantly more expensive than CPU VMs. An NVIDIA L4 instance costs roughly $0.70/hour. Always double-check that your VM has been deleted after a session by running gcloud compute instances list.
:::

:::{warning}
Never store API keys or tokens directly in your code or commit them to GitHub. Use a .env file listed in .gitignore and load credentials at runtime.
:::

---

## 🚀 What's Next?

In the hands-on session that follows, you will run a complete end-to-end ML experiment on a GCP VM with an NVIDIA L4 GPU. This will involve:

- Fine-tuning **Gemma 3 1B** on the Dolly-15k instruction dataset using LoRA.
- Tracking experiments in real time with **Weights & Biases**
- Persisting checkpoints and outputs to GCS 
- Running evaluation and inference on the trained model