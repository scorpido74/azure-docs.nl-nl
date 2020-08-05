---
title: "Quickstart: Een zoekindex maken in Node.js met behulp van REST API's"
titleSuffix: Azure Cognitive Search
description: In deze Node.js-quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert in Azure Cognitive Search met behulp van JavaScript en de REST API's.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 043d5224c9bfefb189e36c0f4b744c93b376ace0
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420852"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Quickstart: Een Azure Cognitive Search-index maken in Node.js met behulp van REST API's
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Maak een Node.js-toepassing waarmee u een Azure Cognitive Search-index maakt en laadt en hierop query's uitvoert. In dit artikel ziet u stapsgewijs hoe u de toepassing maakt. U kunt ook [de broncode en gegevens downloaden](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) en de toepassing uitvoeren vanaf de opdrachtregel.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende software en services zijn gebruikt om deze quickstart te maken en te testen:

+ [Node.js](https://nodejs.org)

+ [NPM](https://www.npmjs.com) moet worden geïnstalleerd door Node.js

+ Een voorbeeldindexstructuur en overeenkomende documenten worden in dit artikel opgegeven, of zijn beschikbaar via de map [**quickstart** van de opslagplaats](https://github.com/Azure-Samples/azure-search-javascript-samples/)

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken.

Aanbevolen:

* [Visual Studio Code](https://code.visualstudio.com)

* [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)- en [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)-extensies voor VSCode.

<a name="get-service-info"></a>

## <a name="get-keys-and-urls"></a>Sleutels en URL's ophalen

Voor aanroepen naar de service zijn voor elke aanvraag een URL-eindpunt en een toegangssleutel vereist. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en haal op de pagina **Overzicht** de naam van uw zoekservice op. U kunt uw servicenaam bevestigen door de URL van het eindpunt te controleren. Als uw eindpunt-URL `https://mydemo.search.windows.net` is, wordt uw service naam `mydemo`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

    Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

![De naam van de service en de querysleutels voor beheer ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Voor alle aanvragen is een API-sleutel vereist in de header die naar uw service wordt verzonden. Me een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Open eerst een Powershell-console of andere omgeving waarin u Node.js hebt geïnstalleerd.

1. Maak een ontwikkelingsmap en noem deze map `quickstart`:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Initialiseer een leeg project met NPM door `npm init` uit te voeren. Accepteer de standaardwaarden, met uitzondering van de licentie. Deze moet u instellen op MIT. 

1. Voeg pakketten toe waar de code op vertrouwt en die helpt bij de ontwikkeling:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Bevestig dat u de projecten en de bijbehorende afhankelijkheden hebt geconfigureerd door te controleren of het bestand **package.json** er als volgt uitziet:

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

5. Maak een bestand **azure_search_config.json** om de gegevens van uw zoekservice in op te slaan:

    ```json
    {
        "serviceName" : "[SEARCH_SERVICE_NAME]",
        "adminKey" : "[ADMIN_KEY]",
        "queryKey" : "[QUERY_KEY]",
        "indexName" : "hotels-quickstart"
    }
    ```

Vervang de waarde `[SERVICE_NAME]` door de naam van uw zoekservice. Vervang `[ADMIN_KEY]` en `[QUERY_KEY]` door de sleutelwaarden die u eerder hebt vastgelegd. 

## <a name="1---create-index"></a>1 - Index maken 

Maak een bestand **hotels_quickstart_index.json**.  Met dit bestand wordt gedefinieerd hoe Azure Cognitive Search werkt met de documenten die u in de volgende stap gaat laden. Elk veld wordt geïdentificeerd door een `name` en beschikt over een opgegeven `type`. Elk veld beschikt ook over een reeks indexkenmerken waarmee wordt opgegeven of Azure Cognitive Search op basis van het veld kan zoeken, filteren en sorteren en facetten kan maken. De meeste velden zijn eenvoudige gegevenstypen, maar een aantal, zoals `AddressType`, is van een complex type waarmee u uitgebreide gegevensstructuren in uw index kunt maken.  U kunt meer lezen over [ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en [indexkenmerken](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes). 

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
    

Het is verstandig de specifieke kenmerken van een specifiek scenario te scheiden van de code die op grote schaal zal worden toegepast. De `AzureSearchClient`-klasse die in het bestand **AzureSearchClient.js** is gedefinieerd, weet hoe aanvraag-URL's moeten worden opgebouwd, een aanvraag wordt gemaakt met behulp van de Fetch-API en moet worden gereageerd op de statuscode van het antwoord.

Begin met **AzureSearchClient.js** te werken door het pakket **node-fetch** te importeren en een eenvoudige klasse te maken. Isoleer de onderdelen die kunnen worden gewijzigd van de `AzureSearchClient`-klasse door de verschillende configuratiewaarden aan de bijbehorende constructor door te geven:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2020-06-30';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

De eerste verantwoordelijkheid van de klasse is weten hoe URL's moeten worden gebouwd waarnaar de verschillende aanvragen moeten worden verzonden. Bouw deze URL's met instantiemethoden waarvoor de configuratiegegevens worden gebruikt die aan de klasseconstructor zijn doorgegeven. U ziet dat de URL die hiermee wordt gebouwd specifiek is voor een API-versie en over een argument moet beschikken waarmee die versie (in deze toepassing, `2020-06-30`) wordt opgegeven. 

Met de eerste methode wordt de URL voor de index zelf geretourneerd. Voeg de volgende methode toe in de hoofdtekst van de klasse:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

De volgende verantwoordelijkheid van `AzureSearchClient` is het maken van een asynchrone aanvraag met de Fetch-API. Met de asynchrone statische methode `request` worden een URL, een tekenreeks waarmee de HTTP-methode ('GET', 'PUT', 'POST', 'DELETE') wordt opgegeven, de sleutel die moet worden gebruikt in de aanvraag en een optioneel JSON-object opgehaald. Met de variabele `headers` wordt de `queryKey` (de beheerderssleutel of de alleen-lezen querysleutel) toegewezen aan de HTTP-aanvraagheader 'api-key'. De aanvraagopties bevatten altijd de `method` die moeten worden gebruikt en de `headers`. Als `bodyJson` niet `null` is, wordt de hoofdtekst van de HTTP-aanvraag ingesteld op de tekenreeksweergave van `bodyJson`. De `request`-methode retourneert de Promise van de Fetch-API's om de HTTP-aanvraag uit te voeren.

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

Voor demonstratiedoeleinden gaan we een uitzondering instellen als de HTTP-aanvraag niet is gelukt. In een echte toepassing zult u waarschijnlijk de logboeken doorzoeken en de HTTP-statuscode in de `response` van de zoekserviceaanvraag diagnosticeren. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Voeg als laatste de methoden toe voor het detecteren, verwijderen en maken van de Azure Cognitive Search-index. Al deze methoden hebben dezelfde structuur:

* Haal het eindpunt op waarop de aanvraag wordt geplaatst.
* Genereer de aanvraag met het juiste eindpunt, HTTP-woord, API-sleutel en, indien van toepassing, een JSON-hoofdtekst. `indexExistsAsync()` en `deleteIndexAsync()` hebben geen JSON-hoofdtekst, maar `createIndexAsync(definition)` wel.
* `await` het antwoord op de aanvraag.  
* Reageer op de statuscode van het antwoord.
* Retourneer een Promise van een geschikte waarde (een Booleaanse waarde, `this`, of de queryresultaten). 

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

Bevestig dat uw methoden binnen de klasse vallen en dat u de klasse gaat exporteren. Het uiterste bereik van **AzureSearchClient.js** moet zijn:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Een op objecten gerichte klasse was een goede keuze voor de mogelijk herbruikbare **AzureSearchClient.js**-module, maar deze is niet nodig voor het hoofdprogramma, dat u in het bestand **index.js** moet plaatsen. 

Maak **index.js** en begin door het volgende in te voeren:

* Het **nconf**-pakket, waarmee u over de flexibiliteit beschikt om de configuratie met JSON, omgevingsvariabelen of opdrachtregelargumenten op te geven.
* De gegevens uit het bestand **hotels_quickstart_index.json**.
* De module `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Met het [**nconf**-pakket](https://github.com/indexzero/nconf) kunt u configuratiegegevens opgeven in verschillende indelingen, zoals omgevingsvariabelen of de opdrachtregel. In dit voorbeeld wordt **nconf** op een basismanier gebruikt om het bestand **azure_search_config.json** te lezen en de inhoud van dat bestand te retourneren als een woordenboek. Met behulp van de functie `get(key)` van **nconf** kunt u snel controleren of de configuratiegegevens goed zijn aangepast. Ten slotte wordt met de functie de volgende configuratie geretourneerd:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME]' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Met de `sleep`-functie wordt een `Promise` gemaakt die na een opgegeven periode wordt omgezet. Met behulp van deze functie kunt u de app pauzeren terwijl u wacht tot asynchrone indexbewerkingen zijn voltooid en beschikbaar zijn. Een dergelijke vertraging is doorgaans alleen nodig in demo's, testen en voorbeeldtoepassingen.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Als laatste moet u de asynchrone `run`-functie opgeven en aanroepen. Met deze functie worden de andere functies in een vaste volgorde aangeroepen, in afwachting van wat nodig is om `Promise`'s om te zetten.

* Haal de configuratie op met de `getAzureConfiguration()` die u eerder hebt geschreven
* Maak een nieuwe `AzureSearchClient`-instantie en voer waarden uit uw configuratie in
* Controleer of de index bestaat en verwijder deze als dat het geval is
* Maak een index met behulp van de `indexDefinition` die u uit **hotels_quickstart_index.json** hebt geladen

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
    } catch (x) {
        console.log(x);
    }
}

run();
```

Vergeet die laatste aanroep naar `run()` niet. Dit is het toegangspunt voor uw programma wanneer u in de volgende stap `node index.js` gaat uitvoeren.

U ziet dat `AzureSearchClient.indexExistsAsync()` en `AzureSearchClient.deleteIndexAsync()` geen parameters hebben. Met deze functies wordt `AzureSearchClient.request()` aangeroepen zonder `bodyJson`-argument. Omdat `bodyJson === null` `true` is, wordt de `init`-structuur in `AzureSearchClient.request()` ingesteld op alleen het HTTP-woord ('GET' voor `indexExistsAsync()` en 'DELETE' voor `deleteIndexAsync()`) en de headers, waarmee de aanvraagsleutel wordt opgegeven.  

Voor de `AzureSearchClient.createIndexAsync(indexDefinition)`-methode wordt daarentegen _wel_ een parameter gebruikt. De `run`-functie in `index.js` wordt gebruikt om de inhoud van het bestand **hotels_quickstart_index.json** door te geven aan de `AzureSearchClient.createIndexAsync(indexDefinition)`-methode. Met de `createIndexAsync()`-methode wordt deze definitie doorgegeven aan `AzureSearchClient.request()`. Omdat `bodyJson === null` nu `false` is, bevat de `init`-structuur in `AzureSearchClient.request()` nu niet alleen het HTTP-woord ('PUT') en de headers, maar wordt ook de `body` ingesteld op de indexdefinitiegegevens.

### <a name="prepare-and-run-the-sample"></a>Het voorbeeld voorbereiden en uitvoeren

Gebruik een terminalvenster voor de volgende opdrachten.

1. Navigeer naar de map met het bestand **package.json** en de rest van uw code.
1. Installeer de pakketten voor het voorbeeld met `npm install`.  Met deze opdracht worden de pakketten gedownload waarvan de code afhankelijk is.
1. Voer uw programma uit met `node index.js`.

U ziet nu een reeks berichten waarin de acties worden beschreven die door het programma worden uitgevoerd. Als u meer details van de aanvragen wilt zien, kunt u de opmerkingen verwijderen van de [regels aan het begin van de `AzureSearchClient.request()`-methode]https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) in **AzureSearchClient.js**. 

Open het **Overzicht** van uw zoekservice in Azure Portal. Selecteer het tabblad **Indexen**. Er verschijnt informatie die er ongeveer als volgt uitziet:

![Schermopname van Azure Portal, overzicht van de zoekservice, het tabblad Indexen](media/search-get-started-nodejs/create-index-no-data.png)

In de volgende stap gaat u gegevens aan de index toevoegen. 

## <a name="2---load-documents"></a>2 - Documenten laden 

In Azure Cognitive Search zijn documenten gegevensstructuren die zowel de invoer van indexeringen als de uitvoer van query's zijn. U moet gegevens via POST toevoegen aan de index. Hiervoor wordt een ander eindpunt gebruikt dan voor de bewerkingen in de vorige stap. Open **AzureSearchClient.js** en voeg de volgende methode toe na `getIndexUrl()`:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Net zoals bij `AzureSearchClient.createIndexAsync(definition)` hebt u een functie nodig waarmee `AzureSearchClient.request()` wordt aangeroepen en de hotelgegevens worden doorgegeven aan de bijbehorende hoofdtekst. Voeg in **AzureSearchClient.js** `postDataAsync(hotelsData)` toe na `createIndexAsync(definition)`:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 De documentinvoer bestaat mogelijk uit rijen in een database, blobs in Blob Storage of, zoals in dit voorbeeld, JSON-documenten op een schijf. U kunt [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) downloaden of uw eigen **hotels.json**-bestand maken met de volgende inhoud:

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

Pas **index.js** aan om deze gegevens in uw programma te laden door de regel bovenaan, die naar `hotelData` verwijst, toe te voegen:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Pas nu de `run()`-functie in **index.js** aan. Het kan enkele minuten duren voordat de index beschikbaar is, dus wacht 2 seconden voordat u `AzureSearchClient.postDataAsync(hotelData)` aanroept:

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

Voer het programma opnieuw uit met `node index.js`. U ziet nu een iets andere reeks berichten dan in stap 1. Nu bestaat de index _wel_ en moet u een bericht zien over het verwijderen van de index voordat de nieuwe index wordt gemaakt en hierin gegevens worden geplaatst. 

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Ga terug naar het tabblad **Indexen** in het **Overzicht** van uw zoekservice in Azure Portal. Uw index bevat nu vier documenten en er wordt enige opslagruimte verbruikt (het kan enige minuten duren voordat de onderliggende status van de index goed wordt weergegeven in de gebruikersinterface). Klik op de indexnaam om naar de **Search Explorer** te gaan. Op deze pagina kunt u experimenteren met gegevensquery's. Zoek in een querytekenreeks van `*&$count=true`; als het goed is, krijgt u al uw documenten en het aantal resultaten terug. Probeer de querytekenreeks `historic&highlight=Description&$filter=Rating gt 4`; als het goed is, krijgt u één document terug, met het woord 'historic' tussen `<em></em>`-tags. Lees meer over [het opstellen van een query in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Reproduceer deze query's in de code door **index.js** te openen en deze code bovenaan toe te voegen:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

In hetzelfde bestand **index.js** schrijft u de onderstaande `doQueriesAsync()`-functie. Met deze functie wordt een `AzureSearchClient`-object gebruikt en wordt de `AzureSearchClient.queryAsync`-methode toegepast op elke waarde in de `queries`-matrix. De `Promise.all()`-functie wordt gebruikt om één `Promise` te retourneren die alleen wordt omgezet wanneer alle query's zijn omgezet. Met de oproep aan `JSON.stringify(body, null, 4)` wordt het queryresultaat geformatteerd, zodat het beter leesbaar wordt.

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

Pas de `run()`-functie aan zodat er lang genoeg wordt gewacht tot de indexeerfunctie werkt. Roep vervolgens de `doQueriesAsync(client)`-functie aan:

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

Als u `AzureSearchClient.queryAsync(query)` wilt implementeren, bewerkt u het bestand **AzureSearchClient.js**. Voor zoekopdrachten is een ander eindpunt vereist en worden de zoektermen URL-argumenten. Voeg de functie `getSearchUrl(searchTerm)` dus toe samen met de `getIndexUrl()`- en `getPostDataUrl()`-methoden die u al hebt geschreven.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

De `queryAsync(searchTerm)`-functie kijkt ook in **AzureSearchClient.js** en volgt dezelfde structuur als `postDataAsync(data)` en de andere queryfuncties: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

De zoekopdracht wordt uitgevoerd met het woord 'GET', zonder hoofdtekst, omdat de zoekterm deel uitmaakt van de URL. U ziet dat voor `queryAsync(searchTerm)` `this.queryKey` wordt gebruikt, in tegenstelling tot de andere functies waarvoor de beheersleutel is gebruikt. Querysleutels kunnen, zoals de naam al aangeeft, alleen worden gebruikt voor het uitvoeren van een query op de index en kunnen niet worden gebruikt om de index op welke manier dan ook aan te passen. Querysleutels zijn daarom veiliger om naar clienttoepassingen te distribueren.

Voer het programma uit met `node index.js`. Nu worden de query's, als aanvulling op de vorige stappen, verzonden en worden de resultaten naar de console geschreven.

### <a name="about-the-sample"></a>Over het voorbeeld

Voor het voorbeeld wordt een klein aantal hotelgegevens gebruikt, voldoende om de basisbeginselen te laten zien van het maken van een Azure Cognitive Search-index en het uitvoeren van query's op die index.

De **AzureSearchClient**-klasse bevat de configuratie, URL's en HTTP-basisaanvragen voor de zoekservice. Het bestand **index.js** laadt de configuratiegegevens voor de Azure Cognitive Search-service, de hotelgegevens die worden geüpload voor indexering, en in de bijbehorende `run`-functie worden de verschillende bewerkingen gerangschikt en uitgevoerd.

Het algemene gedrag van de `run`-functie is het verwijderen van de Azure Cognitive Search-index (als deze bestaat), het maken van de index, het toevoegen van een aantal gegevens en het uitvoeren van een aantal query's.  

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Node.js-quickstart hebt u een reeks taken uitgevoerd om een index te maken, hier documenten in te laden en query's uit te voeren. We hebben een aantal stappen uitgevoerd, zoals het lezen van de configuratie en het definiëren van de query's, op de eenvoudigst mogelijke manier. In een echte toepassing zou u die problemen in afzonderlijke modules plaatsen, voor meer flexibiliteit en inkapseling. 
 
Als u al enige ervaring met Azure Cognitive Search hebt, kunt u dit voorbeeld gebruiken als springplank om een suggestiefunctie (type-ahead of automatisch aangevulde query's), filters en facetnavigatie te proberen. Als u Azure Cognitive Search voor het eerst gebruikt, is het raadzaam andere zelfstudies te bekijken om inzicht te verwerven in wat u zoal kunt maken. Bezoek de [documentatiepagina](https://azure.microsoft.com/documentation/services/search/) voor meer resources. 

> [!div class="nextstepaction"]
> [Azure Cognitive Search aanroepen vanaf een webpagina met behulp van Javascript](https://github.com/liamca/azure-search-javascript-samples)
