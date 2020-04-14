---
title: API-overwegingen | Azure Marketplace
description: Problemen met versiebeheer, foutafhandeling en autorisatie bij het gebruik van de marketplace-API's.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: e4d4d5cb16e1037458d09f8c7681ab2d2ecf8676
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256328"
---
# <a name="api-considerations"></a>API-overwegingen

<a name="api-versioning"></a>API-versiebeheer
--------------

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Er kunnen meerdere versies van de API tegelijk beschikbaar zijn. Clients moeten aangeven welke versie ze willen `api-version` aanroepen door de parameter op te geven als onderdeel van de querytekenreeks.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Het antwoord op een aanvraag met een onbekende of ongeldige API-versie is een HTTP-code 400. Met deze fout wordt de verzameling van bekende API-versies in de antwoordtekst geretourneerd.

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

De API reageert op fouten met de bijbehorende HTTP-statuscodes en optioneel aanvullende informatie in de respons geserialiseerd als JSON.
Wanneer u een fout ontvangt, met name een fout van 400 klassen, probeert u de aanvraag niet opnieuw voordat de onderliggende oorzaak wordt vastgesteld. Stel bijvoorbeeld in het voorbeeldantwoord hierboven de parameter API-versie vast voordat u de aanvraag opnieuw verzendt.

<a name="authorization-header"></a>Autorisatiekoptekst
--------------------

Voor alle API's in deze verwijzing moet u de autorisatiekop doorgeven, samen met het token aan toonder dat is verkregen uit Azure Active Directory (Azure AD). Deze header is vereist om een geldig antwoord te ontvangen; indien niet aanwezig, wordt een `401 Unauthorized` fout geretourneerd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
