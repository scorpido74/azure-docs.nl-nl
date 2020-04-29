---
title: API-overwegingen | Azure Marketplace
description: Problemen met versie beheer, fout afhandeling en autorisatie bij het gebruik van Marketplace-Api's.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256328"
---
# <a name="api-considerations"></a>API-overwegingen

<a name="api-versioning"></a>API-versie beheer
--------------

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Er kunnen meerdere versies van de API tegelijk beschikbaar zijn. Clients moeten aangeven welke versie ze willen gebruiken door de `api-version` para meter op te geven als onderdeel van de query teken reeks.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Het antwoord op een aanvraag met een onbekende of ongeldige API-versie is een HTTP-code 400. Deze fout retourneert de verzameling van bekende API-versies in de antwoord tekst.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Fouten
------

De API reageert op fouten met de bijbehorende HTTP-status codes en optioneel extra informatie in het antwoord dat als JSON is geserialiseerd.
Wanneer u een fout bericht ontvangt, met name een 400-klasse fout, moet u de aanvraag niet opnieuw uitvoeren voordat u de onderliggende oorzaak herstelt. Corrigeer bijvoorbeeld in het bovenstaande voor beeld van het antwoord de API-versie parameter voordat u de aanvraag opnieuw verzendt.

<a name="authorization-header"></a>Autorisatie-header
--------------------

Voor alle Api's in deze Naslag informatie moet u de autorisatie-header door geven, samen met het Bearer-token dat is verkregen van Azure Active Directory (Azure AD). Deze header moet een geldig antwoord ontvangen. Als deze niet aanwezig is `401 Unauthorized` , wordt er een fout geretourneerd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
