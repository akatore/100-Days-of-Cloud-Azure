Previous: [Step 3: Writing the Network Module.md](./Step-3:-Writing-the-Network-Module.md)

You corrected yourself perfectly halfway through! It is a tricky concept, so let's clear up that final bit of doubt.

**Yes, the code will fail (Option C is correct).** Here is why:

Think of a Child Module as a **Black Box**.

* The Root module (the parent) **cannot see inside** the Child module.
* It cannot "reach in" and grab `azurerm_subnet.aks_subnet.id`.
* The **only** way data leaves that black box is through the `outputs.tf` door.

**The Failure Chain:**

1. **AKS Module** *requires* a `subnet_id` to build the cluster.
2. **Root Module** tries to get that ID from the Network Module to pass it to AKS.
3. **Network Module:** If there is no `output`, the Root Module gets *nothing*.
4. **Result:** You cannot write the code to connect them. Terraform will error out saying it doesn't know what `module.network.aks_subnet_id` is.

So, while the Network itself might build fine, the **overall system** fails because the bridge between Network and AKS is broken.

---

### Step 4: Writing the AKS Cluster Module

Now that we have our network ready and "exporting" its Subnet ID, let's build the AKS Cluster module.

We will create a new folder: `modules/aks-cluster/`.

#### 1. Define the Inputs (`variables.tf`)

The AKS module needs to know where to live (Resource Group, Location) and, crucially, which Subnet to use.

```hcl
# modules/aks-cluster/variables.tf

variable "resource_group_name" { type = string }
variable "location" { type = string }
variable "cluster_name" { type = string }

# This is the connector!
variable "vnet_subnet_id" {
  description = "The ID of the subnet where the nodes will be placed"
  type        = string
}

variable "dns_prefix" { type = string }

```

#### 2. Create the Cluster (`main.tf`)

This is the big resource. We need to configure the `default_node_pool` to use the subnet we passed in.

```hcl
# modules/aks-cluster/main.tf

resource "azurerm_kubernetes_cluster" "aks" {
  name                = var.cluster_name
  location            = var.location
  resource_group_name = var.resource_group_name
  dns_prefix          = var.dns_prefix

  default_node_pool {
    name           = "default"
    node_count     = 1
    vm_size        = "Standard_DS2_v2"
    
    # CRITICAL: This connects the cluster to the network module!
    vnet_subnet_id = var.vnet_subnet_id 
  }

  # Use System Assigned Identity (simplest for now)
  identity {
    type = "SystemAssigned"
  }

  # Network Profile is REQUIRED when using advanced networking (CNI)
  network_profile {
    network_plugin    = "azure"   # This enables Azure CNI
    load_balancer_sku = "standard"
  }
}

```

### 🧠 Checkpoint: The Connection

Look closely at the `network_profile` block I added at the end.

```hcl
  network_profile {
    network_plugin    = "azure"
    load_balancer_sku = "standard"
  }

```

If you remember from our earlier discussion about **Azure CNI** vs **Kubenet**, setting `network_plugin = "azure"` turns on Azure CNI.

**Question:** Since we set this to `"azure"` (Azure CNI), what happens if we try to deploy this module using a tiny subnet (like a `/28` with only 11 IPs)?
```
A) Terraform will automatically expand the subnet for us.
B) The deployment will likely fail or get stuck because there aren't enough IPs for the pods.
C) It will automatically switch to Kubenet to save space.
```

## Doubts 
Wait dont leave me, __Cant we just reference the `aks_subnet_id` created in network module?, instead of outputting it?__

__This way AKS module would receive the `aks_subnet_id` and code will not fail.__

---
I am definitely not leaving you! Let's clear this up, because this is the #1 confusion people have when moving from "one big file" to "modules."

The short answer is: **No, you cannot reference it directly without the output.**

Here is the "Why":

### The Concept: Module Scope (The "Locked Room" Rule)

In Terraform, every module is like a **soundproof, locked room**.

* **The Network Module** is in Room A.
* **The AKS Module** is in Room B.

When Room A builds the Subnet, that information stays inside Room A. Room B has **no idea** what is happening in Room A. It cannot "see" the resource names or IDs inside Room A.

If you try to reference `azurerm_subnet.aks_subnet.id` inside the **AKS Module**, Terraform will say: *"I don't know what this is. This resource is not defined in this folder."*

### How "Output" fixes this

The `output` block is like installing a **window** in Room A.

1. **Room A (Network):** Builds the subnet -> Puts the ID in the window (`output`).
2. **Root (Hallway):** Takes the ID from the window -> Walks it over to Room B.
3. **Room B (AKS):** Accepts the ID through the door (`variable`).

**The Code Reality:**
If you don't use `output` in the Network module, the Root module (`main.tf`) cannot grab the ID to pass it to the AKS module. The chain is broken.

---

Does that analogy make sense? We do it this way so that modules are completely independent—you could reuse that Network module for a Database or a VM without changing the code inside.

If you're cool with that, let's jump back to that **Quick Check** regarding the CNI/Subnet size (Step 4), because it's a critical "gotcha" in AKS networking.

**Here is the question again:**
We configured `network_plugin = "azure"` (Azure CNI).
What happens if we deploy this using a tiny subnet (like a `/28` with only 11 IPs)?

A) Terraform will automatically expand the subnet for us.
B) The deployment will likely fail or get stuck because there aren't enough IPs for the pods.
C) It will automatically switch to Kubenet to save space.