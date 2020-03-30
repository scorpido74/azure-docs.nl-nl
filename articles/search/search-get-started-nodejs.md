---
title: "Snelstart: een zoekindex maken in Node.js met REST API's"
titleSuffix: Azure Cognitive Search
description: In deze Quickstart van Node.js leert u hoe u een index maakt, gegevens laadt en query's uitvoert op Azure Cognitive Search met JavaScript en de REST API's.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: cbef6029b93f134f95ee54aa87ce0dd65bcdf50d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77623998"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Snelstart: een Azure Cognitive Search-index maken in Node.js met REST API's
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Maak een Node.js-toepassing die een Azure Cognitive Search-index maakt, laadt en opvraagt. In dit artikel wordt uitgelegd hoe u de toepassing stap voor stap maken. U ook [de broncode en gegevens downloaden](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) en de toepassing uitvoeren vanaf de opdrachtregel.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

We hebben de volgende software en services gebruikt om deze quickstart te bouwen en te testen:

+ [Node.js](https://nodejs.org)

+ [NPM](https://www.npmjs.com) moet worden geïnstalleerd door Node.js

+ Een voorbeeldindexstructuur en overeenkomende documenten zijn opgenomen in dit artikel of in de [ **quickstart-map** van de repo](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart.

Aanbevolen:

* [Visual Studio-code](https://code.visualstudio.com)

* [Prettier-](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) en [ESLint-extensies](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) voor VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Sleutels en URL's ophalen

Voor oproepen naar de service is een URL-eindpunt en een toegangssleutel vereist voor elk verzoek. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en krijg op de pagina **Overzicht** van uw zoekservice de naam van uw zoekservice. U uw servicenaam bevestigen door de URL van het eindpunt te bekijken. Als uw eindpunt-URL dat wel `https://mydemo.search.windows.net` `mydemo`was, zou uw servicenaam .

2. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

    Haal de querysleutel ook. Het is een aanbevolen manier om queryaanvragen uit te geven met alleen-lezen toegang.

![De servicenaam en -beheerder en querysleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Voor alle aanvragen is een api-toets nodig in de header van elk verzoek dat naar uw service wordt verzonden. Een geldige sleutel stelt vertrouwen, op basis van de aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van een Powershell-console of een andere omgeving waarin u Node.js hebt geïnstalleerd.

1. Maak een ontwikkelingsmap, waardoor `quickstart` het de naam:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Initialiseer een leeg project `npm init`met NPM door het uitvoeren van . Accepteer de standaardwaarden, met uitzondering van de licentie, die u moet instellen op 'MIT'. 

1. Voeg pakketten toe waarvan de code en de hulp bij de ontwikkeling afhankelijk zijn:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Controleer of u de projecten en de afhankelijkheden ervan hebt geconfigureerd door te controleren of het **bestand package.json** er hetzelfde uitziet als het volgende:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```

5. Maak een bestand **azure_search_config.json** om uw zoekservicegegevens vast te houden:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Vervang `[SERVICE_NAME]` de waarde door de naam van uw zoekservice. Vervang `[ADMIN_KEY]` `[QUERY_KEY]` en met de belangrijke waarden die u eerder hebt geregistreerd. 

## <a name="1---create-index"></a>1 - Index maken 

Een bestand **hotels_quickstart_index.json maken.**  In dit bestand wordt gedefinieerd hoe Azure Cognitive Search werkt met de documenten die u in de volgende stap laadt. Elk veld wordt geïdentificeerd `name` door een `type`en heeft een opgegeven . Elk veld heeft ook een reeks indexkenmerken die aangeven of Azure Cognitive Search op het veld kan zoeken, filteren, sorteren en faceten. De meeste velden zijn eenvoudige gegevenstypen, `AddressType` maar sommige zijn complexe typen waarmee u rijke gegevensstructuren in uw index maken.  U meer lezen over [ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en [indexkenmerken](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes). 

Voeg het volgende toe aan **hotels_quickstart_index.json** of [download het bestand](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```
    

Het is een goede gewoonte om de specifieke kenmerken van een bepaald scenario te scheiden van code die breed van toepassing zal zijn. De `AzureSearchClient` klasse die is gedefinieerd in het bestand **AzureSearchClient.js** weet hoe u aanvraag-URL's moet construeren, een aanvraag moet indienen met de API Ophalen en reageert op de statuscode van het antwoord.

Begin te werken aan **AzureSearchClient.js** door het **knooppuntpakket te** importeren en een eenvoudige klasse te maken. Isoleer de verwisselbare delen van de `AzureSearchClient` klasse door de verschillende configuratiewaarden door te geven aan de constructor:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

De eerste verantwoordelijkheid van de klasse is om te weten hoe URL's te construeren om de verschillende aanvragen te verzenden. Bouw deze URL's met instantiemethoden die de configuratiegegevens gebruiken die aan de klassenconstructor worden doorgegeven. De URL die ze construeren is specifiek voor een API-versie en moet `2019-05-06`een argument hebben waarin die versie wordt opgegeven (in deze toepassing). 

De eerste van deze methoden retourneert de URL voor de index zelf. Voeg de volgende methode toe in de klassentekst:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

De volgende `AzureSearchClient` verantwoordelijkheid van het maken van een asynchrone aanvraag met de Fetch API. De asynchrone `request` statische methode heeft een URL, een tekenreeks die de HTTP-methode opgeeft ('GET', 'PUT', 'POST', 'Delete'), de sleutel die in de aanvraag moet worden gebruikt en een optioneel JSON-object. De `headers` variabele `queryKey` brengt de (of de beheersleutel of de alleen-lezen querysleutel) in kaart met de HTTP-aanvraagkop 'api-key'. De aanvraagopties bevatten `method` altijd de `headers`te gebruiken en de . Als `bodyJson` dit `null`niet het is, wordt de hoofdtekst `bodyJson`van de HTTP-aanvraag ingesteld op de tekenreeksweergave van . De `request` methode retourneert de belofte van de Fetch API om de HTTP-aanvraag uit te voeren.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

Voor demodoeleinden, gooi gewoon een uitzondering als de HTTP-aanvraag is geen succes. In een echte toepassing, zou je waarschijnlijk doen wat logging `response` en diagnose van de HTTP-statuscode in de van de zoekservice aanvraag. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Voeg ten slotte de methoden toe om de Azure Cognitive Search-index te detecteren, verwijderen en maken. Deze methoden hebben allemaal dezelfde structuur:

* Ontvang het eindpunt waarop de aanvraag zal worden gedaan.
* Genereer de aanvraag met het juiste eindpunt, HTTP-werkwoord, API-toets en, indien van toepassing, een JSON-body. `indexExistsAsync()`en `deleteIndexAsync()` hebben geen JSON lichaam, maar `createIndexAsync(definition)` wel.
* `await`het antwoord op het verzoek.  
* Reageer op de statuscode van het antwoord.
* Een belofte van een bepaalde juiste `this`waarde retourneren (een Booleaan of de queryresultaten). 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Controleer of uw methoden zich binnen de klasse bevinden en of u de klasse exporteert. De buitenste scope van **AzureSearchClient.js** moet zijn:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Een objectgeoriënteerde klasse was een goede keuze voor de mogelijk herbruikbare **AzureSearchClient.js-module,** maar is niet nodig voor het hoofdprogramma, dat u in een bestand moet plaatsen dat **index.js**wordt genoemd. 

Maak **index.js** en begin met het binnenhalen van:

* Het **nconf-pakket,** dat u flexibiliteit biedt voor het opgeven van de configuratie met JSON, omgevingsvariabelen of opdrachtregelargumenten.
* De gegevens van het **hotels_quickstart_index.json** bestand.
* De module `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Met [ **het nconf-pakket** ](https://github.com/indexzero/nconf) u configuratiegegevens opgeven in verschillende indelingen, zoals omgevingsvariabelen of de opdrachtregel. In dit voorbeeld wordt **nconf** op een eenvoudige manier gebruikt om het bestand **azure_search_config.json** te lezen en de inhoud van dat bestand als woordenboek terug te sturen. Met behulp van `get(key)` de functie van **NCONF**u snel controleren of de configuratie-informatie correct is aangepast. Ten slotte retourneert de functie de configuratie:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

De `sleep` functie `Promise` maakt een die na een bepaalde tijd wordt opgelost. Met deze functie kan de app pauzeren terwijl de asynchrone indexbewerkingen zijn voltooid en beschikbaar zijn. Het toevoegen van een dergelijke vertraging is meestal alleen nodig in demo's, tests en voorbeeldtoepassingen.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Geef ten slotte de hoofdasynchrone `run` functie op en aan. Deze functie roept de andere functies in volgorde, in afwachting van zo nodig op te lossen `Promise`s.

* De configuratie ophalen `getAzureConfiguration()` met de eerder geschreven configuratie
* Een nieuw `AzureSearchClient` exemplaar maken en waarden van uw configuratie doorgeven
* Controleren of de index bestaat en, als dat zo is, verwijderen
* Een index maken `indexDefinition` met de geladen **hotels_quickstart_index.json**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

Vergeet niet dat laatste `run()`telefoontje naar! Het is het ingangspunt van `node index.js` uw programma wanneer u in de volgende stap loopt.

Let `AzureSearchClient.indexExistsAsync()` op `AzureSearchClient.deleteIndexAsync()` dat en neem geen parameters. Deze functies `AzureSearchClient.request()` bellen `bodyJson` zonder argument. Binnen `AzureSearchClient.request()`, `bodyJson === null` `true`sinds `init` is , de structuur is ingesteld op `indexExistsAsync()` alleen de HTTP-werkwoord ("GET" voor en "DELETE" voor `deleteIndexAsync()`) en de headers, die de aanvraagsleutel te specificeren.  

De `AzureSearchClient.createIndexAsync(indexDefinition)` methode neemt daarentegen _wel_ een parameter. De `run` functie `index.js`in , geeft de inhoud van `AzureSearchClient.createIndexAsync(indexDefinition)` het bestand **hotels_quickstart_index.json** aan de methode. De `createIndexAsync()` methode geeft `AzureSearchClient.request()`deze definitie door aan . In `AzureSearchClient.request()`, `bodyJson === null` sinds `false`is `init` nu , de structuur omvat niet alleen de HTTP-werkwoord ("PUT") en de headers, maar stelt de `body` op de index definitie gegevens.

### <a name="prepare-and-run-the-sample"></a>Het voorbeeld voorbereiden en uitvoeren

Gebruik een terminalvenster voor de volgende opdrachten.

1. Navigeer naar de map met het **bestand package.json** en de rest van uw code.
1. Installeer de pakketten voor `npm install`het monster met .  Deze opdracht downloadt de pakketten waarvan de code afhankelijk is.
1. Voer uw `node index.js`programma uit met .

U ziet een reeks berichten waarin de acties worden beschreven die door het programma worden uitgevoerd. Als u meer details van de aanvragen wilt zien, u `AzureSearchClient.request()` dehttps://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) opmerkingen [regels aan het begin van de methode] in **AzureSearchClient.js**ongedaan maken. 

Open het **overzicht** van uw zoekservice in de Azure-portal. Selecteer het tabblad **Indexen.** Je zou zoiets als het volgende moeten zien:

![Schermafbeelding van Azure-portal, overzicht van de zoekservice, tabblad Indexen](media/search-get-started-nodejs/create-index-no-data.png)

In de volgende stap voegt u gegevens toe aan de index. 

## <a name="2---load-documents"></a>2 - Documenten laden 

In Azure Cognitive Search zijn documenten gegevensstructuren die zowel invoer zijn voor indexering als uitvoer van query's. U moet dergelijke gegevens naar de index plaatsen. Hiermee wordt een ander eindpunt gebruikt dan de bewerkingen die in de vorige stap zijn uitgevoerd. Open **AzureSearchClient.js** en voeg `getIndexUrl()`de volgende methode toe na:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Net `AzureSearchClient.createIndexAsync(definition)`als , moet `AzureSearchClient.request()` je een functie die gesprekken en passeert in het hotel gegevens om zijn lichaam te zijn. Voeg in **AzureSearchClient.js** toe na `postDataAsync(hotelsData)` : `createIndexAsync(definition)`

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Documentinvoer kunnen rijen in een database zijn, blobs in Blob-opslag of, zoals in dit voorbeeld, JSON-documenten op schijf. U [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) downloaden of uw eigen **hotels.json-bestand** maken met de volgende inhoud:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Als u deze gegevens in uw programma wilt laden, `hotelData` wijzigt u **index.js** door de regel toe te voegen die naar de bovenkant verwijst:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Wijzig nu `run()` de functie in **index.js**. Het kan een paar seconden duren voordat de index beschikbaar is, `AzureSearchClient.postDataAsync(hotelData)`dus voeg een pauze van 2 seconden toe voordat u aanroept:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Voer het programma `node index.js`opnieuw uit met . U ziet een iets andere set berichten dan berichten die u in stap 1 hebt gezien. Deze keer bestaat de index _wel_ en moet u een bericht zien over het verwijderen ervan voordat de app de nieuwe index maakt en gegevens naar deze index verzendt. 

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Ga terug naar het tabblad **Indexen** in het **overzicht** van uw zoekservice op de Azure-portal. Uw index bevat nu vier documenten en verbruikt een bepaalde hoeveelheid opslagruimte (het kan enkele minuten duren voordat de gebruikersinterface de onderliggende status van de index goed weergeeft). Klik op de indexnaam die naar de **zoekverkenner**moet worden gebracht. Op deze pagina u experimenteren met gegevensquery's. Probeer te zoeken op `*&$count=true` een query string van en je moet al je documenten en het aantal resultaten terug te krijgen. Probeer met de `historic&highlight=Description&$filter=Rating gt 4` query string en je moet terug te krijgen een `<em></em>` enkel document, met het woord "historische" verpakt in tags. Lees meer over [het opstellen van een query in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Reproduceren deze query's in code door **index.js** te openen en deze code bovenaan toe te voegen:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Schrijf in hetzelfde **index.js-bestand** de `doQueriesAsync()` onderstaande functie. Deze functie `AzureSearchClient` neemt een `AzureSearchClient.queryAsync` object en past de `queries` methode toe op elk van de waarden in de array. Het maakt `Promise.all()` gebruik van `Promise` de functie om een enkele die alleen wordt opgelost wanneer alle query's zijn opgelost terug te keren. De aanroep om `JSON.stringify(body, null, 4)` het queryresultaat op te maken om beter leesbaar te zijn.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

Wijzig `run()` de functie om lang genoeg te pauzeren voordat `doQueriesAsync(client)` de indexer kan werken en vervolgens de functie aan te roepen:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

Als `AzureSearchClient.queryAsync(query)`u het bestand **AzureSearchClient.js**wilt implementeren, bewerkt u het bestand . Zoeken vereist een ander eindpunt en de zoektermen worden `getSearchUrl(searchTerm)` URL-argumenten, dus voeg de functie toe naast de `getIndexUrl()` en methoden die `getPostDataUrl()` u al hebt geschreven.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

De `queryAsync(searchTerm)` functie gaat ook in **AzureSearchClient.js** `postDataAsync(data)` en volgt dezelfde structuur als en de andere queryfuncties: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Zoeken wordt gedaan met de "GET" werkwoord en geen lichaam, omdat de zoekterm is onderdeel van de URL. Merk `queryAsync(searchTerm)` op `this.queryKey`dat gebruikt, in tegenstelling tot de andere functies die de admin sleutel gebruikt. Querytoetsen, zoals de naam al aangeeft, kunnen alleen worden gebruikt voor het opvragen van de index en kunnen op geen enkele manier worden gebruikt om de index te wijzigen. Querysleutels zijn daarom veiliger te distribueren naar clienttoepassingen.

Voer het `node index.js`programma uit met . Nu, in aanvulling op de vorige stappen, de query's zullen worden verzonden en de resultaten geschreven naar de console.

### <a name="about-the-sample"></a>Over het voorbeeld

Het voorbeeld maakt gebruik van een kleine hoeveelheid hotelgegevens, voldoende om de basisprincipes van het maken en opvragen van een Azure Cognitive Search-index aan te tonen.

De klasse **AzureSearchClient** bevat de configuratie, URL's en basisHTTP-aanvragen voor de zoekservice. Het **bestand index.js** laadt de configuratiegegevens voor de Azure Cognitive Search-service, de hotelgegevens `run` die worden geüpload voor indexering en, in de functie, orders en voert de verschillende bewerkingen uit.

Het algemene gedrag `run` van de functie is om de Azure Cognitive Search-index te verwijderen als deze bestaat, de index te maken, wat gegevens toe te voegen en sommige query's uit te voeren.  

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Quickstart van Node.js hebt u een reeks taken doorlopen om een index te maken, deze te laden met documenten en query's uit te voeren. We hebben bepaalde stappen, zoals het lezen van de configuratie en het definiëren van de query's, op de eenvoudigste manier. In een echte toepassing, zou u die zorgen in afzonderlijke modules willen zetten die flexibiliteit en inkapseling zouden verstrekken. 
 
Als u al een achtergrond hebt in Azure Cognitive Search, u dit voorbeeld gebruiken als springplank voor het proberen van suggesties (voortypen of automatisch aanvullen van query's), filters en gefaceteerde navigatie. Als u nieuw bent bij Azure Cognitive Search, raden we u aan andere zelfstudies uit te proberen om inzicht te krijgen in wat u maken. Bezoek de [documentatiepagina](https://azure.microsoft.com/documentation/services/search/) voor meer resources. 

> [!div class="nextstepaction"]
> [Azure Cognitive Search bellen vanaf een webpagina met Javascript](https://github.com/liamca/azure-search-javascript-samples)
