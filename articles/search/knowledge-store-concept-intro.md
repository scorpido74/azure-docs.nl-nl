---
title: Inleiding tot kennis archief (preview)-Azure Search
description: Verrijkte documenten naar Azure Storage verzenden waar u verrijkte documenten in Azure Search en in andere toepassingen kunt weer geven, wijzigen en gebruiken.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 07a0d3ef8660d32d14b8339fb76fa296b1c9635b
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202992"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Wat is een kennis winkel in Azure Search?

> [!Note]
> Het kennis archief is een preview-versie en is niet bedoeld voor productie gebruik. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>

Knowledge Store is een functie van Azure Search die verrijkte documenten en meta gegevens opslaat die zijn gemaakt met een op AI gebaseerde indexerings pijplijn [(cognitieve zoek functie)](cognitive-search-concept-intro.md). Een verrijkt document is de uitvoer van een pijp lijn, gemaakt op basis van inhoud die is geëxtraheerd, gestructureerd en geanalyseerd met behulp van resources in Cognitive Services. In een standaard-AI-pijp lijn zijn verrijkte documenten onwaar, die alleen tijdens het indexeren worden gebruikt en vervolgens worden verwijderd. In het kennis archief worden documenten opgeslagen voor de volgende evaluatie, het verkennen en kunnen ze worden opgenomen in een Data Science-werk belasting van downstream. 

Als u de functie voor het zoeken in het verleden hebt gebruikt, weet u al dat vaardig heden wordt gebruikt om een document te verplaatsen met behulp van een reeks verrijkingen. De uitkomst kan een Azure Search index zijn of (nieuwe in deze preview-versie) projecties in een kennis archief. De twee uitvoer, zoek index en kennis opslag zijn fysiek onderscheiden van elkaar. Ze delen dezelfde inhoud, maar worden op zeer verschillende manieren opgeslagen en gebruikt.

Een kennis archief is fysiek een Azure Storage-account, als Azure Table-opslag, Blob-opslag of beide, afhankelijk van hoe u de pijp lijn configureert. Elk hulp programma of proces waarmee verbinding kan worden gemaakt met Azure Storage, kan de inhoud van een kennis archief gebruiken.

Projecties zijn uw mechanisme voor het structureren van gegevens in een kennis archief. In projecties kunt u bijvoorbeeld kiezen of de uitvoer wordt opgeslagen als één BLOB of een verzameling gerelateerde tabellen. Een eenvoudige manier om de inhoud van het kennis archief weer te geven, is via de ingebouwde [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) voor Azure Storage.

![Kennis archief in pijplijn diagram](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Kennis archief in pijplijn diagram")

Als u het kennis archief wilt gebruiken `knowledgeStore` , voegt u een-element toe aan een vaardig heden die stapsgewijze bewerkingen in een indexerings pijplijn definieert. Tijdens de uitvoering maakt Azure Search een ruimte in uw Azure Storage-account en worden de verrijkte documenten met de definitie gemaakt in de pijp lijn.

## <a name="benefits-of-knowledge-store"></a>Voor delen van kennis archief

Een kennis archief biedt u de structuur, context en werkelijke inhoud-afgelezen van ongestructureerde en semi-gestructureerde gegevens bestanden zoals blobs, afbeeldings bestanden die een analyse hebben ondergaan of zelfs gestructureerde gegevens die in nieuwe formulieren worden omgezet. In een [Stapsgewijze](knowledge-store-howto.md) uitleg dat voor deze preview is geschreven, kunt u de eerste hand leiding zien hoe een dicht JSON-document is gepartitioneerd in substructuren, worden omgezet in nieuwe structuren en op andere wijze beschikbaar worden gesteld voor downstream-processen zoals machine Leer-en data wetenschappen-workloads.

Hoewel het handig is om te zien wat een op AI gebaseerde indexerings pijplijn kan produceren, is de mogelijkheid om gegevens te wijzigen. U kunt beginnen met een basis vaardighedenset en vervolgens door lopen om meer structuur niveaus toe te voegen, die u vervolgens in andere apps kan combi neren, behalve Azure Search.

Geïnventariseerd, de voor delen van kennis archief zijn onder andere het volgende:

+ Gebruik verrijkte documenten in [analyse-en rapportage hulpprogramma's,](#tools-and-apps) met uitzonde ring van zoeken. Power BI met Power Query is een fascinerende keuze, maar elk hulp programma of elke app die verbinding kan maken met Azure Storage kan worden opgehaald uit een kennis archief dat u maakt.

+ Verfijn een pijp lijn voor AI-indexering bij het opsporen van fouten en definities van vaardig heden. In een kennis archief ziet u het product van een vaardighedenset-definitie in een AI-Indexing-pijp lijn. U kunt deze resultaten gebruiken om een betere vaardig heden te ontwerpen, want u kunt precies zien hoe de verrijkingen eruitzien. U kunt [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in azure Storage gebruiken om de inhoud van een kennis archief weer te geven.

+ De gegevens vorm geven in nieuwe formulieren. Het omvormen wordt in vaardig heden gecodificeerd, maar het punt is dat deze mogelijkheid nu kan worden geboden door een vaardig heden. De [shaper-vaardigheid](cognitive-search-skill-shaper.md) in azure Search is uitgebreid om deze taak te kunnen verwerken. Met Reshaping kunt u een projectie definiëren die wordt afgestemd op uw gebruik van de gegevens, terwijl de relaties behouden blijven.

> [!Note]
> Bent u niet bekend met het indexeren op basis van AI met behulp van Cognitive Services? Azure Search integreert met Cognitive Services Vision-en taal functies om bron gegevens te extra heren en te verrijken met behulp van optische teken herkenning (OCR) over afbeeldings bestanden, het herkennen van entiteiten en het uitpakken van sleutel zinnen uit tekst bestanden. Zie [Wat is cognitieve zoek actie?](cognitive-search-concept-intro.md)voor meer informatie.

## <a name="create-a-knowledge-store"></a>Een Knowledge Store maken

Een kennis archief maakt deel uit van een definitie van een vaardig heden. In deze preview-versie is het vereist dat de rest API `api-version=2019-05-06-Preview` , met of de wizard **gegevens importeren** in de portal.

De volgende JSON geeft een `knowledgeStore`, die deel uitmaakt van een vaardig heden, die wordt aangeroepen door een Indexeer functie (niet weer gegeven). De specificatie van projecties binnen de `knowledgeStore` bepaalt of tabellen of objecten worden gemaakt in azure Storage.

Als u al bekend bent met het indexeren op basis van AI, bepaalt de definitie van de vaardig heden het maken, de organisatie en de stof van elk verrijkt document.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
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

## <a name="components-backing-a-knowledge-store"></a>Onderdelen voor het maken van een back-up van een kennis archief

Als u een Knowledge Store wilt maken, hebt u de volgende services en artefacten nodig.

### <a name="1---source-data"></a>1: Bron gegevens

De gegevens of documenten die u wilt verrijken, moeten aanwezig zijn in een Azure-gegevens bron die wordt ondersteund door Azure Search Indexeer functies: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)

* [Azure Table storage](search-howto-indexing-azure-tables.md)

### <a name="2---azure-search-service"></a>2-Azure Search-service

U hebt ook een Azure Search-service en de REST API nodig om objecten te maken en te configureren die worden gebruikt voor gegevens verrijking. De REST API voor het maken van een kennis `api-version=2019-05-06-Preview`archief is.

Azure Search biedt de functie indexer, en Indexeer functies worden gebruikt om het hele proces end-to-end te laten oplopen, wat resulteert in persistente, verrijkte documenten in azure Storage. Indexeer functies maken gebruik van een gegevens bron, een index en een vakkennisset, die allemaal vereist zijn voor het aanmaken en invullen van een kennis archief.

| Object | REST-API | Description |
|--------|----------|-------------|
| gegevensbron | [Gegevensbron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Een bron voor het identificeren van een externe Azure-gegevens bron die bron gegevens levert die worden gebruikt voor het maken van verrijkte documenten.  |
| vaardig heden | [Vaardig heden maken (API-Version = 2019-05 -06-preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Een resource coördineert het gebruik van [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) en [aangepaste cognitieve vaardig heden](cognitive-search-custom-skill-interface.md) die in een verrijkings pijplijn worden gebruikt tijdens het indexeren. |
| index | [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Een schema waarmee een Azure Search index wordt uitgedrukt. Velden in de index toewijzing naar velden in bron gegevens of in velden die worden geproduceerd tijdens de verrijkings fase (bijvoorbeeld een veld voor organisatie namen die zijn gemaakt door entiteits herkenning). |
| indexeerfunctie | [Indexeer functie maken (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Een resource waarmee onderdelen worden gedefinieerd die worden gebruikt tijdens de indexering: een gegevens bron, een vaardig heden, veld koppelingen van bron-en intermediair gegevens structuren naar doel index en de index zelf. Het uitvoeren van de Indexeer functie is de trigger voor gegevens opname en verrijking. De uitvoer is een zoek index op basis van het index schema, gevuld met bron gegevens, verrijkt via vaardig heden.  |

### <a name="3---cognitive-services"></a>3-Cognitive Services

Verrijkingen die zijn opgegeven in een vaardig heden zijn aangepast of gebaseerd op de functies Computer Vision en taal in Cognitive Services. De functionaliteit van Cognitive Services wordt gebruikt tijdens het indexeren via uw vaardig heden. Een vakkennisset is een samen stelling van vaardig heden en vaardig heden zijn gebonden aan specifieke Computer Vision en taal functies. Als u Cognitive Services wilt integreren, [koppelt u een Cognitive Services resource](cognitive-search-attach-cognitive-services.md) aan een vakkennisset.

### <a name="4---storage-account"></a>4-opslag account

Onder uw Azure Storage-account maakt Azure Search een BLOB-container of-tabellen of beide, afhankelijk van hoe u projecties configureert binnen de vaardig heden. Als uw gegevens afkomstig zijn uit Azure Blob of tabel opslag, bent u al ingesteld en kunt u het opslag account opnieuw gebruiken. Anders moet u een Azure-opslag account maken. Tabellen en objecten in azure Storage bevatten de verrijkte documenten die zijn gemaakt door de op AI gebaseerde indexerings pijplijn.

Het opslag account is opgegeven in de vaardig heden. In `api-version=2019-05-06-Preview`bevat een vaardighedenset-definitie een definitie van een kennis archief zodat u account gegevens kunt opgeven.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5-toegang en verbruiken

Zodra de verrijkingen in de opslag ruimte bestaan, kunnen alle hulp middelen of technologieën die verbinding maken met Azure Blob of tabel opslag worden gebruikt om de inhoud te verkennen, te analyseren of te gebruiken. De volgende lijst is een begin:

+ [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) om de verrijkte document structuur en-inhoud weer te geven. Beschouw dit als het basislijn hulp programma voor het weer geven van de inhoud van het kennis archief.

+ [Power bi met Power query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) voor query's in natuurlijke taal of gebruik de hulpprogram ma's voor rapportage en analyse als u numerieke gegevens hebt.

+ [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) voor verdere manipulatie.

+ Azure Search index voor zoeken in volledige tekst over inhoud die u hebt geïndexeerd met [cognitieve zoek functie](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Document persistentie

Binnen het opslag account kunnen de verrijkingen worden uitgedrukt als tabellen in azure-tabel opslag of als objecten in Azure Blob-opslag. Deze verrijkingen, zodra ze zijn opgeslagen, kunnen worden gebruikt als bron voor het laden van gegevens in andere data bases en hulpprogram ma's.

+ Tabel opslag is handig als u een schema bewuste representatie van de gegevens in tabel vorm wilt maken. Als u de vorm van elementen opnieuw wilt wijzigen of opnieuw wilt combi neren, geeft de tabel opslag u de benodigde granulatie.

+ Met Blob-opslag maakt u één JSON-weer gave van elk document. U kunt beide opslag opties in één vaardigheidset gebruiken om een volledige reeks expressies te verkrijgen.

+ Azure Search inhoud in een index persistent. Als uw scenario niet-zoek gerelateerd is, bijvoorbeeld als uw doel stelling analyset in een ander hulp programma, kunt u de index verwijderen die door de pijp lijn wordt gemaakt. Maar u kunt de index ook behouden en een ingebouwd hulp programma gebruiken, zoals [Search Explorer](search-explorer.md) als een derde medium (achter Storage Explorer en uw analyse-app) voor interactie met uw inhoud.  

## <a name="inside-a-knowledge-store"></a>Binnen een kennis archief

 Een *projectie* definieert het schema en de structuur van de verrijkingen die overeenkomen met uw beoogde gebruik. U kunt meerdere projecties definiëren als u toepassingen hebt die de gegevens in verschillende indelingen en vormen gebruiken. 

Projecties kunnen worden gegeleeerd als objecten of tabellen:

+ Als object wordt de projectie toegewezen aan Blob Storage, waar de projectie wordt opgeslagen in een container, binnen de objecten of hiërarchische voors tellingen in JSON voor scenario's zoals een Data Science-pijp lijn.

+ Als tabel wordt de projectie toegewezen aan tabel opslag. In een tabel weergave worden de relaties voor scenario's zoals gegevens analyse en exporteren als gegevens frames voor machine learning bewaard. De verrijkte projecties kunnen vervolgens eenvoudig worden geïmporteerd in andere gegevens archieven. 

U kunt in een kennis archief meerdere projecties maken voor verschillende constituencies in uw organisatie. Een ontwikkelaar moet mogelijk toegang hebben tot de volledige JSON-weer gave van een verrijkt document, terwijl gegevens wetenschappers of analisten mogelijk gedetailleerde of modulaire gegevens structuren moeten hebben die door uw vaardig heden worden gevormd.

Als een van de doel stellingen van het verrijkings proces bijvoorbeeld is om ook een gegevensset te maken voor het trainen van een model, is het projecteren van de gegevens in de object opslag een manier om de gegevens in uw data Science-pijp lijnen te gebruiken. Als u een snel Power BI dash board wilt maken op basis van de verrijkte documenten, zou de tabellaire projectie goed werken.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

We raden u aan de gratis service voor Learning doeleinden aan te bieden, maar houd er rekening mee dat het aantal gratis trans acties is beperkt tot 20 documenten per dag, per abonnement.

Wanneer u meerdere services gebruikt, maakt u al uw services in dezelfde regio voor de beste prestaties en kunt u de kosten minimaliseren. Er worden geen kosten in rekening gebracht voor de band breedte voor inkomende gegevens of uitgaande gegevens die naar een andere service in dezelfde regio gaan.

**Stap 1: [Een Azure Search resource maken](search-create-service-portal.md)** 

**Stap 2: [Een Azure-opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Stap 3: [Een Cognitive Services resource maken](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Stap 4: Aan [de slag met de portal](cognitive-search-quickstart-blob.md) -of-ga aan de [slag met voorbeeld gegevens met behulp van rest en postman](knowledge-store-howto.md)** 

U kunt rest `api-version=2019-05-06-Preview` gebruiken om een op AI gebaseerde pijp lijn te maken die een kennis archief bevat. In de nieuwste preview-API biedt het object vaardig heden de `knowledgeStore` definitie.

## <a name="takeaways"></a>Opgedane kennis

Het kennis archief biedt diverse voor delen, waaronder maar niet beperkt tot het gebruik van de verrijkte documenten in andere scenario's dan zoeken, kosten beheer en het beheren van drift in uw verrijkings proces. Deze functies zijn allemaal beschikbaar voor gebruik door simpelweg een opslag account toe te voegen aan uw vaardig heden en de bijgewerkte expressie taal te gebruiken zoals wordt beschreven in aan de [slag met kennis opslag](knowledge-store-howto.md). 

## <a name="next-steps"></a>Volgende stappen

De eenvoudigste benadering voor het maken van verrijkte documenten vindt u via de wizard **gegevens importeren** .

> [!div class="nextstepaction"]
> [Snelstart: Probeer cognitieve zoek functie in een portal-overzicht](cognitive-search-quickstart-blob.md)
