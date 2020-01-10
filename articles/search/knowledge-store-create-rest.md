---
title: Een kennis archief maken (preview) met behulp van REST
titleSuffix: Azure Cognitive Search
description: Gebruik de REST API en postman om een Azure Cognitive Search Knowledge Store te maken voor persistentie van een AI-verrijkings pijplijn. Deze functie is momenteel beschikbaar als openbare preview-versie.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 7dd1f07d44bd3b71bb83becee5405cf5c100460c
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754084"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Maak een kennis archief met behulp van REST en postman

> [!IMPORTANT] 
> Het kennis archief is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt preview-functies. Er is momenteel beperkte ondersteuning voor portals en geen .NET SDK-ondersteuning.

Een kennis archief bevat uitvoer van een Azure Cognitive Search-verrijkings pijplijn voor latere analyses of andere downstream-verwerking. Een AI-verrijkte pijp lijn accepteert afbeeldings bestanden of ongestructureerde tekst bestanden, indexeert deze met behulp van Azure Cognitive Search, maakt AI-verrijkingen van Cognitive Services (zoals afbeeldings analyse en natuurlijke taal verwerking) en slaat de resultaten vervolgens op in een Knowledge Store in Azure Storage. U kunt hulpprogram ma's als Power BI of Storage Explorer in de Azure Portal gebruiken om het kennis archief te verkennen.

In dit artikel gebruikt u de REST API-interface om AI-verrijkingen op te nemen, te indexeren en toe te passen op een aantal functionerings gesprekken met hotels. De Hotel beoordelingen worden geïmporteerd in Azure Blob-opslag. De resultaten worden opgeslagen als een kennis archief in azure-tabel opslag.

Nadat u het kennis archief hebt gemaakt, kunt u leren hoe u toegang krijgt tot het kennis archief met behulp van [Storage Explorer](knowledge-store-view-storage-explorer.md) of [Power bi](knowledge-store-connect-power-bi.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!TIP]
> U wordt aangeraden om de [desktop-app](https://www.getpostman.com/) voor dit artikel te plaatsen. De [bron code](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) voor dit artikel bevat een postman-verzameling met alle aanvragen. 

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Deze Snelstartgids maakt gebruik van Azure Cognitive Search, Azure Blob Storage en [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de werk belasting zo klein is, wordt Cognitive Services achter de schermen getikt om gratis Maxi maal 20 trans acties dagelijks te kunnen uitvoeren wanneer deze vanuit Azure Cognitive Search worden aangeroepen. Zolang u de door u geleverde voorbeeld gegevens gebruikt, kunt u het maken of koppelen van een Cognitive Services resource overs Laan.

1. [Down load HotelReviews_Free. CSV](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Deze gegevens zijn gegevens van een hotel beoordeling die zijn opgeslagen in een CSV-bestand (afkomstig van Kaggle.com) en bevat 19 delen van klanten feedback over één hotel. 

1. [Een Azure Storage-account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) of [een bestaand account vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) onder uw huidige abonnement. U gebruikt Azure Storage voor zowel de onbewerkte inhoud die u wilt importeren als het kennis archief dat het eind resultaat heeft.

   Kies het account type **StorageV2 (algemeen gebruik v2)** .

1. Open de pagina's van de BLOB Services en maak een container met de naam *Hotel-Recensies*.

1. Klik op **Uploaden**.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "De Hotels-beoordelingen uploaden")

1. Selecteer het bestand **HotelReviews-Free. CSV** dat u in de eerste stap hebt gedownload.

    ![De Azure Blob-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure Blob-container maken")

1. U bent bijna klaar met deze resource, maar voordat u deze pagina's verlaat, gebruikt u een koppeling in het navigatie deel venster aan de linkerkant om de pagina **toegangs sleutels** te openen. Een connection string ophalen om gegevens op te halen uit de Blob-opslag. Een connection string ziet er ongeveer uit zoals in het volgende voor beeld: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Ga nog in de portal naar Azure Cognitive Search. [Een nieuwe service maken](search-create-service-portal.md) of [een bestaande service zoeken](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt voor deze oefening een gratis service gebruiken.

## <a name="configure-postman"></a>Postman configureren

Postman installeren en instellen.

### <a name="download-and-install-postman"></a>Postman downloaden en installeren

1. Down load de [bron code van de Postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selecteer **bestand** > **importeren** om de bron code in de Postman te importeren.
1. Selecteer het tabblad **verzamelingen** en selecteer vervolgens de knop **...** (weglatings tekens).
1. Selecteer **Bewerken**. 
   
   ![Postman-app met navigatie](media/knowledge-store-create-rest/postman-edit-menu.png "Ga naar het menu bewerken in postman")
1. Selecteer in het dialoog venster **bewerken** het tabblad **variabelen** . 

Op het tabblad **variabelen** kunt u waarden toevoegen die na elke keer dat er wordt vervangen door een specifieke variabele tussen dubbele accolades wordt aangetroffen. Postman vervangt bijvoorbeeld het symbool `{{admin-key}}` door de huidige waarde die u hebt ingesteld voor `admin-key`. Postman maakt de vervanging in Url's, kopteksten, de hoofd tekst van de aanvraag, enzovoort. 

Als u de waarde voor `admin-key`wilt ophalen, gaat u naar de Azure Cognitive Search-service en selecteert u het tabblad **sleutels** . Wijzig `search-service-name` en `storage-account-name` door de waarden die u hebt gekozen in [Services maken](#create-services-and-load-data). Stel `storage-connection-string` in met behulp van de waarde op het tabblad **toegangs sleutels** voor het opslag account. U kunt de standaard waarde voor de andere waarden laten staan.

![Het tabblad app-variabelen van postman](media/knowledge-store-create-rest/postman-variables-window.png "Het venster variabelen van postman")


| Variabele    | Waar kan ik SmartGlass krijgen? |
|-------------|-----------------|
| `admin-key` | Op de pagina **sleutels** van de Azure Cognitive Search-service.  |
| `api-version` | Geef **een voor beeld van 2019-05-06**. |
| `datasource-name` | Als **Hotel laten zien-Recensies-Active Directory**. | 
| `indexer-name` | Verlof als **Hotel-beoordelingen-IXR**. | 
| `index-name` | Als **Hotel laten zien, beoordelingen-IX**. | 
| `search-service-name` | De naam van de Azure Cognitive Search-service. De URL is `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Verlof als **Hotel-beoordelingen-SS**. | 
| `storage-account-name` | De naam van het opslagaccount. | 
| `storage-connection-string` | Selecteer in het opslag account op het tabblad **toegangs sleutels** de optie **key1** > **verbindings reeks**. | 
| `storage-container-name` | Zorg ervoor dat u zich als **Hotel bekijkt**. | 

### <a name="review-the-request-collection-in-postman"></a>Bekijk de verzameling aanvragen in postman

Wanneer u een kennis archief maakt, moet u vier HTTP-aanvragen doen: 

- **De aanvraag voor het maken van de index plaatsen**: deze index bevat de gegevens die door Azure Cognitive Search worden gebruikt en wordt geretourneerd.
- **Post-aanvraag voor het maken van de gegevens bron**: deze gegevens bron verbindt uw Azure Cognitive Search gedrag met het opslag account van de gegevens en het kennis archief. 
- De **aanvraag voor het maken van de vaardig heden plaatsen**: de vaardig heden bevat de verrijkingen die worden toegepast op uw gegevens en de structuur van het kennis archief.
- **Put-aanvraag om de Indexeer functie te maken: als**de Indexeer functie wordt uitgevoerd, worden de gegevens gelezen, wordt de vaardig heden toegepast en worden de resultaten opgeslagen. U moet deze aanvraag als laatste uitvoeren.

De [bron code](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) bevat een postman-verzameling die de vier aanvragen heeft. Als u de aanvragen wilt uitgeven, selecteert u in postman het tabblad voor de aanvraag. Voeg vervolgens `api-key` en `Content-Type` aanvraag headers toe. Stel de waarde van `api-key` in op `{{admin-key}}`. Stel de waarde `Content-type` in op `application/json`. 

![Scherm opname van de interface van Postman voor kopteksten](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> U moet in al uw aanvragen `api-key` en `Content-type` kopteksten instellen. Als in postman een variabele wordt herkend, wordt de variabele in oranje tekst weer gegeven, net als bij `{{admin-key}}` in de vorige scherm afbeelding. Als de variabele verkeerd is gespeld, wordt deze in een rode tekst weer gegeven.
>

## <a name="create-an-azure-cognitive-search-index"></a>Een Azure Cognitive Services-index maken

Maak een Azure Cognitive Search-index voor de gegevens die u wilt doorzoeken, filteren en Toep assen van verbeteringen in. Maak de index door een PUT-aanvraag uit te geven aan `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman vervangt symbolen die tussen dubbele accolades staan (zoals `{{search-service-name}}`, `{{index-name}}`en `{{api-version}}`) met de waarden die u instelt in [postman configureren](#configure-postman). Als u een ander hulp programma gebruikt om uw REST-opdrachten uit te voeren, moet u deze variabelen zelf vervangen.

Stel de structuur van uw Azure Cognitive Search-index in de hoofd tekst van de aanvraag in. Ga in postman naar het deel venster **hoofd tekst** van de aanvraag nadat u de `api-key` en `Content-type` headers hebt ingesteld. De volgende JSON moet worden weer geven. Als dat niet het geval is, selecteert u **Raw** > **JSON (Application/JSON)** en plakt u de volgende code als hoofd tekst:

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

Selecteer **verzenden** om de put-aanvraag uit te geven. U ziet de status `201 - Created`. Als er een andere status wordt weer gegeven, zoekt u in het deel venster **hoofd tekst** naar een JSON-antwoord met een fout bericht. 

## <a name="create-the-datasource"></a>De gegevens bron maken

Vervolgens verbindt u Azure Cognitive Search met de Hotel gegevens die u hebt opgeslagen in Blob Storage. Als u de gegevens bron wilt maken, stuurt u een POST-aanvraag naar `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. U moet de `api-key`-en `Content-Type`-headers instellen zoals eerder is beschreven. 

Ga in postman naar de aanvraag **gegevens bron maken** en vervolgens naar het deel venster **hoofd tekst** . De volgende code zou moeten worden weergegeven:

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

De volgende stap is het opgeven van de vaardig heden die de uitbrei dingen bevat die moeten worden toegepast en de kennis opslag waar de resultaten worden opgeslagen. Selecteer in postman het tabblad **vaardig heden maken** . Deze aanvraag verzendt een PUT-naar-`https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Stel de `api-key`-en `Content-type`-headers in zoals u eerder hebt gedaan. 

Er zijn twee grote objecten op het hoogste niveau: `skills` en `knowledgeStore`. Elk object binnen het `skills`-object is een verrijkings service. Elke verrijkings service heeft `inputs` en `outputs`. Het `LanguageDetectionSkill` heeft een uitvoer `targetName` van `Language`. De waarde van dit knoop punt wordt door de meeste andere vaardig heden gebruikt als invoer. De bron is `document/Language`. De mogelijkheid om de uitvoer van het ene knoop punt te gebruiken als de invoer naar een andere, is nog steeds duidelijker in `ShaperSkill`, waarin wordt aangegeven hoe de gegevens in de tabellen van het kennis archief worden opgeslagen.

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

Het `parameters/configuration`-object bepaalt hoe de Indexeer functie de gegevens opneemt. In dit geval bevindt de invoer gegevens zich in één document met een koptekst regel en door komma's gescheiden waarden. De document sleutel is een unieke id voor het document. Voordat u code ring maakt, is de document sleutel de URL van het bron document. Ten slotte worden de uitvoer waarden van de vaardigheidset, zoals taal code, sentiment en sleutel zinnen, toegewezen aan hun locaties in het document. Hoewel er sprake is van een enkele waarde voor `Language`, wordt `Sentiment` toegepast op elk element in de matrix van `pages`. `Keyphrases` is een matrix die ook wordt toegepast op elk element in de `pages` matrix.

Nadat u de `api-key` en `Content-type` kopteksten hebt ingesteld en hebt bevestigd dat de hoofd tekst van de aanvraag vergelijkbaar is met de volgende bron code, selecteert u **verzenden** in postman. Postman verzendt een PUT-aanvraag naar `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. De Indexeer functie wordt door Azure Cognitive Search gemaakt en uitgevoerd. 

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

Ga in het Azure Portal naar de **overzichts** pagina van de Azure Cognitive Search-service. Selecteer het tabblad **Indexeer functies** en selecteer vervolgens **Hotels-Recensies-IXR**. Als de Indexeer functie nog niet is uitgevoerd, selecteert u **uitvoeren**. De Indexeer taak kan enkele waarschuwingen met betrekking tot taal herkenning veroorzaken. De gegevens bevatten enkele beoordelingen die zijn geschreven in talen die nog niet worden ondersteund door de cognitieve vaardig heden. 

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met behulp van Cognitive Services en de resultaten naar een kennis archief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

Zie dit overzicht voor meer informatie over het verkennen van dit kennis archief met behulp van Storage Explorer:

> [!div class="nextstepaction"]
> [Weer geven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie dit overzicht voor meer informatie over het verbinden van dit kennis archief met Power BI:

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Als u deze oefening wilt herhalen of een andere AI-verrijkings scenario wilt uitproberen, verwijdert u de **idxr** indexer. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller opnieuw ingesteld op nul.
