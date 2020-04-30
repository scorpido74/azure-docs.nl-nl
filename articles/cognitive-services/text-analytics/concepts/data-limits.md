---
title: Gegevens limieten voor de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: De gegevens beperkingen voor de Text Analytics-API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 4af2d060c11b804c5fa09bfdabbcb9753f7d5885
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204412"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Gegevens en frequentie limieten voor de Text Analytics-API
<a name="data-limits"></a>

In dit artikel vindt u informatie over de limieten voor de grootte en de snelheid waarmee u gegevens kunt verzenden naar Text Analytics-API. 

## <a name="data-limits"></a>Gegevenslimieten

> [!NOTE]
> * Als u grotere documenten wilt analyseren dan de limiet toestaat, kunt u de tekst opsplitsen in kleinere stukken tekst voordat u deze naar de API verzendt. 
> * Een document is een enkele teken reeks met tekst tekens.  

| Limiet | Waarde |
|------------------------|---------------|
| Maximale grootte van één document | 5.120 tekens zoals gemeten door [StringInfo. LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximale grootte van de hele aanvraag | 1 MB |

Het maximum aantal documenten dat u in één aanvraag kunt verzenden, is afhankelijk van de API-versie en de functie die u gebruikt.

#### <a name="version-30-preview"></a>[Versie 3,0-Preview](#tab/version-3)

> [!NOTE]
> Als uw v3 API-aanvraag deze limiet overschrijdt, maar binnen de v2-limieten valt, wordt er een waarschuwing weer gegeven in de API-reactie. In plaats van 7/15/2020 wordt een 400-fout code geretourneerd. 

De volgende limieten zijn gewijzigd in v3 van de API. Als u de onderstaande limieten overschrijdt, wordt er een waarschuwing gegenereerd in de API-reactie.


| Functie | Maximum aantal documenten per aanvraag | 
|----------|-----------|
| Taaldetectie | 1000 |
| Sentimentanalyse | 10 |
| Sleuteltermextractie | 10 |
| Herkenning van benoemde entiteiten | 5 |
| Entiteiten koppelen | 5 |

#### <a name="version-2"></a>[Versie 2](#tab/version-2)

| Functie | Maximum aantal documenten per aanvraag | 
|----------|-----------|
| Taaldetectie | 1000 |
| Sentimentanalyse | 1000 |
| Sleuteltermextractie | 1000 |
| Herkenning van benoemde entiteiten | 1000 |
| Entiteiten koppelen | 1000 |

---

## <a name="rate-limits"></a>Frequentie limieten

De frequentie limiet is afhankelijk van uw [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Deze limieten zijn hetzelfde voor beide versies van de API.

| Laag          | Aanvragen per seconde | Aanvragen per minuut |
|---------------|---------------------|---------------------|
| S/meerdere services | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Aanvragen worden afzonderlijk gemeten voor elke Text Analytics-functie. U kunt bijvoorbeeld tegelijkertijd het maximum aantal aanvragen voor uw prijs categorie verzenden naar elke functie.  


## <a name="see-also"></a>Zie ook

* [Wat is het Text Analytics-API](../overview.md)
* [Prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
