## Practice as if you are the worst, Perform as if you are the best.

Create a Virtual Network (VNet) named `xfusion-vnet` in the `East US` region with `192.168.0.0/24` IPv4 CIDR.

```json
~ ➜  az network vnet create -n xfusion-vnet -g kml_rg_main-7110462309124cf4 -l eastus --address-prefixes 192.168.0.0/24
{
  "newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "192.168.0.0/24"
      ]
    },
    "enableDdosProtection": false,
    "etag": "W/\"4ad16ec9-37cd-466e-a94d-93e6b7db8171\"",
    "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/kml_rg_main-7110462309124cf4/providers/Microsoft.Network/virtualNetworks/xfusion-vnet",
    "location": "eastus",
    "name": "xfusion-vnet",
    "privateEndpointVNetPolicies": "Disabled",
    "provisioningState": "Succeeded",
    "resourceGroup": "kml_rg_main-7110462309124cf4",
    "resourceGuid": "1d509388-2db3-4d75-aded-df750b6d17b3",
    "subnets": [],
    "type": "Microsoft.Network/virtualNetworks",
    "virtualNetworkPeerings": []
  }
}
```
