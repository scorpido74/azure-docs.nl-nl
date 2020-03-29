---
title: Aanvraaglimieten - Translator Text API
titleSuffix: Azure Cognitive Services
description: In dit artikel worden aanvraaglimieten voor de Translator Text API weergegeven. Er worden kosten in rekening gebracht op basis van het aantal tekens, niet op verzoekfrequentie met een limiet van 5.000 tekens per aanvraag. Tekenlimieten zijn gebaseerd op een abonnement, waarbij F0 beperkt is tot 2 miljoen tekens per uur.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498929"
---
# <a name="request-limits-for-translator-text"></a>Limieten aanvragen voor tekst vertaler

In dit artikel vindt u beperkingslimieten voor de Translator Text API. Services omvatten vertaling, transliteratie, detectie van zinslengte, taaldetectie en alternatieve vertalingen.

## <a name="character-and-array-limits-per-request"></a>Teken- en arraylimieten per aanvraag

Elke vertaalaanvraag is beperkt tot 5.000 tekens, voor alle doeltalen waarnaar u vertaalt. Als u bijvoorbeeld een vertaalverzoek van 1.500 tekens verzendt om naar 3 verschillende talen te vertalen, resulteert dit in een aanvraaggrootte van 1.500x3 = 4.500 tekens, die voldoet aan de aanvraaglimiet. Er worden kosten in rekening gebracht per teken, niet door het aantal aanvragen. Het wordt aanbevolen om kortere aanvragen te verzenden.

In de volgende tabel worden arrayelement- en tekenlimieten weergegeven voor elke bewerking van de Translator Text API.

| Bewerking | Maximale grootte van arrayelement |   Maximum aantal matrixelementen |  Maximale aanvraaggrootte (tekens) |
|:----|:----|:----|:----|
| Vertalen | 5.000 | 100   | 5.000 |
| Transcriberen | 5.000 | 10    | 5.000 |
| Detecteren | 10.000 | 100 |   50,000 |
| BreakSentence | 10.000    | 100 | 50,000 |
| Opzoeken in woordenlijst| 100 |  10  | 1000 |
| Voorbeelden in woordenlijst | 100 voor tekst en 100 voor vertaling (200 in totaal)| 10|   2.000 |

## <a name="character-limits-per-hour"></a>Tekenlimieten per uur

Je tekenlimiet per uur is gebaseerd op de abonnementslaag Vertalerstekst. 

Het uurquotum moet gelijkmatig gedurende het hele uur worden verbruikt. Bij de F0-laaglimiet van 2 miljoen tekens per uur moeten tekens bijvoorbeeld niet sneller worden verbruikt dan ongeveer 33.300 tekens per minuut schuifvenster (2 miljoen tekens gedeeld door 60 minuten).

Als u deze limieten bereikt of overschrijdt of een te groot deel van het quotum in een korte periode verzendt, ontvangt u waarschijnlijk een niet-quotumrespons. Er zijn geen beperkingen op gelijktijdige aanvragen.

| Laag | Tekenlimiet |
|------|-----------------|
| F0 | 2 miljoen tekens per uur |
| S1 | 40 miljoen tekens per uur |
| S2 / C2 | 40 miljoen tekens per uur |
| S3 / C3 | 120 miljoen tekens per uur |
| S4 / C4 | 200 miljoen tekens per uur |

Limieten voor [abonnementen met meerdere diensten](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) zijn hetzelfde als de S1-laag.

Deze limieten zijn beperkt tot de standaard vertaalmodellen van Microsoft. Aangepaste vertaalmodellen die Aangepaste vertaler gebruiken, zijn beperkt tot 1.800 tekens per seconde.

## <a name="latency"></a>Latentie

De Translator Text API heeft een maximale latentie van 15 seconden met standaardmodellen en 120 seconden bij het gebruik van aangepaste modellen. Reacties voor *tekst binnen 100 tekens* worden doorgaans in 150 milliseconden geretourneerd tot 300 milliseconden. De aangepaste vertalersmodellen hebben vergelijkbare latentiekenmerken op een aanhoudende aanvraagsnelheid en kunnen een hogere latentie hebben wanneer uw aanvraagsnelheid met tussenpozen is. De responstijden variëren afhankelijk van de grootte van het verzoek en het taalpaar. Als u binnen dat tijdsbestek geen vertaling of [foutreactie](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) ontvangt, controleert u uw code, uw netwerkverbinding en probeert u het opnieuw. 

## <a name="sentence-length-limits"></a>Lengtelimieten voor zinnen

Bij het gebruik van de functie [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) is de lengte van de zin beperkt tot 275 tekens. Er zijn uitzonderingen voor deze talen:

| Taal | Code | Tekenlimiet |
|----------|------|-----------------|
| Chinees | Zh | 132 |
| Duits | de | 290 |
| Italiaans | it | 280 |
| Japans | ja | 150 |
| Portugees | pt | 290 |
| Spaans | Ja | 280 |
| Italiaans | it | 280 |
| Thai | Th | 258 |

> [!NOTE]
> Deze limiet is niet van toepassing op vertalingen.

## <a name="next-steps"></a>Volgende stappen

* [Prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionale beschikbaarheid](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text-API-verwijzing](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
