---
title: Een kennis archief maken met behulp van REST-Azure Search
description: Gebruik de REST API en postman om een Azure Search Knowledge Store te maken voor persistentie van verrijkingen van een cognitieve Zoek pijplijn.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: b67f0cf60d279c7bc52b4114d29c37847f5c57f1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244470"
---
# <a name="create-an-azure-search-knowledge-store-by-using-rest"></a>Een Azure Search Knowledge Store maken met behulp van REST

De functie van het kennis archief in Azure Search persistente uitvoer van een AI-verrijkings pijplijn voor latere analyses of andere downstream-verwerking. Een AI-verrijkte pijp lijn accepteert afbeeldings bestanden of ongestructureerde tekst bestanden, indexeert deze met behulp van Azure Search, maakt AI-verrijkingen van Azure Cognitive Services (zoals afbeeldings analyse en natuurlijke taal verwerking) en slaat de resultaten vervolgens op in een kennis opslaan in Azure Storage. U kunt hulpprogram ma's als Power BI of Storage Explorer in de Azure Portal gebruiken om het kennis archief te verkennen.

In dit artikel gebruikt u de REST API-interface om AI-verrijkingen op te nemen, te indexeren en toe te passen op een aantal functionerings gesprekken met hotels. De Hotel beoordelingen worden geïmporteerd in Azure Blob-opslag. De resultaten worden opgeslagen als een kennis archief in azure-tabel opslag.

Nadat u het kennis archief hebt gemaakt, kunt u leren hoe u toegang krijgt tot het kennis archief met behulp van [Storage Explorer](knowledge-store-view-storage-explorer.md) of [Power bi](knowledge-store-connect-power-bi.md).

## <a name="create-services"></a>Services maken

Maak de volgende services:

- Maak een [Azure Search-service](search-create-service-portal.md) of [Zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

- Maak een [Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om de voorbeeld gegevens en het kennis archief op te slaan. Uw opslag account moet dezelfde locatie (zoals vs-West) voor uw Azure Search-service gebruiken. De waarde voor **account soort** moet **StorageV2 (algemeen gebruik v2)** (standaard) of **opslag (algemeen gebruik v1)** zijn.

- Aanbevolen: down load de [postman desktop-app](https://www.getpostman.com/) voor het verzenden van aanvragen naar Azure Search. U kunt de REST API gebruiken met elk hulp programma dat kan werken met HTTP-aanvragen en-antwoorden. Postman is een goede keuze voor het verkennen van REST-Api's. In dit artikel wordt postman gebruikt. De [bron code](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) voor dit artikel bevat ook een postman-verzameling van aanvragen. 

## <a name="store-the-data"></a>De gegevens opslaan

Laadt het het CSV-bestand van het Hotel in Azure Blob-opslag, zodat het toegankelijk is voor een Azure Search indexer en door de AI-verrijkings pijplijn kan worden ingevoerd.

### <a name="create-a-blob-container-by-using-the-data"></a>Een BLOB-container maken met behulp van de gegevens

1. Down load de gegevens voor de [Hotel beoordeling](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) die zijn opgeslagen in een CSV-bestand (HotelReviews_Free. CSV). Deze gegevens zijn afkomstig van Kaggle.com en bevatten feedback van klanten over hotels.
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en ga naar uw Azure Storage-account.
1. Maak een [BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Als u de container wilt maken, selecteert u in het menu links voor uw opslag account de optie **blobs**en selecteert u vervolgens **container**.
1. Voer voor de nieuwe container **naam** **Hotels-Recensies**in.
1. Selecteer voor **openbaar toegangs niveau**een wille keurige waarde. We hebben de standaard waarde gebruikt.
1. Selecteer **OK** om de BLOB-container te maken.
1. Open de nieuwe **hotels: controle** container, selecteer **uploaden**en selecteer vervolgens het HotelReviews-Free. CSV-bestand dat u in de eerste stap hebt gedownload.

    ![De gegevens uploaden upload](media/knowledge-store-create-portal/upload-command-bar.png "de Hotel beoordelingen")

1. Selecteer **uploaden** om het CSV-bestand te importeren in Azure Blob-opslag. De nieuwe container wordt weer gegeven:

    ![De BLOB-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "de BLOB-container maken")

## <a name="configure-postman"></a>Postman configureren

Postman installeren en instellen.

### <a name="download-and-install-postman"></a>Postman downloaden en installeren

1. Down load de [bron code van de Postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selecteer **bestand** > **importeren** om de bron code te importeren in een postman.
1. Selecteer het tabblad **verzamelingen** en selecteer vervolgens de knop **...** (weglatings tekens).
1. Selecteer **Bewerken**. 
   
   ![Postman-app met navigatie](media/knowledge-store-create-rest/postman-edit-menu.png "Ga naar het menu bewerken in de Postman")
1. Selecteer in het dialoog venster **bewerken** het tabblad **variabelen** . 

Op het tabblad **variabelen** kunt u waarden toevoegen die na elke keer dat er wordt vervangen door een specifieke variabele tussen dubbele accolades wordt aangetroffen. Postman vervangt bijvoorbeeld het symbool `{{admin-key}}` door de huidige waarde die u hebt ingesteld voor `admin-key`. Postman maakt de vervanging in Url's, kopteksten, de hoofd tekst van de aanvraag, enzovoort. 

Als u de waarde voor `admin-key` wilt ophalen, gaat u naar de Azure Search-service en selecteert u het tabblad **sleutels** . Wijzig `search-service-name` en `storage-account-name` in de waarden die u hebt gekozen in [Services maken](#create-services). Stel `storage-connection-string` in met behulp van de waarde op het tabblad **toegangs sleutels** van het opslag account. U kunt de standaard waarde voor de andere waarden laten staan.

Het ![tabblad](media/knowledge-store-create-rest/postman-variables-window.png "het venster variabelen van") postman app postman


| Variabele    | Waar om het te krijgen |
|-------------|-----------------|
| `admin-key` | Op het tabblad **sleutels** van de Azure Search-service.  |
| `api-version` | Geef **een voor beeld van 2019-05-06**. |
| `datasource-name` | Als **Hotel laten zien-Recensies-Active Directory**. | 
| `indexer-name` | Verlof als **Hotel-beoordelingen-IXR**. | 
| `index-name` | Als **Hotel laten zien, beoordelingen-IX**. | 
| `search-service-name` | De hoofd naam van de Azure Search service. De URL is `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Verlof als **Hotel-beoordelingen-SS**. | 
| `storage-account-name` | De hoofd naam van het opslag account. | 
| `storage-connection-string` | Selecteer in het opslag account op het tabblad **toegangs sleutels** de waarde **key1** > -**verbindings reeks**. | 
| `storage-container-name` | Zorg ervoor dat u zich als **Hotel bekijkt**. | 

### <a name="review-the-request-collection-in-postman"></a>Bekijk de verzameling aanvragen in postman

Wanneer u een kennis archief maakt, moet u vier HTTP-aanvragen doen: 

- **De aanvraag voor het maken van de index plaatsen**: deze index bevat de gegevens die Azure Search gebruikt en die worden geretourneerd.
- **Post-aanvraag voor het maken van de gegevens bron**: deze gegevens bron verbindt het Azure Search gedrag met het opslag account van de gegevens en het kennis archief. 
- De **aanvraag voor het maken van de vaardig heden plaatsen**: de vaardig heden bevat de verrijkingen die worden toegepast op uw gegevens en de structuur van het kennis archief.
- **Put-aanvraag om de Indexeer functie te maken: als**de Indexeer functie wordt uitgevoerd, worden de gegevens gelezen, wordt de vaardig heden toegepast en worden de resultaten opgeslagen. U moet deze aanvraag als laatste uitvoeren.

De [bron code](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) bevat een postman-verzameling die de vier aanvragen heeft. Als u de aanvragen wilt uitgeven, selecteert u in postman het tabblad voor de aanvraag. Voeg vervolgens `api-key`-en `Content-Type`-aanvraag headers toe. Stel de waarde van `api-key` in op `{{admin-key}}`. Stel de waarde `Content-type` in op `application/json`. 

![Scherm opname van de interface van Postman voor kopteksten](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> U moet in al uw aanvragen `api-key`-en `Content-type`-headers instellen. Als in postman een variabele wordt herkend, wordt de variabele in oranje tekst weer gegeven, net als bij `{{admin-key}}` in de vorige scherm afbeelding. Als de variabele verkeerd is gespeld, wordt deze in een rode tekst weer gegeven.
>

## <a name="create-an-azure-search-index"></a>Een Azure Search-index maken

Maak een Azure Search-index voor de gegevens die u wilt doorzoeken, filteren en Toep assen van verbeteringen in. Maak de index door een PUT-aanvraag uit te geven aan `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman vervangt symbolen die tussen dubbele accolades staan (zoals `{{search-service-name}}`, `{{index-name}}` en `{{api-version}}`) met de waarden die u hebt ingesteld in [Configure postman](#configure-postman). Als u een ander hulp programma gebruikt om uw REST-opdrachten uit te voeren, moet u deze variabelen zelf vervangen.

Stel de structuur van uw Azure Search index in de hoofd tekst van de aanvraag in. Ga in postman naar het deel venster **hoofd tekst** van de aanvraag nadat u de `api-key` en de `Content-type`-headers hebt ingesteld. De volgende JSON moet worden weer geven. Als dat niet het geval is, selecteert u **Raw** > **JSON (Application/JSON)** en plakt u de volgende code als hoofd tekst:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Deze index definitie is een combi natie van gegevens die u aan de gebruiker wilt presen teren (de naam van het Hotel, de inhoud, de datum, de meta gegevens van de zoek opdracht en de AI-verbetering) (sentiment, de zinnen en de taal).

Selecteer **verzenden** om de put-aanvraag uit te geven. De status `201 - Created` wordt weer geven. Als er een andere status wordt weer gegeven, zoekt u in het deel venster **hoofd tekst** naar een JSON-antwoord met een fout bericht. 

## <a name="create-the-datasource"></a>De gegevens bron maken

Vervolgens verbindt u Azure Search met de Hotel gegevens die u hebt opgeslagen in [de gegevens opslaan](#store-the-data). Als u de gegevens bron wilt maken, stuurt u een POST-aanvraag naar `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. U moet de `api-key`-en `Content-Type`-headers instellen zoals eerder is beschreven. 

Ga in postman naar de aanvraag **gegevens bron maken** en vervolgens naar het deel venster **hoofd tekst** . U ziet de volgende code:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selecteer **verzenden** om de post-aanvraag uit te geven. 

## <a name="create-the-skillset"></a>De vaardig heden maken 

De volgende stap is het opgeven van de vaardig heden die de uitbrei dingen bevat die moeten worden toegepast en de kennis opslag waar de resultaten worden opgeslagen. Selecteer in postman het tabblad **vaardig heden maken** . Deze aanvraag verzendt een PUT naar `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Stel de kopteksten `api-key` en `Content-type` in zoals u eerder hebt gedaan. 

Er zijn twee grote objecten op het hoogste niveau: `skills` en `knowledgeStore`. Elk object binnen het `skills`-object is een verrijkings service. Elke verrijkings service heeft `inputs` en `outputs`. De `LanguageDetectionSkill` heeft een uitvoer `targetName` van `Language`. De waarde van dit knoop punt wordt door de meeste andere vaardig heden gebruikt als invoer. De bron is `document/Language`. De mogelijkheid om de uitvoer van het ene knoop punt te gebruiken als de invoer naar een andere, is nog steeds duidelijker in `ShaperSkill`, waarmee wordt aangegeven hoe de gegevens in de tabellen van het kennis archief worden opgeslagen.

Het `knowledge_store`-object maakt verbinding met het opslag account via de variabele `{{storage-connection-string}}` postman. `knowledge_store` bevat een set toewijzingen tussen het uitgebreide document en de tabellen en kolommen in het kennis archief. 

Als u de vaardig heden wilt genereren, selecteert u de knop **verzenden** in postman om de aanvraag in te stellen:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>De Indexeer functie maken

De laatste stap bestaat uit het maken van de Indexeer functie. De Indexeer functie leest de gegevens en activeert de vaardig heden. In postman selecteert u de aanvraag voor het maken van een **Indexeer functie** en bekijkt u de hoofd tekst. De definitie van de Indexeer functie verwijst naar verschillende andere resources die u al hebt gemaakt: de gegevens bron, de index en de vaardig heden. 

Het `parameters/configuration`-object bepaalt hoe de Indexeer functie de gegevens opneemt. In dit geval bevindt de invoer gegevens zich in één document met een koptekst regel en door komma's gescheiden waarden. De document sleutel is een unieke id voor het document. Voordat u code ring maakt, is de document sleutel de URL van het bron document. Ten slotte worden de uitvoer waarden van de vaardigheidset, zoals taal code, sentiment en sleutel zinnen, toegewezen aan hun locaties in het document. Hoewel er sprake is van een enkele waarde voor `Language`, wordt `Sentiment` toegepast op elk element in de matrix van `pages`. `Keyphrases` is een matrix die ook wordt toegepast op elk element in de matrix `pages`.

Nadat u de `api-key` en de `Content-type`-headers hebt ingesteld en bevestigt dat de hoofd tekst van de aanvraag vergelijkbaar is met de volgende bron code, selecteert u **verzenden** in postman. Postman verzendt een PUT-aanvraag naar `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Search maakt de Indexeer functie en voert deze uit. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>De indexeerfunctie uitvoeren 

Ga in het Azure Portal naar de **overzichts** pagina van de Azure Search-service. Selecteer het tabblad **Indexeer functies** en selecteer vervolgens **Hotels-Recensies-IXR**. Als de Indexeer functie nog niet is uitgevoerd, selecteert u **uitvoeren**. De Indexeer taak kan enkele waarschuwingen met betrekking tot taal herkenning veroorzaken. De gegevens bevatten enkele beoordelingen die zijn geschreven in talen die nog niet worden ondersteund door de cognitieve vaardig heden. 

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met behulp van Cognitive Services en de resultaten naar een kennis archief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

Zie dit overzicht voor meer informatie over het verkennen van dit kennis archief met behulp van Storage Explorer:

> [!div class="nextstepaction"]
> [Weer geven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie dit overzicht voor meer informatie over het verbinden van dit kennis archief met Power BI:

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Als u deze oefening wilt herhalen of een andere AI-verrijkings scenario wilt uitproberen, verwijdert u de **idxr** indexer. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller opnieuw ingesteld op nul.
