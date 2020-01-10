---
title: Inleiding tot kennis archief (preview-versie)
titleSuffix: Azure Cognitive Search
description: Verrijkte documenten verzenden naar Azure Storage waar u verrijkte documenten kunt weer geven, wijzigen en gebruiken in azure Cognitive Search en in andere toepassingen. Deze functie is beschikbaar voor openbare preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 9a6fa62384615f60da88bb41da8ad3538d34e62a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754108"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Inleiding tot kennis winkels in azure Cognitive Search

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

Knowledge Store is een functie van Azure Cognitive Search die de uitvoer van een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) persistent maakt voor onafhankelijke analyses of downstream-verwerking. Een *verrijkt document* is de uitvoer van een pijp lijn, gemaakt op basis van inhoud die is geëxtraheerd, gestructureerd en geanalyseerd met behulp van AI-processen. In een Standard AI-pijp lijn zijn verrijkte documenten onwaar, die alleen tijdens het indexeren worden gebruikt en vervolgens worden verwijderd. In het kennis archief worden verrijkte documenten bewaard. 

Als u in het verleden cognitieve vaardig heden hebt gebruikt, weet u al dat *vaardig heden* een document verplaatst via een reeks verrijkingen. De uitkomst kan een zoek index zijn of (nieuw in deze preview) projecties in een kennis archief. De twee uitvoer, zoek index en kennis opslag zijn producten van dezelfde pijp lijn; afgeleid van dezelfde invoer, maar resulteert in uitvoer die is gestructureerd, opgeslagen en op zeer verschillende manieren gebruikt.

Fysiek is een kennis archief [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), ofwel Azure Table Storage, Azure Blob Storage, of beide. Elk hulp programma of proces waarmee verbinding kan worden gemaakt met Azure Storage, kan de inhoud van een kennis archief gebruiken.

![Kennis archief in pijplijn diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kennis archief in pijplijn diagram")

## <a name="benefits-of-knowledge-store"></a>Voor delen van kennis archief

Een kennis archief biedt u de structuur, context en werkelijke inhoud-afgelezen van ongestructureerde en semi-gestructureerde gegevens bestanden zoals blobs, afbeeldings bestanden die analyses hebben ondergaan of zelfs gestructureerde gegevens, met een nieuwe vorm. In een [Stapsgewijze walkthrough](knowledge-store-create-rest.md)kunt u de eerste hand leiding zien hoe een gelijkmatig JSON-document is gepartitioneerd in substructuren, opnieuw te maken in nieuwe structuur en op andere wijze beschikbaar wordt gesteld voor downstream-processen, zoals machine learning en werk belastingen voor data technologie.

Hoewel het handig is om te zien wat een AI-verrijkings pijplijn kan produceren, is het echte potentieel van een kennis archief de mogelijkheid om gegevens te wijzigen. U kunt beginnen met een basis vaardighedenset en vervolgens door lopen om meer structuur niveaus toe te voegen. deze kunnen vervolgens worden gecombineerd tot nieuwe structuren in andere apps dan Azure Cognitive Search.

Geïnventariseerd, de voor delen van kennis archief zijn onder andere het volgende:

+ Gebruik verrijkte documenten in [analyse-en rapportage hulpprogramma's,](#tools-and-apps) met uitzonde ring van zoeken. Power BI met Power Query is een fascinerende keuze, maar elk hulp programma of elke app waarmee verbinding kan worden gemaakt met Azure Storage kan worden opgehaald uit een kennis archief dat u maakt.

+ Verfijn een pijp lijn voor AI-indexering bij het opsporen van fouten en definities van vaardig heden. In een kennis archief ziet u het product van een vaardighedenset-definitie in een AI-Indexing-pijp lijn. U kunt deze resultaten gebruiken om een betere vaardig heden te ontwerpen, want u kunt precies zien hoe de verrijkingen eruitzien. U kunt [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in azure Storage gebruiken om de inhoud van een kennis archief weer te geven.

+ De gegevens vorm geven in nieuwe formulieren. Het omvormen wordt in vaardig heden gecodificeerd, maar het punt is dat deze mogelijkheid nu kan worden geboden door een vaardig heden. De [shaper-vaardigheid](cognitive-search-skill-shaper.md) in azure Cognitive Search is uitgebreid om deze taak te kunnen verwerken. Met Reshaping kunt u een projectie definiëren die wordt afgestemd op uw gebruik van de gegevens, terwijl de relaties behouden blijven.

> [!Note]
> Nieuwe voor AI-verrijkings-en cognitieve vaardig heden? Azure Cognitive Search integreert met Cognitive Services Vision-en taal functies om bron gegevens te extra heren en te verrijken met behulp van optische teken herkenning (OCR) over afbeeldings bestanden, het herkennen van entiteiten en het uitpakken van sleutel zinnen uit tekst bestanden. Zie [AI-verrijking in Azure Cognitive Search](cognitive-search-concept-intro.md)voor meer informatie.

## <a name="physical-storage"></a>Fysieke opslag

De fysieke expressie van een kennis archief wordt gegeleeerd via het `projections` element van een `knowledgeStore` definitie in een Vakkennisset. De projectie definieert een structuur van de uitvoer zodat deze overeenkomt met uw beoogde gebruik.

Projecties kunnen worden gegeleeerd als tabellen, objecten of bestanden.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Het type projectie dat u in deze structuur opgeeft, bepaalt het type opslag dat wordt gebruikt door het kennis archief.

+ Tabel opslag wordt gebruikt wanneer u `tables`definieert. Definieer een tabel projectie wanneer u Structured Reporting-structuren nodig hebt voor invoer in analyse hulpprogramma's of als gegevens frames exporteert naar andere gegevens archieven. U kunt meerdere `tables` opgeven om een subset of Kruis sectie van verrijkte documenten op te halen. In dezelfde projectie groep blijven tabel relaties behouden, zodat u ermee kunt werken.

+ Blob-opslag wordt gebruikt wanneer u `objects` of `files`definieert. De fysieke weer gave van een `object` is een hiërarchische JSON-structuur die een verrijkt document vertegenwoordigt. Een `file` is een afbeelding die uit een document is geëxtraheerd en die intact is overgebracht naar Blob Storage.

Eén projectie object bevat één set `tables`, `objects`, `files`en voor veel scenario's. het maken van een projectie kan echter genoeg zijn. 

Het is echter mogelijk om meerdere sets `table`te maken -`object`-`file` projecties, en u kunt dat doen als u verschillende gegevens relaties wilt. In een set worden gegevens gekoppeld, ervan uitgaande dat deze relaties bestaan en kunnen worden gedetecteerd. Als u extra sets maakt, zijn de documenten in elke groep nooit gerelateerd. Een voor beeld van het gebruik van meerdere projectie groepen is mogelijk als u wilt dat dezelfde gegevens worden geprojecteerd voor gebruik met uw online systeem en u een specifieke manier wilt gebruiken. u wilt ook hetzelfde gegevens projecteren voor gebruik in een Data Science-pijp lijn die wordt weer gegeven afzonderlijk.

## <a name="requirements"></a>Vereisten 

[Azure Storage](https://docs.microsoft.com/azure/storage/) is vereist. Het biedt fysieke opslag. U kunt Blob Storage, Table Storage of beide gebruiken. Blob-opslag wordt gebruikt voor onintacte, verrijkte documenten, meestal wanneer de uitvoer naar downstream-processen gaat. Table Storage is voor segmenten van verrijkte documenten, die vaak worden gebruikt voor analyse en rapportage.

De [vaardig heden](cognitive-search-working-with-skillsets.md) zijn vereist. Het bevat de `knowledgeStore` definitie en bepaalt de structuur en samen stelling van een verrijkt document. U kunt geen kennis archief maken met een lege vaardig heden. U moet ten minste één vaardigheid hebben in een vakkennisset.

[Indexeer functie](search-indexer-overview.md) is vereist. Een vaardig heden wordt aangeroepen door een Indexeer functie, die de uitvoering verstuurt. Indexeer functies worden geleverd met hun eigen set vereisten en kenmerken. Verschillende van deze kenmerken hebben een directe opslag in een kennis archief:

+ Voor Indexeer functies is een [ondersteunde Azure-gegevens bron](search-indexer-overview.md#supported-data-sources) vereist (de pijp lijn die uiteindelijk het kennis archief maakt, wordt gestart door gegevens uit een ondersteunde bron op Azure te halen). 

+ Voor Indexeer functies is een zoek index vereist. Een Indexeer functie vereist dat u een index schema opgeeft, zelfs als u deze nooit wilt gebruiken. Een minimale index heeft één teken reeks veld, aangeduid als de sleutel.

+ Indexeer functies bieden optionele veld toewijzingen, waarmee een bron veld wordt gealiasd voor een doel veld. Als een standaard veld toewijzing moet worden gewijzigd (om een andere naam of een ander type te gebruiken), kunt u een [veld toewijzing](search-indexer-field-mappings.md) maken binnen een Indexeer functie. Voor de uitvoer van het kennis archief kan het doel een veld in een blob-object of-tabel zijn.

+ Indexeer functies hebben schema's en andere eigenschappen, zoals het wijzigen van detectie mechanismen van verschillende gegevens bronnen, kunnen ook worden toegepast op een kennis archief. U kunt bijvoorbeeld met regel matige tussen pozen een verrijking [plannen](search-howto-schedule-indexers.md) om de inhoud te vernieuwen. 

## <a name="how-to-create-a-knowledge-store"></a>Een kennis archief maken

Als u een kennis archief wilt maken, gebruikt u de portal of de preview-REST API (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>De Azure Portal gebruiken

De wizard **gegevens importeren** bevat opties voor het maken van een kennis archief. Voor de eerste verkenning [maakt u uw eerste kennis archief in vier stappen](knowledge-store-connect-power-bi.md).

1. Selecteer een ondersteunde gegevens bron.

1. Verrijking opgeven: een resource koppelen, vaardig heden selecteren en een kennis archief opgeven. 

1. Maak een index schema. De wizard vereist deze en kan één voor u afleiden.

1. Voer de wizard uit. In deze laatste stap vindt extra heren, verrijking en opslag plaats.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Vaardig heden maken en de preview-REST API gebruiken

Een `knowledgeStore` wordt gedefinieerd binnen een [vaardighedenset](cognitive-search-working-with-skillsets.md), die op zijn beurt wordt aangeroepen door een [Indexeer functie](search-indexer-overview.md). Tijdens verrijking maakt Azure Cognitive Search een ruimte in uw Azure Storage-account en projecteert de verrijkte documenten als blobs of in tabellen, afhankelijk van uw configuratie.

Op dit moment is de preview-REST API het enige mechanisme waarmee u een kennis archief programmatisch kunt maken. Een eenvoudige manier om te verkennen, is [het maken van uw eerste kennis archief met behulp van Postman en de rest API](knowledge-store-create-rest.md).

Referentie-inhoud voor deze preview-functie bevindt zich in de sectie [API-verwijzing](#kstore-rest-api) van dit artikel. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Verbinding maken met hulpprogram ma's en apps

Zodra de verrijkingen in de opslag ruimte bestaan, kunnen alle hulp middelen of technologieën die verbinding maken met Azure Blob of tabel opslag worden gebruikt om de inhoud te verkennen, te analyseren of te gebruiken. De volgende lijst is een begin:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) om de verrijkte document structuur en-inhoud weer te geven. Beschouw dit als het basislijn hulp programma voor het weer geven van de inhoud van het kennis archief.

+ [Power bi](knowledge-store-connect-power-bi.md) voor rapportage en analyse. 

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) voor verdere manipulatie.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-referentie

Deze sectie is een versie van het verwijzings document [Create vaardig heden (rest API)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , aangepast aan het toevoegen van een `knowledgeStore` definitie. 

### <a name="example---knowledgestore-embedded-in-a-skillset"></a>Voor beeld-knowledgeStore Inge sloten in een vaardig heden

In het volgende voor beeld ziet u de `knowledgeStore` aan de onderkant van een definitie van een vakkennisset. 

* Gebruik **post** of **put** om de aanvraag te formuleren.
* Gebruik de `api-version=2019-05-06-Preview` versie van de REST API om de functionaliteit van het kennis archief te openen. 

```http
POST https://[servicename].search.windows.net/skillsets?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```

De hoofd tekst van de aanvraag is een JSON-document dat een vaardig heden definieert, waaronder `knowledgeStore`.

```json
{
  "name": "my-skillset-name",
  "description": "Extract organization entities and generate a positive-negative sentiment score from each document.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<YOUR-COGNITIVE-SERVICES-KEY>"
    },
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [  
                { "tableName": "Organizations", "generatedKeyName": "OrganizationId", "source": "/document/organizations*"}, 
                { "tableName": "Sentiment", "generatedKeyName": "SentimentId", "source": "/document/mySentiment"}
                ], 
                "objects": [ ], 
                "files": [  ]       
            }    
        ]     
    } 
}
```

### <a name="request-body-syntax"></a>Syntaxis van aanvraag tekst  

De volgende JSON geeft een `knowledgeStore`, die deel uitmaakt van een [`skillset`](https://docs.microsoft.com/rest/api/searchservice/create-skillset), dat wordt aangeroepen door een `indexer` (niet weer gegeven). Als u al bekend bent met AI-verrijking, bepaalt een vaardig heden de samen stelling van een verrijkt document. Een vaardig heden moet ten minste één vaardigheid bevatten, waarschijnlijk een shaper-vaardigheid als u gegevens structuren gemoduleerd.

De syntaxis voor het structureren van de aanvraag lading is als volgt.

```json
{   
    "name" : "Required for POST, optional for PUT requests which sets the name on the URI",  
    "description" : "Optional. Anything you want, or null",  
    "skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",
    "cognitiveServices": "A key to Cognitive Services, used for billing.",
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [ 
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    . . .
                ], 
                "objects": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>", 
                    "source": "<DOCUMENT-PATH>", 
                    }
                ], 
                "files": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>",
                    "source": "/document/normalized_images/*"
                    }
                ]  
            },
            {
                "tables": [ ],
                "objects": [ ],
                "files":  [ ]
            }  
        ]     
    } 
}
```

Een `knowledgeStore` heeft twee eigenschappen: een `storageConnectionString` aan een Azure Storage-account en `projections` die de fysieke opslag definieert. U kunt elk opslag account gebruiken, maar dit is rendabel om services in dezelfde regio te gebruiken.

Een `projections` verzameling bevat projectie objecten. Elk projectie object moet `tables`, `objects`, `files` (een van beide) bevatten, die zijn opgegeven of null zijn. In de bovenstaande syntaxis ziet u twee objecten, een volledig opgegeven en de andere volledig null. Binnen een projectie object, zodra het is uitgedrukt in de opslag, blijven de relaties tussen de gegevens, indien gedetecteerd, behouden. 

Maak zoveel projectie-objecten als u nodig hebt om isolatie en specifieke scenario's te ondersteunen (bijvoorbeeld gegevens structuren die worden gebruikt voor het verkennen, en die nodig zijn in een Data Science-werk belasting). U kunt isolatie en aanpassingen voor specifieke scenario's verkrijgen door `source` en `storageContainer` in te stellen of te `table` op verschillende waarden binnen een object. Zie [werken met projecties in een Knowledge Store](knowledge-store-projection-overview.md)voor meer informatie en voor beelden.

|Eigenschap      | Van toepassing op | Beschrijving|  
|--------------|------------|------------|  
|`storageConnectionString`| `knowledgeStore` | Vereist. In deze indeling: `DefaultEndpointsProtocol=https;AccountName=<ACCOUNT-NAME>;AccountKey=<ACCOUNT-KEY>;EndpointSuffix=core.windows.net`|  
|`projections`| `knowledgeStore` | Vereist. Een verzameling eigenschappen objecten die bestaan uit `tables`, `objects`, `files` en de bijbehorende eigenschappen. Ongebruikte projecties kunnen worden ingesteld op null.|  
|`source`| Alle projecties| Het pad naar het knoop punt van de verrijkings structuur die de basis vormt van de projectie. Dit knoop punt is de uitvoer van een van de vaardig heden in de vaardig heden. Paden beginnen met `/document/`, die het verrijkte document vertegenwoordigen, maar kunnen worden uitgebreid naar `/document/content/` of knoop punten in de document structuur. Voor beelden: `/document/countries/*` (alle landen) of `/document/countries/*/states/*` (alle staten in alle landen). Zie [concepten en samen stelling van vaardig heden](cognitive-search-working-with-skillsets.md)voor meer informatie over document paden.|
|`tableName`| `tables`| Een tabel die moet worden gemaakt in azure-tabel opslag. |
|`storageContainer`| `objects`, `files`| De naam van een container die moet worden gemaakt in Azure Blob-opslag. |
|`generatedKeyName`| `tables`| Een kolom die wordt gemaakt in de tabel die een unieke identificatie vormt voor een document. Met de verrijkings pijplijn wordt deze kolom gevuld met gegenereerde waarden.|


### <a name="response"></a>Antwoord  

 Voor een succes volle aanvraag ziet u de status code ' 201 gemaakt '. Standaard bevat de antwoord tekst de JSON voor de definitie van de vaardig heden die is gemaakt. U herinnert dat het kennis archief pas wordt gemaakt als u een Indexeer functie aanroept die verwijst naar deze vaardig heden.

## <a name="next-steps"></a>Volgende stappen

Knowledge Store biedt persistentie van verrijkte documenten, handig bij het ontwerpen van een vaardig heden of het maken van nieuwe structuren en inhoud voor het gebruik door client toepassingen die toegang kunnen krijgen tot een Azure Storage-account.

De eenvoudigste benadering voor het maken van verrijkte documenten is [via de portal](knowledge-store-create-portal.md), maar u kunt ook postman en rest API gebruiken, wat nuttiger is als u inzicht wilt in de manier waarop objecten worden gemaakt en waarnaar wordt verwezen.

> [!div class="nextstepaction"]
> [Een kennis archief maken met behulp van Postman en REST](knowledge-store-create-rest.md)
