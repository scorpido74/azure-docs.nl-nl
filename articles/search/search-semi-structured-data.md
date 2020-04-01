---
title: 'Zelfstudie: Semi-gestructureerde gegevens indexeren in JSON-blobs'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren en doorzoeken van semi-gestructureerde Azure JSON-blobs met Azure Cognitive Search REST API's en Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: ce3b3839319de38020b968ff8db1ee6713b29c47
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269979"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Zelfstudie: JSON-blobs indexeren vanuit Azure Storage met REST

Azure Cognitive Search kan JSON-documenten en -arrays indexeren in Azure blob-opslag met behulp van een [indexer](search-indexer-overview.md) die weet hoe u semi-gestructureerde gegevens moet lezen. Semi-gestructureerde gegevens bevatten labels of markeringen die inhoud in de gegevens scheiden. Het verdeelt het verschil tussen ongestructureerde gegevens, die volledig moeten worden geïndexeerd, en formeel gestructureerde gegevens die zich houden aan een gegevensmodel, zoals een relationeel databaseschema, dat per veld kan worden geïndexeerd.

In deze zelfstudie worden postbode- en [zoek-API's](https://docs.microsoft.com/rest/api/searchservice/) gebruikt om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een Azure Cognitive Search-gegevensbron configureren voor een Azure blob-container
> * Een Azure Cognitive Search-index maken om doorzoekbare inhoud te bevatten
> * Een indexeren configureren en uitvoeren om de container te lezen en doorzoekbare inhoud uit Azure blob-opslag te extraheren
> * De index doorzoeken die u zojuist hebt gemaakt

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Postman bureaublad-app](https://www.getpostman.com/)
+ [Een](search-create-service-portal.md) [bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) maken of zoeken 

> [!Note]
> U gebruik maken van de gratis service voor deze tutorial. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerders en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u ruimte hebt op uw service om de nieuwe bronnen te accepteren.

## <a name="download-files"></a>Bestanden downloaden

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) bevat de gegevens die in deze zelfstudie worden gebruikt. Download en rits dit bestand uit naar een eigen map. Gegevens zijn afkomstig van [clinicaltrials.gov,](https://clinicaltrials.gov/ct2/results)geconverteerd naar JSON voor deze zelfstudie.

## <a name="1---create-services"></a>1 - Services maken

Deze zelfstudie maakt gebruik van Azure Cognitive Search voor indexering en query's en Azure Blob-opslag om de gegevens te verstrekken. 

Maak indien mogelijk zowel in dezelfde regio als in de resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan uw Azure Storage-account zich in elke regio bevinden.

### <a name="start-with-azure-storage"></a>Beginnen met Azure Storage

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en klik op **+ Resource maken.**

1. Zoek naar *een opslagaccount* en selecteer het opslagaccountaanbod van Microsoft.

   ![Opslagaccount maken](media/cognitive-search-tutorial-blob/storage-account.png "Opslagaccount maken")

1. Op het tabblad Basisbeginselen zijn de volgende items vereist. Accepteer de standaardinstellingen voor al het andere.

   + **Resourcegroep**. Selecteer een bestaande of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze collectief beheren.

   + **Naam van het opslagaccount**. Als u denkt dat u meerdere bronnen van hetzelfde type hebt, gebruikt u de naam om disambiguate te disambiguateop per type en regio, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vervalt bandbreedtekosten.

   + **Account soort**. Kies de standaardinstelling *StorageV2 (v2 voor algemeen gebruik).*

1. Klik **op Controleren + Maken** om de service te maken.

1. Zodra deze is gemaakt, klikt u op **Ga naar de resource om** de pagina Overzicht te openen.

1. Klik **op Blobs-service.**

1. [Maak een Blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) met voorbeeldgegevens. U het niveau voor openbare toegang instellen op een van de geldige waarden.

1. Nadat de container is gemaakt, opent u deze en selecteert **u Uploaden** op de opdrachtbalk.

   ![Uploaden op de opdrachtbalk](media/search-semi-structured-data/upload-command-bar.png "Uploaden op de opdrachtbalk")

1. Navigeer naar de map met de voorbeeldbestanden. Selecteer ze allemaal en klik op **Uploaden.**

   ![Bestanden uploaden](media/search-semi-structured-data/clinicalupload.png "Bestanden uploaden")

Nadat de upload is voltooid, worden de bestanden weergegeven in hun eigen submap in de gegevenscontainer.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De volgende bron is Azure Cognitive Search, die u [maken in de portal.](search-create-service-portal.md) U de gratis laag gebruiken om deze walkthrough te voltooien. 

Neem, net als bij Azure Blob-opslag, even de tijd om de toegangssleutel te verzamelen. Verderop, wanneer u begint met het structureren van aanvragen, moet u het eindpunt en de api-sleutel voor beheerders verstrekken die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eindpunt en toegangssleutel downloaden](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel downloaden")

Voor alle aanvragen is een api-sleutel vereist voor elk verzoek dat naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-postman"></a>2 - Postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met deze tool, raadpleegt u [API's verkennen](search-get-started-postman.md)voor Azure Cognitive Search REST met behulp van Postbode .

De aanvraagmethoden voor elke oproep in deze zelfstudie zijn **POST** en **GET.** U doet drie API-aanroepen naar uw zoekservice om een gegevensbron, een index en een indexeerder te maken. De gegevensbron bevat een verwijzing naar uw opslagaccount en uw JSON-gegevens. Uw zoekservice maakt de verbinding tijdens het laden van de gegevens.

Stel in Kopteksten 'Inhoudstype' in `application/json` op en stel u in op `api-key` de api-toets van de beheer-api van uw Azure Cognitive Search-service. Zodra u de headers hebt ingesteld, u ze gebruiken voor elk verzoek in deze oefening.

  ![URL en koptekst voor postbodeaanvragen](media/search-get-started-postman/postman-url.png "URL en koptekst voor postbodeaanvragen")

URI's moeten een api-versie opgeven en elke aanroep moet een **201 Gemaakt retourneren.** De algemeen beschikbare api-versie voor het `2019-05-06`gebruik van JSON-arrays is .

## <a name="3---create-a-data-source"></a>3 - Een gegevensbron maken

Met [de API Gegevensbron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source) wordt een Azure Cognitive Search-object gemaakt dat aangeeft welke gegevens moeten worden geïndexeerd.

1. Stel het eindpunt van `https://[service name].search.windows.net/datasources?api-version=2019-05-06`deze oproep in op . Vervang `[service name]` door de naam van uw zoekservice. 

1. Kopieer de volgende JSON naar de aanvraaginstantie.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Vervang de verbindingstekenreeks door een geldige tekenreeks voor uw account.

1. Vervang '[blobcontainernaam]' door de container die u voor de voorbeeldgegevens hebt gemaakt. 

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

## <a name="4---create-an-index"></a>4 - Een index maken
    
De tweede aanroep is [Create Index API](https://docs.microsoft.com/rest/api/searchservice/create-index), het maken van een Azure Cognitive Search index die alle doorzoekbare gegevens opslaat. Een index geeft alle parameters en hun kenmerken op.

1. Stel het eindpunt van `https://[service name].search.windows.net/indexes?api-version=2019-05-06`deze oproep in op . Vervang `[service name]` door de naam van uw zoekservice.

1. Kopieer de volgende JSON naar de aanvraaginstantie.

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

## <a name="5---create-and-run-an-indexer"></a>5 - Een indexer maken en uitvoeren

Een indexer maakt verbinding met de gegevensbron, importeert gegevens in de doelzoekindex en biedt optioneel een planning om de gegevensvernieuwing te automatiseren. De REST API is [Indexer maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Stel de URI in `https://[service name].search.windows.net/indexers?api-version=2019-05-06`voor deze oproep op . Vervang `[service name]` door de naam van uw zoekservice.

1. Kopieer de volgende JSON naar de aanvraaginstantie.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Verzend de aanvraag. De aanvraag wordt onmiddellijk verwerkt. Wanneer het antwoord terugkomt, hebt u een index die volledig-tekst doorzoekbaar is. Het antwoord moet er als volgt uitzien:

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

## <a name="6---search-your-json-files"></a>6 - Uw JSON-bestanden doorzoeken

U beginnen met zoeken zodra het eerste document is geladen.

1. Verander het werkwoord in **GET**.

1. Stel de URI in `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2019-05-06&$count=true`voor deze oproep op . Vervang `[service name]` door de naam van uw zoekservice.

1. Verzend de aanvraag. Dit is een niet-gespecificeerde zoekopdracht met volledige tekst die alle velden retourneert die zijn gemarkeerd als opvraagbaar in de index, samen met een documenttelling. Het antwoord moet er als volgt uitzien:

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

1. Voeg `$select` de parameter query toe om `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2019-05-06&$count=true`de resultaten te beperken tot minder velden: .  Voor deze query komen 100 documenten overeen, maar standaard retourneert Azure Cognitive Search slechts 50 in de resultaten.

   ![Geparameteriseerde query](media/search-semi-structured-data/lastquery.png "Geparamteriseerde query")

1. Een voorbeeld van complexere `$filter=MinimumAge ge 30 and MaximumAge lt 75`query's zou zijn , die alleen resultaten retourneert wanneer de parameters MinimumAge groter is dan of gelijk is aan 30 en MaximumAge lager is dan 75. Vervang `$select` de expressie `$filter` door de expressie.

   ![Semi-gestructureerde zoekopdracht](media/search-semi-structured-data/metadatashort.png)

U ook logische operatoren (en, of niet) en vergelijkingsoperatoren (eq, ne, gt, lt, ge, le) gebruiken. Tekenreeksvergelijkingen zijn hoofdlettergevoelig. Zie Een eenvoudige query [maken](search-query-simple-examples.md)voor meer informatie en voorbeelden.

> [!NOTE]
> De parameter `$filter` werkt alleen met metagegevens die bij het maken van de index zijn gemarkeerd als filterbaar.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele ontwikkelingsfase is de meest praktische benadering voor ontwerpiteratie het verwijderen van de objecten uit Azure Cognitive Search en uw code in staat te stellen ze opnieuw op te bouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U de portal gebruiken om indexen, indexeerders en gegevensbronnen te verwijderen. Of gebruik **DELETE** en geef URL's op aan elk object. Met de volgende opdracht wordt een indexeerder verwijderd.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling Alle resources of Resourcegroepen in het linkernavigatiedeelvenster.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basisprincipes van Azure Blob-indexering, bekijken we de indexerconfiguratie voor JSON-blobs in Azure Storage.

> [!div class="nextstepaction"]
> [Json blob-indexering configureren](search-howto-index-json-blobs.md)