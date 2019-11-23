---
title: Inleiding tot kennis archief (preview-versie)
titleSuffix: Azure Cognitive Search
description: Verrijkte documenten naar Azure Storage verzenden waar u verrijkte documenten in azure Cognitive Search en in andere toepassingen kunt weer geven, wijzigen en gebruiken. Deze functie is beschikbaar voor openbare preview.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a1c6f2d869d8d7ad865005ebd319beac56bdbacd
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720090"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Inleiding tot kennis winkels in azure Cognitive Search

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

Knowledge Store is een functie van Azure Cognitive Search die de uitvoer van een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) persistent maakt voor latere analyses of andere downstream-verwerking. Een *verrijkt document* is de uitvoer van een pijp lijn, gemaakt op basis van inhoud die is geëxtraheerd, gestructureerd en geanalyseerd met behulp van AI-processen. In een Standard AI-pijp lijn zijn verrijkte documenten onwaar, die alleen tijdens het indexeren worden gebruikt en vervolgens worden verwijderd. In het kennis archief worden verrijkte documenten bewaard. 

Als u in het verleden cognitieve vaardig heden hebt gebruikt met Azure Cognitive Search, weet u al dat *vaardig heden* een document verplaatst via een reeks verrijkingen. De uitkomst kan een zoek index zijn of (nieuw in deze preview) projecties in een kennis archief. De twee uitvoer, zoek index en kennis opslag, delen dezelfde inhoud, maar worden op zeer verschillende manieren opgeslagen en gebruikt.

Fysiek is een kennis archief [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview), ofwel Azure Table Storage, Azure Blob Storage, of beide. Elk hulp programma of proces waarmee verbinding kan worden gemaakt met Azure Storage, kan de inhoud van een kennis archief gebruiken.

![Kennis archief in pijplijn diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kennis archief in pijplijn diagram")

Als u het kennis archief wilt gebruiken, voegt u een `knowledgeStore`-element toe aan een vaardig heden die stapsgewijze bewerkingen in een indexerings pijplijn definieert. Tijdens de uitvoering maakt Azure Cognitive Search een ruimte in uw Azure Storage-account en projecteert de verrijkte documenten als blobs of in tabellen, afhankelijk van uw configuratie.

## <a name="benefits-of-knowledge-store"></a>Voor delen van kennis archief

Een kennis archief biedt u de structuur, context en werkelijke inhoud-afgelezen van ongestructureerde en semi-gestructureerde gegevens bestanden zoals blobs, afbeeldings bestanden die een analyse hebben ondergaan of zelfs gestructureerde gegevens die in nieuwe formulieren worden omgezet. In een [stap-voor-stap](knowledge-store-howto.md)ziet u de eerste hand leiding hoe een dicht JSON-document is gepartitioneerd in substructuren, opnieuw samengesteld in nieuwe structuren en op andere wijze beschikbaar wordt gesteld voor downstream-processen zoals machine learning en data wetenschappen workloads.

Hoewel het handig is om te zien wat een AI-verrijkings pijplijn kan produceren, is de mogelijkheid om gegevens te wijzigen. U kunt beginnen met een basis vaardighedenset en vervolgens door lopen om meer structuur niveaus toe te voegen. deze kunnen vervolgens worden gecombineerd tot nieuwe structuren in andere apps dan Azure Cognitive Search.

Geïnventariseerd, de voor delen van kennis archief zijn onder andere het volgende:

+ Gebruik verrijkte documenten in [analyse-en rapportage hulpprogramma's,](#tools-and-apps) met uitzonde ring van zoeken. Power BI met Power Query is een fascinerende keuze, maar elk hulp programma of elke app die verbinding kan maken met Azure Storage kan worden opgehaald uit een kennis archief dat u maakt.

+ Verfijn een pijp lijn voor AI-indexering bij het opsporen van fouten en definities van vaardig heden. In een kennis archief ziet u het product van een vaardighedenset-definitie in een AI-Indexing-pijp lijn. U kunt deze resultaten gebruiken om een betere vaardig heden te ontwerpen, want u kunt precies zien hoe de verrijkingen eruitzien. U kunt [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in azure Storage gebruiken om de inhoud van een kennis archief weer te geven.

+ De gegevens vorm geven in nieuwe formulieren. Het omvormen wordt in vaardig heden gecodificeerd, maar het punt is dat deze mogelijkheid nu kan worden geboden door een vaardig heden. De [shaper-vaardigheid](cognitive-search-skill-shaper.md) in azure Cognitive Search is uitgebreid om deze taak te kunnen verwerken. Met Reshaping kunt u een projectie definiëren die wordt afgestemd op uw gebruik van de gegevens, terwijl de relaties behouden blijven.

> [!Note]
> Nieuwe voor AI-verrijkings-en cognitieve vaardig heden? Azure Cognitive Search integreert met Cognitive Services Vision-en taal functies om bron gegevens te extra heren en te verrijken met behulp van optische teken herkenning (OCR) over afbeeldings bestanden, het herkennen van entiteiten en het uitpakken van sleutel zinnen uit tekst bestanden. Zie [AI-verrijking in Azure Cognitive Search](cognitive-search-concept-intro.md)voor meer informatie.

## <a name="creating-a-knowledge-store"></a>Een kennis archief maken

Een kennis archief maakt deel uit van een [vaardig heden](cognitive-search-working-with-skillsets.md), die op zijn beurt deel uitmaakt van een [Indexeer functie](search-indexer-overview.md). 

In deze preview kunt u een kennis archief maken met behulp van de REST API en `api-version=2019-05-06-Preview`of via de wizard **gegevens importeren** in de portal.

### <a name="json-representation-of-a-knowledge-store"></a>JSON-weer gave van een kennis archief

De volgende JSON geeft een `knowledgeStore`, die deel uitmaakt van een vaardig heden, die wordt aangeroepen door een Indexeer functie (niet weer gegeven). Als u al bekend bent met AI-verrijking, bepaalt een vaardig hedenset het maken, de organisatie en de stof van elk verrijkt document. Een vaardig heden moet ten minste één vaardigheid bevatten, waarschijnlijk een shaper-vaardigheid als u gegevens structuren gemoduleerd.

Een `knowledgeStore` bestaat uit een verbinding en projecties. 

+ Verbinding is een opslag account in dezelfde regio als Azure Cognitive Search. 

+ Projecties zijn tabellen-object paren. `Tables` de fysieke expressie van verrijkte documenten in azure Table Storage te definiëren. `Objects` definieert u de fysieke objecten in Azure Blob-opslag.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
               
            ]      
        },
        { 
            "tables": [ 
            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }        
    ]     
    } 
}
```

### <a name="sources-of-data-for-a-knowledge-store"></a>Gegevens bronnen voor een kennis archief

Als een kennis archief wordt uitgevoerd vanuit een AI-verrijkings pijplijn, wat zijn dan de invoer? De oorspronkelijke gegevens die u wilt extra heren, verrijken en uiteindelijk wilt opslaan naar een kennis archief, kunnen afkomstig zijn van een Azure-gegevens bron die wordt ondersteund door Zoek Indexeer functies: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table storage](search-howto-indexing-azure-tables.md)

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

De Indexeer functies en vaardig heden die u ophaalt en verrijkt of transformeert deze inhoud als onderdeel van een werk belasting voor indexeren en slaat de resultaten vervolgens op in een kennis archief.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>REST-Api's die worden gebruikt bij het maken van een kennis archief

Er zijn slechts twee Api's die nodig zijn voor het maken van een kennis archief (vaardig heden maken en Indexeer functie maken). Andere Api's worden gebruikt als-is.

| Object | REST API | Beschrijving |
|--------|----------|-------------|
| gegevens bron | [Gegevensbron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Een bron voor het identificeren van een externe Azure-gegevens bron die bron gegevens levert die worden gebruikt voor het maken van verrijkte documenten.  |
| vaardig heden | [Vaardig heden maken (API-Version = 2019-05 -06-preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Een resource coördineert het gebruik van [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) en [aangepaste cognitieve vaardig heden](cognitive-search-custom-skill-interface.md) die in een verrijkings pijplijn worden gebruikt tijdens het indexeren. Een vakkennisset heeft een `knowledgeStore` definitie als onderliggend element. |
| index | [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Een schema dat een zoek index uitdrukt. Velden in de index toewijzing naar velden in bron gegevens of in velden die worden geproduceerd tijdens de verrijkings fase (bijvoorbeeld een veld voor organisatie namen die zijn gemaakt door entiteits herkenning). |
| Indexeer functie | [Indexeer functie maken (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Een resource waarmee onderdelen worden gedefinieerd die worden gebruikt tijdens de indexering: een gegevens bron, een vaardig heden, veld koppelingen van bron-en intermediair gegevens structuren naar doel index en de index zelf. Het uitvoeren van de Indexeer functie is de trigger voor gegevens opname en verrijking. De uitvoer is een zoek index op basis van het index schema, gevuld met bron gegevens, verrijkt via vaardig heden.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Fysieke samen stelling van een kennis archief

 Een *projectie*, een element van een `knowledgeStore` definitie, koppelt het schema en de structuur van de uitvoer zodat het overeenkomt met het beoogde gebruik. U kunt meerdere projecties definiëren als u toepassingen hebt die de gegevens in verschillende indelingen en vormen gebruiken. 

Projecties kunnen worden gegeleeerd als objecten of tabellen:

+ Als object wordt de projectie toegewezen aan Blob Storage, waar de projectie wordt opgeslagen in een container, binnen de objecten of hiërarchische voors tellingen in JSON voor scenario's zoals een Data Science-pijp lijn.

+ Als tabel wordt de projectie toegewezen aan tabel opslag. In een tabel weergave worden de relaties voor scenario's zoals gegevens analyse en exporteren als gegevens frames voor machine learning bewaard. De verrijkte projecties kunnen vervolgens eenvoudig worden geïmporteerd in andere gegevens archieven. 

U kunt in een kennis archief meerdere projecties maken voor verschillende constituencies in uw organisatie. Een ontwikkelaar moet mogelijk toegang hebben tot de volledige JSON-weer gave van een verrijkt document, terwijl gegevens wetenschappers of analisten mogelijk gedetailleerde of modulaire gegevens structuren moeten hebben die door uw vaardig heden worden gevormd.

Als een van de doel stellingen van het verrijkings proces bijvoorbeeld is om ook een gegevensset te maken voor het trainen van een model, is het projecteren van de gegevens in de object opslag een manier om de gegevens in uw data Science-pijp lijnen te gebruiken. Als u een snel Power BI dash board wilt maken op basis van de verrijkte documenten, zou de tabellaire projectie goed werken.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Verbinding maken met hulpprogram ma's en apps

Zodra de verrijkingen in de opslag ruimte bestaan, kunnen alle hulp middelen of technologieën die verbinding maken met Azure Blob of tabel opslag worden gebruikt om de inhoud te verkennen, te analyseren of te gebruiken. De volgende lijst is een begin:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) om de verrijkte document structuur en-inhoud weer te geven. Beschouw dit als het basislijn hulp programma voor het weer geven van de inhoud van het kennis archief.

+ [Power bi](knowledge-store-connect-power-bi.md) voor de hulpprogram ma's voor rapportage en analyse als u numerieke gegevens hebt.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) voor verdere manipulatie.

## <a name="next-steps"></a>Volgende stappen

Knowledge Store biedt persistentie van verrijkte documenten, handig bij het ontwerpen van een vaardig heden of het maken van nieuwe structuren en inhoud voor het gebruik door client toepassingen die toegang kunnen krijgen tot een Azure Storage-account.

De eenvoudigste manier om verrijkte documenten te maken, is via de wizard **gegevens importeren** , maar u kunt ook postman en rest API gebruiken. Dit is handiger als u inzicht wilt in de manier waarop objecten worden gemaakt en waarnaar wordt verwezen.

> [!div class="nextstepaction"]
> [Een kennis archief maken met behulp van de portal](knowledge-store-create-portal.md)
> [een kennis archief maken met behulp van Postman en de rest APi](knowledge-store-create-rest.md)
