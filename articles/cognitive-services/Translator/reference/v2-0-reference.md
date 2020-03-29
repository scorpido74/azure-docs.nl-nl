---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Referentiedocumentatie voor de Translator Text API v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242487"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Deze versie van de Translator Text API is afgeschaft. [Bekijk documentatie voor versie 3 van de Translator Text API](v3-0-reference.md).

Versie 2 van de Translator Text API kan naadloos worden geïntegreerd in uw apps, websites, tools of andere oplossingen om gebruikerservaringen in meerdere talen te bieden. U het gebruiken op elk hardwareplatform en met elk besturingssysteem om taalvertaling en andere taalgerelateerde taken uit te voeren, zoals tekst-taaldetectie en tekst-naar-spraak, volgens de industriestandaarden. Zie [Translator Text API](../translator-info-overview.md)voor meer informatie.

## <a name="getting-started"></a>Aan de slag
Als u toegang wilt krijgen tot de Translator Text API, moet u [zich aanmelden voor Microsoft Azure.](../translator-text-how-to-signup.md)

## <a name="authentication"></a>Authentication 
Voor alle aanroepen naar de Translator Text API is een abonnementssleutel nodig voor verificatie. De API ondersteunt drie verificatiemethoden:

- Een toegangstoken. Gebruik de abonnementssleutel om een toegangstoken te maken door een POST-verzoek in te dienen bij de verificatieservice. Zie de documentatie over tokenservice voor meer informatie. Geef het toegangstoken door aan `Authorization` de Translator-service met behulp van de koptekst of de `access_token` queryparameter. Het toegangstoken is 10 minuten geldig. Verkrijg elke 10 minuten een nieuw toegangstoken en blijf dezelfde toegangstoken gebruiken voor herhaalde verzoeken gedurende de 10 minuten.
- Een abonnementssleutel die rechtstreeks wordt gebruikt. Geef uw abonnementssleutel door `Ocp-Apim-Subscription-Key` als waarde in de koptekst die bij uw aanvraag is opgenomen, aan de Translator Text API. Wanneer u de abonnementssleutel rechtstreeks gebruikt, hoeft u de tokenverificatieservice niet te bellen om een toegangstoken te maken.
- Een [Azure Cognitive Services-abonnement voor meerdere services](https://azure.microsoft.com/pricing/details/cognitive-services/). Met deze methode u één geheime sleutel gebruiken om aanvragen voor meerdere services te verifiëren.
Wanneer u een geheime sleutel voor meerdere diensten gebruikt, moet u twee verificatiekoppen bij uw aanvraag opnemen. De eerste header passeert de geheime sleutel. De tweede koptekst geeft het gebied aan dat aan uw abonnement is gekoppeld:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

De regio is vereist voor het multi-service Text API-abonnement. De regio die u selecteert, is het enige gebied dat u gebruiken voor tekstvertaling wanneer u de abonnementssleutel voor meerdere diensten gebruikt. Het moet dezelfde regio zijn die u hebt geselecteerd toen u zich opdeed voor uw multiserviceabonnement op de Azure-portal.

De beschikbare `australiaeast`regio's zijn `eastasia` `eastus`, `eastus2` `brazilsouth` `canadacentral`, `northeurope` `southcentralus`, `southeastasia` `centralindia` `centraluseuap`, `westcentralus` `westeurope`, `westus`, `westus2` `japaneast`, , , , `uksouth`, , , , , en .

Uw abonnementssleutel en toegangstoken zijn geheimen die aan het zicht moeten worden onttrokken.

## <a name="profanity-handling"></a>Godslastering
Normaal gesproken behoudt de Translator-service godslastering die in de bron aanwezig is. De mate van godslastering en de context die woorden profane maakt verschillen per cultuur. Zo zou de graad van godslastering in de doeltaal kunnen worden verhoogd of verminderd.

Als u godslastering in de vertaling wilt voorkomen, zelfs als deze in de brontekst staat, u de filteroptie voor godslastering gebruiken voor de methoden die deze ondersteunen. Met de optie u kiezen of u godslastering wilt zien verwijderd of gemarkeerd met de juiste tags, of dat u de godslastering in het doel wilt toestaan. De geaccepteerde `ProfanityAction` `NoAction` waarden van `Marked`zijn `Deleted`(standaard), en .


|GodslasteringActie    |Actie |Voorbeeldbron (Japans)  |Voorbeeldvertaling (Engels)  |
|:--|:--|:--|:--|
|NoAction (NoAction)   |Standaard. Hetzelfde als het niet instellen van de optie. Godslastering gaat van bron naar doelwit.        |????     |Hij is een klootzak.   |
|Gemarkeerd     |Profane woorden zullen worden \<omringd door XML-tags godslastering> en \</ godslastering>.       |???? |Hij is \<een godslastering>\<jackass / godslastering>.  |
|Verwijderen    |Profane woorden zullen worden verwijderd uit de output zonder vervanging.     |???? |Hij is een.   |

    
## <a name="excluding-content-from-translation"></a>Inhoud uitsluiten van vertaling
Wanneer u inhoud vertaalt met`contentType=text/html`tags, zoals HTML (), is het soms handig om specifieke inhoud uit te sluiten van de vertaling. U het `class=notranslate` kenmerk gebruiken om inhoud op te geven die in de oorspronkelijke taal moet blijven. In het volgende voorbeeld wordt `div` de inhoud in het eerste element niet `div` vertaald, maar wordt de inhoud in het tweede element vertaald.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Vertalen

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee vertaalt u een tekenreeks van de ene taal naar de andere.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/Translate`URI is .

**Retourwaarde:** Een tekenreeks die de vertaalde tekst vertegenwoordigt.

Als u `AddTranslation` eerder `AddTranslationArray` een vertaling met een beoordeling van 5 of `Translate` hoger voor dezelfde bronzin hebt gebruikt of wilt invoeren, wordt alleen de beste keuze geretourneerd die beschikbaar is voor uw systeem. "Dezelfde bronzin" betekent precies hetzelfde (100% overeenkomen), met uitzondering van hoofdletters, witruimte, tagwaarden en interpunctie aan het einde van een zin. Als er geen beoordeling is opgeslagen met een beoordeling van 5 of hoger, is het geretourneerde resultaat de automatische vertaling door Microsoft Translator.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)

tekenreeks

Inhoudstype antwoord: toepassing/xml

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving    |Parametertype|gegevenstype|
|:--|:--|:--|:--|:--|
|Appid  |(leeg)    |Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|tekst|(leeg)   |Vereist. Een tekenreeks die de tekst vertegenwoordigt die moet worden vertaald. De tekst mag niet meer dan 10.000 tekens bevatten.|query|tekenreeks|
|from|(leeg)   |Optioneel. Een tekenreeks die de taalcode van de tekst die wordt vertaald vertegenwoordigt. Bijvoorbeeld, en voor Engels.|query|tekenreeks|
|tot|(leeg) |Vereist. Een tekenreeks die de code van de taal vertegenwoordigt om de tekst naar te vertalen.|query|tekenreeks|
|Contenttype|(leeg)    |Optioneel. Het formaat van de tekst die wordt vertaald. Ondersteunde indelingen `text/plain` zijn (standaard) en `text/html`. Alle HTML-elementen moeten goed gevormde, volledige elementen zijn.|query|tekenreeks|
|category|(leeg)   |Optioneel. Een tekenreeks die de categorie (domein) van de vertaling bevat. De standaardwaarde is `general`.|query|tekenreeks|
|Autorisatie|(leeg)  |Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)  |Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|


### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee haalt u vertalingen op voor meerdere bronteksten.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`URI is .

Hier is het formaat van de aanvraaginstantie:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Deze elementen `TranslateArrayRequest`zijn in :


* `AppId`: Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `AppId` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .
* `From`: Optioneel. Een tekenreeks die de taalcode van de tekst die wordt vertaald vertegenwoordigt. Als dit veld leeg blijft, wordt het antwoord het resultaat van automatische taaldetectie opgenomen.
* `Options`: Optioneel. Een `Options` object dat de volgende waarden bevat. Ze zijn allemaal optioneel en standaard voor de meest voorkomende instellingen. Gespecificeerde elementen moeten in alfabetische volgorde worden vermeld.
    - `Category`: Een tekenreeks die de categorie (domein) van de vertaling bevat. De standaardwaarde is `general`.
    - `ContentType`: De vorm van de tekst die wordt vertaald. De ondersteunde indelingen `text/plain` zijn `text/xml`(standaard), en `text/html`. Alle HTML-elementen moeten goed gevormde, volledige elementen zijn.
    - `ProfanityAction`: Hiermee geeft u op hoe met godslasteringen wordt omgegaan, zoals eerder is uitgelegd. Geaccepteerde `NoAction` waarden zijn `Marked`(standaard), en `Deleted`.
    - `State`: Gebruikersstatus om de aanvraag en het antwoord te correleren. Dezelfde inhoud wordt geretourneerd in het antwoord.
    - `Uri`: Filter resultaten door deze URI. Standaard: `all`.
    - `User`: Filter resultaten door deze gebruiker. Standaard: `all`.
* `Texts`: Vereist. Een array die de tekst voor vertaling bevat. Alle snaren moeten in dezelfde taal zijn. Het totaal van alle te vertalen tekst mag niet hoger zijn dan 10.000 tekens. Het maximum aantal matrixelementen is 2.000.
* `To`: Vereist. Een tekenreeks die de code van de taal vertegenwoordigt om de tekst naar te vertalen.

U optionele elementen weglaten. Elementen waarvan directe `TranslateArrayRequest` kinderen zijn, moeten in alfabetische volgorde worden vermeld.

De `TranslateArray` methode `application/xml` accepteert `text/xml` `Content-Type`of voor .

**Retourwaarde:** Een `TranslateArrayResponse` array. Elk `TranslateArrayResponse` heeft de volgende elementen:

* `Error`: Geeft een fout aan als er een optreedt. Anders ingesteld op null.
* `OriginalSentenceLengths`: Een array van gehele getallen die de lengte van elke zin in de brontekst aangeeft. De lengte van de array geeft het aantal zinnen aan.
* `TranslatedText`: De vertaalde tekst.
* `TranslatedSentenceLengths`: Een array van gehele getallen die de lengte van elke zin in de vertaalde tekst aangeeft. De lengte van de array geeft het aantal zinnen aan.
* `State`: Gebruikersstatus om de aanvraag en het antwoord te correleren. Retourneert dezelfde inhoud als het verzoek.

Hier is het formaat van de reactie body:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
Een succesvol antwoord bevat `TranslateArrayResponse` een array met arrays in de eerder beschreven indeling.

tekenreeks

Inhoudstype antwoord: toepassing/xml

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Autorisatie|(leeg)  |Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode   |Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie. Veelvoorkomende fouten zijn: <ul><li>Arrayelement kan niet leeg zijn.</li><li>Ongeldige categorie.</li><li>Van taal is ongeldig.</li><li>Naar taal is ongeldig.</li><li>De aanvraag bevat te veel elementen.</li><li>De Taal van De taal wordt niet ondersteund.</li><li>De aan-taal wordt niet ondersteund.</li><li>Translate Request heeft te veel gegevens.</li><li>HTML is niet in een juiste indeling.</li><li>Er zijn te veel tekenreeksen geslaagd in de vertaalaanvraag.</li></ul>|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee haalt u vriendelijke namen op `languageCodes`voor de talen `locale` die als parameter zijn doorgegeven, gelokaliseerd in de doorgegeven taal.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`URI is .

De aanvraaginstantie bevat een tekenreeksarray die de ISO 639-1-taalcodes vertegenwoordigt waarvoor de vriendelijke namen kunnen worden opgehaald. Hier volgt een voorbeeld:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Retourwaarde:** Een tekenreeksarray met taalnamen die worden ondersteund door de vertalersservice, gelokaliseerd in de gevraagde taal.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
Een tekenreeksarray met talennamen die worden ondersteund door de vertalersservice, gelokaliseerd in de gevraagde taal.

tekenreeks

Inhoudstype antwoord: toepassing/xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|landinstellingen|(leeg) |Vereist. Een tekenreeks die een van de volgende vermeldingen vertegenwoordigt, wordt gebruikt om de taalnamen te lokaliseren: <ul><li>De combinatie van een ISO 639 tweeletterige kleine letter cultuurcode in combinatie met een taal en een ISO 3166 tweeletterige subcultuurcode met twee letters. <li>Een ISO 639 kleine letters cultuurcode op zichzelf.|query|tekenreeks|
|Autorisatie|(leeg)  |Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)  |Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hier krijgt u een lijst met taalcodes die talen vertegenwoordigen die worden ondersteund door de vertaalservice.  `Translate`en `TranslateArray` kan vertalen tussen twee van deze talen.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`URI is .

**Retourwaarde:** Een tekenreeksarray met de taalcodes die worden ondersteund door de service Translator.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
Een tekenreeksarray met de taalcodes die worden ondersteund door de service Translator.

tekenreeks

Inhoudstype antwoord: toepassing/xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|Autorisatie|(leeg)  |Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503|Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee worden de talen opgehaald die beschikbaar zijn voor spraaksynthese.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`URI is .

**Retourwaarde:** Een tekenreeksarray die de taalcodes bevat die worden ondersteund voor spraaksynthese door de dienst Translator.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
Een tekenreeksarray die de taalcodes bevat die worden ondersteund voor spraaksynthese door de dienst Translator.

tekenreeks

Inhoudstype antwoord: toepassing/xml

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|
 
### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400|Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401|Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Uitvoeringsnota's
Retourneert een WAV- of MP3-stream van de doorgegeven tekst, gesproken in de gewenste taal.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/Speak`URI is .

**Retourwaarde:** Een WAV- of MP3-stream van de doorgegeven tekst, gesproken in de gewenste taal.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)

binair

Inhoudstype antwoord: toepassing/xml

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|tekst|(leeg)   |Vereist. Een tekenreeks die een of meer zinnen bevat die voor de stream moeten worden gesproken, in de opgegeven taal. De tekst mag niet meer dan 2.000 tekens bevatten.|query|tekenreeks|
|language|(leeg)   |Vereist. Een tekenreeks die de ondersteunde taalcode vertegenwoordigt van de taal waarin de tekst moet worden gesproken. De code moet een van de `GetLanguagesForSpeak`codes zijn die volgens de methode worden geretourneerd.|query|tekenreeks|
|formaat|(leeg)|Optioneel. Een tekenreeks die de inhouds-type-id opgeeft. Momenteel `audio/wav` en `audio/mp3` beschikbaar zijn. De standaardwaarde is `audio/wav`.|query|tekenreeks|
|opties|(leeg)    |Optioneel. Een tekenreeks die eigenschappen van de gesynthetiseerde spraak opgeeft:<ul><li>`MaxQuality`en `MinSize` geef de kwaliteit van het audiosignaal aan. `MaxQuality`biedt de hoogste kwaliteit. `MinSize`biedt de kleinste bestandsgrootte. De standaardinstelling is `MinSize`.</li><li>`female`en `male` geef het gewenste geslacht van de stem aan. De standaardwaarde is `female`. Gebruik de verticale<code>\|</code>balk ( ) om meerdere opties op te nemen. Bijvoorbeeld `MaxQuality|Male`.</li></li></ul>  |query|tekenreeks|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)  |Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="get-detect"></a>GET /Detecteren

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee wordt de taal van een gedeelte tekst geïdentificeerd.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/Detect`URI is .

**Retourwaarde:** Een tekenreeks met een taalcode met twee tekens voor de tekst.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)

tekenreeks

Inhoudstype antwoord: toepassing/xml

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)  |Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|tekst|(leeg)|Vereist. Een tekenreeks die tekst bevat waarvan de taal moet worden geïdentificeerd. De tekst mag niet meer dan 10.000 tekens bevatten.|query|  tekenreeks|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key  |(leeg)    |Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400|Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Uitvoeringsnota's

Hiermee worden de talen in een reeks tekenreeksen geïdentificeerd. Detecteert onafhankelijk de taal van elk afzonderlijk arrayelement en retourneert een resultaat voor elke rij van de array.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`URI is .

Hier is het formaat van de aanvraaginstantie:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

De tekst mag niet meer dan 10.000 tekens bevatten.

**Retourwaarde:** Een tekenreeksarray die een taalcode met twee tekens bevat voor elke rij in de invoerarray.

Hier is het formaat van de reactie body:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
`DetectArray`was succesvol. Retourneert een tekenreeksarray die een taalcode met twee tekens bevat voor elke rij van de invoerarray.

tekenreeks

Inhoudstype antwoord: toepassing/xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven.  Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Uitvoeringsnota's

> [!IMPORTANT]
> **Afschrijvingsnota:** Na 31 januari 2018 accepteert deze methode geen nieuwe inzendingen voor zinnen. Je krijgt een foutmelding. Zie de aankondiging over wijzigingen in het Collaborative Translation Framework (CTF).

Hiermee voegt u een vertaling toe aan het vertaalgeheugen.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`URI is .

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)

tekenreeks

Inhoudstype antwoord: toepassing: xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype   |
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|originalText (origineletekst)|(leeg)|Vereist. Een tekenreeks die de tekst bevat om te vertalen. De maximale lengte van de tekenreeks is 1.000 tekens.|query|tekenreeks|
|translatedText|(leeg) |Vereist. Een tekenreeks die tekst bevat die in de doeltaal is vertaald. De maximale lengte van de tekenreeks is 2.000 tekens.|query|tekenreeks|
|from|(leeg)   |Vereist. Een tekenreeks die de taalcode van de oorspronkelijke taal van de tekst vertegenwoordigt. Bijvoorbeeld, en voor Engels en de voor duits.|query|tekenreeks|
|tot|(leeg)|Vereist. Een tekenreeks die de taalcode van de taal vertegenwoordigt om de tekst naar te vertalen.|query|tekenreeks|
|rating|(leeg) |Optioneel. Een geheel getal dat de kwaliteitsbeoordeling voor de tekenreeks vertegenwoordigt. De waarde ligt tussen -10 en 10. De standaardwaarde is 1.|query|geheel getal|
|Contenttype|(leeg)    |Optioneel. Het formaat van de tekst die wordt vertaald. De ondersteunde indelingen zijn `text/plain` en `text/html`. Alle HTML-elementen moeten goed gevormde, volledige elementen zijn.    |query|tekenreeks|
|category|(leeg)|Optioneel. Een tekenreeks die de categorie (domein) van de vertaling bevat. De standaardwaarde is `general`.|query|tekenreeks|
|gebruiker|(leeg)|Vereist. Een tekenreeks die wordt gebruikt om de afzender van de inzending te volgen.|query|tekenreeks|
|Uri|(leeg)|Optioneel. Een tekenreeks die de inhoudslocatie van de vertaling bevat.|query|tekenreeks|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven.  Autorisatietoken: `"Bearer" + " " + "access_token"`.  |koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|410|`AddTranslation`wordt niet langer ondersteund.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Uitvoeringsnota's

> [!IMPORTANT]
> **Afschrijvingsnota:** Na 31 januari 2018 accepteert deze methode geen nieuwe inzendingen voor zinnen. Je krijgt een foutmelding. Zie de aankondiging over wijzigingen in het Collaborative Translation Framework (CTF).

Hiermee voegt u een array met vertalingen toe aan het vertaalgeheugen. Deze methode is een `AddTranslation`matrixversie van .

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`URI is .

Hier is het formaat van de aanvraaginstantie:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Deze elementen `AddtranslationsRequest`zijn in :

* `AppId`: Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `AppId` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .
* `From`: Vereist. Een tekenreeks die de taalcode van de brontaal bevat. Moet een van de talen `GetLanguagesForTranslate` terug door de methode.
* `To`: Vereist. Een tekenreeks die de taalcode van de doeltaal bevat. Moet een van de talen `GetLanguagesForTranslate` terug door de methode.
* `Translations`: Vereist. Een reeks vertalingen die aan het vertaalgeheugen moeten worden toegevoegd. Elke vertaling `OriginalText`moet `TranslatedText`, `Rating`en . De maximale `OriginalText` grootte `TranslatedText` van elk en is 1.000 tekens. Het totaal `OriginalText` van `TranslatedText` alle elementen en elementen mag niet hoger zijn dan 10.000 tekens. Het maximum aantal matrixelementen is 100.
* `Options`: Vereist. Een reeks opties, `Category` `ContentType`waaronder `Uri`, `User`, en . `User` is vereist. `Category`, `ContentType`en `Uri` zijn optioneel. Gespecificeerde elementen moeten in alfabetische volgorde worden vermeld.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
`AddTranslationArray`methode geslaagd. 

Na 31 januari 2018 worden inzendingen voor vonnissen niet meer geaccepteerd. De service reageert met foutcode 410.

tekenreeks

Inhoudstype antwoord: toepassing/xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven.  Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|410    |`AddTranslation`wordt niet langer ondersteund.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503|Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="get-breaksentences"></a>GET / BreakSentences

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee breekt u een gedeelte tekst op in zinnen en retourneert u een array met de lengtes van elke zin.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`URI is .

**Retourwaarde:** Een array van gehele getallen die de lengtes van de zinnen vertegenwoordigt. De lengte van de array vertegenwoordigt het aantal zinnen. De waarden vertegenwoordigen de lengte van elke zin.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
Een array van gehele getallen die de lengtes van de zinnen vertegenwoordigt. De lengte van de array vertegenwoordigt het aantal zinnen. De waarden vertegenwoordigen de lengte van elke zin.

geheel getal

Inhoudstype antwoord: toepassing/xml

### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)  |Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query| tekenreeks|
|tekst|(leeg)   |Vereist. Een tekenreeks die de tekst vertegenwoordigt die in zinnen moet worden gesplitst. De maximale grootte van de tekst is 10.000 tekens.|query|tekenreeks|
|language   |(leeg)    |Vereist. Een tekenreeks die de taalcode van de invoertekst vertegenwoordigt.|query|tekenreeks|
|Autorisatie|(leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.   |koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)|Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400|Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401|Ongeldige referenties.|
|500|Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503|Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Uitvoeringsnota's
Haalt een reeks vertalingen voor een bepaald taalpaar op uit de winkel en de MT-engine. `GetTranslations`verschilt van `Translate` in die zin dat het alle beschikbare vertalingen retourneert.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`URI is .

De hoofdtekst van de `TranslationOptions` aanvraag bevat het optionele object, dat deze indeling heeft:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

Het `TranslateOptions` object bevat de waarden in de volgende lijst. Ze zijn allemaal optioneel en standaard voor de meest voorkomende instellingen. Gespecificeerde elementen moeten in alfabetische volgorde worden vermeld.

* `Category`: Een tekenreeks die de categorie (domein) van de vertaling bevat. De standaardwaarde is `general`.
* `ContentType`: De enige ondersteunde optie en `text/plain`de standaardoptie is .
* `IncludeMultipleMTAlternatives`: Een Booleaanse vlag om aan te geven of er meer dan één alternatief moet worden geretourneerd van de MT-engine. Geldige waarden `true` `false` zijn en (hoofdlettergevoelig). De standaardinstelling is `false`, die slechts één alternatief retourneert. Het instellen `true` van de vlag om het creëren van kunstmatige alternatieven mogelijk te maken, volledig geïntegreerd met het Collaborative Translation Framework (CTF). De functie maakt terugkerende alternatieven voor zinnen die geen vertalingen in CTF hebben door het toevoegen van kunstmatige alternatieven uit de *n-beste*lijst van de decoder.
    - Waarderingen. De beoordelingen worden als volgt toegepast: 
         - De beste automatische vertaling heeft een rating van 5.
       - De alternatieven van CTF weerspiegelen het gezag van de beoordelaar. Ze variëren van -10 tot +10.
       - De automatisch gegenereerde *(n*-beste) vertaalalternatieven hebben een score van 0 en een matchgraad van 100.
    - Aantal alternatieven. Het aantal geretourneerde alternatieven kan net zo `maxTranslations`hoog zijn als de waarde die is opgegeven in , maar het kan lager zijn.
    - Taalparen. Deze functionaliteit is niet beschikbaar voor vertalingen tussen vereenvoudigd Chinees en Traditioneel Chinees, in beide richtingen. Het is beschikbaar voor alle andere taalparen die worden ondersteund door Microsoft Translator.
* `State`: Gebruikersstatus om de aanvraag en het antwoord te correleren. Dezelfde inhoud wordt geretourneerd in het antwoord.
* `Uri`: Filter resultaten door deze URI. Als er geen waarde is `all`ingesteld, is de standaardinstelling .
* `User`: Filter resultaten door deze gebruiker. Als er geen waarde is `all`ingesteld, is de standaardinstelling .

Verzoek `Content-Type` moet `text/xml`worden .

**Retourwaarde:** Hier is het formaat van het antwoord:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Dit antwoord `GetTranslationsResponse` bevat een element dat de volgende waarden bevat:

* `Translations`: Een array van de `TranslationMatch` gevonden overeenkomsten, opgeslagen in objecten (beschreven in de volgende sectie). De vertalingen kunnen lichte varianten van de oorspronkelijke tekst bevatten (fuzzy matching). De vertalingen worden gesorteerd: 100% komt als eerste overeen, fuzzy matches volgende.
* `From`: Als de methode geen `From` taal opgeeft, komt deze waarde voort uit automatische taaldetectie. Anders is dit de `From` opgegeven taal.
* `State`: Gebruikersstatus om de aanvraag en het antwoord te correleren. Bevat de opgegeven waarde `TranslateOptions` in de parameter.

Het `TranslationMatch` object bestaat uit deze waarden:

* `Error`: De foutcode, als er een fout optreedt voor een specifieke invoertekenreeks. Anders is dit veld leeg.
* `MatchDegree`: Geeft aan hoe nauw de invoertekst overeenkomt met de oorspronkelijke tekst in het archief. Het systeem komt overeen met invoerzinnen ten opzichte van de winkel, inclusief onnauwkeurige overeenkomsten. De geretourneerde waarde varieert van 0 tot 100, waarbij 0 geen gelijkenis is en 100 een exacte, casegevoelige overeenkomst is.
* `MatchedOriginalText`: Originele tekst die is afgestemd op dit resultaat. Deze waarde wordt alleen geretourneerd als de overeenkomende oorspronkelijke tekst anders was dan de invoertekst. Het wordt gebruikt om de brontekst van een vage overeenkomst terug te geven. Deze waarde wordt niet geretourneerd voor de resultaten van Microsoft Translator.
* `Rating`: Geeft de bevoegdheid aan van de persoon die de kwaliteitsbeslissing neemt. Machine Translation resultaten hebben een rating van 5. Anoniem verstrekte vertalingen hebben over het algemeen een rating van 1 tot en met 4. Gezaghebbend verstrekte vertalingen hebben over het algemeen een beoordeling van 6 tot en met 10.
* `Count`: Het aantal keren dat deze vertaling met deze beoordeling is geselecteerd. De waarde is 0 voor het automatisch vertaalde antwoord.
* `TranslatedText`: De vertaalde tekst.

### <a name="response-class-status-200"></a>Antwoordklasse (status 200)
Een `GetTranslationsResponse` object in de eerder beschreven indeling.

tekenreeks

Inhoudstype antwoord: toepassing/xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Appid|(leeg)|Vereist. Als `Authorization` de `Ocp-Apim-Subscription-Key` of-koptekst `appid` wordt gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .|query|tekenreeks|
|tekst|(leeg)|Vereist. Een tekenreeks die de tekst vertegenwoordigt die moet worden vertaald. De maximale grootte van de tekst is 10.000 tekens.|query|tekenreeks|
|from|(leeg)|Vereist. Een tekenreeks die de taalcode van de tekst die wordt vertaald vertegenwoordigt.|query|tekenreeks|
|tot |(leeg)    |Vereist. Een tekenreeks die de taalcode van de taal vertegenwoordigt om de tekst naar te vertalen.|query|tekenreeks|
|maxVertalingen|(leeg)|Vereist. Een geheel getal dat het maximum aantal vertalingen vertegenwoordigt dat moet worden geretourneerd.|query|geheel getal|
|Autorisatie| (leeg)|Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven. Autorisatietoken: `"Bearer" + " " + "access_token"`.|tekenreeks|  koptekst|
|Ocp-Apim-Subscription-Key|(leeg)  |Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503|Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Uitvoeringsnota's
Hiermee haalt u meerdere vertaalkandidaten op voor meerdere bronteksten.

Het verzoek `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`URI is .

Hier is het formaat van de aanvraaginstantie:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest`omvat de volgende elementen:

* `AppId`: Vereist. Als `Authorization` de koptekst wordt `AppId` gebruikt, laat u het veld leeg. Voeg anders een tekenreeks `"Bearer" + " " + "access_token"`toe die .
* `From`: Vereist. Een tekenreeks die de taalcode van de tekst die wordt vertaald vertegenwoordigt.
* `MaxTranslations`: Vereist. Een geheel getal dat het maximum aantal vertalingen vertegenwoordigt dat moet worden geretourneerd.
* `Options`: Optioneel. Een `Options` object dat de volgende waarden bevat. Ze zijn allemaal optioneel en standaard voor de meest voorkomende instellingen. Gespecificeerde elementen moeten in alfabetische volgorde worden vermeld.
    - `Category`: Een tekenreeks die de categorie (domein) van de vertaling bevat. De standaardwaarde is `general`.
    - `ContentType`: De enige ondersteunde optie en `text/plain`de standaardoptie is .
    - `IncludeMultipleMTAlternatives`: Een Booleaanse vlag om aan te geven of er meer dan één alternatief moet worden geretourneerd van de MT-engine. Geldige waarden `true` `false` zijn en (hoofdlettergevoelig). De standaardinstelling is `false`, die slechts één alternatief retourneert. Het instellen `true` van de vlag om het genereren van kunstmatige alternatieven in vertaling mogelijk te maken, volledig geïntegreerd met het Collaborative Translations Framework (CTF). De functie maakt terugkerende alternatieven voor zinnen die geen alternatieven in CTF hebben door het toevoegen van kunstmatige alternatieven uit de *n-beste*lijst van de decoder.
        - Beoordelingen De beoordelingen worden als volgt toegepast:
          - De beste automatische vertaling heeft een rating van 5.
          - De alternatieven van CTF weerspiegelen het gezag van de beoordelaar. Ze variëren van -10 tot +10.
          - De automatisch gegenereerde *(n*-beste) vertaalalternatieven hebben een score van 0 en een matchgraad van 100.
        - Aantal alternatieven. Het aantal geretourneerde alternatieven kan net zo `maxTranslations`hoog zijn als de waarde die is opgegeven in , maar het kan lager zijn.
        - Taalparen. Deze functionaliteit is niet beschikbaar voor vertalingen tussen vereenvoudigd Chinees en Traditioneel Chinees, in beide richtingen. Het is beschikbaar voor alle andere taalparen die worden ondersteund door Microsoft Translator.
* `State`: Gebruikersstatus om de aanvraag en het antwoord te correleren. Dezelfde inhoud wordt geretourneerd in het antwoord.
* `Uri`: Filter resultaten door deze URI. Als er geen waarde is `all`ingesteld, is de standaardinstelling .
* `User`: Filter resultaten door deze gebruiker. Als er geen waarde is `all`ingesteld, is de standaardinstelling .
* `Texts`: Vereist. Een array die de tekst voor vertaling bevat. Alle snaren moeten in dezelfde taal zijn. Het totaal van alle te vertalen tekst mag niet hoger zijn dan 10.000 tekens. Het maximum aantal matrixelementen is 10.
* `To`: Vereist. Een tekenreeks die de taalcode van de taal vertegenwoordigt om de tekst naar te vertalen.

U optionele elementen weglaten. Elementen waarvan directe `GetTranslationsArrayRequest` kinderen zijn, moeten in alfabetische volgorde worden vermeld.

Verzoek `Content-Type` moet `text/xml`worden .

**Retourwaarde:** Hier is het formaat van het antwoord:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Elk `GetTranslationsResponse` element bevat de volgende waarden:

* `Translations`: Een array van de `TranslationMatch` gevonden overeenkomsten, opgeslagen in objecten (beschreven in de volgende sectie). De vertalingen kunnen lichte varianten van de oorspronkelijke tekst bevatten (fuzzy matching). De vertalingen worden gesorteerd: 100% komt als eerste overeen, fuzzy matches volgende.
* `From`: Als de methode geen `From` taal opgeeft, komt deze waarde voort uit automatische taaldetectie. Anders is dit de `From` opgegeven taal.
* `State`: Gebruikersstatus om de aanvraag en het antwoord te correleren. Bevat de opgegeven waarde `TranslateOptions` in de parameter.

Het `TranslationMatch` object bevat de volgende waarden:
* `Error`: De foutcode, als er een fout optreedt voor een specifieke invoertekenreeks. Anders is dit veld leeg.
* `MatchDegree`: Geeft aan hoe nauw de invoertekst overeenkomt met de oorspronkelijke tekst in het archief. Het systeem komt overeen met invoerzinnen ten opzichte van de winkel, inclusief onnauwkeurige overeenkomsten. De geretourneerde waarde varieert van 0 tot 100, waarbij 0 geen gelijkenis is en 100 een exacte, casegevoelige overeenkomst is.
* `MatchedOriginalText`: Originele tekst die is afgestemd op dit resultaat. Deze waarde wordt alleen geretourneerd als de overeenkomende oorspronkelijke tekst anders was dan de invoertekst. Het wordt gebruikt om de brontekst van een vage overeenkomst terug te geven. Deze waarde wordt niet geretourneerd voor de resultaten van Microsoft Translator.
* `Rating`: Geeft de bevoegdheid aan van de persoon die de kwaliteitsbeslissing neemt. Machine Translation resultaten hebben een rating van 5. Anoniem verstrekte vertalingen hebben over het algemeen een rating van 1 tot en met 4. Gezaghebbend verstrekte vertalingen hebben over het algemeen een rating van 6 tot en met 10.
* `Count`: Het aantal keren dat deze vertaling met deze beoordeling is geselecteerd. De waarde is 0 voor het automatisch vertaalde antwoord.
* `TranslatedText`: De vertaalde tekst.


### <a name="response-class-status-200"></a>Antwoordklasse (status 200)

tekenreeks

Inhoudstype antwoord: toepassing/xml
 
### <a name="parameters"></a>Parameters

|Parameter|Waarde|Beschrijving|Parametertype|Gegevenstype|
|:--|:--|:--|:--|:--|
|Autorisatie  |(leeg)    |Vereist als `appid` zowel het `Ocp-Apim-Subscription-Key` veld als de koptekst leeg blijven.  Autorisatietoken: `"Bearer" + " " + "access_token"`.|koptekst|tekenreeks|
|Ocp-Apim-Subscription-Key|(leeg)  |Vereist als `appid` zowel het `Authorization` veld als de koptekst leeg blijven.|koptekst|tekenreeks|

### <a name="response-messages"></a>Reactieberichten

|HTTP-statuscode|Reden|
|:--|:--|
|400    |Slecht verzoek. Controleer invoerparameters en de gedetailleerde foutreactie.|
|401    |Ongeldige referenties.|
|500    |Serverfout. Als de fout blijft bestaan, laat het ons weten. Geef ons de geschatte datum & tijdstip van het verzoek en `X-MS-Trans-Info`met de aanvraag-ID opgenomen in de reactie header .|
|503    |Service tijdelijk niet beschikbaar. Probeer het opnieuw en laat het ons weten als de fout blijft bestaan.|

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Migreren naar Translator Text API v3](../migrate-to-v3.md)


