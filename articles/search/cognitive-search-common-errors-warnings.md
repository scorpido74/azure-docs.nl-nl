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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329381"
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