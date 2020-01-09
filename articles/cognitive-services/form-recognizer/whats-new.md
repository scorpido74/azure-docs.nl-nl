---
title: Wat is er nieuw in Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Meer informatie over de meest recente wijzigingen in de API voor formulier herkenning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: cb5639dcf0e13ea03d34604816b3939085674c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456364"
---
# <a name="whats-new-in-form-recognizer"></a>Wat is er nieuw in Form Recognizer?

In dit artikel worden de belangrijkste wijzigingen met nieuwe versies van de API voor formulier herkenning gemarkeerd.

> [!NOTE]
> De Quick starts en hand leidingen in deze documentenset gebruiken altijd de nieuwste versie van de API, tenzij ze anders worden opgegeven.

## <a name="form-recognizer-20-preview"></a>Formulier Recognizer 2,0 (preview-versie)

> [!IMPORTANT]
> Formulier Recognizer 2,0 is momenteel beschikbaar voor abonnementen in de regio's `West US 2` en `West Europe`. Als uw abonnement zich niet in deze regio bevindt, gebruikt u de 1,0-API. De Quick starts voor training en het gebruik van een aangepast model zijn beschikbaar voor zowel v 1.0 als v 2.0.

### <a name="new-features"></a>Nieuwe functies

* **Aangepast model**
  * **Trainen met labels** U kunt nu een aangepast model trainen met hand matig gelabelde gegevens. Dit resulteert in betere uitvoering van modellen en kan modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels werken.
  * **ASYNCHRONE API** U kunt asynchrone API-aanroepen gebruiken om met grote gegevens sets en bestanden te trainen en te analyseren.
  * **Ondersteuning voor TIFF-bestanden** U kunt nu gegevens uit TIFF-documenten trainen en ophalen.
  * **Verbeteringen in de extractie nauwkeurigheid**

* **Gebaseerd ontvangst model**
  * **Fooien** U kunt nu fooie bedragen en andere handgeschreven waarden extra heren.
  * **Extractie van regel items** U kunt waarden van het regel item extra heren uit de bevestigingen.
  * **Betrouwbaarheids waarden** U kunt het vertrouwen van het model voor elke geëxtraheerde waarde weer geven.
  * **Verbeteringen in de extractie nauwkeurigheid**

* **Indelings extractie** U kunt nu de indelings-API gebruiken om tekst gegevens en tabel gegevens op te halen uit uw formulieren.

### <a name="custom-model-api-changes"></a>Wijzigingen in het aangepaste model-API

Alle Api's voor training en het gebruik van aangepaste modellen zijn hernoemd en sommige synchrone methoden zijn nu asynchroon. De volgende belang rijke wijzigingen zijn:

* Het proces voor het trainen van een model is nu asynchroon. U initieert training via de API-aanroep van **/Custom/models** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **aangepaste/modellen/{modelID}** om de resultaten van de training te retour neren.
* De extractie van sleutel/waarde wordt nu geïnitieerd door de API-aanroep van **/Custom/models/{modelID}/analyze** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **aangepaste/modellen/{modelID}/analyzeResults/{resultID}** om de resultaten van de extractie te retour neren.
* Bewerkings-Id's voor de trein bewerking zijn nu gevonden in de **locatie** header van http-antwoorden, niet op de locatie van de **bewerking** .

### <a name="receipt-api-changes"></a>Wijzigingen in de ontvangst-API

De naam van de Api's voor het lezen van de verkoop ontvangst is gewijzigd.

* Het uitpakken van ontvangst gegevens wordt nu geïnitieerd door de API-aanroep van **/prebuilt/Receipt/analyze** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **/prebuilt/Receipt/analyzeResults/{resultID}** om de resultaten van de extractie te retour neren.

### <a name="output-format-changes"></a>Wijzigingen in de uitvoer indeling

De JSON-antwoorden voor alle API-aanroepen hebben nieuwe notaties. Sommige sleutels en waarden zijn toegevoegd, verwijderd of de naam ervan is gewijzigd. Bekijk de Quick starts voor voor beelden van de huidige JSON-indelingen.

## <a name="next-steps"></a>Volgende stappen

Voer een [Snelstartgids](quickstarts/curl-train-extract.md) uit om aan de slag te gaan met de [API voor formulier herkenning](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).