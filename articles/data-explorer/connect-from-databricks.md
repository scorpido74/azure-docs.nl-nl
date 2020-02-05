---
title: Verbinding maken met Azure Data Explorer van Azure Databricks met behulp van python
description: In dit onderwerp wordt beschreven hoe u een python-bibliotheek in Azure Databricks kunt gebruiken om toegang te krijgen tot gegevens van Azure Data Explorer met behulp van een van de twee verificatie methoden.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985676"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Verbinding maken met Azure Data Explorer van Azure Databricks met behulp van python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) is een op Apache Spark gebaseerd analyse platform dat is geoptimaliseerd voor het Microsoft Azure platform. Dit artikel laat u zien hoe u een python-bibliotheek in Azure Databricks kunt gebruiken om toegang te krijgen tot gegevens van Azure Data Explorer. Er zijn verschillende manieren om te verifiëren met Azure Data Explorer, met inbegrip van een apparaat aanmelden en een Azure Active Directory Azure AD-app.

## <a name="prerequisites"></a>Vereisten

- [Maak een Azure Data Explorer-cluster en-data base](/azure/data-explorer/create-cluster-database-portal).
- [Maak een Azure Databricks-werk ruimte](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Selecteer onder **Azure Databricks-service**in de vervolg keuzelijst **prijs categorie** de optie **Premium**. Met deze selectie kunt u Azure Databricks geheimen gebruiken om uw referenties op te slaan en ernaar te verwijzen in notitie blokken en taken.

- [Maak een cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) in azure Databricks met de volgende specificaties (mini maal vereiste instellingen voor het uitvoeren van de voorbeeld notitieblokken):

   ![Specificaties voor het maken van een cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Installeer de python-bibliotheek op uw Azure Databricks-cluster

De python- [bibliotheek](/azure/kusto/api/python/kusto-python-client-library) installeren op uw Azure Databricks-cluster:

1. Ga naar uw Azure Databricks-werk ruimte en [Maak een bibliotheek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Upload een python PyPI-pakket of python-ei](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Upload, installeer en koppel de bibliotheek aan uw Databricks-cluster.
   - Voer de naam van de PyPi in: **Azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Verbinding maken met Azure Data Explorer met behulp van een apparaat aanmelden

[Een notitie blok importeren](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) met behulp van de [ADX-aanmeldings](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) notitieblok van het apparaat. U kunt vervolgens verbinding maken met Azure Data Explorer met behulp van uw referenties.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Verbinding maken met ADX met behulp van een Azure AD-app

1. Maak een Azure AD-app door [een Azure AD-toepassing](/azure/kusto/management/access-control/how-to-provision-aad-app)in te richten.
1. U kunt als volgt toegang verlenen tot uw Azure AD-app in uw Azure Data Explorer-Data Base:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | de naam van uw data base |
    | ```AAD App ID``` | uw Azure AD-App-ID |
    | ```AAD Tenant ID``` | uw Azure AD-Tenant-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Uw Azure AD-Tenant-ID zoeken

Voor het verifiëren van een toepassing gebruikt Azure Data Explorer uw Azure AD-Tenant-ID. Gebruik de volgende URL om uw Tenant-ID te vinden. Vervang uw domein door de *domeinen.*

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Dus als uw domein *contoso.com* is, wordt de URL bijvoorbeeld: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Selecteer deze URL om de resultaten weer te geven. De eerste regel is als volgt: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Uw Tenant-ID is `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Uw Azure AD-App-ID en-sleutel opslaan en beveiligen 

Bewaar en beveilig uw Azure AD-App-ID en-sleutel door Azure Databricks [geheimen](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) als volgt te gebruiken:
1. [De CLI instellen](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installeer de CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Verificatie instellen](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configureer de [geheimen](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) met behulp van de volgende voorbeeld opdrachten:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Een notitie blok importeren
[Importeer een notitie blok](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) met behulp van de [query-ADX-Aad-app-](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) notebook om verbinding te maken met Azure Data Explorer. Werk de waarden van de tijdelijke aanduidingen bij met de cluster naam, de database naam en de Azure AD-Tenant-ID.
