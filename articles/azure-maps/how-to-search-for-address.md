---
title: Zoeken naar een locatie met behulp van Azure Maps-Zoek Services | Microsoft Azure kaarten
description: In dit artikel wordt beschreven hoe u een locatie kunt zoeken met behulp van de Microsoft Azure Maps Search Service voor geocodering en omgekeerde geocodering.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 40066f24fec00610a1efd10b2cb874b1100acdee
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209882"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Zoeken naar een locatie met behulp van Azure Maps-Zoek Services

De Azure Maps [Search service](https://docs.microsoft.com/rest/api/maps/search) is een reeks rest api's waarmee ontwikkel aars adressen, plaatsen, bedrijfs vermeldingen op naam of categorie en andere geografische informatie kunnen doorzoeken. Naast het ondersteunen van traditionele geocodering kunnen services ook Geocode-adressen en Cross-Streets omkeren op basis van de breedte graad en lengte graad. De breedte-en lengte waarden die worden geretourneerd door de zoek opdracht, kunnen worden gebruikt als para meters in andere Azure Maps Services, zoals [route](https://docs.microsoft.com/rest/api/maps/route) en [weer](https://docs.microsoft.com/rest/api/maps/weather) Services.

In dit artikel leert u het volgende:

* De coördinaten van de breedte graad en lengte graad voor een adres (locatie van een geocode) aanvragen met behulp van de [API voor Zoek adressen]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
* Zoeken naar een adres of belang stelling (POI) met behulp van [fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Een adres samen met de eigenschappen en coördinaten zoeken
* Een [omgekeerde adres zoekactie](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) maken om de coördinaten locatie naar het adres te vertalen
* Zoeken naar een cross-straat met behulp van [Address reverse Cross Street-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, moet u eerst een Azure Maps-account maken en uw abonnements sleutel voor Maps-accounts ophalen. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) voor het maken van een abonnement op een Azure Maps-account en volg de stappen in de [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account op te halen. Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

In dit artikel wordt gebruikgemaakt van de [app postman](https://www.getpostman.com/apps) om rest-aanroepen te bouwen. U kunt elke gewenste API-ontwikkel omgeving gebruiken.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>De breedte graad en lengte graad voor een adres aanvragen (geocodering)

In dit voor beeld gebruiken we Azure Maps [Zoek adres-API ophalen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) om een adres te converteren naar de coördinaten voor breedte graad en lengte graad. U kunt een volledig of gedeeltelijk adres door geven aan de API en een antwoord ontvangen dat gedetailleerde adres eigenschappen bevat, zoals straat, post code en land/regio, evenals positionele waarden in breedte graad en lengte graad.

Als u een reeks adressen naar Geocode hebt, kunt u [post Search Address batch-API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) gebruiken om een batch met query's te verzenden in één API-aanroep.

1. Klik in postman op **nieuwe aanvraag** | **aanvraag ophalen** en geef de naam op voor de **Zoek opdracht**.

2. Op het tabblad opbouw functie selecteert u de methode http **ophalen** , voert u de aanvraag-URL in voor uw API-eind punt en selecteert u een autorisatie protocol, indien van toepassing.

![Adres zoeken](./media/how-to-search-for-address/address_search_url.png)

| Parameter | Voorgestelde waarde |
|---------------|------------------------------------------------| 
| HTTP-methode | GET |
| Aanvraag-URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Autorisatie | Geen verificatie |

3. Klik op **params**en voer de volgende sleutel/waarde-paren in om te gebruiken als query-of Path-para meters in de aanvraag-URL: 

![Adres zoeken](./media/how-to-search-for-address/address_search_params.png) 

| Sleutel | Waarde | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| abonnement-sleutel | \<uw Azure Maps-sleutel\> | 
| query | 400 brede St, Seattle, WA 98109 | 

4. Klik op **verzenden** en controleer de tekst van het antwoord. 

In dit geval hebt u een volledige adres query opgegeven en krijgt u één resultaat in de hoofd tekst van het antwoord. 

5. Bewerk in params de query reeks in de volgende waarde: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**: 

| Sleutel | Waarde | 
|-----|------------| 
| typeahead | true | 

De vlag **typeahead** geeft aan dat de zoek-API van het adres de query als gedeeltelijke invoer behandelt en een matrix van voorspellende waarden retourneert.

## <a name="using-fuzzy-search-api"></a>Fuzzy Search-API gebruiken

Azure Maps[ fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) wordt aanbevolen service te gebruiken wanneer u niet weet wat uw gebruikers invoer zijn voor een zoek opdracht. De API combineert POI-Zoek resultaten (Point of interest) en Geocode ring in een canonieke zoek opdracht met één regel. De API kan bijvoorbeeld invoer van een wille keurige combi natie van adressen of POI verwerken. Het kan ook worden gewogen met een contextuele positie (lat./Lon. paar), volledig beperkt door een coördinaat en RADIUS, of meer in het algemeen zonder enige geo-veranderings anker punt.

De meeste zoek query's zijn standaard `maxFuzzyLevel=1` om prestaties te verkrijgen en de ongebruikelijke resultaten te verminderen. Deze standaard instelling kan worden overschreven als nodig per aanvraag door door gegeven in de query parameter `maxFuzzyLevel=2` of `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Zoeken naar een adres met behulp van fuzzy Search

1. Open de Postman-app, klik op nieuw | Maak een nieuwe en selecteer **aanvraag ophalen**. Voer de naam van de aanvraag van **fuzzy zoeken**in, selecteer een verzameling of map om deze op te slaan en klik op **Opslaan**.

2. Selecteer op het tabblad opbouw functie de methode http **ophalen** en voer de aanvraag-URL in voor uw API-eind punt.

    ![Zoek actie op fuzzy](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorisatie | Geen verificatie |

    Het **JSON** -kenmerk in het URL-pad bepaalt de antwoord indeling. In dit artikel wordt JSON gebruikt voor gebruiks gemak en lees baarheid. U vindt de beschik bare antwoord indelingen in de **fuzzy zoek term** van de [functie voor het toewijzen van functionele API-verwijzingen](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Klik op **params**en voer de volgende sleutel/waarde-paren in om te gebruiken als query-of Path-para meters in de aanvraag-URL:

    ![Zoek actie op fuzzy](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | abonnement-sleutel | \<uw Azure Maps-sleutel\> |
    | query | pizza |

4. Klik op **verzenden** en controleer de tekst van het antwoord.

    De dubbel zinnige query reeks voor ' pizza ' heeft 10 [resultaat van de rente](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) in de categorieën ' pizza ' en ' restaurant ' geretourneerd. Elk resultaat retourneert een adres, breedte graad en lengte graad, weergave poort en ingangs punten voor de locatie.
  
    De resultaten zijn variërend voor deze query en zijn niet gekoppeld aan een bepaalde verwijzings locatie. U kunt de para meter **landset** gebruiken om alleen de landen/regio's op te geven waarvoor uw toepassing behoefte heeft. Het standaard gedrag is om de hele wereld te doorzoeken, waardoor mogelijk onnodige resultaten worden geretourneerd.

5. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |------------------|-------------------------|
    | countrySet | VS |
  
    De resultaten worden nu begrensd door de land code en de query retourneert pizza restaurants in de Verenigde Staten.
  
    Als u de resultaten voor een locatie wilt opgeven, kunt u een query uitvoeren op een belang rijke punten en de geretourneerde waarden voor breedte graad en lengte graad gebruiken in uw oproep naar de zoek service van fuzzy. In dit geval hebt u de Search-service gebruikt om de locatie van de naald ruimte van Seattle te retour neren en de lat te gebruiken. Lon. waarden voor het richten van de zoek opdracht.
  
6. Voer in params de volgende sleutel/waarde-paren in en klik op **verzenden**:

    ![Zoek actie op fuzzy](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Sleutel | Waarde |
    |-----|------------|
    | lat | 47,620525 |
    | Lon | -122,349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Zoeken naar een adres met behulp van Reverse Address Search

Azure Maps [Zoek adres voor reverse-API ophalen]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) helpt u een coördinaat (bijvoorbeeld: 37,786505,-122,3862) te vertalen naar een adres dat mensen begrijpt. Dit is vaak nodig bij het volgen van toepassingen waarbij u een GPS-feed van het apparaat of de Asset ontvangt en u wilt weten op welk adres de coördinaat zich bevindt.
Als u een set coördinaten locaties hebt voor reverse Geocode, kunt u [post Search Address reverse batch-API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) gebruiken om een batch met query's in één API-aanroep te verzenden.


1. Klik in postman op **nieuwe aanvraag** | **Get-aanvraag** en noem de **Zoek opdracht voor omgekeerde adressen**.

2. Selecteer op het tabblad opbouw functie de methode http **ophalen** en voer de aanvraag-URL in voor uw API-eind punt.
  
    ![URL voor Reverse Address Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorisatie | Geen verificatie |
  
3. Klik op **params**en voer de volgende sleutel/waarde-paren in om te gebruiken als query-of Path-para meters in de aanvraag-URL:
  
    ![Zoek parameters voor adres terugdraaien](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | abonnement-sleutel | \<uw Azure Maps-sleutel\> |
    | query | 47.591180,-122,332700 |
  
4. Klik op **verzenden** en controleer de tekst van het antwoord.

    Het antwoord bevat sleutel adres informatie over het veld Safeco.
  
5. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | getal | true |

    Als de para meter voor de query [nummer](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) met de aanvraag wordt verzonden, kan het antwoord de zijde van de straat (links of rechts) bevatten en ook een verschuivings positie voor dat nummer.
  
6. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Wanneer de [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -query parameter is ingesteld, retourneert het antwoord de limiet voor de geboekte snelheid.

7. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | returnRoadUse | true |

    Wanneer de [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -query parameter is ingesteld, retourneert het antwoord de gebruiks matrix voor de weg voor reverse geocodes op straat niveau.

8. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | roadUse | true |

    U kunt de omgekeerde Geocode query beperken tot een specifiek type weg met behulp van de [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -query parameter.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Zoeken naar meerdere Streets met omgekeerde adres zoeken in meerdere straat

1. Klik in postman op **nieuwe aanvraag** | **Get-aanvraag** en geef deze de naam **omgekeerde adres zoeken in meerdere Streets**.

2. Selecteer op het tabblad opbouw functie de methode http **ophalen** en voer de aanvraag-URL in voor uw API-eind punt.
  
    ![Adres zoeken in meerdere adressen omkeren](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorisatie | Geen verificatie |
  
3. Klik op **params**en voer de volgende sleutel/waarde-paren in om te gebruiken als query-of Path-para meters in de aanvraag-URL:
  
    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | abonnement-sleutel | \<uw Azure Maps-sleutel\> |
    | query | 47.591180,-122,332700 |
  
4. Klik op **verzenden** en controleer de tekst van het antwoord.

## <a name="next-steps"></a>Volgende stappen

- Verken de documentatie van de [Azure Maps Search service](https://docs.microsoft.com/rest/api/maps/search) -API.
- Meer informatie over [Best practices](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search).