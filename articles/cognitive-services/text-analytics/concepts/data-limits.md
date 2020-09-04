---
title: Gegevenslimieten voor de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Gegevenslimieten voor de Text Analytics-API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 068c2dc698e9f0b6d6f2f6486dff863c1343b178
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258285"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Gegevens- en frequentielimieten voor de Text Analytics-API
<a name="data-limits"></a>

Lees dit artikel voor informatie over de limieten voor de grootte en de frequenties waarmee u gegevens naar de Text Analytics-API kunt verzenden. 

## <a name="data-limits"></a>Gegevenslimieten

> [!NOTE]
> * Als u grotere documenten moet analyseren dan volgens de limiet is toegestaan, kunt u de tekst opdelen in kleinere tekstfragmenten voordat u ze naar de API kunt verzenden. 
> * Een document bestaat uit één tekenreeks van teksttekens.  

| Limiet | Waarde |
|------------------------|---------------|
| Maximale grootte van één document | 5\.120 tekens, gemeten volgens [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). Geldt ook voor de Text Analytics voor de statuscontainer. |
| Maximale grootte van de hele aanvraag | 1 MB. Geldt ook voor de Text Analytics voor de statuscontainer. |

Het maximumaantal documenten dat u in één aanvraag kunt verzenden, is afhankelijk van de API-versie en functie die u gebruikt.

#### <a name="version-3"></a>[Versie 3](#tab/version-3)

De volgende limieten zijn in v3 van de API gewijzigd. Als u de onderstaande limieten overschrijdt, wordt een HTTP 400-foutcode gegenereerd.


| Functie | Max. aantal documenten per aanvraag | 
|----------|-----------|
| Taaldetectie | 1000 |
| Sentimentanalyse | 10 |
| Sleuteltermextractie | 10 |
| Herkenning van benoemde entiteiten | 5 |
| Entiteiten koppelen | 5 |
| Text Analytics voor statuscontainer | 1000 |
#### <a name="version-2"></a>[Versie 2](#tab/version-2)

| Functie | Max. aantal documenten per aanvraag | 
|----------|-----------|
| Taaldetectie | 1000 |
| Sentimentanalyse | 1000 |
| Sleuteltermextractie | 1000 |
| Herkenning van benoemde entiteiten | 1000 |
| Entiteiten koppelen | 1000 |

---

## <a name="rate-limits"></a>Frequentielimieten

Uw frequentielimiet hangt af van uw [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Deze limieten gelden voor beide versies van de API. Deze frequentielimieten zijn niet van toepassing op de Text Analytics voor de statuscontainer, die geen ingestelde frequentielimiet heeft.

| Laag          | Aanvragen per seconde | Aanvragen per minuut |
|---------------|---------------------|---------------------|
| S/multi-service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Aanvragen worden afzonderlijk voor elke Text Analytics-functie gemeten. U kunt bijvoorbeeld het maximumaantal aanvragen voor uw prijscategorie tegelijkertijd naar elke functie verzenden.  


## <a name="see-also"></a>Zie ook

* [Wat is de Text Analytics-API?](../overview.md)
* [Prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
