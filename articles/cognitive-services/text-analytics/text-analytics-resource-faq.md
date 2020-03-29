---
title: Veelgestelde vragen over de Text Analytics API
titleSuffix: Azure Cognitive Services
description: Vind antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot de Text Analytics API voor Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837124"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Veelgestelde vragen (FAQ) over de Text Analytics Cognitive Service

 Vind antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot de Text Analytics API voor Microsoft Cognitive Services op Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Kan Text Analytics sarcasme identificeren?

Analyse is voor positief-negatief sentiment in plaats van stemmingsdetectie.

Er is altijd een zekere mate van onnauwkeurigheid in sentiment analyse, maar het model is het meest nuttig wanneer er geen verborgen betekenis of subtekst aan de inhoud. Ironie, sarcasme, humor en eveneens genuanceerde inhoud vertrouwen op culturele context en normen om intentie over te brengen. Dit type inhoud is een van de meest uitdagende om te analyseren. Typisch, de grootste discrepantie tussen een bepaalde score geproduceerd door de analyzer en een subjectieve beoordeling door een mens is voor de inhoud met genuanceerde betekenis.

## <a name="can-i-add-my-own-training-data-or-models"></a>Kan ik mijn eigen trainingsgegevens of -modellen toevoegen?

Nee, de modellen zijn voorgetraind. De enige bewerkingen die beschikbaar zijn op geüploade gegevens zijn scoring, key phrase extraction en taaldetectie. We hosten geen aangepaste modellen. Als u aangepaste machine learning-modellen wilt maken en hosten, u rekening houden met de [mogelijkheden voor machine learning in Microsoft R Server.](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)

## <a name="can-i-request-additional-languages"></a>Kan ik extra talen aanvragen?

Sentimentanalyse en sleutelzinextractie zijn beschikbaar voor een [select aantal talen](text-analytics-supported-languages.md). Natuurlijke taalverwerking is complex en vereist aanzienlijke tests voordat nieuwe functionaliteit kan worden vrijgegeven. Om deze reden vermijden we vooraf aangekondigde ondersteuning, zodat niemand afhankelijk is van functionaliteit die meer tijd nodig heeft om te rijpen. 

Om ons te helpen prioriteren aan welke talen we vervolgens moeten werken, stem je op specifieke talen op [User Voice.](https://cognitive.uservoice.com/forums/555922-text-analytics) 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Waarom geeft de extractie van sleutelzinnen sommige woorden terug, maar andere niet?

Sleutelzin extractie elimineert niet-essentiële woorden en standalone bijvoeglijke naamwoorden. Bijvoeglijk naamwoord-zelfstandige combinaties, zoals "spectaculair uitzicht" of "mistig weer" worden samen geretourneerd.

Over het algemeen bestaat de uitvoer uit zelfstandig naamwoorden en objecten van de zin. Output wordt vermeld in volgorde van belangrijkheid, waarbij de eerste zin de belangrijkste is. Het belang wordt gemeten aan de doordat een bepaald begrip wordt genoemd, of de relatie van dat element met andere elementen in de tekst.

## <a name="why-does-output-vary-given-identical-inputs"></a>Waarom varieert de output, gezien identieke ingangen?

Verbeteringen aan modellen en algoritmen worden aangekondigd als de wijziging groot is, of stilletjes in de service wordt geslipstreamd als de update klein is. Na verloop van tijd u merken dat dezelfde tekstinvoer resulteert in een andere sentimentscore of toetswoorduitvoer. Dit is een normaal en opzettelijk gevolg van het gebruik van beheerde machine learning-resources in de cloud.

## <a name="next-steps"></a>Volgende stappen

Is uw vraag over een ontbrekende functie of functionaliteit? Overweeg om het aan te vragen of te stemmen op onze [UserVoice website.](https://cognitive.uservoice.com/forums/555922-text-analytics)

## <a name="see-also"></a>Zie ook

 [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitieve services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
