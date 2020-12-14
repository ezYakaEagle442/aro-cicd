# Set-up environment variables

<span style="color:red">/!\ IMPORTANT </span> : your **appName** & **cluster_name** values MUST BE UNIQUE

## ARO Core variables
```sh

# az account list-locations : francecentral | northeurope | westeurope | eastus2
location=westeurope 
echo "location is : " $location 

appName="arocicd" 
echo "appName is : " $appName 

rg_name="rg-${appName}-${location}" 
echo "ARO RG name:" $rg_name 

rg_cluster="rg-managed-cluster-${appName}-${location}" 
echo "ARO Cluster RG name:" $cluster_rg_name 

cluster_name="aro-${appName}-101" #aro-<App Name>-<Environment>-<###>
echo "Cluster name:" $cluster_name

# ARO VNet & Subnet
vnet_name="vnet-${appName}"
echo "VNet Name :" $vnet_name

master_subnet_name="snet-master-${appName}"
echo "ARO Master Subnet Name :" $master_subnet_name

worker_subnet_name="snet-worker-${appName}"
echo "ARO Workers Subnet Name :" $worker_subnet_name

pod_cidr=10.42.0.0/18 # must be /18 or larger https://docs.openshift.com/aro/4/networking/understanding-networking.html
echo "Pod CIDR is : " $pod_cidr 

svc_cidr=10.21.0.0/18 # must be /18 or larger
echo "service CIDR is : " $svc_cidr 

# Private or Public : https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/aro/_validators.py#L180
apiserver_visibility="Public"
echo "apiserver visibility is : " $apiserver_visibility 

ingress_visibility="Public"
echo "ingress visibility is : " $ingress_visibility 

ssh_passphrase="<your secret>"
ssh_key="${appName}-key" # id_rsa

dns_zone="cloudapp.azure.com"
echo "DNS Zone is : " $dns_zone

app_dns_zone="kissmyapp.${location}.${dns_zone}"
echo "App DNS zone " $app_dns_zone

custom_dns="akshandsonlabs.com"
echo "Custom DNS is : " $custom_dns

# Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only
storage_name="stwe""${appName,,}"
echo "Storage name:" $storage_name

target_namespace="staging"
echo "Target namespace:" $target_namespace

vault_secret="NoSugarNoStar" 
echo "Vault secret:" $vault_secret 

git_url="https://github.com/your-project/xxx.git"
echo "Project git repo URL : " $git_url 

git_url_springboot="https://github.com/spring-projects/spring-petclinic.git"
echo "Project git repo URL : " $git_url_springboot 

```
