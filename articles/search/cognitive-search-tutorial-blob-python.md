---
title: 'Python-zelf studie: Cognitive Services aanroepen in een AI-pijp lijn'
titleSuffix: Azure Cognitive Search
description: Bekijk een voor beeld van het uitpakken van gegevens, natuurlijke taal en afbeelding AI-verwerking in azure Cognitive Search met behulp van een Jupyter python-notebook. Geëxtraheerde gegevens worden geïndexeerd en eenvoudig geopend door de query.
manager: nitinme
author: LisaLeib
ms.author: v-lilei
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: bb36ae551c48fc53756933e78ff0212f8ec1cdeb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790209"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-cognitive-search-enrichment-pipeline"></a>Python-zelf studie: Cognitive Services-API's aanroepen in een Azure Cognitive Search-verrijkings pijplijn

In deze zelf studie leert u de mechanismen voor het verrijken van de programmering van gegevens in azure Cognitive Search met *cognitieve vaardig heden*. Vaardig heden worden ondersteund door NLP (natuurlijke taal verwerking) en mogelijkheden voor de analyse van installatie kopieën in Cognitive Services. Via de vaardig heden-compositie en-configuratie kunt u tekst-en tekst representaties van een afbeelding of gescand document bestand extra heren. U kunt ook taal, entiteiten, sleutel zinnen en meer detecteren. Het resultaat is uitgebreide aanvullende inhoud in een zoek index die is gemaakt met AI-verrijkingen in een indexerings pijplijn. 

In deze zelf studie gebruikt u python om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een indexeringspijplijn maken die voorbeeldgegevens onderweg naar een index verrijkt
> * Pas ingebouwde vaardigheden toe: herkenning van entiteiten, taaldetectie, tekstbewerkingen en extractie van sleuteltermen
> * Vaardigheden aan elkaar koppelen door invoeren aan uitvoeren toe te wijzen in een set vaardigheden
> * Aanvragen uitvoeren en resultaten bekijken
> * De index en indexeerfuncties opnieuw instellen voor verdere ontwikkeling

Output is een volledige tekst zoekopdracht bare index op Azure Cognitive Search. U kunt de index uitbreiden met andere standaardfuncties, zoals [synoniemen](search-synonyms.md), [scoreprofielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyses](search-analyzers.md) en [filters](search-filters.md). 

Deze zelf studie wordt uitgevoerd op de gratis service, maar het aantal gratis trans acties is beperkt tot 20 documenten per dag. Als u deze zelf studie meer dan één keer in dezelfde dag wilt uitvoeren, gebruikt u een kleinere set bestanden, zodat u in meer uitvoeringen kunt passen.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie worden de volgende services, hulpprogram ma's en gegevens gebruikt. 

+ [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor het opslaan van de voorbeeld gegevens. Zorg ervoor dat het opslag account zich in dezelfde regio bevindt als Azure Cognitive Search.

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), met python 3. x-en Jupyter-notebooks.

+ De [voorbeeld gegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) bestaan uit een kleine set verschillende typen. 

+ [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Als u met uw Azure Cognitive Search-service wilt communiceren, hebt u de service-URL en een toegangs sleutel nodig. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. In **instellingen** > **sleutels**, een beheerders sleutel ophalen voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Een geldige sleutel brengt een vertrouwens relatie tot stand, op basis van aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="prepare-sample-data"></a>Voorbeeld gegevens voorbereiden

De verrijkingspijplijn haalt gegevens uit Azure-gegevensbronnen. Bron gegevens moeten afkomstig zijn van een ondersteund gegevens bron type van een [Azure Cognitive Search indexer](search-indexer-overview.md). In dit voorbeeld gebruiken we blobopslag om meerdere inhoudstypen te laten zien.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op **blobs**en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om voorbeeld gegevens te bevatten. U kunt het niveau van open bare toegang instellen op een van de geldige waarden.

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk om de voorbeeld bestanden te uploaden die u in een vorige stap hebt gedownload.

   ![Bronbestanden in Azure-blobopslag](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Nadat de voorbeeldbestanden zijn geladen, haalt u de containernaam en een verbindingsreeks voor de Blob-opslag op. U kunt dat doen door in de Azure-portal naar uw opslagaccount te navigeren. Klik op **toegangs sleutels**en kopieer het veld **verbindings reeks** .

De connection string heeft de volgende indeling: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Blijf de connection string het handigst. U hebt deze in een toekomstige stap nodig.

Er zijn andere manieren om de verbindingsreeks op te geven, zoals een Shared Access Signature bieden. Zie [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Azure Blob Storage indexeren) voor meer informatie over referenties voor gegevensbronnen.

## <a name="create-a-jupyter-notebook"></a>Een Jupyter-notebook maken

> [!Note]
> In dit artikel wordt beschreven hoe u een gegevens bron, index, Indexeer functie en vakkennisset maakt met behulp van een reeks python-scripts. Als u het voor beeld van een volledig notitie blok wilt downloaden, gaat u naar [Azure-Search-python-samples opslag plaats](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Gebruik Anaconda Navigator om Jupyter Notebook te starten en een nieuw python 3-notebook te maken.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

Voer in uw notebook dit script uit om de bibliotheken te laden die worden gebruikt voor het werken met JSON en het formuleren van HTTP-aanvragen.

```python
import json
import requests
from pprint import pprint
```

Definieer vervolgens de namen voor de gegevens bron, index, Indexer en vaardigheidset. Voer dit script uit om de namen in te stellen voor deze zelf studie.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Bij een gratis service bent u beperkt tot drie indexen, Indexeer functies en gegevens bronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Zorg ervoor dat u over voldoende ruimte beschikt om nieuwe objecten te maken voordat u verder gaat.

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

## <a name="create-a-data-source"></a>Een gegevensbron maken

Nu dat uw services en bronbestanden zijn voorbereid, kunt u de onderdelen van uw indexeringspijplijn samenstellen. Begin met een gegevens bron object dat Azure Cognitive Search vertelt hoe externe bron gegevens moeten worden opgehaald.

Vervang in het volgende script de tijdelijke aanduiding uw-BLOB-RESOURCE-CONNECTION-STRING door de connection string voor de blob die u in de vorige stap hebt gemaakt. Voer vervolgens het script uit om een gegevens bron te maken met de naam `cogsrch-py-datasource`.

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
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag moet de status code 201 bevestigen van geslaagde pogingen retour neren.

Controleer in de Azure Portal op de pagina zoek service dashboard of de cogsrch-py-data source wordt weer gegeven in de lijst **gegevens bronnen** . Klik op **vernieuwen** om de pagina bij te werken.

![De tegel gegevens bronnen in de portal](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "De tegel gegevens bronnen in de portal")

## <a name="create-a-skillset"></a>Een set vaardigheden maken

In deze stap definieert u een reeks verrijkings stappen die op uw gegevens worden toegepast. We noemen elke verrijkingsstap een *vaardigheid* en de reeks verrijkingsstappen een *set vaardigheden*. In deze zelf studie worden [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) voor de vaardig heden gebruikt:

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Entiteits herkenning](cognitive-search-skill-entity-recognition.md) voor het extra heren van de namen van organisaties uit inhoud in de BLOB-container.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen. 

### <a name="python-script"></a>Python-script
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

## <a name="create-an-index"></a>Een index maken

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

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Een indexeerfunctie maken, velden toewijzen en transformaties uitvoeren

Tot nu toe hebt u een gegevens bron, een vaardig heden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. U moet veld toewijzingen definiëren om deze objecten te koppelen aan een Indexeer functie.

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

De aanvraag zou snel de status code 201 kunnen retour neren, maar het kan enkele minuten duren voordat de verwerking is voltooid. Hoewel de gegevensset klein is, zijn analytische vaardig heden, zoals het analyseren van afbeeldingen, reken kracht en tijd.

Gebruik het script [indexeerere status controleren](#check-indexer-status) in de volgende sectie om te bepalen wanneer het Indexeer proces is voltooid.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er een probleem optreedt bij het openen van de gegevens, het toewijzen van invoer en uitvoer, of met de volg orde van bewerkingen, wordt deze in deze fase weer gegeven. Als u de pijp lijn opnieuw wilt uitvoeren met code-of script wijzigingen, moet u mogelijk eerst objecten verwijderen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="explore-the-request-body"></a>De aanvraagbody verkennen

Het script stelt `"maxFailedItems"` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

Let ook op de instructie `"dataToExtract":"contentAndMetadata"` in de configuratieparameters. Met deze instructie geeft u de Indexeer functie de inhoud uit verschillende bestands indelingen en de meta gegevens die zijn gerelateerd aan elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van `"imageAction":"generateNormalizedImages"`, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn Inge sloten in de documenten (een installatie kopie in een PDF-bestand) en afbeeldingen die zijn gevonden in de gegevens bron, bijvoorbeeld een JPG-bestanden.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>De status van de indexeerfunctie controleren

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de verwerking van de Indexeer functie is voltooid, voert u het volgende script uit.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Controleer in het antwoord op de waarde ' lastResult ' voor de waarden ' status ' en ' endTime '. Voer periodiek het script uit om de status te controleren. Als de Indexeer functie is voltooid, wordt de status ingesteld op ' geslaagd ', wordt ' endTime ' opgegeven en bevat het antwoord eventuele fouten en waarschuwingen die zijn opgetreden tijdens het verrijken.

![Indexeer functie is gemaakt](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Indexeer functie is gemaakt")

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelf studie zijn de waarschuwingen onschadelijk. Een van de JPEG-bestanden die geen tekst bevat, toont bijvoorbeeld de waarschuwing in deze scherm opname.

![Voor beeld van Indexeer functie waarschuwing](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Voor beeld van Indexeer functie waarschuwing")

## <a name="query-your-index"></a>Een query uitvoeren in uw index

Voer nadat het indexeren is voltooid query's uit waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Standaard worden de belangrijkste 50 resultaten door Azure Cognitive Search geretourneerd. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [pagina resultaten in Azure Cognitive Search](search-pagination-page-layout.md)voor instructies.

Voer als verificatiestap query's uit op de index voor alle velden.

```python
# Query the index for all fields
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

In de vroege experimentele stadia van de ontwikkeling van pijp lijnen is het verwijderen van de objecten uit Azure Cognitive Search de meest praktische benadering van het maken van een nieuwe fase en staat u toe dat uw code opnieuw wordt gemaakt. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

Uw documenten opnieuw indexeren met de nieuwe definities:

1. Verwijder de index om persistente gegevens te verwijderen. Verwijder de indexeerfunctie om deze in de service opnieuw te maken.
2. Wijzig de vaardig heden-en index definities.
3. Maak de index en indexeerfunctie opnieuw in de service om de pijplijn uit te voeren.

U kunt de portal gebruiken om indexen, Indexeer functies en vaardig heden te verwijderen. Wanneer u de Indexeer functie verwijdert, kunt u eventueel de index, de vaardig heden en de gegevens bron selectief verwijderen.

![Zoek objecten verwijderen](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Zoek objecten verwijderen in de portal")

U kunt ze ook verwijderen met een script. Met het volgende script wordt de vaardig heden verwijderd die we hebben gemaakt. U kunt de aanvraag voor het verwijderen van de index, de indexer en de gegevens bron eenvoudig wijzigen.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Naarmate uw code meer vormt krijgt, is het raadzaam om uw herbouwstrategie te verfijnen. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Ingebouwde vaardig heden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met definities van vakkennis en een manier om vaardig heden te koppelen via invoer en uitvoer. U hebt ook geleerd dat `outputFieldMappings` in de definitie van de Indexeer functie is vereist voor het door sturen van verrijkte waarden van de pijp lijn naar een Doorzoek bare index op een Azure Cognitive Search-service.

Ten slotte hebt u geleerd hoe u de resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere herhalingen. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. Deze release bevat een mechanisme voor het weergeven van interne constructies (verrijkte documenten die zijn gemaakt door het systeem). U hebt ook geleerd hoe u de Indexeer functie-status kunt controleren en welke objecten moeten worden verwijderd voordat u een pijp lijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om na een zelf studie op te schonen, is door de resource groep te verwijderen met de Azure Cognitive Search-service en Azure Blob service. Als u beide services in dezelfde groep hebt geplaatst, verwijdert u de resource groep zodanig dat alles permanent wordt verwijderd, inclusief de services en eventuele opgeslagen inhoud die u voor deze zelf studie hebt gemaakt. De naam van de resourcegroep staat in de portal op de pagina Overzicht van elke service.

## <a name="next-steps"></a>Volgende stappen

De pijplijn uitbreiden of aanpassen met aangepaste vaardigheden. Door een aangepaste vaardigheid te maken en deze toe te voegen aan een set vaardigheden, kunt u zelfgeschreven tekst- of afbeeldingsanalyse integreren.

> [!div class="nextstepaction"]
> [Voor beeld: een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)
