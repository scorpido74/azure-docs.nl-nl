---
title: Veelvoorkomende fouten en waarschuwingen-Azure Search
description: In dit artikel vindt u informatie en oplossingen voor veelvoorkomende fouten en waarschuwingen die u kunt tegen komen tijdens een AI-verrijking in Azure Search.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: 18befbfb924129518ac32a7fdddaa9ee573840b0
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936492"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Veelvoorkomende fouten en waarschuwingen van de AI-verrijkings pijplijn in Azure Search

In dit artikel vindt u informatie en oplossingen voor veelvoorkomende fouten en waarschuwingen die u kunt tegen komen tijdens een AI-verrijking in Azure Search.

## <a name="errors"></a>Fouten
Het indexeren stopt wanneer het aantal fouten groter is dan [' maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). De volgende secties kunnen u helpen bij het oplossen van fouten, waardoor het indexeren kan worden voortgezet.

### <a name="could-not-read-document"></a>Kan document niet lezen
De Indexeer functie kan het document niet lezen uit de gegevens bron. Dit kan gebeuren vanwege:

| Reason | Voorbeeld | Action |
| --- | --- | --- |
| inconsistente veld typen in verschillende documenten | Het type van de waarde komt niet overeen met het kolom Type. Kan `'{47.6,-122.1}'` niet opslaan in de kolom AUTHORS.  Verwacht type is JArray. | Zorg ervoor dat het type van elk veld hetzelfde is in verschillende documenten. Als bijvoorbeeld het eerste document `'startTime'` veld een datum/tijd is en in het tweede document een teken reeks is, wordt deze fout weer bereikt. |
| fouten van de onderliggende service van de gegevens bron | (van Cosmos DB) `{"Errors":["Request rate is large"]}` | Controleer uw opslag instantie om te controleren of deze in orde is. Mogelijk moet u de schaal/partitionering aanpassen. |
| tijdelijke problemen | Er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server. providers TCP-provider, fout: 0: een bestaande verbinding is geforceerd gesloten door de externe host | Af en toe zijn er onverwachte verbindings problemen. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |

### <a name="could-not-extract-document-content"></a>Kan document inhoud niet extra heren
Indexeer functie met een BLOB-gegevens bron kan de inhoud niet ophalen uit het document (bijvoorbeeld een PDF-bestand). Dit kan gebeuren vanwege:

| Reason | Voorbeeld | Action |
| --- | --- | --- |
| de BLOB overschrijdt de maximale grootte | Document is `'150441598'` bytes, wat groter is dan de maximale grootte `'134217728'` bytes voor de document extractie voor uw huidige servicelaag. | [BLOB-indexerings fouten](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| de BLOB heeft een niet-ondersteund inhouds type | Het document bevat een niet-ondersteund inhouds type `'image/png'` | [BLOB-indexerings fouten](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB is versleuteld | Het document kan niet worden verwerkt omdat het mogelijk is versleuteld of met een wacht woord is beveiligd. | [BLOB-instellingen](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| tijdelijke problemen | Fout bij verwerken van blob: De aanvraag is afgebroken: De aanvraag is geannuleerd. | Af en toe zijn er onverwachte verbindings problemen. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |

### <a name="could-not-parse-document"></a>Kan het document niet parseren
De Indexeer functie heeft het document uit de gegevens bron gelezen, maar er is een probleem opgetreden bij het converteren van de document inhoud naar het opgegeven veld toewijzings schema. Dit kan gebeuren vanwege:

| Reason | Voorbeeld | Action |
| --- | --- | --- |
| De document sleutel ontbreekt | De document sleutel mag niet ontbreken of zijn leeg | Zorg ervoor dat alle documenten geldige document sleutels hebben |
| De document sleutel is ongeldig | De document sleutel mag niet langer zijn dan 1024 tekens | Wijzig de document sleutel zodat deze voldoet aan de validatie vereisten. |
| Kan de veld toewijzing niet Toep assen op een veld | Kan de toewijzings functie `'functionName'` niet Toep assen op het veld `'fieldName'`. Matrix kan niet null zijn. Parameter naam: bytes | Controleer of de [veld Toewijzingen](search-indexer-field-mappings.md) die zijn gedefinieerd op de Indexeer functie dubbel zijn en vergelijk met de gegevens van het opgegeven veld van het mislukte document. Het kan nodig zijn om de veld toewijzingen of de document gegevens te wijzigen. |
| Kan de veld waarde niet lezen | Kan de waarde van kolom `'fieldName'` niet lezen bij index `'fieldIndex'`. Er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server. providers TCP-provider, fout: 0: een bestaande verbinding is geforceerd gesloten door de externe host.) | Deze fouten worden meestal veroorzaakt door onverwachte verbindings problemen met de onderliggende service van de gegevens bron. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>De vaardigheids invoer ' language code ' heeft de volgende taal codes X, Y, Z, ten minste één is ongeldig.
Een of meer van de waarden die zijn door gegeven aan de optionele `languageCode` invoer van een stroomafwaartse vaardigheid, wordt niet ondersteund. Dit kan gebeuren als u de uitvoer van de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) doorgeeft aan de volgende vaardig heden en de uitvoer bestaat uit meer talen dan wordt ondersteund in deze downstream-vaardig heden.

Als u weet dat uw gegevensset zich in één taal bevindt, moet u de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) en de kwalificatie-invoer van `languageCode` verwijderen en in plaats daarvan de para meter `defaultLanguageCode` gebruiken voor die vaardigheid, ervan uitgaande dat de taal voor die vaardigheid wordt ondersteund.

Als u weet dat uw gegevensset meerdere talen bevat en u de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) en `languageCode`-invoer nodig hebt, kunt u overwegen een [ConditionalSkill](cognitive-search-skill-conditional.md) toe te voegen om de tekst te filteren op talen die niet worden ondersteund voordat de tekst naar de downstream-vaardigheid.  Hier volgt een voor beeld van wat er in het EntityRecognitionSkill eruit kan zien:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Hier volgen enkele referenties voor de momenteel ondersteunde talen voor elk van de vaardig heden die dit fout bericht kunnen veroorzaken:
* [Text Analytics ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (voor de [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)en [SentimentSkill](cognitive-search-skill-sentiment.md))
* Door [Translator ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (voor de [tekst TranslationSkill](cognitive-search-skill-text-translation.md))
* [Tekst SplitSkill](cognitive-search-skill-textsplit.md) Ondersteunde talen: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>Vaardigheid is niet uitgevoerd binnen de tijds limiet
Er zijn twee gevallen waarin u dit fout bericht kunt tegen komen, die elk anders moeten worden behandeld. Volg de onderstaande instructies, afhankelijk van de vaardigheid die deze fout heeft geretourneerd.

#### <a name="built-in-cognitive-service-skills"></a>Ingebouwde cognitieve service vaardigheden
Veel van de ingebouwde cognitieve vaardig heden, zoals taal detectie, entiteits herkenning of OCR, worden ondersteund door een API-eind punt van de cognitieve service. Soms zijn er tijdelijke problemen met deze eind punten en treedt er een time-out op voor de aanvraag. Voor tijdelijke problemen is er geen oplossing, behalve dat u wilt wachten en probeer het opnieuw. Als oplossing kunt u overwegen om de Indexeer functie in te stellen [op een schema](search-howto-schedule-indexers.md). De geplande indexering wordt opgehaald wanneer deze is gestopt. Als tijdelijke problemen zijn opgelost, moeten indexerings-en cognitieve vaardigheids verwerking de volgende geplande uitvoering kunnen voortzetten.

#### <a name="custom-skills"></a>Aangepaste vaardigheden
Als er een time-outfout optreedt bij een aangepaste vaardigheid die u hebt gemaakt, kunt u het volgende proberen. Controleer eerst uw aangepaste vaardigheid en zorg ervoor dat deze niet vastloopt in een oneindige lus en dat het resultaat consistent wordt geretourneerd. Nadat u hebt bevestigd dat het het geval is, bepaalt u wat de uitvoerings tijd van uw vaardigheid is. Als u de `timeout`-waarde niet expliciet hebt ingesteld voor uw aangepaste vaardigheids definitie, is de standaard `timeout` 30 seconden. Als 30 seconden niet lang genoeg is om uw vaardigheid uit te voeren, kunt u een hogere waarde voor @no__t 0 opgeven voor uw aangepaste vaardigheids definitie. Hier volgt een voor beeld van een aangepaste vaardigheids definitie waarbij de time-out is ingesteld op 90 seconden:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

De maximum waarde die u voor de para meter `timeout` kunt instellen, is 230 seconden.  Als uw aangepaste vaardigheid niet consistent kan worden uitgevoerd binnen 230 seconden, kunt u overwegen om de `batchSize` van uw aangepaste vaardigheid te verminderen, zodat deze minder documenten heeft voor verwerking binnen één uitvoering.  Als u uw @no__t al hebt ingesteld op 1, moet u de vaardigheid opnieuw schrijven zodat deze binnen 230 seconden kan worden uitgevoerd of op andere wijze worden gesplitst in meerdere aangepaste vaardig heden, zodat de uitvoerings tijd voor een enkele aangepaste vaardigheid Maxi maal 230 seconden is. Raadpleeg de [documentatie voor aangepaste vaardig heden](cognitive-search-custom-skill-web-api.md) voor meer informatie.

##  <a name="warnings"></a>Waarschuwingen
Waarschuwingen worden niet gestopt met indexeren, maar ze doen wel voor waarden die kunnen leiden tot onverwachte resultaten. Of u actie onderneemt of niet afhankelijk is van de gegevens en uw scenario.

### <a name="skill-input-was-truncated"></a>Vaardigheids invoer is afgekapt
Cognitieve vaardig heden hebben limieten voor de lengte van de tekst die tegelijk kan worden geanalyseerd. Als de tekst invoer van deze vaardig heden deze limiet overschrijdt, wordt de tekst afgekapt om aan de limiet te voldoen en wordt de verrijking op die afgekapte tekst uitgevoerd. Dit betekent dat de vaardigheid wordt uitgevoerd, maar niet meer dan al uw gegevens.

In het onderstaande voor beeld LanguageDetectionSkill kan het invoer veld van `'text'` deze waarschuwing activeren als deze de teken limiet overschrijdt. U kunt de invoer limieten voor vaardig heden vinden in de [documentatie over vaardig heden](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Als u ervoor wilt zorgen dat alle tekst wordt geanalyseerd, kunt u overwegen om de [vaardigheid splitsen](cognitive-search-skill-textsplit.md)te gebruiken.