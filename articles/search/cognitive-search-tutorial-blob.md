---
title: 'REST zelf studie: Een AI-verrijkings pijplijn bouwen met cognitieve zoek functie-Azure Search'
description: Neem een voor beeld van tekst extractie en natuurlijke taal verwerking over inhoud in JSON-blobs met behulp van Postman en de Azure Search REST-Api's.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 726fdd6aeebac970142fa9225381af77114bfe42
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274117"
---
# <a name="tutorial-add-structure-to-unstructured-content-with-cognitive-search"></a>Zelfstudie: Structuur toevoegen aan ' ongestructureerde inhoud ' met cognitieve zoek functie

Als u ongestructureerde tekst-of afbeeldings inhoud hebt, kunt u met de [cognitieve Zoek](cognitive-search-concept-intro.md) functie van Azure Search informatie ophalen en nieuwe inhoud maken die nuttig is voor Zoek opdrachten in volledige tekst of kennis analyse. Hoewel het mogelijk is om afbeeldings bestanden (JPG, PNG, TIFF) te verwerken, is deze zelf studie gericht op inhoud op basis van woorden, het Toep assen van taal detectie en tekst analyse voor het maken van nieuwe velden en informatie die u kunt gebruiken in query's, facetten en filters.

> [!div class="checklist"]
> * Begin met hele documenten (ongestructureerde tekst) zoals PDF, MD, DOCX en PPTX in Azure Blob-opslag.
> * Definieer een pijp lijn die tekst extraheert, taal detecteert, entiteiten herkent en sleutel zinnen detecteert.
> * Definieer een index voor het opslaan van de uitvoer (onbewerkte inhoud, plus pijp lijn gegenereerde naam/waarde-paren).
> * Voer de pijp lijn uit om trans formaties en analyses te starten en om de index te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query syntaxis.

U hebt verschillende services nodig om deze procedure te volt ooien, plus de [postman desktop-app](https://www.getpostman.com/) of een ander hulp programma voor het testen van webtoepassingen om rest API-aanroepen te maken. 

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechter muisknop op het zip-bestand en selecteer **Alles uitpakken**. Er zijn 14 bestanden van verschillende typen. U gebruikt 7 voor deze oefening.

## <a name="1---create-services"></a>1-services maken

In deze walkthrough wordt Azure Search gebruikt voor het indexeren en query's, Cognitive Services voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Maak indien mogelijk alle drie de services in dezelfde regio en resource groep voor nabijheid en beheer baarheid. In de praktijk kan uw Azure Storage-account zich in een wille keurige regio bevinden.

### <a name="start-with-azure-storage"></a>Beginnen met Azure Storage

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/) en klik op **+ resource maken**.

1. Zoek naar het *opslag account* en selecteer het opslag account van micro soft.

   ![Opslag account maken](media/cognitive-search-tutorial-blob/storage-account.png "Opslag account maken")

1. Op het tabblad basis beginselen zijn de volgende items vereist. Accepteer de standaard waarden voor alle andere.

   + **Resourcegroep**. Selecteer een bestaande naam of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze gezamenlijk kunt beheren.

   + **Naam van opslag account**. Als u denkt dat u mogelijk meerdere resources van hetzelfde type hebt, gebruikt u de naam van dubbel zinnigheid per type en regio, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Search en Cognitive Services. Een enkele locatie vernietigt bandbreedte kosten.

   + **Soort account**. Kies de standaard *StorageV2 (algemeen gebruik v2)* .

1. Klik op **beoordeling + maken** om de service te maken.

1. Zodra de app is gemaakt, klikt u op **Ga naar de resource** om de pagina overzicht te openen.

1. Klik op **blobs** -service.

1. Klik op **+ container** om een container te maken en geef deze de naam *tandwiel-Search-demo*.

1. Selecteer *tandwiel-Search-demo* en klik vervolgens op **uploaden** om de map te openen waarin u de bestanden hebt opgeslagen. Selecteer alle niet-afbeeldings bestanden. U moet 7 bestanden hebben. Klik op **OK** om te uploaden.

   ![Voorbeeld bestanden uploaden](media/cognitive-search-tutorial-blob/sample-files.png "Voorbeeld bestanden uploaden")

1. Voordat u Azure Storage verlaat, moet u een connection string ophalen zodat u een verbinding in Azure Search kunt formuleren. 

   1. Ga terug naar de pagina overzicht van uw opslag account (we hebben *blobstragewestus* als voor beeld gebruikt). 
   
   1. Selecteer in het navigatie deel venster links de optie **toegangs sleutels** en kopieer een van de verbindings reeksen. 

   De connection string is een URL die vergelijkbaar is met het volgende voor beeld:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de connection string op in Klad blok. U hebt deze later nodig bij het instellen van de gegevens bron verbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking in cognitieve zoek opdracht wordt ondersteund door Cognitive Services, met inbegrip van Text Analytics en Computer Vision voor natuurlijke taal en afbeeldings verwerking. Als u een echt prototype of project wilt volt ooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Search), zodat u het kunt koppelen aan index bewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overs Laan omdat Azure Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis trans acties per Indexeer functie uitvoert. Omdat in deze zelf studie 7 trans acties worden gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md)voor meer informatie.

### <a name="azure-search"></a>Azure Search

Het derde onderdeel is Azure Search, dat u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om deze procedure te volt ooien. 

Net als bij Azure Blob-opslag neemt het even de tijd om de toegangs sleutel te verzamelen. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eind punt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-an-admin-api-key-and-url-for-azure-search"></a>Een beheer-API-sleutel en-URL voor Azure Search ophalen

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en haal de naam van uw zoek service op in de pagina **overzicht** van de zoek service. U kunt uw service naam bevestigen door de URL van het eind punt te controleren. Als uw eind punt- `https://mydemo.search.windows.net`URL zou zijn, zou uw `mydemo`service naam zouden zijn.

2. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

    Haal ook de query sleutel op. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

![De service naam en de beheer-en query sleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Alle aanvragen vereisen een API-sleutel in de header van elke aanvraag die naar uw service wordt verzonden. Een geldige sleutel brengt een vertrouwens relatie tot stand, op basis van aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="2---set-up-postman"></a>2-postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met dit hulp programma, raadpleegt u [Azure Search rest-Api's verkennen met behulp van Postman](search-get-started-postman.md).

De aanvraag methoden die in deze zelf studie worden gebruikt, zijn **post**, **put**en **Get**. U gebruikt de methoden voor het maken van vier API-aanroepen naar uw zoek service: Maak een gegevens bron, een vaardig heden, een index en een Indexeer functie.

Stel in headers ' content-type ' `application/json` in en stel `api-key` in op de beheer-API-sleutel van uw Azure Search service. Wanneer u de koppen hebt ingesteld, kunt u deze gebruiken voor elke aanvraag in deze oefening.

  ![URL en header van Postman-aanvraag](media/search-get-started-postman/postman-url.png "URL en header van Postman-aanvraag")

## <a name="3---create-the-pipeline"></a>3: de pijp lijn maken

In Azure Search wordt AI-verwerking uitgevoerd tijdens het indexeren (of gegevens opname). In dit deel van het scenario worden vier objecten gemaakt: gegevens bron, index definitie, vaardig heden, Indexeer functie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

Een [gegevens bron object](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biedt de Connection String aan de BLOB-container met de bestanden.

1. Gebruik **post** en de volgende URL, waarbij u de naam van uw service vervangt door de werkelijke naam van uw service.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. Kopieer de volgende JSON-definitie in de **hoofd tekst**van de `connectionString` aanvraag en vervang deze door de huidige verbinding van uw opslag account. 

   Vergeet niet om ook de container naam te bewerken. We hebben ' tandwiel-Search-demo ' voor de container naam in een eerdere stap voorgesteld.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Verzend de aanvraag. U ziet de status code 201 bevestigend geslaagd. 

Als u een 403- of 404-fout krijgt, controleert u de constructie van de aanvraag: `api-version=2019-05-06` moet in het eindpunt staan, `api-key` in de header na `Content-Type`, en de waarde ervan moet geldig voor een zoekservice zijn. Mogelijk wilt u het JSON-document uitvoeren via een online JSON-validatie programma om ervoor te zorgen dat de syntaxis juist is. 

### <a name="step-2-create-a-skillset"></a>Stap 2: Een set vaardigheden maken

Een [object voor vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) is een set verrijkings stappen die op uw inhoud wordt toegepast. 

1. Gebruik **put** en de volgende URL, waarbij u de naam van uw service vervangt door de werkelijke naam van uw service.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. Kopieer de onderstaande JSON-definitie in de **hoofd tekst**van de aanvraag. Deze vaardig heden bestaat uit de volgende ingebouwde vaardig heden.

   | Eigen                 | Description    |
   |-----------------------|----------------|
   | [Entiteit herkenning](cognitive-search-skill-entity-recognition.md) | Extraheert de namen van personen, organisaties en locaties uit inhoud in de BLOB-container. |
   | [Taaldetectie](cognitive-search-skill-language-detection.md) | Detecteert de taal van de inhoud. |
   | [Tekst splitsen](cognitive-search-skill-textsplit.md)  | Hiermee verbreekt u grote inhoud in kleinere segmenten voordat u de kwalificatie voor de extractie van sleutel woorden aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen. |
   | [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) | Hiermee worden de belangrijkste hoofd zinnen opgehaald. |

   Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens het verwerken opent Azure Search elk document om inhoud van verschillende bestandsindelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Als zodanig wordt ```"/document/content"```de invoer.

   Voor het uitpakken van sleutel zinnen is ```"document/pages/*"``` het gebruik van de tekst splitter-vaardigheid om grotere bestanden op pagina's te versleutelen, de context voor de kwalificatie voor de extractie van belang rijke frasen (voor elke pagina in het document) in plaats van. ```"/document/content"```

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Een grafische weergave van de set vaardigheden wordt hieronder weergegeven. 

    ![Een set vaardigheden begrijpen](media/cognitive-search-tutorial-blob/skillset.png "Een set vaardigheden begrijpen")

1. Verzend de aanvraag. Postman moet de status code 201 bevestigen van geslaagde pogingen retour neren. 

> [!NOTE]
> Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen. Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

Een [index](https://docs.microsoft.com/rest/api/searchservice/create-index) biedt het schema dat wordt gebruikt voor het maken van de fysieke expressie van uw inhoud in omgekeerde indexen en andere constructs in azure Search. Het grootste onderdeel van een index is de verzameling velden, waarbij het gegevens type en de kenmerken inhoud en gedrag in Azure Search bepalen.

1. Gebruik **put** en de volgende URL om uw-service naam te vervangen door de werkelijke naam van uw service, om uw index een naam te benoemen.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. In de **hoofd tekst**van de aanvraag kopieert u de volgende JSON-definitie. In `content` het veld wordt het document zelf opgeslagen. Aanvullende velden voor `languageCode`, `keyPhrases`en `organizations` vertegenwoordigen nieuwe informatie (velden en waarden) die zijn gemaakt door de vaardig heden.

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
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
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
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Verzend de aanvraag. Postman moet de status code 201 bevestigen van geslaagde pogingen retour neren. 

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Een Indexeer functie maken en uitvoeren

Een [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) verstuurt de pijp lijn. De drie onderdelen die u tot nu toe hebt gemaakt (gegevens bron, vaardig heden, index) zijn invoer naar een Indexeer functie. Het maken van de Indexeer functie op Azure Search is de gebeurtenis die de volledige pijp lijn in beweging zet. 

1. Gebruik **put** en de volgende URL en vervang uw service naam door de werkelijke naam van uw service, zodat u de Indexeer functie een naam kunt.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. Kopieer de onderstaande JSON-definitie in de **hoofd tekst**van de aanvraag. Let op de elementen van de veld toewijzing; deze toewijzingen zijn belang rijk omdat ze de gegevens stroom definiëren. 

   De `fieldMappings` worden verwerkt vóór de vaardig heden en verzenden inhoud van de gegevens bron naar doel velden in een index. U gebruikt veld toewijzingen voor het verzenden van bestaande, niet-gewijzigde inhoud naar de index. Als veld namen en-typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

   Het `outputFieldMappings` zijn voor velden die zijn gemaakt door vaardig heden en die dus worden verwerkt nadat de vaardig heden zijn uitgevoerd. De verwijzingen naar `sourceFieldNames` in `outputFieldMappings` zijn pas aanwezig als het kraken of verrijken van documenten is gemaakt. Het `targetFieldName` is een veld in een index dat in het index schema is gedefinieerd.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
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
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
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
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Verzend de aanvraag. Postman moet het retour neren van de status code 201 bevestigend geslaagde verwerking. 

   Deze stap kan enkele minuten in beslag nemen. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. 

> [!NOTE]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven. Mogelijk moet u eerst objecten verwijderen om de pijplijn opnieuw uit te voeren met code- of scriptwijzigingen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="about-indexer-parameters"></a>Over para meters voor indexeren

Het script stelt ```"maxFailedItems"``` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is acceptabel omdat er zo weinig documenten in de gegevens bron van de demo zijn. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

De ```"dataToExtract":"contentAndMetadata"``` instructie geeft aan dat de Indexeer functie automatisch de inhoud moet extra heren uit verschillende bestands indelingen en meta gegevens die zijn gerelateerd aan elk bestand. 

Wanneer inhoud wordt uitgepakt, kunt u instellen dat ```imageAction``` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van ```"imageAction":"generateNormalizedImages"```, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

## <a name="4---monitor-indexing"></a>4: indexeren controleren

Het indexeren en verrijken begint zodra u de aanvraag voor het maken van een indexer verzendt. Afhankelijk van de cognitieve vaardig heden die u hebt gedefinieerd, kan het indexeren enige tijd duren. Als u wilt weten of de indexeerfunctie nog wordt uitgevoerd, verzendt u de volgende aanvraag om de status van de indexeerfunctie te controleren.

1. Gebruik **Get** en de volgende URL en vervang uw service naam door de werkelijke naam van uw service om uw Indexeer functie een naam te geven.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Bekijk het antwoord om te zien of de Indexeer functie wordt uitgevoerd of om fout-en waarschuwings gegevens weer te geven.  

Als u de gratis laag gebruikt, wordt het volgende bericht verwacht: ' "kan geen inhoud of meta gegevens uit het document extra heren. Geëxtraheerde tekst afgekapt tot ' 32768 ' tekens '. Dit bericht wordt weer gegeven omdat het indexeren van blobs in de gratis laag een[limiet van 32 KB voor teken extractie](search-limits-quotas-capacity.md#indexer-limits)heeft. Dit bericht wordt niet weer gegeven voor deze gegevensset op hogere lagen. 

> [!NOTE]
> Waarschuwingen zijn gebruikelijk in sommige scenario's en geven niet altijd een probleem aan. Als een BLOB-container bijvoorbeeld afbeeldings bestanden bevat en de pijp lijn geen afbeeldingen afhandelt, wordt er een waarschuwing weer gegeven met de mede deling dat de installatie kopieën niet zijn verwerkt.

## <a name="5---search"></a>5-zoeken

Nu u nieuwe velden en informatie hebt gemaakt, gaan we enkele query's uitvoeren om inzicht te krijgen in de waarde van de cognitieve zoek opdracht omdat deze is gekoppeld aan een typisch Zoek scenario.

Intrekken dat we zijn begonnen met blob-inhoud, waarbij het hele document is verpakt `content` in één veld. U kunt dit veld doorzoeken en zoeken naar overeenkomsten met uw query's.

1. Gebruik **Get** en de volgende URL en vervang uw service naam door de werkelijke naam van uw service om te zoeken naar exemplaren van een term of woord groep, waarbij u het `content` veld en het aantal overeenkomende documenten wilt retour neren.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   De resultaten van deze query retour neren de inhoud van het document. Dit heeft hetzelfde resultaat als het gebruik van de BLOB-Indexeer functie zonder de cognitieve Zoek pijplijn. Dit veld kan doorzoekbaar zijn, maar is niet geschikt als u facetten, filters of automatisch aanvullen wilt gebruiken.

   ![Inhouds veld uitvoer](media/cognitive-search-tutorial-blob/content-output.png "Inhouds veld uitvoer")
   
1. Voor de tweede query retourneert u enkele van de nieuwe velden die door de pijp lijn zijn gemaakt (personen, organisaties, locaties, language code). Er worden geen zinsdelen voor het boogje wegge laten, maar u moet deze ook meenemen als u deze waarden wilt zien.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   De velden in de $select-instructie bevatten nieuwe gegevens die zijn gemaakt op basis van de verwerkings mogelijkheden van de natuurlijke taal van Cognitive Services. Zoals u kunt verwachten, is er een ruis in de resultaten en variaties in documenten, maar in veel gevallen levert de analytische modellen nauw keurige resultaten op.

   In de volgende afbeelding ziet u de resultaten voor de open letter van de Satya NADELLA, waarbij wordt aangenomen dat de CEO Role bij micro soft.

   ![Pijplijn uitvoer](media/cognitive-search-tutorial-blob/pipeline-output.png "Pijplijn uitvoer")

1. Als u wilt zien hoe u kunt profiteren van deze velden, voegt u een facet parameter toe om een aggregatie van overeenkomende documenten per locatie te retour neren.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   In dit voor beeld zijn er twee of drie overeenkomsten voor elke locatie.

   ![Facet uitvoer](media/cognitive-search-tutorial-blob/facet-output.png "Facet uitvoer")
   

1. In dit laatste voor beeld past u een filter toe op de verzameling organisaties, waarbij twee overeenkomsten worden geretourneerd voor filter criteria op basis van NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Deze query's illustreren een aantal manieren waarop u kunt werken met query syntaxis en filters voor nieuwe velden die zijn gemaakt door cognitieve Zoek opdrachten. Voor meer query voorbeelden, Zie [voor beelden in zoeken in documenten rest API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [eenvoudige syntaxis query voorbeelden](search-query-simple-examples.md)en [volledige voor beelden](search-query-lucene-examples.md)van de Lucene-query.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van pijplijnontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

Uw documenten opnieuw indexeren met de nieuwe definities:

1. Verwijder de Indexeer functie, de index en de vaardig heden.
2. Objecten wijzigen.
3. Maak uw service opnieuw om de pijp lijn uit te voeren. 

U kunt de portal gebruiken om indexen, Indexeer functies en vaardig heden te verwijderen, of door gebruik te **verwijderen** en url's aan elk object toe te voegen. Met de volgende opdracht wordt een Indexeer functie verwijderd.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

De statuscode 204 wordt na verwijdering geretourneerd.

Naarmate uw code meer vormt krijgt, is het raadzaam om uw herbouwstrategie te verfijnen. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Vooraf gedefinieerde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definitie van de set vaardigheden en het mechanisme voor het koppelen van vaardigheden via in- en uitvoeren. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. 

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om op te schonen na een zelfstudie is om de resourcegroep met de Azure Search-service en Azure Blob service te verwijderen. Ervan uitgaande dat u beide services in dezelfde groep hebt geplaatst, verwijdert u de resourcegroep om alle inhoud ervan permanent te verwijderen, waaronder de services en alle opgeslagen inhoud die u voor deze zelfstudie hebt gemaakt. De naam van de resourcegroep staat in de portal op de pagina Overzicht van elke service.

## <a name="next-steps"></a>Volgende stappen

De pijplijn uitbreiden of aanpassen met aangepaste vaardigheden. Door een aangepaste vaardigheid te maken en deze toe te voegen aan een set vaardigheden, kunt u zelfgeschreven tekst- of afbeeldingsanalyse integreren. 

> [!div class="nextstepaction"]
> [Voorbeeld: Een aangepaste vaardigheid maken voor cognitieve zoek acties](cognitive-search-create-custom-skill-example.md)