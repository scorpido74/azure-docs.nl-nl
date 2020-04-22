---
title: Directe azure IoT Hub-methoden begrijpen | Microsoft Documenten
description: Handleiding voor ontwikkelaars - gebruik directe methoden om code op uw apparaten aan te roepen vanuit een service-app.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 13936a55baed59d5b6257f13f69305a1ce72927a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730390"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Directe methoden van IoT Hub begrijpen en aanroepen

IoT Hub geeft u de mogelijkheid om directe methoden aan te roepen op apparaten vanuit de cloud. Directe methoden vertegenwoordigen een interactie tussen verzoek en antwoord met een apparaat dat vergelijkbaar is met een HTTP-aanroep, omdat ze onmiddellijk slagen of mislukken (na een door de gebruiker opgegeven time-out). Deze aanpak is handig voor scenario's waarin het verloop van de onmiddellijke actie verschilt, afhankelijk van of het apparaat in staat was om te reageren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Elke apparaatmethode is gericht op één apparaat. [Als u taken op meerdere apparaten plant,](iot-hub-devguide-jobs.md) u een manier bieden om directe methoden op meerdere apparaten aan te roepen en methodeaanroep plannen voor losgekoppelde apparaten.

Iedereen met **serviceconnect-machtigingen** op IoT Hub kan een methode op een apparaat aanroepen.

Directe methoden volgen een patroon van het antwoord op verzoek en zijn bedoeld voor communicatie die onmiddellijke bevestiging van hun resultaat vereist. Bijvoorbeeld interactieve bediening van het apparaat, zoals het inschakelen van een ventilator.

Raadpleeg [communicatierichtlijnen voor de cloud naar het apparaat](iot-hub-devguide-c2d-guidance.md) als u twijfelt tussen het gebruik van de gewenste eigenschappen, directe methoden of cloud-to-device-berichten.

## <a name="method-lifecycle"></a>Levenscyclus van de methode

Directe methoden worden geïmplementeerd op het apparaat en kunnen nul of meer ingangen in de methode payload nodig om correct instantiate. U beroept zich op een directe`{iot hub}/twins/{device id}/methods/`methode via een servicegerichte URI ( ). Een apparaat ontvangt directe methoden via een apparaatspecifiek`$iothub/methods/POST/{method name}/`MQTT-onderwerp ( `IoThub-methodname` ) `IoThub-status` of via AMQP-koppelingen (de eigenschappen en toepassingen). 

> [!NOTE]
> Wanneer u een directe methode op een apparaat aanroept, kunnen eigenschapsnamen en -waarden alleen drukbare alfanumerieke gegevens bevatten tussen de VS en ASCII, behalve die in de volgende set:``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Directe methoden zijn synchroon en slagen of mislukken na de time-outperiode (standaard: 30 seconden, settabel tussen 5 en 300 seconden). Directe methoden zijn handig in interactieve scenario's waarbij u wilt dat een apparaat werkt als en alleen als het apparaat online is en opdrachten ontvangt. Bijvoorbeeld het inschakelen van een lampje van een telefoon. In deze scenario's wilt u onmiddellijk succes of fout zien, zodat de cloudservice zo snel mogelijk op het resultaat kan reageren. Het apparaat kan een berichttekst retourneren als gevolg van de methode, maar het is niet vereist voor de methode om dit te doen. Er is geen garantie op het bestellen of een gelijktijdigheid semantiek op methode aanroepen.

Directe methoden zijn HTTPS-only vanaf de cloudkant en MQTT of AMQP vanaf de apparaatzijde.

De payload voor methodeaanvragen en -antwoorden is een JSON-document tot 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Een directe methode van een back-end-app aanroepen

Roep nu een directe methode aan vanuit een back-end-app.

### <a name="method-invocation"></a>Aanroep methode

Directe aanroepingen voor methoden op een apparaat zijn HTTPS-aanroepen die bestaan uit de volgende items:

* De *aanvraag URI* specifiek voor het apparaat samen met de [API-versie:](/rest/api/iothub/service/devicemethod/invokedevicemethod)

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* De *POST-methode*

* *Kopteksten* die de autorisatie, aanvraag-id, inhoudstype en inhoudscodering bevatten.

* Een transparante *JSON-body* in de volgende indeling:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

De waarde `responseTimeoutInSeconds` die wordt opgegeven zoals in de aanvraag is de hoeveelheid tijd die IoT Hub-service moet wachten op de voltooiing van een directe methodeuitvoering op een apparaat. Stel deze time-out in op ten minste even lang als de verwachte uitvoeringstijd van een directe methode door een apparaat. Als er geen time-out is voorzien, wordt de standaardwaarde van 30 seconden gebruikt. De minimum- en `responseTimeoutInSeconds` maximumwaarden voor respectievelijk 5 en 300 seconden zijn.

De waarde `connectTimeoutInSeconds` die wordt opgegeven zoals in het verzoek is de hoeveelheid tijd bij het inroepen van een directe methode die IoT Hub-service moet wachten op een losgekoppeld apparaat om online te komen. De standaardwaarde is 0, wat betekent dat apparaten al online moeten zijn bij het inroepen van een directe methode. De maximale `connectTimeoutInSeconds` waarde voor 300 seconden.


#### <a name="example"></a>Voorbeeld

Zie hieronder voor een `curl`barebone voorbeeld met behulp van . 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Antwoord

De back-end-app ontvangt een antwoord dat bestaat uit de volgende items:

* *HTTP-statuscode*:
  * 200 duidt op een succesvolle uitvoering van de directe methode;
  * 404 geeft aan dat een apparaat-ID ongeldig is, of dat het `connectTimeoutInSeconds` apparaat niet online was bij het inroepen van een directe methode en voor daarna (gebruik vergezelde foutmelding om de hoofdoorzaak te begrijpen);
  * 504 geeft een time-out van de gateway `responseTimeoutInSeconds`aan die wordt veroorzaakt doordat het apparaat niet reageert op een directe methodeaanroep binnen .

* *Kopteksten* die de ETag, aanvraag-ID, inhoudstype en inhoudscodering bevatten.

* Een *JSON-body* in het volgende formaat:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Beide `status` `body` en worden geleverd door het apparaat en gebruikt om te reageren met de eigen statuscode en/of beschrijving van het apparaat.

### <a name="method-invocation-for-iot-edge-modules"></a>Aanroep methode voor IoT Edge-modules

Directe methoden oproepen met behulp van een module-ID wordt ondersteund in de [IoT Service Client C# SDK.](https://www.nuget.org/packages/Microsoft.Azure.Devices/)

Gebruik hiervoor de `ServiceClient.InvokeDeviceMethodAsync()` methode en geef `deviceId` `moduleId` de methode door in de en als parameters.

## <a name="handle-a-direct-method-on-a-device"></a>Een directe methode op een apparaat hanteren

Laten we eens kijken hoe je een directe methode op een IoT-apparaat hanteren.

### <a name="mqtt"></a>MQTT

De volgende sectie is voor het MQTT-protocol.

#### <a name="method-invocation"></a>Aanroep methode

Apparaten ontvangen directe methodeaanvragen voor het `$iothub/methods/POST/{method name}/?$rid={request id}`MQTT-onderwerp: . Het aantal abonnementen per apparaat is beperkt tot 5. Het wordt daarom aanbevolen om niet op elke directe methode afzonderlijk in te schrijven. In plaats daarvan `$iothub/methods/POST/#` overwegen zich te abonneren op en vervolgens filteren de geleverde berichten op basis van de gewenste methode namen.

De behuizing die het apparaat ontvangt, heeft de volgende indeling:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Methodeaanvragen zijn QoS 0.

#### <a name="response"></a>Antwoord

Het apparaat stuurt `$iothub/methods/res/{status}/?$rid={request id}`reacties naar , waar:

* De `status` eigenschap is de door het apparaat geleverde status van de uitvoering van de methode.

* De `$rid` eigenschap is de aanvraag-id van de methode aanroep ontvangen van IoT Hub.

Het lichaam is ingesteld door het apparaat en kan elke status zijn.

### <a name="amqp"></a>AMQP

De volgende sectie is voor het AMQP-protocol.

#### <a name="method-invocation"></a>Aanroep methode

Het apparaat ontvangt directe methodeaanvragen door `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`een ontvangende koppeling op adres te maken.

Het AMQP-bericht wordt weergegeven op de koppeling ontvangen die de methodeaanvraag vertegenwoordigt. Het bevat de volgende secties:

* De eigenschap correlatie-ID, die een aanvraag-id bevat die moet worden doorgegeven met het bijbehorende methodeantwoord.

* Een eigenschap `IoThub-methodname`van een toepassing met de naam , die de naam bevat van de methode die wordt aangeroepen.

* De AMQP-berichtlichaam met de methodepayload als JSON.

#### <a name="response"></a>Antwoord

Het apparaat maakt een verzendkoppeling om `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`het antwoord op de methode op adres terug te sturen.

Het antwoord van de methode wordt geretourneerd op de verzendkoppeling en is als volgt gestructureerd:

* De eigenschap correlatie-ID, die de aanvraag-id bevat die is doorgegeven in het verzoekbericht van de methode.

* Een eigenschap `IoThub-status`van de toepassing met de naam , die de door de gebruiker geleverde methodestatus bevat.

* De AMQP-berichttekst met de methoderespons als JSON.

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere referentieonderwerpen in de IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eindpunten die elke IoT-hub blootlegt voor run-time- en beheerbewerkingen.

* [Beperking en quota](iot-hub-devguide-quotas-throttling.md) beschrijven de quota die van toepassing zijn en het beperkingsgedrag dat u verwachten wanneer u IoT Hub gebruikt.

* [Azure IoT-apparaat en service-SDK's](iot-hub-devguide-sdks.md) bevat de verschillende taal-SDK's die u gebruiken wanneer u zowel apparaat- als service-apps ontwikkelt die met IoT Hub werken.

* [IoT Hub-querytaal voor apparaattweelingen, taken en berichtroutering](iot-hub-devguide-query-language.md) beschrijft de IoT Hub-querytaal die u gebruiken om informatie uit IoT Hub op te halen over uw apparaattweeling en taken.

* [IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u directe methoden gebruiken, bent u mogelijk geïnteresseerd in het volgende artikel over de ontwikkelaar van IoT Hub:

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Als u enkele concepten wilt uitproberen die in dit artikel worden beschreven, bent u mogelijk geïnteresseerd in de volgende IoT Hub-zelfstudie:

* [Directe methoden gebruiken](quickstart-control-device-node.md)
* [Apparaatbeheer met Azure IoT Tools voor VS Code](iot-hub-device-management-iot-toolkit.md)
