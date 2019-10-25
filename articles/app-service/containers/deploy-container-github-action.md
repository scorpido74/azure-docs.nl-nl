---
title: Implementeer uw container vanuit een CI/CD-pijp lijn met GitHub-acties-Azure App Service | Microsoft Docs
description: Meer informatie over het gebruik van GitHub-acties voor het implementeren van uw container in App Service
services: app-service
documentationcenter: ''
author: jasonfreeberg
writer: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: jafreebe
ms.openlocfilehash: 2341eba2c24c06d654c9d2eeda96788d168fe27c
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809810"
---
# <a name="github-actions-for-deploying-to-web-app-for-containers"></a>GitHub acties voor de implementatie van Web App for Containers

[Github-acties](https://help.github.com/en/articles/about-github-actions) bieden u de flexibiliteit om een geautomatiseerde werk stroom voor de levens cyclus van software ontwikkeling te bouwen. Met de Azure App Service acties voor GitHub kunt u uw werk stroom automatiseren voor het implementeren [van Azure web apps voor containers](https://azure.microsoft.com/services/app-service/containers/) met github-acties.

> [!IMPORTANT]
> GitHub-acties zijn momenteel in een bèta versie. U moet [zich eerst aanmelden om lid te worden van het voor beeld](https://github.com/features/actions) met behulp van uw github-account.
> 

Een werk stroom wordt gedefinieerd door een YAML-bestand (. yml) in het pad `/.github/workflows/` in uw opslag plaats. Deze definitie bevat de verschillende stappen en para meters die deel uitmaken van de werk stroom.

Voor een Azure web app-container werk stroom heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. een Service-Principal definiëren <br /> 2. een GitHub-geheim maken |
|**PE** | 1. de omgeving instellen <br /> 2. bouw de container installatie kopie |
|**Implementeren** | 1. de container installatie kopie implementeren |

## <a name="create-a-service-principal"></a>Een service-principal maken

U kunt een [Service-Principal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) maken met behulp van de opdracht [AZ AD SP create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure cli](https://docs.microsoft.com/cli/azure/). U kunt deze opdracht uitvoeren met behulp van [Azure Cloud shell](https://shell.azure.com/) in het Azure portal of door de knop **try it** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

In dit voor beeld vervangt u de tijdelijke aanduidingen in de resource door uw abonnements-ID, resource groep en de naam van de web-app. De uitvoer is de roltoewijzings referenties waarmee u toegang hebt tot uw web-app. Kopieer dit JSON-object, dat u kunt gebruiken om te verifiëren vanuit GitHub.

> [!NOTE]
> U hoeft geen service-principal te maken als u het publicatie profiel voor verificatie wilt gebruiken.

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. Daarom is de scope in het vorige voor beeld beperkt tot de specifieke Web-app en niet de hele resource groep.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

In het onderstaande voor beeld wordt gebruikgemaakt van referenties op gebruikers niveau, zoals de Azure-service-principal voor implementatie. Volg de stappen voor het configureren van het geheim:

1. In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toevoegen**

2. Plak de inhoud van de onderstaande `az cli` opdracht als waarde voor de geheime variabele. Bijvoorbeeld `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Nu in het werk stroom bestand in uw vertakking: `.github/workflows/workflow.yml` vervangt u het geheim in azure-aanmeldings actie door uw geheim.

4. U kunt ook de volgende extra geheimen voor de container register referenties definiëren en instellen in de aanmeldings actie voor docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. De geheimen worden weer gegeven, zoals hieronder is gedefinieerd.

    ![container geheimen](../media/app-service-github-actions/app-service-secrets-container.png)

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
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

## <a name="deploy-to-web-app-container"></a>Implementeren naar web-app-container

Als u uw installatie kopie wilt implementeren in een web-app-container, moet u de actie `Azure/appservice-actions/webapp@master` gebruiken. Deze actie heeft 5 para meters:

| **Bepaalde**  | **Uitleg**  |
|---------|---------|
| **app-naam** | Lang De naam van de Azure-web-app | 
| **sleuf naam** | Beschrijving Voer een bestaande sleuf in, behalve de productie sleuf |
| **installatie kopieën** | Lang Geef de volledig gekwalificeerde naam van de container installatie kopie (n) op. Bijvoorbeeld ' myregistry.azurecr.io/nginx:latest ' of ' python: 3.7.2-Alpine/'. Voor scenario met meerdere containers kunnen namen van verschillende container installatie kopieën worden gegeven (meerdere regels gescheiden) |
| **configuratie-bestand** | Beschrijving Pad van het docker-bestand. Moet een volledig gekwalificeerd pad of relatief ten opzichte van de standaard werkmap zijn. Vereist voor het scenario met meerdere containers |
| **container-opdracht** | Beschrijving Voer de opstart opdracht in. Voor ex. DotNet run of DotNet filename. dll |

Hieronder ziet u de voorbeeld werk stroom voor het maken en implementeren van een node. js-web-app in een Azure web app-container.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/actions/login@v1
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

- [Azure-aanmelding](https://github.com/Azure/actions)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp voor containers](https://github.com/Azure/webapps-container-deploy)

- [Aanmelden/afmelden bij docker](https://github.com/Azure/docker-login)

- [Gebeurtenissen waarmee werk stromen worden geactiveerd](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s implementeren](https://github.com/Azure/k8s-deploy)

- [Starter-werk stromen](https://github.com/actions/starter-workflows)
