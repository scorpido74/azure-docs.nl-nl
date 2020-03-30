---
title: Tips voor AI verrijking ontwerp
titleSuffix: Azure Cognitive Search
description: Tips en probleemoplossing voor het instellen van AI-verrijkingspijplijnen in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245484"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tips voor AI-verrijking in Azure Cognitive Search

Dit artikel bevat een lijst met tips en trucs om u in beweging te houden terwijl u aan de slag gaat met AI-verrijkingsmogelijkheden in Azure Cognitive Search. 

Als u dit nog niet hebt gedaan, stapt u door de [zelfstudie: Leer hoe u AI-verrijkingsAPI's](cognitive-search-quickstart-blob.md) voor de praktijk aanroepen bij het toepassen van AI-verrijkingen op een blob-gegevensbron.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Begin met een kleine dataset
De beste manier om problemen snel te vinden is om de snelheid waarmee u problemen oplossen te verhogen. De beste manier om de indexeringstijd te verkorten, is door het aantal te indexeren documenten te verminderen. 

Begin met het maken van een gegevensbron met slechts een handvol documenten / records. Uw documentvoorbeeld moet een goede weergave zijn van de verscheidenheid aan documenten die worden geïndexeerd. 

Voer het documentvoorbeeld uit via de end-to-end-pijplijn en controleer of de resultaten aan uw behoeften voldoen. Zodra u tevreden bent met de resultaten, u meer bestanden toevoegen aan uw gegevensbron.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Zorg ervoor dat de gegevensbronreferenties correct zijn
De gegevensbronverbinding wordt pas gevalideerd als u een indexeerder definieert die deze gebruikt. Als u fouten ziet waarin staat dat de indexer niet bij de gegevens kan komen, moet u ervoor zorgen dat:
- Uw verbindingstekenreeks is correct. Speciaal wanneer u SAS-tokens maakt, moet u de indeling gebruiken die wordt verwacht door Azure Cognitive Search. Zie [De sectie Referenties opgeven](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) voor meer informatie over de verschillende ondersteunde indelingen.
- De naam van uw container in de indexer is correct.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Zie wat werkt, zelfs als er een aantal storingen
Soms stopt een kleine fout een indexeerder in zijn sporen. Dat is prima als u van plan bent om problemen een voor een op te lossen. U echter een bepaald type fout negeren, zodat de indexer kan doorgaan, zodat u zien welke stromen daadwerkelijk werken.

In dat geval u de indexer vertellen fouten te negeren. Doe dat door *maxFailedItems* en *maxFailedItemsPerBatch* in te stellen als -1 als onderdeel van de indexerdefinitie.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>Tip 4: Kijken naar verrijkte documenten onder de motorkap 
Verrijkte documenten zijn tijdelijke structuren die tijdens de verrijking zijn gemaakt en vervolgens worden verwijderd wanneer de verwerking is voltooid.

Als u een momentopname van het verrijkte document wilt vastleggen tijdens het indexeren, voegt u een veld met de naam ```enriched``` toe aan uw index. De indexeerfunctie dumpt automatisch een tekenreeksrepresentatie van de verrijkingen voor het document in het veld.

Het veld ```enriched``` bevat dan een tekenreeks die een logische representatie vormt van het verrijkte document in het geheugen in JSON.  De veldwaarde is echter een geldig JSON-document. Aanhalingstekens worden geïdentificeerd met een escape-teken, waardoor u `\"` met `"` moet vervangen als u het document als geformatteerde JSON wilt weergeven. 

Het verrijkte veld is alleen bedoeld voor foutopsporingsdoeleinden, om u te helpen de logische vorm te begrijpen van de inhoud waartegen expressies worden geëvalueerd. U moet niet afhankelijk zijn van dit veld voor indexeringsdoeleinden.

Voeg ```enriched``` een veld toe als onderdeel van uw indexdefinitie voor foutopsporingsdoeleinden:

#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: Verwachte inhoud wordt niet weergegeven

Ontbrekende inhoud kan het gevolg zijn van documenten die tijdens het indexeren worden gedropt. Gratis en Basic-lagen hebben lage limieten voor de documentgrootte. Elk bestand dat de limiet overschrijdt, wordt tijdens het indexeren verwijderd. U controleren op gevallen documenten in de Azure-portal. Dubbelklik in het dashboard van de zoekservice op de tegel Indexers. Bekijk de verhouding van de geïndexeerde succesvolle documenten. Als het niet 100% is, u op de verhouding klikken om meer detail te krijgen. 

Als het probleem gerelateerd is aan de bestandsgrootte, ziet \<u mogelijk een fout als \<volgt: 'De blob-bestandsnaam>' heeft de grootte van de bestandsgrootte> bytes, die de maximale grootte voor documentextractie voor uw huidige servicelaag overschrijdt. Zie [Servicelimieten](search-limits-quotas-capacity.md)voor meer informatie over indexerlimieten.

Een tweede reden voor inhoud die niet wordt weergegeven, zijn mogelijk gerelateerde fouten in het toewijzingssysteem voor invoer/uitvoer. Een uitvoerdoelnaam is bijvoorbeeld 'Personen', maar de naam van het indexveld is kleine 'personen'. Het systeem kan 201 succesberichten voor de hele pijplijn retourneren, zodat u denkt dat indexeren geslaagd is, terwijl in feite een veld leeg is. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: Verleng de verwerking tot na de maximale looptijd (24-uursvenster)

Beeldanalyse is rekenintensief voor zelfs eenvoudige gevallen, dus wanneer afbeeldingen bijzonder groot of complex zijn, kunnen verwerkingstijden de toegestane maximumtijd overschrijden. 

De maximale looptijd verschilt per laag: enkele minuten op de gratis laag, 24-uurs indexering op factureerbare lagen. Als de verwerking niet binnen een periode van 24 uur is voltooid voor verwerking op aanvraag, schakelt u over naar een planning om de indexeerder de verwerking te laten verwerken waar deze was gebleven. 

Voor geplande indexeerders wordt de indexering op schema hervat bij het laatst bekende goede document. Door een terugkerend schema te gebruiken, kan de indexer zich een weg banen door de beeldachterstand gedurende een reeks uren of dagen, totdat alle niet-verwerkte afbeeldingen zijn verwerkt. Zie [Stap 3: Maak-een-indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) of zie [Indexers plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over de syntaxis van de planning.

> [!NOTE]
> Als een indexer is ingesteld op een bepaald schema, maar herhaaldelijk niet op hetzelfde document over en weer elke keer dat het wordt uitgevoerd, zal de indexer beginnen te draaien op een minder frequent interval (tot het maximum van ten minste eenmaal per 24 uur) totdat het met succes vooruitgang boekt Opnieuw.  Als u denkt dat u het probleem hebt opgelost waardoor de indexer op een bepaald punt vast kwam te zitten, u een on-demand-run van de indexeerder uitvoeren en als dat met succes vooruitgang boekt, keert de indexer terug naar het ingestelde schemainterval.

Voor portal-gebaseerde indexering (zoals beschreven in de quickstart), het kiezen van de`"maxRunTime": "PT1H"`"eenmaal uitvoeren" indexer optie beperkt de verwerking tot 1 uur ( ). U het verwerkingsvenster uitbreiden naar iets langer.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: Verhoog de indexeringsdoorvoer

Voor [parallelle indexering](search-howto-large-index.md)plaatst u uw gegevens in meerdere containers of meerdere virtuele mappen in dezelfde container. Maak vervolgens meerdere datasource- en indexerparen. Alle indexers kunnen dezelfde vaardigheden gebruiken en schrijven in dezelfde doelzoekindex, zodat uw zoek-app zich niet bewust hoeft te zijn van deze partitionering.
Zie [Grote gegevenssets indexeren voor](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)meer informatie .

## <a name="see-also"></a>Zie ook
+ [Snelstart: een AI-verrijkingspijplijn maken in de portal](cognitive-search-quickstart-blob.md)
+ [Zelfstudie: AI-verrijkingREST API's leren](cognitive-search-tutorial-blob.md)
+ [Gegevensbronreferenties opgeven](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Grote gegevenssets indexeren](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Verrijkte velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
