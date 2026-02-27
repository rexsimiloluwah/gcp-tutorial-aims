# Hands-On: Setting Up Billing Alerts and Budgets 

## Overview 

In this hands-on session, we will set up a GCP billing budget and alert so you are notified before you overspend your credits. We will also use the **GCP Pricing Calculator** to estimate the cost of the experiments we ran in the previous hands-on sessions.

## Learning Objectives 

By the end of this session, you will be able to:

- Set up a billing budget and alert in GCP
- Use the GCP Pricing Calculator to estimate experiment costs 
- Understand the cost breakdown of the resources used in this tutorial

---

## Step 1: Open the Billing Console 

1. Go to console.cloud.google.com
2. In the top left, click the navigation menu
3. Click **Billing**

:::{note} For AIMS Students:

When you click **Billing** you will see a page saying "You have multiple billing accounts" with the message "Billing account aims.ac.za Billing Account is linked to this project". This is expected since your project is linked to the AIMS institutional billing account. 

Click **Go to linked billing account** to access the billing dashboard for your project and continue with the steps below.
:::

## Step 2: Create a Budget 

In the left sidebar, click **Budgets and alerts**, then click **Create budget** to create a new budget.

```{figure} ./images/gcp-console-create-budget.png
:alt: Create budget
:align: center

Screenshot: Create a budget.
```

:::{warning}
Notice the banner on the Budgets and alerts page that says **"Setting a budget does not cap resource or API consumption."** This is important. GCP will send you an email alert when you reach your threshold but will not stop your resources automatically. You are still responsible for deleting resources when you are done.
:::

To create a budget, we need to set up the **scope** (which projects and services to monitor), the **amount** (the total budget limit), and the **actions** (how you want to be notified when you hit your limit).

**Scope:**

- Enter a name for the budget, for example `gcp-aims-tutorial-budget`
- Under **Time range**, leave it as **Monthly**
- Under **Projects**, make sure your project is selected
- Leave all services selected so the budget covers all GCP services
- Click **Next**

```{figure} ./images/gcp-console-create-budget-scope.png
:alt: Create budget - scope
:align: center

Screenshot: Set up the budget scope
```

**Amount**

- Under Budget type, select Specified amount
- Enter your target amount i.e. $10.
  - **Note for AIMS students:** While your total allocation might be \$2,000, setting a low monthly target like \$10 or \$20 helps you catch accidental "resource leaks" early.
- Click **Next**

```{figure} ./images/gcp-console-create-budget-amount.png
:alt: Create budget - amount
:align: center

Screenshot: Set up the budget amount
```

**Actions**

- By default, Google Cloud creates three alert thresholds at 50%, 90%, and 100% of your budget. Keep these defaults:

    - 50%: Acts as an early warning that your experiment is halfway through its monthly allowance.

    - 90%: Serves as a critical "wrap up" signal to finish your runs.

    - 100%: Notifies you that you have fully reached your limit.

- Under **Manage Notifications**, make sure the box **"Email alerts to billing admins and users"** is checked. This ensures you receive an immediate email notification the moment a threshold is crossed.

- Click **Finish** to finally create the budget.

```{figure} ./images/gcp-console-create-budget-actions.png
:alt: Create budget - actions
:align: center

Screenshot: Set up the budget actions
```


## Step 3: Verify the Budget

You should not see the budget listed on the **Budget and alerts** page with the spend progress bar and configured thresholds.

```{figure} ./images/gcp-console-budgets-alerts-list.png
:alt: Budgets and alerts page
:align: center

Screenshot: Budgets and alerts page showing the newly created budget with spend progress.
```

You will receive an email notification when your spending reaches 50%, 90%, and 100% of the budget amount.

:::{note} Approximate costs for this tutorial

You can use the [GCP Pricing Calculator](https://cloud.google.com/products/calculator) to estimate costs before running any experiment. 

Based on `europe-west4` pricing, the approximate costs for the resources used in this tutorial are:

- L4 GPU VM (`g2-standard-4`): ~\$0.70/hour
- Vertex AI Custom Training Job (same machine): ~\$0.75/hour
- Vertex AI Colab Enterprise L4 runtime: ~\$0.70/hour
- GCS Standard Storage: ~\$0.02/GB/month

The full tutorial from start to finish, including all hands-on sessions, should cost roughly $4-5 in total if you clean up resources promptly after each session. 

The biggest risk is forgetting to delete a VM or runtime. A 20-minute training run on an NVIDIA L4 GPU costs roughly \$0.25, but leaving a VM running overnight costs ~\$16.
:::


---

## 🔑 Key Takeaways 

- Always set up billing budget and alert before running experiments, not after.
- GCP billing alerts notify you but do not stop your resources automatically. Manual cleanup of resources is always required.
- The biggest cost driver is usually GPU compute time. Always delete VMs and runtimes when you are done.
- Job-based compute like Vertex AI Custom Training is almost always cheaper than always-on VMs for batch training since it terminates automatically when done.

## 📚 References and Further Reading 

- [GCP Billing Documentation](https://cloud.google.com/billing/docs) - Official documentation on managing billing and budgets
- [GCP Pricing Calculator](https://cloud.google.com/products/calculator) - Estimate costs for any GCP service before running experiments
- [Compute Engine Pricing](https://cloud.google.com/compute/gpus-pricing) - Full pricing breakdown for GPU VM instances
- [Vertex AI Pricing](https://cloud.google.com/vertex-ai/pricing) - Full pricing breakdown for Vertex AI training and serving
- [GCS Pricing](https://cloud.google.com/storage/pricing) - Storage and data transfer pricing for Google Cloud Storage