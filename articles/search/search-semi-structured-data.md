---
title: 'Zelf studie: semi-gestructureerde gegevens in JSON-blobs indexeren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren en doorzoeken van semi-gestructureerde Azure JSON-blobs met behulp van Azure Cognitive Search REST Api's en postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: ce3b3839319de38020b968ff8db1ee6713b29c47
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269979"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Zelf studie: JSON-blobs indexeren van Azure Storage met REST

Azure Cognitive Search kan JSON-documenten en-matrices indexeren in Azure Blob-opslag met behulp van een [Indexeer functie](search-indexer-overview.md) die het lezen van semi-gestructureerde gegevens kent. Semi-gestructureerde gegevens bevatten labels of markeringen die inhoud in de gegevens scheiden. Het verschil tussen ongestructureerde gegevens, dat volledig kan worden geïndexeerd, en formeel gestructureerde gegevens die voldoen aan een gegevens model, zoals een relationeel database schema, wordt gesplitst, dat per veld kan worden geïndexeerd.

In deze zelf studie wordt gebruikgemaakt van Postman en de [Zoek-rest-api's](https://docs.microsoft.com/rest/api/searchservice/) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een Azure Cognitive Search-gegevens bron configureren voor een Azure Blob-container
> * Een Azure Cognitive Search-index maken die Doorzoek bare inhoud bevat
> * Een Indexeer functie configureren en uitvoeren om de container te lezen en Doorzoek bare inhoud uit Azure Blob-opslag te halen
> * De index doorzoeken die u zojuist hebt gemaakt

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Postman bureaublad-app](https://www.getpostman.com/)
+ [Een bestaande zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [maken](search-create-service-portal.md) of zoeken 

> [!Note]
> U kunt de gratis service voor deze zelf studie gebruiken. Een gratis zoek service beperkt u tot drie indexen, drie Indexeer functies en drie gegevens bronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u over voldoende ruimte beschikt om de nieuwe resources te accepteren.

## <a name="download-files"></a>Bestanden downloaden

[Clinical-Trials-JSON. zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) bevat de gegevens die in deze zelf studie worden gebruikt. Down load en pak dit bestand uit naar een eigen map. De gegevens zijn afkomstig uit [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), GECONVERTEERD naar JSON voor deze zelf studie.

## <a name="1---create-services"></a>1-services maken

In deze zelf studie maakt gebruik van Azure Cognitive Search voor indexering en query's en Azure Blob-opslag om de gegevens op te geven. 

Maak, indien mogelijk, beide in dezelfde regio en resource groep voor nabijheid en beheer baarheid. In de praktijk kan uw Azure Storage-account zich in een wille keurige regio bevinden.

### <a name="start-with-azure-storage"></a>Beginnen met Azure Storage

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/) en klik op **+ resource maken**.

1. Zoek naar het *opslag account* en selecteer het opslag account van micro soft.

   ![Opslag account maken](media/cognitive-search-tutorial-blob/storage-account.png "Opslag account maken")

1. Op het tabblad basis beginselen zijn de volgende items vereist. Accepteer de standaard waarden voor alle andere.

   + **Resourcegroep**. Selecteer een bestaande naam of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze gezamenlijk kunt beheren.

   + **Naam van opslag account**. Als u denkt dat u mogelijk meerdere resources van hetzelfde type hebt, gebruikt u de naam van dubbel zinnigheid per type en regio, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vernietigt bandbreedte kosten.

   + **Soort account**. Kies de standaard *StorageV2 (algemeen gebruik v2)* .

1. Klik op **beoordeling + maken** om de service te maken.

1. Zodra de app is gemaakt, klikt u op **Ga naar de resource** om de pagina overzicht te openen.

1. Klik op **blobs** -service.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om voorbeeld gegevens te bevatten. U kunt het niveau van open bare toegang instellen op een van de geldige waarden.

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk.

   ![Uploaden op de opdracht balk](media/search-semi-structured-data/upload-command-bar.png "Uploaden op de opdracht balk")

1. Navigeer naar de map met de voorbeeld bestanden. Selecteer alles en klik vervolgens op **uploaden**.

   ![Bestanden uploaden](media/search-semi-structured-data/clinicalupload.png "Bestanden uploaden")

Nadat de upload is voltooid, worden de bestanden weergegeven in hun eigen submap in de gegevenscontainer.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De volgende resource is Azure Cognitive Search, die u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om deze procedure te volt ooien. 

Net als bij Azure Blob-opslag neemt het even de tijd om de toegangs sleutel te verzamelen. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eind punt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-postman"></a>2-postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met dit hulp programma, raadpleegt u [Azure COGNITIVE Search rest-Api's verkennen met behulp van Postman](search-get-started-postman.md).

De aanvraag methoden voor elke aanroep in deze zelf studie zijn **post** en **Get**. U maakt drie API-aanroepen naar uw zoek service voor het maken van een gegevens bron, een index en een Indexeer functie. De gegevensbron bevat een verwijzing naar uw opslagaccount en uw JSON-gegevens. Uw zoekservice maakt de verbinding tijdens het laden van de gegevens.

Stel in headers ' content-type ' in op `application/json` en stel `api-key` in op de beheer-API-sleutel van uw Azure Cognitive Search-service. Wanneer u de koppen hebt ingesteld, kunt u deze gebruiken voor elke aanvraag in deze oefening.

  ![URL en header van Postman-aanvraag](media/search-get-started-postman/postman-url.png "URL en header van Postman-aanvraag")

Uri's moeten een API-versie opgeven en elke aanroep moet een **201-gemaakt**retour neren. De algemeen beschik bare API-versie voor het gebruik van JSON-matrices is `2019-05-06`.

## <a name="3---create-a-data-source"></a>3: een gegevens bron maken

Met de [Create Data Source-API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) wordt een Azure Cognitive Search-object gemaakt waarmee wordt opgegeven welke gegevens moeten worden geïndexeerd.

1. Stel het eind punt van deze aanroep in op `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Vervang `[service name]` door de naam van uw zoekservice. 

1. Kopieer de volgende JSON in de hoofd tekst van de aanvraag.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Vervang het connection string door een geldige teken reeks voor uw account.

1. Vervang ' [BLOB-container naam] ' door de container die u hebt gemaakt voor de voorbeeld gegevens. 

1. Verzend de aanvraag. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4-een index maken
    
De tweede aanroep is [Create Index API](https://docs.microsoft.com/rest/api/searchservice/create-index), waarmee een Azure Cognitive search-index wordt gemaakt waarmee alle Doorzoek bare gegevens worden opgeslagen. Een index geeft alle parameters en hun kenmerken op.

1. Stel het eind punt van deze aanroep in op `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Vervang `[service name]` door de naam van uw zoekservice.

1. Kopieer de volgende JSON in de hoofd tekst van de aanvraag.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Verzend de aanvraag. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5-een Indexeer functie maken en uitvoeren

Een Indexeer functie maakt verbinding met de gegevens bron, importeert gegevens in de doel zoek index en biedt optioneel een schema voor het automatiseren van de gegevens vernieuwing. De REST API is [Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Stel de URI in voor deze aanroep van `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Vervang `[service name]` door de naam van uw zoekservice.

1. Kopieer de volgende JSON in de hoofd tekst van de aanvraag.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Verzend de aanvraag. De aanvraag wordt onmiddellijk verwerkt. Wanneer het antwoord terugkomt, hebt u een index die in volledige tekst kan worden doorzocht. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6: uw JSON-bestanden doorzoeken

U kunt beginnen met zoeken zodra het eerste document is geladen.

1. Wijzig de term in **Get**.

1. Stel de URI in voor deze aanroep van `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2019-05-06&$count=true`. Vervang `[service name]` door de naam van uw zoekservice.

1. Verzend de aanvraag. Dit is een niet-opgegeven zoek opdracht voor volledige tekst die alle velden retourneert die in de index zijn gemarkeerd als ophalen, samen met het aantal documenten. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Voeg de para meter `$select` query toe om de resultaten te beperken tot minder velden: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2019-05-06&$count=true`.  Voor deze query komen 100 documenten overeen, maar standaard retourneert Azure Cognitive Search alleen 50 in de resultaten.

   ![Query met para meters](media/search-semi-structured-data/lastquery.png "Paramterized-query")

1. Een voor beeld van een complexere query bevat `$filter=MinimumAge ge 30 and MaximumAge lt 75`, die alleen resultaten retourneert waarbij de para meters mini maal groter zijn dan of gelijk is aan 30 en maximum aantal is kleiner dan 75. Vervang de `$select` expressie door de expressie `$filter`.

   ![Semi-gestructureerde zoekopdracht](media/search-semi-structured-data/metadatashort.png)

U kunt ook logische Opera tors (en, of niet) en vergelijkings operatoren (EQ, ne, gt, lt, ge, Le) gebruiken. Tekenreeksvergelijkingen zijn hoofdlettergevoelig. Zie [een eenvoudige query maken](search-query-simple-examples.md)voor meer informatie en voor beelden.

> [!NOTE]
> De parameter `$filter` werkt alleen met metagegevens die bij het maken van de index zijn gemarkeerd als filterbaar.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele stadia van de ontwikkeling kunt u het beste de objecten uit Azure Cognitive Search verwijderen en uw code zo instellen dat deze opnieuw worden opgebouwd. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U kunt de portal gebruiken om indexen, Indexeer functies en gegevens bronnen te verwijderen. Of gebruik **verwijderen** en geef url's op voor elk object. Met de volgende opdracht wordt een Indexeer functie verwijderd.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling alle resources of resource groepen in het navigatie deel venster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basis principes van Azure Blob-indexering, gaan we de Indexeer functie van JSON-blobs in Azure Storage eens nader bekijken.

> [!div class="nextstepaction"]
> [Het indexeren van JSON-blobs configureren](search-howto-index-json-blobs.md)