---
title: Containers bouwen, testen en implementeren in azure Kubernetes service met GitHub-acties
description: Meer informatie over het gebruik van GitHub-acties voor het implementeren van uw container in Kubernetes
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: cc2d6df952b2e0aa9b9f4d4e1dcb4859a5bb3790
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130527"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub acties voor het implementeren van de Kubernetes-service

[Github-acties](https://help.github.com/en/articles/about-github-actions) bieden u de flexibiliteit om een geautomatiseerde werk stroom voor de levens cyclus van software ontwikkeling te bouwen. De Kubernetes-actie [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) implementaties van Azure Kubernetes-Service clusters vergemakkelijken. Met deze actie wordt de doel context van de AKS-cluster ingesteld, die kan worden gebruikt door andere acties zoals [Azure/K8S-Deploy](https://github.com/Azure/k8s-deploy/tree/master), [Azure/K8S-Create-Secret](https://github.com/Azure/k8s-create-secret/tree/master) , enzovoort. u kunt ook kubectl-opdrachten uitvoeren.

> [!IMPORTANT]
> GitHub-acties zijn momenteel in een bèta versie. U moet [zich eerst aanmelden om lid te worden van het voor beeld](https://github.com/features/actions) met behulp van uw github-account.
> 

Een werk stroom wordt gedefinieerd door een YAML-bestand (. yml) in het pad `/.github/workflows/` in uw opslag plaats. Deze definitie bevat de verschillende stappen en para meters die deel uitmaken van de werk stroom.

Voor een werk stroom gericht AKS heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | Aanmelden bij een persoonlijk container register (ACR) |
|**PE** | De container installatie kopie bouwen & pushen  |
|**Implementeren** | 1. Stel het doel-AKS-cluster in |
| |2. Maak een algemeen/docker-register geheim in het Kubernetes-cluster  |
||3. implementeren naar het Kubernetes-cluster|

## <a name="create-a-service-principal"></a>Een service-principal maken

U kunt een [Service-Principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) maken met behulp van de opdracht [AZ AD SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure cli](https://docs.microsoft.com/cli/azure/). U kunt deze opdracht uitvoeren met behulp van [Azure Cloud shell](https://shell.azure.com/) in het Azure portal of door de knop **try it** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Vervang in de bovenstaande opdracht de tijdelijke aanduidingen door de abonnements-ID en de resource groep. De uitvoer is de roltoewijzings referenties die toegang bieden tot uw resource. De opdracht moet een JSON-object uitvoeren dat er ongeveer als volgt uitziet.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopieer dit JSON-object, dat u kunt gebruiken om te verifiëren vanuit GitHub.

## <a name="configure-the-github-secrets"></a>De GitHub-geheimen configureren

Volg de stappen voor het configureren van de geheimen:

1. Blader in [github](https://github.com/)naar uw opslag plaats, selecteer **instellingen > geheimen > een nieuw geheim toe te voegen**.

    ![geheimen](media/kubernetes-action/secrets.png)

2. Plak de inhoud van de bovenstaande `az cli` opdracht als waarde voor de geheime variabele. Bijvoorbeeld `AZURE_CREDENTIALS`.

3. U kunt ook de volgende extra geheimen voor de container register referenties definiëren en instellen in de aanmeldings actie voor docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. De geheimen worden weer gegeven, zoals hieronder is gedefinieerd.

    ![kubernetes-geheimen](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Een container installatie kopie bouwen en implementeren in azure Kubernetes service-cluster

Het bouwen en pushen van de container installatie kopieën wordt uitgevoerd met `Azure/docker-login@v1` actie. Als u een container installatie kopie wilt implementeren in AKS, moet u de actie `Azure/k8s-deploy@v1` gebruiken. Deze actie heeft vijf para meters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **naam ruimte** | Beschrijving Kies de doel-Kubernetes naam ruimte. Als de naam ruimte niet wordt gegeven, worden de opdrachten uitgevoerd in de standaard naam ruimte | 
| **manifesten** |  Lang Pad naar de manifest bestanden die worden gebruikt voor implementatie |
| **installatie kopieën** | Beschrijving Volledig gekwalificeerde resource-URL van de afbeelding (en) die moet worden gebruikt voor vervangingen in de manifest bestanden |
| **imagepullsecrets** | Beschrijving Naam van een docker-REGI ster dat al in het cluster is ingesteld. Elk van deze geheime namen wordt toegevoegd onder het veld imagePullSecrets voor de werk belastingen die zijn gevonden in de invoer manifest bestanden |
| **kubectl-versie** | Beschrijving Hiermee wordt een specifieke versie van kubectl binary geïnstalleerd |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implementeren naar Azure Kubernetes service-cluster

End-to-end werk stroom voor het bouwen van container installatie kopieën en het implementeren van een Azure Kubernetes service-cluster.

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

U vindt onze set acties in verschillende opslag plaatsen op GitHub, elk met documentatie en voor beelden die u kunnen helpen bij het gebruik van GitHub voor CI/CD en uw apps te implementeren in Azure.

- [Setup-kubectl](https://github.com/Azure/setup-kubectl)

- [K8S-set-context](https://github.com/Azure/k8s-set-context)

- [AKS-set-context](https://github.com/Azure/aks-set-context)

- [K8S-maken-geheim](https://github.com/Azure/k8s-create-secret)

- [K8S-implementeren](https://github.com/Azure/k8s-deploy)

- [webapps-container-implementeren](https://github.com/Azure/webapps-container-deploy)

- [acties-werk stroom-voor beelden](https://github.com/Azure/actions-workflow-samples)
