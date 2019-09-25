---
title: Aan de slag met Knowledge Store (preview)-Azure Search
description: Meer informatie over de stappen voor het verzenden van verrijkte documenten die zijn gemaakt door pijp lijnen voor AI-indexering in Azure Search naar een kennis archief in uw Azure Storage-account. Vanuit deze weer gave kunt u verrijkte documenten in Azure Search en in andere toepassingen bekijken, opnieuw vorm geven en gebruiken.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: heidist
ms.openlocfilehash: 7568742c4a01903eddf7681da71e4f243cac1e56
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265632"
---
# <a name="how-to-get-started-with-knowledge-store-in-azure-search"></a>Aan de slag met een Knowledge Store in Azure Search

> [!Note]
> Het kennis archief is een preview-versie en is niet bedoeld voor productie gebruik. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>
In het [kennis archief](knowledge-store-concept-intro.md) worden AI-verrijkte documenten opgeslagen die tijdens de indexering zijn gemaakt in uw Azure-opslag account voor downstream gegevens analyse in andere apps. U kunt ook opgeslagen verrijkingen gebruiken om een Azure Search Indexing-pijp lijn te begrijpen en te verfijnen. 

Een kennis archief wordt gedefinieerd door een *vaardig heden* en gemaakt door een *Indexeer functie*. De fysieke expressie van een kennis archief wordt opgegeven via *projecties* waarmee de gegevens structuren in de opslag worden bepaald. Op het moment dat u deze procedure hebt voltooid, hebt u al deze objecten gemaakt en weet u hoe ze allemaal bij elkaar passen. 

In deze oefening begint u met voorbeeld gegevens, services en hulpprogram ma's om de basis werk stroom te leren voor het maken en gebruiken van uw eerste kennis archief, met nadruk op de definitie van de vaardig heden.

## <a name="prerequisites"></a>Vereisten

Het kennis archief bevindt zich in het midden van meerdere services, met Azure Blob Storage en Azure Table Storage die fysieke opslag bieden, en Azure Search en Cognitive Services voor het maken en bijwerken van objecten. De kennis van de [basis architectuur](knowledge-store-concept-intro.md) is een vereiste voor deze walkthrough.

De volgende services en hulpprogram ma's worden gebruikt in deze Quick Start. 

+ [Ontvang de Postman desktop-app](https://www.getpostman.com/), die wordt gebruikt voor het verzenden van HTTP-aanvragen naar Azure Search.

+ [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor het opslaan van voorbeeld gegevens en de kennis opslag. Uw kennis archief bevinden zich in azure Storage.

+ [Maak een Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) op de S0-laag voor betalen per gebruik voor brede toegang tot het volledige aantal vaardig heden dat wordt gebruikt in AI-verrijkingen. Cognitive Services en uw Azure Search-service moeten zich in dezelfde regio bevinden.

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service. 

Er zijn ook een voor beeld van JSON-documenten en een postman-verzamelings bestand vereist. Instructies voor het zoeken en laden van aanvullende bestanden vindt u in de sectie [voorbeeld gegevens voorbereiden](#prepare-sample-data) .

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

    ![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. In de volgende secties geeft u de service naam en API-sleutel op voor elke HTTP-aanvraag.

<a name="prepare-sample-data"></a>

## <a name="prepare-sample-data"></a>Voorbeeld gegevens voorbereiden

Een kennis archief bevat de uitvoer van een verrijkings pijplijn. Invoer bestaat uit ' onbruikbaar ' gegevens die uiteindelijk ' bruikbaar ' zijn tijdens de uitvoering van de pijp lijn. Voor beelden van niet-bruikbare gegevens zijn onder andere afbeeldings bestanden die moeten worden geanalyseerd voor tekst-of afbeeldings kenmerken of compacte tekst bestanden die kunnen worden geanalyseerd voor entiteiten, sleutel zinnen of sentiment. 

In deze oefening wordt uitgelichte tekst bestanden (informatie over de jurisprudentie) gebruikt die afkomstig zijn van de download pagina voor de open bare bulk gegevens van het [Caselaw-toegangs project](https://case.law/bulk/download/) . We hebben een voor beeld van tien documenten geüpload naar GitHub voor deze oefening. 

In deze taak maakt u een Azure Blob-container voor deze documenten die moeten worden gebruikt als invoer voor de pijp lijn. 

1. Down load en pak de [Azure Search Sample Data](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) -opslag plaats uit om de [Caselaw-gegevensset](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw)op te halen. 

1. [Meld u aan bij de Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op blobs en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om voorbeeld gegevens te bevatten: 

   1. Geef de container `caselaw-test`een naam. 
   
   1. Stel het niveau van open bare toegang in op een van de geldige waarden.

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk.

   ![Uploaden op de opdracht balk](media/search-semi-structured-data/upload-command-bar.png "Uploaden op de opdracht balk")

1. Navigeer naar de map met het voor beeld-voorbeeld bestand **caselaw. json** . Selecteer het bestand en klik vervolgens op **uploaden**.

1. Terwijl u zich in azure Storage bevindt, moet u de naam van connection string en container ophalen.  U hebt beide teken reeksen nodig in [gegevens bron maken](#create-data-source):

   1. Op de pagina overzicht klikt u op **toegangs sleutels** en kopieert u een *Connection String*. Deze begint met `DefaultEndpointsProtocol=https;` en eindigt met. `EndpointSuffix=core.windows.net` De naam en de sleutel van uw account zijn tussen. 

   1. De naam van de container `caselaw-test` moet of de naam zijn die u hebt toegewezen.



## <a name="set-up-postman"></a>Postman instellen

Postman is de client-app die u gebruikt om aanvragen en JSON-documenten naar Azure Search te verzenden. U kunt diverse aanvragen formuleren door alleen de informatie in dit artikel te gebruiken. Twee van de grootste aanvragen (het maken van een index, het maken van een vaardig heden) bevatten echter uitgebreide JSON die te groot is om in een artikel in te sluiten. 

We hebben een postman-verzamelings bestand gemaakt om alle JSON-documenten en-aanvragen volledig beschikbaar te maken. Het downloaden en vervolgens importeren van dit bestand is uw eerste taak bij het instellen van de client.

1. Down load de [Azure Search postman samples](https://github.com/Azure-Samples/azure-search-postman-samples) -opslag en pak deze uit.

1. Start de Postman en importeer de Caselaw-postman-verzameling:

   1. Klik > op import**bestanden** > importeren**bestanden**. 

   1. Ga naar de map \azure-search-postman-samples-master\azure-search-postman-samples-master\Caselaw.

   1. Select **Caselaw.postman_collection_v2.json**. U ziet vier **post** -aanvragen in de verzameling.

   ![Postman-verzameling voor Caselaw-demo](media/knowledge-store-howto/postman-collection.png "Postman-verzameling voor Caselaw-demo")
   

## <a name="create-an-index"></a>Een index maken
    
De eerste aanvraag gebruikt de [Create Index-API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), waarmee een Azure search index wordt gemaakt waarmee alle Doorzoek bare gegevens worden opgeslagen. Een index geeft alle velden, para meters en kenmerken op.

U hoeft niet per se een index voor kennis analyse te hebben, maar een Indexeer functie wordt pas uitgevoerd als er een index wordt gegeven. 

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexes?api-version=2019-05-06-Preview` Vervang`YOUR-AZURE-SEARCH-SERVICE-NAME` in de URL door de naam van uw zoek service. 

1. Vervang `<YOUR AZURE SEARCH ADMIN API-KEY>` in de sectie koptekst door een beheer-API-sleutel voor Azure Search.

1. Het JSON-document bevindt zich in de sectie body van een index schema. De buitenste shell van een index is samengevouwen voor zicht baarheid en bestaat uit de volgende elementen. De verzameling velden correspondeert met velden in de gegevensset caselaw.

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

1. Vouw de `fields` verzameling uit. Het bevat het meren deel van de index definitie, bestaande uit eenvoudige velden, [complexe velden](search-howto-complex-data-types.md) met geneste substructuren en verzamelingen.

   Neem even de tijd om de veld definitie voor het `casebody` complexe veld op de regels 302-384 te controleren. U ziet dat een complex veld andere complexe velden kan bevatten wanneer hiërarchische representaties nodig zijn. Hiërarchische structuren kunnen worden gemodelleerd in een index, zoals hier wordt weer gegeven, en ook als projectie in een vaardig heden, waardoor een geneste gegevens structuur in het kennis archief wordt gemaakt.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

1. Klik op **verzenden** om de aanvraag uit te voeren.  U moet een **status krijgen: 201 heeft** een bericht als antwoord gemaakt.

<a name="create-data-source"></a>

## <a name="create-a-data-source"></a>Een gegevensbron maken

Bij de tweede aanvraag wordt de [API Create Data Source](https://docs.microsoft.com/rest/api/searchservice/create-data-source) gebruikt om verbinding te maken met Azure Blob Storage. 

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/datasources?api-version=2019-05-06-Preview` Vervang`YOUR-AZURE-SEARCH-SERVICE-NAME` in de URL door de naam van uw zoek service. 

1. Vervang `<YOUR AZURE SEARCH ADMIN API-KEY>` in de sectie koptekst door een beheer-API-sleutel voor Azure Search.

1. Het JSON-document in de hoofd sectie bevat uw opslag account connection string en de naam van de BLOB-container. De connection string vindt u in de Azure Portal in de **toegangs sleutels**van uw opslag account. 

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<YOUR-BLOB-CONTAINER-NAME>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

1. Klik op **verzenden** om de aanvraag uit te voeren.  U moet een **status krijgen: 201 heeft** een bericht als antwoord gemaakt.



<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Een vaardig heden-en kennis archief maken

De derde aanvraag gebruikt de [Create vaardig heden-API](https://docs.microsoft.com/rest/api/searchservice/create-skillset), waarmee een Azure Search-object wordt gemaakt waarmee wordt aangegeven welke cognitieve vaardig heden moeten worden aangeroepen, hoe vaardig heden moeten worden gebundeld en wat het belangrijkste voor deze procedure is om een kennis archief op te geven.

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/skillsets?api-version=2019-05-06-Preview` Vervang`YOUR-AZURE-SEARCH-SERVICE-NAME` in de URL door de naam van uw zoek service. 

1. Vervang `<YOUR AZURE SEARCH ADMIN API-KEY>` in de sectie koptekst door een beheer-API-sleutel voor Azure Search.

1. Het JSON-document in de hoofd tekst sectie is een definitie van een vaardig heden. De buitenste shell van een vaardig heden is samengevouwen voor zicht baarheid en bestaat uit de volgende elementen. De `skills` verzameling definieert de verrijkingen in het geheugen, maar `knowledgeStore` de definitie geeft aan hoe de uitvoer wordt opgeslagen. De `cognitiveServices` definitie is uw verbinding met de AI-verrijkings engines.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

1. Vouw `cognitiveServices` en`knowledgeStore` zodat u verbindings gegevens kunt opgeven. In het voor beeld bevinden deze teken reeksen zich na de definitie van de vaardig heden, naar het einde van de aanvraag tekst. 

   Voor `cognitiveServices`is het inrichten van een resource in de S0-laag, die zich in dezelfde regio bevindt als Azure Search. U kunt de cognitiveServices-naam en-sleutel ophalen van dezelfde pagina in de Azure Portal. 
   
   Voor `knowledgeStore`kunt u dezelfde connection string gebruiken die wordt gebruikt voor de caselaw-BLOB-container.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "YOUR-SAME-REGION-S0-COGNITIVE-SERVICES-RESOURCE",
        "key": "YOUR-COGNITIVE-SERVICES-KEY"
    },
    "knowledgeStore": {
        "storageConnectionString": "YOUR-STORAGE-ACCOUNT-CONNECTION-STRING",
    ```

1. Vouw de verzameling vaardig heden uit, met name de shaper-vaardig heden op de regels 85 en 179. De shaper-vaardigheid is belang rijk omdat hiermee de gewenste gegevens structuren voor kennis analyse worden samengesteld. Tijdens de uitvoering van de vaardig heden zijn deze structuren alleen in het geheugen, maar wanneer u naar de volgende stap gaat, ziet u hoe deze uitvoer kan worden opgeslagen in een kennis archief om verder te kunnen verkennen.

   Het volgende fragment bevindt zich in regel 217. 

    ```json
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
            ]
        }
    ]
   . . .
   ```

1. Vouw `projections` element in `knowledgeStore`uit, beginnend op regel 262. Prognoses geven de samen stelling van het kennis archief aan. Prognoses worden opgegeven in tabellen-object paren, maar momenteel maar één op tijdstip. Zoals u in de eerste projectie kunt zien, `tables` is opgegeven maar `objects` niet. In de tweede staat het tegenover elkaar.

   In azure Storage worden tabellen gemaakt in tabel opslag voor elke tabel die u maakt, en elk object haalt een container op in Blob Storage.

   Blob-objecten bevatten doorgaans de volledige expressie van een verrijking. Tabellen bevatten doorgaans gedeeltelijke verrijkingen, in combi naties die u voor specifieke doel einden rangschikt. In dit voor beeld ziet u een tabel cases en een tabel met adviezen, maar deze worden niet weer gegeven in andere tabellen, zoals entiteiten, advocaten, rechters en partijen.

    ```json
    "projections": [
        {
            "tables": [
                {
                    "tableName": "Cases",
                    "generatedKeyName": "CaseId",
                    "source": "/document/Case"
                },
                {
                    "tableName": "Opinions",
                    "generatedKeyName": "OpinionId",
                    "source": "/document/Case/OpinionsSnippets/*"
                }
            ],
            "objects": []
        },
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "enrichedcases",
                    
                    "source": "/document/CaseFull"
                }
            ]
        }
    ]
    ```

1. Klik op **verzenden** om de aanvraag uit te voeren. Het antwoord moet **201** zijn en lijkt op het volgende voor beeld, waarin het eerste deel van het antwoord wordt weer gegeven.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Een Indexeer functie maken en uitvoeren

De vierde aanvraag maakt gebruik van de [Create indexer-API](https://docs.microsoft.com/rest/api/searchservice/create-indexer), waarmee een Azure Search Indexeer functie wordt gemaakt. Een Indexeer functie is de uitvoerings engine van de Indexing-pijp lijn. Alle definities die u tot nu toe hebt gemaakt, worden in beweging gebracht met deze stap.

1. `https://YOUR-AZURE-SEARCH-SERVICE-NAME.search.windows.net/indexers?api-version=2019-05-06-Preview` Vervang`YOUR-AZURE-SEARCH-SERVICE-NAME` in de URL door de naam van uw zoek service. 

1. Vervang `<YOUR AZURE SEARCH ADMIN API-KEY>` in de sectie koptekst door een beheer-API-sleutel voor Azure Search.

1. In het hoofd gedeelte van het JSON-document wordt de naam van de Indexeer functie opgegeven. Er is een gegevens bron en index vereist voor de Indexeer functie. Een vaardig heden is optioneel voor een Indexeer functie, maar is vereist voor AI-verrijking.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    ```

1. Vouw outputFieldMappings uit. In tegens telling tot fieldMappings, die worden gebruikt voor aangepaste toewijzing tussen velden in een gegevens bron en velden in een index, worden de outputFieldMappings gebruikt voor het toewijzen van verrijkte velden, gemaakt en gevuld door de pijp lijn, naar uitvoer velden in een index of projectie.

    ```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
            "targetFieldName": "people",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
            "targetFieldName": "orginizations",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
            "targetFieldName": "locations",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
            "targetFieldName": "entities",
            "mappingFunction": null
        },
        {
            "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases",
            "mappingFunction": null
        }
    ]
    ```

1. Klik op **verzenden** om de aanvraag uit te voeren. Het antwoord moet **201** zijn en de antwoord tekst moet er bijna hetzelfde uitzien als de door u aangevraagde nettolading (afgekapt voor de boog).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": { },
        "fieldMappings": [],
        "outputFieldMappings": [ ]
    }
    ```

## <a name="explore-knowledge-store"></a>Kennis archief verkennen

U kunt beginnen met verkennen zodra het eerste document is geïmporteerd. Voor deze taak gebruikt u [**Storage Explorer**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) in de portal.

Het is belang rijk om te realiseren dat een kennis archief volledig is losgekoppeld van Azure Search. De Azure Search-index en het kennis archief bevatten beide gegevens weergave en-inhoud, maar onderdelen van daaruit. Gebruik de index voor zoeken in volledige tekst, gefilterde Zoek opdrachten en alle scenario's die worden ondersteund in Azure Search. U kunt ook door gaan met alleen uw kennis archief, andere hulp middelen toevoegen om inhoud te analyseren.

## <a name="takeaways"></a>Opgedane kennis

U hebt nu uw eerste kennis archief gemaakt in azure Storage en gebruikt Storage Explorer om de verrijkingen weer te geven. Dit is de fundamentele ervaring voor het werken met opgeslagen verrijkingen. 

## <a name="next-steps"></a>Volgende stappen

De shaper-vaardigheid heeft de zware belasting voor het maken van nauw keurige gegevens formulieren die kunnen worden gecombineerd in nieuwe vormen. Bekijk als volgende stap de referentie pagina voor deze vaardigheid voor meer informatie over hoe deze wordt gebruikt.

> [!div class="nextstepaction"]
> [Naslag informatie voor shaper-kwalificaties](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->
