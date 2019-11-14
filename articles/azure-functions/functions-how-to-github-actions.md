---
title: GitHub-acties gebruiken om code-updates te maken in Azure Functions
description: Leer hoe u GitHub-acties kunt gebruiken om een werk stroom te definiëren voor het bouwen en implementeren van Azure Functions projecten in GitHub.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 8decf4622c4043cbac0f2e2f6e2ad59f1b5baaae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074018"
---
# <a name="continuous-delivery-by-using-github-action"></a>Continue levering met behulp van GitHub-actie

Met [github acties](https://github.com/features/actions) kunt u een werk stroom definiëren om automatisch uw functions-code te bouwen en te implementeren in functie-app in Azure. 

In GitHub acties is een [werk stroom](https://help.github.com/articles/about-github-actions#workflow) een geautomatiseerd proces dat u in uw github-opslag plaats definieert. Dit proces vertelt u GitHub hoe u uw functions-app-project bouwt en implementeert op GitHub. 

Een werk stroom wordt gedefinieerd door een YAML-bestand (. yml) in het pad `/.github/workflows/` in uw opslag plaats. Deze definitie bevat de verschillende stappen en para meters die deel uitmaken van de werk stroom. 

Voor een Azure Functions werk stroom heeft het bestand drie secties: 

| Sectie | Taken |
| ------- | ----- |
| **Verificatie** | <ol><li>Definieer een service-principal.</li><li>Publicatie profiel downloaden.</li><li>Maak een GitHub-geheim.</li></ol>|
| **PE** | <ol><li>Stel de omgeving in.</li><li>De functie-app bouwen.</li></ol> |
| **Implementeren** | <ol><li>Implementeer de functie-app.</li></ol>|

> [!NOTE]
> U hoeft geen service-principal te maken als u het publicatie profiel voor verificatie wilt gebruiken.

## <a name="create-a-service-principal"></a>Een service-principal maken

U kunt een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met behulp van de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure cli](/cli/azure/). U kunt deze opdracht uitvoeren met behulp van [Azure Cloud shell](https://shell.azure.com) in het Azure portal of door de knop **try it** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

In dit voor beeld vervangt u de tijdelijke aanduidingen in de resource door uw abonnements-ID, resource groep en naam van de functie-app. De uitvoer is de roltoewijzings referenties die toegang bieden tot uw functie-app. Kopieer dit JSON-object, dat u kunt gebruiken om te verifiëren vanuit GitHub.

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. Daarom is de scope in het vorige voor beeld beperkt tot de specifieke functie-app en niet de hele resource groep.

## <a name="download-the-publishing-profile"></a>Het publicatie profiel downloaden

U kunt het publicatie Profiel van uw functionapp downloaden door naar de pagina **overzicht** van uw app te gaan en te klikken op **publicatie profiel ophalen**.

   ![Publicatie profiel downloaden](media/functions-how-to-github-actions/get-publish-profile.png)

Kopieer de inhoud van het bestand.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

1. In [github](https://github.com)gaat u naar uw opslag plaats, selecteert u **instellingen** > **geheimen** > **een nieuw geheim toe te voegen**.

   ![Geheim toevoegen](media/functions-how-to-github-actions/add-secret.png)

1. Gebruik `AZURE_CREDENTIALS` voor de **naam** en de gekopieerde uitvoer van de opdracht voor **waarde**. Als u vervolgens **geheim toevoegen**selecteert. Als u een publicatie profiel gebruikt, gebruikt u `SCM_CREDENTIALS` voor de **naam** en de bestands inhoud voor **waarde**.

GitHub kan nu worden geverifieerd bij uw functie-app in Azure.

## <a name="set-up-the-environment"></a>De omgeving instellen 

Het instellen van de omgeving kan worden uitgevoerd met behulp van een van de instellingen voor het publiceren van de publicatie.

|Taal | Installatie actie |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

In de volgende voor beelden ziet u het deel van de werk stroom waarmee de omgeving voor de verschillende ondersteunde talen wordt ingesteld:

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>De functie-app bouwen

Dit is afhankelijk van de taal en voor talen die door Azure Functions worden ondersteund. dit gedeelte moet de standaard stappen voor het bouwen van elke taal zijn.

In de volgende voor beelden ziet u het deel van de werk stroom waarmee de functie-app wordt gebouwd in de verschillende ondersteunde talen.:

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
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
        # If your function app project is not located in your repository's root
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
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```

## <a name="deploy-the-function-app"></a>De functie-app implementeren

Als u uw code wilt implementeren in een functie-app, moet u de actie `Azure/functions-action` gebruiken. Deze actie heeft twee para meters:

|Parameter |Uitleg  |
|---------|---------|
|**_app-naam_** | Ingevuld De naam van de functie-app. |
|_**sleuf naam**_ | Beschrijving De naam van de [implementatie sleuf](functions-deployment-slots.md) waarnaar u wilt implementeren. De sleuf moet al zijn gedefinieerd in uw functie-app. |


In het volgende voor beeld wordt versie 1 van de `functions-action`gebruikt:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Volgende stappen

Als u een volledige werk stroom. yaml wilt weer geven, raadpleegt u een van de bestanden in de [werk stroom voor beelden van Azure github actions opslag plaats](https://github.com/Azure/actions-workflow-samples) die `functionapp` in de naam hebben. U kunt deze voor beelden gebruiken als uitgangs punt voor uw werk stroom.

> [!div class="nextstepaction"]
> [Meer informatie over GitHub-acties](https://help.github.com/en/articles/about-github-actions)
