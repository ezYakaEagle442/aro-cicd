## Plan IP addressing for your cluster

See  :

- [Public IP sku comparison](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)
- [https://docs.openshift.com/aro/4/networking/understanding-networking.html](https://docs.openshift.com/aro/4/networking/understanding-networking.html)
- [https://docs.microsoft.com/fr-fr/cli/azure/aro?view=azure-cli-latest](https://docs.microsoft.com/fr-fr/cli/azure/aro?view=azure-cli-latest)
- [https://docs.openshift.com/aro/4/networking/openshift_sdn/configuring-egress-firewall.html](https://docs.openshift.com/aro/4/networking/openshift_sdn/configuring-egress-firewall.html)
- [https://docs.openshift.com/aro/4/networking/configuring_ingress_cluster_traffic/overview-traffic.html](https://docs.openshift.com/aro/4/networking/configuring_ingress_cluster_traffic/overview-traffic.html)

```sh
# https://www.ipaddressguide.com/cidr


``` 

## Create Networks

```sh
# ARO nodes VNet & Subnet
az network vnet create --name $vnet_name --resource-group $rg_name --address-prefixes 172.16.0.0/22 --location $location
az network vnet subnet create --name $master_subnet_name --address-prefixes 172.16.1.0/24 --vnet-name $vnet_name --resource-group $rg_name --service-endpoints Microsoft.ContainerRegistry
az network vnet subnet create --name $worker_subnet_name --address-prefixes 172.16.2.0/24 --vnet-name $vnet_name -g $rg_name --service-endpoints Microsoft.ContainerRegistry

vnet_id=$(az network vnet show --resource-group $rg_name --name $vnet_name --query id -o tsv)
echo "VNet Id :" $vnet_id	

master_subnet_id=$(az network vnet subnet show --name $master_subnet_name --vnet-name $vnet_name  -g $rg_name --query id -o tsv)
echo "Master Subnet Id :" $master_subnet_id	

worker_subnet_id=$(az network vnet subnet show --name $worker_subnet_name --vnet-name $vnet_name -g $rg_name --query id -o tsv)
echo "Worker Subnet Id :" $worker_subnet_id	

# https://docs.microsoft.com/en-us/azure/private-link/create-private-link-service-cli#disable-private-link-service-network-policies-on-subnet
az network vnet subnet update --name $master_subnet_name --vnet-name $vnet_name --disable-private-link-service-network-policies true -g $rg_name


```


### Setup NSG 
```sh

# https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg
# https://docs.microsoft.com/en-us/azure/bastion/bastion-nsg
# NSG sample : https://user-images.githubusercontent.com/47132998/69514141-4f55d380-0f70-11ea-980e-2094bd57de20.png
# https://github.com/Azure/azure-quickstart-templates/blob/master/101-azure-bastion-nsg/azuredeploy.json

b_nsg="bastion-nsg-management"
az network nsg create --name $b_nsg -g $rg_bastion_name --location $location

az network nsg rule create --access Allow --destination-port-range 22 --source-address-prefixes Internet --name "Allow SSH from Internet" --nsg-name $b_nsg -g $rg_bastion_name --priority 100

az network nsg rule create --access Allow --destination-port-range 3389 --source-address-prefixes Internet --name "Allow RDP from Internet" --nsg-name $b_nsg -g $rg_bastion_name --priority 110

az network vnet subnet update --name ManagementSubnet --network-security-group $b_nsg --vnet-name $vnet_bastion_name -g $rg_bastion_name

```
