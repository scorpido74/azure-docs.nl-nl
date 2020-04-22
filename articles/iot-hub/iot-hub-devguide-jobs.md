---
title: Inzicht in Azure IoT Hub-taken | Microsoft Documenten
description: Ontwikkelaarshandleiding - het plannen van taken die worden uitgevoerd op meerdere apparaten die zijn verbonden met uw IoT-hub. Taken kunnen tags en gewenste eigenschappen bijwerken en directe methoden op meerdere apparaten aanroepen.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730106"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Taken op meerdere apparaten plannen

Azure IoT Hub maakt een aantal bouwstenen mogelijk, zoals [twee eigenschappen en tags van het apparaat](iot-hub-devguide-device-twins.md) en directe [methoden.](iot-hub-devguide-direct-methods.md) Back-end-apps stellen apparaatbeheerders en operators doorgaans in staat om IoT-apparaten in bulk en op een gepland tijdstip bij te werken en ermee te communiceren. Taken voeren apparaatdubbele updates en directe methoden uit op een set apparaten op een gepland tijdstip. Een operator gebruikt bijvoorbeeld een back-end-app die een taak initieert en bijhoudt om een set apparaten opnieuw op te starten in gebouw 43 en verdieping 3 op een moment dat de werking van het gebouw niet zou verstoren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Overweeg taken te gebruiken wanneer u de voortgang van een van de volgende activiteiten op een reeks apparaten moet plannen en bijhouden:

* Gewenste eigenschappen bijwerken
* Tags bijwerken
* Directe methoden aanroepen

## <a name="job-lifecycle"></a>Levenscyclus van werk

Taken worden geïnitieerd door de back-end van de oplossing en onderhouden door IoT Hub. U een taak starten via`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`een uri met servicegerichte functie ( ) en`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`vragen om voortgang bij een uitvoerende taak via een uri met servicegerichte functie ( ). Als u de status wilt vernieuwen van het uitvoeren van taken zodra een taak is gestart, voert u een taakquery uit.

> [!NOTE]
> Wanneer u een taak initieert, kunnen eigenschapsnamen en -waarden alleen drukbare alfanumerieke gegevens bevatten, behalve in de volgende set:`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Taken om directe methoden uit te voeren

In het volgende fragment worden de gegevens van het HTTPS 1.1-verzoek weergegeven voor het uitvoeren van een [directe methode](iot-hub-devguide-direct-methods.md) op een set apparaten die een taak gebruiken:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

De queryvoorwaarde kan ook op één apparaat-id of op een lijst met apparaat-id's staan, zoals in de volgende voorbeelden wordt weergegeven:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub Query Language](iot-hub-devguide-query-language.md) omvat de IoT Hub-querytaal in meer detail.

In het volgende fragment ziet u de aanvraag en het antwoord voor een taak die is gepland om een directe methode met de naam testMethode aan te roepen op alle apparaten op contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Taken om de dubbele eigenschappen van het apparaat bij te werken

In het volgende fragment worden de gegevens van het HTTPS 1.1-verzoek weergegeven voor het bijwerken van de dubbele eigenschappen van het apparaat met behulp van een taak:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> De eigenschap *updateTwin* vereist een geldige etag-overeenkomst; bijvoorbeeld . `etag="*"`

In het volgende fragment ziet u de aanvraag en het antwoord voor een taak die is gepland om de dubbele eigenschappen van het apparaat bij te werken voor testapparaat op contoso-hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Vragen voor voortgang op taken

In het volgende fragment worden de gegevens van het HTTPS 1.1-verzoek weergegeven voor het opvragen naar taken:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

De continuationToken wordt verstrekt op basis van het antwoord.

U op elk apparaat de status van taakuitvoering opvragen met de [IoT Hub-querytaal voor apparaattweelingen, taken en berichtroutering.](iot-hub-devguide-query-language.md)

## <a name="jobs-properties"></a>Eigenschappen van taken

In de volgende lijst worden de eigenschappen en bijbehorende beschrijvingen weergegeven, die kunnen worden gebruikt bij het zoeken naar taken of taakresultaten.

| Eigenschap | Beschrijving |
| --- | --- |
| **jobId** |Sollicitatie verstrekt ID voor de baan. |
| **startTime** |Sollicitatie voorzien starttijd (ISO-8601) voor de functie. |
| **endTime** |IoT Hub verstrekte datum (ISO-8601) voor wanneer de taak is voltooid. Alleen geldig nadat de taak de status 'voltooid' heeft bereikt. |
| **Type** |Soorten taken: |
| | **schemaUpdateTwin**: Een taak die wordt gebruikt om een set gewenste eigenschappen of tags bij te werken. |
| | **scheduleDeviceMethod:** een taak die wordt gebruikt om een apparaatmethode aan te roepen op een set apparaattweelingen. |
| **Status** |Huidige stand van zaken. Mogelijke waarden voor status: |
| | **in behandeling**: Gepland en wachten om te worden opgehaald door de jobservice. |
| | **gepland**: Gepland voor een tijd in de toekomst. |
| | **uitgevoerd**: Momenteel actieve taak. |
| | **geannuleerd**: Taak is geannuleerd. |
| | **mislukt**: Taak is mislukt. |
| | **voltooid**: De taak is voltooid. |
| **deviceJobStatistics deviceJobStatistics deviceJobStatistics deviceJob** |Statistieken over de uitvoering van de taak. |
| | **eigenschappen deviceJobStatistics:** |
| | **deviceJobStatistics.deviceCount:** Aantal apparaten in de taak. |
| | **deviceJobStatistics.failedCount:** Aantal apparaten waarbij de taak is mislukt. |
| | **deviceJobStatistics.succeededCount**: Aantal apparaten waar de taak is geslaagd. |
| | **deviceJobStatistics.runningCount:** aantal apparaten dat momenteel de taak uitvoert. |
| | **deviceJobStatistics.pendingCount:** aantal apparaten dat in behandeling is om de taak uit te voeren. |

### <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere referentieonderwerpen in de IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eindpunten die elke IoT-hub blootlegt voor run-time- en beheerbewerkingen.

* [Beperking en quota](iot-hub-devguide-quotas-throttling.md) beschrijven de quota die van toepassing zijn op de IoT Hub-service en het beperkingsgedrag dat u verwachten wanneer u de service gebruikt.

* [Azure IoT-apparaat en service-SDK's](iot-hub-devguide-sdks.md) bevat de verschillende taal-SDK's die u gebruiken wanneer u zowel apparaat- als service-apps ontwikkelt die met IoT Hub werken.

* [IoT Hub-querytaal voor apparaattweelingen, taken en berichtroutering](iot-hub-devguide-query-language.md) beschrijft de IoT Hub-querytaal. Gebruik deze querytaal om informatie op te halen uit IoT Hub over uw apparaattweeling en vacatures.

* [IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende IoT Hub-zelfstudie om een aantal concepten uit te proberen die in dit artikel worden beschreven:

* [Taken plannen en uitzenden](iot-hub-node-node-schedule-jobs.md)