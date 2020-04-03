---
title: Verwijzing naar vertalertekst-API V3.0
titleSuffix: Azure Cognitive Services
description: Referentiedocumentatie voor de Translator Text API V3.0. Versie 3 van de Translator Text API biedt een moderne JSON-gebaseerde Web API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 4/2/2020
ms.author: swmachan
ms.openlocfilehash: fcbaabac0961f1269a929fb4a56f81ac282bae29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619148"
---
# <a name="translator-text-api-v30"></a>Translator Text API v3.0

## <a name="whats-new"></a>Wat is nieuw?

Versie 3 van de Translator Text API biedt een moderne JSON-gebaseerde Web API. Het verbetert de bruikbaarheid en prestaties door bestaande functies te consolideren in minder bewerkingen en het biedt nieuwe functies.

 * Transliteratie om tekst in de ene taal om te zetten van het ene script naar het andere script.
 * Vertaling naar meerdere talen in één aanvraag.
 * Taaldetectie, vertaling en transliteratie in één aanvraag.
 * Woordenboek om alternatieve vertalingen van een term op te zoeken, om back-vertalingen en voorbeelden te vinden met termen die in context worden gebruikt.
 * Meer informatieve taaldetectieresultaten.

## <a name="base-urls"></a>Basis-URL's

Microsoft Translator wordt bediend vanuit meerdere datacenterlocaties. Momenteel bevinden ze zich in 10 [Azure-regio's:](https://azure.microsoft.com/global-infrastructure/regions)

* **Amerika:** Oost-VS, South Central US, West Central US en West US 2 
* **Azië-Pacific:** Korea Zuid, Japan Oost, Zuidoost-Azië, en Australië Oost
* **Europa:** Noord-Europa en West-Europa

Verzoeken aan de Microsoft Translator Text API worden in de meeste gevallen afgehandeld door het datacenter dat het dichtst bij de plaats van de aanvraag ligt. In het geval van een datacenterstoring kan de aanvraag worden doorgestuurd buiten de Azure-geografie.

Als u wilt afdwingen dat de aanvraag moet worden verwerkt door een specifieke Azure-geografie, wijzigt u het globale eindpunt in de API-aanvraag in het gewenste regionale eindpunt:

|Beschrijving|Azure-geografie|Basis-URL|
|:--|:--|:--|
|Azure|Globaal (niet-regionaal)|   api.cognitive.microsofttranslator.com|
|Azure|Verenigde Staten|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Azië en Stille Oceaan|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Verificatie

Abonneer u op Translator Text API of [Cognitive Services multi-service](https://azure.microsoft.com/pricing/details/cognitive-services/) in Azure Cognitive Services en gebruik uw abonnementssleutel (beschikbaar in de Azure-portal) om te verifiëren. 

Er zijn drie kopteksten die u gebruiken om uw abonnement te verifiëren. In deze tabel wordt beschreven hoe elk wordt gebruikt:

|Headers|Beschrijving|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Gebruik met cognitive services-abonnement als u uw geheime sleutel doorgeeft.*<br/>De waarde is de azure secret key voor uw abonnement op Translator Text API.|
|Autorisatie|*Gebruik met een abonnement op Cognitive Services als u een verificatietoken doorgeeft.*<br/>De waarde is het `Bearer <token>`token aan toonder: .|
|Ocp-Apim-Subscription-Regio|*Gebruik met Cognitive Services multi-service en regionale vertalersbron.*<br/>De waarde is de regio van de multi-service of regionale vertaler bron. Deze waarde is optioneel bij het gebruik van een globale vertalersbron.|

###  <a name="secret-key"></a>Geheime sleutel
De eerste optie is `Ocp-Apim-Subscription-Key` om te verifiëren met behulp van de koptekst. Voeg `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` de koptekst toe aan uw aanvraag.

#### <a name="authenticating-with-a-global-resource"></a>Authenticeren met een wereldwijde bron

Wanneer u een [globale vertalersbron](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)gebruikt, moet u één koptekst opnemen om de vertaler-API aan te roepen.

|Headers|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de azure secret key voor uw abonnement op Translator Text API.|

Hier is een voorbeeldverzoek om de Translator API aan te roepen met behulp van de globale vertalersbron

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Authenticeren met een regionale bron

Wanneer u een [regionale vertalersbron gebruikt.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)
Er zijn 2 headers die u nodig hebt om de vertaler API te bellen.

|Headers|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de azure secret key voor uw abonnement op Translator Text API.|
|Ocp-Apim-Subscription-Regio| De waarde is de regio van de vertalersbron. |

Hier is een voorbeeldverzoek om de Translator API aan te roepen met behulp van de regionale vertalersbron

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Authenticeren met een Multi-service resource

Wanneer u de multiservicebron van een Cognitive Service gebruikt. Hiermee u één geheime sleutel gebruiken om aanvragen voor meerdere services te verifiëren. 

Wanneer u een geheime sleutel voor meerdere diensten gebruikt, moet u twee verificatiekoppen bij uw aanvraag opnemen. Er zijn 2 headers die u nodig hebt om de vertaler API te bellen.

|Headers|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de Azure-geheime sleutel voor uw multiservicebron.|
|Ocp-Apim-Subscription-Regio| De waarde is de regio van de multi-service resource. |

Regio is vereist voor het multi-service Text API-abonnement. Het gebied dat u selecteert, is het enige gebied dat u gebruiken voor tekstvertaling wanneer u de abonnementssleutel voor meerdere services gebruikt en moet dezelfde regio zijn die u hebt geselecteerd toen u zich via de Azure-portal voor uw multiserviceabonnement hebt aangemeld.

De beschikbare `australiaeast` `brazilsouth`regio `canadacentral` `centralindia`'s zijn , `japaneast`, `japanwest` `koreacentral`, `northcentralus` `centralus`, `centraluseuap` `southeastasia`, `uksouth` `eastasia` `westcentralus` `westeurope` `westus` `westus2` `southafricanorth` `northeurope` `southcentralus` `eastus`, `eastus2` `francecentral`, , , , , , , , , , , , , , , en .

Als u de geheime sleutel in de `Subscription-Key`querytekenreeks met de parameter `Subscription-Region`doorgeeft, moet u het gebied met queryparameter opgeven.

### <a name="authenticating-with-an-access-token"></a>Authenticeren met een toegangstoken
U ook uw geheime sleutel inwisselen voor een toegangstoken. Dit token is bij elk `Authorization` verzoek inbegrepen als de koptekst. Als u een autorisatietoken `POST` wilt verkrijgen, dient u een aanvraag in voor de volgende URL:

| Resourcetype     | URL van verificatieservice                                |
|-----------------|-----------------------------------------------------------|
| Wereldwijd          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionale of multiservice | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Hier volgen voorbeeldverzoeken om een token te verkrijgen met een geheime sleutel:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Een succesvol verzoek retourneert het gecodeerde toegangstoken als platte tekst in de antwoordtekst. Het geldige token wordt doorgegeven aan de service Translator als token aan toonder in de autorisatie.

```http
Authorization: Bearer <Base64-access_token>
```

Een verificatietoken is 10 minuten geldig. Het token moet worden hergebruikt bij het voeren van meerdere gesprekken naar de Translator API's. Als uw programma echter gedurende een langere periode aanvragen voor de Translator API doet, moet uw programma op regelmatige tijdstippen (bijvoorbeeld elke 8 minuten) een nieuw toegangstoken aanvragen.

## <a name="virtual-network-support"></a>Ondersteuning voor virtuele netwerken

Vertalerservice is nu beschikbaar met mogelijkheden voor`WestUS2` `EastUS`virtueel `SouthCentralUS` `WestUS`netwerk `Central US EUAP` `global`in beperkte regio's ( , , , , , ). Zie [Azure Cognitive Services Virtual Networks configureren](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)voor virtueel netwerk configureren. 

Zodra u deze mogelijkheid inschakelt, moet u het aangepaste eindpunt gebruiken om de Translator API aan te roepen. U het eindpunt van de globale vertaler ('api.cognitive.microsofttranslator.com') niet gebruiken en u niet verifiëren met een toegangstoken.

U het aangepaste eindpunt vinden zodra u de [vertalersbron hebt gemaakt.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)

|Headers|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de azure secret key voor uw abonnement op Translator Text API.|
|Ocp-Apim-Subscription-Regio| De waarde is de regio van de vertalersbron. Deze waarde is optioneel als de resource`global`|

Hier is een voorbeeldverzoek om de Translator API aan te roepen met behulp van het aangepaste eindpunt

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Fouten

Een standaardfoutantwoord is een JSON-object `error`met naam/waardepaar met de naam . De waarde is ook een JSON-object met eigenschappen:

  * `code`: een door de server gedefinieerde foutcode.
  * `message`: Een snaar die een door de mens leesbare weergave van de fout geeft.

Een klant met een gratis proefabonnement ontvangt bijvoorbeeld de volgende fout zodra het gratis quotum is uitgeput:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
De foutcode is een 6-cijferig getal dat de 3-cijferige HTTP-statuscode combineert, gevolgd door een 3-cijferig getal om de fout verder te categoriseren. Veelvoorkomende foutcodes zijn:

| Code | Beschrijving |
|:----|:-----|
| 400000| Een van de aanvraagingangen is niet geldig.|
| 400001| De parameter 'bereik' is ongeldig.|
| 400002| De parameter 'categorie' is ongeldig.|
| 400003| Een taalaanduiding ontbreekt of is ongeldig.|
| 400004| Een doelscriptspecificifier ('Naar script') ontbreekt of ongeldig is.|
| 400005| Een invoertekst ontbreekt of is ongeldig.|
| 400006| De combinatie van taal en script is niet geldig.|
| 400018| Een bronscriptspecificer ('Van script') ontbreekt of ongeldig is.|
| 400019| Een van de opgegeven talen wordt niet ondersteund.|
| 400020| Een van de elementen in de array van invoertekst is niet geldig.|
| 400021| De parameter API-versie ontbreekt of ongeldig is.|
| 400023| Een van de opgegeven talenpaar is niet geldig.|
| 400035| De brontaal (veld'Van' is ongeldig.|
| 400036| De doeltaal ('Aan'-veld) ontbreekt of ongeldig is.|
| 400042| Een van de opgegeven opties (veld 'Opties' is ongeldig.|
| 400043| De clienttrace-id (clientTraceId-veld of X-ClientTranceId-header) ontbreekt of ongeldig is.|
| 400050| De invoertekst is te lang. [Aanvraaglimieten weergeven](../request-limits.md).|
| 400064| De parameter "vertaling" ontbreekt of ongeldig is.|
| 400070| Het aantal doelscripts (ToScript-parameter) komt niet overeen met het aantal doeltalen (Parameter Naar).|
| 400071| De waarde is niet geldig voor TextType.|
| 400072| De array met invoertekst heeft te veel elementen.|
| 400073| De scriptparameter is niet geldig.|
| 400074| De instantie van het verzoek is niet geldig JSON.|
| 400075| Het taalpaar en de categoriecombinatie zijn niet geldig.|
| 400077| De maximale aanvraaggrootte is overschreden. [Aanvraaglimieten weergeven](../request-limits.md).|
| 400079| Het aangepaste systeem dat wordt aangevraagd voor vertaling tussen van en naar taal bestaat niet.|
| 400080| Transliteratie wordt niet ondersteund voor de taal of het script.|
| 401000| De aanvraag is niet geautoriseerd omdat referenties ontbreken of ongeldig zijn.|
| 401015| "De referenties zijn voor de Speech API. Voor deze aanvraag zijn referenties voor de tekst-API vereist. Gebruik een abonnement op Translator Text API."|
| 403000| De bewerking is niet toegestaan.|
| 403001| De bewerking is niet toegestaan omdat het abonnement het gratis quotum heeft overschreden.|
| 405000| De aanvraagmethode wordt niet ondersteund voor de gevraagde bron.|
| 408001| Het gevraagde vertaalsysteem wordt voorbereid. Probeer het over een paar minuten opnieuw.|
| 408002| Vraag time-out wachten op inkomende stroom. De client heeft geen verzoek ingediend binnen de tijd dat de server bereid was te wachten. De klant kan het verzoek op een later tijdstip zonder wijzigingen herhalen.|
| 415000| De koptekst Inhoudstype ontbreekt of is ongeldig.|
| 429000, 429001, 429002| De server heeft de aanvraag afgewezen omdat de client de aanvraaglimieten heeft overschreden.|
| 500000| Er is een onverwachte fout opgetreden. Als de fout blijft bestaan, meldt u deze met datum/tijd van fout, verzoek-id van antwoordkopX-RequestId en client-id van de x-clienttraceid van de aanvraagkop.|
| 503000| Service is tijdelijk niet beschikbaar. Probeer het opnieuw. Als de fout blijft bestaan, meldt u deze met datum/tijd van fout, verzoek-id van antwoordkopX-RequestId en client-id van de x-clienttraceid van de aanvraagkop.|

## <a name="metrics"></a>Metrische gegevens 
Met statistieken u de informatie over het gebruik en de beschikbaarheid van vertalers bekijken in azure-portal, onder de sectie Statistieken zoals weergegeven in de onderstaande schermafbeelding. Zie [Gegevens- en platformstatistieken](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)voor meer informatie .

![Statistieken voor vertalers](../media/translatormetrics.png)

In deze tabel vindt u de beschikbare statistieken met een beschrijving van hoe ze worden gebruikt om api-aanroepen voor vertalingen te controleren.

| Metrische gegevens | Beschrijving |
|:----|:-----|
| Totaal Aantal oproepen| Totaal aantal API-aanroepen.|
| TotalTokencalls| Totaal aantal API-aanroepen via tokenservice met verificatietoken.|
| Succesvol bellen| Aantal succesvolle gesprekken.|
| Totaalfouten| Aantal oproepen met een foutmelding.|
| Geblokkeerde oproepen| Aantal oproepen dat de limiet voor het tarief of het quotum heeft overschreden.|
| Serverfouten| Aantal oproepen met interne serverfout(5XX).|
| ClientFouten| Aantal gesprekken met clientside error(4XX).|
| Latentie| Duur om aanvraag in milliseconden te voltooien.|
| Tekensvertaald| Totaal aantal tekens in binnenkomende tekstaanvraag.|
