### Step 1: Understanding Terraform Modules

Great! Let's start with the foundation. In Terraform, a **Module** is just a container for multiple resources that are used together.

Think of a module like a **function** in Python or Java.

* It takes inputs (arguments).
* It performs actions (creates resources like VMs or VNets).
* It returns outputs (like an IP address or Resource ID).

#### The "Standard" Module Structure

When you write a module (for example, to build your Network), you typically split the code into three specific files. This keeps things organized.

| File Name | Purpose | Analogy (Programming) |
| --- | --- | --- |
| **`main.tf`** | Contains the actual resource code (e.g., `resource "azurerm_subnet" ...`). | The Function Body |
| **`variables.tf`** | Defines the values the user must provide (e.g., "What is the VNet address space?"). | Function Arguments |
| **`outputs.tf`** | Defines the values the module sends back after it runs (e.g., "Here is the Subnet ID created"). | Return Values |

**Visualizing the structure:**
If you were building an AKS setup, your folder structure would look like this:

```text
my-infrastructure/
├── main.tf           # (Root) Calls the modules
├── modules/
│   ├── network/      # Child Module for Network
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── aks-cluster/  # Child Module for AKS
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf

```

This way, your root `main.tf` stays clean. instead of 500 lines of code, it just says "Run the Network module" and "Run the AKS module."

---

### 🧠 Quick Check

Let's make sure this structure clicks.

**Scenario:** You are writing the `network` module. You want to make sure that whoever uses this module *must* provide a specific name for the Resource Group.

**Question:** In which of the three files (`main.tf`, `variables.tf`, or `outputs.tf`) would you define that requirement?

Next: [Step 2: Designing the Network Layer](./Step-2:-Designing-the-Network-Layer.md)

