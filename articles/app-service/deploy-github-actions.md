---
title: CI/CD configureren met GitHub-acties
description: Meer informatie over het implementeren van uw code naar Azure App Service vanuit een CI/CD-pijplijn met GitHub Actions. Pas de buildtaken aan en voer complexe implementaties uit.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084988"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Implementeren in App-service met GitHub-acties

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) biedt u de flexibiliteit om een workflow voor geautomatiseerde softwareontwikkeling te bouwen. Met de Azure App Service Actions voor GitHub u uw werkstroom automatiseren om te implementeren in [Azure App Service](overview.md) met GitHub Actions.

> [!IMPORTANT]
> GitHub Actions is momenteel in bèta. Je moet je eerst [aanmelden om deel te nemen aan de preview](https://github.com/features/actions) met je GitHub-account.
> 

Een werkstroom wordt gedefinieerd door een YAML-bestand `/.github/workflows/` (.yml) in het pad in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Voor een Azure App Service-werkstroom bevat het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een serviceprincipal <br /> 2. Maak een GitHub-geheim |
|**Ontwikkelen** | 1. Het milieu instellen <br /> 2. De web-app bouwen |
|**Implementeren** | 1. De web-app implementeren |

## <a name="create-a-service-principal"></a>Een service-principal maken

U een [serviceprincipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [AZ Ad SP create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure CLI.](https://docs.microsoft.com/cli/azure/) U deze opdracht uitvoeren met [Azure Cloud Shell](https://shell.azure.com/) in de Azure-portal of door de knop Uitproberen **te** selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

Vervang in dit voorbeeld de tijdelijke aanduidingen in de bron door uw abonnements-id, de naam van de brongroep en de naam van de app. De uitvoer is de referenties voor roltoewijzing die toegang bieden tot uw App Service-app. Kopieer dit JSON-object, dat u gebruiken om te verifiëren vanaf GitHub.

> [!NOTE]
> U hoeft geen serviceprincipal te maken als u besluit het publicatieprofiel te gebruiken voor verificatie.

> [!IMPORTANT]
> Het is altijd een goede gewoonte om minimale toegang te verlenen. Daarom is het bereik in het vorige voorbeeld beperkt tot de specifieke App Service-app en niet tot de hele resourcegroep.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

U ook referenties op app-niveau gebruiken, d.w.z. profiel publiceren voor implementatie. Volg de stappen om het geheim te configureren:

1. Download het publicatieprofiel voor de App Service-app van de portal met de optie **Profiel publiceren ophalen.**

2. In [GitHub](https://github.com/)door bladeren door uw opslagplaats, selecteert u **Instellingen > Geheimen > Een nieuw geheim toevoegen**

    ![geheimen](media/app-service-github-actions/secrets.png)

3. Plak de inhoud van het gedownloade publicatieprofielbestand in het waardeveld van het geheim.

4. Nu in het werkstroombestand `.github/workflows/workflow.yml` in uw branch: vervang het geheim voor de invoer `publish-profile` van de actie Azure Web App implementeren.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. U ziet het geheim zoals hieronder eens gedefinieerd.

    ![geheimen](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>De omgeving instellen

Het instellen van de omgeving kan worden gedaan met behulp van een van de setup-acties.

|**Taal**  |**Setup-actie**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**Javascript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

In de volgende voorbeelden wordt het deel van de werkstroom weergegeven dat de omgeving voor de verschillende ondersteunde talen instelt:

**Javascript**

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

Dit is afhankelijk van de taal en voor talen die worden ondersteund door Azure App Service, moet deze sectie de standaardbouwstappen van elke taal zijn.

In de volgende voorbeelden wordt het deel van de werkstroom weergegeven dat de web-app bouwt, in de verschillende ondersteunde talen.

**Javascript**

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
## <a name="deploy-to-app-service"></a>Implementeren naar App Service

Als u uw code wilt implementeren `azure/webapps-deploy@v2` in een App Service-app, gebruikt u de actie. Deze actie heeft vier parameters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **app-naam** | (Vereist) Naam van de App Service-app | 
| **publicatieprofiel** | (Optioneel) Inhoud van profielbestanden publiceren met webdeploy-geheimen |
| **Pakket** | (Optioneel) Pad naar pakket of map. *.zip, *.war, *.jar of een map die moet worden geïmplementeerd |
| **sleufnaam** | (Optioneel) Voer een andere bestaande sleuf in dan de productiesleuf |

### <a name="deploy-using-publish-profile"></a>Implementeren met publicatieprofiel

Hieronder vindt u de voorbeeldwerkstroom voor het bouwen en implementeren van een Node.js-app naar Azure met behulp van publicatieprofiel.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Implementeren met Azure-serviceprincipal

Hieronder vindt u de voorbeeldwerkstroom voor het bouwen en implementeren van een Node.js-app naar Azure met behulp van een Azure-serviceprincipal.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
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
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Volgende stappen

U onze set acties vinden die zijn gegroepeerd in verschillende opslagplaatsen op GitHub, elk met documentatie en voorbeelden om u te helpen GitHub voor CI/CD te gebruiken en uw apps te implementeren in Azure.

- [Werkstroom acties die moet worden geïmplementeerd in Azure](https://github.com/Azure/actions-workflow-samples)

- [Azure-aanmelding](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp voor containers](https://github.com/Azure/webapps-container-deploy)

- [Inloggen/afmelden docker](https://github.com/Azure/docker-login)

- [Gebeurtenissen die werkstromen activeren](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8's implementeren](https://github.com/Azure/k8s-deploy)

- [Startwerk](https://github.com/actions/starter-workflows)
