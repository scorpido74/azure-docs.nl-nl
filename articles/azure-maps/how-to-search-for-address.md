---
title: Zoeken naar een locatie met Azure Maps Search-services | Microsoft Azure Maps
description: In dit artikel leert u hoe u naar een locatie zoeken met de Microsoft Azure Maps Search Service voor geocodering en omgekeerde geocodering.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335435"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Zoeken naar een locatie met Azure Maps Search-services

De Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) is een set RESTful API's die zijn ontworpen om ontwikkelaars te helpen bij het zoeken naar adressen, plaatsen, bedrijfsvermeldingen op naam of categorie en andere geografische informatie. Naast het ondersteunen van traditionele geocodering, kunnen services ook geocodeadressen omkeren en straten doorkruisen op basis van breedtegraden en lengtegraden. Breedte- en lengtewaarden die door de zoekopdracht worden geretourneerd, kunnen worden gebruikt als parameters in andere Azure Maps-services, zoals [route-](https://docs.microsoft.com/rest/api/maps/route) en [weerservices.](https://docs.microsoft.com/rest/api/maps/weather)

In dit artikel leer je, hoe:

* Latitude- en lengtecoördinaten aanvragen voor een adres (locatie geocodeadres) met behulp [van Search Address API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Zoeken naar een adres of Point of Interest (POI) met behulp van [Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Zoek naar een adres samen met eigenschappen en coördinaten
* Een [reverse address search maken](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) om de coördinatenlocatie naar het adres te vertalen
* Zoeken naar een cross street met [zoekadres Reverse Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, moet u eerst een Azure Maps-account maken en u een abonnementssleutel voor het account kaarten opleveren. Volg instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-accountabonnement te maken en volg de stappen in de primaire [sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account te krijgen. Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

In dit artikel wordt de [Postman-app](https://www.getpostman.com/apps) gebruikt om REST-oproepen te maken. U elke API-ontwikkelomgeving gebruiken die u verkiest.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Breedte- en lengtegraad aanvragen voor een adres (geocodering)

In dit voorbeeld gebruiken we Azure Maps [Get Search Address API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) om het adres om te zetten in breedte- en lengtecoördinaten. U een volledig of gedeeltelijk adres doorgeven aan de API en een antwoord ontvangen met gedetailleerde adreseigenschappen zoals straat, postcode en land/regio, evenals positionele waarden in breedte- en lengtegraad.

Als u een set adressen hebt om te geocoderen, u de Api voor [de batchpostadres](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) gebruiken om een batch query's in één API-aanroep te verzenden.

1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef deze naam **Adreszoeken**.

2. Selecteer op het tabblad Bouwer de **METHODE HTTP OPHALEN,** voer de aanvraag-URL voor uw API-eindpunt in en selecteer eventueel een autorisatieprotocol.

![Zoeken op adres](./media/how-to-search-for-address/address_search_url.png)

| Parameter | Voorgestelde waarde |
|---------------|------------------------------------------------| 
| HTTP-methode | GET |
| Aanvraag-URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorisatie | Geen Auth |

3. Klik **op Params**en voer de volgende sleutel-/waardeparen in die u wilt gebruiken als query- of padparameters in de aanvraag-URL: 

![Zoeken op adres](./media/how-to-search-for-address/address_search_params.png) 

| Sleutel | Waarde | 
|------------------|-------------------------| 
| api-versie | 1.0 | 
| abonnementssleutel | \<uw Azure Maps-sleutel\> | 
| query | 400 Broad St, Seattle, WA 98109 | 

4. Klik **op Verzenden** en bekijk de reactietekst. 

In dit geval hebt u een volledige adresquery opgegeven en ontvangt u één resultaat in de antwoordtekst. 

5. Bewerk in Params de querytekenreeks naar de volgende waarde: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Voeg de volgende sleutel/waardepaar toe aan de sectie **Params** en klik op **Verzenden:** 

| Sleutel | Waarde | 
|-----|------------| 
| Typeahead | waar | 

De **tekstregelvlag** geeft aan dat de API voor adreszoeken de query als een gedeeltelijke invoer moet behandelen en een array met voorspellende waarden moet retourneren.

## <a name="using-fuzzy-search-api"></a>Fuzzy Search API gebruiken

Azure Maps[ Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wordt aanbevolen service te gebruiken wanneer u niet weet wat uw gebruikersingangen zijn voor een zoekopdracht. De API combineert Point of Interest (POI) zoeken en geocodering in een canonieke 'single-line search'. De API kan bijvoorbeeld invoer van elk adres of POI-tokencombinatie verwerken. Het kan ook worden gewogen met een contextuele positie (lat./lon. paar), volledig beperkt door een coördinaat en straal, of meer in het algemeen uitgevoerd zonder enig geo biasing ankerpunt.

De meeste zoekopdrachten `maxFuzzyLevel=1` standaard om prestaties te krijgen en ongebruikelijke resultaten te verminderen. Deze standaardinstelling kan naar behoefte per aanvraag worden `maxFuzzyLevel=2` overschreven door de queryparameter door te geven of `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Zoeken naar een adres met Fuzzy Search

1. Open de postbode-app, klik op Nieuw | Nieuw maken en selecteer **GET-aanvraag**. Voer een aanvraagnaam van **fuzzy search**in, selecteer een verzameling of map waarop u deze wilt opslaan en klik op **Opslaan**.

2. Selecteer op het tabblad Bouwer de **METHODE HTTP OPHALEN** en voer de aanvraag-URL voor uw API-eindpunt in.

    ![Fuzzy Zoeken](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorisatie | Geen Auth |

    Het **kenmerk Json** in het URL-pad bepaalt de antwoordnotatie. Dit artikel maakt gebruik van json voor gebruiksgemak en leesbaarheid. U vindt de beschikbare antwoord-indelingen in de **fuzzy-definitie voor zoeken** van de [map-functie-API-verwijzing.](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

3. Klik **op Params**en voer de volgende sleutel-/waardeparen in die u wilt gebruiken als query- of padparameters in de aanvraag-URL:

    ![Fuzzy Zoeken](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-versie | 1.0 |
    | abonnementssleutel | \<uw Azure Maps-sleutel\> |
    | query | Pizza |

4. Klik **op Verzenden** en bekijk de reactietekst.

    De dubbelzinnige query string voor "pizza" terug 10 [point of interest resultaat](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) in zowel de "pizza" en "restaurant" categorieën. Elk resultaat retourneert een adres, breedte- en lengtewaarden, weergavepoort en toegangspunten voor de locatie.
  
    De resultaten zijn gevarieerd voor deze query, niet gekoppeld aan een bepaalde referentielocatie. U de parameter **countrySet** gebruiken om alleen de landen/regio's op te geven waarvoor uw toepassing dekking nodig heeft. Het standaardgedrag is om de hele wereld te doorzoeken, waardoor mogelijk onnodige resultaten worden retourneren.

5. Voeg de volgende sleutel/waardepaar toe aan de sectie **Params** en klik op **Verzenden:**

    | Sleutel | Waarde |
    |------------------|-------------------------|
    | landSet | VS |
  
    De resultaten zijn nu begrensd door de landcode en de query retourneert pizzarestaurants in de Verenigde Staten.
  
    Als u resultaten wilt leveren voor een locatie, u een interessant punt opvragen en de geretourneerde breedte- en lengtewaarden gebruiken in uw gesprek naar de fuzzy zoekservice. In dit geval hebt u de zoekservice gebruikt om de locatie van de Seattle Space Needle terug te geven en de lat gebruikt. / lon. waarden om de zoekopdracht te oriënteren.
  
6. Voer in Params de volgende sleutel-/waardeparen in en klik op **Verzenden:**

    ![Fuzzy Zoeken](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Sleutel | Waarde |
    |-----|------------|
    | Lat | 47.620525 |
    | Lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Zoeken naar een adres met Reverse Address Search

Azure Maps [Get Search Address Reverse API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) helpt bij het vertalen van een coördinaat (voorbeeld: 37.786505, -122.3862) naar een door mensen begrijpelijk adres. Meestal is dit nodig in het bijhouden van toepassingen waar u een GPS-feed van het apparaat of asset ontvangt en wilt weten welk adres waar de coördinaat zich bevindt.
Als u een set coördinatenlocaties hebt om geocode om te keren, u [de Post Search Address Reverse Batch API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) gebruiken om een batch query's in één API-aanroep te verzenden.


1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef deze naam **Zoeken naar een omgekeerd adres**.

2. Selecteer op het tabblad Bouwer de **METHODE HTTP OPHALEN** en voer de aanvraag-URL voor uw API-eindpunt in.
  
    ![URL voor zoeken naar adres omkeren](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorisatie | Geen Auth |
  
3. Klik **op Params**en voer de volgende sleutel-/waardeparen in die u wilt gebruiken als query- of padparameters in de aanvraag-URL:
  
    ![Zoekparameters voor adres omkeren](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-versie | 1.0 |
    | abonnementssleutel | \<uw Azure Maps-sleutel\> |
    | query | 47.591180,-122.332700 |
  
4. Klik **op Verzenden** en bekijk de reactietekst.

    Het antwoord bevat belangrijke adresgegevens over Safeco Field.
  
5. Voeg de volgende sleutel/waardepaar toe aan de sectie **Params** en klik op **Verzenden:**

    | Sleutel | Waarde |
    |-----|------------|
    | getal | waar |

    Als de [parameter getalquery](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) met de aanvraag wordt verzonden, kan het antwoord de zijkant van de straat (links of rechts) en ook een verschuivingspositie voor dat getal bevatten.
  
6. Voeg de volgende sleutel/waardepaar toe aan de sectie **Params** en klik op **Verzenden:**

    | Sleutel | Waarde |
    |-----|------------|
    | retoursnelheidLimiet | waar |
  
    Wanneer de parameter [returnSpeedLimit-query](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) is ingesteld, retourneert het antwoord de geboekte snelheidslimiet.

7. Voeg de volgende sleutel/waardepaar toe aan de sectie **Params** en klik op **Verzenden:**

    | Sleutel | Waarde |
    |-----|------------|
    | retourRoadUse | waar |

    Wanneer de parameter [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) query is ingesteld, retourneert het antwoord de matrix voor het weggebruik voor omgekeerde geocodes op straatniveau.

8. Voeg de volgende sleutel/waardepaar toe aan de sectie **Params** en klik op **Verzenden:**

    | Sleutel | Waarde |
    |-----|------------|
    | roadGebruik | waar |

    U de omgekeerde geocodequery beperken tot een specifiek type weg met behulp van de parameter [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) query.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Zoek naar cross street met Reverse Address Cross Street Search

1. Klik in Postman op **Nieuw verzoek** | **GET-aanvraag** en geef deze naam **Reverse Address Cross Street Search**.

2. Selecteer op het tabblad Bouwer de **METHODE HTTP OPHALEN** en voer de aanvraag-URL voor uw API-eindpunt in.
  
    ![Omgekeerd adres Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorisatie | Geen Auth |
  
3. Klik **op Params**en voer de volgende sleutel-/waardeparen in die u wilt gebruiken als query- of padparameters in de aanvraag-URL:
  
    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-versie | 1.0 |
    | abonnementssleutel | \<uw Azure Maps-sleutel\> |
    | query | 47.591180,-122.332700 |
  
4. Klik **op Verzenden** en bekijk de reactietekst.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [API-documentatie van Azure Maps Search Service REST.](https://docs.microsoft.com/rest/api/maps/search)
- Meer informatie over [de aanbevolen procedures van Azure Maps Search Service](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) en hoe u uw query's optimaliseren.
