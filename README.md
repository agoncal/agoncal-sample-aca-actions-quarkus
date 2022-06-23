# agoncal-sample-aca-actions-quarkus

Make sure you have Azure CLI installed. If it already is installed, make sure you update it to the latest version:

```shell
az upgrade
```

Log in to Azure and add the Container Apps extension

```shell
az login

az extension add --name containerapp --upgrade
az extension add --name deploy-to-azure --upgrade
```

Set the environment variables

```shell
RESOURCE_GROUP="rg-aca-actions-quarkus"
LOCATION="eastus2"
CONTAINERAPPS_ENV="env-aca-actions-quarkus"
TAG="aca-actions-quarkus"
LOG_ANALYTICS_WORKSPACE="workspace-aca-actions-quarkus"
RBAC="rbac-aca-actions-quarkus"
REGISTRY="registryacaactionsquarkus"
```

Get the subscription ID

```shell
SUBSCRIPTION_ID=`az account show \
  --query id \
  --output tsv`
  
echo $SUBSCRIPTION_ID  
```

Create a Resource Group

```shell
az group create \
  --name "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --tags system="$TAG"
```

Create a Log Analytics workspace with the following command:

```shell
az monitor log-analytics workspace create \
  --resource-group "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --tags system="$TAG" \
  --workspace-name "$LOG_ANALYTICS_WORKSPACE"
```

Let's also retrieve the Log Analytics Client ID and client secret and store them in environment variables:

```shell
LOG_ANALYTICS_WORKSPACE_CLIENT_ID=`az monitor log-analytics workspace show  \
  --resource-group "$RESOURCE_GROUP" \
  --workspace-name "$LOG_ANALYTICS_WORKSPACE" \
  --query customerId  \
  --output tsv | tr -d '[:space:]'`

echo $LOG_ANALYTICS_WORKSPACE_CLIENT_ID

LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET=`az monitor log-analytics workspace get-shared-keys \
  --resource-group "$RESOURCE_GROUP" \
  --workspace-name "$LOG_ANALYTICS_WORKSPACE" \
  --query primarySharedKey \
  --output tsv | tr -d '[:space:]'`

echo $LOG_ANALYTICS_WORKSPACE_CLIENT_SECRET
```

First, let's created an Azure Container Registry with the following command:

```shell
az acr create \
  --resource-group "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --tags system="$TAG" \
  --name "$REGISTRY" \
  --workspace "$LOG_ANALYTICS_WORKSPACE" \
  --sku Standard \
  --admin-enabled true
```

```shell
REGISTRY_URL=$(az acr show \
  --resource-group "$RESOURCE_GROUP" \
  --name "$REGISTRY" \
  --query "loginServer" \
  --output tsv)
```


Service principal client ID, secret and tenant ID

```shell
az ad sp create-for-rbac \
  --name "$RBAC" \
  --role contributor \
  --scopes /subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP
```

Create a GitHub action

```shell
az containe```
rapp github-action add \
  --name agoncal-sample-aca-actions-quarkus \
  --repo-url https://github.com/agoncal/agoncal-sample-aca-actions-quarkus \
  --resource-group "$RESOURCE_GROUP" \
  --login-with-github \
  --service-principal-client-id {{client-id}} \
  --service-principal-client-secret {{client-secret}} \
  --service-principal-tenant-id {{tenant-id}} \
  --registry-url $REGISTRY_URL
```

## Azure Container Apps UP

Create a Quarkus project that you push to GitHub

```shell
https://code.quarkus.io/?g=io.quarkus.workshop.superheroes&a=villains-app&e=smallrye-openapi&e=resteasy-reactive&e=hibernate-orm-panache&e=hibernate-validator&e=jdbc-postgresql&e=smallrye-health
```

```shell
az containerapp up \
  --name villains-app \
  --environment "$CONTAINERAPPS_ENV" \
  --repo https://github.com/agoncal/villains-app \
  --resource-group "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --ingress external \
  --context-path src/main/docker \
  --logs-workspace-id "$LOG_ANALYTICS_WORKSPACE"

                   [--branch]
                   [--browse]
                   [--env-vars]
                   [--image]
                   [--ingress {external, internal}]
                   [--logs-workspace-id]
                   [--logs-workspace-key]
                   [--registry-password]
                   [--registry-server]
                   [--registry-username]
                   [--service-principal-client-id]
                   [--service-principal-client-secret]
                   [--service-principal-tenant-id]
                   [--source]
                   [--target-port]
                   [--token]
```

Get the GitHub token

```shell
gh auth status --show-token
```

Set the `GH_TOKEN` variable with the value of the GitHub token
