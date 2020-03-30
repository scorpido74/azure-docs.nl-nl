---
title: GitHub-acties gebruiken om code-updates uit te voeren in Azure-functies
description: Meer informatie over het gebruik van GitHub-acties om een werkstroom te definiëren voor het bouwen en implementeren van Azure Functions-projecten in GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: dd74fd5c38e5a8800d2092afc1db1b412b126861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649905"
---
# <a name="continuous-delivery-by-using-github-action"></a>Continue levering met GitHub Action

[Met GitHub Actions](https://github.com/features/actions) u een werkstroom definiëren om uw functiescode automatisch te bouwen en te implementeren naar de functie-app in Azure. 

In GitHub-acties is een [werkstroom](https://help.github.com/articles/about-github-actions#workflow) een geautomatiseerd proces dat u definieert in uw GitHub-repository. Dit proces vertelt GitHub hoe u uw functie-appproject op GitHub bouwen en implementeren. 

Een werkstroom wordt gedefinieerd door een YAML-bestand `/.github/workflows/` (.yml) in het pad in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom. 

Voor een azure-werkstroom bevat het bestand drie secties: 

| Sectie | Taken |
| ------- | ----- |
| **Verificatie** | <ol><li>Definieer een serviceprincipal.</li><li>Download publicatieprofiel.</li><li>Maak een GitHub-geheim.</li></ol>|
| **Ontwikkelen** | <ol><li>Stel de omgeving in.</li><li>Bouw de functie-app.</li></ol> |
| **Implementeren** | <ol><li>Implementeer de functie-app.</li></ol>|

> [!NOTE]
> U hoeft geen serviceprincipal te maken als u besluit het publicatieprofiel te gebruiken voor verificatie.

## <a name="create-a-service-principal"></a>Een service-principal maken

U een [serviceprincipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [AZ Ad SP create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) in de [Azure CLI.](/cli/azure/) U deze opdracht uitvoeren met [Azure Cloud Shell](https://shell.azure.com) in de Azure-portal of door de knop Uitproberen **te** selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Vervang in dit voorbeeld de tijdelijke aanduidingen in de bron door de naam van uw abonnements-id, resourcegroep en functie-app. De uitvoer is de referenties voor roltoewijzing die toegang bieden tot uw functie-app. Kopieer dit JSON-object, dat u gebruiken om te verifiëren vanaf GitHub.

> [!IMPORTANT]
> Het is altijd een goede gewoonte om minimale toegang te verlenen. Daarom is het bereik in het vorige voorbeeld beperkt tot de specifieke functie-app en niet tot de hele resourcegroep.

## <a name="download-the-publishing-profile"></a>Het publicatieprofiel downloaden

U het publicatieprofiel van uw functie-app downloaden door naar de **pagina Overzicht** van uw app te gaan en op **Publicatieprofiel downloaden**.

   ![Publicatieprofiel downloaden](media/functions-how-to-github-actions/get-publish-profile.png)

Kopieer de inhoud van het bestand.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

1. Blader in [GitHub](https://github.com)naar uw opslagplaats en selecteer > **Instellingengeheimen** > **Voeg een nieuw geheim toe.** **Settings**

   ![Geheim toevoegen](media/functions-how-to-github-actions/add-secret.png)

1. Voeg een nieuw geheim toe.

   * Als u de serviceprincipal gebruikt die u hebt gemaakt `AZURE_CREDENTIALS` met de Azure CLI, gebruikt u de **naam .** Plak vervolgens de gekopieerde JSON-objectuitvoer voor **Waarde**en selecteer **Geheim toevoegen**.
   * Als u een publicatieprofiel gebruikt, gebruikt u `SCM_CREDENTIALS` de **naam**. Gebruik vervolgens de bestandsinhoud van het publicatieprofiel voor **Waarde**en selecteer **Geheim toevoegen**.

GitHub kan nu verifiëren naar uw functie-app in Azure.

## <a name="set-up-the-environment"></a>De omgeving instellen 

Het instellen van de omgeving gebeurt met behulp van een taalspecifieke publicatie-instellingsactie.

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt het `actions/setup-node` deel van de werkstroom weergegeven dat de actie gebruikt om de omgeving in te stellen:

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

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt het `actions/setup-python` deel van de werkstroom weergegeven dat de actie gebruikt om de omgeving in te stellen:

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

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt het `actions/setup-dotnet` deel van de werkstroom weergegeven dat de actie gebruikt om de omgeving in te stellen:

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

# <a name="java"></a>[Java](#tab/java)

In het volgende voorbeeld wordt het `actions/setup-java` deel van de werkstroom weergegeven dat de actie gebruikt om de omgeving in te stellen:

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
---

## <a name="build-the-function-app"></a>De functie-app bouwen

Dit is afhankelijk van de taal en voor talen die worden ondersteund door Azure-functies, moet deze sectie de standaardbouwstappen van elke taal zijn.

In het volgende voorbeeld wordt het deel van de werkstroom weergegeven dat de functie-app bouwt, wat taalspecifiek is:

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="java"></a>[Java](#tab/java)

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
---

## <a name="deploy-the-function-app"></a>De functie-app implementeren

Als u uw code wilt implementeren in een `Azure/functions-action` functie-app, moet u de actie gebruiken. Deze actie heeft twee parameters:

|Parameter |Uitleg  |
|---------|---------|
|**_app-naam_** | (Verplicht) De naam van uw functie-app. |
|_**sleufnaam**_ | (Optioneel) De naam van de [implementatiesleuf die](functions-deployment-slots.md) u wilt implementeren. De sleuf moet al worden gedefinieerd in uw functie-app. |


In het volgende voorbeeld `functions-action`wordt gebruik gebruikt van versie 1 van :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Volgende stappen

Zie een van de bestanden in de [werkstroomvoorbeelden van Azure GitHub Actions](https://aka.ms/functions-actions-samples) `functionapp` die in de naam staan als u een volledige werkstroom .yaml wilt weergeven. U deze voorbeelden een startpunt voor uw werkstroom gebruiken.

> [!div class="nextstepaction"]
> [Meer informatie over GitHub-acties](https://help.github.com/en/articles/about-github-actions)
