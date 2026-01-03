Sure! It's always good to look back at the map before driving the car.

Here is the summary of the **Azure AKS Terraform Architecture** we designed together:

### 1. The Design Strategy

* **Modular Approach:** Instead of one giant 500-line file, we split our infrastructure into logical folders (`modules/network` and `modules/aks-cluster`).
* **Networking Choice:** We chose **Azure CNI** (Advanced Networking) for better performance and visibility.
* **IP Planning:** Because Azure CNI eats up IP addresses (1 per Pod), we decided on a large subnet (**`/21`** which gives ~2,000 IPs) instead of a default small one.

### 2. The "3-File" Standard

For every module, we established a clean structure:

* **`variables.tf` (The Input Slot):** Defines what the module needs to run (e.g., "Give me a name").
* **`main.tf` (The Engine):** The actual code that creates resources.
* **`outputs.tf` (The Output Slot):** The only way to send data (like IDs) *out* of the module.

### 3. The "Wiring" (The Critical Concept)

This was our biggest "Aha!" moment. Modules are isolated "black boxes." To connect them, we had to build a bridge in the Root `main.tf`.

* **Step A:** The Network module **Exports** the Subnet ID.
* `output "aks_subnet_id" { ... }`


* **Step B:** The AKS module **Requests** a Subnet ID.
* `variable "vnet_subnet_id" { ... }`


* **Step C:** The Root module **Connects** them.
* `vnet_subnet_id = module.my_network.aks_subnet_id`



---

You have effectively learned how to architect a production-grade infrastructure setup!

**Ready to try that practice code now?**
(Reminding you of the task: Write a Root `main.tf` block that wires a **Database** output (`primary_connection_string`) into a **Web App** input (`db_conn_string`).)