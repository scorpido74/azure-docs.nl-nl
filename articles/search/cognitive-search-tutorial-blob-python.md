---
title: 'Zelf studie: python en AI over Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Bekijk een voor beeld van tekst extractie en natuurlijke taal verwerking over inhoud in Blob Storage met behulp van een Jupyter python notebook en de Azure Cognitive Search REST-Api's.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194051"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelf studie: python en AI gebruiken voor het genereren van Doorzoek bare inhoud van Azure-blobs

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kan een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) informatie ophalen en nieuwe inhoud maken die nuttig is voor Zoek opdrachten in volledige tekst of kennis analyse. Hoewel een pijp lijn installatie kopieën kan verwerken, wordt in deze python-zelf studie aandacht besteed aan tekst, het Toep assen van taal detectie en de verwerking van natuurlijke taal om nieuwe velden te maken die u kunt gebruiken in query's, facetten en filters.

Deze zelf studie maakt gebruik van python en de [Zoek rest api's](https://docs.microsoft.com/rest/api/searchservice/) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met hele documenten (ongestructureerde tekst) zoals PDF, HTML, DOCX en PPTX in Azure Blob-opslag.
> * Definieer een pijp lijn die tekst extraheert, taal detecteert, entiteiten herkent en sleutel zinnen detecteert.
> * Definieer een index voor het opslaan van de uitvoer (onbewerkte inhoud, plus pijp lijn gegenereerde naam/waarde-paren).
> * Voer de pijp lijn uit om trans formaties en analyses te starten en om de index te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query syntaxis.

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Een bestaande zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [maken](search-create-service-portal.md) of zoeken 

> [!Note]
> U kunt de gratis service voor deze zelf studie gebruiken. Een gratis zoek service beperkt u tot drie indexen, drie Indexeer functies en drie gegevens bronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u over voldoende ruimte beschikt om de nieuwe resources te accepteren.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechter muisknop op het zip-bestand en selecteer **Alles uitpakken**. Er zijn 14 bestanden van verschillende typen. U gebruikt 7 voor deze oefening.

## <a name="1---create-services"></a>1-services maken

In deze zelf studie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's, Cognitive Services op de back-end voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Deze zelf studie bevindt zich onder de gratis toewijzing van 20 trans acties per Indexeer functie per dag op Cognitive Services, dus de enige services die u moet maken, zijn zoeken en opslag.

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

1. Klik op **+ container** om een container te maken en geef deze de naam *tandwiel-Search-demo*.

1. Selecteer *tandwiel-Search-demo* en klik vervolgens op **uploaden** om de map te openen waarin u de bestanden hebt opgeslagen. Selecteer alle niet-afbeeldings bestanden. U moet 7 bestanden hebben. Klik op **OK** om te uploaden.

   ![Voorbeeld bestanden uploaden](media/cognitive-search-tutorial-blob/sample-files.png "Voorbeeld bestanden uploaden")

1. Voordat u Azure Storage verlaat, moet u een connection string ophalen zodat u een verbinding in azure Cognitive Search kunt formuleren. 

   1. Ga terug naar de pagina overzicht van uw opslag account (we hebben *blobstragewestus* als voor beeld gebruikt). 
   
   1. Selecteer in het navigatie deel venster links de optie **toegangs sleutels** en kopieer een van de verbindings reeksen. 

   De connection string is een URL die vergelijkbaar is met het volgende voor beeld:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de connection string op in Klad blok. U hebt deze later nodig bij het instellen van de gegevens bron verbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor de verwerking van natuurlijke taal en afbeelding. Als u een echt prototype of project wilt volt ooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search), zodat u het kunt koppelen aan index bewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overs Laan omdat Azure Cognitive Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis trans acties per Indexeer functie uitvoert. Omdat in deze zelf studie 7 trans acties worden gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md)voor meer informatie.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om deze procedure te volt ooien. 

Net als bij Azure Blob-opslag neemt het even de tijd om de toegangs sleutel te verzamelen. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eind punt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en-URL voor Azure Cognitive Search ophalen

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en haal de naam van uw zoek service op in de pagina **overzicht** van de zoek service. U kunt uw service naam bevestigen door de URL van het eind punt te controleren. Als uw eind punt-URL is `https://mydemo.search.windows.net`, wordt uw service naam `mydemo`.

2. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de query sleutel op. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

   ![De service naam en de beheer-en query sleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Alle aanvragen vereisen een API-sleutel in de header van elke aanvraag die naar uw service wordt verzonden. Een geldige sleutel brengt een vertrouwens relatie tot stand, op basis van aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="2---start-a-notebook"></a>2: een notitie blok starten

Maak het notitie blok met behulp van de volgende instructies of down load een volledig notitie blok van [Azure-Search-python-samples opslag plaats](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Gebruik Anaconda Navigator om Jupyter Notebook te starten en een nieuw python 3-notebook te maken.

Voer in uw notebook dit script uit om de bibliotheken te laden die worden gebruikt voor het werken met JSON en het formuleren van HTTP-aanvragen.

```python
import json
import requests
from pprint import pprint
```

Definieer in hetzelfde notitie blok de namen voor de gegevens bron, index, Indexer en vaardigheidset. Voer dit script uit om de namen in te stellen voor deze zelf studie.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

Vervang in het volgende script de tijdelijke aanduidingen voor uw zoek service (uw-zoek service naam) en de beheer-API-sleutel (uw-beheer-API-sleutel) en voer deze uit om het eind punt van de zoek service in te stellen.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3: de pijp lijn maken

In azure Cognitive Search treedt AI-verwerking op tijdens het indexeren (of gegevens opname). In dit deel van het scenario worden vier objecten gemaakt: gegevens bron, index definitie, vaardig heden, Indexeer functie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

Een [gegevens bron object](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biedt de Connection String aan de BLOB-container met de bestanden.

Vervang in het volgende script de tijdelijke aanduiding uw-BLOB-RESOURCE-CONNECTION-STRING door de connection string voor de blob die u in de vorige stap hebt gemaakt. Vervang de tekst van de tijdelijke aanduiding voor de container. Voer vervolgens het script uit om een gegevens bron te maken met de naam `cogsrch-py-datasource`.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag moet de status code 201 bevestigen van geslaagde pogingen retour neren.

Controleer in de Azure Portal op de pagina zoek service dashboard of de cogsrch-py-data source wordt weer gegeven in de lijst **gegevens bronnen** . Klik op **vernieuwen** om de pagina bij te werken.

![De tegel gegevens bronnen in de portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "De tegel gegevens bronnen in de portal")

### <a name="step-2-create-a-skillset"></a>Stap 2: een vaardig heden maken

In deze stap definieert u een reeks verrijkings stappen die op uw gegevens worden toegepast. We noemen elke verrijkingsstap een *vaardigheid* en de reeks verrijkingsstappen een *set vaardigheden*. In deze zelf studie worden [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) voor de vaardig heden gebruikt:

+ [Entiteits herkenning](cognitive-search-skill-entity-recognition.md) voor het extra heren van de namen van organisaties uit inhoud in de BLOB-container.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen. 

Voer het volgende script uit om een vaardig heden met de naam `cogsrch-py-skillset`te maken.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag moet de status code 201 bevestigen van geslaagde pogingen retour neren.

De sleutel frase extractie vaardigheid wordt toegepast voor elke pagina. Als u de context instelt op `"document/pages/*"`, voert u deze verrijker uit voor elk lid van de matrix document/pages (voor elke pagina in het document).

Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens de verwerking wordt elk document door Azure Cognitive Search gekraakt om inhoud te lezen uit verschillende bestands indelingen. Tekst gevonden in het bron bestand wordt in een `content` veld geplaatst, één voor elk document. Stel daarom de invoer in als `"/document/content"`.

Een grafische weergave van de set vaardigheden wordt hieronder weergegeven.

![Een vaardig heden begrijpen](media/cognitive-search-tutorial-blob/skillset.png "Een vaardig heden begrijpen")

Uitvoer kan worden toegewezen aan een index die wordt gebruikt als invoer voor een stroomafwaartse vaardigheid, of beide, alsof het het geval is met taal code. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="step-3-create-an-index"></a>Stap 3: een index maken

In deze sectie definieert u het index schema door de velden op te geven die moeten worden meegenomen in de Doorzoek bare index en de zoek kenmerken voor elk veld in te stellen. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Voer dit script uit om de index met de naam `cogsrch-py-index`te maken.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag moet de status code 201 bevestigen van geslaagde pogingen retour neren.

Zie [Create Index (Azure Cognitive Search rest API)](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over het definiëren van een index.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: een Indexeer functie maken en uitvoeren

Een [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) verstuurt de pijp lijn. De drie onderdelen die u tot nu toe hebt gemaakt (gegevens bron, vaardig heden, index) zijn invoer naar een Indexeer functie. Het maken van de Indexeer functie op Azure Cognitive Search is de gebeurtenis die de volledige pijp lijn in beweging zet. 

U moet veld toewijzingen definiëren om deze objecten te koppelen aan een Indexeer functie.

+ De fieldMappings worden vóór de vaardig heden verwerkt, waarbij bron velden van de gegevens bron worden toegewezen aan de doel velden in een index. Als veld namen en-typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

+ De outputFieldMappings worden verwerkt na de vaardig heden en verwijzen naar sourceFieldNames die niet bestaan totdat het document wordt gekraakt of verrijkt. De targetFieldName is een veld in een index.

Naast het koppelen van invoer naar uitvoer, kunt u ook veld toewijzingen gebruiken om gegevens structuren samen te voegen. Zie [hoe u verrijkte velden aan een Doorzoek bare index kunt toewijzen](cognitive-search-output-field-mapping.md)voor meer informatie.

Voer dit script uit om een Indexeer functie met de naam `cogsrch-py-indexer`te maken.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag moet een status code van 201 binnenkort retour neren, maar het kan enkele minuten duren voordat de verwerking is voltooid. Hoewel de gegevensset klein is, zijn analytische vaardig heden, zoals het analyseren van afbeeldingen, reken kracht en tijd.

U kunt de status van de [Indexeer functie bewaken](#check-indexer-status) om te bepalen wanneer de Indexeer functie wordt uitgevoerd of voltooid.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er een probleem optreedt bij het openen van de gegevens, het toewijzen van invoer en uitvoer, of met de volg orde van bewerkingen, wordt deze in deze fase weer gegeven. Als u de pijp lijn opnieuw wilt uitvoeren met code-of script wijzigingen, moet u mogelijk eerst objecten verwijderen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="about-the-request-body"></a>Over de hoofd tekst van de aanvraag

Het script stelt `"maxFailedItems"` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

Let ook op de instructie `"dataToExtract":"contentAndMetadata"` in de configuratieparameters. Met deze instructie geeft u de Indexeer functie de inhoud uit verschillende bestands indelingen en de meta gegevens die zijn gerelateerd aan elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van `"imageAction":"generateNormalizedImages"`, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn Inge sloten in de documenten (een installatie kopie in een PDF-bestand) en afbeeldingen die zijn gevonden in de gegevens bron, bijvoorbeeld een JPG-bestanden.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4: indexeren controleren

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de verwerking van de Indexeer functie is voltooid, voert u het volgende script uit.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Controleer in het antwoord op de waarde ' lastResult ' voor de waarden ' status ' en ' endTime '. Voer periodiek het script uit om de status te controleren. Als de Indexeer functie is voltooid, wordt de status ingesteld op ' geslaagd ', wordt ' endTime ' opgegeven en bevat het antwoord eventuele fouten en waarschuwingen die zijn opgetreden tijdens het verrijken.

![Indexeer functie is gemaakt](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexeer functie is gemaakt")

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. Veel waarschuwingen zijn onschadelijk. Als u bijvoorbeeld een JPEG-bestand indexeert dat geen tekst bevat, ziet u de waarschuwing in deze scherm opname.

![Voor beeld van Indexeer functie waarschuwing](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Voor beeld van Indexeer functie waarschuwing")

## <a name="5---search"></a>5-zoeken

Voer nadat het indexeren is voltooid query's uit waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Standaard worden de belangrijkste 50 resultaten door Azure Cognitive Search geretourneerd. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [pagina resultaten in Azure Cognitive Search](search-pagination-page-layout.md)voor instructies.

Als verificatie stap haalt u de index definitie op waarin alle velden worden weer gegeven.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De resultaten moeten er ongeveer uitzien als in het volgende voor beeld. In de scherm afbeelding wordt alleen een deel van het antwoord weer gegeven.

![Query-index voor alle velden](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Query's uitvoeren op de index voor alle velden")

De uitvoer is het schema van de index met de naam, het type en de kenmerken van elk veld.

Verzend een tweede query voor `"*"` om alle inhoud te retourneren van één veld, zoals `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De resultaten moeten er ongeveer uitzien als in het volgende voor beeld. In de scherm afbeelding wordt alleen een deel van het antwoord weer gegeven.

![Query index voor de inhoud van organisaties](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Query's uitvoeren op de index om de inhoud van organisaties te retour neren")

Herhaal dit voor aanvullende velden: inhoud, language code, zinsdelen en organisaties in deze oefening. U kunt meerdere velden retourneren via `$select` met behulp van een door komma's gescheiden lijst.

U kunt GET of POST gebruiken, afhankelijk van de complexiteit en lengte van de queryreeks. Zie [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Query's uitvoeren met de REST API) voor meer informatie.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele stadia van de ontwikkeling kunt u het beste de objecten uit Azure Cognitive Search verwijderen en uw code zo instellen dat deze opnieuw worden opgebouwd. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U kunt de portal gebruiken om indexen, Indexeer functies, gegevens bronnen en vaardig heden te verwijderen. Wanneer u de Indexeer functie verwijdert, kunt u eventueel de index, de vaardig heden en de gegevens bron selectief verwijderen.

![Zoek objecten verwijderen](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Zoek objecten verwijderen in de portal")

U kunt ze ook verwijderen met een script. Het volgende script toont hoe u een vaardig heden verwijdert. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Ingebouwde vaardig heden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met definities van vakkennis en een manier om vaardig heden te koppelen via invoer en uitvoer. U hebt ook geleerd dat `outputFieldMappings` in de definitie van de Indexeer functie is vereist voor het door sturen van verrijkte waarden van de pijp lijn naar een Doorzoek bare index op een Azure Cognitive Search-service.

Ten slotte hebt u geleerd hoe u de resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere herhalingen. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. Deze release bevat een mechanisme voor het weergeven van interne constructies (verrijkte documenten die zijn gemaakt door het systeem). U hebt ook geleerd hoe u de Indexeer functie-status kunt controleren en welke objecten moeten worden verwijderd voordat u een pijp lijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling alle resources of resource groepen in het navigatie deel venster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkings pijplijn, gaan we kijken naar de definities van vakkennis en individuele vaardig heden.

> [!div class="nextstepaction"]
> [Een vaardig heden maken](cognitive-search-defining-skillset.md)
