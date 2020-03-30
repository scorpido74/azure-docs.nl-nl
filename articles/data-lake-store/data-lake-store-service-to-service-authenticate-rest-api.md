---
title: Service-to-service authenticatie - Data Lake Storage Gen1 - REST API
description: Meer informatie over het implementeren van service-to-service-verificatie met Azure Data Lake Storage Gen1 en Azure Active Directory met behulp van de REST API.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904516"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Service-to-service-verificatie met Azure Data Lake Storage Gen1 met REST API
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK gebruiken](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-service-to-service-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

In dit artikel leert u hoe u de REST API gebruiken om service-to-service-verificatie uit te brengen met Azure Data Lake Storage Gen1. Zie Verificatie van eindgebruikers met Data Lake Storage Gen1 met REST API voor verificatie van eindgebruikers [met Data Lake Storage Gen1 met REST API.](data-lake-store-end-user-authenticate-rest-api.md)

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory "Web"-toepassing**. U moet de stappen in [service-to-service-verificatie met Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md)hebben voltooid met Azure Active Directory .

## <a name="service-to-service-authentication"></a>Verificatie van service-tot-service

In dit scenario biedt de toepassing zijn eigen referenties om de bewerkingen uit te voeren. Hiervoor moet u een post-verzoek indienen, zoals in het volgende fragment:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

De uitvoer van de aanvraag bevat een `access-token` autorisatietoken (aangeduid met in de uitvoer hieronder) dat u vervolgens doorgeeft met uw REST API-aanroepen. Sla het verificatietoken op in een tekstbestand; je hebt het nodig bij het maken van REST-gesprekken naar Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

In dit artikel wordt de **niet-interactieve** benadering gebruikt. Zie [Service-naar-service-aanroepen met referenties](https://msdn.microsoft.com/library/azure/dn645543.aspx) voor meer informatie over niet-interactieve (service-naar-service) aanroepen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u service-to-service-verificatie gebruiken om te verifiëren met Data Lake Storage Gen1 met restapi. U nu de volgende artikelen bekijken waarin wordt gesproken over hoe u de REST API gebruiken om te werken met Data Lake Storage Gen1.

* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met REST API](data-lake-store-get-started-rest-api.md)
* [Gegevensbewerkingen op Data Lake Storage Gen1 met REST API](data-lake-store-data-operations-rest-api.md)
