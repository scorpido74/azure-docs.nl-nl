---
title: Wat is er nieuw in de Text Analytics-API
titleSuffix: Text Analytics - Azure Cognitive Services
description: In dit artikel vindt u informatie over nieuwe releases en functies voor de Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 6ef23c03f01cd8e83d4327f358eaf12209659ce8
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286461"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Wat is er nieuw in de Text Analytics-API?

De Text Analytics-API wordt doorlopend bijgewerkt. In dit artikel vindt u informatie over nieuwe releases en functies, zodat u op de hoogte blijft van recente ontwikkelingen.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Named entity Recognition v3 Public Preview-oktober 2019

De volgende versie van named entity Recognition (NER) is nu beschikbaar voor open bare preview en biedt uitgebreide detectie en categorisatie van entiteiten die in tekst zijn gevonden. De oplossing biedt het volgende:

* Herkenning van de volgende nieuwe entiteits typen:
    * Telefoonnummer
    * IP-adres

* Een [Nieuw eind punt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) voor het herkennen van entiteits typen van persoonlijke gegevens (alleen Engels)
* Afzonderlijke eind punten voor [entiteits herkenning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) en [entiteits koppeling](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

Koppeling van entiteit ondersteunt Engels en Spaans. NER taal ondersteuning varieert per entiteits type. 

> [!div class="nextstepaction"]
> [Meer informatie over named entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Open bare preview van Sentimentanalyse v3-oktober 2019

De [volgende versie van sentimentanalyse](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) is nu beschikbaar voor open bare preview en biedt aanzienlijke verbeteringen in de nauw keurigheid en Details van de tekst categorisatie en Score van de API. Daarnaast vindt u het volgende:

* Automatische labeling voor verschillende gevoel in tekst.
* Sentiment analyse en uitvoer op het niveau van een document en zin. 

Het biedt ondersteuning voor Engels (`en`), Japans (`ja`) vereenvoudigd Chinees (`zh-Hans`), traditioneel Chinees (`zh-Hant`), Frans (`fr`), Italiaans (`it`), Spaans (`es`), Nederlands (`nl`), Portugees (`pt`) en Duits (`de`), en is beschikbaar in de volgende regio's: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`en `West US 2`. 

> [!div class="nextstepaction"]
> [Meer informatie over Sentimentanalyse v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Text Analytics-API?](overview.md)  
* [Voor beelden van gebruikers scenario's](text-analytics-user-scenarios.md)
* [Sentiment analyse](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Taal detectie](how-tos/text-analytics-how-to-language-detection.md)
* [Entiteit herkenning](how-tos/text-analytics-how-to-entity-linking.md)
* [Extractie van sleutel woorden](how-tos/text-analytics-how-to-keyword-extraction.md)
