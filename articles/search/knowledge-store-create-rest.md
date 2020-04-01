---
title: Een kennisarchief maken (voorbeeld) met REST
titleSuffix: Azure Cognitive Search
description: Gebruik de REST API en Postman om een Azure Cognitive Search-kennisarchief te maken voor aanhoudende verrijkingen uit een AI-verrijkingspijplijn. Deze functie is momenteel beschikbaar als openbare preview-versie.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 12/30/2019
ms.openlocfilehash: 478a7e03b432006b429c96e03307fd8e494c88ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472312"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Maak een kenniswinkel met REST en Postman

> [!IMPORTANT] 
> Knowledge store is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt preview functies. Er is momenteel beperkte portalondersteuning en geen .NET SDK-ondersteuning.

Een kennisarchief bevat uitvoer uit een Azure Cognitive Search-verrijkingspijplijn voor latere analyse of andere downstreamverwerking. Een met AI verrijkte pijplijn accepteert afbeeldingsbestanden of ongestructureerde tekstbestanden, indexeert ze met Azure Cognitive Search, past AI-verrijkingen uit Cognitive Services toe (zoals beeldanalyse en verwerking van natuurlijke talen) en slaat de resultaten vervolgens op in een kennisarchief in Azure Storage. U hulpprogramma's zoals Power BI of Storage Explorer in de Azure-portal gebruiken om de kenniswinkel te verkennen.

In dit artikel gebruikt u de REST API-interface om AI-verrijkingen in te nemen, te indexeren en toe te passen op een reeks hotelbeoordelingen. De hotelbeoordelingen worden geïmporteerd in Azure Blob-opslag. De resultaten worden opgeslagen als kennisarchief in Azure Table-opslag.

Nadat u de kenniswinkel hebt gemaakt, u meer informatie krijgen over hoe u toegang krijgt tot de kenniswinkel met [Storage Explorer](knowledge-store-view-storage-explorer.md) of [Power BI.](knowledge-store-connect-power-bi.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

> [!TIP]
> We raden [postman desktop app](https://www.getpostman.com/) voor dit artikel. De [broncode](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) voor dit artikel bevat een Postman-verzameling met alle aanvragen. 

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Deze quickstart maakt gebruik van Azure Cognitive Search, Azure Blob-opslag en [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de werklast zo klein is, wordt Cognitive Services achter de schermen afgetapt om gratis verwerking te bieden voor maximaal 20 transacties per dag. Omdat de gegevensset zo klein is, u het maken of koppelen van een resource voor Cognitive Services overslaan.

1. [Download HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Deze gegevens zijn hotelbeoordelinggegevens die zijn opgeslagen in een CSV-bestand (afkomstig van Kaggle.com) en bevatten 19 stukjes feedback van klanten over één hotel. 

1. [Maak een Azure-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) aan of [zoek een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) onder uw huidige abonnement. U gebruikt Azure-opslag voor zowel de te importeren ruwe inhoud als de kennisopslag die het eindresultaat is.

   Kies het accounttype **StorageV2 (V2 voor algemeen gebruik).**

1. Open de blobservicespagina's en maak een container met de naam *hotelbeoordelingen.*

1. Klik op **Uploaden**.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "Upload de hotelrecensies")

1. Selecteer het **HotelReviews-Free.csv-bestand** dat u in de eerste stap hebt gedownload.

    ![De Azure Blob-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure Blob-container maken")

1. U bent bijna klaar met deze bron, maar voordat u deze pagina's verlaat, gebruikt u een koppeling in het linkernavigatiedeelvenster om de pagina **Toegangssleutels** te openen. Ontvang een verbindingstekenreeks om gegevens uit blob-opslag op te halen. Een verbindingstekenreeks lijkt op het volgende voorbeeld:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Nog steeds in de portal, overschakelen naar Azure Cognitive Search. [Maak een nieuwe service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U gebruik maken van een gratis service voor deze oefening.

## <a name="configure-postman"></a>Postman configureren

Installeer en stel Postman in.

### <a name="download-and-install-postman"></a>Postman downloaden en installeren

1. Download de broncode van de [Postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selecteer **Bestand** > **importeren** om de broncode te importeren in Postman.
1. Selecteer het tabblad **Verzamelingen** en selecteer de knop **...** (ellips).
1. Selecteer **Bewerken**. 
   
   ![Postbode-app met navigatie](media/knowledge-store-create-rest/postman-edit-menu.png "Ga naar het menu Bewerken in Postman")
1. Selecteer in het dialoogvenster **Bewerken** het tabblad **Variabelen.** 

Op het tabblad **Variabelen** u waarden toevoegen die Postman inwisselt telkens wanneer deze een specifieke variabele in dubbele beugels tegenkomt. Postman vervangt het symbool `{{admin-key}}` bijvoorbeeld door de huidige `admin-key`waarde die u instelt voor . Postman maakt de vervanging in URL's, headers, de aanvraagbody, enzovoort. 

`admin-key`Ga naar de azure cognitive search-service en selecteer het `search-service-name` tabblad `storage-account-name` **Sleutels.** Wijzigen en de waarden die u hebt gekozen in [Services maken.](#create-services-and-load-data) Stel `storage-connection-string` in met de waarde op het tabblad **Toegangssleutels van** het opslagaccount. U de standaardwaarden voor de andere waarden verlaten.

![Tabblad Variabelen van de postman-app](media/knowledge-store-create-rest/postman-variables-window.png "Het variabelenvenster van de postbode")


| Variabele    | Waar te verkrijgen? |
|-------------|-----------------|
| `admin-key` | Op de pagina **Sleutels** van de Azure Cognitive Search-service.  |
| `api-version` | Vertrek als **2019-05-06-Preview**. |
| `datasource-name` | Laat als **hotel-reviews-ds**. | 
| `indexer-name` | Laat als **hotel-reviews-ixr**. | 
| `index-name` | Laat als **hotel-reviews-ix**. | 
| `search-service-name` | De naam van de Azure Cognitive Search-service. De URL `https://{{search-service-name}}.search.windows.net`is . | 
| `skillset-name` | Laat als **hotel-reviews-ss**. | 
| `storage-account-name` | De naam van het opslagaccount. | 
| `storage-connection-string` | Selecteer in het opslagaccount op het tabblad **Toegangssleutels** **toets1** > **verbindingstekenreeks**. | 
| `storage-container-name` | Laat als **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Bekijk de aanvraagverzameling in Postbode

Wanneer u een kennisarchief maakt, moet u vier HTTP-aanvragen uitgeven: 

- **PUT-verzoek om de index te maken:** deze index bevat de gegevens die Azure Cognitive Search gebruikt en retourneert.
- **POST-verzoek om de gegevensbron te maken:** deze gegevensbron verbindt uw Azure Cognitive Search-gedrag met het opslagaccount van de gegevens- en kennisarchief. 
- **PUT-aanvraag om de skillset te maken:** De skillset geeft de verrijkingen aan die worden toegepast op uw gegevens en de structuur van de kenniswinkel.
- **PUT-aanvraag om de indexer te maken:** Als u de indexer uitvoert, worden de gegevens gelezen, wordt de skillset toepast en worden de resultaten opgeslagen. U moet dit verzoek als laatste uitvoeren.

De [broncode](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) bevat een Postman-verzameling met de vier aanvragen. Als u de aanvragen wilt indienen, selecteert u in Postman het tabblad voor de aanvraag. Voeg vervolgens `api-key` `Content-Type` kopteksten toe en vraag deze aan. Stel de `api-key` waarde `{{admin-key}}`in op . Stel de `Content-type` `application/json`waarde in op . 

![Schermafbeelding van de interface van de postbode voor kopteksten](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> U moet `api-key` `Content-type` instellen en headers in al uw verzoeken. Als De postbode een variabele herkent, wordt de `{{admin-key}}` variabele weergegeven in oranje tekst, zoals in de vorige schermafbeelding. Als de variabele verkeerd is gespeld, wordt deze in rode tekst weergegeven.
>

## <a name="create-an-azure-cognitive-search-index"></a>Een Azure Cognitive Services-index maken

Maak een Azure Cognitive Search-index om de gegevens weer te geven waarop u wilt zoeken, filteren en verbeteringen toepassen. Maak de index door een `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`PUT-verzoek uit te geven aan . Postbode vervangt symbolen die zijn ingesloten in `{{search-service-name}}` `{{index-name}}`dubbele `{{api-version}}`accolades (zoals , en ) door de waarden die u inStelt in [Postman configureren](#configure-postman). Als u een ander hulpmiddel gebruikt om uw REST-opdrachten uit te geven, moet u deze variabelen zelf vervangen.

Stel de structuur van uw Azure Cognitive Search-index in de hoofdtekst van de aanvraag in. Ga in Postman, `api-key` nadat `Content-type` u de kopteksten en kopteksten hebt ingesteld, naar het deelvenster **Lichaam** van het verzoek. U ziet de volgende JSON. Als u dit niet doet, selecteert u **Raw** > **JSON (toepassing/json)** en plakt u de volgende code als de hoofdtekst:

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

Deze indexdefinitie is een combinatie van gegevens die u aan de gebruiker wilt presenteren (de naam van het hotel, recensie-inhoud, de datum), zoekmetagegevens en AI-verbeteringsgegevens (Sentiment, Sleutelzinnen en Taal).

Selecteer **Verzenden** om de PUT-aanvraag uit te geven. Je zou de `201 - Created`status moeten zien. Als u een andere status ziet, zoekt u in het deelvenster **Lichaam** naar een JSON-antwoord met een foutbericht. 

## <a name="create-the-datasource"></a>De gegevensbron maken

Sluit vervolgens Azure Cognitive Search aan op de hotelgegevens die u hebt opgeslagen in blob-opslag. Als u de gegevensbron wilt `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`maken, stuurt u een POST-verzoek naar . U moet `api-key` de `Content-Type` kop- en kopteksten instellen zoals eerder besproken. 

Ga in Postman naar de aanvraag **Gegevensbron maken** en vervolgens naar het deelvenster **Lichaam.** De volgende code zou moeten worden weergegeven:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selecteer **Verzenden** om de post-aanvraag uit te geven. 

## <a name="create-the-skillset"></a>De skillset maken 

De volgende stap is het opgeven van de skillset, die zowel de toe te passen verbeteringen als de kenniswinkel aangeeft waar de resultaten worden opgeslagen. Selecteer in Postman het tabblad **Skillset maken.** Met dit verzoek `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`wordt een PUT naar . Stel `api-key` de `Content-type` kopteksten en kopteksten in zoals u eerder deed. 

Er zijn twee grote objecten `skills` `knowledgeStore`op het hoogste niveau: en . Elk object `skills` in het object is een verrijkingsservice. Elke verrijkingsdienst heeft `inputs` en `outputs`. Het `LanguageDetectionSkill` heeft `targetName` een `Language`output van . De waarde van dit knooppunt wordt door de meeste andere vaardigheden gebruikt als input. De bron `document/Language`is. De mogelijkheid om de uitvoer van het ene knooppunt als `ShaperSkill`invoer naar het andere te gebruiken is nog duidelijker in , wat aangeeft hoe de gegevens in de tabellen van de kennisopteslaan stromen.

Het `knowledge_store` object maakt verbinding met `{{storage-connection-string}}` het opslagaccount via de variabele Postman. `knowledge_store`bevat een set toewijzingen tussen het verbeterde document en tabellen en kolommen in de kenniswinkel. 

Als u de skillset wilt genereren, selecteert u de knop **Verzenden** in Postbode om het verzoek in te dienen:

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

## <a name="create-the-indexer"></a>De indexerer maken

De laatste stap is het maken van de indexer. De indexer leest de gegevens en activeert de skillset. Selecteer in Postman de aanvraag **Indexer maken** en bekijk de instantie. De definitie van de indexer verwijst naar verschillende andere resources die u al hebt gemaakt: de gegevensbron, de index en de skillset. 

Het `parameters/configuration` object bepaalt hoe de indexering van de gegevens wordt inneemt. In dit geval bevindt de invoergegevens zich in één document met een kopregel en door komma's gescheiden waarden. De documentsleutel is een unieke id voor het document. Voordat u het coderen, de documentsleutel is de URL van het brondocument. Ten slotte worden de uitvoerwaarden voor vaardigheden, zoals taalcode, sentiment en sleutelzinnen, toegewezen aan hun locaties in het document. Hoewel er één waarde `Language`voor `Sentiment` is, wordt toegepast op `pages`elk element in de array van . `Keyphrases`is een array die ook wordt toegepast `pages` op elk element in de array.

Nadat u `api-key` de `Content-type` kopteksten en kopteksten hebt ingesteld en hebt bevestigd dat de hoofdtekst van de aanvraag vergelijkbaar is met de volgende broncode, selecteert u **Verzenden** in Postbode. Postbode stuurt een PUT-verzoek naar `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Cognitive Search maakt en voert de indexer uit. 

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

Ga in de Azure-portal naar de **overzichtspagina** van de Azure Cognitive Search-service. Selecteer het tabblad **Indexers** en selecteer **vervolgens hotels-reviews-ixr.** Als de indexerer nog niet is uitgevoerd, selecteert u **Uitvoeren**. De indexeringstaak kan een aantal waarschuwingen met betrekking tot taalherkenning genereren. De gegevens bevatten een aantal beoordelingen die zijn geschreven in talen die nog niet worden ondersteund door de cognitieve vaardigheden. 

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met behulp van Cognitive Services en de resultaten hebt geprojecteerd in een kennisarchief, u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

Zie deze walkthrough voor meer informatie over het verkennen van deze kenniswinkel met Storage Explorer:

> [!div class="nextstepaction"]
> [Weergeven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie deze walkthrough voor meer informatie over het aansluiten van deze kenniswinkel op Power BI:

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Als u deze oefening wilt herhalen of een andere AI-verrijkingswalkthrough wilt proberen, verwijdert u de **hotel-reviews-idxr-indexeerder.** Als u de indexer verwijderen, wordt de gratis dagelijkse transactieteller op nul gezet.
