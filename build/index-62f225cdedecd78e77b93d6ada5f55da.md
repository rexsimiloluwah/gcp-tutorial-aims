# Introduction

## Cost Management for Machine Learning on Google Cloud

Running machine learning experiments in the cloud is powerful, but it can get expensive fast. High-performance hardware like GPUs and TPUs are billed at a premium rate. To keep your budget under control, you need to be intentional about how you provision, use, and tear down your infrastructure.

---

### Core Cost Management Strategies

:::{dropdown} 1. Implement a "Provision-Run-Delete" Workflow

GPU-enabled VMs and Notebook instances are billed for compute resources every second they are in a "Running" state. Make it a habit to save your results to Google Cloud Storage (GCS) and delete the compute resource immediately after the task completes. 

Use `gcloud compute instances list` to verify that no active machines are still running and costing you money.
:::

:::{dropdown} 2. Configure Budget Alerts and Quotas

Set up Cloud Billing alerts to get email notifications when your spending reaches specific thresholds, like 25% or 50% of your budget. 

For an extra layer of protection, you can also limit your GPU Quota in the IAM settings to prevent anyone in the project from accidentally launching a massive, expensive cluster.
:::

:::{dropdown} 3. Prioritize Managed Training Jobs

Use Vertex AI Custom Training for batch workloads. Unlike interactive notebooks or VMs, these jobs automatically provision the hardware, run your code, and shut down the infrastructure the moment the process finishes. 

This removes the risk of human error where a VM is accidentally left running over a weekend.
:::

:::{dropdown} 4. Clean Up Orphaned Persistent Disks

When you delete a VM, the storage disk attached to it is often kept by default. These orphaned disks continue to accrue costs even though they are not attached to anything. 

Periodically check the "Disks" section of the Cloud Console and delete any volumes that are no longer associated with an active VM.
:::

:::{dropdown} 5. Utilize Spot Virtual Machines

For experiments that are not time-critical, use Spot VMs. These offer the same hardware at a discount of up to 90% compared to standard prices. 

The trade-off is that Google can reclaim these instances if they need the capacity elsewhere. Just ensure your code saves checkpoints to GCS frequently so you do not lose progress.
:::

:::{dropdown} 6. Avoid Over-Provisioning

Avoid over-provisioning. High-end GPUs like the A100 or H100 are impressive but expensive. Start with smaller, cost-effective GPUs like the L4 or T4 for debugging and initial testing. 

Only scale up to larger accelerators when your model architecture or data size specifically requires the extra memory.
:::

:::{dropdown} 7. Offload Data to Cloud Storage

Standard persistent disks are much more expensive per gigabyte than Google Cloud Storage. Store your large datasets and final model weights in GCS buckets instead of leaving them on a VM disk. 

This allows you to delete expensive compute resources while keeping your data accessible for a fraction of the cost.
:::

:::{dropdown} 8. Enable Idle Shutdown for Notebooks

If you use managed notebooks, always enable the Idle Shutdown feature. This acts as a safety net by automatically powering down the instance after a set period of inactivity, such as two hours, ensuring you do not pay for hardware you are not actively using.
:::

---

## 📚 References & Further Reading 

To learn more about optimizing your Google Cloud expenses and managing AI infrastructure, you can explore the following resources:

* **[Google Cloud Billing Documentation](https://cloud.google.com/billing/docs)**: Learn how to set up budgets, alerts, and export billing data to BigQuery for advanced analysis.
* **[Vertex AI Pricing Details](https://cloud.google.com/vertex-ai/pricing)**: A comprehensive breakdown of costs for training jobs, notebook instances, and prediction endpoints.
* **[Best Practices for Cost Optimization on GCP](https://cloud.google.com/architecture/framework/cost-optimization)**: Part of the Google Cloud Architecture Framework, this guide covers organization-wide strategies for reducing waste.
* **[Managing GPU Quotas](https://cloud.google.com/compute/docs/resource-quotas)**: Instructions on how to view and request changes to your hardware limits to prevent unexpected scaling.
* **[Cloud Storage Storage Classes](https://cloud.google.com/storage/docs/storage-classes)**: Understand the difference between Standard, Nearline, and Coldline storage to optimize your data lifecycle costs.
