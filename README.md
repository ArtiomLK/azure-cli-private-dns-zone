# Azure Private DNS Zone (pdnsz)

## Instructions

```bash
# ---
# Main Vars
# ---
project="security";                                 echo $project
env="prod";                                         echo $env
project_rg="rg-$project-$env";                      echo $project_rg
l="eastus";                                         echo $l
tags="env=$env project=$project";                   echo $tags

# ---
# NETWORK TOPOLOGY
# ---
vnet_n="vnet-security-$env";                        echo $vnet_n

# ---
# Private DNS Zone
# ---
pdnsz_n="privatelink.azurecr.io"                    echo $pdnsz_n
pdnsz_vnet_n="vnet-name"                            echo $pdnsz_vnet_n
dns_link="dnslink-$project";                        echo $dns_link
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
--record-set-name "192.168.0.1" \
--zone-name $pdnsz_n \
--resource-group $project_rg \
--ipv4-address $REGISTRY_PRIVATE_IP
```
