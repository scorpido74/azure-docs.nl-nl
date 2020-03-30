---
title: Verbinding maken met Azure Data Explorer via Azure Databricks met Python
description: In dit onderwerp ziet u hoe u een Python-bibliotheek in Azure Databricks gebruikt om toegang te krijgen tot gegevens uit Azure Data Explorer met behulp van een van de twee verificatiemethoden.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76985676"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Verbinding maken met Azure Data Explorer vanuit Azure Databricks met Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) is een op Apache Spark gebaseerd analyseplatform dat is geoptimaliseerd voor het Microsoft Azure-platform. In dit artikel ziet u hoe u een Python-bibliotheek in Azure Databricks gebruikt om toegang te krijgen tot gegevens uit Azure Data Explorer. Er zijn verschillende manieren om te verifiëren met Azure Data Explorer, waaronder een apparaataanmelding en een Azure AD-app (Azure Directory).

## <a name="prerequisites"></a>Vereisten

- [Maak een Azure Data Explorer-cluster en -database](/azure/data-explorer/create-cluster-database-portal).
- [Maak een Azure Databricks-werkruimte](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Selecteer Onder **Azure Databricks Service**in de vervolgkeuzelijst **Prijscategorie** de optie **Premium**. Met deze selectie u Azure Databricks-geheimen gebruiken om uw referenties op te slaan en ernaar te verwijzen in notitieblokken en taken.

- [Maak een cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) in Azure Databricks met de volgende specificaties (minimale instellingen die nodig zijn om de voorbeeldnotitieblokken uit te voeren):

   ![Specificaties voor het maken van een cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>De Python-bibliotheek installeren op uw Azure Databricks-cluster

Ga als het gaat om het installeren van de [Python-bibliotheek](/azure/kusto/api/python/kusto-python-client-library) op uw Azure Databricks-cluster:

1. Ga naar uw Azure Databricks-werkruimte en [maak een bibliotheek.](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Upload een Python PyPI-pakket of Python Egg.](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
   - Upload, installeer en voeg de bibliotheek toe aan uw Databricks-cluster.
   - Voer de **PyPi-naam in: azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Verbinding maken met Azure Data Explorer via een apparaataanmelding

[Een notitieblok importeren](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) met het [query-ADX-apparaat-login-notitienotitieblok.](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) U vervolgens verbinding maken met Azure Data Explorer met behulp van uw referenties.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Verbinding maken met ADX met een Azure AD-app

1. Maak de Azure AD-app door [een Azure AD-toepassing in te richten](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Geef de volgende toegang tot uw Azure AD-app in uw Azure Data Explorer-database:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | uw databasenaam |
    | ```AAD App ID``` | uw Azure AD-app-id |
    | ```AAD Tenant ID``` | uw Azure AD-tenant-id |

### <a name="find-your-azure-ad-tenant-id"></a>Uw Azure AD-tenant-id zoeken

Als u een toepassing wilt verifiëren, gebruikt Azure Data Explorer uw Azure AD-tenant-id. Als u uw tenant-id wilt vinden, gebruikt u de volgende URL. Vervang uw domein voor *YourDomain.*

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Dus als uw domein *contoso.com* is, wordt de URL bijvoorbeeld: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Selecteer deze URL om de resultaten te bekijken. De eerste regel is als volgt: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Uw tenant-id is `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Uw Azure AD-app-id en -sleutel opslaan en beveiligen 

Sla uw Azure AD-app-id en -sleutel op en beveilig deze met Azure [Databricks-geheimen](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) als volgt:
1. [Stel de CLI in.](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [Installeer de CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Verificatie instellen](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configureer de [geheimen](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) met behulp van de volgende voorbeeldopdrachten:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Een notitieblok importeren
[Importeer een notitieblok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) met het [query-ADX-AAD-App-notitieblok](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) om verbinding te maken met Azure Data Explorer. Werk de tijdelijke aanduidingswaarden bij met uw clusternaam, databasenaam en Azure AD-tenant-id.
