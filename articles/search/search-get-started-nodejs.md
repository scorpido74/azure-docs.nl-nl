---
title: "Snelstartgids: een zoek index maken in node. js met REST-Api's"
titleSuffix: Azure Cognitive Search
description: In deze Snelstartgids voor node. js leert u hoe u een index maakt, gegevens laadt en query's uitvoert op Azure Cognitive Search met behulp van Java script en de REST Api's.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: fd8a053eb4ff0805b95dc11db4206e1dd2edb184
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406937"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Snelstartgids: een Azure Cognitive Search-index maken in node. js met REST-Api's
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Maak een node. js-toepassing die een Azure Cognitive Search-index maakt, laadt en opvraagt. In dit artikel wordt beschreven hoe u de toepassing stap voor stap maakt. U kunt ook [de bron code en gegevens downloaden](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) en de toepassing uitvoeren vanaf de opdracht regel.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services, hulpprogram ma's en gegevens worden gebruikt in deze Quick Start.

+ [Node.js](https://nodejs.org).
+ [NPM](https://www.npmjs.com) moet worden geïnstalleerd door node. js.
+ In dit artikel vindt u een voor beeld van een structuur van de index en de bijbehorende documenten of vanuit de [ **Quick** start van de opslag plaats](https://github.com/Azure-Samples/azure-search-javascript-samples/).
+ [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken.

Aanbevolen:

* [Visual Studio Code](https://code.visualstudio.com).
* [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) -en [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) -uitbrei dingen voor VSCode.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Sleutels en Url's ophalen

Aanroepen naar de service vereisen een URL-eind punt en een toegangs sleutel voor elke aanvraag. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en haal de naam van uw zoek service op in de pagina **overzicht** van de zoek service. U kunt uw service naam bevestigen door de URL van het eind punt te controleren. Als uw eind punt-URL is `https://mydemo.search.windows.net`, wordt uw service naam `mydemo`.

2. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

    Haal ook de query sleutel op. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

![De service naam en de beheer-en query sleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Alle aanvragen vereisen een API-sleutel in de header van elke aanvraag die naar uw service wordt verzonden. Een geldige sleutel brengt een vertrouwens relatie tot stand, op basis van aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van een Power shell-console of een andere omgeving waarin u node. js hebt geïnstalleerd.

1. Maak een ontwikkelingsprogram Directory met de naam `quickstart`:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. Initialiseer een leeg project met NPM door `npm init`uit te voeren. Accepteer de standaard waarden, met uitzonde ring van de licentie, die u moet instellen op ' MIT '. 

1. Voeg pakketten toe die afhankelijk zijn van de code en hulp in ontwikkeling:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. Controleer of u de projecten en de bijbehorende afhankelijkheden hebt geconfigureerd door te controleren of het bestand **package. json** er ongeveer als volgt uitziet:

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
Maak een bestand **azure_search_config. json** om uw zoek service gegevens op te slaan:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

Vervang de `[SERVICE_NAME]` waarde door de naam van uw zoek service. Vervang `[ADMIN_KEY]` en `[QUERY_KEY]` door de sleutel waarden die u eerder hebt vastgelegd. 

## <a name="1---create-index"></a>1-index maken 

Maak een bestand **hotels_quickstart_index. json**.  In dit bestand wordt gedefinieerd hoe Azure Cognitive Search werkt met de documenten die u in de volgende stap gaat laden. Elk veld wordt geïdentificeerd door een `name` en heeft een opgegeven `type`. Elk veld bevat ook een reeks index kenmerken die aangeven of Azure Cognitive Search op het veld kan zoeken, filteren, sorteren en facetten. De meeste velden zijn eenvoudige gegevens typen, maar sommige, zoals `AddressType`, zijn complexe typen waarmee u uitgebreide gegevens structuren in uw index kunt maken.  Meer informatie over [ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) en [index kenmerken](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)vindt u hier. 

Voeg het volgende toe aan **hotels_quickstart_index. json** of [down load het bestand](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

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
    

Het is raadzaam om de specifieke gegevens van een bepaald scenario te scheiden van code die breed van toepassing is. De `AzureSearchClient` klasse die in het bestand **AzureSearchClient. js** is gedefinieerd, weet hoe u aanvraag-url's kunt maken, een aanvraag kunt indienen met de API voor ophalen en kunt reageren op de status code van het antwoord.

Aan de slag met **AzureSearchClient. js** door het **ophalen van het knoop punt** en het maken van een eenvoudige klasse. Isoleer de wijzigbaar delen van de klasse `AzureSearchClient` door aan de constructor de verschillende configuratie waarden door te geven:

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

De eerste verantwoordelijkheid van de klasse is om te weten hoe u Url's moet maken waarnaar de verschillende aanvragen worden verzonden. Bouw deze Url's met exemplaar methoden die gebruikmaken van de configuratie gegevens die worden door gegeven aan de klasse-constructor. U ziet dat de URL die ze maken specifiek is voor een API-versie en een argument moet hebben dat die versie opgeeft (in deze toepassing `2019-05-06`). 

Met de eerste van deze methoden wordt de URL voor de index zelf geretourneerd. Voeg de volgende methode toe binnen de hoofd tekst van de klasse:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

De volgende verantwoordelijkheid van `AzureSearchClient` is het maken van een asynchrone aanvraag met de API voor ophalen. De asynchrone statische methode `request` maakt gebruik van een URL, een teken reeks waarmee de HTTP-methode (' GET ', ' PUT ', ' POST ', ' DELETE ') wordt opgegeven, de sleutel die moet worden gebruikt in de aanvraag en een optioneel JSON-object. De variabele `headers` wijst de `queryKey` (of de beheerders sleutel of de alleen-lezen-query sleutel) toe aan de HTTP-aanvraag header API-Key. De aanvraag opties bevatten altijd de `method` die moeten worden gebruikt en de `headers`. Als `bodyJson` niet `null`, wordt de hoofd tekst van de HTTP-aanvraag ingesteld op de teken reeks representatie van `bodyJson`. De methode `request` retourneert de Promise van de ophaal-API voor het uitvoeren van de HTTP-aanvraag.

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

Voor demo doeleinden moet u gewoon een uitzonde ring genereren als de HTTP-aanvraag niet is voltooid. In een echte toepassing zou u waarschijnlijk enkele logboek registratie en diagnose van de HTTP-status code in de `response` van de zoek service aanvraag doen. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Voeg ten slotte de methoden toe om de Azure Cognitive Search-index te detecteren, te verwijderen en te maken. Deze methoden hebben allemaal dezelfde structuur:

* Haal het eind punt op waaraan de aanvraag wordt gedaan.
* Genereer de aanvraag met het juiste eind punt, HTTP-woord, API-sleutel en, indien van toepassing, een JSON-hoofd tekst. `indexExistsAsync()` en `deleteIndexAsync()` hebben geen JSON-hoofd tekst, maar `createIndexAsync(definition)` wel.
* `await` het antwoord op de aanvraag.  
* Handel over de status code van het antwoord.
* Een belofte van een geschikte waarde Retour neren (een Booleaanse, `this`of de query resultaten). 

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

Controleer of de methoden zich in de klasse bevinden en of u de klasse exporteert. Het ultraperifere bereik van **AzureSearchClient. js** moet zijn:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Een object gerichte klasse is een goede keuze voor de mogelijk herbruikbare **AzureSearchClient. js** -module, maar is niet nodig voor het hoofd programma, dat u moet plaatsen in een bestand met de naam **index. js**. 

Maak **index. js** en begin met het volgende:

* Het **nconf** -pakket, waarmee u de configuratie met JSON, omgevings variabelen of opdracht regel argumenten kunt opgeven.
* De gegevens uit het bestand **hotels_quickstart_index. json** .
* De module `AzureSearchClient`.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

Met het [ **nconf** -pakket](https://github.com/indexzero/nconf) kunt u configuratie gegevens opgeven in verschillende indelingen, zoals omgevings variabelen of de opdracht regel. In dit voor beeld wordt **nconf** op een eenvoudige manier gebruikt om het bestand **azure_search_config. json** te lezen en de inhoud van het bestand als een woorden boek te retour neren. Met de `get(key)`-functie van **nconf**kunt u snel controleren of de configuratie gegevens correct zijn aangepast. Ten slotte retourneert de functie de configuratie:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

Met de functie `sleep` wordt een `Promise` gemaakt die wordt omgezet na een opgegeven tijds duur. Met deze functie kan de app worden onderbroken tijdens het wachten tot asynchrone index bewerkingen zijn voltooid en beschikbaar worden. Het toevoegen van een dergelijke vertraging is doorgaans alleen nodig in demo's, testen en voorbeeld toepassingen.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Geef tot slot de belangrijkste asynchrone `run`-functie op en roep deze aan. Deze functie roept de andere functies op in volg orde, in afwachting van het oplossen van `Promise`s.

* De configuratie ophalen met de `getAzureConfiguration()` die u eerder hebt geschreven
* Een nieuw exemplaar van `AzureSearchClient` maken, waarbij waarden worden door gegeven vanuit uw configuratie
* Controleer of de index bestaat en verwijder deze
* Een index maken met behulp van de `indexDefinition` geladen vanuit **hotels_quickstart_index. json**

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
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

Vergeet niet dat de laatste aanroep naar `run()`! Het is het toegangs punt voor uw programma wanneer u `node index.js` in de volgende stap uitvoert.

U ziet dat `AzureSearchClient.indexExistsAsync()` en `AzureSearchClient.deleteIndexAsync()` geen para meters hebben. Deze functies roepen `AzureSearchClient.request()` zonder `bodyJson`-argument aan. In `true``bodyJson === null` `AzureSearchClient.request()`is de `init`-structuur ingesteld op alleen de HTTP-term (' GET ' voor `indexExistsAsync()` en ' DELETE ' voor `deleteIndexAsync()`) en de headers, die de aanvraag sleutel opgeven.  

De methode `AzureSearchClient.createIndexAsync(indexDefinition)` daarentegen _heeft_ een para meter. Met de functie `run` in `index.js`wordt de inhoud van het bestand **hotels_quickstart_index. json** door gegeven aan de methode `AzureSearchClient.createIndexAsync(indexDefinition)`. De `createIndexAsync()`-methode geeft deze definitie door aan `AzureSearchClient.request()`. In `AzureSearchClient.request()`, omdat `bodyJson === null` nu `false`, bevat de structuur van de `init` niet alleen de HTTP-term (' PUT ') en de headers, maar wordt de `body` ingesteld op de index definitie gegevens.

### <a name="prepare-and-run-the-sample"></a>Het voor beeld voorbereiden en uitvoeren

Gebruik een Terminal venster voor de volgende opdrachten.

1. Ga naar de map die het bestand **package. json** bevat en de rest van de code.
1. Installeer de pakketten voor het voor beeld met `npm install`.  Met deze opdracht worden de pakketten gedownload waarvan de code afhankelijk is.
1. Voer uw programma uit met `node index.js`.

Er wordt een reeks berichten weer gegeven met een beschrijving van de acties die door het programma worden uitgevoerd. Als u meer details van de aanvragen wilt zien, kunt u de [regels aan het begin van de `AzureSearchClient.request()` methode] opheffen https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) in **AzureSearchClient. js**. 

Open het **overzicht** van uw zoek service in de Azure Portal. Selecteer het tabblad **indexen** . Het volgende moet er ongeveer als volgt uitzien:

![Scherm opname van Azure Portal, overzicht van zoek services, tabblad indexen](media/search-get-started-nodejs/create-index-no-data.png)

In de volgende stap voegt u gegevens toe aan de index. 

## <a name="2---load-documents"></a>2-documenten laden 

In azure Cognitive Search zijn documenten gegevens structuren die beide invoer zijn voor het indexeren en uitvoeren van query's. U moet dergelijke gegevens naar de index posten. Dit maakt gebruik van een ander eind punt dan de bewerkingen die in de vorige stap zijn uitgevoerd. Open **AzureSearchClient. js** en voeg na `getIndexUrl()`de volgende methode toe:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

Net als bij `AzureSearchClient.createIndexAsync(definition)`hebt u een functie nodig waarmee `AzureSearchClient.request()` en de gegevens van het hotel worden door gegeven aan de hoofd tekst. Voeg `postDataAsync(hotelsData)` toe na `createIndexAsync(definition)`in **AzureSearchClient. js** :

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Invoer van documenten kan rijen zijn in een Data Base, blobs in Blob-opslag of, zoals in dit voor beeld, JSON-documenten op schijf. U kunt [Hotels. json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) downloaden of uw eigen **Hotels. json** -bestand maken met de volgende inhoud:

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

Als u deze gegevens wilt laden in uw programma, wijzigt u **index. js** door de regel toe te voegen aan `hotelData` in de buurt van de bovenkant:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Wijzig nu de functie `run()` in **index. js**. Het kan een paar seconden duren voordat de index beschikbaar is. Voeg daarom een onderbreking van 2 seconden toe voordat u `AzureSearchClient.postDataAsync(hotelData)`aanroept:

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

Voer het programma opnieuw uit met `node index.js`. Er wordt een iets andere set met berichten weer geven die u in stap 1 hebt gezien. Deze keer bestaat _de index en wordt het_ bericht weer gegeven over het verwijderen voordat de app de nieuwe index maakt en er gegevens naar verzendt. 

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Ga terug naar het tabblad **indexen** in het **overzicht** van uw zoek service op de Azure Portal. Uw index bevat nu vier documenten en verbruikt een bepaalde hoeveelheid opslag ruimte (het kan enkele minuten duren voordat de gebruikers interface de onderliggende status van de index weergeeft). Klik op de naam van de index die u wilt **Zoeken in de zoek Verkenner**. Op deze pagina kunt u experimenteren met gegevens query's. Probeer te zoeken in een query reeks van `*&$count=true` en u moet alle documenten en het aantal resultaten weer geven. Probeer met de query reeks `historic&highlight=Description&$filter=Rating gt 4` en ga terug naar één document met het woord "historisch" in `<em></em>` Tags. Lees meer over [het opstellen van een query in Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-query-overview). 

Reproduceer deze query's in code door **index. js** te openen en deze code toe te voegen aan de bovenkant:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

In hetzelfde bestand **index. js** schrijft u de `doQueriesAsync()` functie die hieronder wordt weer gegeven. Met deze functie wordt een `AzureSearchClient`-object gebruikt en wordt de methode `AzureSearchClient.queryAsync` toegepast op elk van de waarden in de `queries` matrix. Er wordt gebruikgemaakt van de functie `Promise.all()` om één `Promise` te retour neren die alleen wordt omgezet wanneer alle query's zijn opgelost. Met de aanroep van `JSON.stringify(body, null, 4)` wordt het query resultaat opgemaakt om beter leesbaar te zijn.

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

Wijzig de `run()` functie zodanig dat deze lang genoeg wordt gepauzeerd om de Indexeer functie te kunnen gebruiken. Vervolgens roept u de `doQueriesAsync(client)` op:

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

Als u `AzureSearchClient.queryAsync(query)`wilt implementeren, bewerkt u het bestand **AzureSearchClient. js**. Zoeken vereist een ander eind punt en de zoek termen worden URL-argumenten, dus voeg de functie toe `getSearchUrl(searchTerm)` naast de `getIndexUrl()` en `getPostDataUrl()` methoden die u al hebt geschreven.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

De functie `queryAsync(searchTerm)` wordt ook gebruikt in **AzureSearchClient. js** en volgt dezelfde structuur als `postDataAsync(data)` en de andere query functies: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

De zoek opdracht wordt uitgevoerd met de opdracht ' GET ' en geen hoofd tekst, omdat de zoek term deel uitmaakt van de URL. U ziet dat `queryAsync(searchTerm)` `this.queryKey`gebruikt, in tegens telling tot de andere functies die de beheerders sleutel hebben gebruikt. Query sleutels, zoals de naam al aangeeft, kunnen alleen worden gebruikt voor het uitvoeren van query's op de index en kan niet worden gebruikt om de index op een wille keurige manier te wijzigen. Query sleutels zijn daarom veiliger om te distribueren naar client toepassingen.

Voer het programma uit met `node index.js`. In aanvulling op de vorige stappen worden de query's verzonden en de resultaten die naar de-console worden geschreven.

### <a name="about-the-sample"></a>Over het voor beeld

In het voor beeld wordt gebruikgemaakt van een kleine hoeveelheid Hotel gegevens, voldoende om de basis beginselen van het maken en doorzoeken van een Azure Cognitive Search-index te demonstreren.

De **AzureSearchClient** -klasse kapselt de configuratie, url's en Basic HTTP-aanvragen voor de zoek service in. Het bestand **index. js** laadt de configuratie gegevens voor de Azure Cognitive Search-service, de Hotel gegevens die worden geüpload voor indexering en, in de bijbehorende `run` functie, orders en voert de verschillende bewerkingen uit.

Het algemene gedrag van de `run` functie bestaat uit het verwijderen van de Azure Cognitive Search index als deze bestaat, de index te maken, gegevens toe te voegen en enkele query's uit te voeren.  

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids voor node. js hebt u een reeks taken uitgevoerd voor het maken van een index, het laden van documenten en het uitvoeren van query's. We hebben bepaalde stappen uitgevoerd, zoals het lezen van de configuratie en het definiëren van de query's, op de eenvoudigste manier. In een echte toepassing wilt u deze problemen in afzonderlijke modules plaatsen die flexibiliteit en inkapseling zouden bieden. 
 
Als u al enige achtergrond hebt in azure Cognitive Search, kunt u dit voor beeld gebruiken als Spring plank voor het uitproberen van Voorst Ellen (query's voor type vooruit of automatisch aanvullen), filters en facet navigatie. Als u geen ervaring hebt met Azure Cognitive Search, raden we u aan andere zelf studies te proberen om een goed beeld te ontwikkelen van wat u kunt maken. Bezoek de [documentatiepagina](https://azure.microsoft.com/documentation/services/search/) voor meer resources. 

> [!div class="nextstepaction"]
> [Azure Cognitive Search aanroepen vanaf een webpagina met behulp van Java script](https://github.com/liamca/azure-search-javascript-samples)
