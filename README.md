# agoncal-sample-aca-actions-quarkus

Make sure you have Azure CLI installed. If it already is installed, make sure you update it to the latest version:

```shell
az upgrade
```

Log in to Azure and add the Container Apps extension

```shell
az login

az extension add --name containerapp --upgrade
```

Set the environment variables

```shell
RESOURCE_GROUP="rg-aca-actions-quarkus"
LOCATION="eastus2"
TAG="aca-actions-quarkus"
```

Create a Resource Group

```shell
az group create \
  --name "$RESOURCE_GROUP" \
  --location "$LOCATION" \
  --tags system="$TAG"
```

Service principal client ID, secret and tenant ID

```shell
az ad sp create-for-rbac --name rbac-aca-actions-quarkus \
  --role contributor \
  --scopes /subscriptions/{{subscription}}/resourceGroups/$RESOURCE_GROUP
  --sdk-auth


Create a GitHub action

```shell
az containerapp github-action add --name agoncal-sample-aca-actions-quarkus \
                                  --repo-url https://github.com/agoncal/agoncal-sample-aca-actions-quarkus \
                                  --resource-group "$RESOURCE_GROUP" \
                                  --login-with-github

[--branch]
                                  [--context-path]
                                  [--image]
                                  [--registry-password]
                                  [--registry-url]
                                  [--registry-username]
                                  [--service-principal-client-id]
                                  [--service-principal-client-secret]
                                  [--service-principal-tenant-id]
                                  [--token]
```
