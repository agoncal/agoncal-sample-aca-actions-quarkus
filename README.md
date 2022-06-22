# agoncal-sample-aca-actions-quarkus

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

Create a GitHub action

```shell
az containerapp github-action add --name agoncal-sample-aca-actions-quarkus \
                                  --repo-url https://github.com/agoncal/agoncal-sample-aca-actions-quarkus \
                                  --resource-group "$RESOURCE_GROUP" 
                                  [--branch]
                                  [--context-path]
                                  [--image]
                                  [--login-with-github]
                                  [--registry-password]
                                  [--registry-url]
                                  [--registry-username]
                                  [--service-principal-client-id]
                                  [--service-principal-client-secret]
                                  [--service-principal-tenant-id]
                                  [--token]
```
