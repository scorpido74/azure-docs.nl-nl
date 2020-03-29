---
title: Voorbeeldvan gebruikersscenario's voor de Text Analytics API
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel om enkele veelvoorkomende scenario's te zien voor het integreren van de Text Analytics API in uw services en processen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219245"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Voorbeeldvan gebruikersscenario's voor de Text Analytics API

De Text Analytics API is een cloudservice die geavanceerde natuurlijke taalverwerking via tekst biedt. In dit artikel worden enkele voorbeeldvoorbeelden beschreven voor het integreren van de API in uw bedrijfsoplossingen en -processen. 

## <a name="analyze-survey-results"></a>Enquêteresultaten analyseren

Verkrijg inzichten uit de resultaten van klant- en werknemersenquêtes door de ruwe tekstreacties te verwerken met Sentiment Analysis. Verzamel de bevindingen voor analyse, follow-up en driving engagements.

![Een afbeelding waarin wordt beschreven hoe u sentimentanalyses uitvoert op enquêtes van klanten en werknemers.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Recorded inbound customer calls analyseren

Haal inzichten uit gesprekken met de klantenservice met tekst naar spraak, sentimentanalyse en sleutelwoordextractie. Geef de resultaten weer in het Power BI-dashboard of een portal om klanten beter te begrijpen, trends in de klantenservice te markeren en de betrokkenheid van klanten te vergroten. Api-aanvragen verzenden als een batch voor rapportage, of in real-time voor interventie. Zie de voorbeeldcode [op GitHub](https://github.com/rlagh2/callcenteranalytics).

![Een afbeelding waarin wordt beschreven hoe u inzichten uit klantenservicegesprekken automatiseren met behulp van sentimentanalyse](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Ondersteuningsincidenten verwerken en categoriseren

Gebruik Key Phrase Extraction en Entity Recognition om ondersteuningsaanvragen te verwerken die zijn ingediend in een ongestructureerde tekstuele indeling. Gebruik de uitgepakte zinnen en entiteiten om de aanvragen voor resourceplanning en trendanalyse te categoriseren.

![Een afbeelding waarin wordt beschreven hoe de extractie van sleutelzinnen en entiteitsherkenning kunnen worden gebruikt om incidentrapporten en trends te categoriseren](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Houd de sociale media-feeds van uw product in de gaten

Houd de productfeedback van gebruikers bij op de twitter- of Facebook-pagina van je product. Gebruik de gegevens om het sentiment van klanten ten aanzien van nieuwe producten te analyseren, belangrijke zinnen over functies en functieverzoeken te extraheren of klachten van klanten op te pakken terwijl deze zich voordoen. Zie het voorbeeld [Microsoft Flow-sjabloon](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Een afbeelding waarin wordt beschreven hoe u uw product en bedrijfsfeedback op sociale media controleren met behulp van de extractie van belangrijke zinnen](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Documenten met gevoelige informatie classificeren en redacten

Gebruik Named Entity Recognition om persoonlijke en gevoelige informatie in documenten te identificeren. Gebruik de gegevens om documenten te classificeren of te rederen, zodat ze veilig kunnen worden gedeeld.

![Een afbeelding waarin wordt beschreven hoe u NER gebruiken om persoonlijke gegevens te detecteren en documenten te classificeren en te redleren](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>Volgende stappen

* [Wat is Text Analytics-API?](overview.md)
* [Een aanvraag verzenden naar de Text Analytics API via de clientbibliotheek](quickstarts/text-analytics-sdk.md)
