---
title: Fouten en waarschuwingen voor indexeerfunctie
titleSuffix: Azure Cognitive Search
description: In dit artikel vindt u informatie en oplossingen voor veelvoorkomende fouten en waarschuwingen die u tijdens AI-verrijking in Azure Cognitive Search tegenkomen.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72bf08dce36d857c1fe91bbe9806336dfa185f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671981"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Veelvoorkomende indexfouten en waarschuwingen oplossen in Azure Cognitive Search

In dit artikel vindt u informatie en oplossingen voor veelvoorkomende fouten en waarschuwingen die u tegenkomen tijdens indexering en AI-verrijking in Azure Cognitive Search.

Indexering stopt wanneer het aantal fouten hoger is dan ['maxFailedItems'.](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) 

Als u wilt dat indexers deze fouten negeren (en `maxFailedItems` 'mislukte documenten' overslaan), u overwegen de en `maxFailedItemsPerBatch` zoals [hier](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)beschreven documenten bij te werken.

> [!NOTE]
> Elk mislukt document samen met de documentsleutel (indien beschikbaar) wordt weergegeven als een fout in de uitvoeringsstatus van de indexer. U de [index-api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) gebruiken om de documenten op een later tijdstip handmatig te uploaden als u de indexer hebt ingesteld om fouten te tolereren.

De foutgegevens in dit artikel kunnen u helpen bij het oplossen van fouten, waardoor indexering kan worden voortgezet.

Waarschuwingen stoppen niet met indexeren, maar geven wel voorwaarden aan die tot onverwachte resultaten kunnen leiden. Of u actie onderneemt of niet, hangt af van de gegevens en uw scenario.

Beginnend met `2019-05-06`API-versie, artikel-niveau Indexer fouten en waarschuwingen zijn gestructureerd om meer duidelijkheid rond oorzaken en volgende stappen te bieden. Ze bevatten de volgende eigenschappen:

| Eigenschap | Beschrijving | Voorbeeld |
| --- | --- | --- |
| sleutel | De document-id van het document die wordt beïnvloed door de fout of waarschuwing. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | De bewerkingsnaam die beschrijft waar de fout of waarschuwing is opgetreden. Dit wordt gegenereerd door de volgende structuur: [categorie]. [subcategorie]. [resourceType]. [resourceName], je hebt een artikel kunnen schrijven! | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Een beschrijving op hoog niveau van de fout of waarschuwing. | Kon geen vaardigheid uitvoeren omdat de Web Api-aanvraag is mislukt. |
| Details | Aanvullende details die nuttig kunnen zijn om het probleem te diagnosticeren, zoals de WebApi-reactie als het uitvoeren van een aangepaste vaardigheid is mislukt. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 bron,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... rest van stack trace... |
| documentatieLink | Een link naar relevante documentatie met gedetailleerde informatie om het probleem te debuggen en op te lossen. Deze link zal vaak verwijzen naar een van de onderstaande secties op deze pagina. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Fout: kan document niet lezen

Indexer kan het document niet lezen uit de gegevensbron. Dit kan gebeuren als gevolg van:

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| Inconsistente veldtypen voor verschillende documenten | "Type waarde heeft een mismatch met kolomtype. Kon niet `'{47.6,-122.1}'` opslaan in de kolom auteurs.  Verwacht type is JArray."  "Fout omzetten van gegevens type nvarchar te zweven."  "Conversie is mislukt bij het converteren van de nvarchar-waarde '12 maanden' naar het gegevenstype int."  "Rekenkundige overloopfout die expressie omzet naar gegevenstype int." | Controleer of het type van elk veld hetzelfde is voor verschillende documenten. Als het eerste documentveld `'startTime'` bijvoorbeeld een DateTime is en in het tweede document een tekenreeks is, wordt deze fout geraakt. |
| fouten van de onderliggende service van de gegevensbron | (van Cosmos DB)`{"Errors":["Request rate is large"]}` | Controleer uw opslaginstantie om er zeker van te zijn dat deze in orde is. Mogelijk moet u uw schaling/partitionering aanpassen. |
| tijdelijke problemen | Er is een fout op transportniveau opgetreden bij het ontvangen van resultaten van de server. (provider: TCP Provider, fout: 0 - Een bestaande verbinding werd met geweld gesloten door de externe host | Af en toe zijn er onverwachte verbindingsproblemen. Probeer het document later opnieuw door uw indexer te laten lopen. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Fout: kan geen inhoud of metagegevens uit uw document extraheren
Indexeren met een Blob-gegevensbron kunnen de inhoud of metagegevens niet uit het document extraheren (bijvoorbeeld een PDF-bestand). Dit kan gebeuren als gevolg van:

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| blob is boven de groottelimiet | Document `'150441598'` is bytes, die `'134217728'` de maximale grootte bytes voor documentextractie voor uw huidige servicelaag overschrijdt. | [blobindexeringsfouten](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob heeft niet-ondersteund inhoudstype | Document heeft niet-ondersteund inhoudstype`'image/png'` | [blobindexeringsfouten](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob is versleuteld | Document kan niet worden verwerkt - het kan worden versleuteld of met een wachtwoord beveiligd. | U de blob overslaan met [blob-instellingen](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| tijdelijke problemen | 'Blob foutverwerking: de aanvraag is afgebroken: de aanvraag is geannuleerd'. "Document time-out tijdens de verwerking." | Af en toe zijn er onverwachte verbindingsproblemen. Probeer het document later opnieuw door uw indexer te laten lopen. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Fout: kan document niet ontsmetten
Indexer las het document uit de gegevensbron, maar er was een probleem waarbij de documentinhoud werd omgezet in het opgegeven veldtoewijzingsschema. Dit kan gebeuren als gevolg van:

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| De documentsleutel ontbreekt | Documentsleutel mag niet ontbreken of leeg zijn | Controleren of alle documenten geldige documentsleutels hebben |
| De documentsleutel is ongeldig | Documentsleutel mag niet langer zijn dan 1024 tekens | Wijzig de documentsleutel om aan de validatievereisten te voldoen. |
| Kan veldtoewijzing niet toepassen op een veld | Kan de `'functionName'` toewijzingsfunctie `'fieldName'`niet toepassen op het veld . Array kan niet null zijn. Parameternaam: bytes | Controleer de [veldtoewijzingen](search-indexer-field-mappings.md) die op de indexer zijn gedefinieerd en vergelijk met de gegevens van het opgegeven veld van het mislukte document. Het kan nodig zijn om de veldtoewijzingen of de documentgegevens te wijzigen. |
| Kon de veldwaarde niet lezen | Kon de waarde van `'fieldName'` de `'fieldIndex'`kolom bij index niet lezen . Er is een fout op transportniveau opgetreden bij het ontvangen van resultaten van de server. (provider: TCP Provider, fout: 0 - Een bestaande verbinding is met geweld gesloten door de externe host.) | Deze fouten zijn meestal te wijten aan onverwachte verbindingsproblemen met de onderliggende service van de gegevensbron. Probeer het document later opnieuw door uw indexer te laten lopen. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Fout: Kan vaardigheid niet uitvoeren
Indexer was niet in staat om een vaardigheid in de skillset uit te voeren.

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| Problemen met tijdelijke connectiviteit | Er is een tijdelijke fout opgetreden. Probeer het later opnieuw. | Af en toe zijn er onverwachte verbindingsproblemen. Probeer het document later opnieuw door uw indexer te laten lopen. |
| Potentiële productbug | Er is een onverwachte fout opgetreden. | Dit duidt op een onbekende klasse van falen en kan betekenen dat er een productbug is. Dien een [ondersteuningsticket in](https://ms.portal.azure.com/#create/Microsoft.Support) om hulp te krijgen. |
| Een vaardigheid heeft een fout ondervonden tijdens de uitvoering | (Van Samenvoegen Skill) Een of meer offsetwaarden waren ongeldig en konden niet worden ontleed. Items zijn ingevoegd aan het einde van de tekst | Gebruik de informatie in het foutbericht om het probleem op te lossen. Dit soort fouten vereist actie op te lossen. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Fout: kan geen vaardigheid uitvoeren omdat de web-API-aanvraag is mislukt
De uitvoering van de vaardigheid is mislukt omdat de aanroep naar de web-API is mislukt. Deze foutklasse treedt meestal op wanneer aangepaste vaardigheden worden gebruikt, in welk geval u uw aangepaste code moet debuggen om het probleem op te lossen. Als de fout in plaats daarvan afkomstig is van een ingebouwde vaardigheid, raadpleegt u het foutbericht voor hulp bij het oplossen van het probleem.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Fout: kan vaardigheid niet uitvoeren omdat de reactie van de Web API-vaardigheid ongeldig is
De uitvoering van de vaardigheid is mislukt omdat de aanroep naar de web-API een ongeldig antwoord heeft geretourneerd. Deze foutklasse treedt meestal op wanneer aangepaste vaardigheden worden gebruikt, in welk geval u uw aangepaste code moet debuggen om het probleem op te lossen. Als de fout in plaats daarvan afkomstig is van een ingebouwde vaardigheid, dient u een [ondersteuningsticket](https://ms.portal.azure.com/#create/Microsoft.Support) in om hulp te krijgen.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Fout: Vaardigheid is niet binnen de tijdslimiet uitgevoerd
Er zijn twee gevallen waarin u deze foutmelding tegenkomen, die elk anders moeten worden behandeld. Volg de onderstaande instructies, afhankelijk van welke vaardigheid deze fout voor u heeft geretourneerd.

### <a name="built-in-cognitive-service-skills"></a>Ingebouwde cognitieve servicevaardigheden
Veel van de ingebouwde cognitieve vaardigheden, zoals taaldetectie, entiteitsherkenning of OCR, worden ondersteund door een eindpunt van de Cognitive Service API. Soms zijn er tijdelijke problemen met deze eindpunten en een aanvraag zal time-out. Voor tijdelijke problemen is er geen remedie, behalve om te wachten en het opnieuw te proberen. Als beperking u overwegen uw indexer in te [stellen om volgens een planning te worden uitgevoerd.](search-howto-schedule-indexers.md) Geplande indexering gaat verder waar het gebleven was. Ervan uitgaande dat tijdelijke problemen zijn opgelost, moeten indexering en cognitieve vaardigheidsverwerking in staat zijn om door te gaan op de volgende geplande uitvoering.

Als u deze fout blijft zien op hetzelfde document voor een ingebouwde cognitieve vaardigheid, dient u een [ondersteuningsticket](https://ms.portal.azure.com/#create/Microsoft.Support) in om hulp te krijgen, omdat dit niet wordt verwacht.

### <a name="custom-skills"></a>Aangepaste vaardigheden
Als u een time-outfout tegenkomt met een aangepaste vaardigheid die u hebt gemaakt, zijn er een paar dingen die u proberen. Ten eerste, controleer uw aangepaste vaardigheid en ervoor te zorgen dat het niet vast komt te zitten in een oneindige lus en dat het een resultaat consequent terug. Zodra u hebt bevestigd dat het geval is, bepalen wat de uitvoeringstijd van uw vaardigheid is. Als u niet expliciet een `timeout` waarde hebt ingesteld op uw `timeout` aangepaste vaardigheidsdefinitie, is de standaardinstelling 30 seconden. Als 30 seconden niet lang genoeg is om uw `timeout` vaardigheid uit te voeren, u een hogere waarde opgeven op uw aangepaste vaardigheidsdefinitie. Hier is een voorbeeld van een aangepaste vaardigheidsdefinitie waarbij de time-out is ingesteld op 90 seconden:

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

De maximale waarde die `timeout` u voor de parameter instellen, is 230 seconden.  Als uw aangepaste vaardigheid niet in staat is om consistent uit `batchSize` te voeren binnen 230 seconden, u overwegen het verminderen van de van uw aangepaste vaardigheid, zodat het minder documenten te verwerken binnen een enkele uitvoering.  Als u uw `batchSize` reeds op 1 hebt geplaatst, zult u de vaardigheid moeten herschrijven om in onder 230 seconden kunnen uitvoeren of anders het in veelvoudige douanevaardigheden kunnen verdelen zodat de uitvoeringstijd voor om het even welke enige douanevaardigheid een maximum van 230 seconden is. Bekijk de [aangepaste vaardigheidsdocumentatie](cognitive-search-custom-skill-web-api.md) voor meer informatie.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Fout: Kan`MergeOrUpload`niet ' ' | '`Delete`document naar de zoekindex

Het document is gelezen en verwerkt, maar de indexeerder kon het niet toevoegen aan de zoekindex. Dit kan gebeuren als gevolg van:

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| Een veld bevat een term die te groot is | Een term in uw document is groter dan de [limiet van 32 KB](search-limits-quotas-capacity.md#api-request-limits) | U deze beperking vermijden door ervoor te zorgen dat het veld niet is geconfigureerd als filterbaar, facetable of sorteerbaar.
| Document is te groot om te worden geïndexeerd | Een document is groter dan de [maximale api-aanvraaggrootte](search-limits-quotas-capacity.md#api-request-limits) | [Grote gegevenssets indexeren](search-howto-large-index.md)
| Document bevat te veel objecten in verzameling | Een verzameling in uw document overschrijdt de [maximale elementen in alle complexe verzamelingen limiet](search-limits-quotas-capacity.md#index-limits) "Het document met sleutel `'1000052'` heeft `'4303'` objecten in verzamelingen (JSON arrays). Bij `'3000'` de meeste objecten zijn toegestaan om in collecties over het hele document. Verwijder objecten uit verzamelingen en probeer het document opnieuw te indexeren." | We raden u aan de grootte van de complexe verzameling in het document te verlagen tot onder de limiet en een hoog opslaggebruik te vermijden.
| Problemen met het maken van verbinding met de doelindex (die blijft bestaan na nieuwe pogingen) omdat de service onder andere belasting staat, zoals query's of indexering. | Kan geen verbinding tot stand brengen om de index bij te werken. De zoekservice staat onder zware belasting. | [Uw zoekservice opschalen](search-capacity-planning.md)
| Zoekservice wordt gepatcht voor service-update of bevindt zich in het midden van een topologie-herconfiguratie. | Kan geen verbinding tot stand brengen om de index bij te werken. Zoekservice is momenteel down/ Search service ondergaat een overgang. | Service configureren met ten minste 3 replica's voor 99,9% beschikbaarheid per [SLA-documentatie](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Fout in de onderliggende compute/networking resource (zeldzaam) | Kan geen verbinding tot stand brengen om de index bij te werken. Er is een onbekende fout opgetreden. | Indexers configureren om [volgens een schema](search-howto-schedule-indexers.md) uit te voeren om op te halen van een mislukte status.
| Een indexeringsverzoek aan de doelindex werd niet binnen een time-outperiode erkend vanwege netwerkproblemen. | Kon niet tijdig verbinding maken met de zoekindex. | Indexers configureren om [volgens een schema](search-howto-schedule-indexers.md) uit te voeren om op te halen van een mislukte status. Probeer bovendien de grootte van de [indexerbatch](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) te verlagen als deze foutvoorwaarde aanhoudt.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Fout: kan document niet indexeren omdat sommige gegevens van het document niet geldig waren

Het document is gelezen en verwerkt door de indexeerder, maar vanwege een mismatch in de configuratie van de indexvelden en de gegevens die door de indexeerder zijn geëxtraheerd en verwerkt, kan het niet aan de zoekindex worden toegevoegd. Dit kan gebeuren als gevolg van:

| Reden | Details/Voorbeeld
| --- | ---
| Het gegevenstype van het veld(en) dat door de indexeerder wordt geëxtraheerd, is niet compatibel met het gegevensmodel van het overeenkomstige doelindexveld. | Het gegevensveld '_gegevens_' in het document met de sleutel '888' heeft een ongeldige waarde 'van het type 'Edm.String''. Het verwachte type was 'Collection(Edm.String)'. |
| Kan geen JSON-entiteit uit een tekenreekswaarde extraheren. | Kan de waarde 'van het type 'Edm.String'' van_veldgegevens_niet ontleden als een JSON-object. Fout:'Na het ontleed van een waarde is een onverwacht teken aangetroffen: ''. Pad '_pad_', lijn 1, positie 3162.' |
| Kan een verzameling JSON-entiteiten niet extraheren uit een tekenreekswaarde.  | Kan de waarde 'van het type 'Edm.String'' van_veldgegevens_niet ontleden als een JSON-array. Fout:'Na het ontleed van een waarde is een onverwacht teken aangetroffen: ''. Pad '[0]', lijn 1, positie 27.' |
| Een onbekend type werd ontdekt in het brondocument. | Onbekend type '_onbekend_' kan niet worden geïndexeerd |
| In het brondocument is een onverenigbare notatie voor geografische punten gebruikt. | WKT POINT string liters worden niet ondersteund. Gebruik in plaats daarvan GeoJson-puntliteralals |

In al deze gevallen verwijzen we je naar [Ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en [Gegevenstypekaart voor indexers](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) om ervoor te zorgen dat je het indexschema correct hebt opgebouwd en de juiste [indexerveldtoewijzingen](search-indexer-field-mappings.md)hebt ingesteld. De foutmelding bevat details die kunnen helpen bij het opsporen van de bron van de mismatch.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Fout: Geïntegreerd beleid voor het bijhouden van wijzigingen kan niet worden gebruikt omdat de tabel een samengestelde primaire sleutel heeft

Dit geldt voor SQL-tabellen en gebeurt meestal wanneer de sleutel wordt gedefinieerd als een samengestelde sleutel of wanneer de tabel een unieke geclusterde index heeft gedefinieerd (zoals in een SQL-index, niet als een Azure Search-index). De belangrijkste reden is dat het sleutelkenmerk is gewijzigd om een samengestelde primaire sleutel te zijn in het geval van een [unieke geclusterde index](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). Zorg er in dat geval voor dat uw SQL-tabel geen unieke geclusterde index heeft of dat u het sleutelveld in kaart brengt aan een veld dat gegarandeerd geen dubbele waarden heeft.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Fout: kan document niet verwerken binnen de maximale looptijd van indexeren

Deze fout treedt op wanneer de indexer de verwerking van één document uit de gegevensbron niet kan voltooien binnen de toegestane uitvoeringstijd. [De maximale looptijd](search-limits-quotas-capacity.md#indexer-limits) is korter wanneer skillsets worden gebruikt. Wanneer deze fout optreedt, als maxFailedItems zijn ingesteld op een andere waarde dan 0, omzeilt de indexer het document op toekomstige uitvoeringen, zodat indexering kan worden uitgevoerd. Als u het zich niet veroorloven om een document over te slaan of als u deze fout consistent ziet, u overwegen documenten in kleinere documenten op te splitsen, zodat gedeeltelijke vooruitgang kan worden geboekt binnen één indexeruitvoering.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Fout: kan geen projectdocument

Deze fout treedt op wanneer de indexer gegevens probeert te [projecteren in een kennisarchief](knowledge-store-projection-overview.md) en er een fout is gemaakt in onze poging om dit te doen.  Deze fout kan consistent en fixable zijn of het kan een tijdelijke fout met de projectie output sink die u nodig hebt om te wachten en opnieuw te proberen om op te lossen.  Hier zijn een reeks bekende fouttoestanden en mogelijke resoluties.

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| Kan de projectieblob in de `'blobUri'` container niet bijwerken`'containerName'` |De opgegeven container bestaat niet. | De indexer controleert of de opgegeven container eerder is gemaakt en maakt deze indien nodig, maar voert deze controle slechts één keer uit per indexerrun. Deze fout betekent dat iets de container na deze stap heeft verwijderd.  Als u deze fout wilt oplossen, probeert u dit: laat uw opslagaccountgegevens met rust, wacht tot de indexer is voltooid en voer de indexer opnieuw uit. |
| Kan de projectieblob in de `'blobUri'` container niet bijwerken`'containerName'` |Kan geen gegevens naar de transportverbinding schrijven: een bestaande verbinding is met geweld gesloten door de externe host. | Dit is naar verwachting een tijdelijke fout met Azure Storage en moet dus worden opgelost door de indexer opnieuw uit te voeren. Als u deze fout consistent tegenkomt, dient u een [ondersteuningsticket](https://ms.portal.azure.com/#create/Microsoft.Support) in, zodat het verder kan worden onderzocht.  |
| Kan rij `'projectionRow'` in tabel niet bijwerken`'tableName'` | De server is bezet. | Dit is naar verwachting een tijdelijke fout met Azure Storage en moet dus worden opgelost door de indexer opnieuw uit te voeren. Als u deze fout consistent tegenkomt, dient u een [ondersteuningsticket](https://ms.portal.azure.com/#create/Microsoft.Support) in, zodat het verder kan worden onderzocht.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Waarschuwing: de invoer van vaardigheden was ongeldig
Een invoer in de vaardigheid ontbrak, het verkeerde type of anderszins ongeldig. Het waarschuwingsbericht geeft de impact aan:
1) Kon geen vaardigheid uitvoeren
2) Vaardigheid uitgevoerd, maar kan onverwachte resultaten hebben

Cognitieve vaardigheden hebben input en optionele ingangen vereist. Bijvoorbeeld de [sleutel zin extractie](cognitive-search-skill-keyphrases.md) vaardigheid `text` `languageCode`heeft twee vereiste ingangen, en geen optionele ingangen. Aangepaste vaardigheidsingangen worden allemaal beschouwd als optionele ingangen.

Als er vereiste ingangen ontbreken of als een invoer niet het juiste type is, wordt de vaardigheid overgeslagen en genereert een waarschuwing. Overgeslagen vaardigheden genereren geen uitvoer, dus als andere vaardigheden uitvoer van de overgeslagen vaardigheid gebruiken, kunnen ze extra waarschuwingen genereren.

Als een optionele invoer ontbreekt, wordt de vaardigheid nog steeds uitgevoerd, maar kan deze onverwachte uitvoer produceren vanwege de ontbrekende invoer.

In beide gevallen kan deze waarschuwing worden verwacht vanwege de vorm van uw gegevens. Als u bijvoorbeeld een document hebt met informatie `firstName`over `middleName`personen `lastName`met de velden , en u `middleName`kunt een aantal documenten hebben waarvoor geen vermelding is voor . Als u `middleName` als input aan een vaardigheid in de pijpleiding moet overgaan, dan is het verwacht dat deze vaardigheidsinput een deel van de tijd kan missen. U moet uw gegevens en scenario evalueren om te bepalen of er actie nodig is als gevolg van deze waarschuwing.

Als u een standaardwaarde wilt opgeven in het geval van ontbrekende invoer, u de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md) gebruiken om een standaardwaarde te genereren en vervolgens de uitvoer van de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md) als vaardigheidsinvoer gebruiken.


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

| Reden | Details/Voorbeeld | Oplossing |
| --- | --- | --- |
| Vaardigheidsinvoer is het verkeerde type | "Vereiste vaardigheid input was niet `String`van het verwachte type . Naam: `text`, `/document/merged_content`Bron: ."  "Vereiste vaardigheid input was niet van het verwachte formaat. Naam: `text`, `/document/merged_content`Bron: ."  "Kan niet herhalen over `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`non-array."  'Kan niet `0` selecteren in `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`niet-array' | Bepaalde vaardigheden verwachten ingangen van bepaalde `text` typen, bijvoorbeeld Sentiment [vaardigheid](cognitive-search-skill-sentiment.md) verwacht een string. Als de invoer een niet-tekenreekswaarde opgeeft, wordt de vaardigheid niet uitgevoerd en genereert deze geen uitvoer. Controleer of uw gegevensset invoerwaarden uniform van type heeft of gebruik een [aangepaste web-API-vaardigheid](cognitive-search-custom-skill-web-api.md) om de invoer vooraf te verwerken. Als u de vaardigheid over een array wijzigt, controleert `*` u de vaardigheidscontext en -invoer in de juiste posities. Meestal moeten zowel de context `*` als de invoerbron eindigen met voor arrays. |
| Vaardigheidsinvoer ontbreekt | "Vereiste vaardigheid input ontbreekt. Naam: `text`, `/document/merged_content`Bron: " `/document/normalized_images/0/imageTags`"Ontbrekende waarde."  "Niet in `0` staat `/document/pages` om `0`te selecteren in array van lengte." | Als al uw documenten deze waarschuwing krijgen, is er waarschijnlijk een typfout in de `*` invoerpaden en moet u de behuizing van de eigendomsnaam, extra of ontbrekend in het pad, dubbel controleren en ervoor zorgen dat de documenten van de gegevensbron de vereiste invoer bieden. |
| Voertaalcode-invoer is ongeldig | Vaardigheidsinvoer `languageCode` heeft de `X,Y,Z`volgende taalcodes, waarvan er ten minste één ongeldig is. | Zie [hieronder](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) meer details |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Waarschuwing: Skill input 'languageCode' heeft de volgende taalcodes 'X,Y,Z', waarvan er ten minste één ongeldig is.
Een of meer van de `languageCode` waarden die worden doorgegeven aan de optionele invoer van een downstreamvaardigheid, worden niet ondersteund. Dit kan gebeuren als u de uitvoer van de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) doorgeeft aan latere vaardigheden en de uitvoer uit meer talen bestaat dan wordt ondersteund in die downstreamvaardigheden.

Als u weet dat uw gegevensset in één taal is, `languageCode` moet u de `defaultLanguageCode` [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) en de vaardigheidsinvoer verwijderen en in plaats daarvan de vaardigheidsparameter voor die vaardigheid gebruiken, ervan uitgaande dat de taal voor die vaardigheid wordt ondersteund.

Als u weet dat uw gegevensset meerdere talen bevat `languageCode` en u dus de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) en invoer nodig hebt, u overwegen een [ConditionalSkill](cognitive-search-skill-conditional.md) toe te voegen om de tekst uit te filteren met talen die niet worden ondersteund voordat u de tekst doorgeeft aan de downstream-vaardigheid.  Hier volgt een voorbeeld van hoe dit eruit zou kunnen zien voor de EntityRecognitionSkill:

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

Hier volgen enkele verwijzingen naar de momenteel ondersteunde talen voor elk van de vaardigheden die dit foutbericht kunnen opleveren:
* [Ondersteunde talen voor text analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (voor de [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)en [PIIDetectionSkill)](cognitive-search-skill-pii-detection.md)
* [Door vertalers ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (voor de [Text TranslationSkill)](cognitive-search-skill-text-translation.md)
* [Tekst SplitSkill](cognitive-search-skill-textsplit.md) Ondersteunde talen:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Waarschuwing: Vaardigheidsinvoer is afgekapt
Cognitieve vaardigheden hebben grenzen aan de lengte van de tekst die kan worden geanalyseerd in een keer. Als de tekstinvoer van deze vaardigheden boven die limiet ligt, worden de tekst afgekapt om aan de limiet te voldoen en voeren we de verrijking uit op die afgekapte tekst. Dit betekent dat de vaardigheid wordt uitgevoerd, maar niet over al uw gegevens.

In het voorbeeld LanguageDetectionSkill `'text'` hieronder kan het invoerveld deze waarschuwing activeren als deze de tekenlimiet overschrijdt. U vindt de vaardigheid input grenzen in de [vaardigheden documentatie](cognitive-search-predefined-skills.md).

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

Als u ervoor wilt zorgen dat alle tekst wordt geanalyseerd, u overwegen de [vaardigheid Splitsen te](cognitive-search-skill-textsplit.md)gebruiken.

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Waarschuwing: web-API-vaardigheidsrespons bevat waarschuwingen
Indexer was in staat om een vaardigheid in de skillset uit te voeren, maar het antwoord van de Web API-aanvraag gaf aan dat er waarschuwingen waren tijdens de uitvoering. Bekijk de waarschuwingen om te begrijpen hoe uw gegevens worden beïnvloed en of er actie moet worden ondernomen.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Waarschuwing: de huidige indexerconfiguratie biedt geen ondersteuning voor incrementele voortgang

Deze waarschuwing vindt alleen plaats voor Gegevensbronnen van Cosmos DB.

Incrementele voortgang tijdens het indexeren zorgt ervoor dat als de uitvoering van indexeren wordt onderbroken door tijdelijke fouten of uitvoeringstijdslimiet, de indexer kan verder gaan waar hij de volgende keer was gebleven, in plaats van de hele verzameling opnieuw te indexeren. Dit is vooral belangrijk bij het indexeren van grote collecties.

De mogelijkheid om een onvoltooide indexeringstaak te hervatten, is gebaseerd op het hebben van documenten die door de `_ts` kolom zijn besteld. De indexer gebruikt de tijdstempel om te bepalen welk document vervolgens moet worden opgehaald. Als `_ts` de kolom ontbreekt of als de indexer niet kan bepalen of een aangepaste query door de kolom is geordend, begint de indexer bij het begin en ziet u deze waarschuwing.

Het is mogelijk om dit gedrag te overschrijven, waardoor `assumeOrderByHighWatermarkColumn` incrementele voortgang mogelijk is en deze waarschuwing wordt onderdrukt met behulp van de eigenschap configuratie.

Zie [Incrementele voortgang en aangepaste query's](search-howto-index-cosmosdb.md#IncrementalProgress)voor meer informatie.

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Waarschuwing: Sommige gegevens zijn verloren gegaan tijdens de projectie. Rij 'X' in tabel 'Y' heeft string eigenschap 'Z' die te lang was.

De [service Tabelopslag](https://azure.microsoft.com/services/storage/tables) heeft beperkingen voor hoe groot [entiteitseigenschappen](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) kunnen zijn. Tekenreeksen kunnen 32.000 tekens of minder hebben. Als een rij met een tekenreekseigenschap die langer is dan 32.000 tekens wordt geprojecteerd, blijven alleen de eerste 32.000 tekens behouden. Als u dit probleem wilt oplossen, moet u voorkomen dat rijen met tekenreekseigenschappen langer dan 32.000 tekens worden projecteren.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Waarschuwing: Uitgeknotte uitgepakte tekst naar X-tekens
Indexers beperken hoeveel tekst uit één document kan worden geëxtraheerd. Deze limiet is afhankelijk van de prijscategorie: 32.000 tekens voor gratis laag, 64.000 voor Basic, 4 miljoen voor Standard, 8 miljoen voor Standard S2 en 16 miljoen voor Standard S3. Tekst die is afgekapt, wordt niet geïndexeerd. Als u deze waarschuwing wilt voorkomen, probeert u documenten met grote hoeveelheden tekst uit elkaar te halen in meerdere, kleinere documenten. 

Zie [Indexerlimieten voor](search-limits-quotas-capacity.md#indexer-limits)meer informatie .

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Waarschuwing: Kan uitvoerveld 'X' niet toewijzen aan zoekindex
Uitvoerveldtoewijzingen die verwijzen naar niet-bestaande/null-gegevens, leveren waarschuwingen voor elk document en resulteren in een leeg indexveld. Als u dit probleem wilt oplossen, controleert u de bronpaden voor uitvoerveldtoewijzing op mogelijke typefouten of stelt u een standaardwaarde in met de [voorwaardelijke vaardigheid](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Waarschuwing: het detectiebeleid voor gegevenswijziging is geconfigureerd om sleutelkolom 'X' te gebruiken
[Het detectiebeleid voor gegevenswijziging](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) bevat specifieke vereisten voor de kolommen die ze gebruiken om wijzigingen te detecteren. Een van deze vereisten is dat deze kolom wordt bijgewerkt elke keer dat het bronitem wordt gewijzigd. Een andere vereiste is dat de nieuwe waarde voor deze kolom groter is dan de vorige waarde. Belangrijke kolommen voldoen niet aan deze vereiste omdat ze niet op elke update worden gewijzigd. Als u dit probleem wilt oplossen, selecteert u een andere kolom voor het wijzigingsdetectiebeleid.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Waarschuwing: documenttekst lijkt utf-16 gecodeerd te zijn, maar mist een byte-ordermerk

De [indexerparerende modi](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) moeten weten hoe tekst wordt gecodeerd voordat u deze ontkadert. De twee meest voorkomende manieren om tekst te coderen zijn UTF-16 en UTF-8. UTF-8 is een codering met variabele lengte waarbij elk teken tussen 1 byte en 4 bytes lang is. UTF-16 is een codering met een vaste lengte waarbij elk teken 2 bytes lang is. UTF-16 heeft twee verschillende varianten, "big endian" en "little endian". Tekstcodering wordt bepaald door een "byte order mark", een reeks bytes voor de tekst.

| Encoding | Byte Order Mark |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 Kleine Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Als er geen byteordermerk aanwezig is, wordt ervan uitgegaan dat de tekst is gecodeerd als UTF-8.

Als u deze waarschuwing wilt omzeilen, bepaalt u wat de tekstcodering voor deze blob is en voegt u het juiste doorudeksingsteken toe.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Waarschuwing: Cosmos DB collectie 'X' heeft een Lazy indexering beleid. Sommige gegevens kunnen verloren gaan

Verzamelingen [Lazy](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) met lazy-indexeringsbeleid kunnen niet consistent worden opgevraagd, waardoor uw indexer gegevens mist. Als u deze waarschuwing wilt omzeilen, wijzigt u uw indexeringsbeleid in Consistent.
