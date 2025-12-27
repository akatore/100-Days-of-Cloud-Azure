For this task, create a Virtual Network (VNet) named `nautilus-vnet` and one subnet named `nautilus-subnet` within the VNet in the `East US` region. Make sure the `IPv4 address range` is `10.0.0.0/16`.

```json
~ ➜  az group list -o table
Name                          Location    Status
----------------------------  ----------  ---------
kml_rg_main-90a86e6b5d4b464e  westus      Succeeded

~ ➜  az network vnet create -g kml_rg_main-90a86e6b5d4b464e -n nautilus-vnet --address-prefix 10.0.0.0/16 --subnet-name nautilu
s-subnet --subnet-prefixes 10.0.0.0/24
{
  "newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
      ]
    },
    "enableDdosProtection": false,
    "etag": "W/\"fcfb124f-80a9-4aac-9431-fad80817871e\"",
    "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/kml_rg_main-90a86e6b5d4b464e/providers/Microsoft.Network/virtualNetworks/nautilus-vnet",
    "location": "westus",
    "name": "nautilus-vnet",
    "privateEndpointVNetPolicies": "Disabled",
    "provisioningState": "Succeeded",
    "resourceGroup": "kml_rg_main-90a86e6b5d4b464e",
    "resourceGuid": "3cae72ff-d651-42d3-bc37-18f3cc6740ab",
    "subnets": [
      {
        "addressPrefix": "10.0.0.0/24",
        "delegations": [],
        "etag": "W/\"fcfb124f-80a9-4aac-9431-fad80817871e\"",
        "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/kml_rg_main-90a86e6b5d4b464e/providers/Microsoft.Network/virtualNetworks/nautilus-vnet/subnets/nautilus-subnet",
        "name": "nautilus-subnet",
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled",
        "provisioningState": "Succeeded",
        "resourceGroup": "kml_rg_main-90a86e6b5d4b464e",
        "type": "Microsoft.Network/virtualNetworks/subnets"
      }
    ],
    "type": "Microsoft.Network/virtualNetworks",
    "virtualNetworkPeerings": []
  }

~ ➜  az network vnet show -g kml_rg_main-90a86e6b5d4b464e -n nautilus-vnet
{
  "addressSpace": {
    "addressPrefixes": [
      "10.0.0.0/16"
    ]
  },
  "enableDdosProtection": false,
  "etag": "W/\"fcfb124f-80a9-4aac-9431-fad80817871e\"",
  "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/kml_rg_main-90a86e6b5d4b464e/providers/Microsoft.Network/virtualNetworks/nautilus-vnet",
  "location": "westus",
  "name": "nautilus-vnet",
  "privateEndpointVNetPolicies": "Disabled",
  "provisioningState": "Succeeded",
  "resourceGroup": "kml_rg_main-90a86e6b5d4b464e",
  "resourceGuid": "3cae72ff-d651-42d3-bc37-18f3cc6740ab",
  "subnets": [
    {
      "addressPrefix": "10.0.0.0/24",
      "delegations": [],
      "etag": "W/\"fcfb124f-80a9-4aac-9431-fad80817871e\"",
      "id": "/subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/kml_rg_main-90a86e6b5d4b464e/providers/Microsoft.Network/virtualNetworks/nautilus-vnet/subnets/nautilus-subnet",
      "name": "nautilus-subnet",
      "privateEndpointNetworkPolicies": "Disabled",
      "privateLinkServiceNetworkPolicies": "Enabled",
      "provisioningState": "Succeeded",
      "resourceGroup": "kml_rg_main-90a86e6b5d4b464e",
      "type": "Microsoft.Network/virtualNetworks/subnets"
    }
  ],
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": []
}  
}```