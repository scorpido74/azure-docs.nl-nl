---
title: Bing Local Business Search API v7-verwijzing
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u technische details over de antwoordobjecten en de queryparameters en -koppen die van invloed zijn op de zoekresultaten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74075701"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing Local Business Search API v7-referentie

De Local Business Search API stuurt een zoekopdracht naar Bing om resultaten te krijgen die restaurants, hotels of andere lokale bedrijven bevatten. Voor plaatsen kan de query de naam van het lokale bedrijf of een categorie opgeven (bijvoorbeeld restaurants bij mij in de buurt). Entiteitsresultaten zijn personen, plaatsen of dingen. Plaats in deze context is zakelijke entiteiten, staten, landen / regio's, enz.  

In deze sectie vindt u technische details over de antwoordobjecten en de queryparameters en -koppen die van invloed zijn op de zoekresultaten. Zie Quickstart voor lokaal zakelijk [zoeken c# snelstart](quickstarts/local-quickstart.md) of Java snel zoeken [in Lokaal Zakelijk zoeken.](quickstarts/local-search-java-quickstart.md) 
  
Zie [Kopteksten](#headers)voor informatie over kopteksten die aanvragen moeten bevatten.  
  
Zie [Queryparameters](#query-parameters)voor informatie over queryparameters die aanvragen moeten bevatten.  
  
Zie [Antwoordobjecten](#response-objects)voor informatie over de JSON-objecten die het antwoord bevat.

Zie [Gebruiks- en weergavevereisten](use-display-requirements.md)voor informatie over toegestaan gebruik en weergave van resultaten.


  
## <a name="endpoint"></a>Eindpunt  
Als u lokale bedrijfsresultaten wilt opvragen, stuurt u een GET-verzoek naar: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
In de aanvraag moet u het HTTPS-protocol gebruiken.  
  
> [!NOTE]
> De maximale URL-lengte is 2.048 tekens. Om ervoor te zorgen dat uw URL-lengte de limiet niet overschrijdt, moet de maximale lengte van uw queryparameters minder dan 1.500 tekens zijn. Als de URL meer dan 2.048 tekens bedraagt, retourneert de server 404 Niet gevonden.  
  
  
## <a name="headers"></a>Headers  
Hieronder volgen de kopteksten die een aanvraag en antwoord kunnen bevatten.  
  
|Header|Beschrijving|  
|------------|-----------------|  
|Accepteren|Optionele aanvraagheader.<br /><br /> Het standaardmediatype is application/json. Als u wilt opgeven dat [json-LD](https://json-ld.org/)van het antwoord wordt gebruikt, stelt u de koptekst Accepteren in op toepassing/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Optionele aanvraagheader.<br /><br /> Een door komma's gescheiden lijst met talen die moet worden gebruikt voor gebruikersinterfacetekenreeksen. De lijst is in aflopende volgorde van voorkeur. Zie [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) voor meer informatie, waaronder de verwachte indeling.<br /><br /> Deze header en de queryparameter [setLang](#setlang) sluiten elkaar uit&mdash;geef ze niet beide op.<br /><br /> Als u deze header instelt, moet u ook de queryparameter cc opgeven. Om de markt te bepalen waarvoor resultaten moeten worden geretourneerd, gebruikt Bing de eerste ondersteunde taal die wordt gevonden in de lijst en combineert deze met de parameterwaarde `cc`. Als de lijst geen ondersteunde taal bevat, vindt Bing de dichtstbijzijnde taal en markt die de aanvraag ondersteunen, of gebruikt een geaggregeerde of standaardmarkt voor de resultaten. Zie de header BingAPIs-Market om de markt te bepalen die Bing heeft gebruikt.<br /><br /> Gebruik deze header en de queryparameter `cc` alleen als u meerdere talen opgeeft. Gebruik anders de queryparameters [mkt](#mkt) en [setLang](#setlang).<br /><br /> Een gebruikersinterfacetekenreeks is een tekenreeks die wordt gebruikt als label in een gebruikersinterface. Er zijn maar weinig gebruikersinterfacetekenreeksen in de JSON-antwoordobjecten. De opgegeven taal wordt toegepast op koppelingen naar Bing.com-eigenschappen in de antwoordobjecten.|  
|<a name="market" />BingAPIs-Market|Antwoordheader.<br /><br /> De markt die wordt gebruikt door de aanvraag. De notatie is \<languageCode\>-\<countryCode\>. Bijvoorbeeld: nl-NL.|  
|<a name="traceid" />BingAPIs-TraceId|Antwoordheader.<br /><br /> De id van de logboekvermelding die de details van de aanvraag bevat. Registreer deze id wanneer er een fout optreedt. Als u het probleem niet kunt vaststellen en oplossen, neemt u deze id op bij de andere informatie die u aan het ondersteuningsteam verstrekt.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Vereiste aanvraagheader.<br /><br /> De abonnementssleutel die u hebt ontvangen toen u zich hebt geregistreerd voor deze service in [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Optionele aanvraagheader<br /><br /> Bing retourneert standaard cache-inhoud, indien beschikbaar. Om te voorkomen dat Bing inhoud uit de cache retourneert, stelt u de Pragma-header in op no-cache (bijvoorbeeld: Pragma: no-cache).
|<a name="useragent" />User-Agent|Optionele aanvraagheader.<br /><br /> De gebruikersagent waarvan de aanvraag afkomstig is. Bing gebruikt de user-agent om mobiele gebruikers een geoptimaliseerde ervaring te bieden. Hoewel dit optioneel is, wordt u aangeraden deze header altijd op te geven.<br /><br /> De user-agent moet dezelfde tekenreeks zijn die elke veelgebruikte browser verzendt. Zie [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) voor meer informatie over gebruikersagenten.<br /><br /> Hier volgen enkele voorbeelden van user-agent-tekenreeksen.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; nl-nl; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; zoals Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) zoals Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 zoals Mac OS X) AppleWebKit/537.51.1 (KHTML, zoals Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Optionele aanvraag- en antwoord-header.<br /><br /> Bing gebruikt deze header om gebruikers consistent gedrag te bieden bij Bing API-aanroepen. Bing introduceert nieuwe functies en verbeteringen vaak in flights, en gebruikt de client-ID als sleutel voor het toewijzen van verkeer aan verschillende flights. Als u niet dezelfde client-id gebruikt voor een gebruiker voor meerdere aanvragen, kan Bing de gebruiker toewijzen aan meerdere conflicterende flights. Toewijzing aan meerdere conflicterende flights kan leiden tot een inconsistente gebruikerservaring. Als de tweede aanvraag bijvoorbeeld een andere flighttoewijzing heeft dan de eerste, kan de ervaring onverwacht zijn. Bing kan de client-id ook gebruiken om webresultaten aan te passen aan de zoekgeschiedenis van die client-id, waardoor de gebruiker een rijkere ervaring krijgt.<br /><br /> Bing gebruikt deze header ook om de rangschikking van resultaten te verbeteren door de activiteit te analyseren die wordt gegenereerd door een client-id. De relevantie-verbeteringen helpen de kwaliteit van de resultaten die door Bing-API's worden geleverd te verbeteren, en maken op hun beurt hogere doorklikpercentages mogelijk voor de API-consument.<br /><br /> **BELANGRIJK:** Hoewel deze header optioneel is, dient u deze als verplicht te beschouwen. Door de client-id te behouden voor meerdere aanvragen voor dezelfde combinatie van eindgebruiker en apparaat, zorgt u voor 1) een consistente gebruikerservaring van de API-consument, en 2) hogere doorklikpercentages via een betere kwaliteit van de resultaten van de Bing-API's.<br /><br /> Hieronder volgen de basisgebruiksregels die van toepassing zijn op deze header.<br /><ul><li>Elke gebruiker die uw toepassing op het apparaat gebruikt, moet een unieke door Bing gegenereerde client-id hebben.<br /><br/>Als u deze header niet in de aanvraag opneemt, genereert Bing een id en retourneert deze in de X-MSEdge-ClientID-antwoordheader. De enige keer dat u deze header NIET in een aanvraag moet opnemen, is de eerste keer dat de gebruiker uw app op dat apparaat gebruikt.<br /><br/></li><li>Gebruik de client-id voor elke Bing-API-aanvraag die uw app voor deze gebruiker op het apparaat doet.<br /><br/></li><li>**LET OP:** U moet ervoor zorgen dat deze client-id niet kan worden gekoppeld aan een authentieke gebruikersaccountgegevens.</li><br/><li>Maak de client-id persistent. Gebruik in een browser-app een persistent HTTP-cookie om ervoor te zorgen dat de id in alle sessies wordt gebruikt. Gebruik geen sessiecookie. Voor andere apps, zoals mobiele apps, gebruikt u de persistente opslag van het apparaat om de id persistent te maken.<br /><br/>De volgende keer dat de gebruiker uw app op dat apparaat gebruikt, haalt u de client-id op die u persistent hebt gemaakt.</li></ul><br /> **OPMERKING:** Bing-antwoorden kunnen deze header al dan niet bevatten. Als het antwoord deze header bevat, registreert u de client-id en gebruikt u deze voor alle volgende Bing-aanvragen voor de gebruiker op dat apparaat.<br /><br /> **OPMERKING:** als u de X-MSEdge-ClientID opneemt, moet u geen cookies opnemen in de aanvraag.|  
|<a name="clientip" />X-MSEdge-ClientIP|Optionele aanvraagheader.<br /><br /> Het IPv4- of IPv6-adres van het clientapparaat. Het IP-adres wordt gebruikt voor het detecteren van de locatie van de gebruiker. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen.<br /><br /> **OPMERKING:** Hoewel dit optioneel is, wordt u aangeraden deze header en de X-Search-Location-header altijd op te geven.<br /><br /> Verdoezel het adres niet (bijvoorbeeld door het laatste octet te wijzigen in 0). Wanneer u het adres verdoezelt, is de locatie totaal niet in de buurt van de werkelijke locatie van het apparaat, wat ertoe kan leiden dat Bing onjuiste resultaten geeft.|  
|<a name="location" />X-Search-Location|Optionele aanvraagheader.<br /><br /> Een met puntkomma's gescheiden lijst met sleutel-waardeparen die de geografische locatie van de client beschrijven. Bing gebruikt de locatie-informatie om het gedrag van Veilig Zoeken te bepalen en relevante lokale inhoud te retourneren. Geef het sleutel-waardepaar op als \<sleutel\>:\<waarde\>. Hier volgen de sleutels die u gebruikt om de locatie van de gebruiker op te geven.<br /><br /><ul><li>lat&mdash;De breedtegraad van de locatie van de klant, in graden. De breedtegraad moet groter dan of gelijk zijn aan -90.0 en kleiner dan of gelijk aan +90.0. Negatieve waarden geven zuidelijke breedtegraden aan, en positieve waarden noordelijke.<br /><br /></li><li>lang&mdash;De longitude van de locatie van de klant, in graden. De lengtegraad moet groter dan of gelijk zijn aan -180.0 en kleiner dan of gelijk aan +180.0. Negatieve waarden geven westelijke lengtegraden aan, en positieve waarden oostelijke.<br /><br /></li><li>re&mdash; De straal, in meters, die de horizontale nauwkeurigheid van de coördinaten specificeert. Geef de waarde door die wordt geretourneerd door de locatieservice van het apparaat. Typische waarden zijn bijvoorbeeld 22 m voor GPS/Wi-Fi, 380 m voor triangulatie op basis van gsm-masten en 18.000 m voor reverse IP-lookup.<br /><br /></li><li>ts&mdash; De UTC UNIX-tijdstempel van wanneer de client zich op de locatie bevond. (Het UNIX-tijdstempel is het aantal seconden sinds 1 januari 1970.)<br /><br /></li><li>head&mdash;Optioneel. De relatieve koers of reisrichting van de client. Geef de reisrichting op als graden van 0 t/m 360, gerekend met de klok mee ten opzichte van het ware noorden. Geef deze sleutel alleen op als de `sp`-sleutel niet nul is.<br /><br /></li><li>sp&mdash; De horizontale snelheid (snelheid), in meters per seconde, dat de client apparaat reist.<br /><br /></li><li>alt&mdash; De hoogte van het clientapparaat, in meters.<br /><br /></li><li>are&mdash;Optioneel. De straal, in meters, die de verticale nauwkeurigheid van de coördinaten aangeeft. Radius standaard tot 50 kilometer. Geef deze sleutel alleen op als u de `alt`-sleutel opgeeft.<br /><br /></li></ul> **LET OP:** Hoewel deze toetsen optioneel zijn, hoe meer informatie u verstrekt, hoe nauwkeuriger de locatieresultaten zijn.<br /><br /> **LET OP:** U wordt aangemoedigd om altijd de geografische locatie van de gebruiker op te geven. Het opgeven van de locatie is vooral belangrijk als het IP-adres van de client de fysieke locatie van de gebruiker niet nauwkeurig weergeeft (bijvoorbeeld als de client VPN gebruikt). Voor optimale resultaten moet u zowel deze header als de X-MSEdge-ClientIP-header opnemen, maar neem minimaal deze header op.|

> [!NOTE] 
> Vergeet niet dat de gebruiksvoorwaarden naleving van alle toepasselijke wetgeving vereisen, inclusief die over het gebruik van deze headers. In bepaalde rechtsgebieden, zoals Europa, is het bijvoorbeeld vereist om toestemming van de gebruiker te verkrijgen voordat bepaalde traceringsapparaten op gebruikersapparaten wordt geplaatst.
  

## <a name="query-parameters"></a>Queryparameters  
De aanvraag kan de volgende queryparameters bevatten. Zie de kolom Vereist voor vereiste parameters. U moet de queryparameters coderen.  
  
  
|Name|Waarde|Type|Vereist|  
|----------|-----------|----------|--------------|
|<a name="count" />Tellen|Het aantal resultaten dat moet worden retournerd, te beginnen met de index die is opgegeven door de `offset` parameter.|Tekenreeks|Nee|   
|<a name="localCategories" />lokaalCategorieën|Lijst met opties die zoeken definiëren op bedrijfscategorie.  Zoek [naar lokale bedrijfsrubrieken](local-categories.md)|Tekenreeks|Nee|  
|<a name="mkt" />mkt|De markt waaruit de resultaten afkomstig zijn. <br /><br />Zie Marktcodes voor een lijst met mogelijke marktwaarden.<br /><br /> **LET OP:** De Local Business Search API ondersteunt momenteel alleen de en-us markt en taal.<br /><br />|Tekenreeks|Ja|
|<a name="offset"/>Offset|De index om resultaten `count` te starten die door de parameter zijn opgegeven.|Geheel getal|Nee|  
|<a name="query" />V|De zoekterm van de gebruiker.|Tekenreeks|Nee|  
|<a name="responseformat" />responseFormat responseFormat responseFormat responseFormat|Het mediatype dat u wilt gebruiken voor het antwoord. Hieronder volgen de mogelijke hoofdletters.<br /><ul><li>JSON</li><li>JSONLD JSONLD</li></ul><br /> De standaardinstelling is JSON. Zie [Antwoordobjecten](#response-objects)voor informatie over de JSON-objecten die het antwoord bevat.<br /><br />  Als u JsonLd opgeeft, bevat de antwoordtekst JSON-LD-objecten die de zoekresultaten bevatten. Zie [JSON-LD](https://json-ld.org/)voor informatie over de JSON-LD.|Tekenreeks|Nee|  
|<a name="safesearch" />safeSearch|Een filter dat wordt gebruikt voor het filteren van inhoud voor volwassenen. Hier volgen de mogelijke niet-hoofdlettergevoelige filterwaarden.<br /><ul><li>Off&mdash;Return-webpagina's met tekst, afbeeldingen of video's voor volwassenen.<br /><br/></li><li>Webpagina's met matige&mdash;terugkeer met tekst voor volwassenen, maar geen afbeeldingen of video's voor volwassenen.<br /><br/></li><li>Streng&mdash;Geef webpagina's met tekst, afbeeldingen of video's voor volwassenen niet terug.</li></ul><br /> De standaardwaarde is Moderate.<br /><br /> **LET OP:** Als het verzoek afkomstig is van een markt `safeSearch` die het beleid voor `safeSearch` volwassenen van Bing vereist dat is ingesteld op Strict, negeert Bing de waarde en gebruikt Strict.<br/><br/>**OPMERKING:** Als u de zoekoperator `site:` gebruikt, is het mogelijk dat het antwoord inhoud voor volwassenen bevat, ongeacht de instelling van de queryparameter `safeSearch`. Gebruik `site:` alleen als u zich bewust bent van de inhoud op de site en uw scenario de mogelijkheid van inhoud voor volwassenen ondersteunt. |Tekenreeks|Nee|  
|<a name="setlang" />setLang|De taal die moet worden gebruikt voor gebruikersinterfacetekenreeksen. Geef de taal op met behulp van de tweeletterige ISO 639-1 taalcode. De taalcode voor Nederlands is bijvoorbeeld NL. De standaardwaarde is EN (Engels).<br /><br /> Hoewel dit optioneel is, moet u altijd de taal opgeven. Doorgaans stelt u `setLang` in op dezelfde taal die is opgegeven door `mkt`, tenzij de gebruiker wil dat gebruikersinterfacetekenreeksen in een andere taal worden weergegeven.<br /><br /> Deze parameter en de header [Accept-Language](#acceptlanguage) sluiten elkaar uit&mdash;geef ze niet beide op.<br /><br /> Een gebruikersinterfacetekenreeks is een tekenreeks die wordt gebruikt als label in een gebruikersinterface. Er zijn maar weinig gebruikersinterfacetekenreeksen in de JSON-antwoordobjecten. De opgegeven taal wordt ook toegepast op koppelingen naar Bing.com-eigenschappen in de antwoordobjecten.|Tekenreeks|Nee| 


## <a name="response-objects"></a>Reactieobjecten  
Hieronder volgen de JSON-antwoordobjecten die het antwoord kan bevatten. Als de aanvraag slaagt, is het object op het hoogste niveau in het antwoord het object [SearchResponse.](#searchresponse) Als de aanvraag mislukt, is het object op het hoogste niveau het object [ErrorResponse.](#errorresponse)


|Object|Beschrijving|  
|------------|-----------------|  
|[Locatie](#place)|Definieert informatie over een lokaal bedrijf, zoals een restaurant of hotel.|  

  
### <a name="error"></a>Fout  
Hiermee definieert u de fout die is opgetreden.  
  
|Element|Beschrijving|Type|  
|-------------|-----------------|----------|  
|<a name="error-code" />Code|De foutcode die de foutcategorie identificeert. Zie [Foutcodes](#error-codes)voor een lijst met mogelijke codes .|Tekenreeks|  
|<a name="error-message" />Bericht|Een beschrijving van de fout.|Tekenreeks|  
|<a name="error-moredetails" />meerDetails|Een beschrijving met aanvullende informatie over de fout.|Tekenreeks|  
|<a name="error-parameter" />Parameter|De queryparameter in de aanvraag die de fout heeft veroorzaakt.|Tekenreeks|  
|<a name="error-subcode" />subCode|De foutcode die de fout identificeert. Als dit `code` bijvoorbeeld Ongeldige `subCode` Aanvraag is, kan het parameterongeldig of parameterongeldige waarde zijn. |Tekenreeks|  
|<a name="error-value" />value|De waarde van de queryparameter die niet geldig was.|Tekenreeks|  
  

### <a name="errorresponse"></a>ErrorResponse (ErrorResponse)  
Het object op het hoogste niveau dat het antwoord bevat wanneer de aanvraag mislukt.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Type hint.|Tekenreeks|  
|<a name="errors" />Fouten|Een lijst met fouten die de redenen beschrijven waarom de aanvraag is mislukt.|[Fout](#error)[]|  

  
  
### <a name="license"></a>Licentie  
Hiermee definieert u de licentie waaronder de tekst of foto mag worden gebruikt.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|name|De naam van de licentie.|Tekenreeks|  
|url|De URL naar een website waar de gebruiker meer informatie over de licentie kan krijgen.<br /><br /> Gebruik de naam en URL om een hyperlink te maken.|Tekenreeks|  


### <a name="link"></a>Koppeling  
Hiermee definieert u de onderdelen van een hyperlink.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Type hint.|Tekenreeks|  
|tekst|De weergavetekst.|Tekenreeks|  
|url|Een URL. Gebruik de URL en geef tekst weer om een hyperlink te maken.|Tekenreeks|  
  


  
### <a name="organization"></a>Organisatie  
Definieert een uitgever.  
  
Houd er rekening mee dat een uitgever zijn of haar naam of website of beide kan opgeven.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|name|De naam van de uitgever.|Tekenreeks|  
|url|De URL naar de website van de uitgever.<br /><br /> Houd er rekening mee dat de uitgever geen website mag verstrekken.|Tekenreeks|  
  
  

### <a name="place"></a>Locatie  
Hiermee definieert u informatie over een lokaal bedrijf, zoals een restaurant of hotel.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Typ hint, die kan worden ingesteld op een van de volgende:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurant</ul><li>|Tekenreeks|  
|adres|Het postadres van waar de entiteit zich bevindt.|Postadres|  
|entiteitPresentatieInfo|Aanvullende informatie over de entiteit, zoals hints die u gebruiken om het type van de entiteit te bepalen. Bijvoorbeeld, of het nu een restaurant of hotel is. Het `entityScenario` veld is ingesteld op ListItem.|EntiteitPresentatieInfo|  
|name|De naam van de entiteit.|Tekenreeks|  
|telefoon|Het telefoonnummer van de entiteit.|Tekenreeks|  
|url|De URL naar de website van de entiteit.<br /><br /> Gebruik deze URL samen met de naam van de entiteit om een hyperlink te maken die de gebruiker bij het klikken naar de website van de entiteit brengt.|Tekenreeks|  
|webSearchUrl|De URL naar het zoekresultaat van Bing voor deze plek.|Tekenreeks| 
  
  
### <a name="querycontext"></a>QueryContext  
Hiermee definieert u de querycontext die Bing voor de aanvraag heeft gebruikt.  
  
|Element|Beschrijving|Type|  
|-------------|-----------------|----------|  
|volwassenIntentie|Een Booleaanse waarde die aangeeft of de opgegeven query de intentie van een volwassene heeft. De waarde is **waar** als de query een intentie voor volwassenen heeft. anders, **vals**.|Booleaans|  
|wijzigingOverrideQuery|De querytekenreeks die moet worden gebruikt om Bing te dwingen de oorspronkelijke tekenreeks te gebruiken. Als de querytekenreeks bijvoorbeeld *saling downwind*is, is de querytekenreeks overschrijven *+saling downwind*. Vergeet niet om de querytekenreeks te coderen, wat resulteert in *%2Bsaling+downwind*.<br /><br /> Dit veld wordt alleen opgenomen als de oorspronkelijke querytekenreeks een spelfout bevat.|Tekenreeks|  
|gewijzigdQuery|De querytekenreeks die door Bing wordt gebruikt om de query uit te voeren. Bing gebruikt de gewijzigde querytekenreeks als de oorspronkelijke querytekenreeks spelfouten bevat. Als de querytekenreeks bijvoorbeeld `saling downwind`is, is `sailing downwind`de gewijzigde querytekenreeks .<br /><br /> Dit veld wordt alleen opgenomen als de oorspronkelijke querytekenreeks een spelfout bevat.|Tekenreeks|  
|askUserForLocatie|Een Booleaanse waarde die aangeeft of Bing de locatie van de gebruiker vereist om nauwkeurige resultaten te leveren. Als u de locatie van de gebruiker hebt opgegeven met de headers [X-MSEdge-ClientIP](#clientip) en [X-Search-Location,](#location) u dit veld negeren.<br /><br /> Voor locatiebewuste query's, zoals 'het weer van vandaag' of 'restaurants bij mij in de buurt' die de locatie van de gebruiker nodig hebben om nauwkeurige resultaten te leveren, is dit veld ingesteld op **true.**<br /><br /> Voor locatiebewuste query's die de locatie bevatten (bijvoorbeeld 'Seattle-weer'), is dit veld ingesteld op **false**. Dit veld is ook ingesteld op **false** voor query's die niet locatiebewust zijn, zoals 'bestsellers'.|Booleaans|  
|originalQuery|De querytekenreeks zoals opgegeven in de aanvraag.|Tekenreeks|  

### <a name="identifiable"></a>Identificeerbare

|Name|Waarde|Type|  
|-------------|-----------------|----------|
|id|Een resource-id|Tekenreeks|
 
### <a name="rankinggroup"></a>RankingGroep
Hiermee definieert u een groep zoekresultaten, zoals hoofdlijn.

|Name|Waarde|Type|  
|-------------|-----------------|----------|
|Items|Een lijst met zoekresultaten die in de groep moeten worden weergegeven.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Hiermee definieert u een object met zoekresultaten dat moet worden weergegeven.

|Name|Waarde|Type|  
|-------------|-----------------|----------|
|resultaatIndex|Een op nul gebaseerde index van het item in het antwoord dat moet worden weergegeven. Als het item dit veld niet bevat, geeft u alle items in het antwoord weer. Geef bijvoorbeeld alle nieuwsartikelen weer in het antwoord nieuws.|Geheel getal|
|answerType|Het antwoord dat het item bevat dat moet worden weergegeven. Bijvoorbeeld Nieuws.<br /><br />Gebruik het type om het antwoord te vinden in het object SearchResponse. Het type is de naam van een veld SearchResponse.<br /><br /> Gebruik het antwoordtype echter alleen als dit object het waardeveld bevat. anders, negeer het.|Tekenreeks|
|textualIndex|De index van het antwoord in tekstueleAntwoorden om te tonen.| Niet-ondertekend geheel|
|waarde|De id die een antwoord identificeert om weer te geven of een item van een antwoord dat moet worden weergegeven. Als de id een antwoord identificeert, geeft u alle items van het antwoord weer.|Identificeerbare|

### <a name="rankingresponse"></a>RankingResponse  
Hiermee bepaalt u waar op de pagina met zoekresultaten inhoud moet worden geplaatst en in welke volgorde.  
  
|Name|Waarde|  
|----------|-----------|  
|<a name="ranking-mainline" />Mainline|De zoekresultaten worden weergegeven in de hoofdlijn.|  
|<a name="ranking-pole" />Pole|De zoekresultaten die de meest zichtbare behandeling moeten krijgen (bijvoorbeeld boven de hoofdlijn en zijbalk).|  
|<a name="ranking-sidebar" />Zijbalk|De zoekresultaten worden weergegeven in de zijbalk.| 

### <a name="searchresponse"></a>SearchResponse  
Hiermee definieert u het object op het hoogste niveau dat het antwoord bevat wanneer de aanvraag wordt voltooid.  
  
Houd er rekening mee dat als de service een denial of service-aanval vermoedt, het verzoek slaagt (HTTP-statuscode is 200 OK); echter, het lichaam van de reactie zal leeg zijn.  
  
|Name|Waarde|Type|  
|----------|-----------|----------|  
|_type|Typ hint, die is ingesteld op SearchResponse.|Tekenreeks|  
|Plaatsen|Een lijst met entiteiten die relevant zijn voor de zoekopdracht.|JSON-object|  
|queryContext|Een object dat de querytekenreeks bevat die Bing voor de aanvraag heeft gebruikt.<br /><br /> Dit object bevat de querytekenreeks die door de gebruiker is ingevoerd. Het kan ook een gewijzigde querytekenreeks bevatten die Bing voor de query heeft gebruikt als de querytekenreeks een spelfout bevat.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Foutcodes

Hieronder volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.  
  
|Statuscode|Beschrijving|  
|-----------------|-----------------|  
|200|Geslaagd.|  
|400|Een van de queryparameters ontbreekt of is ongeldig.|  
|401|De abonnementssleutel ontbreekt of is ongeldig.|  
|403|De gebruiker is geverifieerd (ze hebben bijvoorbeeld een geldige abonnementssleutel gebruikt), maar heeft geen toestemming voor de gevraagde bron.<br /><br /> Bing kan deze status ook retourneren als de beller zijn query's per maandquotum heeft overschreden.|  
|410|De aanvraag gebruikte HTTP in plaats van het HTTPS-protocol. HTTPS is het enige ondersteunde protocol.|  
|429|De beller heeft zijn query's per seconde quotum overschreden.|  
|500|Onverwachte serverfout.|

Als de aanvraag mislukt, bevat het antwoord een [object ErrorResponse,](#errorresponse) dat een lijst met [foutobjecten](#error) bevat waarin wordt beschreven wat de oorzaak van de fout is. Als de fout gerelateerd is `parameter` aan een parameter, identificeert het veld de parameter die het probleem is. En als de fout gerelateerd is `value` aan een parameterwaarde, identificeert het veld de waarde die niet geldig is.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Hieronder volgen de mogelijke foutcode- en subfoutcodewaarden.

|Code|Subcode|Beschrijving
|-|-|-
|ServerFout|Onverwachts<br/>ResourceFout<br/>Niet geïmplementeerd|HTTP-statuscode is 500.
|Ongeldig verzoek|ParameterMissing<br/>ParameterOngeldigWaarde<br/>HttpNotAtoegestaan<br/>Geblokkeerd|Bing retourneert Ongeldige aanvraag wanneer een deel van de aanvraag niet geldig is. Een vereiste parameter ontbreekt bijvoorbeeld of een parameterwaarde is ongeldig.<br/><br/>Als de fout ParameterMissing of ParameterInvalidValue is, is de HTTP-status400.<br/><br/>Als u het HTTP-protocol gebruikt in plaats van HTTPS, retourneert Bing HttpNotA-toegestaan en is de HTTP-statuscode 410.
|RateLimitoverschreden|Geen subcodes|Bing retourneert RateLimitExceeded wanneer u uw query's per seconde (QPS) of query's per maand (QPM) quotum overschrijdt.<br/><br/>Als u QPS overschrijdt, retourneert Bing HTTP-statuscode 429 en als u QPM overschrijdt, geeft Bing 403 als resultaat.
|Ongeldig autorisatie|Autorisatieontbreekt<br/>AutorisatieRedundantie|Bing retourneert InvalidAuthorization wanneer Bing de beller niet kan verifiëren. De `Ocp-Apim-Subscription-Key` koptekst ontbreekt bijvoorbeeld of de abonnementssleutel is ongeldig.<br/><br/>Redundantie treedt op als u meer dan één verificatiemethode opgeeft.<br/><br/>Als de fout Ongeldig autorisatie is, is de HTTP-status401.
|OnvoldoendeAutorisatie|AutorisatieUitgeschakeld<br/>Autorisatie verlopen|Bing retourneert OnvoldoendeAutorisatie wanneer de beller geen machtigingen heeft om toegang te krijgen tot de bron. Dit kan gebeuren als de abonnementssleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout Onvoldoende autorisatie is, is de HTTP-statuscode 403.

## <a name="next-steps"></a>Volgende stappen
- [Zoeken in lokaal bedrijf snel gestart](quickstarts/local-quickstart.md)
- [Lokaal zakelijk zoeken Java snelaan de dag](quickstarts/local-search-java-quickstart.md)
- [Lokaal zakelijk zoekknooppunt snel aan de slag](quickstarts/local-search-node-quickstart.md)
- [Lokaal zoeken in Business Python snel gestart](quickstarts/local-search-python-quickstart.md)
