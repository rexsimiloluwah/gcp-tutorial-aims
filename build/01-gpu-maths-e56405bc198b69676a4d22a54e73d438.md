# Appendix A: GPU Maths - Choosing the Right GPU for Your Experiment

Before provisioning a GPU VM or submitting a Vertex AI training job, it is worth spending a few minutes estimating whether your chosen GPU can actually fit your experiment. Running out of GPU memory mid-training is one of the most common and frustrating errors in ML, and it is entirely avoidable with some simple upfront calculation.

## What Determines GPU Memory Usage?

The GPU memory required for a training run is determined by four main factors:

- **Model parameters** - the weights of the model itself. The memory cost depends on how many parameters the model has and the precision they are stored in
- **Gradients** - a copy of the model parameters computed during the backward pass. In full fine-tuning these match the size of the model weights
- **Optimizer states** - the Adam optimizer stores two additional copies of the parameters (momentum and variance), roughly doubling the gradient memory cost
- **Activations** - intermediate values computed during the forward pass. These scale with batch size and sequence length

## Worked Example: Fine-tuning Gemma 3 1B Model with LoRA

Let us walk through how to estimate the GPU memory required for fine-tuning Gemma 3 1B with LoRA in `bfloat16`, which is exactly the experiment we used in this tutorial.

**Step 1: Base model weights**

Gemma 3 1B has 1 billion parameters. We load it in `bfloat16`, where each parameter takes 2 bytes (16 bits = 2 bytes):
```
1,000,000,000 parameters x 2 bytes = 2,000,000,000 bytes = ~2GB
```

**Step 2: LoRA adapter weights**

With LoRA rank 8 targeting `q_proj` and `v_proj`, we are training roughly 0.1% of the model's parameters. This adds a negligible amount of memory:

```
0.1% x 2GB = ~0.002GB
```

**Step 3: Gradients and optimizer states**

Since LoRA freezes the base model, gradients and optimizer states only apply to the small set of trainable LoRA parameters, not the full model. This adds roughly:
```
~0.1GB
```

**Step 4: Activations**

Activations scale with batch size and sequence length. With batch size 8 and max length 256:
```
~0.3GB
```

**Total estimate:**
```
2GB (base model) + 0.002GB (LoRA) + 0.1GB (gradients) + 0.3GB (activations) = ~2.5GB
```

This is why a 24GB NVIDIA L4 GPU is more than sufficient for this experiment. We are only using about 10% of its available VRAM. You could even run it on a smaller 16GB T4 with room to spare.

> **Note:** These are rough estimates. Actual memory usage can vary depending on the framework version, attention implementation, and other factors. It makes sense to run a quick test with a small number of samples before committing to a full training run.

## What If You Were to Run This Without LoRA?

For comparison, here is what full fine-tuning of Gemma 3 1B in float32 would cost:
```
1B parameters x 4 bytes (float32) = 4GB (model weights)
+ 4GB (gradients)
+ 8GB (Adam optimizer states)
+ ~1GB (activations)
= ~17GB total
```

This would not fit on a 16GB T4 and would be tight on a 24GB L4. This is exactly why LoRA is so valuable. It reduces the memory requirement from ~17GB to ~2.5GB for the same base model.

## Estimating GPU Hours

Once you know your experiment fits in memory, the next question is how long it will take. A simple estimate:
```
GPU hours = (num_samples x num_epochs) / (steps_per_second x batch_size x 3600)
```

For our experiment on the L4, we use **3 steps/second** as our estimate. This comes directly from the training logs we observed during the hands-on session, where the L4 consistently processed between 2.7 and 3.0 steps per second for this specific workload (Gemma 3 1B, LoRA rank 8, bfloat16, batch size 8, sequence length 256). We round up to 3 for a conservative estimate:
```
(10,000 samples x 2 epochs) / (3 steps/second x 8 batch size x 3600 seconds)
= 20,000 / 86,400
= ~0.23 hours (~14 minutes)
```

This matches what we observed in practice, where the full training run completed in roughly 15 minutes.

A few things that affect training speed:

- **Batch size** - larger batches process more samples per step, reducing total steps and training time. However, the tradeoff is that larger batch sizes require more GPU VRAM to store activations, so there is a practical upper limit based on how much memory your GPU has available
- **Sequence length** - longer sequences increase the cost of each step due to the attention mechanism scaling quadratically with sequence length
- **Precision** - bfloat16 is roughly 2x faster than float32 on modern GPUs
- **GPU generation** - an A100 is roughly 3 to 4x faster per step than an L4 for the same workload

Use the [GCP Pricing Calculator](https://cloud.google.com/products/calculator) with your estimated GPU hours to get a cost estimate before starting a run.

## Common GCP GPUs and When to Use Them

| GPU | VRAM | Best For |
|---|---|---|
| NVIDIA T4 | 16GB | Small models up to 3B, inference, cost-sensitive runs |
| NVIDIA L4 | 24GB | Models up to 7B with LoRA, good price/performance |
| NVIDIA A100 40GB | 40GB | Models up to 13B full fine-tune, large batch training |
| NVIDIA A100 80GB | 80GB | Models up to 70B with LoRA, large context lengths |
| NVIDIA H100 | 80GB | Largest models, fastest training, highest cost |

## Practical Tips

- **Start small and scale up.** Run a quick test with a small number of training examples before committing to a full run. If it fits in memory, scale up.
- **Reduce batch size if you hit OOM.** Halving the batch size roughly halves activation memory. Use gradient accumulation to compensate for effective batch size.
- **Use bfloat16.** Switching from float32 to bfloat16 halves model and gradient memory with minimal impact on training quality. This is why we set `bf16=True` in the training arguments.
- **Check memory usage during training.** Run `nvidia-smi` in a separate terminal or tmux window during training to see live GPU memory utilization.

## References and Further Reading

- [NVIDIA GPU Specifications](https://www.nvidia.com/en-us/data-center/products/gpus/) - Full specs for all NVIDIA data center GPUs including VRAM and compute capacity
- [GCP GPU Pricing](https://cloud.google.com/compute/gpus-pricing) - Full pricing breakdown for GPU VM instances on GCP
- [HuggingFace Model Memory Calculator](https://huggingface.co/spaces/hf-accelerate/model-memory-usage) - A handy tool for estimating memory requirements for HuggingFace models
- [LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685) - The original LoRA paper by Hu et al.
- [Mixed Precision Training](https://arxiv.org/abs/1710.03740) - The paper introducing bfloat16 and float16 training