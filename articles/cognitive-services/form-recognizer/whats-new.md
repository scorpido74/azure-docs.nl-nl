---
title: Wat is er nieuw in Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Inzicht in de laatste wijzigingen in de API voor formulierherkenning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531079"
---
# <a name="whats-new-in-form-recognizer"></a>Wat is er nieuw in Form Recognizer?

De form recognizer-service wordt voortdurend bijgewerkt. Gebruik dit artikel om op de hoogte te blijven van functieverbeteringen, fixes en documentatie-updates.

> [!NOTE]
> De quickstarts en hulplijnen voor Formulierherkenning gebruiken altijd de nieuwste versie van de API, tenzij opgegeven.

## <a name="march-2020"></a>Maart 2020 

### <a name="new-features"></a>Nieuwe functies

* **Waardetypen voor etikettering** U nu de typen waarden opgeven die u labelt met het voorbeeldlabelingsgereedschap Formulierherkenning. De volgende waardetypen en -variaties worden momenteel ondersteund:
  * `string`
    * in `no-whitespaces`gebreke blijven, ,`alphanumeric`
  * `number`
    * Standaard`currency`
  * `date` 
    * in `dmy`gebreke blijven, , `mdy``ymd`
  * `time`
  * `integer`

  Zie de handleiding [voor het labelen van voorbeelden](./quickstarts/label-tool.md#specify-tag-value-types) voor meer informatie over het gebruik van deze functie.


* **Tabelvisualisatie** In het voorbeeldlabelinggereedschap worden nu tabellen weergegeven die in het document zijn herkend. Hiermee u de tabellen bekijken die zijn herkend en uit het document zijn gehaald voordat u wordt gelabeld en geanalyseerd. Deze functie kan aan/uit worden geschakeld met de optie lagen.

  Dit is een voorbeeld van hoe tabellen worden herkend en geëxtraheerd:

  > [!div class="mx-imgBorder"]
  > ![Tabelvisualisatie met het voorbeeldlabelgereedschap](./media/whats-new/formre-table-viz.png)

    De uitgepakte tabellen zijn beschikbaar in `"pageResults"`de JSON-uitvoer onder .

  > [!IMPORTANT]
  > Labelen tabellen wordt niet ondersteund. Als tabellen niet worden herkend en automatisch worden geëxtraford, u ze alleen labelen als sleutel-/waardeparen. Wanneer u tabellen labelt als sleutel-/waardeparen, labelt u elke cel als een unieke waarde.

### <a name="extraction-enhancements"></a>Extractieverbeteringen

Deze release bevat extractieverbeteringen en nauwkeurigheidsverbeteringen, met name de mogelijkheid om meerdere sleutel/waardeparen in dezelfde tekstregel te labelen en te extraheren. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Voorbeeldlabelingtool is nu open source

Het voorbeeldlabelingsprogramma Form Recognizer is nu beschikbaar als een open-sourceproject. U het integreren in uw oplossingen en klantspecifieke wijzigingen aanbrengen om aan uw behoeften te voldoen.

Voor meer informatie over het voorbeeldlabelingshulpmiddel Form Recognizer controleert u de documentatie die beschikbaar is op [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>TLS 1.2 afdwingen

TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen voor deze service. Zie [Azure Cognitive Services-beveiliging](../cognitive-services-security.md)voor meer informatie.

## <a name="january-2020"></a>Januari 2020

Deze release introduceert de Form Recognizer 2.0 (preview). In de onderstaande secties vindt u meer informatie over nieuwe functies, verbeteringen en wijzigingen. 

### <a name="new-features"></a>Nieuwe functies

* **Aangepast model**
  * **Trainen met labels** U nu een aangepast model trainen met handmatig gelabelde gegevens. Dit resulteert in beter presterende modellen en kan modellen produceren die werken met complexe formulieren of formulieren die waarden zonder sleutels bevatten.
  * **Asynchrone API** U async API-aanroepen gebruiken om te trainen met en grote gegevenssets en bestanden te analyseren.
  * **Ondersteuning voor TIFF-bestanden** U nu trainen met en gegevens extraheren uit TIFF-documenten.
  * **Verbeteringen in extractienauwkeurigheid**

* **Vooraf gebouwd ontvangstmodel**
  * **Tipbedragen** U nu tipbedragen en andere handgeschreven waarden extraheren.
  * **Extractie van regelitem** U regelartikelwaarden uit ontvangstbewijzen extraheren.
  * **Betrouwbaarheidswaarden** U het vertrouwen van het model voor elke geëxtraheerde waarde bekijken.
  * **Verbeteringen in extractienauwkeurigheid**

* **Indelingextractie** U nu de Layout API gebruiken om tekstgegevens en tabelgegevens uit uw formulieren te extraheren.

### <a name="custom-model-api-changes"></a>Aangepaste model-API-wijzigingen

Alle API's voor training en het gebruik van aangepaste modellen zijn hernoemd en sommige synchrone methoden zijn nu asynchroon. De volgende zijn belangrijke veranderingen:

* Het proces van het trainen van een model is nu asynchroon. U start training via de **API-aanroep /custom/models.** Met deze aanroep wordt een bewerkings-id geretourneerd, die u doorgeven aan **aangepaste/modellen/{modelID}** om de trainingsresultaten terug te geven.
* Key/value extraction wordt nu geïnitieerd door de **/custom/models/{modelID}/analyze** API call. Met deze aanroep wordt een bewerkings-id geretourneerd, die u doorgeven aan **aangepaste/modellen/{modelID}/analyzeResults/{resultID}** om de extractieresultaten te retourneren.
* De bewerkings-id's voor de bewerking Trein worden nu gevonden in de **koplocatie** van HTTP-antwoorden en niet in de **kopfunctie Operatielocatie.**

### <a name="receipt-api-changes"></a>Wijziging van ontvangst-API's

De API's voor het lezen van verkoopbewijzen zijn hernoemd.

* Ontvangstgegevensextractie wordt nu gestart door de **/prebuilt/receipt/analyze** API call. Met deze aanroep retourneert een bewerkings-ID, die u doorgeven aan **/prebuilt/receipt/analyzeResults/{resultID}** om de extractieresultaten te retourneren.

### <a name="output-format-changes"></a>Wijzigingen in uitvoernotatie

De JSON-antwoorden voor alle API-aanroepen hebben nieuwe indelingen. Sommige sleutels en waarden zijn toegevoegd, verwijderd of hernoemd. Bekijk de quickstarts voor voorbeelden van de huidige JSON-indelingen.

## <a name="next-steps"></a>Volgende stappen

Voltooi een [snelle start](quickstarts/curl-train-extract.md) om aan de slag te gaan met de [FORM Recognizer API's](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).