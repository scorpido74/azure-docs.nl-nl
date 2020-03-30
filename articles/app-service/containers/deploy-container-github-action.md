---
title: Aangepaste container-CI/CD van GitHub-acties
description: Meer informatie over het gebruik van GitHub Actions om uw aangepaste Linux-container te implementeren in App Service vanuit een CI/CD-pijplijn.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246975"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Een aangepaste container implementeren in app-service met GitHub-acties

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) biedt u de flexibiliteit om een workflow voor geautomatiseerde softwareontwikkeling te bouwen. Met de [Azure App Service Action for Containers](https://github.com/Azure/webapps-container-deploy)u uw werkstroom automatiseren om apps te implementeren als aangepaste containers naar App [Service](https://azure.microsoft.com/services/app-service/containers/) met GitHub Actions.

> [!IMPORTANT]
> GitHub Actions is momenteel in bèta. Je moet je eerst [aanmelden om deel te nemen aan de preview](https://github.com/features/actions) met je GitHub-account.
> 

Een werkstroom wordt gedefinieerd door een YAML-bestand `/.github/workflows/` (.yml) in het pad in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Voor een containerwerkstroom van Azure App Service bevat het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een serviceprincipal. <br /> 2. Maak een GitHub-geheim. |
|**Ontwikkelen** | 1. Het milieu instellen. <br /> 2. Bouw het containerbeeld. |
|**Implementeren** | 1. De containerafbeelding implementeren. |

## <a name="create-a-service-principal"></a>Een service-principal maken

U een [serviceprincipal](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) maken met de opdracht [AZ Ad SP create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure CLI.](https://docs.microsoft.com/cli/azure/) U deze opdracht uitvoeren met [Azure Cloud Shell](https://shell.azure.com/) in de Azure-portal of door de knop Uitproberen **te** selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

De uitvoer is een JSON-object met de referenties voor roltoewijzing die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object om te verifiëren vanaf GitHub.

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
> Het is altijd een goede gewoonte om minimale toegang te verlenen. U het bereik in de bovenstaande opdracht Az CLI beperken tot de specifieke App Service-app en het Azure Container Registry waarnaar de containerafbeeldingen worden gepusht.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

In het onderstaande voorbeeld worden referenties op gebruikersniveau gebruikt, d.w.z. Azure Service Principal voor implementatie. Volg de stappen om het geheim te configureren:

1. In [GitHub](https://github.com/)door bladeren door uw opslagplaats, selecteert u **Instellingen > Geheimen > Een nieuw geheim toevoegen**

2. Plak de inhoud `az cli` van de onderstaande opdracht als de waarde van de geheime variabele. Bijvoorbeeld `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Nu in het werkstroombestand `.github/workflows/workflow.yml` in uw branche: vervang het geheim in Azure-inlogactie door uw geheim.

4. Definieer ook de volgende aanvullende geheimen voor de referenties van het containerregister en stel deze in docker-inlogactie in. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. U ziet de geheimen zoals hieronder getoond eenmaal gedefinieerd.

    ![containergeheimen](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>De afbeelding van de container bouwen

In het volgende voorbeeld wordt een deel van de werkstroom weergegeven waarmee de dockerafbeelding wordt opgebouwd.

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

Als u uw afbeelding wilt implementeren in `azure/webapps-container-deploy@v1` een aangepaste container in App-service, gebruikt u de actie. Deze actie heeft vijf parameters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-naam** | (Vereist) Naam van de App Service-app | 
| **sleufnaam** | (Optioneel) Voer een andere bestaande sleuf in dan de productiesleuf |
| **Afbeeldingen** | (Vereist) Geef de volledig gekwalificeerde naam(en) van de containerafbeelding op. Bijvoorbeeld 'myregistry.azurecr.io/nginx:latest' of 'python:3.7.2-alpine/'. Voor een multicontainer-app kunnen meerdere namen van containerafbeeldingen worden opgegeven (meerdere regels gescheiden) |
| **configuratiebestand** | (Optioneel) Pad van het docker-compose-bestand. Moet een volledig gekwalificeerd pad of ten opzichte van de standaard werkmap. Vereist voor multi-container apps. |
| **container-opdracht** | (Optioneel) Voer de opdracht Opstarten in. Voor ex. dotnet run of dotnet filename.dll |

Hieronder vindt u de voorbeeldwerkstroom voor het bouwen en implementeren van een Node.js-app naar een aangepaste container in App Service.

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

U onze set acties vinden die zijn gegroepeerd in verschillende opslagplaatsen op GitHub, elk met documentatie en voorbeelden om u te helpen GitHub voor CI/CD te gebruiken en uw apps te implementeren in Azure.

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp voor containers](https://github.com/Azure/webapps-container-deploy)

- [Inloggen/afmelden docker](https://github.com/Azure/docker-login)

- [Gebeurtenissen die werkstromen activeren](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8's implementeren](https://github.com/Azure/k8s-deploy)

- [Ci-werkstromen voor starter](https://github.com/actions/starter-workflows)

- [Startwerkstromen voor implementatie naar Azure](https://github.com/Azure/actions-workflow-samples)
