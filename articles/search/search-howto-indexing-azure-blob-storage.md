---
title: Zoeken in inhoud van Azure Blob-opslag
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren van Azure Blob Storage en het extra heren van tekst uit documenten met Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 1c2bac06f2526260fb290b63e5aa559a1e2337b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379562"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Documenten in Azure Blob Storage indexeren met Azure Cognitive Search

In dit artikel wordt beschreven hoe u Azure Cognitive Search gebruikt voor het indexeren van documenten (zoals Pdf's, Microsoft Office documenten en enkele andere algemene indelingen) die zijn opgeslagen in Azure Blob Storage. Eerst worden de basis principes uitgelegd van het instellen en configureren van een BLOB-indexer. Vervolgens biedt het een uitgebreidere onderzoek van gedrag en scenario's die u waarschijnlijk zult tegen komen.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Ondersteunde documentindelingen
De BLOB-indexer kan tekst uit de volgende document indelingen ophalen:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>BLOB-indexering instellen
U kunt een Azure Blob Storage Indexeer functie instellen met behulp van:

* [Azure-portal](https://ms.portal.azure.com)
* Azure Cognitive Search [rest API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Sommige functies (bijvoorbeeld veld Toewijzingen) zijn nog niet beschikbaar in de portal en moeten worden gebruikt via een programma.
>

Hier demonstreren we de stroom met behulp van de REST API.

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken
Een gegevens bron specificeert welke gegevens moeten worden geïndexeerd, de referenties die nodig zijn voor toegang tot de gegevens en het beleid om de wijzigingen in de gegevens (nieuwe, gewijzigde of verwijderde rijen) efficiënt te kunnen identificeren. Een gegevens bron kan door meerdere Indexeer functies in dezelfde zoek service worden gebruikt.

Voor BLOB-indexering moet de gegevens bron de volgende vereiste eigenschappen hebben:

* **naam** is de unieke naam van de gegevens bron in uw zoek service.
* het **type** moet `azureblob`zijn.
* **referenties** bieden het opslag account Connection String als de `credentials.connectionString`-para meter. Zie onderstaande [referenties opgeven](#Credentials) voor meer informatie.
* **container** bevat een container in uw opslag account. Standaard kunnen alle blobs in de container worden opgehaald. Als u alleen blobs wilt indexeren in een bepaalde virtuele map, kunt u die directory opgeven met behulp van de optionele **query** parameter.

Een gegevens bron maken:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Zie [Data Source maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source)voor meer informatie over het maken van data source-API.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Referenties opgeven ####

U kunt de referenties voor de BLOB-container op een van de volgende manieren opgeven:

- **Volledig Access Storage-account Connection String**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` u de Connection String van de Azure Portal kunt verkrijgen door te navigeren naar de Blade opslag account > Instellingen > sleutels (voor klassieke opslag accounts) of instellingen > toegangs sleutels (voor Azure Resource Manager opslag accounts).
- **Opslag account voor Shared Access Signature** (SAS) connection string: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` de SAS moet de lijst en lees machtigingen hebben voor containers en objecten (blobs in dit geval).
-  **Shared Access-hand tekening voor container**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` de sa's moeten de lijst en lees machtigingen voor de container hebben.

Zie [using Shared Access signatures](../storage/common/storage-dotnet-shared-access-signature-part-1.md)(Engelstalig) voor meer informatie over gedeelde toegangs handtekeningen voor opslag.

> [!NOTE]
> Als u SAS-referenties gebruikt, moet u de referenties van de gegevens bron regel matig bijwerken met de vernieuwde hand tekeningen om te voor komen dat ze verlopen. Als de SAS-referenties verlopen, mislukt de Indexeer functie met een fout bericht dat vergelijkbaar is met `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Stap 2: een index maken
De index specificeert de velden in een document, kenmerken en andere constructies die de zoek ervaring vormen.

Hier volgt een overzicht van het maken van een index met een Doorzoek bare `content` veld voor het opslaan van de tekst die uit blobs is geëxtraheerd:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Zie [index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="step-3-create-an-indexer"></a>Stap 3: een Indexeer functie maken
Een Indexeer functie verbindt een gegevens bron met een doel zoek index en biedt een planning voor het automatiseren van de gegevens vernieuwing.

Zodra de index en gegevens bron zijn gemaakt, kunt u de Indexeer functie nu maken:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Deze Indexeer functie wordt elke twee uur uitgevoerd (schema-interval is ingesteld op "PT2H"). Als u een Indexeer functie elke 30 minuten wilt uitvoeren, stelt u het interval in op ' PT30M '. Het kortste ondersteunde interval is 5 minuten. Het schema is optioneel: als u dit weglaat, wordt een Indexeer functie slechts eenmaal uitgevoerd wanneer deze wordt gemaakt. U kunt echter op elk gewenst moment een Indexeer functie op aanvraag uitvoeren.   

Bekijk [Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer)voor meer informatie over het maken van Indexeer functie-API.

Zie [Indexeer functies plannen voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van de planningen voor de Indexeer functie.

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Hoe Azure Cognitive Search indexen blobs

Afhankelijk van de configuratie van de [Indexeer](#PartsOfBlobToIndex)functie kan de BLOB-indexer alleen opslag meta gegevens indexeren (handig als u alleen de meta gegevens gebruikt en de inhoud van blobs niet hoeft te indexeren), meta gegevens van opslag en inhoud, of meta gegevens en tekst inhoud. Standaard haalt de Indexeer functie zowel de meta gegevens als de inhoud op.

> [!NOTE]
> Standaard worden blobs met gestructureerde inhoud, zoals JSON of CSV, geïndexeerd als één tekst segment. Zie [JSON-blobs indexeren](search-howto-index-json-blobs.md) en [CSV-blobs indexeren](search-howto-index-csv-blobs.md) voor meer informatie als u JSON-en CSV-blobs op een gestructureerde manier wilt indexeren.
>
> Een samengesteld of Inge sloten document (zoals een ZIP-archief of een Word-document met Inge sloten Outlook-e-mail met bijlagen) wordt ook als één document geïndexeerd.

* De tekst inhoud van het document wordt geëxtraheerd in een teken reeks veld met de naam `content`.

> [!NOTE]
> Azure Cognitive Search beperkt hoeveel tekst er wordt geëxtraheerd, afhankelijk van de prijs categorie: 32.000 tekens voor de gratis laag, 64.000 voor Basic, 4.000.000 voor Standard, 8.000.000 voor Standard S2 en 16.000.000 voor Standard S3. Er is een waarschuwing opgenomen in het indexeer programma status antwoord voor afgekapte documenten.  

* Door de gebruiker opgegeven eigenschappen van meta gegevens in de blob, indien aanwezig, worden geëxtraheerd Verbatim. Houd er rekening mee dat hiervoor een veld moet worden gedefinieerd in de index met dezelfde naam als de meta gegevens sleutel van de blob. Als uw BLOB bijvoorbeeld een meta gegevens sleutel bevat van `Sensitivity` met de waarde `High`, moet u een veld met de naam `Sensitivity` in uw zoek index definiëren. dit wordt ingevuld met de waarde `High`.
* Standaard eigenschappen van BLOB-meta gegevens worden in de volgende velden geëxtraheerd:

  * **meta gegevens\_storage\_name** (EDM. String): de bestands naam van de blob. Als u bijvoorbeeld een BLOB-/My-container/My-Folder/subfolder/resume.PDF hebt, is de waarde van dit veld `resume.pdf`.
  * **meta gegevens\_storage\_pad** (EDM. String): de volledige URI van de blob, met inbegrip van het opslag account. Bijvoorbeeld: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **meta gegevens\_opslag\_inhouds\_type** (EDM. String): type inhoud zoals opgegeven door de code die u hebt gebruikt voor het uploaden van de blob. Bijvoorbeeld `application/octet-stream`.
  * **meta gegevens\_storage\_laatst\_gewijzigd** (EDM. date time offset)-laatst gewijzigd tijds tempel voor de blob. Azure Cognitive Search gebruikt deze tijds tempel voor het identificeren van gewijzigde blobs, om te voor komen dat alles na de initiële indexering opnieuw moet worden geïndexeerd.
  * **meta gegevens\_opslag\_grootte** (EDM. Int64)-Blob-grootte in bytes.
  * **meta gegevens\_opslag\_inhoud\_MD5** (EDM. String)-MD5-hash van de blob-inhoud, indien beschikbaar.
  * **meta gegevens\_opslag\_sas\_token** (EDM. String): een tijdelijk SAS-token dat door [aangepaste vaardig heden](cognitive-search-custom-skill-interface.md) kan worden gebruikt om toegang te krijgen tot de blob. Dit token mag niet worden opgeslagen voor later gebruik omdat het kan verlopen.

* Eigenschappen van meta gegevens die specifiek zijn voor elke document indeling, worden uitgepakt in de velden die [hier](#ContentSpecificMetadata)worden weer gegeven.

U hoeft geen velden te definiëren voor alle bovenstaande eigenschappen in uw zoek index. u hoeft alleen de eigenschappen vast te leggen die u nodig hebt voor uw toepassing.

> [!NOTE]
> Vaak zijn de veld namen in uw bestaande index niet hetzelfde als de veld namen die zijn gegenereerd tijdens het ophalen van documenten. U kunt **veld Toewijzingen** gebruiken om de eigenschaps namen die door Azure Cognitive Search worden verschaft, toe te wijzen aan de veld namen in uw zoek index. Hieronder ziet u een voor beeld van het gebruik van veld toewijzingen.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Document sleutels en veld toewijzingen definiëren
In azure Cognitive Search is de document sleutel een unieke identificatie van een document. Elke zoek index moet precies één sleutel veld van het type EDM. string bevatten. Het sleutel veld is vereist voor elk document dat wordt toegevoegd aan de index (dit is eigenlijk het enige vereiste veld).  

U moet zorgvuldig overwegen welk geëxtraheerde veld moet worden toegewezen aan het sleutel veld voor uw index. De kandidaten zijn:

* **meta gegevens\_storage\_name** : dit kan een handige kandidaat zijn, maar houd er rekening mee dat 1) de namen mogelijk niet uniek zijn, omdat u blobs met dezelfde naam in verschillende mappen kunt hebben, en 2) de naam mag tekens bevatten die ongeldig zijn in document sleutels, zoals streepjes. U kunt met ongeldige tekens omgaan met de functie voor het toewijzen van `base64Encode` [velden](search-indexer-field-mappings.md#base64EncodeFunction) . Als u dit doet, moet u document sleutels coderen tijdens het door geven in API-aanroepen zoals lookup. (In .NET kunt u bijvoorbeeld de [UrlTokenEncode-methode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) voor dat doel gebruiken).
* **meta gegevens\_storage\_pad** : met het volledige pad zorgt u voor uniekheid, maar het pad bevat wel `/` tekens die [ongeldig zijn in een document sleutel](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Net als hierboven hebt u de mogelijkheid om de sleutels te coderen met behulp van de [functie](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.
* Als geen van de bovenstaande opties voor u werkt, kunt u een aangepaste meta gegevens eigenschap toevoegen aan de blobs. Deze optie vereist echter dat het proces voor het uploaden van blobs de meta gegevens eigenschap aan alle blobs toevoegt. Omdat de sleutel een vereiste eigenschap is, kunnen niet alle blobs die deze eigenschap hebben, worden geïndexeerd.

> [!IMPORTANT]
> Als er geen expliciete toewijzing is voor het sleutel veld in de index, gebruikt Azure Cognitive Search automatisch `metadata_storage_path` als de sleutel en base-64 sleutel waarden codeert (de tweede optie hierboven).
>
>

Voor dit voor beeld kiezen we het veld `metadata_storage_name` als document sleutel. Ook wordt ervan uitgegaan dat uw index een sleutel veld bevat met de naam `key` en een veld `fileSize` om de document grootte op te slaan. Geef de volgende veld toewijzingen op wanneer u een Indexeer functie maakt of bijwerkt om de gewenste items te maken of bij te werken:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Als u dit alles wilt doen, kunt u veld toewijzingen toevoegen en de base-64-code ring van sleutels voor een bestaande Indexeer functie inschakelen:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Zie [dit artikel](search-indexer-field-mappings.md)voor meer informatie over veld toewijzingen.
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Bepalen welke blobs worden geïndexeerd
U kunt bepalen welke blobs worden geïndexeerd en welke worden overgeslagen.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Alleen de blobs met specifieke bestands extensies indexeren
U kunt alleen de blobs indexeren met de bestandsnaam extensies die u opgeeft met behulp van de para meter `indexedFileNameExtensions` indexers configuratie. De waarde is een teken reeks met een door komma's gescheiden lijst met bestands extensies (met een voorloop punt). Als u bijvoorbeeld alleen de wilt indexeren. PDF en. DOCX-blobs, Ga als volgt te werk:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Blobs met specifieke bestands extensies uitsluiten
U kunt blobs met specifieke bestandsnaam extensies uitsluiten van indexering met behulp van de para meter `excludedFileNameExtensions` Configuration. De waarde is een teken reeks met een door komma's gescheiden lijst met bestands extensies (met een voorloop punt). Als u bijvoorbeeld alle blobs wilt indexeren, behalve die in de. PNG en. JPEG-extensies:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Als zowel `indexedFileNameExtensions` als `excludedFileNameExtensions`-para meters aanwezig zijn, zoekt Azure Cognitive Search eerst naar `indexedFileNameExtensions`en vervolgens op `excludedFileNameExtensions`. Dit betekent dat als dezelfde bestands extensie aanwezig is in beide lijsten, het indexeren wordt uitgesloten van indexering.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Bepalen welke delen van de BLOB worden geïndexeerd

U kunt bepalen welke delen van de blobs worden geïndexeerd met behulp van de para meter `dataToExtract` Configuration. Dit kan de volgende waarden hebben:

* `storageMetadata`: Hiermee geeft u op dat alleen de [standaard-BLOB-eigenschappen en door de gebruiker opgegeven meta gegevens](../storage/blobs/storage-properties-metadata.md) worden geïndexeerd.
* `allMetadata`-geeft aan dat de meta gegevens van de opslag en de [specifieke meta gegevens](#ContentSpecificMetadata) die zijn geëxtraheerd uit de blob-inhoud worden geïndexeerd.
* `contentAndMetadata`: Hiermee geeft u aan dat alle meta gegevens en tekstuele inhoud die uit de BLOB zijn geëxtraheerd, worden geïndexeerd. Dit is de standaardwaarde.

Als u bijvoorbeeld alleen de meta gegevens van de opslag wilt indexeren, gebruikt u:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB-meta gegevens gebruiken om te bepalen hoe blobs worden geïndexeerd

De configuratie parameters die hierboven worden beschreven, zijn van toepassing op alle blobs. Soms wilt u mogelijk bepalen hoe *afzonderlijke blobs* worden geïndexeerd. U kunt dit doen door de volgende eigenschappen en waarden van de BLOB-meta gegevens toe te voegen:

| Naam van eigenschap | Eigenschaps waarde | Uitleg |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Hiermee geeft u de BLOB-Indexeer functie de BLOB volledig overs Laan. Er is geen meta gegevens of extra inhoud opgehaald. Dit is handig wanneer een bepaalde BLOB herhaaldelijk mislukt en het indexerings proces onderbreekt. |
| AzureSearch_SkipContent |"true" |Dit is equivalent van `"dataToExtract" : "allMetadata"` instelling die [hierboven](#PartsOfBlobToIndex) is beschreven en die het bereik van een bepaalde BLOB bevat. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Omgaan met fouten

De BLOB-indexer stopt standaard zodra er een BLOB wordt aangetroffen met een niet-ondersteund inhouds type (bijvoorbeeld een afbeelding). U kunt natuurlijk gebruikmaken van de para meter `excludedFileNameExtensions` om bepaalde inhouds typen over te slaan. Het is echter mogelijk dat u blobs moet indexeren zonder alle mogelijke inhouds typen vooraf te weten. Als u wilt door gaan met indexeren wanneer er een niet-ondersteund inhouds type wordt aangetroffen, stelt u de para meter `failOnUnsupportedContentType` Configuration in op `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Voor sommige blobs kan Azure Cognitive Search het inhouds type niet bepalen of kan een document van een andere ondersteund inhouds type niet verwerken. Als u deze fout modus wilt negeren, stelt u de para meter `failOnUnprocessableDocument` Configuration in op False:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Cognitive Search beperkt de grootte van blobs die worden geïndexeerd. Deze limieten worden beschreven in de [service limieten in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). De grootte van blobs wordt standaard als fouten beschouwd. U kunt echter wel de meta gegevens van de opslag van overgrote blobs indexeren als u `indexStorageMetadataOnlyForOversizedDocuments` configuratie parameter instelt op True: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

U kunt ook door gaan met indexeren als er fouten optreden tijdens de verwerking, hetzij tijdens het parseren van blobs of tijdens het toevoegen van documenten aan een index. Als u een bepaald aantal fouten wilt negeren, stelt u de para meters `maxFailedItems` en `maxFailedItemsPerBatch` in op de gewenste waarden. Bijvoorbeeld:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Incrementele indexering en detectie van verwijderingen
Wanneer u een BLOB-Indexeer functie zo instelt dat deze volgens een planning wordt uitgevoerd, worden alleen de gewijzigde blobs opnieuw geïndexeerd, zoals wordt bepaald door de `LastModified` tijds tempel van de blob.

> [!NOTE]
> U hoeft geen detectie beleid voor wijzigingen op te geven: incrementele indexering is automatisch ingeschakeld voor u.

Als u het verwijderen van documenten wilt ondersteunen, gebruikt u de methode ' zacht verwijderen '. Als u de blobs naar rechts verwijdert, worden de bijbehorende documenten niet verwijderd uit de zoek index. Gebruik in plaats daarvan de volgende stappen:  

1. Voeg een aangepaste meta gegevens eigenschap toe aan de blob om aan te geven dat de Azure-Cognitive Search logisch is verwijderd
2. Een detectie beleid voor zacht verwijderen configureren op de gegevens bron
3. Zodra de Indexeer functie de BLOB heeft verwerkt (zoals weer gegeven door de API van de Indexeer functie), kunt u de BLOB fysiek verwijderen

Het volgende beleid beschouwt bijvoorbeeld een blob die moet worden verwijderd als deze een meta gegevens eigenschap heeft `IsDeleted` met de waarde `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Grote gegevens sets indexeren

Het indexeren van blobs kan een tijdrovend proces zijn. In gevallen waarin u miljoenen blobs hebt om te indexeren, kunt u het indexeren versnellen door uw gegevens te partitioneren en meerdere Indexeer functies te gebruiken om de gegevens parallel te verwerken. U kunt dit als volgt instellen:

- Uw gegevens partitioneren in meerdere BLOB-containers of virtuele mappen
- Stel meerdere Azure Cognitive Search-gegevens bronnen in, één per container of map. Als u wilt verwijzen naar een BLOB-map, gebruikt u de para meter `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Maak een bijbehorende Indexeer functie voor elke gegevens bron. Alle Indexeer functies kunnen verwijzen naar dezelfde doel zoek index.  

- Eén Zoek eenheid in uw service kan op elk gewenst moment één Indexeer functie uitvoeren. Het maken van meerdere Indexeer functies zoals hierboven wordt beschreven, is alleen nuttig als deze parallel worden uitgevoerd. Als u meerdere Indexeer functies parallel wilt uitvoeren, kunt u de zoek service uitschalen door een passend aantal partities en replica's te maken. Als uw zoek service bijvoorbeeld 6 Zoek eenheden heeft (bijvoorbeeld 2 partities x 3 replica's), kunnen er met 6 Indexeer functies gelijktijdig worden uitgevoerd, wat resulteert in een toename van zes vouwen in de door Voer van indexeren. Zie [resource niveaus schalen voor het uitvoeren van query's en het indexeren van werk belastingen in Azure Cognitive Search](search-capacity-planning.md)voor meer informatie over schalen en capaciteits planning.

## <a name="indexing-documents-along-with-related-data"></a>Documenten indexeren samen met gerelateerde gegevens

Mogelijk wilt u documenten uit meerdere bronnen in uw index assembleren. U kunt bijvoorbeeld tekst uit blobs samen voegen met andere meta gegevens die zijn opgeslagen in Cosmos DB. U kunt zelfs de push-indexerings-API samen met de verschillende Indexeer functies gebruiken om Zoek documenten uit meerdere delen op te bouwen. 

Om dit te laten werken, moeten alle Indexeer functies en andere onderdelen overeenkomen met de document sleutel. Zie [meerdere Azure-gegevens bronnen indexeren](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources)voor meer informatie over dit onderwerp. Zie dit externe artikel: [documenten combi neren met andere gegevens in Azure Cognitive Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)voor gedetailleerde instructies.

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Tekst zonder opmaak indexeren 

Als alle blobs onbewerkte tekst in dezelfde code ring bevatten, kunt u de index prestaties aanzienlijk verbeteren met behulp van de modus voor het **parseren van tekst**. Als u de modus voor het parseren van tekst wilt gebruiken, stelt u de eigenschap `parsingMode` Configuration in op `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

De `UTF-8` Encoding wordt standaard gebruikt. Als u een andere code ring wilt opgeven, gebruikt u de configuratie-eigenschap `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Inhouds type-specifieke meta gegevens eigenschappen
De volgende tabel geeft een overzicht van de verwerking die wordt uitgevoerd voor elke document indeling en beschrijft de eigenschappen van meta gegevens die zijn geëxtraheerd door Azure Cognitive Search.

| Document indeling/inhouds type | Eigenschappen van specifieke meta gegevens van inhouds type | Details verwerken |
| --- | --- | --- |
| HTML (tekst/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-opmaak verwijderen en tekst ophalen |
| PDF (toepassing/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten (met uitzonde ring van afbeeldingen) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| DOC (toepassing/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| DOCM (application/vnd. MS-Word. document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Strip XML-opmaak en extra heren tekst |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Strip XML-opmaak en extra heren tekst |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Tekst extra heren, inclusief bijlagen. `metadata_message_to_email`, `metadata_message_cc_email` en `metadata_message_bcc_email` zijn teken reeks verzamelingen, de rest van de velden zijn teken reeksen.|
| ODT (application/vnd. Oasis. open document. text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| ODS (application/vnd. Oasis. open document. spread sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| ODP (application/vnd. Oasis. open document. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Tekst extra heren, inclusief Inge sloten documenten |
| ZIP (toepassing/zip) |`metadata_content_type` |Tekst uit alle documenten in het archief extra heren |
| GZ (toepassing/gzip) |`metadata_content_type` |Tekst uit alle documenten in het archief extra heren |
| EPUB (toepassing/EPUB en zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Tekst uit alle documenten in het archief extra heren |
| XML (toepassing/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Strip XML-opmaak en extra heren tekst |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Tekst extraheren<br/>Opmerking: als u meerdere document velden van een JSON-BLOB wilt extra heren, raadpleegt u [JSON-blobs indexeren](search-howto-index-json-blobs.md) voor meer informatie |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Tekst extra heren, inclusief bijlagen |
| RTF (toepassing/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Tekst extraheren|
| Tekst zonder opmaak (tekst/normaal) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Tekst extraheren|


## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u functie aanvragen of ideeën voor verbeteringen hebt, laat het ons dan weten op onze [UserVoice-site](https://feedback.azure.com/forums/263029-azure-search/).
