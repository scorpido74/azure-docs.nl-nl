---
title: Aangepaste container-CI/CD van GitHub-acties
description: Meer informatie over het gebruik van GitHub-acties voor het implementeren van uw aangepaste Linux-container voor App Service van een CI/CD-pijp lijn.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 21019917f37ad95dc15056daa51b3d9e53ec06fa
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083111"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Een aangepaste container implementeren op App Service met behulp van GitHub-acties

[Github-acties](https://help.github.com/en/articles/about-github-actions) bieden u de flexibiliteit om een geautomatiseerde werk stroom voor de levens cyclus van software ontwikkeling te bouwen. Met de [Azure app service actie voor containers](https://github.com/Azure/webapps-container-deploy)kunt u uw werk stroom automatiseren om aangepaste containers [app service](overview.md) te implementeren met behulp van github-acties.

> [!IMPORTANT]
> GitHub-acties zijn momenteel in een bèta versie. U moet [zich eerst aanmelden om lid te worden van het voor beeld](https://github.com/features/actions) met behulp van uw github-account.
> 

Een werk stroom wordt gedefinieerd door een YAML-bestand (. yml) in het `/.github/workflows/` pad in uw opslag plaats. Deze definitie bevat de verschillende stappen en para meters die deel uitmaken van de werk stroom.

Voor een Azure App Service container werk stroom heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-geheim. |
|**Ontwikkelen** | 1. Stel de omgeving in. <br /> 2. bouw de container installatie kopie. |
|**Implementeren** | 1. Implementeer de container installatie kopie. |

## <a name="create-a-service-principal"></a>Een service-principal maken

U kunt een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met behulp van de opdracht [AZ AD SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure cli](https://docs.microsoft.com/cli/azure/). U kunt deze opdracht uitvoeren met behulp van [Azure Cloud shell](https://shell.azure.com/) in het Azure portal of door de knop **try it** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

In het bovenstaande voor beeld vervangt u de tijdelijke aanduidingen door de abonnements-ID en de naam van de resource groep. De uitvoer is een JSON-object met de roltoewijzings referenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. U kunt de scope in de bovenstaande AZ CLI-opdracht beperken tot de specifieke App Service-app en de Azure Container Registry waarnaar de container installatie kopieën worden gepusht.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

Plak de inhoud van de JSON-uitvoer van [een service-principal maken](#create-a-service-principal) als waarde voor de geheime variabele. Geef het geheim de naam zoals `AZURE_CREDENTIALS` .

Wanneer u het werk stroom bestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldings actie. Bijvoorbeeld:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

U kunt ook de volgende extra geheimen voor de container register referenties definiëren en instellen in de aanmeldings actie voor docker.

- REGISTRY_USERNAME
- REGISTRY_PASSWORD

## <a name="build-the-container-image"></a>De container installatie kopie bouwen

In het volgende voor beeld wordt een deel van de werk stroom weer gegeven dat de docker-installatie kopie bouwt.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Implementeren in een App Service-container

Gebruik de actie om uw installatie kopie te implementeren in een aangepaste container in App Service `azure/webapps-container-deploy@v1` . Deze actie heeft vijf para meters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-naam** | Lang De naam van de App Service-app | 
| **sleuf naam** | Beschrijving Voer een bestaande sleuf in, behalve de productie sleuf |
| **installatie kopieën** | Lang Geef de volledig gekwalificeerde naam van de container installatie kopie (n) op. Bijvoorbeeld ' myregistry.azurecr.io/nginx:latest ' of ' python: 3.7.2-Alpine/'. Voor een app met meerdere containers kunnen namen van meerdere container installatie kopieën worden gegeven (gescheiden door meerdere regels) |
| **configuratie-bestand** | Beschrijving Pad van het docker-bestand. Moet een volledig gekwalificeerd pad of relatief ten opzichte van de standaard werkmap zijn. Vereist voor apps met meerdere containers. |
| **container-opdracht** | Beschrijving Voer de opstart opdracht in. Voor ex. DotNet-run-of DotNet-filename.dll |

Hieronder ziet u de voorbeeld werk stroom voor het maken en implementeren van een Node.js-app in een aangepaste container in App Service. U ziet hoe de `creds` invoer verwijst naar het `AZURE_CREDENTIALS` geheim dat u eerder hebt gemaakt.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Volgende stappen

U vindt onze set acties die zijn gegroepeerd in verschillende opslag plaatsen op GitHub, elk met documentatie en voor beelden die u kunnen helpen bij het gebruik van GitHub voor CI/CD en uw apps te implementeren in Azure.

- [Werk stromen voor acties die moeten worden geïmplementeerd in azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp voor containers](https://github.com/Azure/webapps-container-deploy)

- [Aanmelden/afmelden bij docker](https://github.com/Azure/docker-login)

- [Gebeurtenissen die workflows activeren](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implementeren](https://github.com/Azure/k8s-deploy)

- [Starter-werk stromen](https://github.com/actions/starter-workflows)
