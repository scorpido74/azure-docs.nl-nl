---
title: Aanvraag limieten-Translator Text-API
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u de aanvraag limieten voor de Translator Text-API. De kosten worden berekend op basis van het aantal tekens, niet de aanvraag frequentie met een limiet van 5.000 tekens per aanvraag. De teken limieten zijn gebaseerd op abonnementen, met F0 tot 2.000.000 tekens per uur.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 79cf87cef4e58cf4e5a2039f30289d55038c8b92
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671954"
---
# <a name="request-limits-for-translator-text"></a>Aanvraag limieten voor Translator Text

Dit artikel bevat beperkings limieten voor de Translator Text-API. Services omvatten omzetting, vele, detectie van de lengte van de zinnen, taal detectie en alternatieve vertalingen.

## <a name="character-and-array-limits-per-request"></a>Limieten voor tekens en matrices per aanvraag

Elke Vertaal aanvraag is beperkt tot 5.000 tekens. U betaalt per teken, niet op het aantal aanvragen. Het wordt aanbevolen om kortere aanvragen te verzenden.

De volgende tabel bevat een overzicht van matrix elementen en teken limieten voor elke bewerking van de Translator Text-API.

| Bewerking | Maximale grootte van matrix element |   Maximum aantal matrix elementen |  Maximale aanvraag grootte (tekens) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10.000 | 100 |   50,000 |
| BreakSentence | 10.000    | 100 | 50,000 |
| Opzoeken in woordenlijst| 100 |  10  | 1000 |
| Voorbeelden in woordenlijst | 100 voor tekst en 100 voor vertaling (200 in totaal)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>Maximum aantal tekens per uur

De teken limiet per uur is gebaseerd op uw Translator Text Subscription. 

Het quotum per uur moet gelijkmatig over het hele uur worden verbruikt. Bijvoorbeeld: bij de limiet van F0 van 2.000.000 tekens per uur moeten tekens worden verbruikt die niet sneller zijn dan ongeveer 33.300 tekens per minuut sliding window (2.000.000 tekens gedeeld door 60 minuten).

Als u deze limieten bereikt of overschrijdt, of als u een deel van het quotum te groot verzendt, wordt er waarschijnlijk een out-of-quota reactie weer gegeven. Er zijn geen limieten voor gelijktijdige aanvragen.

| Laag | Teken limiet |
|------|-----------------|
| F0 | 2\.000.000 tekens per uur |
| S1 | 40.000.000 tekens per uur |
| S2/C2 | 40.000.000 tekens per uur |
| S3/C3 | 120.000.000 tekens per uur |
| S4/C4 | 200.000.000 tekens per uur |

De limieten voor [meerdere service abonnementen](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) zijn gelijk aan die van de S1-laag.

Deze limieten zijn beperkt tot de standaard Vertaal modellen van micro soft. Aangepaste Vertaal modellen die gebruikmaken van aangepaste vertalers, zijn beperkt tot 1.800 tekens per seconde.

## <a name="latency"></a>Latentie

Het Translator Text-API heeft een maximale latentie van 15 seconden met behulp van standaard modellen. Vertaling met aangepaste modellen heeft een maximale latentie van 25 seconden. Op dit moment hebt u een resultaat of een time-outantwoord ontvangen. Normaal gesp roken worden antwoorden in 150 milliseconden geretourneerd tot 300 milliseconden. Reactie tijden variëren op basis van de grootte van de aanvraag en het taal paar. Als u geen vertaling of een [fout](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) bericht binnen dat tijds bestek ontvangt, controleert u uw netwerk verbinding en probeert u het opnieuw.

## <a name="sentence-length-limits"></a>Maximale lengte van zin

Wanneer u de functie [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) gebruikt, is de lengte van de zin beperkt tot 275 tekens. Er zijn uitzonde ringen voor deze talen:

| Taal | Code | Teken limiet |
|----------|------|-----------------|
| Chinees | zh | 132 |
| Duits | de | 290 |
| Italiaans | it | 280 |
| Japans | ja | 150 |
| Portugees | pt | 290 |
| Spaans | es | 280 |
| Italiaans | it | 280 |
| Thais | e | 258 |

> [!NOTE]
> Deze limiet is niet van toepassing op vertalingen.

## <a name="next-steps"></a>Volgende stappen

* [Prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionale beschikbaarheid](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referentie voor v3-Translator Text-API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
