---
title: 'Zelfstudie: REST en AI over Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Stap door een voorbeeld van tekstextractie en verwerking van natuurlijke taal over inhoud in Blob-opslag met behulp van Postman en de AZURE Cognitive Search REST API's.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 8acafa14afab507b704806056efac0f877a47684
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190719"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelfstudie: REST en AI gebruiken om doorzoekbare inhoud te genereren vanuit Azure-blobs

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kan een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken die handig is voor scenario's voor zoeken in volledige tekst of kennismijnbouw. Hoewel een pijplijn afbeeldingen kan verwerken, richt deze REST-zelfstudie zich op tekst, waarbij taaldetectie en verwerking van natuurlijke talen worden toegepast om nieuwe velden te maken die u gebruiken in query's, facetten en filters.

In deze zelfstudie worden postbode- en [zoek-API's](https://docs.microsoft.com/rest/api/searchservice/) gebruikt om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met hele documenten (ongestructureerde tekst) zoals PDF, HTML, DOCX en PPTX in Azure Blob-opslag.
> * Definieer een pijplijn die tekst extraheert, taal detecteert, entiteiten herkent en sleutelzinnen detecteert.
> * Definieer een index om de uitvoer op te slaan (ruwe inhoud, plus door pijplijn gegenereerde naamwaardeparen).
> * Voer de pijplijn uit om transformaties en analyses te starten en de index te maken en te laden.
> * Bekijk de resultaten met behulp van zoeken met volledige tekst en een syntaxis van uitgebreide query's.

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Postman bureaublad-app](https://www.getpostman.com/)
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

## <a name="2---set-up-postman"></a>2 - Postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met deze tool, raadpleegt u [API's verkennen](search-get-started-postman.md)voor Azure Cognitive Search REST met behulp van Postbode .

De aanvraagmethoden die in deze zelfstudie worden gebruikt, zijn **POST,** **PUT**en **GET**. U gebruikt de methoden om vier API-aanroepen naar uw zoekservice te maken: maak een gegevensbron, een skillset, een index en een indexeerder.

Stel in Kopteksten 'Inhoudstype' in `application/json` op en stel u in op `api-key` de api-toets van de beheer-api van uw Azure Cognitive Search-service. Zodra u de headers hebt ingesteld, u ze gebruiken voor elk verzoek in deze oefening.

  ![URL en koptekst voor postbodeaanvragen](media/search-get-started-postman/postman-url.png "URL en koptekst voor postbodeaanvragen")

## <a name="3---create-the-pipeline"></a>3 - De pijplijn maken

In Azure Cognitive Search vindt AI-verwerking plaats tijdens het indexeren (of inname van gegevens). Dit deel van de walkthrough maakt vier objecten: gegevensbron, indexdefinitie, skillset, indexer. 

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

Een [gegevensbronobject](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biedt de verbindingstekenreeks met de Blob-container die de bestanden bevat.

1. Gebruik **POST** en de volgende URL, waarbij uw servicenaam wordt vervangen door de werkelijke naam van uw service.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. Kopieer in **aanvraaginstantie**de volgende JSON-definitie en vervang de `connectionString` verbinding met de werkelijke verbinding van uw opslagaccount. 

   Vergeet niet om de containernaam ook te bewerken. We stelden "cog-search-demo" voor de container naam in een eerdere stap.

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
1. Verzend de aanvraag. U ziet een statuscode van 201 die het succes bevestigt. 

Als u een 403- of 404-fout krijgt, controleert u de constructie van de aanvraag: `api-version=2019-05-06` moet in het eindpunt staan, `api-key` in de header na `Content-Type`, en de waarde ervan moet geldig voor een zoekservice zijn. U het JSON-document uitvoeren via een online JSON-validator om te controleren of de syntaxis correct is. 

### <a name="step-2-create-a-skillset"></a>Stap 2: Een skillset maken

Een [skillset-object](https://docs.microsoft.com/rest/api/searchservice/create-skillset) is een reeks verrijkingsstappen die op uw inhoud worden toegepast. 

1. Gebruik **PUT** en de volgende URL, waarbij uw servicenaam wordt vervangen door de werkelijke naam van uw service.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. Kopieer in **aanvraagbody**de onderstaande JSON-definitie. Deze skillset bestaat uit de volgende ingebouwde vaardigheden.

   | Behendigheid                 | Beschrijving    |
   |-----------------------|----------------|
   | [Herkenning van entiteiten](cognitive-search-skill-entity-recognition.md) | Haalt de namen van personen, organisaties en locaties uit inhoud in de blobcontainer. |
   | [Taaldetectie](cognitive-search-skill-language-detection.md) | Detecteert de taal van de inhoud. |
   | [Tekst splitsen](cognitive-search-skill-textsplit.md)  | Hiermee breekt u grote inhoud in kleinere stukken voordat u de sleutelzinextractievaardigheid aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen. |
   | [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) | Haalt de bovenste sleutelzinnen eruit. |

   Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens de verwerking kraakt Azure Cognitive Search elk document om inhoud uit verschillende bestandsindelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Als zodanig wordt ```"/document/content"```de input .

   Voor de extractie van de sleutelzin, omdat we de vaardigheid voor tekstsplitter gebruiken ```"document/pages/*"``` om grotere bestanden in pagina's op te splitsen, is de context voor de sleutelzinextractievaardigheid (voor elke pagina in het document) in plaats van ```"/document/content"```.

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

    ![Een skillset begrijpen](media/cognitive-search-tutorial-blob/skillset.png "Een skillset begrijpen")

1. Verzend de aanvraag. Postbode moet een statuscode van 201 terugsturen die succes bevestigt. 

> [!NOTE]
> Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen. Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

Een [index](https://docs.microsoft.com/rest/api/searchservice/create-index) bevat het schema dat wordt gebruikt om de fysieke expressie van uw inhoud te maken in omgekeerde indexen en andere constructies in Azure Cognitive Search. De grootste component van een index is de veldenverzameling, waarbij gegevenstype en kenmerken de inhoud en het gedrag in Azure Cognitive Search bepalen.

1. Gebruik **PUT** en de volgende URL, waarbij uw servicenaam wordt vervangen door de werkelijke naam van uw service, om uw index een naam te geven.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. Kopieer in **aanvraaginstantie**de volgende JSON-definitie. Het `content` veld slaat het document zelf op. Extra velden `languageCode` `keyPhrases`voor `organizations` , en vertegenwoordigen nieuwe informatie (velden en waarden) die zijn gemaakt door de skillset.

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

1. Verzend de aanvraag. Postbode moet een statuscode van 201 terugsturen die succes bevestigt. 

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Een indexer maken en uitvoeren

Een [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) stuurt de pijplijn aan. De drie componenten die u tot nu toe hebt gemaakt (gegevensbron, skillset, index) zijn ingangen naar een indexeerder. Het maken van de indexer op Azure Cognitive Search is de gebeurtenis die de hele pijplijn in beweging zet. 

1. Gebruik **PUT** en de volgende URL, waarbij uw servicenaam wordt vervangen door de werkelijke naam van uw service, om uw indexer een naam te geven.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. Kopieer in **aanvraagbody**de onderstaande JSON-definitie. Let op de veldtoewijzingselementen; deze toewijzingen zijn belangrijk omdat ze de gegevensstroom definiëren. 

   Deze `fieldMappings` worden vóór de skillset verwerkt en worden inhoud van de gegevensbron naar doelvelden in een index verzonden. U gebruikt veldtoewijzingen om bestaande, ongewijzigde inhoud naar de index te verzenden. Als veldnamen en -typen aan beide uiteinden hetzelfde zijn, is er geen toewijzing vereist.

   De `outputFieldMappings` zijn voor velden gemaakt door vaardigheden, en dus verwerkt nadat de skillset is uitgevoerd. De verwijzingen `sourceFieldNames` naar `outputFieldMappings` in bestaan niet totdat document kraken of verrijking maakt ze. Het `targetFieldName` is een veld in een index, gedefinieerd in het indexschema.

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

1. Verzend de aanvraag. Postbode moet een statuscode van 201 retourneren die een succesvolle verwerking bevestigt. 

   Deze stap kan enkele minuten in beslag nemen. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. 

> [!NOTE]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven. Mogelijk moet u eerst objecten verwijderen om de pijplijn opnieuw uit te voeren met code- of scriptwijzigingen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="about-indexer-parameters"></a>Informatie over indexerparameters

Het script stelt ```"maxFailedItems"``` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is acceptabel omdat er zo weinig documenten in de demo-gegevensbron staan. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

De ```"dataToExtract":"contentAndMetadata"``` instructie vertelt de indexer om de inhoud automatisch uit verschillende bestandsindelingen te halen, evenals metagegevens met betrekking tot elk bestand. 

Wanneer inhoud wordt uitgepakt, kunt u instellen dat ```imageAction``` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van ```"imageAction":"generateNormalizedImages"```, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

## <a name="4---monitor-indexing"></a>4 - Monitor indexering

Indexering en verrijking beginnen zodra u de aanvraag Indexer maken indient. Afhankelijk van welke cognitieve vaardigheden u gedefinieerd, indexering kan een tijdje duren. Als u wilt weten of de indexeerfunctie nog wordt uitgevoerd, verzendt u de volgende aanvraag om de status van de indexeerfunctie te controleren.

1. Gebruik **GET** en de volgende URL, waarbij uw servicenaam wordt vervangen door de werkelijke naam van uw service, om uw indexer een naam te geven.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Bekijk het antwoord om te zien of de indexer wordt uitgevoerd of om fout- en waarschuwingsinformatie weer te geven.  

Als u de laag Gratis gebruikt, wordt het volgende bericht verwacht: '"Kan geen inhoud of metagegevens uit uw document extraheren. Afgekapte geëxtraheerde tekst naar '32768' tekens". Dit bericht wordt weergegeven omdat blobindexering op de laag Gratis een[32K-limiet heeft voor tekenextractie.](search-limits-quotas-capacity.md#indexer-limits) U ziet dit bericht niet voor deze gegevensset op hogere niveaus. 

> [!NOTE]
> Waarschuwingen komen vaak voor in sommige scenario's en wijzen niet altijd op een probleem. Als een blobcontainer bijvoorbeeld afbeeldingsbestanden bevat en de pijplijn geen afbeeldingen verwerkt, krijgt u een waarschuwing waarin staat dat afbeeldingen niet zijn verwerkt.

## <a name="5---search"></a>5 - Zoeken

Nu u nieuwe velden en informatie hebt gemaakt, laten we enkele query's uitvoeren om de waarde van cognitief zoeken te begrijpen als het gaat om een typisch zoekscenario.

Bedenk dat we zijn begonnen met blob-inhoud, `content` waarbij het hele document is verpakt in één veld. U in dit veld zoeken en overeenkomsten zoeken met uw zoekopdrachten.

1. Gebruik **GET** en de volgende URL, waarbij uw servicenaam wordt vervangen door de werkelijke naam van `content` uw service, om te zoeken naar exemplaren van een term of woordgroep, het veld terug te sturen en een telling van de overeenkomende documenten.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   De resultaten van deze query retourdocument inhoud, dat is hetzelfde resultaat dat je zou krijgen als gebruikt de blob indexer zonder de cognitieve zoekpijplijn. Dit veld is doorzoekbaar, maar onwerkbaar als u facetten, filters of automatisch aanvullen wilt gebruiken.

   ![Inhoudsvelduitvoer](media/cognitive-search-tutorial-blob/content-output.png "Inhoudsvelduitvoer")
   
1. Retourneer voor de tweede query enkele van de nieuwe velden die door de pijplijn zijn gemaakt (personen, organisaties, locaties, languageCode). We laten sleutelzinnen weg voor beknoptheid, maar je moet het opnemen als je die waarden wilt zien.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   De velden in de $select verklaring bevatten nieuwe informatie die is gemaakt op de mogelijkheden voor het verwerken van natuurlijke talen van Cognitive Services. Zoals u zou verwachten, is er enige ruis in de resultaten en variatie tussen documenten, maar in veel gevallen leveren de analytische modellen nauwkeurige resultaten op.

   De volgende afbeelding toont resultaten voor de open brief van Satya Nadella bij het aannemen van de CEO-rol bij Microsoft.

   ![Pijplijnuitvoer](media/cognitive-search-tutorial-blob/pipeline-output.png "Pijplijnuitvoer")

1. Als u wilt zien hoe u van deze velden profiteren, voegt u een facetparameter toe om een aggregatie van overeenkomende documenten per locatie terug te sturen.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   In dit voorbeeld zijn er voor elke locatie 2 of 3 overeenkomsten.

   ![Facet-uitvoer](media/cognitive-search-tutorial-blob/facet-output.png "Facet-uitvoer")
   

1. In dit laatste voorbeeld u een filter toepassen op de verzameling van organisaties, waarbij twee overeenkomsten worden retournerd voor filtercriteria op basis van NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Deze query's illustreren een aantal manieren waarop u werken met querysyntaxis en filters op nieuwe velden die zijn gemaakt door cognitief zoeken. Zie [Voorbeelden in de API voor zoekdocumenten REST](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), Eenvoudige [syntaxisqueryvoorbeelden](search-query-simple-examples.md)en [Voorbeelden van volledige Lucene-query's](search-query-lucene-examples.md)voor meer queryvoorbeelden .

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele ontwikkelingsfase is de meest praktische benadering voor ontwerpiteratie het verwijderen van de objecten uit Azure Cognitive Search en uw code in staat te stellen ze opnieuw op te bouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U de portal gebruiken om indexen, indexeerders, gegevensbronnen en skillsets te verwijderen. Wanneer u de indexer verwijdert, u de index, skillset en gegevensbron tegelijkertijd selectief verwijderen.

![Zoekobjecten verwijderen](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Zoekobjecten in de portal verwijderen")

Of gebruik **DELETE** en geef URL's op aan elk object. Met de volgende opdracht wordt een indexeerder verwijderd.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) werden geïntroduceerd, samen met skillset definitie en de mechanica van chaining vaardigheden samen door middel van ingangen en uitgangen. U hebt `outputFieldMappings` ook geleerd dat in de indexerdefinitie vereist is voor het routeren van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling Alle resources of Resourcegroepen in het linkernavigatiedeelvenster.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, bekijken we vaardigheden en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Hoe maak je een skillset](cognitive-search-defining-skillset.md)