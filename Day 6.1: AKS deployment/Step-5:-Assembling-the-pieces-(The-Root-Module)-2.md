
---

### Step 5: Assembling the Pieces (The Root Module)

This is the file at the very top of your folder structure (`/main.tf`). This is where you tell Terraform to run both modules and **connect** them.

```hcl
# main.tf (The Root)

provider "azurerm" {
  features {}
}

# 1. Call the Network Module
module "my_network" {
  source = "./modules/network"  # Tells Terraform where the code lives

  # We pass in the VALUES here
  resource_group_name = "rg-learning-terraform"
  location            = "East US"
  vnet_name           = "vnet-learning"
  vnet_address_space  = ["10.1.0.0/16"]
  
  aks_subnet_name           = "subnet-aks"
  aks_subnet_address_prefix = ["10.1.0.0/21"] # Our nice big subnet
}

# 2. Call the AKS Module
module "my_aks" {
  source = "./modules/aks-cluster"

  resource_group_name = "rg-learning-terraform"
  location            = "East US"
  cluster_name        = "aks-demo-cluster"
  dns_prefix          = "aksdemo"

  # ---------------------------------------------------------
  # HERE IS THE MAGIC WIRING!
  # Left Side: The Input variable name inside the AKS module
  # Right Side: The Output value coming from the Network module
  # ---------------------------------------------------------
  vnet_subnet_id = module.my_network.aks_subnet_id 
}

```

### 🧠 The Breakdown

Look at that specific line: `vnet_subnet_id = module.my_network.aks_subnet_id`

1. `vnet_subnet_id`: This matches the `variable "vnet_subnet_id"` we wrote inside **modules/aks-cluster/variables.tf**.
2. `module.my_network`: This refers to the network module block we defined just above.
3. `aks_subnet_id`: This matches the `output "aks_subnet_id"` we wrote inside **modules/network/outputs.tf**.

So, Terraform reads this line and says: "Okay, I will take the output from `my_network` and feed it into the input of `my_aks`."

---

### 🧠 Final Review Quiz

This covers everything we've discussed.

You run `terraform apply`.

1. Terraform creates the VNet and Subnet first.
2. The Subnet gets an ID: `/subscriptions/.../subnets/subnet-aks`.
3. Terraform passes this ID to the AKS module.

**Scenario:** Suppose you made a typo in the Root `main.tf`:

```hcl
vnet_subnet_id = module.my_network.wrong_name

```

What happens when you run `terraform plan`?

A) Terraform runs the network module, sees there is no output named `wrong_name`, and fails during the run.
B) Terraform catches this immediately before running anything, saying "module.my_network does not have an output named 'wrong_name'".
C) Terraform creates the cluster but puts it in the default VPC instead.