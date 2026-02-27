# Hands-On 01: Create a VM via GCP Console

In this hands-on tutorial, you will create your first GPU-powered virtual machine using the GCP Console web interface.

## Step 1: Create a Virtual Private Cloud (VPC) Network 

Every virtual machine requires a network to function. A [Virtual Private Cloud (VPC)](https://docs.cloud.google.com/vpc/docs/vpc) is a secure, isolated private network hosted within Google Cloud. It acts as the virtual equivalent of a physical network in a traditional data center. Specifically, a VPC network does the following:

- **Provides connectivity** for your Compute Engine virtual machine (VM) instances.
- **Enables communication** between your VMs, other Google Cloud services, and the public internet.
- **Acts as a secure boundary**, allowing you to control exactly what traffic can enter or leave your network using firewall rules.

To ensure your VM has the correct network configuration and to prevent creation errors, we will manually create a VPC first.

:::{note} Basic Cloud Networking Concepts

Before diving into creating the VPC and virtual machine, let's clarify a few essential concepts that are key to cloud networking, as understanding these will ensure your VM is both secure and accessible:

- **VPC:** The overarching network for your Google Cloud project. VPC networks, including their associated routes and firewall rules, are **global resources**. They are not associated with any particular region or zone.

- **Subnet (Subnetwork):** A division of your VPC. Subnets are regional resources. When you create a VM, you attach it to a specific subnet, which assigns the VM its internal IP address from a defined range.

- **Firewall Rules:** Security policies that control what traffic is allowed into (ingress) or out of (egress) your VPC. By default, VPC networks block all incoming traffic.

- **Cloud NAT (Network Address Translation):** In enterprise environments, VMs are often kept fully private without external IP addresses for security. Cloud NAT allows these strictly private VMs to reach the internet to download software updates without exposing them to incoming internet traffic.

- **Cloud Router:** Works alongside Cloud NAT to manage the dynamic routing of traffic between your private subnets and the outside world.

For this specific tutorial, we will assign a temporary external IP directly to our VM so we can connect to it easily. Therefore, we do not need to configure Cloud NAT or Cloud Router here.
:::

1. Open the Navigation menu **(☰)** and go to **VPC network → VPC networks**.

2. Click the **Create VPC Network** button

3. **Name:** Enter `demo-vpc`, or whatever name you want.

4. **Subnet creation mode:** Select **Automatic**. This instructs Google Cloud to automatically create a subnet with a predefined IP range in every available region.

5. **Firewall rules:** Select the checkboxes for **allow-ssh**, **allow-icmp**, and **allow-custom**. Opening the SSH port (port 22) is absolutely required so you can log into your VM later.

6. Click **Create** and wait for the status icon to show it is ready.

```{figure} ./images/hands-on1-gcp-console-vpc-creation-a.png
:alt: VPC Creation (A)
:align: center

Screenshot of VPC creation step A (configure VPC details and subnet creation mode)
```

```{figure} ./images/hands-on1-gcp-console-vpc-creation-b.png
:alt: VPC Creation (B)
:align: center

Screenshot: VPC creation step B (configure firewall rules and click "create")
```

## Step 2: Navigate to Compute Engine on the [GCP Console](https://console.cloud.google.com)

1. Open the **Navigation menu (☰)** in top-left
2. Go to **Compute Engine → VM instances**
3. If the Compute Engine API is not enabled, click **Enable** and wait a few moments. You can also enable the Compute Engine API from the **APIs & Services** page.

```{figure} ./images/hands-on1-gcp-console-create-instance.png
:alt: Create instance on GCP Compute Engine
:align: center

Screenshot of "Create instance" page
```

## Step 3: Create and Configure the Instance

Click the **Create instance** button at the top of the Compute Engine dashboard. This will take you to the instance configuration page where you can configure the machine configuration, operating system (OS) and storage, networking, security, etc.

### For Machine Configuration
1. Enter a name for the virtual machine instance i.e. `aims-gcp-tutorial-1` and select a region i.e. `us-central1 (Iowa)` and zone.
2. To configure the machine type, select the **GPUs** tab and choose **NVIDIA T4** under **GPU type** and **1** under the **Number of GPUs** dropdown to use 1 GPU. This means we will be working with a single NVIDIA T4 GPU.

:::{note}
The console categorizes hardware into "Machine Families" based on your needs:

- **General-purpose (E2, N2, N1)**: The best price-performance ratio for everyday balanced workloads.
- **Compute-optimized**: Designed for high-performance computing (HPC) and CPU-intensive tasks.
- **Memory-optimized**: Ideal for memory-intensive workloads like large in-memory databases.
- **Storage-optimized**: Best for high-density storage and high-throughput data access.
- **GPUs**: Essential to access GPU hardware accelerators for AI/ML experiments.
:::

```{figure} ./images/hands-on1-gcp-console-machine-config.png
:alt: Machine configuration
:align: center

Screenshot of "Machine configuration" page
```

### For OS and Storage Configuration

On the sidebar, click on OS and Storage. This will allow you to configure the operating system. 

1. Click on **Switch Image** to configure the OS image for the boot disk.
2. In the **Operating system** dropdown under "Public images", select **Deep Learning on Linux** to use a 
3. In the **Version** dropdown, select **Deep Learning VM with CUDA 12.4 M124**. You can also select a different CUDA version.
4. Set the **Boot disk type** to **Balanced persistent disk**. This provides the best cost-to-performance ratio.
5. Click **Select** to continue.

```{figure} ./images/hands-on1-gcp-console-os-and-storage.png
:alt: OS and Storage Configuration
:align: center

Screenshot: "OS and Storage" configuration window
```

### Networking, Security, and Advanced Settings 

Before clicking the **Create** button on the bottom of the page to create your first VM instance on GCP, click on **Networking** in the sidebar to link your VM to the network you created in Step 2.

1. Under the Firewall section, check **Allow HTTP traffic** and **Allow HTTPS traffic**. Checking these boxes opens ports 80 and 443, permitting inbound HTTP and HTTPS traffic to your VM. This is essential for exposing web-based applications hosted on your VM to the internet, allowing you to access tools like Jupyter Notebooks or web demos remotely via your browser.
2. Under **Network interfaces**, click the dropdown.
3. You can select the VPC network you just created i.e. `demo-vpc` for the network interface's **Network**.
4. Ensure **External IPv4 address** is set to **Ephemeral**. This assigns a public IP address so you can reach the VM from the internet.

```{figure} ./images/hands-on1-gcp-console-networking.png
:alt: Networking Configuration
:align: center

Screenshot: "Networking" configuration page
```

- **Security & Advanced**: Here you can manage SSH keys or add startup scripts. For most tasks, the default service account settings are sufficient.

:::{tip}
At the top right, look for the **Equivalent Code** button. This opens a panel showing the exact `gcloud` CLI command for the settings you just chose. This is a great way to learn the CLI syntax by seeing how the Console translates your clicks into code.
:::

After completing the configuration steps above, you can then click the **Create** button to create your first VM instance in GCP using the Console 🥳. The creation process will take about ~1-2 minutes.

## Step 4: The VM Instances Page 

Once you click **Create**, your VM will appear on the list of VMs.

```{figure} ./images/hands-on1-gcp-vm-created.png
:alt: VM instances list
:align: center

Screenshot: VM instances list page with the **SSH button** outlined in red.
```

Notice two critical pieces of information:

- **Internal IP**: Used for communication between different Google Cloud resources in your project. It is private and carries no data transfer costs.

- **External IP**: The public address used to reach your VM from the internet (e.g., via your laptop terminal).

### Connecting to your VM via SSH 

To access the VM, click the **SSH** button in the "Connect" column of the VMs list.

1. A browser-based terminal window will open. Click **Authorize** when prompted to allow the connection.

2. Because we used a Deep Learning image, the system will automatically detect the GPU hardware. You will see a prompt: *"Would you like to install the Nvidia driver? [y/n]"*.

3. Type `y` and press **Enter**. The installation will take 3–5 minutes.

Once finished, verify the setup by running `pwd` to see your home directory and `nvidia-smi` to view the GPU status.

```{figure} ./images/hands-on1-gcp-console-ssh-in-browser-test.png
:alt: GCP Console SSH-in-Browser Terminal
:align: center

Screenshot: SSH browser terminal showing the output of the `pwd` command and the `nvidia-smi` command
```

## Step 5: Stopping or Deleting your VM Istance

As you may have noticed, the monthly estimate for keeping a this VM you just created running 24/7 is approximately $200 (at an hourly rate of about $0.35 for an N1 machine with a T4 GPU). This is quite a lot of money to lose.

To manage your budget, you must proactively manage the state of your VM from the **VM instances** page by stopping or deleting unused VMs.

**To stop or delete a VM:**
1. Go to the **VM instances** page in the GCP Console.
2. Check the box next to your VM name.
3. Click the **Stop** button to stop the VM or the **Delete** button to delete the VM.

:::{important}
- **Stopping a VM** is like turning off your laptop. Google stops charging you for the GPU and CPU usage. However, you will still incur a small monthly charge for the **Disk storage** (the "Balanced Persistent Disk") because Google must keep your data saved so it's there when you "Turn on" the VM again.

- **Deleting a VM** wipes the instance and its disk completely. You stop paying for everything. This is the best option once you have finished an experiment and have downloaded your results. **Note:** Once deleted, your code and data cannot be recovered unless you have backed them up elsewhere.
:::

---

## 🚀 What's Next?

You have successfully created a GPU-enabled VM via the GCP Console using the Compute Engine service 🥳. Beyond creating VMs, the console allows you to interact with a wide range of GCP services and resources.

Now that you have mastered the interface, we will move to the terminal to learn how to automate these tasks using the command line








