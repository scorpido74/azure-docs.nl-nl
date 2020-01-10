---
title: Fouten en waarschuwingen voor indexeerfunctie
titleSuffix: Azure Cognitive Search
description: In dit artikel vindt u informatie en oplossingen voor veelvoorkomende fouten en waarschuwingen die u kunt tegen komen tijdens AI-verrijking in azure Cognitive Search.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0738e56cf6760a356b6e2b6db76f2dc3f6f157ee
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763161"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Veelvoorkomende fouten en waarschuwingen voor Indexeer functies in azure Cognitive Search oplossen

In dit artikel vindt u informatie en oplossingen voor veelvoorkomende fouten en waarschuwingen die kunnen optreden tijdens het indexeren en AI-verrijking in azure Cognitive Search.

Het indexeren stopt wanneer het aantal fouten groter is dan [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Als u wilt dat Indexeer functies deze fouten negeren (en overs Laan over ' mislukte documenten '), kunt u overwegen om de `maxFailedItems` en `maxFailedItemsPerBatch` bij te werken, zoals [hier](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)wordt beschreven.

> [!NOTE]
> Elk mislukt document samen met de document sleutel (indien beschikbaar) wordt weer gegeven als een fout in de uitvoerings status van de Indexeer functie. U kunt de index- [API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) gebruiken om de documenten op een later tijdstip hand matig te uploaden als u de Indexeer functie hebt ingesteld op het verdragen van fouten.

De fout informatie in dit artikel kan u helpen bij het oplossen van fouten, waardoor het indexeren kan worden voortgezet.

Waarschuwingen worden niet gestopt met indexeren, maar ze doen wel voor waarden die kunnen leiden tot onverwachte resultaten. Of u actie onderneemt of niet afhankelijk is van de gegevens en uw scenario.

Met ingang van API-versie `2019-05-06`worden indexerings fouten en waarschuwingen op item niveau gestructureerd, zodat u meer duidelijkheid krijgt over oorzaken en de volgende stappen. Ze bevatten de volgende eigenschappen:

| Eigenschap | Beschrijving | Voorbeeld |
| --- | --- | --- |
| sleutel | De document-ID van het document dat wordt beïnvloed door de fout of waarschuwing. | https:\/-coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| naam | De naam van de bewerking waarin wordt beschreven waar de fout of waarschuwing zich voordeed. Dit wordt gegenereerd door de volgende structuur: [categorie]. [subcategorie]. [resource type]. ResourceName | DocumentExtraction. azureblob. myBlobContainerName-verrijking. WebApiSkill. mySkillName projectie. SearchIndex. OutputFieldMapping. myOutputFieldName-projectie. SearchIndex. MergeOrUpload. myIndexName Projectie. KnowledgeStore. table. myTableName |
| message | Een beschrijving van de fout of waarschuwing op hoog niveau. | Kan de vaardigheid niet uitvoeren omdat de Web-API-aanvraag is mislukt. |
| details informatie | Aanvullende informatie die nuttig kan zijn bij het vaststellen van het probleem, zoals het WebApi-antwoord als het uitvoeren van een aangepaste kwalificatie is mislukt. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 bron, func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`... rest van Stack tracering... |
| documentationLink | Een koppeling naar relevante documentatie met gedetailleerde informatie voor het opsporen van fouten en het oplossen van het probleem. Deze koppeling wijst vaak naar een van de onderstaande secties op deze pagina. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Fout: kan document niet lezen

De Indexeer functie kan het document niet lezen uit de gegevens bron. Dit kan gebeuren vanwege:

| Reden | Details/voor beeld | Oplossing |
| --- | --- | --- |
| inconsistente veld typen in verschillende documenten | Het type van de waarde komt niet overeen met het kolom Type. Kan `'{47.6,-122.1}'` in de kolom AUTHORS niet opslaan.  Verwacht type is JArray. | Zorg ervoor dat het type van elk veld hetzelfde is in verschillende documenten. Als bijvoorbeeld het eerste document `'startTime'` veld een datum/tijd is en in het tweede document een teken reeks is, wordt deze fout weer bereikt. |
| fouten van de onderliggende service van de gegevens bron | (van Cosmos DB) `{"Errors":["Request rate is large"]}` | Controleer uw opslag instantie om te controleren of deze in orde is. Mogelijk moet u de schaal/partitionering aanpassen. |
| tijdelijke problemen | Er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server. (provider: TCP-provider, fout: 0-een bestaande verbinding is geforceerd gesloten door de externe host | Af en toe zijn er onverwachte verbindings problemen. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Fout: kan geen inhoud of meta gegevens uit het document extra heren
Indexeer functie met een BLOB-gegevens bron kan de inhoud of meta gegevens niet ophalen uit het document (bijvoorbeeld een PDF-bestand). Dit kan gebeuren vanwege:

| Reden | Details/voor beeld | Oplossing |
| --- | --- | --- |
| de BLOB overschrijdt de maximale grootte | Document is `'150441598'` bytes, wat de maximale grootte `'134217728'` bytes voor de document extractie voor uw huidige servicelaag overschrijdt. | [BLOB-indexerings fouten](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| de BLOB heeft een niet-ondersteund inhouds type | Het document bevat een niet-ondersteund inhouds type `'image/png'` | [BLOB-indexerings fouten](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB is versleuteld | Het document kan niet worden verwerkt omdat het mogelijk is versleuteld of met een wacht woord is beveiligd. | U kunt de BLOB overs laan met [BLOB-instellingen](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| tijdelijke problemen | "Fout bij verwerken van blob: de aanvraag is afgebroken: de aanvraag is geannuleerd." Er is een time-out opgetreden tijdens het verwerken van het document. | Af en toe zijn er onverwachte verbindings problemen. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Fout: kan het document niet parseren
De Indexeer functie heeft het document uit de gegevens bron gelezen, maar er is een probleem opgetreden bij het converteren van de document inhoud naar het opgegeven veld toewijzings schema. Dit kan gebeuren vanwege:

| Reden | Details/voor beeld | Oplossing |
| --- | --- | --- |
| De document sleutel ontbreekt | De document sleutel mag niet ontbreken of zijn leeg | Zorg ervoor dat alle documenten geldige document sleutels hebben |
| De document sleutel is ongeldig | De document sleutel mag niet langer zijn dan 1024 tekens | Wijzig de document sleutel zodat deze voldoet aan de validatie vereisten. |
| Kan de veld toewijzing niet Toep assen op een veld | Kan toewijzings functie niet Toep assen `'functionName'` op veld `'fieldName'`. Matrix kan niet null zijn. Parameter naam: bytes | Controleer of de [veld Toewijzingen](search-indexer-field-mappings.md) die zijn gedefinieerd op de Indexeer functie dubbel zijn en vergelijk met de gegevens van het opgegeven veld van het mislukte document. Het kan nodig zijn om de veld toewijzingen of de document gegevens te wijzigen. |
| Kan de veld waarde niet lezen | Kan de waarde van kolom `'fieldName'` op index `'fieldIndex'`niet lezen. Er is een fout op transport niveau opgetreden bij het ontvangen van resultaten van de server. (provider: TCP-provider, fout: 0-een bestaande verbinding is geforceerd gesloten door de externe host.) | Deze fouten worden meestal veroorzaakt door onverwachte verbindings problemen met de onderliggende service van de gegevens bron. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Fout: kan de kwalificatie niet uitvoeren
De Indexeer functie kan geen vaardigheid uitvoeren in de vaardig heden.

| Reden | Details/voor beeld | Oplossing |
| --- | --- | --- |
| Problemen met de tijdelijke verbinding | Er is een tijdelijke fout opgetreden. Probeer het later opnieuw. | Af en toe zijn er onverwachte verbindings problemen. Probeer het document later opnieuw uit te voeren via uw Indexeer functie. |
| Mogelijke product bug | Er is een onverwachte fout opgetreden. | Dit duidt op een onbekende klasse fout. Dit kan betekenen dat er een product fout is opgetreden. Neem een [ondersteunings ticket](https://ms.portal.azure.com/#create/Microsoft.Support) op om hulp te krijgen. |
| Er is een fout opgetreden tijdens de uitvoering van een kwalificatie | (Van vaardigheid samen voegen) Een of meer offset waarden zijn ongeldig en kunnen niet worden geparseerd. Items zijn ingevoegd aan het einde van de tekst | Gebruik de informatie in het fout bericht om het probleem op te lossen. Voor dit soort storingen moet actie worden uitgevoerd. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Fout: kan de vaardigheid niet uitvoeren omdat de Web-API-aanvraag is mislukt
Het uitvoeren van de vaardigheid is mislukt omdat de aanroep van de Web-API is mislukt. Deze klasse van fouten treedt doorgaans op wanneer er aangepaste vaardig heden worden gebruikt. in dat geval moet u fouten in uw aangepaste code opsporen om het probleem op te lossen. Als de fout wordt veroorzaakt door een ingebouwde vaardigheid, raadpleegt u het fout bericht voor hulp bij het oplossen van het probleem.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Fout: kan de vaardigheid niet uitvoeren omdat het antwoord van de Web-API-vaardigheid ongeldig is
Het uitvoeren van de vaardigheid is mislukt omdat de aanroep van de Web-API een ongeldig antwoord heeft geretourneerd. Deze klasse van fouten treedt doorgaans op wanneer er aangepaste vaardig heden worden gebruikt. in dat geval moet u fouten in uw aangepaste code opsporen om het probleem op te lossen. Als de fout wordt veroorzaakt door een ingebouwde vaardigheid, kunt u een [ondersteunings ticket](https://ms.portal.azure.com/#create/Microsoft.Support) indienen om hulp te krijgen.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Fout: vaardigheid is niet uitgevoerd binnen de tijds limiet
Er zijn twee gevallen waarin u dit fout bericht kunt tegen komen, die elk anders moeten worden behandeld. Volg de onderstaande instructies, afhankelijk van de vaardigheid die deze fout heeft geretourneerd.

### <a name="built-in-cognitive-service-skills"></a>Ingebouwde cognitieve service vaardigheden
Veel van de ingebouwde cognitieve vaardig heden, zoals taal detectie, entiteits herkenning of OCR, worden ondersteund door een API-eind punt van de cognitieve service. Soms zijn er tijdelijke problemen met deze eind punten en treedt er een time-out op voor de aanvraag. Voor tijdelijke problemen is er geen oplossing, behalve dat u wilt wachten en probeer het opnieuw. Als oplossing kunt u overwegen om de Indexeer functie in te stellen [op een schema](search-howto-schedule-indexers.md). De geplande indexering wordt opgehaald wanneer deze is gestopt. Als tijdelijke problemen zijn opgelost, moeten indexerings-en cognitieve vaardigheids verwerking de volgende geplande uitvoering kunnen voortzetten.

Als deze fout blijft optreden in hetzelfde document voor een ingebouwde cognitieve vaardigheid, kunt u een [ondersteunings ticket](https://ms.portal.azure.com/#create/Microsoft.Support) indienen om hulp te krijgen, omdat dit niet wordt verwacht.

### <a name="custom-skills"></a>Aangepaste vaardigheden
Als er een time-outfout optreedt bij een aangepaste vaardigheid die u hebt gemaakt, kunt u het volgende proberen. Controleer eerst uw aangepaste vaardigheid en zorg ervoor dat deze niet vastloopt in een oneindige lus en dat het resultaat consistent wordt geretourneerd. Nadat u hebt bevestigd dat het het geval is, bepaalt u wat de uitvoerings tijd van uw vaardigheid is. Als u een `timeout` waarde niet expliciet hebt ingesteld voor uw aangepaste vaardigheids definitie, is de standaard `timeout` 30 seconden. Als 30 seconden niet lang genoeg is om uw vaardigheid uit te voeren, kunt u een hogere `timeout` waarde opgeven op uw aangepaste vaardigheids definitie. Hier volgt een voor beeld van een aangepaste vaardigheids definitie waarbij de time-out is ingesteld op 90 seconden:

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

De maximum waarde die u voor de para meter `timeout` kunt instellen, is 230 seconden.  Als uw aangepaste vaardigheid niet consistent kan worden uitgevoerd binnen 230 seconden, kunt u overwegen om de `batchSize` van uw aangepaste vaardigheid te verminderen, zodat deze minder documenten heeft voor verwerking binnen één uitvoering.  Als u uw `batchSize` al hebt ingesteld op 1, moet u de vaardigheid opnieuw schrijven zodat deze binnen 230 seconden kan worden uitgevoerd of op andere wijze worden gesplitst in meerdere aangepaste vaardig heden, zodat de uitvoerings tijd voor een enkele aangepaste vaardigheid Maxi maal 230 seconden is. Raadpleeg de [documentatie voor aangepaste vaardig heden](cognitive-search-custom-skill-web-api.md) voor meer informatie.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Fout: kan de`MergeOrUpload`| het document '`Delete`' naar de zoek index

Het document is gelezen en verwerkt, maar de Indexeer functie kan het niet toevoegen aan de zoek index. Dit kan gebeuren vanwege:

| Reden | Details/voor beeld | Oplossing |
| --- | --- | --- |
| Een veld bevat een term die te groot is | Een term in uw document is groter dan de [limiet van 32 KB](search-limits-quotas-capacity.md#api-request-limits) | U kunt deze beperking vermijden door ervoor te zorgen dat het veld niet is geconfigureerd als filterbaar, facetable of sorteerbaar.
| Het document is te groot om te worden geïndexeerd | Een document is groter dan de [maximale grootte](search-limits-quotas-capacity.md#api-request-limits) van de API-aanvraag | [Grote gegevens sets indexeren](search-howto-large-index.md)
| Het document bevat te veel objecten in de verzameling | Een verzameling in uw document overschrijdt het [maximum aantal elementen voor alle complexe verzamelingen limieten](search-limits-quotas-capacity.md#index-limits) . het document met Key `'1000052'` heeft `'4303'` objecten in verzamelingen (JSON-matrices). Er mogen Maxi maal `'3000'` objecten in verzamelingen in het hele document staan. Verwijder objecten uit verzamelingen en probeer het document opnieuw te indexeren. " | We raden u aan de grootte van de complexe verzameling in het document onder de limiet te verminderen en gebruik van hoge opslag te voor komen.
| Er is een probleem opgetreden bij het maken van verbinding met de doel index (die blijft bestaan na nieuwe pogingen), omdat de service wordt uitgevoerd onder andere belasting, zoals het uitvoeren van query's of het indexeren. | Kan geen verbinding maken met update-index. De zoek service wordt zwaar belast. | [De zoek service omhoog schalen](search-capacity-planning.md)
| De zoek service wordt bijgewerkt voor service-updates of bevindt zich in het midden van de herconfiguratie van een topologie. | Kan geen verbinding maken met update-index. De zoek service is momenteel niet beschikbaar en de zoek service gaat naar een overgang. | Service configureren met ten minste 3 replica's voor een Beschik baarheid van 99,9% per [Sla-documentatie](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Fout in de onderliggende Compute/Networking-Resource (zeldzaam) | Kan geen verbinding maken met update-index. Er is een onbekende fout opgetreden. | Indexeer functies zodanig configureren dat [ze worden uitgevoerd volgens een](search-howto-schedule-indexers.md) mislukte status.
| Een indexerings aanvraag voor de doel index is niet bevestigd binnen een time-outperiode vanwege netwerk problemen. | Kan geen verbinding maken met de zoek index op tijd. | Indexeer functies zodanig configureren dat [ze worden uitgevoerd volgens een](search-howto-schedule-indexers.md) mislukte status. Probeer ook de [Batch grootte](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) van de Indexeer functie te verlagen als deze fout zich blijft voordoen.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Fout: kan het document niet indexeren omdat de Indexeer functie gegevens voor de index ongeldig zijn.

Het document is gelezen en verwerkt, maar als gevolg van een niet-overeenkomende configuratie van de index velden en de aard van de gegevens die door de Indexeer functie zijn geëxtraheerd, kan deze niet worden toegevoegd aan de zoek index. Dit kan gebeuren vanwege:

| Reden | Details/voor beeld
| --- | ---
| Het gegevens type van het veld (en) dat door de Indexeer functie is geëxtraheerd, is incompatibel met het gegevens model van het bijbehorende doel index veld. | Het gegevens veld_Data_in het document met de sleutel 888 heeft een ongeldige waarde van het type EDM. String. Het verwachte type was ' verzameling (EDM. String) '. |
| Kan geen JSON-entiteit ophalen uit een teken reeks waarde. | Kan de waarde van het type EDM. String ' van het veld '_Data_' niet parseren als een JSON-object. Fout: ' na het parseren van een waarde is een onverwacht teken aangetroffen: ' '. Pad_naar_pad, regel 1, positie 3162. |
| Het uitpakken van een verzameling JSON-entiteiten uit een teken reeks waarde is mislukt.  | Kan de waarde van het type EDM. String ' van het veld '_Data_' niet parseren als een JSON-matrix. Fout: ' na het parseren van een waarde is een onverwacht teken aangetroffen: ' '. Pad [0], regel 1, positie 27. |
| Er is een onbekend type gedetecteerd in het bron document. | Onbekend type '_onbekend_' kan niet worden geïndexeerd |
| Er is een niet-compatibele notatie voor geografische punten gebruikt in het bron document. | Letterlijke teken reeksen van WKT-punten worden niet ondersteund. Gebruik in plaats daarvan geojson Point-tekens |

In al deze gevallen raadpleegt u [ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en [gegevens type toewijzing voor Indexeer functies](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) om er zeker van te zijn dat u het index schema correct bouwt en de juiste [toewijzings veld Toewijzingen](search-indexer-field-mappings.md)hebt ingesteld. Het fout bericht bevat details die kunnen helpen bij het volgen van de bron van de niet-overeenkomende bronnen.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Fout: kan het document niet verwerken binnen de Indexeer functie Max. uitvoerings tijd

Deze fout treedt op wanneer de verwerking van één document uit de gegevens bron binnen de toegestane uitvoerings tijd niet kan worden voltooid met de Indexeer functie. De [maximale uitvoerings tijd](search-limits-quotas-capacity.md#indexer-limits) is korter wanneer vaardig heden worden gebruikt. Als deze fout optreedt, als u maxFailedItems hebt ingesteld op een andere waarde dan 0, wordt het document in toekomstige uitvoeringen door de Indexeer functie omzeild, zodat de indexering kan worden uitgevoerd. Als u geen enkel document kunt overs Laan of als deze fout consequent wordt weer gegeven, kunt u overwegen documenten te verbreken in kleinere documenten zodat gedeeltelijke voortgang kan worden gemaakt binnen een uitvoering van één Indexeer functie.

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Waarschuwing: de vaardigheids invoer is ongeldig
Er ontbreekt een invoer voor de vaardigheid, het verkeerde type of is ongeldig. In het waarschuwings bericht wordt de impact aangegeven:
1) Kan de kwalificatie niet uitvoeren
2) Vaardigheid uitgevoerd, maar kan onverwachte resultaten hebben

Cognitieve vaardig heden hebben vereiste invoer en optionele invoer. De [belangrijkste woordgroepen kwalificatie](cognitive-search-skill-keyphrases.md) heeft bijvoorbeeld twee vereiste invoer `text`, `languageCode`en geen optionele invoer. Aangepaste vaardigheids invoer worden alle beschouwd als optionele invoer.

Als vereiste invoer ontbreekt of als een invoer niet het juiste type is, wordt de vaardigheid overgeslagen en wordt er een waarschuwing gegenereerd. Overgeslagen vaardig heden genereren geen uitvoer, dus als andere vaardig heden uitvoer van de overgeslagen vaardigheid gebruiken, kunnen ze extra waarschuwingen genereren.

Als een optionele invoer ontbreekt, wordt de vaardigheid nog steeds uitgevoerd, maar kan er onverwachte uitvoer ontstaan als gevolg van de ontbrekende invoer.

In beide gevallen kan deze waarschuwing worden verwacht als gevolg van de vorm van uw gegevens. Als u bijvoorbeeld een document hebt met informatie over personen met de velden `firstName`, `middleName`en `lastName`, hebt u mogelijk een aantal documenten waarvoor geen vermelding is voor `middleName`. Als u `middleName` als invoer wilt door geven aan een vaardigheid in de pijp lijn, wordt ervan uitgegaan dat deze vaardigheids invoer een deel van de tijd kan missen. U moet uw gegevens en scenario evalueren om te bepalen of er een actie moet worden uitgevoerd als gevolg van deze waarschuwing.

Als u een standaard waarde wilt opgeven in het geval van een ontbrekende invoer, kunt u de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md) gebruiken om een standaard waarde te genereren en vervolgens de uitvoer van de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md) gebruiken als de vaardigheids invoer.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Reden | Details/voor beeld | Oplossing |
| --- | --- | --- |
| De vaardigheids invoer is van het verkeerde type | De vereiste vaardigheids invoer is niet van het verwachte type `String`. Naam: `text`, Bron: `/document/merged_content`. "  ' De vereiste vaardigheids invoer heeft niet de verwachte indeling. Naam: `text`, Bron: `/document/merged_content`. "  "Kan niet nalopen over niet-matrix `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`".  "Kan geen `0` selecteren in niet-matrix `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`" | Bepaalde vaardig heden verwachten invoer van bepaalde typen, bijvoorbeeld [sentiment-vaardigheid](cognitive-search-skill-sentiment.md) verwacht `text` een teken reeks te zijn. Als met de invoer een niet-teken reeks waarde wordt opgegeven, wordt de kwalificatie niet uitgevoerd en worden er geen uitvoer gegenereerd. Zorg ervoor dat uw gegevensset uniforme invoer waarden bevat, of gebruik een [aangepaste web API-vaardigheid](cognitive-search-custom-skill-web-api.md) om de invoer voor te verwerken. Als u de vaardigheid over een matrix wilt door lopen, controleert u of de context van de vaardigheid en de invoer `*` op de juiste posities hebben. Normaal gesp roken moet zowel de context als de invoer bron eindigen op `*` voor matrices. |
| Vaardigheids invoer ontbreekt | De vereiste vaardigheids invoer ontbreekt. Naam: `text`, Bron: `/document/merged_content`ontbreekt `/document/normalized_images/0/imageTags`.  ' Kan geen `0` selecteren in matrix `/document/pages` met een lengte van `0`. | Als in al uw documenten deze waarschuwing wordt weer gegeven, is er waarschijnlijk een type fout in de invoer paden en moet u de naam van de eigenschap name, extra of ontbrekende `*` in het pad controleren en ervoor zorgen dat de documenten uit de gegevens bron de vereiste invoer hebben. |
| De invoer voor de taal code van de bekwaamheid is ongeldig | `languageCode` voor het invoeren van vaardig heden heeft de volgende taal codes `X,Y,Z`, ten minste één is ongeldig. | Meer details vindt u [hieronder](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Waarschuwing: de vaardigheids invoer language code heeft de volgende taal codes X, Y, Z, ten minste één is ongeldig.
Een of meer van de waarden die zijn door gegeven aan de optionele `languageCode` invoer van een stroomafwaartse vaardigheid, wordt niet ondersteund. Dit kan gebeuren als u de uitvoer van de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) doorgeeft aan de volgende vaardig heden en de uitvoer bestaat uit meer talen dan wordt ondersteund in deze downstream-vaardig heden.

Als u weet dat uw gegevensset zich in één taal bevindt, moet u de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) en de `languageCode` vaardigheids invoer verwijderen en in plaats daarvan de para meter `defaultLanguageCode` vaardig heden voor die vaardigheid gebruiken, ervan uitgaande dat de taal voor die vaardigheid wordt ondersteund.

Als u weet dat uw gegevensset meerdere talen bevat en u de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) en `languageCode` invoer nodig hebt, kunt u overwegen om een [ConditionalSkill](cognitive-search-skill-conditional.md) toe te voegen om de tekst te filteren op basis van talen die niet worden ondersteund voordat de tekst wordt door gegeven aan de downstream-vaardigheid.  Hier volgt een voor beeld van wat er in het EntityRecognitionSkill eruit kan zien:

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

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Waarschuwing: vaardigheids invoer is afgekapt
Cognitieve vaardig heden hebben limieten voor de lengte van de tekst die tegelijk kan worden geanalyseerd. Als de tekst invoer van deze vaardig heden deze limiet overschrijdt, wordt de tekst afgekapt om aan de limiet te voldoen en wordt de verrijking op die afgekapte tekst uitgevoerd. Dit betekent dat de vaardigheid wordt uitgevoerd, maar niet meer dan al uw gegevens.

In het onderstaande voor beeld LanguageDetectionSkill kan het invoer veld van `'text'` deze waarschuwing activeren als het boven de teken limiet ligt. U kunt de invoer limieten voor vaardig heden vinden in de [documentatie over vaardig heden](cognitive-search-predefined-skills.md).

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

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Waarschuwing: het vaardigheids antwoord Web API bevat waarschuwingen
Indexeer functie kan een vaardigheid uitvoeren in de vaardig heden, maar het antwoord van de Web API-aanvraag heeft aangegeven dat er waarschuwingen zijn tijdens de uitvoering. Bekijk de waarschuwingen om te begrijpen hoe uw gegevens worden beïnvloed en of actie moet worden ondernomen.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Waarschuwing: de huidige configuratie van de Indexeer functie biedt geen ondersteuning voor incrementele voortgang

Deze waarschuwing treedt alleen op voor Cosmos DB gegevens bronnen.

Incrementele voortgang tijdens het indexeren zorgt ervoor dat als de uitvoering van de Indexeer functie wordt onderbroken door tijdelijke fouten of limiet voor uitvoerings tijd, de Indexeer functie kan ophalen waar de volgende keer wordt uitgevoerd, in plaats van de volledige verzameling helemaal opnieuw te indexeren. Dit is vooral belang rijk bij het indexeren van grote verzamelingen.

De mogelijkheid om een onvoltooide indexerings taak te hervatten, wordt gepredikd met documenten die zijn geordend op basis van de `_ts` kolom. De Indexeer functie maakt gebruik van de tijds tempel om te bepalen welk document er volgende moet worden opgehaald. Als de `_ts` kolom ontbreekt of als de Indexeer functie niet kan bepalen of er een aangepaste query wordt gesorteerd, wordt de Indexeer functie gestart om te starten. deze waarschuwing wordt weer gegeven.

Het is mogelijk om dit gedrag te negeren, waardoor er incrementele voortgang wordt gemaakt en deze waarschuwing wordt onderdrukt met behulp van de configuratie-eigenschap `assumeOrderByHighWatermarkColumn`.

Zie [incrementele voortgang en aangepaste query's](search-howto-index-cosmosdb.md#IncrementalProgress)voor meer informatie.

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Waarschuwing: sommige gegevens zijn verloren gegaan tijdens de projectie. De rij ' X ' in de tabel ' Y ' heeft een teken reeks eigenschap ' Z ' die te lang is.

De [Table Storage-service](https://azure.microsoft.com/services/storage/tables) heeft een limiet voor de manier waarop de eigenschappen van de grote [entiteit](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) kunnen zijn. Teken reeksen mogen Maxi maal 32.000 tekens bevatten. Als een rij met een teken reeks-eigenschap die langer is dan 32.000 tekens wordt geprojecteerd, worden alleen de eerste 32.000 tekens bewaard. U kunt dit probleem omzeilen door rijen te projecteren met teken reeks eigenschappen die langer zijn dan 32.000 tekens.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Waarschuwing: geëxtraheerde tekst wordt afgekapt tot X tekens
Indexeer functies beperken hoeveel tekst uit één document kan worden opgehaald. Deze limiet is afhankelijk van de prijs categorie: 32.000 tekens voor de gratis laag, 64.000 voor Basic en 4.000.000 voor Standard, standaard S2 en standaard S3-lagen. De tekst die is afgekapt, wordt niet geïndexeerd. U kunt deze waarschuwing vermijden door documenten met grote hoeveel heden tekst te splitsen in meerdere, kleinere documenten. 

Zie voor meer informatie [indexerings limieten](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Waarschuwing: kan het uitvoer veld ' X ' niet toewijzen aan de zoek index
Uitvoer veld toewijzingen die naar niet-bestaande/null-gegevens verwijzen, genereren waarschuwingen voor elk document en resulteren in een leeg index veld. Om dit probleem op te lossen, controleert u de bron paden van de uitvoer veld toewijzing voor mogelijke typfouten of stelt u een standaard waarde in met behulp van de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Waarschuwing: het detectie beleid voor gegevens wijzigingen is geconfigureerd voor het gebruik van sleutel kolom X
Voor het [beleid voor gegevens wijzigings detectie](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) gelden specifieke vereisten voor de kolommen die worden gebruikt om de wijziging te detecteren. Een van deze vereisten is dat deze kolom wordt bijgewerkt telkens wanneer het bron item wordt gewijzigd. Een andere vereiste is dat de nieuwe waarde voor deze kolom groter is dan de vorige waarde. Sleutel kolommen voldoen niet aan deze vereiste omdat ze niet worden gewijzigd bij elke update. U kunt dit probleem omzeilen door een andere kolom te selecteren voor het beleid voor wijzigings detectie.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Waarschuwing: de document tekst lijkt UTF-16-code ring te zijn, maar er ontbreekt een byte order mark

De [indexerings modi van de Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) moeten weten hoe tekst moet worden gecodeerd voordat deze wordt geparseerd. De twee meest voorkomende manieren voor het coderen van tekst zijn UTF-16 en UTF-8. UTF-8 is een code ring met variabele lengte waarbij elk teken tussen 1 en 4 bytes lang is. UTF-16 is een code ring met een vaste lengte waarbij elk teken 2 bytes lang is. UTF-16 heeft twee verschillende varianten: ' big endian ' en ' little endian '. Tekst codering wordt bepaald door een ' byte order mark ', een reeks bytes voor de tekst.

| Encoding | Byte order markering |
| --- | --- |
| UTF-16 big endian | 0xFF 0xFE |
| UTF-16 little endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Als er geen byte order mark aanwezig is, wordt ervan uitgegaan dat de tekst als UTF-8 wordt gecodeerd.

Om deze waarschuwing te omzeilen, bepaalt u wat de tekst codering voor deze blob is en voegt u de juiste byte order mark toe.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Waarschuwing: Cosmos DB verzameling ' X ' heeft een Lazy-indexerings beleid. Er zijn mogelijk gegevens verloren gegaan

Verzamelingen met een [Lazy](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) -indexerings beleid kunnen niet consistent worden opgevraagd, wat resulteert in uw Indexeer functie waarbij gegevens ontbreken. Als u deze waarschuwing wilt omzeilen, wijzigt u het indexerings beleid in consistent.
