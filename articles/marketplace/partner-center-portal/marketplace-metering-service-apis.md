---
title: Api's voor marktplaatsmeetservice | Azure Marketplace
description: Gebruiksgebeurtenis voor SaaS-aanbiedingen in de Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275744"
---
# <a name="marketplace-metering-service-apis"></a>Service-API's voor Marketplace-meting

Met de API voor gebruiksgebeurtenissen u gebruiksgebeurtenissen uitzenden voor een specifieke gekochte entiteit. De aanvraag voor gebruiksgebeurtenis verwijst naar de dimensie metingservices die door de uitgever is gedefinieerd bij het publiceren van de aanbieding.

## <a name="usage-event"></a>Gebruiksgebeurtenis

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Queryparameters:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | De versie van de bewerking die voor dit verzoek moet worden gebruikt. Nieuwste API-versie is 2018-08-31. |

*Kopteksten aanvragen:*

| Inhoudstype       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| `x-ms-correlationid` | Unieke tekenreekswaarde voor bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| `authorization`   | [Download JSON webtoken (JWT) toondertoken.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Opmerking: Bij het indienen van `Bearer` het HTTP-verzoek u het voorvoegsel van het token dat is verkregen via de koppeling waarnaar wordt verwezen, worden uitgevoerd. |

*Verzoek:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Antwoorden

Code: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Code: 400 <br>
Slecht verzoek, ontbrekende of ongeldige gegevens verstrekt of verlopen

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Code: 403<br>
Slecht verzoek, ontbrekende of ongeldige gegevens verstrekt of verlopen

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Code: 409<br>
Conflict, wanneer we het gebruik oproep voor het gebruik resource ID ontvangen, en effectief gebruik, die al bestaat. Het antwoord `additionalInfo` bevat een veld met informatie over het geaccepteerde bericht.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Gebeurtenis Batchgebruik

Met de API voor batchgebruikgebeurtenissen u gebruiksgebeurtenissen voor meer dan één gekochte entiteit tegelijk uitzenden. De gebeurtenisaanvraag batchgebruik verwijst naar de dimensie metingservices die door de uitgever is gedefinieerd bij het publiceren van de aanbieding.

>[!Note]
>U meerdere SaaS-aanbiedingen registreren op de commerciële marktplaats van Microsoft. Elke geregistreerde SaaS-aanbieding heeft een unieke Azure AD-toepassing die is geregistreerd voor verificatie- en autorisatiedoeleinden. De gebeurtenissen die in batch worden uitgestoten, moeten behoren tot aanbiedingen met dezelfde Azure AD-toepassing op het moment van registratie van de aanbieding.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Queryparameters:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | De versie van de bewerking die voor dit verzoek moet worden gebruikt. Nieuwste API-versie is 2018-08-31. |

*Kopteksten aanvragen:*

| Inhoudstype       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unieke tekenreekswaarde voor het bijhouden van de aanvraag van de client, bij voorkeur een GUID. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| `x-ms-correlationid` | Unieke tekenreekswaarde voor bewerking op de client. Deze parameter correleert alle gebeurtenissen van clientbewerking met gebeurtenissen aan de serverzijde. Als deze waarde niet wordt opgegeven, wordt er een gegenereerd en wordt deze weergegeven in de antwoordkoppen. |
| `authorization`      | [Download JSON webtoken (JWT) toondertoken.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Opmerking: Bij het indienen van `Bearer` het HTTP-verzoek u het voorvoegsel van het token dat is verkregen via de koppeling waarnaar wordt verwezen, worden uitgevoerd.  |

*Verzoek:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Antwoorden

Code: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Beschrijving van de statuscode waarnaar wordt verwezen in `BatchUsageEvent` API-respons:

| Statuscode  | Beschrijving |
| ---------- | -------------------- |
| `Accepted` | Geaccepteerde code. |
| `Expired` | Verlopen gebruik. |
| `Duplicate` | Dupliceer het gebruik. |
| `Error` | Foutcode. |
| `ResourceNotFound` | De geleverde gebruiksbron is ongeldig. |
| `ResourceNotAuthorized` | U bent niet bevoegd om gebruik te maken van deze bron. |
| `InvalidDimension` | De dimensie waarvoor het gebruik wordt doorgegeven, is ongeldig voor deze aanbieding/abonnement. |
| `InvalidQuantity` | De doorgegeven hoeveelheid is < 0. |
| `BadArgument` | De invoer ontbreekt of misvormd is. |

Code: 400<br>
Slecht verzoek, ontbrekende of ongeldige gegevens verstrekt of verlopen

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Code: 403<br>
Gebruiker is onbevoegd om dit gesprek te voeren

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Volgende stappen

Zie [Facturering met een gemeten factuur met een datameter](./saas-metered-billing.md)voor meer informatie.
