---
title: "Python Quick Start: Indexen maken, laden en query's uitvoeren met REST-Api's-Azure Search"
description: Hierin wordt uitgelegd hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van python, Jupyter-notebooks en de Azure Search REST API.
ms.date: 08/28/2019
author: heidisteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.openlocfilehash: 352d26a1f3e781f7dbb6e502caea44bdb45b398a
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129393"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Quickstart: Een Azure Search-index in python maken met behulp van Jupyter-notebooks
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [Power shell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Bouw een Jupyter-notebook dat een Azure Search index maakt, laadt en opvraagt met behulp van python en de [Azure Search rest-api's](https://docs.microsoft.com/rest/api/searchservice/). In dit artikel wordt uitgelegd hoe u stap voor stap een notitie blok maakt. U kunt ook [een voltooide Jupyter python-notebook downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-python-samples).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogram ma's zijn vereist voor deze Quick Start. 

+ [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), met python 3. x-en Jupyter-notebooks.

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt de gratis laag voor deze Quick Start gebruiken. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-search"></a>Verbinding maken met Azure Search

Start in deze taak een Jupyter-notebook en controleer of u verbinding kunt maken met Azure Search. U doet dit door een lijst met indexen van uw service op te vragen. In Windows met Anaconda3 kunt u Anaconda Navigator gebruiken om een notitie blok te starten.

1. Maak een nieuw Python3-notebook.

1. In de eerste cel laadt u de bibliotheken die worden gebruikt voor het werken met JSON en het formuleren van HTTP-aanvragen.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. In de tweede cel voert u de elementen van de aanvraag in die constanten zijn voor elke aanvraag. Vervang de naam van de zoek service (YOUR-SEARCH-SERVICE-NAME) en de beheer-API-sleutel (uw-beheer-API-sleutel) met geldige waarden. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Als u ConnectionError `"Failed to establish a new connection"`ontvangt, controleert u of de API-sleutel een primaire of secundaire Administrator sleutel is en of alle voor-en achterstallige tekens `/`(`?` en) aanwezig zijn.

1. Formuleer de aanvraag in de derde cel. Deze GET-aanvraag streeft naar de index verzameling van uw zoek service en selecteert de eigenschap name van bestaande indexen.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Voer elke stap uit. Als er indexen bestaan, bevat het antwoord een lijst met index namen. In de onderstaande scherm afbeelding heeft de service al een azureblob-index en een realestate-US-voorbeeld index.

   ![Python-script in Jupyter-notebook met HTTP-aanvragen voor Azure Search](media/search-get-started-python/connect-azure-search.png "Python-script in Jupyter-notebook met HTTP-aanvragen voor Azure Search")

   Een lege index verzameling retourneert daarentegen het volgende antwoord:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Een index maken

Tenzij u de portal gebruikt, moet er een index op de service bestaan voordat u gegevens kunt laden. Deze stap maakt gebruik van de [rest API Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) om een index schema naar de service te pushen.

Vereiste elementen van een index bevatten een naam, een verzameling velden en een sleutel. De verzameling velden definieert de structuur van een *document*. Elk veld heeft een naam, type en kenmerken die bepalen hoe het veld wordt gebruikt (bijvoorbeeld of het in volledige tekst kan worden doorzocht, kan worden gefilterd of kunnen worden opgehaald in Zoek resultaten). Binnen een index moet een van de velden van het `Edm.String` type worden aangewezen als de *sleutel* voor document identiteit.

Deze index heet "Hotels-Quick Start" en heeft de veld definities die hieronder worden weer gegeven. Het is een subset van een grotere [Hotels index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) die wordt gebruikt in andere scenario's. Deze Quick start is in deze Snelstartgids bijgesneden.

1. Plak in de volgende cel het volgende voor beeld in een cel om het schema op te geven. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Formuleer de aanvraag in een andere cel. Deze PUT-aanvraag streeft naar de index verzameling van uw zoek service en maakt een index op basis van het index schema dat u in de vorige cel hebt gegeven.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Voer elke stap uit.

   Het antwoord bevat de JSON-weer gave van het schema. Op de volgende scherm afbeelding wordt slechts een deel van het antwoord weer gegeven.

    ![Aanvraag voor het maken van een index](media/search-get-started-python/create-index.png "Aanvraag voor het maken van een index")

> [!Tip]
> U kunt het maken van een index ook controleren door de lijst indexen te controleren in de portal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-documenten laden

Als u documenten wilt pushen, gebruikt u een HTTP POST-aanvraag voor het URL-eind punt van uw index. De REST API is het [toevoegen, bijwerken of verwijderen van documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Documenten zijn afkomstig van [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) op github.

1. Geef in een nieuwe cel vier documenten op die voldoen aan het index schema. Geef een upload actie op voor elk document.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Formuleer de aanvraag in een andere cel. Deze POST-aanvraag streeft naar de docs-verzameling van de hotels-index Snelstartgids en duwt de documenten die in de vorige stap zijn opgenomen.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Voer elke stap uit om de documenten naar een index in uw zoek service te pushen. De resultaten moeten er ongeveer uitzien als in het volgende voor beeld. 

    ![Documenten verzenden naar een index](media/search-get-started-python/load-index.png "Documenten verzenden naar een index")

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

In deze stap ziet u hoe u een query kunt uitvoeren op een index met behulp van de [Zoek documenten rest API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Geef in een cel een query expressie op waarmee een lege zoek opdracht wordt uitgevoerd (Search = *), waarbij een niet-geordede lijst wordt geretourneerd (zoek score = 1,0) van wille keurige documenten. Azure Search retourneert standaard 50 overeenkomsten per keer. Als gestructureerd retourneert deze query een volledige document structuur en-waarden. Voeg $count = True toe om een telling van alle documenten in de resultaten op te halen.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Geef in een nieuwe cel het volgende voor beeld op om te zoeken naar de termen "Hotels" en "WiFi". Voeg $select toe om op te geven welke velden moeten worden meegenomen in de zoek resultaten.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. Formuleer een aanvraag in een andere cel. Deze GET-aanvraag streeft naar de docs-verzameling van de hotels-index Snelstartgids en koppelt de query die u in de vorige stap hebt opgegeven.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Voer elke stap uit. De resultaten moeten er ongeveer uitzien als in de volgende uitvoer. 

    ![Een index doorzoeken](media/search-get-started-python/search-index.png "Een index doorzoeken")

1. Voer een paar andere query voorbeelden uit om een idee te krijgen van de syntaxis. U kunt de `searchstring` door de volgende voor beelden vervangen en vervolgens de zoek opdracht opnieuw uitvoeren. 

   Een filter Toep assen: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Doe de twee beste resultaten:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Sorteren op een specifiek veld:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Als vereenvoudiging maakt deze Snelstartgids gebruik van een verkorte versie van de hotels-index. U kunt de volledige versie maken om meer interessante query's uit te proberen. Als u de volledige versie en alle 50-documenten wilt ophalen, voert u de wizard **gegevens importeren** uit, selecteert u *hotels-voor beeld* van de ingebouwde voorbeeld gegevens bronnen.

> [!div class="nextstepaction"]
> [Snelstart: Een index maken in de Azure Portal](search-get-started-portal.md)
