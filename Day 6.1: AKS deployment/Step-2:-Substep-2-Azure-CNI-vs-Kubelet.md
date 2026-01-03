You are absolutely right to pick **C (`10.1.0.0/21`)**! 🌟 That provides around 2,000 IPs, which is plenty of breathing room for a standard cluster.

Regarding your question about the overlap between `10.1.0.0/16` and `10.2.0.0/16`:

**They do NOT overlap, and that is exactly why B was incorrect.**

Think of the IP address like a street address: `10.[Street].[House].[Apartment]`.

* **VNet (`10.1.0.0/16`):** You own everything on **Street #1**.
* Range: `10.1.0.0` to `10.1.255.255`.


* **Option B (`10.2.0.0/16`):** This is everything on **Street #2**.

Since your VNet only owns "Street #1", you cannot put a subnet from "Street #2" inside it. They are neighbors, but completely separate properties.

---

### Step 2, Substep 2: Azure CNI vs. Kubenet

Before we write the Terraform code, there is one major architectural decision you have to make that changes your network requirements: **The Network Plugin.**

This decides how your Kubernetes pods get their IP addresses.

| Feature | **Azure CNI** (Advanced/Default for many) | **Kubenet** (Basic) |
| --- | --- | --- |
| **How it works** | Every single Pod gets a real IP from your VNet subnet. | Pods use a fake internal IP; only Nodes get real VNet IPs. |
| **IP Consumption** | **High**. If you run 30 pods on a node, that node eats 30+ IPs from your subnet. | **Low**. You only need IPs for the Nodes themselves. |
| **Performance** | Slightly faster (direct connection). | Slightly slower (requires NAT). |
| **Terraform Impact** | You need a **larger** Subnet. | You can use a smaller Subnet. |

**Why this matters for your IaC:**
If you choose **Azure CNI**, you must ensure your Terraform creates a subnet large enough to handle `(Max Pods per Node) * (Max Nodes)`. If you run out of IPs, your pods will fail to start.

**Recommendation:** For learning and enterprise "production-ready" setups, we usually learn **Azure CNI** first because it integrates better with other Azure services, even though it requires more careful IP planning.

---

### 🧠 Checkpoint

Let's assume we are going to use **Azure CNI**.

If we have a subnet with **250** usable IPs, and we configure our AKS cluster to allow a maximum of **30 pods** per node.

Roughly how many Nodes (VMs) can we fit in that subnet?
*(Hint: Each node takes 1 IP for itself, plus 30 IPs reserved for its pods.)*

A) About 250 Nodes
B) About 8 Nodes
C) About 30 Nodes

Next: [Step 3: Writing the Network Module](./Step-3:-Writing-the-Network-Module.md)