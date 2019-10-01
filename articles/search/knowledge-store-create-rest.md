---
title: Een kennis archief maken met behulp van REST-Azure Search
description: Maak een Azure Search Knowledge Store voor persistentie van verrijkingen van de cognitieve Zoek pijplijn, met behulp van de REST API en de Postman.
author: lobrien
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: laobri
ms.openlocfilehash: ae0694c4c79527ef3b64ad68d32ef3bce0150462
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703579"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Een Azure Search Knowledge Store maken met behulp van REST

Het kennis archief is een functie in Azure Search die de uitvoer van een AI-verrijkings pijplijn persistent maakt voor latere analyses of andere downstream-verwerking. Een AI-verrijkte pijp lijn accepteert afbeeldings bestanden of ongestructureerde tekst bestanden, indexeert deze met behulp van Azure Search, maakt AI-verrijkingen van Cognitive Services (zoals afbeeldings analyse en natuurlijke taal verwerking) en slaat de resultaten op in een kennis archief in azure opslagpad. U kunt vervolgens hulpprogram ma's als Power BI of Storage Explorer gebruiken om het kennis archief te verkennen.

In dit artikel gebruikt u de REST API-interface om AI-verrijkingen op te nemen, te indexeren en toe te passen op een aantal functionerings gesprekken met hotels. De Hotel beoordelingen worden geïmporteerd in Azure Blob Storage en de resultaten worden opgeslagen als een Knowledge Store in azure Table Storage.

Nadat u het kennis archief hebt gemaakt, kunt u meer informatie krijgen over toegang tot dit kennis archief met behulp van [Storage Explorer](knowledge-store-view-storage-explorer.md) of [Power bi](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1-services maken

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

+ [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) om de voorbeeld gegevens en het kennis archief op te slaan. Uw opslag account moet dezelfde locatie (zoals vs-West) voor uw Azure Search-service gebruiken. Het *type account* moet *StorageV2 (algemeen gebruik v2)* (standaard) of *opslag (algemeen gebruik v1)* zijn.

+ Aanbevolen: [Postman desktop-app](https://www.getpostman.com/) voor het verzenden van aanvragen naar Azure Search. U kunt de REST API gebruiken met elk hulp programma dat kan werken met HTTP-aanvragen en-antwoorden. Postman is een goede keuze voor het verkennen van REST-Api's en wordt gebruikt in dit artikel. Daarnaast bevat de [bron code](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) voor dit artikel een postman-verzameling van aanvragen. 

## <a name="2---store-the-data"></a>2: de gegevens opslaan

Laadt het het CSV-bestand van het Hotel in Azure Blob-opslag, zodat het toegankelijk is voor een Azure Search indexer en door de AI-verrijkings pijplijn kan worden ingevoerd.

### <a name="create-an-azure-blob-container-with-the-data"></a>Een Azure Blob-container maken met de gegevens

1. [Down load de gegevens voor de Hotel beoordeling die zijn opgeslagen in een CSV-bestand (HotelReviews_Free. CSV)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Deze gegevens zijn afkomstig van Kaggle.com en bevatten feedback van klanten over hotels.
1. [Meld u aan bij de Azure Portal](https://portal.azure.com)en navigeer naar uw Azure Storage-account.
1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Als u de container wilt maken, klikt u in de linkernavigatiebalk voor uw opslag account op **blobs**en klikt u vervolgens op **+ container** op de opdracht balk.
1. Voer`hotel-reviews`in voor de nieuwe container **naam**.
1. Selecteer een **openbaar toegangs niveau**. We hebben de standaard waarde gebruikt.
1. Klik op **OK** om de Azure Blob-container te maken.
1. Open de nieuwe `hotels-review` container, klik op **uploaden**en selecteer het bestand **HotelReviews-Free. CSV** dat u in de eerste stap hebt gedownload.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "De Hotels-beoordelingen uploaden")

1. Klik op **uploaden** om het CSV-bestand te importeren in Azure Blob Storage. De nieuwe container wordt weer gegeven.

    ![De Azure Blob-container maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure Blob-container maken")

## <a name="3---configure-postman"></a>3-postman configureren

Down load de [bron code van de Postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/knowledge-store/KnowledgeStore.postman_collection.json) en importeer deze in een postman met behulp van **bestand, importeren...** . Ga naar het tabblad **verzamelingen** en klik op de knop **..** . en selecteer **bewerken**. 

![Postman-app met navigatie](media/knowledge-store-create-rest/postman-edit-menu.png "Navigeer naar het menu bewerken in een bericht")

Navigeer in het dialoog venster resultd Edit naar het tabblad **Varia bles** . 

Op het tabblad **variabelen** kunt u waarden toevoegen die na elke keer dat er wordt getraceerd, worden vervangen door dubbele accolades. Als u bijvoorbeeld postman vervangt, wordt het symbool `{{admin-key}}` vervangen door de huidige waarde van de `admin-key`. Postman maakt deze vervanging in Url's, kopteksten, de hoofd tekst van de aanvraag, enzovoort. 

De waarde voor `admin-key` vindt u op het tabblad **sleutels** van Search service. U moet `search-service-name` en `storage-account-name` wijzigen in de waarden die u in [stap 1](#1---create-services)hebt gekozen. Stel `storage-connection-string` in op de waarde op het tabblad **toegangs sleutels** voor het opslag account. De andere waarden die u ongewijzigd kunt laten.

Het ![tabblad](media/knowledge-store-create-rest/postman-variables-window.png "het venster variabelen van") postman app postman


| Variabele    | Waar om het te krijgen |
|-------------|-----------------|
| `admin-key` | Search Service, tabblad **sleutels**              |
| `api-version` | Laten staan als ' 2019-05-06-preview ' |
| `datasource-name` | Als ' Hotel-beoordelingen-Active Directory ' laten staan | 
| `indexer-name` | Als ' Hotel-beoordelingen-IXR ' blijven | 
| `index-name` | Als ' Hotel-beoordelingen-IX ' blijven | 
| `search-service-name` | Search Service, hoofd naam. De URL is `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Als ' Hotel-beoordelingen-SS ' laten staan | 
| `storage-account-name` | Opslag account, hoofd naam | 
| `storage-connection-string` | Opslag account, tabblad **toegangs sleutels** , **key1** - **verbindings reeks** | 
| `storage-container-name` | Als ' Hotel-beoordelingen ' blijven | 

### <a name="review-the-request-collection-in-postman"></a>Bekijk de verzameling aanvragen in postman

Voor het maken van een kennis archief moet u vier HTTP-aanvragen doen: 

1. Een PUT-aanvraag voor het maken van de index. Deze index bevat de gegevens die worden gebruikt en geretourneerd door Azure Search.
1. Een POST-aanvraag om de gegevens bron te maken. Deze gegevens bron verbindt het Azure Search gedrag met het opslag account van de gegevens en het kennis archief. 
1. Een PUT-aanvraag om de vaardig heden te maken. De vaardig heden specificeert de verrijkingen die worden toegepast op uw gegevens en de structuur van het kennis archief.
1. Een PUT-aanvraag om de Indexeer functie te maken. Als de Indexeer functie wordt uitgevoerd, worden de gegevens gelezen, wordt de vaardig heden toegepast en worden de resultaten opgeslagen. U moet deze aanvraag als laatste uitvoeren.

De [bron code](https://github.com/Azure-Samples/azure-search-postman-searches/Tutorial/Knowledge_Store/KnowledgeStore.postman_collection.json) bevat een postman-verzameling met deze vier aanvragen. Als u de aanvragen wilt uitgeven, gaat u naar het tabblad aanvragen in postman en voegt u `api-key` en `Content-Type`-aanvraag headers toe. Stel de waarde van `api-key` in op `{{admin-key}}`. Stel de waarde `Content-type` in op `application/json`. 

> [!div class="mx-imgBorder"]
> ![Screenshot met de interface van Postman voor kopteksten @ no__t-1

> [!Note]
> U moet in al uw aanvragen `api-key`-en `Content-type`-headers instellen. Als een variabele wordt herkend door Postman, wordt deze in oranje tekst weer gegeven, net als bij `{{admin-key}}` in de scherm opname. Als de variabele verkeerd is gespeld, wordt deze in rode tekst weer gegeven.
>

## <a name="4---create-an-azure-search-index"></a>4-een Azure Search-index maken

U moet een Azure Search index maken om de gegevens weer te geven waarop u wilt zoeken, filteren en uitbrei dingen kunt doen. U maakt de index door een PUT-aanvraag uit te geven aan `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Bij postman worden symbolen vervangen tussen dubbele accolades, zoals `{{search-service-name}}`, `{{index-name}}` en `{{api-version}}` met de waarden die zijn opgegeven in [stap 3](#3---configure-postman). Als u een ander hulp programma gebruikt om uw REST-opdrachten uit te geven, moet u deze variabelen zelf vervangen.

Geef de structuur van uw Azure Search index op in de hoofd tekst van de aanvraag. Ga in postman na het instellen van de `api-key` en `Content-type`-headers naar het deel venster **hoofd tekst** van de aanvraag. De volgende JSON moet worden weer geven, maar als dat niet het geval is, kiest u **RAW** en **JSON (Application/JSON)** en plakt u de volgende code als hoofd tekst:

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

U ziet dat deze index definitie een combi natie is van gegevens die u aan de gebruiker wilt presen teren (naam van het Hotel, inhoud, datum, enzovoort), Zoek opdrachten voor meta gegevens en AI-verbeteringen (sentiment, woordgroepen en taal).

Druk op de knop **verzenden** om de put-aanvraag uit te geven. Het status bericht van `201 - Created` moet worden weer gegeven. Als u een andere status ontvangt, wordt in het deel venster **hoofd tekst** een JSON-antwoord met een fout bericht weer gegeven. 

## <a name="5---create-the-datasource"></a>5-de gegevens bron maken

Nu moet u Azure Search verbinding maken met de Hotel gegevens die u in [stap 2](#2---store-the-data)hebt opgeslagen. Het maken van de gegevens bron is voltooid met een POST naar `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Ook hier moet u de `api-key`-en `Content-Type`-kopteksten instellen zoals eerder is opgegeven. 

Open in postman de aanvraag ' data source maken '. Schakel over naar het **hoofd** venster, dat de volgende code moet bevatten:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Druk op de knop **verzenden** om de post-aanvraag uit te geven. 

## <a name="6---create-the-skillset"></a>6: de vaardig heden maken 

De volgende stap is het opgeven van de vaardig heden die de uitbrei dingen bevat die moeten worden toegepast en de kennis opslag waar de resultaten worden opgeslagen. Open in postman het tabblad ' de vaardig heden maken '. Deze aanvraag verzendt een PUT-naar-`https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Stel de `api-key`-en `Content-type`-headers in zoals u eerder hebt gedaan. 

Er zijn twee grote objecten op het hoogste niveau: `"skills"` en `"knowledgeStore"`. Elk object binnen het `"skills"`-object is een verrijkings service. Elke verrijkings service heeft `"inputs"` en `"outputs"`. U ziet hoe de `LanguageDetectionSkill` een uitvoer `targetName` van `"Language"` heeft. De waarde van dit knoop punt wordt door de meeste andere vaardig heden gebruikt als invoer, met de bron als `document/Language`. Deze mogelijkheid van het gebruik van de uitvoer van het ene knoop punt als de invoer naar een andere is nog duidelijker in de `ShaperSkill`, waarmee wordt aangegeven hoe de gegevens in de tabellen van het kennis archief worden geplaatst.

Het `"knowledge_store"`-object maakt verbinding met het opslag account via de variabele `{{storage-connection-string}}` postman. Vervolgens bevat het een set toewijzingen tussen het uitgebreide document en de tabellen en kolommen die beschikbaar zijn in het kennis archief zelf. 

Als u de vaardig heden wilt genereren, plaatst u de aanvraag door op de knop **verzenden** in postman te drukken.

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

## <a name="7---create-the-indexer"></a>7: de Indexeer functie maken

De laatste stap bestaat uit het maken van de Indexeer functie, die de gegevens daad werkelijk leest en de vaardig heden activeert. In postman gaat u naar de aanvraag Indexeer functie maken en bekijkt u de hoofd tekst. Zoals u kunt zien, verwijst de definitie van de Indexeer functie naar verschillende andere resources die u al hebt gemaakt: de gegevens bron, de index en de vaardig heden. 

Het `"parameters/configuration"`-object bepaalt hoe de Indexeer functie de gegevens opneemt. In dit geval bevinden de invoer gegevens zich in één document met een kopregel en door komma's gescheiden waarden. De document sleutel is een unieke id voor het document, die vóór de code ring de URL van het bron document is. Ten slotte worden de uitvoer waarden van de vaardig heden, zoals taal code, sentiment en sleutel zinnen, toegewezen aan hun juiste locaties in het document. Hoewel er sprake is van een enkele waarde voor `Language`, wordt `Sentiment` toegepast op elk element in de matrix van `pages`. `Keyphrases` is zichzelf een matrix en wordt ook toegepast op elk element in de matrix `pages`.

Nadat u de `api-key`-en `Content-type`-headers hebt ingesteld en hebt bevestigd dat de hoofd tekst van de aanvraag vergelijkbaar is met de bron code die volgt, drukt u op **verzenden** in het bericht. De aanvraag wordt door postman `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` geplaatst. De Indexeer functie wordt door Azure Search gemaakt en uitgevoerd. 

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

## <a name="8---run-the-indexer"></a>8-de Indexeer functie uitvoeren 

Ga in het Azure Portal naar het **overzicht** van de Search service en selecteer het tabblad **Indexeer functies** . Klik op de **IXR** die u in de vorige stap hebt gemaakt. Als de Indexeer functie nog niet is uitgevoerd, drukt u op de knop **uitvoeren** . De Indexeer taak kan enkele waarschuwingen met betrekking tot taal herkenning veroorzaken, omdat de gegevens sommige beoordelingen bevatten die zijn geschreven in talen die nog niet worden ondersteund door de cognitieve vaardig heden. 

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met behulp van cognitieve Services en de resultaten in een kennis archief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

Zie de volgende walkthrough voor meer informatie over het verkennen van dit kennis archief met behulp van Storage Explorer.

> [!div class="nextstepaction"]
> [Weer geven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie de volgende walkthrough voor meer informatie over het verbinden van dit kennis archief met Power BI.

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Als u deze oefening wilt herhalen of een andere AI-verrijkings scenario wilt uitproberen, verwijdert u de *idxr* indexer. Als u de Indexeer functie verwijdert, wordt de gratis dagelijkse transactie teller weer ingesteld op nul.
