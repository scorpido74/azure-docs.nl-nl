---
title: API-overwegingen | Azure Marketplace
description: Problemen met versie beheer, fout afhandeling en autorisatie bij het gebruik van Marketplace-Api's.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 07cdb5e44dde0ca655191111d0a23dbab85b4cb2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819727"
---
# <a name="api-considerations"></a>API-overwegingen


<a name="api-versioning"></a>API-versiebeheer
--------------

Er kunnen meerdere versies van de API tegelijk beschikbaar zijn. Clients moeten aangeven welke versie ze willen gebruiken door de `api-version`-para meter op te geven als onderdeel van de query teken reeks.

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

Voor alle Api's in deze Naslag informatie moet u de autorisatie-header door geven, samen met het Bearer-token dat is verkregen van Azure Active Directory (Azure AD). Deze header moet een geldig antwoord ontvangen. Als deze niet aanwezig is, wordt er een `401 Unauthorized` fout geretourneerd. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
