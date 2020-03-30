---
title: Zoeken via JSON-blobs
titleSuffix: Azure Cognitive Search
description: Azure JSON-blobs crawlen voor tekstinhoud met de Azure Cognitive Search Blob-indexer. Indexers automatiseren gegevensopname voor geselecteerde gegevensbronnen zoals Azure Blob-opslag.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533974"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Json-blobs indexeren met een Blob-indexer in Azure Cognitive Search

In dit artikel ziet u hoe u een Azure Cognitive Search [blob-indexer kunt](search-indexer-overview.md) configureren om gestructureerde inhoud uit JSON-documenten in Azure Blob-opslag te extraheren en doorzoekbaar te maken in Azure Cognitive Search. Met deze werkstroom wordt een Azure Cognitive Search-index gemaakt en geladen met bestaande tekst uit JSON-blobs. 

U de [portal,](#json-indexer-portal) [REST API's](#json-indexer-rest)of [.NET SDK](#json-indexer-dotnet) gebruiken om JSON-inhoud te indexeren. Veel voorkomende benaderingen is dat JSON-documenten zich in een blobcontainer in een Azure Storage-account bevinden. Zie [Gegevens importeren in Azure Cognitive Search](search-what-is-data-import.md)voor richtlijnen voor het pushen van JSON-documenten vanaf andere niet-Azure-platforms.

JSON-blobs in Azure Blob-opslag zijn meestal één JSON-document (parsing-modus is `json`) of een verzameling JSON-entiteiten. Voor verzamelingen kan de blob een **array** van goed gevormde JSON-elementen hebben (parsing-modus is `jsonArray`). Blobs kunnen ook bestaan uit meerdere afzonderlijke JSON-entiteiten gescheiden door `jsonLines`een nieuwe regel (parsing-modus is). De **parameter parsingMode** op de aanvraag bepaalt de uitvoerstructuren.

> [!NOTE]
> Zie [Eén-op-veel indexering](search-howto-index-one-to-many-blobs.md)voor meer informatie over het indexeren van meerdere zoekdocumenten vanuit één blob.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Gebruik de portal

De eenvoudigste methode voor het indexeren van JSON-documenten is het gebruik van een wizard in de [Azure-portal.](https://portal.azure.com/) Door metagegevens in de Azure blob-container te ontwijgen, kan de wizard [**Gegevens importeren**](search-import-data-portal.md) een standaardindex maken, bronvelden toewijzen om indexvelden te targeten en de index in één bewerking laden. Afhankelijk van de grootte en complexiteit van brongegevens, u binnen enkele minuten een operationele full text search index hebben.

We raden u aan dezelfde regio of locatie te gebruiken voor zowel Azure Cognitive Search als Azure Storage voor lagere latentie en om bandbreedtekosten te voorkomen.

### <a name="1---prepare-source-data"></a>1 - Brongegevens voorbereiden

[Meld u aan bij de Azure-portal](https://portal.azure.com/) en [maak een Blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om uw gegevens te bevatten. Het openbare toegangsniveau kan worden ingesteld op een van de geldige waarden.

U hebt de naam van het opslagaccount, de naam van de container en een toegangssleutel nodig om uw gegevens op te halen in de wizard **Gegevens importeren.**

### <a name="2---start-import-data-wizard"></a>2 - Wizard Gegevens importeren starten

Op de pagina Overzicht van uw zoekservice u [de wizard starten](search-import-data-portal.md) vanaf de opdrachtbalk.

   ![Opdracht Gegevens importeren in portal](./media/search-import-data-portal/import-data-cmd2.png "De wizard Gegevens importeren starten")

### <a name="3---set-the-data-source"></a>3 - De gegevensbron instellen

Op de **pagina gegevensbron** moet de bron **Azure Blob Storage**zijn, met de volgende specificaties:

+ **Gegevens die moeten worden geëxtraheerd,** moeten *inhoud en metagegevens*zijn. Als u deze optie kiest, kan de wizard een indexschema afleiden en de velden voor import in kaart brengen.
   
+ **De parsingmodus** moet worden ingesteld op *JSON-,* *JSON-array-* of *JSON-lijnen*. 

  *JSON* articuleert elke blob als één zoekdocument, weergegeven als een onafhankelijk item in de zoekresultaten. 

  *JSON array* is voor blobs die goed gevormde JSON-gegevens bevatten - de goed gevormde JSON komt overeen met een array van objecten, of heeft een eigenschap die een array van objecten is en u wilt dat elk element wordt gearticuleerd als een zelfstandig, onafhankelijk zoekdocument. Als blobs complex zijn en u geen *JSON-array* kiest, wordt de hele blob ingenomen als één document.

  *JSON-regels* zijn bedoeld voor blobs die bestaan uit meerdere JSON-entiteiten die zijn gescheiden door een nieuwe regel, waarbij u wilt dat elke entiteit wordt gearticuleerd als een zelfstandig onafhankelijk zoekdocument. Als blobs complex zijn en u geen *JSON-lijnen-parsing-modus* kiest, wordt de hele blob ingenomen als één document.
   
+ **De opslagcontainer** moet uw opslagaccount en container opgeven, of een verbindingstekenreeks die naar de container wordt opgelost. U verbindingstekenreeksen op de pagina van de Blob-serviceportal krijgen.

   ![Blob-gegevensbrondefinitie](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - De pagina Inhoud verrijken overslaan in de wizard

Het toevoegen van cognitieve vaardigheden (of verrijking) is geen importvereiste. Tenzij u een specifieke noodzaak hebt om [AI-verrijking toe](cognitive-search-concept-intro.md) te voegen aan uw indexeringspijplijn, moet u deze stap overslaan.

Als u de stap wilt overslaan, klikt u op de blauwe knoppen onder aan de pagina voor 'Volgende' en 'Overslaan'.

### <a name="5---set-index-attributes"></a>5 - Indexkenmerken instellen

Op de pagina **Index** ziet u een lijst met velden met een gegevenstype en een reeks selectievakjes voor het instellen van indexkenmerken. De wizard kan een veldenlijst genereren op basis van metagegevens en door de brongegevens te samplen. 

U kenmerken bulkselecteren door boven aan een kenmerkkolom op het selectievakje te klikken. Kies **Opvraagbaar** en **doorzoekbaar** voor elk veld dat moet worden geretourneerd naar een client-app en onder voorbehoud van volledige tekstzoekverwerking. U zult merken dat gehele getallen geen volledige tekst of fuzzy doorzoekbaar zijn (getallen worden letterlijk geëvalueerd en zijn vaak nuttig in filters).

Bekijk de beschrijving van [indexkenmerken](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) en [taalanalysers](https://docs.microsoft.com/rest/api/searchservice/language-support) voor meer informatie. 

Neem even de tijd om uw selecties te bekijken. Zodra u de wizard uitvoert, worden fysieke gegevensstructuren gemaakt en u deze velden niet bewerken zonder alle objecten te laten vallen en opnieuw te maken.

   ![Blob-indexdefinitie](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Indexermaken

Volledig opgegeven, de wizard maakt drie verschillende objecten in uw zoekservice. Een gegevensbronobject en -indexobject worden opgeslagen als benoemde resources in uw Azure Cognitive Search-service. Met de laatste stap wordt een indexerobject gemaakt. Als u de indexer noemt, kan deze bestaan als een zelfstandige bron, die u onafhankelijk van het index- en gegevensbronobject plannen en beheren dat in dezelfde wizardreeks is gemaakt.

Als u niet bekend bent met indexeerders, is een *indexeerder* een bron in Azure Cognitive Search die een externe gegevensbron crawlt voor doorzoekbare inhoud. De uitvoer van de wizard **Gegevens importeren** is een indexer die uw JSON-gegevensbron crawlt, doorzoekbare inhoud extraheert en deze importeert in een index op Azure Cognitive Search.

   ![Blob-indexerdefinitie](media/search-howto-index-json/import-wizard-json-indexer.png)

Klik op **OK** om de wizard uit te voeren en alle objecten te maken. De indexering begint onmiddellijk.

U het importeren van gegevens in de portalpagina's controleren. Voortgangsmeldingen geven de indexeringsstatus aan en hoeveel documenten worden geüpload. 

Wanneer de indexering is voltooid, u [Zoekverkenner](search-explorer.md) gebruiken om uw index op te vragen.

> [!NOTE]
> Als u de verwachte gegevens niet ziet, moet u mogelijk meer kenmerken instellen op meer velden. Verwijder de index en indexer die u zojuist hebt gemaakt en stap opnieuw door de wizard en wijzig uw selecties voor indexkenmerken in stap 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST-API's gebruiken

U de REST API gebruiken om JSON-blobs te indexeren, waarbij een driedelige werkstroom volgt die alle indexeerders in Azure Cognitive Search gemeen hebben: maak een gegevensbron, maak een index, maak een indexeerder. Gegevensextractie uit blobopslag vindt plaats wanneer u de aanvraag Indexer maken indient. Nadat dit verzoek is voltooid, hebt u een opvraagbare index. 

U [restvoorbeeldcode](#rest-example) aan het einde van deze sectie bekijken die laat zien hoe u alle drie de objecten maakt. Deze sectie bevat ook details over [JSON-parsingmodi,](#parsing-modes) [enkele blobs,](#parsing-single-blobs) [JSON-arrays](#parsing-arrays)en [geneste arrays](#nested-json-arrays).

Gebruik [Postman](search-get-started-postman.md) en de REST API voor op code gebaseerde JSON-indexering om deze objecten te maken:

+ [Index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [gegevensbron](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Volgorde van bewerkingen vereist dat u objecten in deze volgorde maakt en aanroept. In tegenstelling tot de portalworkflow vereist een codebenadering een beschikbare index om de JSON-documenten te accepteren die via de aanvraag **Indexer maken** worden verzonden.

JSON-blobs in Azure Blob-opslag zijn meestal één JSON-document of een JSON-array. De blob-indexer in Azure Cognitive Search kan beide constructies ontlopen, afhankelijk van hoe u de parameter **parsingMode** op de aanvraag instelt.

| JSON-document | parsingMode | Beschrijving | Beschikbaarheid |
|--------------|-------------|--------------|--------------|
| Eén per blob | `json` | Parses JSON blobs als een enkel stuk tekst. Elke JSON-blob wordt één Azure Cognitive Search-document. | Algemeen beschikbaar in zowel [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API als [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Meerdere per blob | `jsonArray` | Hiermee wordt een JSON-array in de blob gepareert, waarbij elk element van de array een afzonderlijk Azure Cognitive Search-document wordt.  | Algemeen beschikbaar in zowel [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API als [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Meerdere per blob | `jsonLines` | Hiermee wordt een blob gepariseerd die meerdere JSON-entiteiten (een 'array') bevat, gescheiden door een nieuwe regel, waarbij elke entiteit een afzonderlijk Azure Cognitive Search-document wordt. | Algemeen beschikbaar in zowel [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API als [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Ingangen voor het verzoek monteren

Voor elk verzoek moet u de servicenaam en de beheerderssleutel voor Azure Cognitive Search (in de post-header) en de naam en sleutel voor het opslagaccount voor blobopslag opgeven. U [Postman](search-get-started-postman.md) gebruiken om HTTP-aanvragen naar Azure Cognitive Search te verzenden.

Kopieer de volgende vier waarden naar kladblok, zodat u ze in een aanvraag plakken:

+ Naam azure Cognitive Search-service
+ Azure Cognitive Search-beheersleutel
+ Naam van het Microsoft Azure Storage-account
+ Azure-opslagaccountsleutel

U vindt deze waarden in de portal:

1. Kopieer de URL van de zoekservice op de portalpagina's voor Azure Cognitive Search vanaf de pagina Overzicht.

2. Klik in het linkernavigatiedeelvenster op **Toetsen** en kopieer vervolgens de primaire of secundaire toets (deze zijn gelijkwaardig).

3. Schakel over naar de portalpagina's voor uw opslagaccount. Klik in het linkernavigatiedeelvenster onder **Instellingen**op **Toegangssleutels**. Deze pagina bevat zowel de accountnaam als de sleutel. Kopieer de naam van het opslagaccount en een van de sleutels naar Kladblok.

### <a name="2---create-a-data-source"></a>2 - Een gegevensbron maken

Deze stap biedt gegevensbronverbindingsgegevens die door de indexeerder worden gebruikt. De gegevensbron is een benoemd object in Azure Cognitive Search dat de verbindingsgegevens blijft bestaan. Het gegevensbrontype `azureblob`bepaalt welke gegevensextractiegedrag door de indexeerder wordt aangeroepen. 

Vervang geldige waarden voor servicenaam, beheerderssleutel, opslagaccount en tijdelijke aanduidingen voor accountsleutels.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Een doelzoekindex maken 

Indexers zijn gekoppeld aan een indexschema. Als u de API gebruikt (in plaats van de portal), stelt u vooraf een index voor, zodat u deze opgeven op de indexerbewerking.

De index slaat doorzoekbare inhoud op in Azure Cognitive Search. Als u een index wilt maken, geeft u een schema op dat de velden in een document, kenmerken en andere constructies opgeeft die de zoekervaring vormen. Als u een index maakt met dezelfde veldnamen en gegevenstypen als de bron, komt de indexer overeen met de bron- en doelvelden, zodat u het werk bespaart dat u de velden expliciet moet toewijzen.

In het volgende voorbeeld wordt een [indexaanvraag maken](https://docs.microsoft.com/rest/api/searchservice/create-index) weergegeven. De index heeft een `content` doorzoekbaar veld om de uit blobs geëxtraheerde tekst op te slaan:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - De indexeren configureren en uitvoeren

Net als bij een index en een gegevensbron is en indexer ook een benoemd object dat u maakt en hergebruikt op een Azure Cognitive Search-service. Een volledig opgegeven verzoek om een indexeerder te maken, ziet er als volgt uit:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Indexerconfiguratie bevindt zich in het hoofd van het verzoek. Hiervoor is een gegevensbron en een lege doelindex vereist die al bestaat in Azure Cognitive Search. 

Planning en parameters zijn optioneel. Als u ze weglaat, wordt de `json` indexer onmiddellijk uitgevoerd, met behulp van de parsing-modus.

Deze specifieke indexer bevat geen veldtoewijzingen. Binnen de definitie van indexer u **veldtoewijzingen** weglaten als de eigenschappen van het bron-JSON-document overeenkomen met de velden van uw doelzoekindex. 


### <a name="rest-example"></a>VOORBEELD VAN REST

Deze sectie is een samenvatting van alle aanvragen die worden gebruikt voor het maken van objecten. Zie de vorige secties in dit artikel voor een bespreking van onderdelen.

### <a name="data-source-request"></a>Gegevensbronaanvraag

Alle indexers hebben een gegevensbronobject nodig dat verbindingsgegevens voorziet van bestaande gegevens. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Indexaanvraag

Alle indexers hebben een doelindex nodig die de gegevens ontvangt. De hoofdtekst van de aanvraag definieert het indexschema, bestaande uit velden, dat wordt toegeschreven aan het gewenste gedrag in een doorzoekbare index. Deze index moet leeg zijn wanneer u de indexer uitvoert. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Indexerverzoek

Deze aanvraag toont een volledig gespecificeerde indexer. Het bevat veldtoewijzingen, die in eerdere voorbeelden zijn weggelaten. Bedenk dat 'planning', 'parameters' en 'fieldMappings' optioneel zijn zolang er een standaardinstelling beschikbaar is. Het weglaten van "schema" zorgt ervoor dat de indexer onmiddellijk wordt uitgevoerd. Het weglaten van "parsingMode" zorgt ervoor dat de index de standaardinstelling "json" gebruikt.

Als u de indexerer maakt op Azure Cognitive Search, worden gegevens geïmporteerd. Het loopt onmiddellijk, en daarna op een schema als je er een hebt verstrekt.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK gebruiken

De .NET SDK heeft volledige pariteit met de REST API. We raden u aan de vorige REST API-sectie te bekijken om concepten, workflow en vereisten te leren. U vervolgens verwijzen naar de volgende .NET API-referentiedocumentatie om een JSON-indexer in beheerde code te implementeren.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Parsing modi

JSON-blobs kunnen meerdere vormen aannemen. De parameter **parsingMode** op de JSON-indexer bepaalt hoe JSON-blobinhoud wordt geparseerd en gestructureerd in een Azure Cognitive Search-index:

| parsingMode | Beschrijving |
|-------------|-------------|
| `json`  | Indexeer elke blob als één document. Dit is de standaardinstelling. |
| `jsonArray` | Kies deze modus als uw blobs bestaan uit JSON-arrays en u elk element van de array nodig hebt om een afzonderlijk document te worden in Azure Cognitive Search. |
|`jsonLines` | Kies deze modus als uw blobs bestaan uit meerdere JSON-entiteiten, die worden gescheiden door een nieuwe regel, en u elke entiteit nodig hebt om een afzonderlijk document te worden in Azure Cognitive Search. |

U een document zien als één item in de zoekresultaten. Als u wilt dat elk element in de array wordt weergegeven `jsonArray` in `jsonLines` zoekresultaten als een onafhankelijk item, gebruikt u de optie of naar gelang van het geval.

Binnen de definitie van indexer u optioneel [veldtoewijzingen](search-indexer-field-mappings.md) gebruiken om te kiezen welke eigenschappen van het bron-JSON-document worden gebruikt om uw doelzoekindex in te vullen. Als `jsonArray` de array als eigenschap op een lager niveau bestaat, u voor de parsingmodus een documenthoofdmap instellen die aangeeft waar de array zich in de blob bevindt.

> [!IMPORTANT]
> Wanneer u `json` `jsonArray` de `jsonLines` modus of parsing- of parsing-modus gebruikt, gaat Azure Cognitive Search ervan uit dat alle blobs in uw gegevensbron JSON bevatten. Als u een mix van JSON- en niet-JSON-blobs in dezelfde gegevensbron moet ondersteunen, laat het ons dan weten op [onze UserVoice-site.](https://feedback.azure.com/forums/263029-azure-search)


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Parse enkele JSON blobs

[Azure Cognitive Search blob indexer](search-howto-indexing-azure-blob-storage.md) parses JSON blobs standaard als één stuk tekst. Vaak wilt u de structuur van uw JSON-documenten behouden. Stel dat u het volgende JSON-document hebt in Azure Blob-opslag:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

De blob-indexer ontleden het JSON-document in één Azure Cognitive Search-document. De indexer laadt een index door 'tekst', 'datum gepubliceerd' en 'tags' van de bron af te stellen op identiek benoemde en getypte doelindexvelden.

Zoals opgemerkt, zijn veldtoewijzingen niet vereist. Als u een index met 'tekst', datumgepubliceerd en 'tags'-velden krijgt, kan de blob-indexer de juiste toewijzing afleiden zonder dat er een veldtoewijzing in de aanvraag aanwezig is.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parse JSON-arrays

U ook de JSON-arrayoptie gebruiken. Deze optie is handig wanneer blobs een *array met goed gevormde JSON-objecten*bevatten en u wilt dat elk element een afzonderlijk Azure Cognitive Search-document wordt. Als u bijvoorbeeld de volgende JSON-blob ziet, u uw Azure Cognitive Search-index vullen met drie afzonderlijke documenten, elk met 'id' en 'tekst'.For example, following the following JSON blob, you can populate your Azure Cognitive Search index with three separate documents, each with id and text fields.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Voor een JSON-array moet de indexerdefinitie er hetzelfde uitzien als het volgende voorbeeld. De parameter parsingMode geeft `jsonArray` de parser aan. Het opgeven van de juiste parser en het hebben van de juiste gegevensinvoer zijn de enige twee matrixspecifieke vereisten voor het indexeren van JSON-blobs.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Nogmaals, merk op dat veldtoewijzingen kunnen worden weggelaten. Uitgaande van een index met identiek de naam "id" en "tekst" velden, kan de blob indexer afleiden van de juiste toewijzing zonder een expliciete veld toewijzing lijst.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Parse geneste arrays
Voor JSON-arrays met geneste elementen `documentRoot` u een structuur op multiniveau opgeven. Als uw blobs er bijvoorbeeld als volgt uitzien:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Gebruik deze configuratie om de array `level2` in de eigenschap te indexeren:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Parse blobs gescheiden door nieuwe lijnen

Als uw blob meerdere JSON-entiteiten bevat die zijn gescheiden door een nieuwe regel en u wilt dat elk element een afzonderlijk Azure Cognitive Search-document wordt, u kiezen voor de optie JSON-regels. Bijvoorbeeld, gezien de volgende blob (waar er drie verschillende JSON entiteiten), u uw Azure Cognitive Search index vullen met drie afzonderlijke documenten, elk met id en tekst velden.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Voor JSON-regels moet de indexerdefinitie op het volgende voorbeeld lijken. De parameter parsingMode geeft `jsonLines` de parser aan. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Nogmaals, merk op dat veldtoewijzingen kunnen `jsonArray` worden weggelaten, vergelijkbaar met de parsing-modus.

## <a name="add-field-mappings"></a>Veldtoewijzingen toevoegen

Wanneer bron- en doelvelden niet perfect zijn uitgelijnd, u een veldtoewijzingssectie definiëren in de aanvraaginstantie voor expliciete veld-naar-veldkoppelingen.

Azure Cognitive Search kan momenteel geen willekeurige JSON-documenten rechtstreeks indexeren omdat het alleen primitieve gegevenstypen, tekenreeksarrays en GeoJSON-punten ondersteunt. U echter **veldtoewijzingen** gebruiken om onderdelen van uw JSON-document te kiezen en ze naar velden op het hoogste niveau van het zoekdocument te 'tillen'. Zie [Veldtoewijzingen in Azure Cognitive Search-indexeerders](search-indexer-field-mappings.md)voor meer informatie over veldtoewijzingen.

Onze voorbeeld JSON-document opnieuw bekijken:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Ga uit van een zoekindex `Edm.String`met `date` de `Edm.DateTimeOffset`volgende `tags` velden: `Collection(Edm.String)` `text` van type , van type en van type . Let op de discrepantie tussen "datePublished" in de bron en `date` het veld in de index. Als u uw JSON in de gewenste vorm wilt toewijzen, gebruikt u de volgende veldtoewijzingen:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

De namen van het bronveld in de toewijzingen worden opgegeven met de [JSON](https://tools.ietf.org/html/rfc6901) Pointer-notatie. U begint met een slash naar voren om te verwijzen naar de hoofdmap van uw JSON-document en kiest vervolgens de gewenste eigenschap (op willekeurig nestniveau) met behulp van voorwaarts slash-gescheiden pad.

U ook verwijzen naar afzonderlijke arrayelementen met behulp van een op nul gebaseerde index. Als u bijvoorbeeld het eerste element van de array 'tags' uit het bovenstaande voorbeeld wilt kiezen, gebruikt u een veldtoewijzing als volgt:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Als een bronveldnaam in een veldtoewijzingspad verwijst naar een eigenschap die niet bestaat in JSON, wordt die toewijzing zonder een fout overgeslagen. Dit wordt gedaan zodat we documenten kunnen ondersteunen met een ander schema (wat een veelvoorkomende use case is). Omdat er geen validatie is, moet u ervoor zorgen dat typefouten in uw veldtoewijzingsspecificatie worden voorkomen.
>
>

## <a name="see-also"></a>Zie ook

+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Azure Blob-opslag indexeren met Azure Cognitive Search](search-howto-index-json-blobs.md)
+ [CSV-blobs indexeren met Azure Cognitive Search blobindexer](search-howto-index-csv-blobs.md)
+ [Zelfstudie: Semi-gestructureerde gegevens zoeken vanuit Azure Blob-opslag](search-semi-structured-data.md)
