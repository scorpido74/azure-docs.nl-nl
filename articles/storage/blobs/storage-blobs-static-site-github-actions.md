---
title: GitHub-acties gebruiken om een statische site te implementeren op Azure Storage
description: Azure Storage van statische website-hosting met GitHub-acties
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure
ms.openlocfilehash: 9b616f8df0a8b64969fac1d18544e6d316120e50
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771041"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Stel een GitHub actions-werk stroom in om uw statische website te implementeren in Azure Storage

Aan de slag met [github-acties](https://docs.github.com/en/actions) met behulp van een werk stroom voor het implementeren van een statische site naar een Azure Storage-blob. Zodra u een werk stroom voor GitHub-acties hebt ingesteld, kunt u uw site automatisch implementeren in azure vanaf GitHub wanneer u wijzigingen aanbrengt in de code van uw site. 

> [!NOTE]
> Als u [statische web apps van Azure](https://docs.microsoft.com/azure/static-web-apps/)gebruikt, hoeft u de werk stroom voor github-acties niet hand matig in te stellen.
> Met Azure static Web Apps wordt automatisch een GitHub-werk stroom gemaakt. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement en GitHub-account. 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account met de code van uw statische website. Als u geen GitHub-account hebt, kunt u [zich gratis aanmelden](https://github.com/join).  
- Een werkende statische website die wordt gehost in Azure Storage. Meer informatie over het [hosten van een statische website in azure Storage](storage-blob-static-website-how-to.md). Uw statische website moet een [Azure CDN](static-website-content-delivery-network.md)bevatten.

## <a name="generate-deployment-credentials"></a>Implementatie referenties genereren

U kunt een [Service-Principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in de [Azure cli](/cli/azure/). Voer deze opdracht uit met [Azure Cloud shell](https://shell.azure.com/) in het Azure portal of door de knop **try it** te selecteren.

Vervang de tijdelijke aanduiding door `myStaticSite` de naam van uw site die wordt gehost in azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
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
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voor beeld is beperkt tot de specifieke App Service-app en niet de hele resource groep.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

1. In [github](https://github.com/), bladert u in uw opslag plaats.

1. Selecteer **instellingen > geheimen > nieuw geheim**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het veld waarde Value van het geheim. Geef het geheim de naam zoals `AZURE_CREDENTIALS` .

    Wanneer u het werk stroom bestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldings actie. Bijvoorbeeld:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Uw werk stroom toevoegen

1. Ga naar **acties** voor uw github-opslag plaats. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Menu-item GitHub acties&quot;:::

1. Selecteer **zelf uw werk stroom instellen**. 

1. Verwijder alles na de `on:` sectie van uw werk stroom bestand. Uw resterende werk stroom kan er bijvoorbeeld als volgt uitzien. 

    ```yml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Wijzig de naam van uw werk stroom `Blob storage website CI` en voeg de acties voor uitchecken en aanmelden toe. Met deze acties wordt uw site code uitgecheckt en geverifieerd met Azure met behulp van het `AZURE_CREDENTIALS` github-geheim dat u eerder hebt gemaakt. 

    ```yml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Gebruik de actie Azure CLI om uw code te uploaden naar Blob Storage en uw CDN-eind punt te verwijderen. `az storage blob upload-batch`Vervang de tijdelijke aanduiding voor door de naam van uw opslag account. Het script wordt geüpload naar de `$web` container. `az cdn endpoint purge`Vervang de tijdelijke aanduidingen door de naam van uw CDN-profiel, de naam van het CDN-eind punt en de resource groep.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Voltooi uw werk stroom door een actie toe te voegen aan de afmelding van Azure. Dit is de voltooide werk stroom. Het bestand wordt weer gegeven in de `.github/workflows` map van de opslag plaats.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Uw implementatie controleren

1. Ga naar **acties** voor uw github-opslag plaats. 

1. Open het eerste resultaat om gedetailleerde logboeken van de uitvoering van de werk stroom weer te geven. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Menu-item GitHub acties&quot;:::

1. Selecteer **zelf uw werk stroom instellen**. 

1. Verwijder alles na de `on:` sectie van uw werk stroom bestand. Uw resterende werk stroom kan er bijvoorbeeld als volgt uitzien. 

    ```yml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Wijzig de naam van uw werk stroom `Blob storage website CI` en voeg de acties voor uitchecken en aanmelden toe. Met deze acties wordt uw site code uitgecheckt en geverifieerd met Azure met behulp van het `AZURE_CREDENTIALS` github-geheim dat u eerder hebt gemaakt. 

    ```yml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Gebruik de actie Azure CLI om uw code te uploaden naar Blob Storage en uw CDN-eind punt te verwijderen. `az storage blob upload-batch`Vervang de tijdelijke aanduiding voor door de naam van uw opslag account. Het script wordt geüpload naar de `$web` container. `az cdn endpoint purge`Vervang de tijdelijke aanduidingen door de naam van uw CDN-profiel, de naam van het CDN-eind punt en de resource groep.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP&quot;
    ``` 

1. Voltooi uw werk stroom door een actie toe te voegen aan de afmelding van Azure. Dit is de voltooide werk stroom. Het bestand wordt weer gegeven in de `.github/workflows` map van de opslag plaats.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  &quot;/*&quot; --profile-name &quot;CDN_PROFILE_NAME&quot; --name &quot;CDN_ENDPOINT&quot; --resource-group &quot;RESOURCE_GROUP":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw statische Azure-site en-opslag plaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd door de resource groep en de GitHub-opslag plaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over statische Web Apps van Azure](https://docs.microsoft.com/azure/static-web-apps/)