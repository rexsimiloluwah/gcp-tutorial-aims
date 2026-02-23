# GCP Core Concepts

Before diving in, it's essential to develop a clear intuition for what the Google Cloud Platform (GCP) is, its core components, and how it organizes and manages resources. This section also introduces the core ideas that will guide how we use GCP throughout this tutorial. This foundation will help you navigate GCP confidently and avoid common pitfalls.

## What Is the Google Cloud Platform (GCP)?

GCP is a suite of cloud computing[^cloudcomputing] services, including computing, storage, networking, data analytics, machine learning, etc., that runs on the same infrastructure Google uses for products like Search, Gmail, and YouTube. It allows individuals and organizations/enterprises to use Google's computing infrastructure without owning or managing physical hardware.

At a high level, GCP provides a way to create, configure, and manage computing resources using software in an on-demand manner. Machines, storage, and networks are no longer physical objects, but configurable resources that can be created, modified, and removed as needed.

:::{note}
If the term *cloud computing* is unfamiliar, it may be helpful to pause here and review the general idea. 

You do not need a deep understanding of cloud computing to follow this tutorial, but having a rough mental model can make later sections easier to reason about.

Here are some helpful links:
- [What is Cloud Computing? - Google Cloud (Blog)](https://cloud.google.com/learn/what-is-cloud-computing?hl=en)
- [I Explain Cloud Computing in About 10 Minutes - Cloud Computing Insider (YouTube)](https://www.youtube.com/watch?v=aGMYBsBvRWs)
:::

## Why Should You Care About GCP?

For AI/ML researchers, GCP provides:

- **Compute resources**: Virtual machines with GPUs and TPUs.
- **Storage**: Persistent disks for storing datasets, model artifacts, etc.
- **Networking**: High-speed connections between resources.
- **Flexible pricing**: Pay-per-second billing.
- **Identity and access management**: Fine-grained control over who can create, view, or modify resources.

## What is a Resource in GCP?

In GCP, a *resource* is any manageable entity that you can create, configure, and pay fore. 

A resource represents something concrete, such as:
- A virtual machine
- A disk or storage bucket 
- A network or firewall rule
- A GPU or TPU attached to a virtual machine
- A managed database service instance 

Resources have a **lifecycle**: they can be created, modified, stopped, deleted, and monitored. Creating resources in GCP incurs cost.

:::{important}
Once you understand that everything you use in GCP is a resource, the hierarchical organization of resources in GCP becomes intuitive.
:::

:::{note}
A resource is different from a service. A service in GCP is a managed feature or platform that provides a specific type of functionality, such as computing, storage, or machine learning, allowing you to create and manage resources without worrying about the underlying infrastructure. While a resource is a concrete instance created using that service.

For example, Cloud SQL is a service that lets you create and manage databases, and each database instance you create within it is a resource.
:::

## GCP Resource Hierarchy 

GCP organizes resources hierarchically. This structure is crucial for managing access, billing, and organization.

```
Organization (e.g. *aims.ac.za*)
└── Folders (optional groupings)
        └── Projects (your main workspace)
            └── Resources (virtual machines, storage, etc.)
```

### Organization 

The **Organization** sits at the top of the GCP resource hierarchy, representing an institution or company. 

:::{note}
For AIMS Students: You're working within the `aims.ac.za` organization. This means:
- Billing is managed centrally by AIMS
- You have specific quotas and permissions 
- You create projects under this organization
- Administrative policies are set at the organization level
:::

### Folders (Optional)

Folders group projects together. An organization (e.g., AIMS) may use folders to organize projects by cohort, program, or research group.

You typically won't create folders yourself - focus on **projects**.

### Projects 

Projects are the core organizational unit. Everything you create lives inside a project.

**Key Characteristics:**
- Each project has a unique **Project ID** (immutable, globally unique)
  - Example: `trump-project`
- Each project has a **Project Name** (can be changed)
- Each project has a **Project Number** (assigned by Google Cloud)
- All resources belong to exactly one project
- Billing is tracked per project
- Access control (IAM) is managed at the project level 

:::{note}
For AIMS students, projects are already created for you by AIMS. You do not need to create a new project. You will work inside an existing project that has billing and permissions preconfigured.
:::

## Core GCP Concepts 

### 1. Billing Accounts 

A **billing account** is linked to a payment method and pays for resource usage across projects.

:::{note}
For AIMS students: Billing is managed by AIMS centrally. You do not need to:

- Add a credit card 
- Manage billing accounts directly 
- Worry about unexpected charges (within approved quotas)

However, you should still:
- Monitor your resource usage
- Clean up resources when done 
- Follow cost-conscious practices 
:::

### 2. Quotas 

**Quotas** limit resource usage in a project. Google Cloud implements quotas to:

- Prevent accidental overspending 
- Ensure fair resource allocation/distribution
- Protect Google Cloud against abuse 

**Common quotas:**
- **GPU quotas**: Number of GPUs per region 
- **CPU quotas**: Number of vCPUs
- **Persistent disk**: Total disk space
- **API rate limits**: Requests per minute 

:::{important}
**Why Quotas matter?**: You might try to launch a GPU virtual machine and get an error like *"Quota 'GPUS_ALL_REGIONS' exceeded"* even though you have permissions. You'll need to request a quota increase.
:::

:::{important}
**To check your quotas on the GCP console**: 

1.	Go to Console → Navigation Menu → IAM & Admin → Quotas and system limits.
2.	Use the filter to select a specific API or service, for example, the Compute Engine API.
3.	Once selected, you will see the available quotas for that service, such as the number of A100 80GB GPUs, CPUs, or other resources.
:::

### 3. Regions and Zones 

GCP resources are deployed in specific geographic locations.

**Region**: A specific, independent, and secure geographic location used to deploy resources. Examples include `us-central1` (Iowa, USA), `europe-west4` (Netherlands), `asia-southeast1` (Singapore). 

**Zones**: A deployment area within a region. A region is a collection of zones. Zones have high-bandwidth, low-latency network connections to other zones in the same region. For example, the `us-central1` region can have zones like `us-central1-a`, `us-central1-b`, `us-central1-c`.

:::{important}
**Why this matters?**

1. **Latency**: Always choose regions closer to you 
2. **Hardware availability**: Not all GPUs are available everywhere. 
3. **Pricing**: Costs vary by region. Check [pricing calculator](https://cloud.google.com/products/calculator?hl=en) for exact costs.
4. **Quotas**: Quotas are set by region. You might have a GPU quota in `us-central1` but not in `europe-west4`.
5. **Reliability**: Deploying across multiple zones within a region improves fault tolerance and reduces downtime.
:::

:::{note}
**Recommended regions for AIMS students:**

| Region | Location | Pros | Cons |
|--------|----------|------|------|
| `europe-west4` | Netherlands | Good latency from Africa, good GPU availability | Slightly higher cost |
| `us-central1` | Iowa, USA | Best GPU availability, often cheaper | Higher latency from Africa |
| `asia-southeast1` | Singapore | Moderate latency, good availability | Moderate pricing |

**Start with `us-central1`** for this tutorial - it has the best GPU availability for learning.
:::

### 4. APIs and Services 

GCP functionality is accessed through **APIs**. Before using a service, you need to enable its API for your project.

**Common APIs you may need to enable:**
- **Compute Engine API**: To create virtual machines 
- **Vertex AI API**: To use managed ML services 
- **Cloud Storage API**: To use object storage services
- **Cloud Build API**: To use container building services.

:::{important}
**To enable an API:**
1.	Go to Console → APIs & Services → Enable APIs and Services 
2. Filter to select the specific API e.g. Vertex AI API
3. Click on the **Enable API** button to enable the API.
:::

### 5. Identity and Access Management (IAM)

**IAM** controls who can do what in GCP.

**Key concepts:**
- **Principal**: Who (user, service account)
  - Example: `you@aims.ac.za` 
- **Role**: What permissions
  - **Owner**: Full control 
  - **Editor**: Can modify resources
  - **Viewer**: Read-only access 
- **Resource**: Where (project, VM, bucket)

:::{note}
For AIMS students: Your permissions are managed by AIMS IT. If you need additional permissions, please contact AIMS IT support.
:::

**Best practices:**
- Use least privilege (use only the minimum permissions needed)
- Don't share credentials
- Use service accounts for automated tasks 

---

## 🔑 Key Takeaways 

- ✅ **Projects** are your main workspace
- ✅ **Quotas** can block you even with permissions - check and request quota increases early
- ✅ **Regions** matter for latency, availability, and cost 
- ✅ **APIs** must be enabled before use
- ✅ **AIMS manages billing** - you focus on research, not payment setup.

## 🚀 What's Next

Now that you understand GCP's core concepts, let's explore the console interface in detail.

---

## 📚 References & Further Reading

1. [Google Cloud Resource Hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy)
2. [Understanding Projects](https://cloud.google.com/resource-manager/docs/creating-managing-projects)
3. [Cloud Quotas](https://docs.cloud.google.com/docs/quotas/overview)
4. [Regions and Zones](https://cloud.google.com/compute/docs/regions-zones)
5. [IAM Overview](https://cloud.google.com/iam/docs/overview)

---

[^cloudcomputing]: Cloud computing is the on-demand delivery of IT resources like storage and processing power over the internet on a pay-as-you-go basis, allowing businesses and individuals to reduce costs and scale rapidly without owning or maintaining physical infrastructure. 


