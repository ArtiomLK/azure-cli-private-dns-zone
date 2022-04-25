# Azure Private DNS Zone (pdnsz)

## Instructions

```bash
# ---
# Main Vars
# ---
project="dns";                                      echo $project
env="sb";                                           echo $env
l="eastus";                                         echo $l
project_rg="rg-$project-$env-$l";                   echo $project_rg
tags="env=$env project=$project";                   echo $tags

# ---
# NETWORK TOPOLOGY
# ---
vnet_n="vnet-$project-$env-$l";                     echo $vnet_n

# ---
# Private DNS Zone
# ---
pdnsz_n="privatelink.azurewebsites.net";            echo $pdnsz_n
pdnsz_vnet_n="vnet-name";                           echo $pdnsz_vnet_n
dns_link="dnslink-$project";                        echo $dns_link
```

```bash

# If required create a Resource Group
az group create \
--name $project_rg \
--location $l \
--tags $tags

# If required create a sample Virtual Network
az network vnet create \
--name $vnet_n \
--resource-group $project_rg \
--address-prefixes '192.168.0.0/25' \
--location $l \
--tags $tags
```

```bash
# Private DNS Zone
az network private-dns zone create \
--resource-group $project_rg \
--name $pdnsz_n \
--tags $tags

# Create an association link to associate your private zone with the virtual network
az network private-dns link vnet create \
--resource-group $project_rg \
--zone-name $pdnsz_n \
--name $dns_link \
--virtual-network $vnet_n \
--registration-enabled false \
--tags $tags

# Add as many records as required:
az network private-dns record-set a add-record \
--record-set-name "app-safe-svc-n-dev-eastus2" \
--zone-name $pdnsz_n \
--resource-group $project_rg \
--ipv4-address "192.168.0.1"

# Add as many records as required:
az network private-dns record-set a add-record \
--record-set-name "app-safe-svc-n-dev-eastus2.scm" \
--zone-name $pdnsz_n \
--resource-group $project_rg \
--ipv4-address "192.168.0.1"
```
