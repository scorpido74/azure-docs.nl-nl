---
title: Veelgestelde vragen - Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat antwoorden op veelgestelde vragen over de aangepaste vertaler azure cognitive services.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836598"
---
# <a name="custom-translator-frequently-asked-questions"></a>Custom Translator veelgestelde vragen

Dit artikel bevat antwoorden op veelgestelde vragen over [aangepaste vertaler.](https://portal.customtranslator.azure.ai)

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Wat zijn de huidige beperkingen in Custom Translator?

Er zijn beperkingen en beperkingen met betrekking tot bestandsgrootte, modeltraining en modelimplementatie. Houd deze beperkingen in gedachten bij het instellen van uw training om een model te bouwen in Aangepaste vertaler.

- Ingediende bestanden moeten minder dan 100 MB groot zijn.
- Eentalige gegevens worden niet ondersteund.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Wanneer moet ik implementatie aanvragen voor een getraind vertaalsysteem?

Het kan verschillende trainingen vergen om het optimale vertaalsysteem voor uw project te creëren. U proberen meer trainingsgegevens of zorgvuldig gefilterde gegevens te gebruiken, als de BLEU-score en/of de testresultaten niet bevredigend zijn. U moet streng en voorzichtig zijn bij het ontwerpen van uw tuning set en uw test set, om volledig representatief te zijn voor de terminologie en stijl van het materiaal dat u wilt vertalen. U liberaler zijn in het samenstellen van uw trainingsgegevens en experimenteren met verschillende opties. Vraag een systeemimplementatie aan wanneer u tevreden bent met de vertalingen in uw systeemtestresultaten, geen gegevens meer hebt om toe te voegen aan de training om uw getrainde systeem te verbeteren en u wilt toegang krijgen tot het getrainde model via API's.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hoeveel getrainde systemen kunnen worden ingezet in een project?

Per project kan slechts één getraind systeem worden geïmplementeerd. Het kan verschillende trainingen vergen om een geschikt vertaalsysteem voor uw project te maken en we raden u aan om de implementatie van een training aan te vragen die u het beste resultaat geeft. U de kwaliteit van de training bepalen aan de hand van de BLEU-score (hoger is beter) en door met beoordelaars te overleggen alvorens te besluiten dat de kwaliteit van vertalingen geschikt is voor implementatie.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Wanneer kan ik verwachten dat mijn trainingen worden ingezet?

De implementatie duurt over het algemeen minder dan een uur.

## <a name="how-do-you-access-a-deployed-system"></a>Hoe krijg je toegang tot een geïmplementeerd systeem?

Geïmplementeerde systemen zijn toegankelijk via de Microsoft Translator Text API V3 door de CategoryID op te geven. Meer informatie over de Translator Text API vindt u op de webpagina [API Reference.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hoe sla ik uitlijning en zinsbreuk over als mijn gegevens al zijn uitgelijnd?

De aangepaste vertaler slaat zinsuitlijning en zinsbreuk over `.align` voor TMX-bestanden en voor tekstbestanden met de extensie. `.align`bestanden geven gebruikers de mogelijkheid om het uitspreken en uitlijnproces van Custom Translator over te slaan voor de bestanden die perfect zijn uitgelijnd en geen verdere verwerking nodig hebben. We raden `.align` u aan de extensie alleen te gebruiken voor bestanden die perfect zijn uitgelijnd.

Als het aantal uitgepakte zinnen niet overeenkomt met de twee bestanden met dezelfde basisnaam, wordt de zinsaligner nog steeds uitgevoerd op `.align` bestanden.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Ik heb geprobeerd het uploaden van mijn TMX, maar het zegt "document verwerking mislukt".

Zorg ervoor dat de TMX voldoet aan de <https://www.gala-global.org/tmx-14b>TMX 1.4b Specificatie op .
