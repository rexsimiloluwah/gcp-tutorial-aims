# Introduction 

## What is Vertex AI?

When you run a machine learning experiment on a raw virtual machine (VM), you are responsible for provisioning the machine, managing Python environments, monitoring your training jobs, and remembering to shut everything down when you are done. This works fine for one or two experiments, but it becomes a real burden as your workload scales.

**Vertex AI** is Google Cloud's fully managed, end-to-end ML platform. It abstracts away the infrastructure so you can focus entirely on your models and data. Instead of thinking about virtual machines and infrastructure setup, you work with higher-level concepts like jobs, datasets, experiments, and pipelines.

:::{note}
**“Fully managed”** means Google handles the underlying infrastructure, including provisioning hardware, installing drivers, scaling resources, and cleaning up after jobs complete.
:::

## Core Capabilities of Vertex AI

Vertex AI is not a single tool. It is a suite of services that covers the entire ML workflow:

- **Custom Training Jobs**: Enables you to submit training jobs by specifying your code, container image, and machine configuration. Vertex AI provisions the required compute resources, runs the job, streams logs, and automatically shuts down the infrastructure when training completes. You are billed only for the time the job is running.

- **Vertex AI Workbench**: Provides managed notebook environments for development and experimentation. It offers:
  - **Workbench Instances (formerly Managed Notebooks)** — A managed JupyterLab environment with optional GPU support and pre-installed ML libraries, accessible directly from your browser without manual infrastructure setup.
  - **Colab Enterprise** — A serverless notebook experience similar to Google Colab, but running within your Google Cloud project. It integrates directly with Google Cloud Storage and other Google Cloud services, without requiring you to provision or manage infrastructure.

- **Model Registry**: A centralized service for storing, versioning, and managing trained models. After training, a model can be registered as a versioned artifact that you can deploy, compare against other versions, or roll back to when needed.

- **Vertex AI Pipelines**: Enables you to define reproducible and automated workflows that connect steps such as data preparation, training, evaluation, and deployment. Once defined, a pipeline can be triggered programmatically to ensure consistent and repeatable ML processes.

- **Prediction and Serving**: Allows you to deploy trained models as managed REST endpoints with automatic scaling. Vertex AI handles load balancing and scales resources up or down based on traffic patterns.

- **Vertex AI Experiments**: Provides built-in experiment tracking that logs parameters, metrics, and artifacts across training runs, helping you compare results and maintain reproducibility within the platform.

- **Vertex AI Model Garden**: A curated catalog of foundation and open models that can be deployed or fine-tuned within your project, offering streamlined access to large models on Google Cloud without complex infrastructure setup.

## How Vertex AI Fits Into GCP?

Vertex AI does not work in isolation. It is deeply integrated with the rest of GCP:

- **GCS**: Training jobs read data from and write checkpoints to GCS buckets automatically.  
- **Cloud Logging**: Job logs are streamed to Cloud Logging and accessible from the Console or CLI.  
- **IAM**: Access control for jobs, models, and endpoints is managed through the same IAM roles as the rest of GCP.  
- **Artifact Registry**: Custom training containers are stored in Artifact Registry and referenced by training jobs.

:::{note}
One of the main benefits of Vertex AI is its seamless integration with Google Cloud. If your data is already in GCS and your team uses GCP, Vertex AI can be used immediately without setting up separate infrastructure.
:::

## Vertex AI vs Raw VMs on GCP

Both raw virtual machines (VMs) and Vertex AI can run the same experiments, but they differ in **operational overhead and management**:

- **Raw VM**: Offers full control but requires more setup. You must provision the machine, configure environments, monitor jobs, and clean up manually. Best suited for one-off experiments or when you need custom system-level configuration.

- **Vertex AI Custom Training**: Provides less control but greatly reduces operational overhead. You submit a job specification and the platform handles provisioning, execution, logging, and cleanup automatically. Ideal for repeated experiments, team workflows, and large-scale projects.

- **Vertex AI Workbench Instances**: Interactive, browser-based JupyterLab environments running on managed hardware. The instance remains active until you stop it, similar to a VM, but you don’t need to manage drivers or environment setup.

- **Colab Enterprise**: Fully serverless notebooks. No infrastructure to manage, making it perfect for quick experimentation and data exploration.

:::{warning}
Vertex AI requires more initial setup compared to SSH-ing into a VM. For a single quick experiment, a raw VM can be faster to start. Vertex AI becomes advantageous when running multiple experiments, collaborating with a team, or building reproducible pipelines.
:::

| Feature | Raw VM | Vertex AI Custom Training Job | Vertex AI Workbench |
|---------|--------|--------------------|-------------------|
| **Management** | Manual | Fully managed | Semi-managed |
| **Idle costs** | Yes, until deleted | No, auto-terminates | Yes, until stopped |
| **Access** | SSH / Terminal | CLI / Console logs | Browser (Jupyter) |
| **Setup complexity** | Low | Medium | Low |

---

## 🔑 Key Takeaways

- Vertex AI is a fully managed machine learning platform on Google Cloud. You define what to run, and the platform manages the underlying infrastructure required to execute it.

- It spans the end-to-end ML workflow, including training, experiment tracking, model versioning, pipelines, and model serving.

- Vertex AI Workbench is the umbrella for managed notebook environments:
  - Workbench Instances provide a persistent JupyterLab environment running on managed compute.
  - Colab Enterprise provides a serverless notebook experience within your Google Cloud project.

- Model Garden provides curated access to foundation models that can be deployed or fine-tuned directly within your project, reducing the need for custom infrastructure setup.

- Vertex AI integrates natively with services such as Google Cloud Storage (GCS), IAM, and Cloud Logging, making it a natural fit for teams already operating within Google Cloud.

- Compared to raw virtual machines, the primary tradeoff is control versus operational overhead. Vertex AI requires more initial configuration, but it reduces manual infrastructure management and eliminates idle costs for managed training jobs.

## 🚀 What Next?

In the next two hands-on sessions, we will run the same Gemma 1B fine-tuning experiment on Vertex AI in two different ways, focusing on the developer experience.

- **Hands-On 1: Vertex AI Custom Training Job**: Submit the experiment as a managed batch training job using the `gcloud` CLI. This demonstrates how Vertex AI provisions compute, executes the job, streams logs, and automatically cleans up when training completes.

- **Hands-On 2: Vertex AI Notebook (Colab Enterprise)**: Run the same experiment interactively inside a Vertex AI notebook using Colab Enterprise. This provides a fully serverless, browser-based notebook experience within your Google Cloud project, allowing you to experiment and iterate without managing infrastructure.

These sessions are designed to help you understand the workflow of working within Vertex AI.

---

## 📚 References & Further Reading 

* [Vertex AI Documentation](https://cloud.google.com/vertex-ai/docs) - Official Google Cloud documentation covering all Vertex AI services.
* [Vertex AI Custom Training Overview](https://cloud.google.com/vertex-ai/docs/training/custom-training) - Detailed guide on submitting and managing custom training jobs.
* [Vertex AI Workbench Documentation](https://cloud.google.com/vertex-ai/docs/workbench/introduction) - Guide to creating and using managed Workbench Instances.
* [Colab Enterprise Documentation](https://cloud.google.com/colab/docs/introduction) - Guide to using serverless Colab Enterprise notebooks on Vertex AI.
* [Vertex AI Model Garden](https://cloud.google.com/model-garden) - Browse and deploy foundation models directly from GCP.
* [Vertex AI Pricing](https://cloud.google.com/vertex-ai/pricing) - Full pricing breakdown for training, prediction, and notebook instances.



