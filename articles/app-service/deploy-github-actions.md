---
title: Implementeer uw code vanuit een CI/CD-pijp lijn met GitHub-acties-Azure App Service | Microsoft Docs
description: Meer informatie over het gebruik van GitHub-acties voor het implementeren van uw code in App Service
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
ms.date: 10/22/2019
ms.author: jafreebe
ms.openlocfilehash: b7ec1ae1d04fb1dbe16fd9f4a2640b2b3d9584c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809771"
---
# <a name="github-actions-for-deploying-to-app-service"></a>GitHub acties voor de implementatie van App Service

[Github-acties](https://help.github.com/en/articles/about-github-actions) bieden u de flexibiliteit om een geautomatiseerde werk stroom voor de levens cyclus van software ontwikkeling te bouwen. Met de Azure App Service acties voor GitHub kunt u uw werk stroom automatiseren om [Azure web apps](https://azure.microsoft.com/services/app-service/web/) te implementeren met behulp van github-acties.

> [!IMPORTANT]
> GitHub-acties zijn momenteel in een bèta versie. U moet [zich eerst aanmelden om lid te worden van het voor beeld](https://github.com/features/actions) met behulp van uw github-account.
> 

Een werk stroom wordt gedefinieerd door een YAML-bestand (. yml) in het pad `/.github/workflows/` in uw opslag plaats. Deze definitie bevat de verschillende stappen en para meters die deel uitmaken van de werk stroom.

Voor een Azure web app-werk stroom heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. een Service-Principal definiëren <br /> 2. een GitHub-geheim maken |
|**PE** | 1. de omgeving instellen <br /> 2. de web-app bouwen |
|**Implementeren** | 1. de web-app implementeren |

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

U kunt ook referenties op app-niveau gebruiken, dat wil zeggen, het profiel publiceren voor implementatie. Volg de stappen voor het configureren van het geheim:

1. Down load het publicatie profiel voor de web-app vanuit de portal met de optie **publicatie profiel ophalen** .

2. In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toevoegen**

    ![geheimen](media/app-service-github-actions/secrets.png)

3. Plak de inhoud van het gedownloade bestand met het publicatie profiel in het veld waarde van het geheim.

4. Nu in het werk stroom bestand in uw vertakking: `.github/workflows/workflow.yml` het geheim vervangen door de invoer `publish-profile` van de actie Azure web app implementeren.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Het geheim wordt weer gegeven, zoals hieronder is gedefinieerd.

    ![geheimen](media/app-service-github-actions/app-service-secrets.png)

## <a name="setup-the-environment"></a>De omgeving instellen

Het instellen van de omgeving kan worden uitgevoerd met behulp van een van de installatie acties.

|**Taal**  |**Installatie actie**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

In de volgende voor beelden ziet u het deel van de werk stroom waarmee de omgeving voor de verschillende ondersteunde talen wordt ingesteld:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>De web-app bouwen

Dit is afhankelijk van de taal en voor talen die door Azure Web Apps worden ondersteund. dit gedeelte moet de standaard stappen voor het bouwen van elke taal zijn.

In de volgende voor beelden ziet u het deel van de werk stroom dat de web-app bouwt, in de verschillende ondersteunde talen.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-the-web-app"></a>De web-app implementeren

Als u uw code wilt implementeren in een web-app, moet u de actie `Azure/appservice-actions/webapp@master` gebruiken. Deze actie heeft 4 para meters:

| **Bepaalde**  | **Uitleg**  |
|---------|---------|
| **app-naam** | Lang De naam van de Azure-web-app | 
| **publicatie profiel** | Beschrijving Inhoud van profiel bestand publiceren met Web Deploy-geheimen |
| **pakket** | Beschrijving Het pad naar het pakket of de map. *. zip, *. War, *. jar of een map om te implementeren |
| **sleuf naam** | Beschrijving Voer een bestaande sleuf in, behalve de productie sleuf |

### <a name="deploy-using-publish-profile"></a>Implementeren met een publicatie profiel

Hieronder ziet u de voorbeeld werk stroom voor het maken en implementeren van een node. js-web-app in azure met behulp van een publicatie profiel.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Implementeren met behulp van de service-principal van Azure

Hieronder ziet u de voorbeeld werk stroom voor het maken en implementeren van een node. js-web-app in azure met behulp van de service-principal van Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
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
