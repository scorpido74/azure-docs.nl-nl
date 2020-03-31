---
title: Containers bouwen, testen en implementeren voor Azure Kubernetes Service met GitHub Actions
description: Meer informatie over het gebruik van GitHub Actions om uw container te implementeren in Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 5ee8ee4d2c9e225d82e58daffeef9e5f09e43e6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595362"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub-acties voor implementatie naar Kubernetes-service

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) biedt u de flexibiliteit om een workflow voor geautomatiseerde softwareontwikkeling te bouwen. De Kubernetes-actie [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) vergemakkelijkt implementaties naar Azure Kubernetes Service-clusters. De actie stelt de doel-AKS-clustercontext in, die kan worden gebruikt door andere acties zoals [azure/k8s-deploy,](https://github.com/Azure/k8s-deploy/tree/master) [azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) etc. of voer kubectl-opdrachten uit.

Een werkstroom wordt gedefinieerd door een YAML-bestand `/.github/workflows/` (.yml) in het pad in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Voor een werkstroom die is gericht op AKS, bevat het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | Inloggen bij een privécontainerregister (ACR) |
|**Ontwikkelen** | Build & push de containerafbeelding  |
|**Implementeren** | 1. Het beoogde AKS-cluster instellen |
| |2. Maak een generiek/docker-registergeheim in Kubernetes-cluster  |
||3. Implementeren in het Kubernetes-cluster|

## <a name="create-a-service-principal"></a>Een service-principal maken

U een [serviceprincipal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) maken met de opdracht [AZ Ad SP create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure CLI.](https://docs.microsoft.com/cli/azure/) U deze opdracht uitvoeren met [Azure Cloud Shell](https://shell.azure.com/) in de Azure-portal of door de knop Uitproberen **te** selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Vervang in de bovenstaande opdracht de tijdelijke aanduidingen door uw abonnements-ID en resourcegroep. De uitvoer is de bevoegdheid voor roltoewijzing die toegang biedt tot uw resource. De opdracht moet een JSON-object uitvoeren dat vergelijkbaar is met deze.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopieer dit JSON-object, dat u gebruiken om te verifiëren vanaf GitHub.

## <a name="configure-the-github-secrets"></a>De GitHub-geheimen configureren

Volg de stappen om de geheimen te configureren:

1. Blader in [GitHub](https://github.com/)naar uw opslagplaats en selecteer **Instellingen > Geheimen > Voeg een nieuw geheim toe.**

    ![geheimen](media/kubernetes-action/secrets.png)

2. Plak de inhoud `az cli` van de bovenstaande opdracht als de waarde van de geheime variabele. Bijvoorbeeld `AZURE_CREDENTIALS`.

3. Definieer ook de volgende aanvullende geheimen voor de referenties van het containerregister en stel deze in docker-inlogactie in. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. U ziet de geheimen zoals hieronder getoond eenmaal gedefinieerd.

    ![kubernetes-geheimen](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Een containerafbeelding maken en implementeren in Azure Kubernetes Service-cluster

Het bouwen en duwen van de `Azure/docker-login@v1` containerbeelden gebeurt met actie. Als u een containerafbeelding wilt implementeren op `Azure/k8s-deploy@v1` AKS, moet u de actie gebruiken. Deze actie heeft vijf parameters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **Namespace** | (Optioneel) Kies de doelnaamruimte van Kubernetes. Als de naamruimte niet is opgegeven, worden de opdrachten uitgevoerd in de standaardnaamruimte | 
| **Manifesteert** |  (Vereist) Pad naar de manifestbestanden, die worden gebruikt voor implementatie |
| **Afbeeldingen** | (Optioneel) Volledig gekwalificeerde resource-URL van de afbeelding(en) die moet worden gebruikt voor vervangingen op de manifestbestanden |
| **imagepullsecrets** | (Optioneel) Naam van een docker-register geheim dat al is ingesteld binnen het cluster. Elk van deze geheime namen wordt toegevoegd onder het veld PullSecrets van imagevoor de workloads in de invoermanifestbestanden |
| **kubectl-versie** | (Optioneel) Installeert een specifieke versie van kubectl binaire |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implementeren in Azure Kubernetes Service-cluster

End-to-end workflow voor het bouwen van containerafbeeldingen en implementeren in een Azure Kubernetes Service-cluster.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>Volgende stappen

U onze set acties vinden in verschillende opslagplaatsen op GitHub, elk met documentatie en voorbeelden om u te helpen GitHub voor CI/CD te gebruiken en uw apps te implementeren in Azure.

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [acties-werkstroom-voorbeelden](https://github.com/Azure/actions-workflow-samples)
