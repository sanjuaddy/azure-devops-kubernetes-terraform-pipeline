## Azure DevOps
## Azure Marketplace
## Terraform 1 (https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks)
## Terraform 2 (https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform)

## IAAC with Kubernetes on Azure with Azure DevOps - Pipelines
Step 01 - Review Terraform Configuration for Azure Kubernetes Cluster Creation
Step 02 - Setting up Client ID, Secret and Public Key for Azure Kubernetes Cluster Creation
Step 03 - Creating Azure DevOps Pipeline for Azure Kubernetes Cluster IAAC
Step 04 - Performing Terraform apply to create Azure Kubernetes Cluster in Azure DevOps
Step 05 - Connecting to Azure Kubernetes Cluster using Azure CLI
Step 06 - Creating Azure DevOps Pipeline for Deploying Microservice to Azure Kubernetes
Step 07 - Creating V2 and Enable Build and Push of Docker Image
Step 08 - Performing Terraform destroy to delete Azure Kubernetes Cluster in Azure DevOps

## Azure Kubernetes Cluster
Pre-requisites

Service Account
SSH Public Key
# Create Service Account To Create Azure K8S Cluster using Terraform
az login
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<<azure_subscription_id>>"

# Create Public Key for SSH Access
ssh-keygen -m PEM -t rsa -b 4096 # PEM - Privacy Enhanced Mail - Certificate Format RSA- Encryption Algorithm

# ls /Users/sanjayk/.ssh/id_rsa.pub

# Get Cluster Credentials
az aks get-credentials --name <<MyManagedCluster>> --resource-group <<MyResourceGroup>>

## Manually setting up from local machine
Create Service Account For Your Subscription To Create Azure K8S Cluster using Terraform
az login
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<<azure_subscription_id>>"
export TF_VAR_client_id=<<service_account_appId>>
export TF_VAR_client_secret=<<service_account_password>>
Create Public Key for SSH Access
ssh-keygen -m PEM -t rsa -b 4096 # PEM - Privacy Enhanced Mail - Certificate Format RSA- Encryption Algorithm
ls /Users/sanjayk/.ssh/id_rsa.pub
Create Resource Group, Storage Account and Storage Container
az group create -l westeurope -n In28minutesK8sResourceGroup
az storage account create -n TestingstarK8sStorageAccount -g TestingstarK8sResourceGroup -l westeurope --sku Standard_LRS
az storage container create -n devterraformstatestorage --account-name <<storage_account_name>> --account-key <<storage_account_key>>

## Execute Terraform Commands
# comment backend
terraform init
terraform apply
# add backend
# terraform init with backend
terraform init -backend-config="storage_account_name=<<storage_account_name>>" -backend-config="container_name=<<storage_container_name>>" -backend-config="access_key=<<storage_account_key>>" -backend-config="key=<<k8s.environment.tfstate>>"

## Set Up kubectl
terraform output kube_config>~/.kube/config
Launch up
kubectl proxy
open 'http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/overview?namespace=default'

# Currency Exchange Micro Service

Run in.testingstar.microservices.currencyconversionservice.CurrencyConversionServiceApplicationH2 as a Java Application.

## Resources

- http://localhost:8000/currency-exchange/from/USD/to/INR

```json
{
  "id": 10001,
  "from": "USD",
  "to": "INR",
  "conversionMultiple": 65.00,
  "environmentInfo": "NA"
}
```

## H2 Console

- http://localhost:8000/h2-console
- Use `jdbc:h2:mem:testdb` as JDBC URL


## Notes

## Tables Created
```
create table exchange_value 
(
	id bigint not null, 
	conversion_multiple decimal(19,2), 
	currency_from varchar(255), 
	currency_to varchar(255), 
	primary key (id)
)
```

## Containerization

### Troubleshooting

- Problem - Caused by: com.spotify.docker.client.shaded.javax.ws.rs.ProcessingException: java.io.IOException: No such file or directory
- Solution - Check if docker is up and running!
- Problem - Error creating the Docker image on MacOS - java.io.IOException: Cannot run program “docker-credential-osxkeychain”: error=2, No such file or directory
- Solution - https://medium.com/@dakshika/error-creating-the-docker-image-on-macos-wso2-enterprise-integrator-tooling-dfb5b537b44e

### Creating Container

- mvn package

### Running Container

#### Basic
```
docker container run --publish 8000:8000 testingstar/currency-exchange:0.0.1-SNAPSHOT
```
