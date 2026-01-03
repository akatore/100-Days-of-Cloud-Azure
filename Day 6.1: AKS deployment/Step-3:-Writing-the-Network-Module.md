**Spot on!** 🎯

You calculated that perfectly. You have about **8 Nodes** max.

* **Math:** 
* **Takeaway:** This is exactly why we prefer larger subnets (like the `/21` you chose earlier) for AKS. 8 nodes is very small for a production cluster!

Now that you understand the math, let's write the code.

---

### Step 3: Writing the Network Module

We are going to build the `modules/network` folder. This module will create the "House" (VNet) and the "Room" (Subnet).

We need three files in our `modules/network/` folder: `variables.tf`, `main.tf`, and `outputs.tf`.

#### 1. Define the Inputs (`variables.tf`)

First, we tell Terraform what ingredients we need from the user.

```hcl
# modules/network/variables.tf

variable "resource_group_name" {
  description = "Name of the resource group"
  type        = string
}

variable "location" {
  description = "Azure region (e.g., East US)"
  type        = string
}

variable "vnet_name" {
  description = "Name of the Virtual Network"
  type        = string
}

variable "vnet_address_space" {
  description = "The CIDR block for the VNet (e.g., 10.0.0.0/16)"
  type        = list(string)
}

variable "aks_subnet_name" {
  description = "Name of the AKS subnet"
  type        = string
}

variable "aks_subnet_address_prefix" {
  description = "The CIDR block for the AKS subnet (e.g., 10.0.1.0/24)"
  type        = list(string)
}

```

#### 2. Create the Resources (`main.tf`)

Now, we use those variables to build the infrastructure.

```hcl
# modules/network/main.tf

# 1. Create the Virtual Network
resource "azurerm_virtual_network" "aks_vnet" {
  name                = var.vnet_name
  location            = var.location
  resource_group_name = var.resource_group_name
  address_space       = var.vnet_address_space
}

# 2. Create the Subnet for AKS
resource "azurerm_subnet" "aks_subnet" {
  name                 = var.aks_subnet_name
  resource_group_name  = var.resource_group_name
  virtual_network_name = azurerm_virtual_network.aks_vnet.name
  address_prefixes     = var.aks_subnet_address_prefix
}

```

#### 3. Return the ID (`outputs.tf`)

This is the most critical part for modules. The **AKS Cluster** (which we will build next) needs to know *where* to live. It doesn't know the name of the subnet; it needs the **Resource ID**.

```hcl
# modules/network/outputs.tf

output "aks_subnet_id" {
  description = "The ID of the AKS subnet"
  value       = azurerm_subnet.aks_subnet.id
}

```

**Visualizing the Data Flow:**

1. **Root Module** sends names and IP ranges INTO `variables.tf`.
2. **`main.tf`** builds the VNet and Subnet.
3. **`outputs.tf`** sends the specific `subnet.id` OUT back to the Root, so the AKS module can use it.

---

### 🧠 Quick Check: The "Glue"

Look at the `outputs.tf` file above.

If we **forgot** to write that `output "aks_subnet_id"` block, what would happen when we try to write the AKS Cluster module later?
```
A) The AKS cluster would automatically find the subnet by name.
B) The AKS cluster would create its own new subnet.
C) We would have no way to tell the AKS cluster which subnet to use, and our code would fail.
```

Next: [Step-4:-Writing-the-AKS-Cluster-Module](./Step-4:-Writing-the-AKS-Cluster-Module.md)

