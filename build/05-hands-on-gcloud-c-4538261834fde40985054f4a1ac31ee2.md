# Hands-On 02: Automation with the Google Cloud CLI (`gcloud`)

While the GCP Console is great for clicking through settings, using the `gcloud` **command-line interface (CLI)** is much more powerful. It allows your work to be repeatable and scriptable, meaning you can recreate your exact environment with a single command instead of dozens of manual clicks. It also enables automating tasks, such as starting or deleting multiple machines at once.

## Installing the Google Cloud CLI

You need the Google Cloud CLI installed on your local machine to use `gcloud`

Follow the official guides to download and install it:

- [Install for Windows](https://docs.cloud.google.com/sdk/docs/install-sdk#windows)
- [Install for Linux](https://docs.cloud.google.com/sdk/docs/install-sdk#linux)
- [Install for macOS](https://docs.cloud.google.com/sdk/docs/install-sdk#mac)

:::{note}
For AIMS Students, The Google Cloud CLI is already installed on all AIMS Campus desktops. If you run into any issues, kindly contact the IT team.
:::

After installation, run the `gcloud init` command in your terminal to log in and select your project.

```{code-block} bash
gcloud init
```

**Try these basic commands to make sure your terminal is connected to your GCP account:**

```{code-block} bash 
# See which project you are currently using
gcloud config list project

# List the VMs you currently have
gcloud compute instances list
```

### Creating a Virtual Machine (VM)

### Step 1: Create a Secure Network (VPC)

This creates a **Virtual Private Cloud (VPC)** to isolate your computing resources within the project.

```{code-block} bash 
export AIMSUSERNAME="your_aims_username" # This is for AIMS students
gcloud compute networks create ${AIMSUSERNAME}-vpc --subnet-mode=auto
```

### Step 2: Create a Firewall Rule for SSH 

By default, all incoming traffic is blocked. This rule allows traffic on **Port 22**, which is required for SSH connections.

```{code-block} bash 
gcloud compute firewall-rules create ${AIMSUSERNAME}-fw-ssh \
    --network=${AIMSUSERNAME}-vpc \
    --allow=tcp:22
```

### Step 3: Set Up Internet Access (Router and NAT)

This allows your VM to access external resources (such as HuggingFace to download a model) without assigning a public External IP address to the instance.

```{code-block} bash
export REGION="europe-west4" # This is your region

# Create the Cloud Router
gcloud compute routers create ${AIMSUSERNAME}-router-${REGION} \
    --network=${AIMSUSERNAME}-vpc \
    --region=${REGION}

# Create the NAT configuration
gcloud compute routers nats create ${AIMSUSERNAME}-nat-config \
    --router-region=${REGION} \
    --router=${AIMSUSERNAME}-router-${REGION} \
    --nat-all-subnet-ip-ranges \
    --auto-allocate-nat-external-ips
```

### Step 4: Create the VM 

This command provisions a virtual machine with an NVIDIA L4 GPU. The L4 is a modern, cost-effective mid-range GPU with 24GB RAM.

```{code-block} bash
export ZONE="europe-west4-a"
export VM_NAME="${AIMSUSERNAME}-l4-vm" # Any other name works!

gcloud compute instances create ${VM_NAME} \
    --zone=${ZONE} \
    --machine-type=g2-standard-4 \
    --accelerator="type=nvidia-l4,count=1" \
    --image-family=common-cu128-ubuntu-2204-nvidia-570 \
    --image-project=deeplearning-platform-release \
    --maintenance-policy=TERMINATE \
    --network=${AIMSUSERNAME}-vpc
```

:::{note}
If you need a more powerful GPU like an **A100**, you only need to change two flags in the command above:

1. `--machine-type`: Change from `g2-standard-4` to an A2 machine type (e.g., `a2-highgpu-1g`).
2. `--accelerator`: Change from `type=nvidia-l4,count=1` to `type=nvidia-tesla-a100,count=1`.
:::

**Query the VM status**:
Verify that the instance has been provisioned and is in the `RUNNING` state:

```{code-block} bash
gcloud compute instances list --filter="name=${VM_NAME}"
```

```{figure} ./images/hands-on2-gcloud-vm-created.png
:alt: gcloud VM creation output
:align: center

Screenshot: L4 GPU VM created and queried successfully using `gcloud`
```

### Step 5: Connecting to your VM via SSH

You can access the VM securely through the private network via SSH using **Identity-Aware Proxy (IAP)**. We include a port-forwarding flag (`-L 8080:localhost:8080`) so you can open JupyterLab in your local browser:

```{code-block} bash
gcloud compute ssh ${VM_NAME} \
    --zone=${ZONE} \
    --tunnel-through-iap \
    -- -L 8080:localhost:8080
```

Now you can run commands like `nvidia-smi` on your VM to check the GPU information.

```{figure} ./images/hands-on2-gcloud-vm-ssh.png
:alt: gcloud VM SSH output
:align: center

Screenshot: L4 GPU VM successfully connected to via SSH using `gcloud`
```

### Step 6: Automating Multiple VMs with a Bash Script 

The `gcloud` CLI enables automating large-scale tasks. You can use a bash script to launch multiple VM instances simultaneously for parallel experiments.

**Script to create 3 VMs:**

```{code-block} bash
# This loop provisions 3 instances in the background
for i in {1..3}
do
   gcloud compute instances create "experiment-vm-$i" \
       --zone=europe-west4-a \
       --machine-type=g2-standard-4 \
       --accelerator="type=nvidia-l4,count=1" \
       --image-family=common-cu128-ubuntu-2204-nvidia-570 \
       --image-project=deeplearning-platform-release \
       --maintenance-policy=TERMINATE \
       --network=${AIMSUSERNAME}-vpc &
done
wait
echo "All VM instances have been deployed."
```

```{figure} ./images/hands-on2-gcloud-batch-vms-list.png
:alt: gcloud batch VMs creation
:align: center

Screenshot: Multiple L4 GPU VMs created and queried successfully using `gcloud`
```

### Step 7: Resource Cleanup

:::{danger}
To terminate all charges and return your project to an empty state, you must delete the resources in the correct order. 

This is important for cost management to avoid burning your GCP credits.
:::

**Part A: Delete all VM instances**

```{code-block} bash
gcloud compute instances delete $(gcloud compute instances list --format="value(name)") --quiet --zone=${ZONE}
```

```{figure} ./images/hands-on2-gcloud-delete-vms.png
:alt: gcloud VMs deletion and resource cleanup
:align: center

Screenshot: VMs deleted successfully.
```

**Part B: Delete Networking Infrastructure**

```{code-block} bash
# 1. Delete the NAT and Cloud Router
gcloud compute routers nats delete ${AIMSUSERNAME}-nat-config --router=${AIMSUSERNAME}-router-${REGION} --region=${REGION} --quiet
gcloud compute routers delete ${AIMSUSERNAME}-router-${REGION} --region=${REGION} --quiet

# 2. Delete the Firewall rule
gcloud compute firewall-rules delete ${AIMSUSERNAME}-fw-ssh --quiet

# 3. Delete the VPC Network
gcloud compute networks delete ${AIMSUSERNAME}-vpc --quiet
```

---

## 🔑 Key Takeaways 

- Using the `gcloud` CLI makes infrastructure deployment repeatable and scriptable, allowing you to provision complex networking and high-performance compute resources with a single command.
- Using variables like `AIMSUSERNAME`, `REGION`, and `ZONE` ensures your scripts are flexible and easily adaptable.
- To avoid residual charges for cost management, you must return the project to a blank state by deleting not only the VM instance but also the associated networking components like the Cloud NAT, Router, and VPC.

## 🚀 What's Next 

In the next part, we will run a full AI experiment on a GPU VM to fine-tune a small language model with 1 billion parameters (**Gemma 1B**). This hands-on session will introduce you to leveraging GCP services  to manage end-to-end machine learning workflows.











