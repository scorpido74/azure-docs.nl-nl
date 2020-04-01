---
title: 'Zelfstudie: Python en AI via Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Stap door een voorbeeld van tekstextractie en verwerking van natuurlijke taal over inhoud in Blob-opslag met behulp van een Jupyter Python-notitieblok en de AZURE Cognitive Search REST API's.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: e7708b0043b7f5baf2c12e813306595cc358a01d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78194051"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelfstudie: Python en AI gebruiken om doorzoekbare inhoud te genereren uit Azure-blobs

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kan een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken die handig is voor scenario's voor zoeken in volledige tekst of kennismijnbouw. Hoewel een pijplijn afbeeldingen kan verwerken, richt deze Python-zelfstudie zich op tekst, waarbij taaldetectie en verwerking van natuurlijke talen worden toegepast om nieuwe velden te maken die u gebruiken in query's, facetten en filters.

Deze zelfstudie gebruikt Python en de [API's voor zoekrest](https://docs.microsoft.com/rest/api/searchservice/) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met hele documenten (ongestructureerde tekst) zoals PDF, HTML, DOCX en PPTX in Azure Blob-opslag.
> * Definieer een pijplijn die tekst extraheert, taal detecteert, entiteiten herkent en sleutelzinnen detecteert.
> * Definieer een index om de uitvoer op te slaan (ruwe inhoud, plus door pijplijn gegenereerde naamwaardeparen).
> * Voer de pijplijn uit om transformaties en analyses te starten en de index te maken en te laden.
> * Bekijk de resultaten met behulp van zoeken met volledige tekst en een syntaxis van uitgebreide query's.

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Een](search-create-service-portal.md) [bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) maken of zoeken 

> [!Note]
> U gebruik maken van de gratis service voor deze tutorial. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerders en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u ruimte hebt op uw service om de nieuwe bronnen te accepteren.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **Downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechtermuisknop op het zip-bestand en selecteer **Alles uitpakken**. Er zijn 14 bestanden van verschillende soorten. Je gebruikt er 7 voor deze oefening.

## <a name="1---create-services"></a>1 - Services maken

Deze zelfstudie maakt gebruik van Azure Cognitive Search voor indexering en query's, Cognitive Services op de backend voor AI-verrijking en Azure Blob-opslag om de gegevens te verstrekken. Deze zelfstudie blijft onder de gratis toewijzing van 20 transacties per indexer per dag op Cognitive Services, dus de enige services die u moet maken zijn zoeken en opslaan.

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

1. Klik **op + Container** om een container te maken en deze *cog-search-demo een*naam te geven.

1. Selecteer *cog-search-demo* en klik op **Uploaden** om de map te openen waar u de downloadbestanden hebt opgeslagen. Selecteer alle niet-afbeeldingsbestanden. Je zou 7 dossiers moeten hebben. Klik op **OK** om te uploaden.

   ![Voorbeeldbestanden uploaden](media/cognitive-search-tutorial-blob/sample-files.png "Voorbeeldbestanden uploaden")

1. Voordat u Azure Storage verlaat, krijgt u een verbindingstekenreeks zodat u een verbinding formuleren in Azure Cognitive Search. 

   1. Blader terug naar de overzichtspagina van uw opslagaccount (we *gebruikten blobstragewestus* als voorbeeld). 
   
   1. Selecteer **access-toetsen** in het linkernavigatiedeelvenster en kopieer een van de verbindingstekenreeksen. 

   De verbindingstekenreeks is een URL die lijkt op het volgende voorbeeld:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de verbindingstekenreeks op in Kladblok. U hebt het later nodig bij het instellen van de gegevensbronverbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor natuurlijke taal- en beeldverwerking. Als het doel was om een echt prototype of project te voltooien, zou u op dit punt cognitieve services inrichten (in dezelfde regio als Azure Cognitive Search), zodat u het koppelen aan indexeringsbewerkingen.

Voor deze oefening u echter resourceprovisioning overslaan omdat Azure Cognitive Search achter de schermen verbinding kan maken met Cognitive Services en u 20 gratis transacties per indexerrun geven. Aangezien deze zelfstudie 7 transacties gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten, plannen op het inrichten van Cognitive Services op de pay-as-you-go S0 laag. Zie [Cognitieve services bijvoegen](cognitive-search-attach-cognitive-services.md)voor meer informatie.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De derde component is Azure Cognitive Search, die u [maken in de portal.](search-create-service-portal.md) U de gratis laag gebruiken om deze walkthrough te voltooien. 

Neem, net als bij Azure Blob-opslag, even de tijd om de toegangssleutel te verzamelen. Verderop, wanneer u begint met het structureren van aanvragen, moet u het eindpunt en de api-sleutel voor beheerders verstrekken die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een api-sleutel voor beheerders en URL ophalen voor Azure Cognitive Search

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en krijg op de pagina **Overzicht** van uw zoekservice de naam van uw zoekservice. U uw servicenaam bevestigen door de URL van het eindpunt te bekijken. Als uw eindpunt-URL dat wel `https://mydemo.search.windows.net` `mydemo`was, zou uw servicenaam .

2. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal de querysleutel ook. Het is een aanbevolen manier om queryaanvragen uit te geven met alleen-lezen toegang.

   ![De servicenaam en -beheerder en querysleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Voor alle aanvragen is een api-toets nodig in de header van elk verzoek dat naar uw service wordt verzonden. Een geldige sleutel stelt vertrouwen, op basis van de aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="2---start-a-notebook"></a>2 - Een notitieblok starten

Maak het notitieblok met de volgende instructies of download een afgewerkt notitieblok van [Azure-Search-python-samples repo](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Gebruik Anaconda Navigator om Jupyter Notebook te starten en een nieuwe Python 3-notebook te maken.

Voer dit script in uw notitieblok uit om de bibliotheken te laden die worden gebruikt voor het werken met JSON en het formuleren van HTTP-aanvragen.

```python
import json
import requests
from pprint import pprint
```

Definieer in hetzelfde notitieblok de namen voor de gegevensbron, index, indexeren en vaardigheden. Voer dit script uit om de namen voor deze zelfstudie in te stellen.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

Vervang in het volgende script de tijdelijke aanduidingen voor uw zoekservice (YOUR-SEARCH-SERVICE-NAME) en admin API-sleutel (YOUR-ADMIN-API-KEY) en voer deze uit om het eindpunt van de zoekservice in te stellen.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3 - De pijplijn maken

In Azure Cognitive Search vindt AI-verwerking plaats tijdens het indexeren (of inname van gegevens). Dit deel van de walkthrough maakt vier objecten: gegevensbron, indexdefinitie, skillset, indexer. 

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

Een [gegevensbronobject](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biedt de verbindingstekenreeks met de Blob-container die de bestanden bevat.

Vervang in het volgende script de tijdelijke aanduiding YOUR-BLOB-RESOURCE-CONNECTION-STRING door de verbindingstekenreeks voor de blob die u in de vorige stap hebt gemaakt. Vervang de tijdelijke aanduidingstekst voor de container. Voer vervolgens het script uit om `cogsrch-py-datasource`een gegevensbron met de naam te maken.

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

Het verzoek moet een statuscode van 201 retourneren die het succes bevestigt.

Controleer in de Azure-portal op de dashboardpagina van de zoekservice of de cogsrch-py-databron wordt weergegeven in de lijst **Gegevensbronnen.** Klik **op Vernieuwen** om de pagina bij te werken.

![Tegel gegevensbronnen in de portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Tegel gegevensbronnen in de portal")

### <a name="step-2-create-a-skillset"></a>Stap 2: Een skillset maken

In deze stap definieert u een reeks verrijkingsstappen die u op uw gegevens moet toepassen. We noemen elke verrijkingsstap een *vaardigheid* en de reeks verrijkingsstappen een *set vaardigheden*. Deze zelfstudie maakt gebruik van [ingebouwde cognitieve vaardigheden](cognitive-search-predefined-skills.md) voor de skillset:

+ [Entiteitsherkenning](cognitive-search-skill-entity-recognition.md) voor het extraheren van de namen van organisaties uit inhoud in de blobcontainer.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen. 

Voer het volgende script uit `cogsrch-py-skillset`om een skillset te maken met de aangeroepen .

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

Het verzoek moet een statuscode van 201 retourneren die het succes bevestigt.

De sleutelzin extractie vaardigheid wordt toegepast voor elke pagina. Door de context `"document/pages/*"`in te stellen op , voert u deze verrijking uit voor elk lid van de document/paginaarray (voor elke pagina in het document).

Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens de verwerking kraakt Azure Cognitive Search elk document om inhoud uit verschillende bestandsindelingen te lezen. Tekst in het bronbestand wordt `content` in een veld geplaatst, één voor elk document. Stel daarom de `"/document/content"`invoer in als .

Een grafische weergave van de set vaardigheden wordt hieronder weergegeven.

![Een skillset begrijpen](media/cognitive-search-tutorial-blob/skillset.png "Een skillset begrijpen")

Uitgangen kunnen worden toegewezen aan een index, gebruikt als invoer voor een downstreamvaardigheid, of beide, zoals het geval is met taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

In deze sectie definieert u het indexschema door de velden op te geven die moeten worden opgenomen in de doorzoekbare index en de zoekkenmerken voor elk veld in te stellen. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Voer dit script uit `cogsrch-py-index`om de index met de naam te maken .

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

Het verzoek moet een statuscode van 201 retourneren die het succes bevestigt.

Zie [Index maken (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over het definiëren van een index.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Een indexer maken en uitvoeren

Een [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) stuurt de pijplijn aan. De drie componenten die u tot nu toe hebt gemaakt (gegevensbron, skillset, index) zijn ingangen naar een indexeerder. Het maken van de indexer op Azure Cognitive Search is de gebeurtenis die de hele pijplijn in beweging zet. 

Als u deze objecten in een indexer wilt koppelen, moet u veldtoewijzingen definiëren.

+ De veldtoewijzingen worden vóór de skillset verwerkt en worden bronvelden van de gegevensbron toewijzen aan doelvelden in een index. Als veldnamen en -typen aan beide uiteinden hetzelfde zijn, is er geen toewijzing vereist.

+ De outputFieldMappings worden verwerkt na de skillset, verwijzend naar sourceFieldNames die niet bestaan totdat het kraken van documenten of verrijking ze maakt. De targetFieldName is een veld in een index.

Naast het aansluiten van ingangen op uitgangen, u ook veldtoewijzingen gebruiken om gegevensstructuren af te vlakken. Zie [Verrijkte velden toewijzen aan een doorzoekbare index voor](cognitive-search-output-field-mapping.md)meer informatie.

Voer dit script uit om `cogsrch-py-indexer`een indexeerder met de naam te maken.

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

Het verzoek moet binnenkort een statuscode van 201 retourneren, maar de verwerking kan enkele minuten in beslag nemen. Hoewel de gegevensset klein is, zijn analytische vaardigheden, zoals beeldanalyse, rekenkundig intensief en nemen ze tijd in beslag.

U [de indexerstatus controleren](#check-indexer-status) om te bepalen wanneer de indexer wordt uitgevoerd of voltooid.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er een probleem is met de toegang tot de gegevens, het toewijzen van ingangen en uitvoer, of met de volgorde van bewerkingen, wordt deze in dit stadium weergegeven. Als u de pijplijn opnieuw wilt uitvoeren met code- of scriptwijzigingen, moet u mogelijk eerst objecten verwijderen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="about-the-request-body"></a>Over het verzoek orgaan

Het script stelt `"maxFailedItems"` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

Let ook op de instructie `"dataToExtract":"contentAndMetadata"` in de configuratieparameters. Deze instructie vertelt de indexer om de inhoud uit verschillende bestandsindelingen en de metagegevens met betrekking tot elk bestand te extraheren.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van `"imageAction":"generateNormalizedImages"`, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die in de documenten zijn ingesloten (denk aan een afbeelding in een PDF) als afbeeldingen in de gegevensbron, bijvoorbeeld een JPG-bestand.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitor indexering

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Voer het volgende script uit om erachter te komen of de indexerverwerking is voltooid.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Controleer in het antwoord de waarden 'lastResult' op de waarden 'status' en 'endTime'. Voer het script regelmatig uit om de status te controleren. Wanneer de indexer is voltooid, wordt de status ingesteld op "succes", wordt een "endTime" opgegeven en bevat het antwoord eventuele fouten en waarschuwingen die tijdens de verrijking zijn opgetreden.

![Indexer wordt gemaakt](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexer wordt gemaakt")

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. Veel waarschuwingen zijn goedaardig. Als u bijvoorbeeld een JPEG-bestand indexeert dat geen tekst heeft, ziet u de waarschuwing in deze schermafbeelding.

![Waarschuwing voor indexeren](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Waarschuwing voor indexeren")

## <a name="5---search"></a>5 - Zoeken

Voer nadat het indexeren is voltooid query's uit waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Azure Cognitive Search retourneert standaard de top 50-resultaten. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie Resultaten [van de pagina in Azure Cognitive Search](search-pagination-page-layout.md)voor instructies.

Als verificatiestap krijgt u de indexdefinitie met alle velden.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De resultaten moeten lijken op het volgende voorbeeld. De screenshot toont slechts een deel van het antwoord.

![Queryindex voor alle velden](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "De index voor alle velden opvragen")

De uitvoer is het schema van de index met de naam, het type en de kenmerken van elk veld.

Verzend een tweede query voor `"*"` om alle inhoud te retourneren van één veld, zoals `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De resultaten moeten lijken op het volgende voorbeeld. De screenshot toont slechts een deel van het antwoord.

![Query-index voor de inhoud van organisaties](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "De index opvragen om de inhoud van organisaties terug te geven")

Herhaal dit voor extra velden: inhoud, taalcode, sleutelzinnen en organisaties in deze oefening. U kunt meerdere velden retourneren via `$select` met behulp van een door komma's gescheiden lijst.

U kunt GET of POST gebruiken, afhankelijk van de complexiteit en lengte van de queryreeks. Zie [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Query's uitvoeren met de REST API) voor meer informatie.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele ontwikkelingsfase is de meest praktische benadering voor ontwerpiteratie het verwijderen van de objecten uit Azure Cognitive Search en uw code in staat te stellen ze opnieuw op te bouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U de portal gebruiken om indexen, indexeerders, gegevensbronnen en skillsets te verwijderen. Wanneer u de indexer verwijdert, u de index, skillset en gegevensbron tegelijkertijd selectief verwijderen.

![Zoekobjecten verwijderen](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Zoekobjecten in de portal verwijderen")

U ze ook verwijderen met behulp van een script. In het volgende script ziet u hoe u een skillset verwijdert. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) werden geïntroduceerd, samen met skillset definities en een manier om vaardigheden aan elkaar te ketenen door middel van ingangen en uitgangen. U hebt `outputFieldMappings` ook geleerd dat in de indexerdefinitie vereist is voor het routeren van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Ten slotte heb je geleerd hoe je de resultaten te testen en het systeem te resetten voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. Deze release bevat een mechanisme voor het weergeven van interne constructies (verrijkte documenten die zijn gemaakt door het systeem). U hebt ook geleerd hoe u de indexerstatus controleren en welke objecten moeten worden verwijderd voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling Alle resources of Resourcegroepen in het linkernavigatiedeelvenster.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, bekijken we vaardigheden en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Hoe maak je een skillset](cognitive-search-defining-skillset.md)
