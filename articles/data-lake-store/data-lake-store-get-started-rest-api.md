---
title: 'REST API: Accountbeheerbewerkingen op Azure Data Lake Storage Gen1 | Microsoft Documenten'
description: Azure Data Lake Storage Gen1 en WebHDFS REST API gebruiken om accountbeheerbewerkingen uit te voeren in het Data Lake Storage Gen1-account
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877101"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Accountbeheerbewerkingen op Azure Data Lake Storage Gen1 met REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In dit artikel leert u hoe u accountbeheerbewerkingen uitvoert op Azure Data Lake Storage Gen1 met behulp van de REST API. Accountbeheeractiviteiten omvatten het maken van een Data Lake Storage Gen1-account, het verwijderen van een Data Lake Storage Gen1-account, enz. Zie [Bestandssysteembewerkingen](data-lake-store-data-operations-rest-api.md)op Data Lake Storage Gen1 met REST API voor instructies over het uitvoeren van bestandssysteembewerkingen op Data Lake Storage Gen1.

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. In dit artikel wordt cURL gebruikt om aan te tonen hoe u REST API-aanroepen voeren tegen een Data Lake Storage Gen1-account.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hoe verifieer ik met Azure Active Directory?
Er zijn twee benaderingen voor verificatie met Azure Active Directory.

* Zie Verificatie van eindgebruikers met Data [Lake Storage Gen1 met .NET SDK](data-lake-store-end-user-authenticate-rest-api.md)voor verificatie van eindgebruikers voor uw toepassing (interactief).
* Zie [Service-to-service-verificatie met Data Lake Storage Gen1 met .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md)voor service-to-service-verificatie voor uw toepassing (niet-interactief).


## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken
Deze bewerking is gebaseerd op de REST-API-aanroep die [hier](https://docs.microsoft.com/rest/api/datalakestore/accounts/create) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht: Vervang ** \<uw naam van uw storagegen1-naam>** door uw naam Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Vervang \<`REDACTED`\> in de bovenstaande opdracht door het verificatietoken dat u eerder hebt opgehaald. De nettolading van de aanvraag voor deze opdracht zit in het bestand **input.json** dat is opgegeven voor de parameter `-d` hierboven. De inhoud van het bestand input.json ziet er ongeveer als uit als het volgende codefragment:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account verwijderen
Deze bewerking is gebaseerd op de REST-API-aanroep die [hier](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete) wordt gedefinieerd.

Gebruik de volgende cURL-opdracht om een Data Lake Storage Gen1-account te verwijderen. Vervang ** \<uw storagegen1name>** door de naam van uw Data Lake Storage Gen1-account.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Als het goed is, wordt ongeveer het volgende codefragment weergegeven:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Volgende stappen
* [Bestandssysteembewerkingen op Data Lake Storage Gen1 met REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Zie ook
* [Naslaginformatie over Azure Data Lake Storage Gen1 REST API](https://docs.microsoft.com/rest/api/datalakestore/)
* [Open Source Big Data-toepassingen die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

