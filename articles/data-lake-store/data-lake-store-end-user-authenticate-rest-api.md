---
title: 'Verificatie van eindgebruikers: REST API met Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het bereiken van verificatie door eindgebruikers met Azure Data Lake Storage Gen1 met Azure Active Directory met REST API
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0ef65c23ee1bf4f064695779b71c8616427da204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877819"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Verificatie van eindgebruikers met Azure Data Lake Storage Gen1 met REST API
> [!div class="op_single_selector"]
> * [Java gebruiken](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK gebruiken](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python gebruiken](data-lake-store-end-user-authenticate-python.md)
> * [REST-API gebruiken](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

In dit artikel vindt u meer informatie over het gebruik van de REST API om verificatie van eindgebruikers uit te brengen met Azure Data Lake Storage Gen1. Zie [Service-to-service-verificatie met Data Lake](data-lake-store-service-to-service-authenticate-rest-api.md)Storage Gen1 met REST API met REST API.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Maak een Azure Active Directory 'Native'-toepassing.** U moet de stappen in [verificatie van eindgebruikers met Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md)hebben voltooid met Azure Active Directory .

* **[cURL](https://curl.haxx.se/)**. In dit artikel wordt cURL gebruikt om aan te tonen hoe u REST API-aanroepen voeren tegen een Data Lake Storage Gen1-account.

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
Verificatie door eindgebruikers is de aanbevolen aanpak als u wilt dat een gebruiker zich aanmeldt bij uw toepassing met Azure AD. Uw toepassing heeft toegang tot Azure-bronnen met hetzelfde toegangsniveau als de ingelogde gebruiker. De gebruiker moet zijn referenties periodiek verstrekken om ervoor te zorgen dat uw toepassing de toegang behoudt.

Het resultaat van het inloggen van de eindgebruiker is dat uw toepassing een toegangstoken en een vernieuwingstoken krijgt. Het toegangstoken wordt gekoppeld aan elk verzoek aan Data Lake Storage Gen1 of Data Lake Analytics en is standaard één uur geldig. Het vernieuwingstoken kan worden gebruikt om een nieuw toegangstoken te verkrijgen en is standaard maximaal twee weken geldig, indien regelmatig gebruikt. U twee verschillende benaderingen gebruiken voor het inloggen van eindgebruikers.

In dit scenario wordt de gebruiker via de toepassing gevraagd om zich te melden en worden alle bewerkingen uitgevoerd in de context van de gebruiker. Voer de volgende stappen uit:

1. Leid de gebruiker via de toepassing om naar de volgende URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

   > [!NOTE]
   > \<REDIRECT-URI> moet zijn gecodeerd om te worden gebruikt in een URL. Dus, https://localhostvoor `https%3A%2F%2Flocalhost`, gebruik )

    Voor deze zelfstudie kunt u de waarden van de tijdelijke aanduiding in bovenstaande URL vervangen en deze in de adresbalk van de webbrowser plakken. U wordt omgeleid om u te verifiëren met uw Azure-aanmelding. Wanneer u bent aangemeld, wordt het antwoord weergegeven in de adresbalk van de browser. Het antwoord heeft de volgende indeling:

        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Leg de autorisatiecode uit het antwoord vast. Voor deze zelfstudie u de autorisatiecode kopiëren vanaf de adresbalk van de webbrowser en deze doorgeven in het POST-verzoek aan het tokeneindpunt, zoals in het volgende fragment wordt weergegeven:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>

   > [!NOTE]
   > In dit geval hoeft de \<REDIRECT-URI> niet te worden gecodeerd.
   > 
   > 

3. Het antwoord is een JSON-object dat een `"access_token": "<ACCESS_TOKEN>"`access token (bijvoorbeeld) `"refresh_token": "<REFRESH_TOKEN>"`en een vernieuwingstoken (bijvoorbeeld) bevat. Uw toepassing gebruikt het toegangstoken wanneer u toegang krijgt tot Azure Data Lake Storage Gen1 en het vernieuwingstoken om een ander toegangstoken te krijgen wanneer een toegangstoken verloopt.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4. Wanneer het toegangstoken verloopt, u een nieuw toegangstoken aanvragen met behulp van het vernieuwingstoken, zoals in het volgende fragment wordt weergegeven:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Zie [De stroom voor autorisatiecodetoekenning](https://msdn.microsoft.com/library/azure/dn645542.aspx) voor meer informatie over interactieve gebruikersverificatie.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u service-to-service-verificatie gebruiken om te verifiëren met Azure Data Lake Storage Gen1 met behulp van REST API. U nu de volgende artikelen bekijken waarin wordt gesproken over hoe u de REST API gebruiken om te werken met Azure Data Lake Storage Gen1.

* [Accountbeheerbewerkingen op Data Lake Storage Gen1 met REST API](data-lake-store-get-started-rest-api.md)
* [Gegevensbewerkingen op Data Lake Storage Gen1 met REST API](data-lake-store-data-operations-rest-api.md)

