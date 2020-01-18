---
title: Locaties zoeken met de Azure Maps Search Service | Microsoft Azure kaarten
description: In dit artikel wordt beschreven hoe u een locatie kunt zoeken met behulp van de Microsoft Azure Maps Search Service.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 53856b4157afa5976947c451952fc26eefcdd0ea
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264183"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Een adres zoeken met behulp van de Azure Maps Search-service

De Maps Search-service is een set REST-Api's die is ontworpen voor ontwikkel aars. De service kan zoeken naar adressen, plaatsen, interesses, bedrijfs vermeldingen en andere geografische informatie. Elk van de volgende waarden voor breedte graad en lengte graad: een specifiek adres, een andere straat, een geografische functie of een POI. U kunt de geretourneerde waarden voor breedte graad en lengte graad van een query gebruiken als para meters in andere toewijzings Services. De geretourneerde waarden kunnen bijvoorbeeld para meters voor route service of de Traffic Flow-service worden. 

Leer hoe u het volgende kunt doen:

* Zoeken naar een adres met behulp van [fuzzy Search-API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Een adres samen met de eigenschappen en coördinaten zoeken
* Maak een [Reverse Address-zoek opdracht](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) om te zoeken naar een adres
* Zoeken naar een cross-straat met behulp van [Address reverse Cross Street-API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Vereisten

Als u aanroepen naar de Maps service-Api's wilt maken, hebt u een Maps-account en een sleutel nodig. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps)om een account te maken voor Azure Maps. Volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account)als u hulp nodig hebt bij het verkrijgen van uw primaire sleutel. Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

In dit artikel wordt gebruikgemaakt van de [app postman](https://www.getpostman.com/apps) om rest-aanroepen te bouwen. U kunt elke gewenste API-ontwikkel omgeving gebruiken.

## <a name="using-fuzzy-search"></a>Met fuzzy Search

De standaard-API voor de zoek service is [wazig zoeken](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Deze service is handig wanneer u niet zeker weet wat de invoer van de gebruiker is in een zoek opdracht. De API combineert POI Search en Geocode ring in een canonieke zoek opdracht met één regel. De API kan bijvoorbeeld invoer van een wille keurige combi natie van adressen of POI verwerken. Het kan ook worden gewogen met een contextuele positie (lat./Lon. paar), volledig beperkt door een coördinaat en RADIUS, of meer in het algemeen zonder enige geo-veranderings anker punt.

De meeste zoek query's zijn standaard `maxFuzzyLevel=1` om prestaties te verkrijgen en de ongebruikelijke resultaten te verminderen. Deze standaard instelling kan worden overschreven als nodig per aanvraag door door gegeven in de query parameter `maxFuzzyLevel=2` of `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Zoeken naar een adres met behulp van fuzzy Search

1. Open de Postman-app, klik op nieuw | Maak een nieuwe en selecteer **aanvraag ophalen**. Voer de naam van de aanvraag van **fuzzy zoeken**in, selecteer een verzameling of map om deze op te slaan en klik op **Opslaan**.

2. Selecteer op het tabblad opbouw functie de methode http **ophalen** en voer de aanvraag-URL in voor uw API-eind punt.

    ![Zoek actie op fuzzy](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Autorisatie | Geen autorisatie |

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

## <a name="search-for-address-properties-and-coordinates"></a>Adres eigenschappen en-coördinaten zoeken

U kunt een volledig of gedeeltelijk adres door geven aan de API voor Zoek adressen. U ontvangt nog steeds een antwoord met gedetailleerde adres eigenschappen. Gedetailleerde adres eigenschappen zijn waarden als positionele waarden in de hoogte en lengte graad, gemeente of onderverdeling.

1. Klik in postman op **nieuwe aanvraag** | **aanvraag ophalen** en geef de naam op voor de **Zoek opdracht**.
2. Op het tabblad opbouw functie selecteert u de methode http **ophalen** , voert u de aanvraag-URL in voor uw API-eind punt en selecteert u een autorisatie protocol, indien van toepassing.

    ![Adres zoeken](./media/how-to-search-for-address/address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Autorisatie | Geen autorisatie |

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
    | typeahead | waar |

    De vlag **typeahead** geeft aan dat de zoek-API van het adres de query als gedeeltelijke invoer behandelt en een matrix van voorspellende waarden retourneert.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Zoeken naar een adres met behulp van Reverse Address Search

1. Klik in postman op **nieuwe aanvraag** | **Get-aanvraag** en noem de **Zoek opdracht voor omgekeerde adressen**.

2. Selecteer op het tabblad opbouw functie de methode http **ophalen** en voer de aanvraag-URL in voor uw API-eind punt.
  
    ![URL voor Reverse Address Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Autorisatie | Geen autorisatie |
  
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
    | getal | waar |

    Als de para meter voor de query [nummer](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) met de aanvraag wordt verzonden, kan het antwoord de zijde van de straat (links of rechts) bevatten en ook een verschuivings positie voor dat nummer.
  
6. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | returnSpeedLimit | waar |
  
    Wanneer de [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -query parameter is ingesteld, retourneert het antwoord de limiet voor de geboekte snelheid.

7. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | returnRoadUse | waar |

    Wanneer de [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -query parameter is ingesteld, retourneert het antwoord de gebruiks matrix voor de weg voor reverse geocodes op straat niveau.

8. Voeg de volgende sleutel/waarde-paar toe aan de sectie **params** en klik op **verzenden**:

    | Sleutel | Waarde |
    |-----|------------|
    | roadUse | waar |

    U kunt de omgekeerde Geocode query beperken tot een specifiek type weg de query parameter [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) gebruiken.
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Zoeken naar de cross-straat met omgekeerde adres zoeken in meerdere straat

1. Klik in postman op **nieuwe aanvraag** | **Get-aanvraag** en geef deze de naam **omgekeerde adres zoeken in meerdere Streets**.

2. Selecteer op het tabblad opbouw functie de methode http **ophalen** en voer de aanvraag-URL in voor uw API-eind punt.
  
    ![Adres zoeken in meerdere adressen omkeren](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parameter | Voorgestelde waarde |
    |---------------|------------------------------------------------|
    | HTTP-methode | GET |
    | Aanvraag-URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Autorisatie | Geen autorisatie |
  
3. Klik op **params**en voer de volgende sleutel/waarde-paren in om te gebruiken als query-of Path-para meters in de aanvraag-URL:
  
    | Sleutel | Waarde |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | abonnement-sleutel | \<uw Azure Maps-sleutel\> |
    | query | 47.591180,-122,332700 |
  
4. Klik op **verzenden** en controleer de tekst van het antwoord.

## <a name="next-steps"></a>Volgende stappen

- Verken de documentatie van de [Azure Maps Search service](https://docs.microsoft.com/rest/api/maps/search) -API.
