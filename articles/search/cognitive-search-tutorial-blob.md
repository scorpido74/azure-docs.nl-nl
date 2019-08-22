---
title: 'REST zelf studie: Cognitive Services aanroepen in een pijp lijn met AI-verrijking-Azure Search'
description: Stap voor een voor beeld van gegevens extractie, natuurlijke taal en afbeelding AI-verwerking in Azure Search indexeren voor gegevens extractie en trans formatie via JSON-blobs met behulp van Postman en de REST API.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 07fd1bbab68dc9abcd6e7f8df7f36f7977ff5b3a
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638922"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>REST zelf studie: Cognitive Services-API's aanroepen in een Azure Search Indexing-pijp lijn

In deze zelfstudie leert u de mechanismen achter gegevensverrijking programmeren in Azure Search met behulp van *cognitieve vaardigheden*. Vaardig heden worden ondersteund door NLP (natuurlijke taal verwerking) en mogelijkheden voor de analyse van installatie kopieën in Cognitive Services. Via de vaardig heden-compositie en-configuratie kunt u tekst-en tekst representaties van een afbeelding of gescand document bestand extra heren. U kunt ook taal, entiteiten, sleutel zinnen en meer detecteren. Het eind resultaat is uitgebreide extra inhoud in een Azure Search index, gemaakt met AI-verrijkingen in een Indexing-pijp lijn. 

In deze zelfstudie maakt u REST API-aanroepen om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een indexeringspijplijn maken die voorbeeldgegevens onderweg naar een index verrijkt
> * Pas ingebouwde vaardigheden toe: herkenning van entiteiten, taaldetectie, tekstbewerkingen en extractie van sleuteltermen
> * Vaardigheden aan elkaar koppelen door invoeren aan uitvoeren toe te wijzen in een set vaardigheden
> * Aanvragen uitvoeren en resultaten bekijken
> * De index en indexeerfuncties opnieuw instellen voor verdere ontwikkeling

Uitvoer is een volledige doorzoekbare index in Azure Search. U kunt de index uitbreiden met andere standaardfuncties, zoals [synoniemen](search-synonyms.md), [scoreprofielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyses](search-analyzers.md) en [filters](search-filters.md).

Deze zelf studie wordt uitgevoerd op de gratis service, maar het aantal gratis trans acties is beperkt tot 20 documenten per dag. Als u deze zelf studie meer dan één keer in dezelfde dag wilt uitvoeren, gebruikt u een kleinere set bestanden, zodat u in meer uitvoeringen kunt passen.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in Azure Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met Azure Search prijzen](https://go.microsoft.com/fwlink/?linkid=2042400).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie worden de volgende services, hulpprogram ma's en gegevens gebruikt. 

+ [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor het opslaan van de voorbeeld gegevens. Zorg ervoor dat het opslag account zich in dezelfde regio bevindt als Azure Search.

+ [Postman desktop-app](https://www.getpostman.com/) wordt gebruikt om rest-aanroepen naar Azure Search te maken.

+ De [voorbeeld gegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) bestaan uit een kleine set verschillende typen. 

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="prepare-sample-data"></a>Voorbeeld gegevens voorbereiden

De verrijkingspijplijn haalt gegevens uit Azure-gegevensbronnen. Brongegevens moeten afkomstig zijn van een ondersteund type gegevensbron van een [Azure Search-indexeerfunctie](search-indexer-overview.md). Azure Table Storage wordt niet ondersteund voor cognitieve Zoek opdrachten. In dit voorbeeld gebruiken we blobopslag om meerdere inhoudstypen te laten zien.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op blobs en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om voorbeeld gegevens te bevatten. U kunt het niveau van open bare toegang instellen op een van de geldige waarden.

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk om de voorbeeld bestanden te uploaden die u in een vorige stap hebt gedownload.

   ![Bronbestanden in Azure-blobopslag](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Nadat de voorbeeldbestanden zijn geladen, haalt u de containernaam en een verbindingsreeks voor de Blob-opslag op. U kunt dat doen door in de Azure-portal naar uw opslagaccount te navigeren. Ga naar **Toegangssleutels** en kopieer het veld **Verbindingsreeks**.

   De verbindingsreeks moet een URL zijn die er ongeveer als volgt uitziet:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Er zijn andere manieren om de verbindingsreeks op te geven, zoals een Shared Access Signature bieden. Zie [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Azure Blob Storage indexeren) voor meer informatie over referenties voor gegevensbronnen.

## <a name="set-up-postman"></a>Postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met dit hulp programma, raadpleegt u [Azure Search rest-Api's verkennen met behulp van Postman](search-get-started-postman.md).

De aanvraag methoden die in deze zelf studie worden gebruikt, zijn **post**, **put**en **Get**. De header sleutels zijn ' content-type ' ingesteld op ' application/json ' en een ' API-Key ' die is ingesteld op een beheerders sleutel van uw Azure Search service. In de hoofdtekst plaatst u de werkelijke inhoud van uw aanroep. 

  ![Semi-gestructureerde zoekopdracht](media/search-semi-structured-data/postmanoverview.png)

We gebruiken postman om vier API-aanroepen naar uw zoek service te maken voor het maken van een gegevens bron, een vaardig heden, een index en een Indexeer functie. De gegevensbron bevat een verwijzing naar uw opslagaccount en uw JSON-gegevens. Uw zoekservice maakt de verbinding tijdens het laden van de gegevens.


## <a name="create-a-data-source"></a>Een gegevensbron maken

Nu dat uw services en bronbestanden zijn voorbereid, kunt u de onderdelen van uw indexeringspijplijn samenstellen. Begin met een [gegevensbronobject](https://docs.microsoft.com/rest/api/searchservice/create-data-source) dat Azure Search vertelt hoe het externe gegevens moet ophalen.

Geef in de aanvraagheader de naam van de service op die u hebt gebruikt bij het maken van de Azure Search-service en de API-sleutel die is gegenereerd voor uw zoekservice. Geef in de aanvraagbody de naam van de blobcontainer en de verbindingsreeks op.

### <a name="sample-request"></a>Voorbeeldaanvraag
```http
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Verzend de aanvraag. Het online hulpprogramma zou een 201-statuscode moeten retourneren om de succesvolle uitvoering te bevestigen. 

Aangezien dit uw eerste aanvraag is, controleert u Azure Portal om te bevestigen dat de gegevensbron in Azure Search is gemaakt. Controleer op de pagina zoek service dashboard of de lijst met gegevens bronnen een nieuw item is. U moet mogelijk een paar minuten wachten tot de portalpagina is vernieuwd. 

  ![Tegel Gegevensbronnen in de portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Tegel Gegevensbronnen in de portal")

Als u een 403- of 404-fout krijgt, controleert u de constructie van de aanvraag: `api-version=2019-05-06` moet in het eindpunt staan, `api-key` in de header na `Content-Type`, en de waarde ervan moet geldig voor een zoekservice zijn. U kunt de header hergebruiken voor de overige stappen in deze zelfstudie.

## <a name="create-a-skillset"></a>Een set vaardigheden maken

In deze stap definieert u een reeks verrijkingsstappen die u op uw gegevens wilt toepassen. We noemen elke verrijkingsstap een *vaardigheid* en de reeks verrijkingsstappen een *set vaardigheden*. In deze zelf studie worden [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) voor de vaardig heden gebruikt:

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Entiteits herkenning](cognitive-search-skill-entity-recognition.md) voor het extra heren van de namen van organisaties uit inhoud in de BLOB-container.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen. 

### <a name="sample-request"></a>Voorbeeldaanvraag
Voordat u de REST-aanroep uitvoert, moet u de servicenaam en de beheersleutel in de aanvraag hieronder vervangen als het hulpprogramma de aanvraagheader tussen aanroepen niet behoudt. 

Deze aanvraag maakt een set vaardigheden. Gebruik de naam van de set vaardigheden ```demoskillset``` voor de rest van deze zelfstudie.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
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
      "textSplitMode" : "pages",
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
          "name":"languageCode", "source": "/document/languageCode"
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
```

Verzend de aanvraag. Het online hulpprogramma zou een 201-statuscode moeten retourneren om de succesvolle uitvoering te bevestigen. 

#### <a name="explore-the-request-body"></a>De aanvraagbody verkennen

U ziet hoe de vaardigheid voor sleuteltermextractie op elke pagina wordt toegepast. Door de context op ```"document/pages/*"``` in te stellen, voert u deze verrijker uit voor elk onderdeel van de matrix document/pagina's (voor elke pagina in het document).

Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens het verwerken opent Azure Search elk document om inhoud van verschillende bestandsindelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Stel de invoer daarom in als ```"/document/content"```.

Een grafische weergave van de set vaardigheden wordt hieronder weergegeven. 

![Een set vaardigheden begrijpen](media/cognitive-search-tutorial-blob/skillset.png "Een set vaardigheden begrijpen")

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

## <a name="create-an-index"></a>Een index maken

In dit gedeelte kunt u het indexschema definiëren door op te geven welke velden in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld op te geven. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Voorbeeldaanvraag
Voordat u de REST-aanroep uitvoert, moet u de servicenaam en de beheersleutel in de aanvraag hieronder vervangen als het hulpprogramma de aanvraagheader tussen aanroepen niet behoudt. 

Deze aanvraag maakt een index. Gebruik de naam van de index ```demoindex``` voor de rest van deze zelfstudie.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Verzend de aanvraag. Het online hulpprogramma zou een 201-statuscode moeten retourneren om de succesvolle uitvoering te bevestigen. 

Zie [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Index maken (Azure Search REST API)) voor meer informatie over het definiëren van een index.


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Een indexeerfunctie maken, velden toewijzen en transformaties uitvoeren

Tot nu toe hebt u een gegevensbron, een set vaardigheden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. Als u deze in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren. 

+ De fieldMappings worden vóór de vaardig heden verwerkt, waarbij bron velden van de gegevens bron worden toegewezen aan de doel velden in een index. Als veld namen en-typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

+ De outputFieldMappings worden verwerkt na de vaardig heden en verwijzen naar sourceFieldNames die niet bestaan totdat het document wordt gekraakt of verrijkt. De targetFieldName is een veld in een index.

Naast het koppelen van invoer naar uitvoer, kunt u ook veld toewijzingen gebruiken om gegevens structuren samen te voegen. Zie [hoe u verrijkte velden aan een Doorzoek bare index kunt toewijzen](cognitive-search-output-field-mapping.md)voor meer informatie.

### <a name="sample-request"></a>Voorbeeldaanvraag

Voordat u de REST-aanroep uitvoert, moet u de servicenaam en de beheersleutel in de aanvraag hieronder vervangen als het hulpprogramma de aanvraagheader tussen aanroepen niet behoudt. 

Geef ook de naam van de indexeerfunctie op. U kunt er voor de rest van deze zelfstudie naar verwijzen als ```demoindexer```.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Syntaxis aanvraagbody

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
  "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}
```

Verzend de aanvraag. Het online hulpprogramma zou een 201-statuscode moeten retourneren om de succesvolle verwerking te bevestigen. 

Deze stap kan enkele minuten in beslag nemen. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. Sommige vaardigheden, zoals afbeeldingsanalyse, zijn langlopend.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven. Mogelijk moet u eerst objecten verwijderen om de pijplijn opnieuw uit te voeren met code- of scriptwijzigingen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="explore-the-request-body"></a>De aanvraagbody verkennen

Het script stelt ```"maxFailedItems"``` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

Let ook op de instructie ```"dataToExtract":"contentAndMetadata"``` in de configuratieparameters. Deze instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand. 

Wanneer inhoud wordt uitgepakt, kunt u instellen dat ```imageAction``` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van ```"imageAction":"generateNormalizedImages"```, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

## <a name="check-indexer-status"></a>De status van de indexeerfunctie controleren

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de indexeerfunctie nog wordt uitgevoerd, verzendt u de volgende aanvraag om de status van de indexeerfunctie te controleren.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Het antwoord geeft aan of de indexeerfunctie wordt uitgevoerd. Gebruik nadat het indexeren is voltooid een andere HTTP GET voor het STATUS-eindpunt (zoals hierboven) om rapporten te bekijken van eventuele fouten en waarschuwingen die tijdens de verrijking zijn opgetreden.  

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelfstudie zijn de waarschuwingen onschadelijk (bijvoorbeeld geen tekstinvoeren van de JPEG-bestanden). U kunt het statusantwoord bekijken voor uitgebreide informatie over waarschuwingen die tijdens het indexeren zijn verzonden.
 
## <a name="query-your-index"></a>Een query uitvoeren in uw index

Voer nadat het indexeren is voltooid query's uit waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Standaard retourneert Azure Search de 50 hoogste resultaten. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [How to page results in Azure Search](search-pagination-page-layout.md) (Resultaten genereren in Azure Search) voor instructies.

Voer als verificatiestap query's uit op de index voor alle velden.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

De uitvoer is het schema van de index met de naam, het type en de kenmerken van elk veld.

Verzend een tweede query voor `"*"` om alle inhoud te retourneren van één veld, zoals `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Herhaal dit voor aanvullende velden: inhoud, language code, zinsdelen en organisaties in deze oefening. U kunt meerdere velden retourneren via `$select` met behulp van een door komma's gescheiden lijst.

U kunt GET of POST gebruiken, afhankelijk van de complexiteit en lengte van de queryreeks. Zie [Query using the REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Query's uitvoeren met de REST API) voor meer informatie.



<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van pijplijnontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

Uw documenten opnieuw indexeren met de nieuwe definities:

1. Verwijder de index om persistente gegevens te verwijderen. Verwijder de indexeerfunctie om deze in de service opnieuw te maken.
2. Wijzig de definitie van een set vaardigheden en index.
3. Maak de index en indexeerfunctie opnieuw in de service om de pijplijn uit te voeren. 

U kunt de portal gebruiken om indexen, indexeerfuncties en vaardighedensets te verwijderen.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

De statuscode 204 wordt na verwijdering geretourneerd.

Naarmate uw code meer vormt krijgt, is het raadzaam om uw herbouwstrategie te verfijnen. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Vooraf gedefinieerde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definitie van de set vaardigheden en het mechanisme voor het koppelen van vaardigheden via in- en uitvoeren. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. Deze release bevat een mechanisme voor het weergeven van interne constructies (verrijkte documenten die zijn gemaakt door het systeem). U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten u moet verwijderen voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om op te schonen na een zelfstudie is om de resourcegroep met de Azure Search-service en Azure Blob service te verwijderen. Ervan uitgaande dat u beide services in dezelfde groep hebt geplaatst, verwijdert u de resourcegroep om alle inhoud ervan permanent te verwijderen, waaronder de services en alle opgeslagen inhoud die u voor deze zelfstudie hebt gemaakt. De naam van de resourcegroep staat in de portal op de pagina Overzicht van elke service.

## <a name="next-steps"></a>Volgende stappen

De pijplijn uitbreiden of aanpassen met aangepaste vaardigheden. Door een aangepaste vaardigheid te maken en deze toe te voegen aan een set vaardigheden, kunt u zelfgeschreven tekst- of afbeeldingsanalyse integreren. 

> [!div class="nextstepaction"]
> [Voorbeeld: Een aangepaste vaardigheid maken voor cognitieve zoek acties](cognitive-search-create-custom-skill-example.md)
