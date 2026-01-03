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

A) Terraform will automatically expand the subnet for us.
B) The deployment will likely fail or get stuck because there aren't enough IPs for the pods.
C) It will automatically switch to Kubenet to save space.