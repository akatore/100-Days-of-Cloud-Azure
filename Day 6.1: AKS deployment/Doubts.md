we didn't used service principal for the AKS cluster creation, right?



That is correct! We did **not** use a Service Principal.

In the AKS module code (Step 4), we used this block:

```hcl
  identity {
    type = "SystemAssigned"
  }

```

This uses a **Managed Identity**.

**Why did we choose this over a Service Principal?**

* **Old Way (Service Principal):** You have to create a "user," give it a password (client secret), and crucially, **you have to rotate that password** every 1-2 years. If you forget, your cluster breaks.
* **New Way (Managed Identity):** Azure manages the identity for you. There are no passwords to manage or rotate. It is much safer and easier for learning (and production!).

---