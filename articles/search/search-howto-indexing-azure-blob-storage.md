---
title: Zoeken via Azure Blob-opslaginhoud
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren van Azure Blob Storage en het extraheren van tekst uit documenten met Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5df1198e6681431738f886eb7c3ad549936eab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067647"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Documenten indexeren in Azure Blob Storage met Azure Cognitive Search

In dit artikel ziet u hoe u Azure Cognitive Search gebruiken om documenten (zoals PDF's, Microsoft Office-documenten en verschillende andere algemene indelingen) die zijn opgeslagen in Azure Blob-opslag te indexeren. Eerst wordt de basisbeginselen van het instellen en configureren van een blob-indexer uitgelegd. Vervolgens biedt het een diepere verkenning van gedrag en scenario's die u waarschijnlijk zult tegenkomen.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Ondersteunde documentindelingen
De blob-indexer kan tekst extraheren uit de volgende documentindelingen:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Blob-indexering instellen
U een Azure Blob Storage-indexer instellen met:

* [Azure-portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Sommige functies (bijvoorbeeld veldtoewijzingen) zijn nog niet beschikbaar in de portal en moeten programmatisch worden gebruikt.
>

Hier tonen we de stroom met behulp van de REST API.

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken
Een gegevensbron geeft aan welke gegevens moeten worden geïndexeerd, welke referenties nodig zijn om toegang te krijgen tot de gegevens en beleid om wijzigingen in de gegevens efficiënt te identificeren (nieuwe, gewijzigde of verwijderde rijen). Een gegevensbron kan door meerdere indexeerders in dezelfde zoekservice worden gebruikt.

Voor blobindexering moet de gegevensbron de volgende vereiste eigenschappen hebben:

* **naam** is de unieke naam van de gegevensbron binnen uw zoekservice.
* **type** moet `azureblob`zijn .
* **referenties** biedt de tekenreeks voor `credentials.connectionString` opslagaccountverbinding als parameter. Zie [Hieronder credentiala opgeven](#Credentials) voor meer informatie.
* **container** geeft een container op in uw opslagaccount. Standaard kunnen alle blobs in de container worden opgehaald. Als u alleen blobs in een bepaalde virtuele map wilt indexeren, u die map opgeven met behulp van de optionele **queryparameter.**

Ga als lid van het nieuws naar een andere bron:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Zie [Gegevensbron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source)voor meer informatie over de API Gegevensbron maken.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Referenties opgeven ####

U de referenties voor de blobcontainer op een van de volgende manieren verstrekken:

- **Tekenreeks voor opslagaccountmeting**voor volledige toegang : `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` u de verbindingstekenreeks van de Azure-portal ophalen door te navigeren naar het opslagaccountblad > Instellingen > Sleutels (voor Klassieke opslagaccounts) of Instellingen > Access-sleutels (voor Azure Resource Manager-opslagaccounts).
- **SAS-verbindingstekenreeks (Shared Access** Signature `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` Signature) (Storage Account Shared Access Signature): De SAS moet de lijst- en leesmachtigingen voor containers en objecten (blobs in dit geval) hebben.
-  **Handtekening gedeelde**toegang `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` voor containers : De SAS moet de lijst en leesmachtigingen op de container hebben.

Zie Handtekeningen voor gedeelde toegang [gebruiken](../storage/common/storage-dotnet-shared-access-signature-part-1.md)voor meer informatie over gedeelde toegangshandtekeningen voor opslag.

> [!NOTE]
> Als u SAS-referenties gebruikt, moet u de gegevensbronreferenties periodiek bijwerken met vernieuwde handtekeningen om te voorkomen dat deze verlopen. Als SAS-referenties verlopen, mislukt de indexer met `Credentials provided in the connection string are invalid or have expired.`een foutbericht dat vergelijkbaar is met .  

### <a name="step-2-create-an-index"></a>Stap 2: een index maken
De index geeft de velden in een document, kenmerken en andere constructies op die de zoekervaring vormen.

U als u een index `content` maken met een doorzoekbaar veld om de tekst die uit blobs is geëxtraheerd, op te slaan:   

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

Zie [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over het maken van indexen

### <a name="step-3-create-an-indexer"></a>Stap 3: Een indexer maken
Een indexer verbindt een gegevensbron met een doelzoekindex en biedt een planning om de gegevensvernieuwing te automatiseren.

Zodra de index en gegevensbron zijn gemaakt, bent u klaar om de indexer te maken:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Deze indexer wordt elke twee uur uitgevoerd (het schemainterval is ingesteld op 'PT2H'). Als u elke 30 minuten een indexer wilt uitvoeren, stelt u het interval in op "PT30M". Het kortste ondersteunde interval is 5 minuten. De planning is optioneel - als deze wordt weggelaten, wordt een indexer slechts één keer uitgevoerd wanneer deze is gemaakt. U echter op elk gewenst moment een indexer op aanvraag uitvoeren.   

Ga voor meer informatie over de API Indexer maken naar [Indexer maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Zie [Indexeerders voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van indexerschema's.

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Hoe Azure Cognitive Search blobs indexeert

Afhankelijk van de [indexerconfiguratie](#PartsOfBlobToIndex)kan de blob-indexer alleen opslagmetagegevens indexeren (handig als u alleen om de metagegevens geeft en de inhoud van blobs niet hoeft te indexeren), opslag- en inhoudsmetagegevens of metagegevens en tekstuele inhoud. Standaard haalt de indexer zowel metagegevens als inhoud uit.

> [!NOTE]
> Blobs met gestructureerde inhoud zoals JSON of CSV worden standaard geïndexeerd als één stuk tekst. Zie [JSON-blobs indexeren](search-howto-index-json-blobs.md) en [CSV-blobs indexeren](search-howto-index-csv-blobs.md) voor meer informatie als u JSON- en CSV-blobs op een gestructureerde manier wilt indexeren.
>
> Een samengesteld of ingesloten document (zoals een ZIP-archief of een Word-document met ingesloten Outlook-e-mail met bijlagen) wordt ook geïndexeerd als één document.

* De tekstuele inhoud van het document wordt geëxtraheerd in een tekenreeksveld met de naam `content`.

> [!NOTE]
> Azure Cognitive Search beperkt hoeveel tekst het eruit haalt, afhankelijk van de prijscategorie: 32.000 tekens voor gratis laag, 64.000 voor Basic, 4 miljoen voor Standard, 8 miljoen voor Standard S2 en 16 miljoen voor Standard S3. Een waarschuwing is opgenomen in het antwoord op de indexerstatus voor afgekapte documenten.  

* Eventuele door de gebruiker opgegeven metagegevens die op de blob aanwezig zijn, worden letterlijk geëxtraheerd. Houd er rekening mee dat hiervoor een veld moet worden gedefinieerd in de index met dezelfde naam als de metagegevenstoets van de blob. Als `Sensitivity` uw blob bijvoorbeeld een metagegevenssleutel heeft met waarde, `High`moet u een veld definiëren met de naam `Sensitivity` in uw zoekindex en wordt het gevuld met de waarde `High`.
* Standaardeigenschappen voor blobmetagegevens worden geëxtraheerd in de volgende velden:

  * **metagegevensopslagnaam\_\_** (Edm.String) - de bestandsnaam van de blob. Als u bijvoorbeeld een blob /my-container/my-folder/submap/cv.pdf hebt, is `resume.pdf`de waarde van dit veld .
  * **opslagpad metmetagegevens\_(Edm.String) - de volledige URI van de blob, inclusief het opslagaccount.\_** Bijvoorbeeld: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **inhoudstype\_metagegevens\_\_(Edm.String)** - inhoudstype zoals opgegeven door de code die u hebt gebruikt om de blob te uploaden. Bijvoorbeeld `application/octet-stream`.
  * **\_metagegevensopslag\_\_laatst gewijzigd** (Edm.DateTimeOffset) - laatst gewijzigde tijdstempel voor de blob. Azure Cognitive Search gebruikt deze tijdstempel om gewijzigde blobs te identificeren, om te voorkomen dat alles opnieuw wordt geïndexeerd na de eerste indexering.
  * **grootte\_\_van de opslagvan metagegevens** (Edm.Int64) - blobgrootte in bytes.
  * **metagegevens\_\_opslag inhoud\_md5** (Edm.String) - MD5 hash van de blob inhoud, indien beschikbaar.
  * **metadata\_\_storage\_sas token** (Edm.String) - Een tijdelijk SAS-token dat kan worden gebruikt door [aangepaste vaardigheden](cognitive-search-custom-skill-interface.md) om toegang te krijgen tot de blob. Dit token mag niet worden opgeslagen voor later gebruik, omdat het mogelijk verloopt.

* Metagegevenseigenschappen die specifiek zijn voor elke documentindeling worden geëxtraheerd in de velden die [hier](#ContentSpecificMetadata)worden vermeld .

U hoeft geen velden te definiëren voor alle bovenstaande eigenschappen in uw zoekindex - leg gewoon de eigenschappen vast die u nodig hebt voor uw toepassing.

> [!NOTE]
> Vaak verschillen de veldnamen in uw bestaande index van de veldnamen die tijdens documentextractie zijn gegenereerd. U veldtoewijzingen gebruiken om de **eigenschapsnamen** die azure cognitive search biedt, toe te geven aan de veldnamen in uw zoekindex. Hieronder ziet u een voorbeeld van veldtoewijzingen die worden gebruikt.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Documentsleutels en veldtoewijzingen definiëren
In Azure Cognitive Search identificeert de documentsleutel op unieke wijze een document. Elke zoekindex moet precies één sleutelveld van het type Edm.String hebben. Het sleutelveld is vereist voor elk document dat aan de index wordt toegevoegd (het is eigenlijk het enige vereiste veld).  

U moet zorgvuldig overwegen welk geëxtraheerd veld moet toewijzen aan het sleutelveld voor uw index. De kandidaten zijn:

* **metagegevens\_\_opslag naam** - dit kan een handige kandidaat, maar merk op dat 1) de namen misschien niet uniek zijn, omdat je blobs met dezelfde naam in verschillende mappen, en 2) de naam kan bevatten tekens die ongeldig zijn in documentsleutels, zoals streepjes. U ongeldige tekens `base64Encode` afhandelen met de [veldtoewijzingsfunctie](search-indexer-field-mappings.md#base64EncodeFunction) - als u dit doet, moet u documentsleutels coderen wanneer u deze doorgeeft in API-aanroepen, zoals Opzoeken. (In .NET u bijvoorbeeld de [UrlTokenEncode-methode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) voor dat doel gebruiken).
* **metagegevensopslagpad\_\_** - het gebruik van het volledige `/` pad zorgt voor uniciteit, maar het pad bevat zeker tekens die ongeldig zijn [in een documentsleutel](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Zoals hierboven, heb je de mogelijkheid om `base64Encode` de toetsen te coderen met behulp van de [functie](search-indexer-field-mappings.md#base64EncodeFunction).
* Als geen van de bovenstaande opties voor u werkt, u een aangepaste eigenschap met metagegevens toevoegen aan de blobs. Deze optie vereist echter wel het uploadproces van uw blob om die eigenschap metagegevens toe te voegen aan alle blobs. Aangezien de sleutel een vereiste eigenschap is, worden alle blobs die die eigenschap niet hebben, niet geïndexeerd.

> [!IMPORTANT]
> Als er geen expliciete toewijzing is voor het sleutelveld in `metadata_storage_path` de index, gebruikt Azure Cognitive Search automatisch als de sleutel en basis-64 codeert de sleutelwaarden (de tweede optie hierboven).
>
>

In dit voorbeeld kiezen we `metadata_storage_name` het veld als documentsleutel. Laten we er ook van uitgaan `key` dat uw `fileSize` index een sleutelveld heeft met de naam en een veld voor het opslaan van de documentgrootte. Als u dingen naar wens wilt verbinden, geeft u de volgende veldtoewijzingen op bij het maken of bijwerken van uw indexer:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Om dit alles samen te brengen, u als volgt veldtoewijzingen toevoegen en base-64-codering van sleutels inschakelen voor een bestaande indexeerder:

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
> Zie [dit artikel](search-indexer-field-mappings.md)voor meer informatie over veldtoewijzingen.
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Bepalen welke blobs worden geïndexeerd
U bepalen welke blobs worden geïndexeerd en welke worden overgeslagen.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Alleen de blobs indexeren met specifieke bestandsextensies
U alleen de blobs indexeren met `indexedFileNameExtensions` de bestandsnaamextensies die u opgeeft met behulp van de parameter indexerconfiguratie. De waarde is een tekenreeks met een door komma's gescheiden lijst met bestandsextensies (met een voorloopstip). Bijvoorbeeld om alleen de . PDF en . DOCX blobs, doe dit:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Blobs uitsluiten met specifieke bestandsextensies
U blobs met specifieke bestandsnaamextensies uitsluiten `excludedFileNameExtensions` van indexering met behulp van de configuratieparameter. De waarde is een tekenreeks met een door komma's gescheiden lijst met bestandsextensies (met een voorloopstip). Bijvoorbeeld om alle blobs te indexeren, behalve die met de . PNG en . JPEG-extensies, doe dit:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Als `indexedFileNameExtensions` beide `excludedFileNameExtensions` en parameters aanwezig zijn, `indexedFileNameExtensions`kijkt Azure `excludedFileNameExtensions`Cognitive Search eerst naar , dan naar . Dit betekent dat als dezelfde bestandsextensie in beide lijsten aanwezig is, deze wordt uitgesloten van indexering.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Bepalen welke delen van de blob zijn geïndexeerd

U bepalen welke delen van de `dataToExtract` blobs worden geïndexeerd met behulp van de configuratieparameter. Er kunnen de volgende waarden worden aan:

* `storageMetadata`- hiermee geeft u aan dat alleen de [standaardblob-eigenschappen en door de gebruiker opgegeven metagegevens](../storage/blobs/storage-properties-metadata.md) worden geïndexeerd.
* `allMetadata`- hiermee geeft u aan dat opslagmetagegevens en de [specifieke metagegevens](#ContentSpecificMetadata) van het inhoudstype die uit de blob-inhoud worden geëxtraheerd, worden geïndexeerd.
* `contentAndMetadata`- hiermee geeft u aan dat alle metagegevens en tekstuele inhoud die uit de blob zijn geëxtraheerd, worden geïndexeerd. Dit is de standaardwaarde.

Als u bijvoorbeeld alleen de opslagmetagegevens wilt indexeren, gebruikt u het als:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Blob-metagegevens gebruiken om te bepalen hoe blobs worden geïndexeerd

De hierboven beschreven configuratieparameters zijn van toepassing op alle blobs. Soms wilt u bepalen hoe *afzonderlijke blobs* worden geïndexeerd. U dit doen door de volgende eigenschappen en waarden voor blobmetagegevens toe te voegen:

| Naam van eigenschap | Waarde van eigenschap | Uitleg |
| --- | --- | --- |
| AzureSearch_Skip |"Waar" |Hiermee instrueert u de blob-indexer om de blob volledig over te slaan. Er wordt niet geprobeerd metagegevens of inhoudsextractie. Dit is handig wanneer een bepaalde blob herhaaldelijk mislukt en het indexeringsproces onderbreekt. |
| AzureSearch_SkipContent |"Waar" |Dit is `"dataToExtract" : "allMetadata"` gelijk aan de [hierboven](#PartsOfBlobToIndex) beschreven instelling op een bepaalde blob. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Omgaan met fouten

Standaard stopt de blob-indexer zodra deze een blob tegenkomt met een niet-ondersteund inhoudstype (bijvoorbeeld een afbeelding). U de `excludedFileNameExtensions` parameter natuurlijk gebruiken om bepaalde inhoudstypen over te slaan. Het kan echter nodig zijn om blobs te indexeren zonder alle mogelijke inhoudstypen van tevoren te kennen. Als u wilt doorgaan met indexeren wanneer een `failOnUnsupportedContentType` niet-ondersteund inhoudstype wordt aangetroffen, stelt u de configuratieparameter in op: `false`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Voor sommige blobs kan Azure Cognitive Search het inhoudstype niet bepalen of kan het geen document van anderszins ondersteund inhoudstype verwerken. Als u deze foutmodus `failOnUnprocessableDocument` wilt negeren, stelt u de configuratieparameter in op false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Cognitive Search beperkt de grootte van blobs die zijn geïndexeerd. Deze limieten zijn gedocumenteerd in [Servicelimieten in Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) Oversized blobs worden standaard als fouten behandeld. U echter nog steeds opslagmetagegevens van `indexStorageMetadataOnlyForOversizedDocuments` oversized blobs indexeren als u de configuratieparameter instelt op true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

U ook doorgaan met indexeren als er fouten optreden op elk punt van verwerking, tijdens het ontwijs en ontken van blobs of tijdens het toevoegen van documenten aan een index. Als u een bepaald aantal `maxFailedItems` fouten `maxFailedItemsPerBatch` wilt negeren, stelt u de parameters en configuratieparameters in op de gewenste waarden. Bijvoorbeeld:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Detectie van incrementele indexering en verwijdering

Wanneer u een blob-indexer instelt die volgens een planning wordt uitgevoerd, worden alleen `LastModified` de gewijzigde blobs opnieuw geïndexeerd, zoals bepaald door de tijdstempel van de blob.

> [!NOTE]
> U hoeft geen wijzigingsdetectiebeleid op te geven: incrementele indexering is automatisch voor u ingeschakeld.

Als u het verwijderen van documenten wilt ondersteunen, gebruikt u een "soft delete"-benadering. Als u de blobs helemaal verwijdert, worden overeenkomstige documenten niet uit de zoekindex verwijderd.

Er zijn twee manieren om de soft delete-aanpak te implementeren. Beide worden hieronder beschreven.

### <a name="native-blob-soft-delete-preview"></a>Native blob soft delete (preview)

> [!IMPORTANT]
> Ondersteuning voor native blob soft delete is in preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](https://docs.microsoft.com/azure/search/search-api-preview) biedt deze functie. Er is momenteel geen portal of .NET SDK-ondersteuning.

> [!NOTE]
> Bij het gebruik van het native blob soft delete-beleid moeten de documentsleutels voor de documenten in uw index een blobeigenschap of blob-metagegevens zijn.

In deze methode gebruikt u de [native blob soft delete-functie](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) die wordt aangeboden door Azure Blob-opslag. Als native blob soft delete is ingeschakeld in uw opslagaccount, heeft uw gegevensbron een native soft delete-beleidsset en vindt de indexer een blob die is overgezet naar een zachte verwijderde status, verwijdert de indexer dat document uit de index. Het native blob soft delete-beleid wordt niet ondersteund bij het indexeren van blobs uit Azure Data Lake Storage Gen2.

Voer de volgende stappen uit:
1. Native [soft delete inschakelen voor Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). We raden u aan het bewaarbeleid in te stellen op een waarde die veel hoger is dan uw indexerintervalschema. Op deze manier als er een probleem is met de indexerof als u een groot aantal documenten moet indexeren, is er voldoende tijd voor de indexer om uiteindelijk de zachte verwijderde blobs te verwerken. Azure Cognitive Search-indexeerders verwijderen een document alleen uit de index als het de blob verwerkt terwijl het in een zachte verwijderde status is.
1. Configureer een native blob soft deletion detection policy op de gegevensbron. Hieronder kunt u een voorbeeld bekijken. Aangezien deze functie in preview is, moet u de PREVIEW REST API gebruiken.
1. Voer de indexeren uit of stel de indexer in om op een planning te draaien. Wanneer de indexer de blob uitvoert en verwerkt, wordt het document uit de index verwijderd.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>Onverwijderde blobs opnieuw indexeren

Als u een blob verwijdert uit Azure Blob-opslag met native soft delete ingeschakeld in uw opslagaccount, wordt de blob overgezet naar een zachte verwijderde status, zodat u die blob binnen de bewaarperiode verwijderen. Wanneer een Azure Cognitive Search-gegevensbron een native blob soft delete-beleid heeft en de indexer een zachte verwijderde blob verwerkt, wordt dat document uit de index verwijderd. Als die blob later wordt verwijderd, zal de indexer die blob niet altijd opnieuw indexeren. Dit komt omdat de indexer bepaalt welke blobs `LastModified` moet indexeren op basis van de tijdstempel van de blob. Wanneer een zachte verwijderde blob niet `LastModified` wordt verwijderd, wordt de tijdstempel niet bijgewerkt, `LastModified` dus als de indexer blobs met tijdstempels al recenter heeft verwerkt dan de niet-verwijderde blob, wordt de niet-verwijderde blob niet opnieuw geïndexeerd. Als u ervoor wilt zorgen dat een niet-verwijderde blob opnieuw wordt `LastModified` geïndexeerd, moet u de tijdstempel van de blob bijwerken. Een manier om dit te doen is door het opslaan van de metadata van die blob. U hoeft de metagegevens niet te wijzigen, maar het `LastModified` opnieuw opslaan van de metagegevens wordt de tijdstempel van de blob bijgewerkt, zodat de indexer weet dat deze blob opnieuw moet worden geïndexeerd.

### <a name="soft-delete-using-custom-metadata"></a>Soft delete met aangepaste metagegevens

Bij deze methode gebruikt u de metagegevens van een blob om aan te geven wanneer een document uit de zoekindex moet worden verwijderd.

Voer de volgende stappen uit:

1. Voeg een aangepast sleutelsleutelsleutelpaar met ametten toe aan de blob om aan Azure Cognitive Search aan te geven dat deze op logisch wijze wordt verwijderd.
1. Een beveiligingscontrolebeleid voor zachte verwijderingskolom configureren op de gegevensbron. Hieronder kunt u een voorbeeld bekijken.
1. Zodra de indexer de blob heeft verwerkt en het document uit de index heeft verwijderd, u de blob voor Azure Blob-opslag verwijderen.

In het volgende beleid wordt bijvoorbeeld rekening gehouden met een `IsDeleted` blob `true`die moet worden verwijderd als deze een eigenschap met metagegevens heeft met de waarde:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }

#### <a name="reindexing-undeleted-blobs"></a>Onverwijderde blobs opnieuw indexeren

Als u een beleid voor het detecteren van een zachte kolom op uw gegevensbron instelt, de aangepaste metagegevens toevoegt aan een blob met de markeringswaarde en vervolgens de indexer uitvoert, verwijdert de indexer dat document uit de index. Als u dat document opnieuw wilt indexeren, wijzigt u gewoon de waarde van de metagegevens voor soft delete voor die blob en voert u de indexer opnieuw uit.

## <a name="indexing-large-datasets"></a>Grote gegevenssets indexeren

Het indexeren van blobs kan een tijdrovend proces zijn. In gevallen waarin u miljoenen blobs moet indexeren, u de indexering versnellen door uw gegevens te partitioneren en meerdere indexeerders te gebruiken om de gegevens parallel te verwerken. U dit als volgt instellen:

- Uw gegevens in meerdere blobcontainers of virtuele mappen verdelen
- Stel verschillende Azure Cognitive Search-gegevensbronnen in, één per container of map. Als u een blobmap `query` wilt aanwijzen, gebruikt u de parameter:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Maak een bijbehorende indexer voor elke gegevensbron. Alle indexeerders kunnen naar dezelfde doelzoekindex wijzen.  

- Eén zoekeenheid in uw service kan op elk gewenst moment één indexer uitvoeren. Het maken van meerdere indexers zoals hierboven beschreven is alleen nuttig als ze daadwerkelijk parallel draaien. Als u meerdere indexeerders parallel wilt uitvoeren, schaalt u uw zoekservice uit door een passend aantal partities en replica's te maken. Als uw zoekservice bijvoorbeeld 6 zoekeenheden heeft (bijvoorbeeld 2 partities x 3 replica's), kunnen 6 indexers tegelijkertijd worden uitgevoerd, wat resulteert in een verzesvoudiging van de indexeringsdoorvoer. Zie [Resourceniveaus schalen voor query' s en indexering van workloads in Azure Cognitive Search](search-capacity-planning.md)voor meer informatie over schalen en capaciteitsplanning.

## <a name="indexing-documents-along-with-related-data"></a>Indexering van documenten samen met gerelateerde gegevens

U documenten uit meerdere bronnen in uw index assembleren. U bijvoorbeeld tekst van blobs samenvoegen met andere metagegevens die zijn opgeslagen in Cosmos DB. U de push-indexerings-API zelfs samen met verschillende indexeerders gebruiken om zoekdocumenten uit meerdere onderdelen op te bouwen. 

Om dit te laten werken, moeten alle indexeerders en andere componenten het eens worden over de documentsleutel. Zie [Meerdere Azure-gegevensbronnen indexeren](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources)voor meer informatie over dit onderwerp. Zie dit externe artikel documenten combineren [met andere gegevens in Azure Cognitive Search](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)voor een gedetailleerde doorloop.

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Platte tekst indexeren 

Als al uw blobs platte tekst in dezelfde codering bevatten, u de indexeringsprestaties aanzienlijk verbeteren met behulp van **de tekstparsing-modus**. Als u de tekstparsingmodus wilt gebruiken, stelt u de `parsingMode` eigenschap configuration in op: `text`

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Standaard wordt `UTF-8` de codering aangenomen. Als u een andere codering `encoding` wilt opgeven, gebruikt u de eigenschap configuration: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Eigenschappen van inhoudstypespecifieke metagegevens
In de volgende tabel wordt een overzicht van de verwerking die voor elke documentindeling is uitgevoerd en worden de metagegevenseigenschappen beschreven die zijn geëxtraheerd door Azure Cognitive Search.

| Documentformaat/ inhoudstype | Eigenschappen van specifieke metagegevens van inhoudstype | Verwerkingsgegevens |
| --- | --- | --- |
| HTML (tekst/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-markering strippen en tekst extraheren |
| PDF (toepassing/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Tekst extraheren, inclusief ingesloten documenten (met uitzondering van afbeeldingen) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extraheren, inclusief ingesloten documenten |
| DOC (toepassing/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extraheren, inclusief ingesloten documenten |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extraheren, inclusief ingesloten documenten |
| WORD XML (application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML-markering strippen en tekst extraheren |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML-markering strippen en tekst extraheren |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extraheren, inclusief ingesloten documenten |
| XLS (applicatie/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extraheren, inclusief ingesloten documenten |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extraheren, inclusief ingesloten documenten |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extraheren, inclusief ingesloten documenten |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extraheren, inclusief ingesloten documenten |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extraheren, inclusief ingesloten documenten |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Tekst extraheren, inclusief bijlagen. `metadata_message_to_email`, `metadata_message_cc_email` `metadata_message_bcc_email` en zijn tekenreeksverzamelingen, de rest van de velden zijn tekenreeksen.|
| ODT (toepassing/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extraheren, inclusief ingesloten documenten |
| ODS (applicatie/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extraheren, inclusief ingesloten documenten |
| ODP (applicatie/vnd.oasis.opendocument.presentatie) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Tekst extraheren, inclusief ingesloten documenten |
| ZIP (toepassing/zip) |`metadata_content_type` |Tekst extraheren uit alle documenten in het archief |
| GZ (toepassing/gzip) |`metadata_content_type` |Tekst extraheren uit alle documenten in het archief |
| EPUB (applicatie/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Tekst extraheren uit alle documenten in het archief |
| XML (toepassing/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML-markering strippen en tekst extraheren |
| JSON (toepassing/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Tekst extraheren<br/>OPMERKING: Als u meerdere documentvelden uit een JSON-blob moet extraheren, raadpleegt u [JSON-blobs indexeren](search-howto-index-json-blobs.md) voor meer informatie |
| EML (bericht/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Tekst extraheren, inclusief bijlagen |
| RTF (toepassing/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Tekst extraheren|
| Platte tekst (tekst/vlak) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Tekst extraheren|


## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u functieverzoeken of ideeën voor verbeteringen hebt, laat het ons dan weten op onze [UserVoice-site.](https://feedback.azure.com/forums/263029-azure-search/)
