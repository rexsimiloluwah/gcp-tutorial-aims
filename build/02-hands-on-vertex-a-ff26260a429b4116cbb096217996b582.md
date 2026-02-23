# Hands-On 02: Vertex AI Notebooks (Colab Enterprise)

## Overview 

In this hands on session, we will run the same Gemma 1B fine-tuning experiment using **Colab Enterprise**, Vertex AI's managed notebook environment. Unlike the Custom Training Job approach where you submit a job specification and wait, Colab Enterprise gives you an interactive Jupyter notebook (similar to Google Colab) that runs directly in your browser with no SSH or infrastructure setup required.

:::{note}
This hands-on tutorial uses the same Gemma 1B fine-tuning experiment from the previous chapter. If you want to understand the experiment in detail, refer back to **[Fine-tuning a Small Language Model (Gemma 3 1B) on a GCP VM](../part2-running-experiments-on-gcp-vms/01-hands-on-running-ai-experiments-on-gcp-vms.md)**.
:::

## Learning Objectives 

By the end of this session, you will be able to:

- Create a runtime template with a GPU configuration in Colab Enterprise 
- Create and connect a runtime to a notebook 
- Run the fine-tuning experiment interactively in a notebook 
- Clean up all resources after the session.

## Prerequisites 

- A WandB account and API key
- A HuggingFace account and API token with the Gemma 3 1B model license agreement accepted. 

:::{important}
To follow this tutorial, you need a GCP project with the Vertex AI API enabled. Kindly navigate to the **APIs and services** page to enable the API for Vertex AI, if it is not already enabled.
:::

---

## Step 1: Open Colab Enterprise 

1. Go to your [GCP Console](https://console.cloud.google.com)
2. In the left sidebar, navigate to **Vertex AI**
3. Under the **Notebooks** section, click **Colab Enterprise**.

```{figure} ./images/gcp-console-colab-enterprise-page.png
:alt: Colab Enterprise page
:align: center

Screenshot of the Colab enterprise page in Vertex AI
```

## Step 2: Create a Runtime Template 

A runtime template defines the hardware configuration that your notebook will run on. We create it once and can reuse it across multiple notebooks. 

In the left sidebar, click **Runtime templates**, then click **Create a new runtime template.**

```{figure} ./images/gcp-console-colab-enterprise-page-create-runtime-template.png
:alt: Colab Enterprise page
:align: center

Screenshot: Create a new runtime template
```

The runtime template creation has four sections.

**Runtime basics**

- Enter a name for the template, for example `nvidia-l4-gpu-template`
- Select the region for the runtime template, for example `europe-west4`

```{figure} ./images/gcp-console-vertex-notebook-create-runtime-template-basics.png
:alt: Runtime basics
:align: center

Screenshot: Runtime basics (edit display name and select region)
```

**Configure compute**

This is where we configure the compute associated with the runtime template.

- Under **Machine Type**, select `g2-standard-4`
- Under **Accelerator type**, select `NVIDIA_L4` and set Accelerator count to `1`

```{figure} ./images/gcp-console-vertex-notebook-create-runtime-template-configure-compute.png
:alt: Configure runtime template machine configuration
:align: center

Screenshot: Configure machine configuration for the runtime template
```

**Configure Python Environment**

- Select the Python environment under the **Environment** input (you can use Python 3.12)
- Under the **Environment variables**, add your environment variables:
    - `HF_TOKEN` = Your HuggingFace API token 
    - `WANDB_API_KEY` = Your WandB API key
    - `WANDB_PROJECT` = Your WandB project name 
    - `BUCKET_NAME` = Your GCS bucket name

```{figure} ./images/gcp-console-vertex-notebook-create-runtime-template-environment.png
:alt: Configure runtime template Python environment
:align: center

Screenshot: Configure Python environment for runtime template with environment variables filled in
```

:::{note}
Adding environment variables here means they are automatically available in every notebook that connects to this runtime. You do not need to set them manually in your notebook cells.
:::

:::{warning}
Do not share your runtime template with others as it contains your API keys. Keep it private to your GCP project.
:::

**Networking and security**

This is where we choose the networking configuration.

- Under **Network** input, select the VPC you created earlier (`similoluwa-vpc`) or create a new VPC.
- Select the same VPC under the **Subnetwork** input.

```{figure} ./images/gcp-console-vertex-notebook-create-runtime-template-networking.png
:alt: Colab Enterprise page
:align: center

Screenshot: Networking and security section with VPC selected
```

Finally, click the **Create** button to create the runtime template.

## Step 3: Create a Runtime 

Now, we can create a runtime using the runtime template that was just created.

1. In the left sidebar, click **Runtimes**
2. Click **Create new runtime**
3. Select the template you just created i.e. `nvidia-l4-gpu-template`, under the **Runtime template** input
4. Enter a name for your runtime in the **Runtime name** input
5. Click **Create** and wait for it to provision. This takes a few seconds.

```{figure} ./images/gcp-console-create-vertexai-runtime.png
:alt: Colab Enterprise page
:align: center

Screenshot: Create a new runtime using the created runtime template
```

## Step 4: Create a Notebook and Connect to the Runtime 

Now, we can create a Vertex AI notebook using the newly created runtime.

1. On the Colab Enterprise main page, click the **Create notebook** button in Quick actions section
2. Once the notebook opens, click the **Connect** dropdown in the top right
3. Click **Connect to a runtime** 
4. In the **Connect to Vertex AI runtime** dialog:
    - Select the **Connect to an existing runtime** option 
    - Select the runtime you just created under the **Select an existing runtime** input 
5. Finally, click **Create** to connect to the runtime.

```{figure} ./images/gcp-console-colab-enterprise-page-create-notebook.png
:alt: Create a new notebook 
:align: center

Screenshot: Create Colab Enterprise notebook
```

```{figure} ./images/gcp-console-vertex-notebook-connect-to-runtime.png
:alt: Connect to a runtime dropdown
:align: center

Screenshot: Connect to a runtime dropdown
```

```{figure} ./images/gcp-console-vertex-notebook-connect-to-runtime-dialog.png
:alt: Connect to a runtime dialog
:align: center

Screenshot: Connect to a runtime dialog
```

Once connected, the runtime immediately becomes active and you can start using notebook.

## Step 5: Using the Notebook

In this section, we will use the notebook to fine-tune the Gemma 3 1B model.

**Verify the GPU is Available**

To verify the GPU is available:

```{code-block} bash
%%bash
nvidia-smi
```

```{figure} ./images/gcp-console-vertex-notebook-nvidia-smi.png
:alt: Testing `nvidia-smi` command in Vertex notebook
:align: center

Screenshot: Testing `nvidia-smi` command in Vertex notebook
```

**Set Up the Python Environment**

In the next cell, install `uv` and clone the repository:

```{code-block} bash
%%bash
curl -LsSf https://astral.sh/uv/install.sh | sh
source $HOME/.local/bin/env
git clone https://github.com/rexsimiloluwah/finetuning-gemma-1b-aims-gcp-tutorial.git
cd finetuning-gemma-1b-aims-gcp-tutorial
uv sync
```

**Run Training**

In the next cell, run the fine-tuning script:

```{code-block} bash
%%bash
source $HOME/.local/bin/env
cd finetuning-gemma-1b-aims-gcp-tutorial
uv run python -m scripts.run_train data.source=gcs data.max_train_samples=10000 training.num_epochs=2 training.batch_size=8 experiment_id=exp_lora_r8_colab
```

Training takes roughly 20-30 minutes on the L4. You can monitor progress in real time from your WandB dashboard at wandb.ai while the cell is running.

```{figure} ./images/gcp-console-vertex-notebook-train-script.png
:alt: Run the training script
:align: center

Screenshot: Run the training script in the Vertex notebook. Here, it was trained for only 1 epoch using 500 samples for demonstration purposes. Do not expect any good results during evaluation.
```

:::{note}
Unlike the VM-based session, you do not need tmux here. The notebook cell keeps running even if you close your browser tab, as long as the runtime stays active. You can come back later and see the output when it completes.
:::

**Run Evaluation**

Once training finishes, run evaluation in the next cell:

```{code-block} bash
%%bash
source $HOME/.local/bin/env
cd finetuning-gemma-1b-aims-gcp-tutorial
uv run python -m scripts.run_evaluate \
    --model_path outputs/exp_lora_r8_colab/checkpoint-2500 \
    --eval_file data/eval/eval_prompts.jsonl \
    --max_eval_samples 200
```

```{figure} ./images/gcp-console-vertex-notebook-evaluate-script.png
:alt: Run the evaluation script
:align: center

Screenshot: Run the evaluation script.
```

This computes perplexity and repetition rate on 200 eval examples and logs the results to WandB.

:::{note}
Do not worry about the evaluation results. The goal of this session is to walk through the complete workflow, not to produce a state-of-the-art model.
:::

**Run Inference**

Test the trained model on a single instruction in the next cell:

```{code-block} bash
%%bash
source $HOME/.local/bin/env
cd finetuning-gemma-1b-aims-gcp-tutorial
uv run python -m scripts.run_inference \
    --model_path outputs/exp_lora_r8_colab/checkpoint-2500 \
    --instruction "Explain what machine learning is in simple terms"
```

```{figure} ./images/gcp-console-vertex-notebook-inference-script.png
:alt: Run the inference script
:align: center

Screenshot: Run the inference script.
```

## Step 6: Resource Cleanup

:::{danger}
Colab Enterprise runtimes are billed by the hour even when idle. Always delete the runtime when you are done to avoid unexpected charges.
:::

Delete the runtime from the GCP Console:

1. In the left sidebar, click **Runtimes**
2. Find your runtime in the list
3. Click the three dots menu next to it
4. Click **Delete**

:::{tip}
You can alternatively click **Stop** if you want to retain your data/environment for later use.
:::

```{figure} ./images/gcp-console-delete-vertexai-runtime.png
:alt: Delete the vertex AI runtime
:align: center

Screenshot: Delete the Vertex AI runtime.
```
---

## 🔑 Key Takeaways

- Colab Enterprise gives you an interactive GPU notebook environment with no infrastructure to manage, accessible directly from your browser
- Creating a runtime template first and reusing it across notebooks saves time and ensures a consistent hardware configuration across sessions
- Adding environment variables to the runtime template means credentials are automatically available in every notebook that connects to it, without hardcoding them in notebook cells
- Unlike the VM-based approach, you do not need `tmux` since notebook cells keep running even if you close your browser, as long as the runtime stays active
- For long training runs, a Custom Training Job is more reliable since it runs entirely unattended. Colab Enterprise is better suited for interactive experimentation and debugging.










