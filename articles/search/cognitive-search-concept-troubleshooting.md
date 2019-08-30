---
title: Tips voor probleem oplossing voor cognitieve zoek opdracht-Azure Search
description: Tips en probleem oplossing voor het instellen van cognitieve Zoek pijplijnen in Azure Search.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.openlocfilehash: bc83a7c06c52ee26246329d6ca3177bce71c9de8
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186424"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Tips voor het oplossen van problemen met cognitieve zoek acties

Dit artikel bevat een lijst met tips en trucs om u te laten overstappen terwijl u aan de slag gaat met cognitieve zoek mogelijkheden in Azure Search. 

Als u dit nog niet hebt gedaan, volgt u de [zelf studie: Meer informatie over het aanroepen van de](cognitive-search-quickstart-blob.md) cognitieve Zoek-api's voor de praktijk bij het Toep assen van verrijkingen voor cognitieve zoek acties op een BLOB-gegevens bron.

## <a name="tip-1-start-with-a-small-dataset"></a>Tip 1: Beginnen met een kleine gegevensset
De beste manier om snel problemen te vinden is door de snelheid te verhogen waarmee u problemen kunt oplossen. De beste manier om de indexerings tijd te verminderen, is door het aantal te indexeren documenten te verminderen. 

Begin met het maken van een gegevens bron met slechts een aantal documenten/records. Uw document voorbeeld moet een goede weer gave zijn van de verschillende documenten die worden geïndexeerd. 

Voer uw document voorbeeld door de end-to-end-pijp lijn uit en controleer of de resultaten voldoen aan uw behoeften. Zodra u tevreden bent met de resultaten, kunt u meer bestanden toevoegen aan de gegevens bron.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>Tip 2: Controleer of de referenties voor de gegevens bron juist zijn
De verbinding met de gegevens bron wordt pas gevalideerd als u een Indexeer functie hebt gedefinieerd die gebruikmaakt van deze. Als er fouten worden weer gegeven waarin wordt vermeld dat de Indexeer functie de gegevens niet kan ophalen, moet u ervoor zorgen dat:
- Uw connection string is juist. Als u SAS-tokens maakt, moet u er ook voor zorgen dat de indeling wordt verwacht door Azure Search. Zie [de sectie](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) referenties opgeven voor meer informatie over de verschillende indelingen die worden ondersteund.
- De naam van de container in de Indexeer functie is juist.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>Tip 3: Zie wat er gebeurt, zelfs als er fouten zijn
Soms stopt een kleine fout een Indexeer functie in de bijbehorende sporen. Dat is prima als u van plan bent om problemen één voor één op te lossen. Het is echter mogelijk dat u een bepaald type fout wilt negeren, waardoor de Indexeer functie kan door gaan, zodat u kunt zien welke stromen werkelijk werken.

In dat geval kunt u de Indexeer functie de fouten laten negeren. Doe dat door *maxFailedItems* en *maxFailedItemsPerBatch* als-1 in te stellen als onderdeel van de definitie van de Indexeer functie.

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

Het verrijkte veld is alleen bedoeld voor fout opsporing, zodat u inzicht krijgt in de logische vorm van de inhoud waarvoor expressies worden geëvalueerd. U moet niet afhankelijk zijn van dit veld voor het indexeren van de doel einden.

Voeg een ```enriched``` veld toe als onderdeel van de index definitie voor fout opsporing:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>Tip 5: Verwachte inhoud wordt niet weer gegeven

Ontbrekende inhoud kan het gevolg zijn van het weghalen van documenten tijdens het indexeren. Voor de lagen gratis en basis is de document grootte beperkt. Bestanden die de limiet overschrijden, worden verwijderd tijdens het indexeren. U kunt controleren op verwijderde documenten in de Azure Portal. Dubbel klik op de tegel Indexeer functies in het dash board van de zoek service. Bekijk de verhouding van geslaagde documenten die zijn geïndexeerd. Als dit niet 100% is, kunt u op de verhouding klikken om meer details te krijgen. 

Als het probleem te maken heeft met de bestands grootte, ziet u mogelijk een fout als de volgende: "De naam \<van het BLOB-bestand >" heeft de \<grootte van de bestands grootte > bytes, wat de maximale grootte voor het uitpakken van documenten voor uw huidige servicelaag overschrijdt. " Zie [service limieten](search-limits-quotas-capacity.md)voor meer informatie over de limieten voor de Indexeer functie.

Een tweede reden waarom inhoud niet wordt weer gegeven, kan betrekking hebben op fouten in de i/o-toewijzing. De naam van een uitvoer doel is bijvoorbeeld ' personen ', maar de naam van het index veld is kleine letters ' personen '. Het systeem kan 201 succes berichten retour neren voor de volledige pijp lijn, zodat u kunt zien dat indexeren is geslaagd, wanneer een veld leeg is. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>Tip 6: Verwerking uitbreiden na maximale uitvoerings tijd (24-uurs venster)

Het berekenen van de afbeeldings analyse is voor eenvoudige cases evenredig, dus wanneer de installatie kopieën bijzonder groot of complex zijn, kan de maximale toegestane tijd worden overschreden. 

De maximale uitvoerings tijd varieert per laag: enkele minuten op de laag gratis, 24 uur per dag indexeren op factureer bare lagen. Als de verwerking niet kan worden voltooid binnen een periode van 24 uur voor de verwerking op aanvraag, moet u overschakelen naar een schema om de Indexeer functie uit te voeren op het tijdstip waarop deze bewerking is afgebroken. 

Voor geplande Indexeer functies worden de indexeringen hervat op schema in het laatst bekende goede document. Als u een terugkerend schema gebruikt, kan de Indexeer functie de achterstand van de installatie kopie over een reeks uren of dagen werken totdat alle niet-verwerkte installatie kopieën zijn verwerkt. Zie [stap 3: voor meer informatie over de syntaxis van het schema. Create-a-Indexeer functie](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) of Zie [Indexeer functies plannen voor Azure Search](search-howto-schedule-indexers.md).

> [!NOTE]
> Als een Indexeer functie is ingesteld op een bepaald schema, maar herhaaldelijk meerdere keren een fout optreedt in hetzelfde document, wordt de Indexeer functie gestart op een minder frequent interval (Maxi maal ten minste één keer per 24 uur) totdat de voortgang Aga Naast.  Als u van mening bent dat het probleem dat de Indexeer functie niet op een bepaald moment vastloopt, kunt u een on-demand uitvoering van de Indexeer functie uitvoeren. als dat wel het geval is, keert de Indexeer functie weer terug naar het ingestelde plannings interval.

Voor indexering op basis van een portal (zoals beschreven in de Quick Start) kiest u de optie voor het uitvoeren van de Indexeer functie beperkt`"maxRunTime": "PT1H"`de verwerking tot 1 uur (). Mogelijk wilt u het verwerkings venster uitbreiden naar iets langer.

## <a name="tip-7-increase-indexing-throughput"></a>Tip 7: Door Voer van indexering verhogen

Voor [parallelle indexering](search-howto-large-index.md)plaatst u uw gegevens in meerdere containers of meerdere virtuele mappen binnen dezelfde container. Maak vervolgens meerdere data source-en Indexeer functie paren. Alle Indexeer functies kunnen dezelfde vaardig heden gebruiken en naar dezelfde doel zoek index schrijven, zodat uw zoek-app niet op de hoogte hoeft te zijn van deze partities.
Zie [indexeren van grote gegevens sets](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)voor meer informatie.

## <a name="see-also"></a>Zie ook
+ [Snelstart: Een cognitieve Zoek pijplijn maken in de portal](cognitive-search-quickstart-blob.md)
+ [Zelfstudie: Meer informatie over de cognitieve Zoek REST-Api's](cognitive-search-tutorial-blob.md)
+ [Referenties voor de gegevens bron opgeven](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Grote gegevens sets indexeren](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Uitgebreide velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
