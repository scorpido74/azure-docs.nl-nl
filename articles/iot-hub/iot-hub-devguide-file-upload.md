---
title: Inzicht in het uploaden van Azure IoT Hub-bestanden | Microsoft Documenten
description: Ontwikkelaarshandleiding - gebruik de functie voor het uploaden van bestanden van IoT Hub om het uploaden van bestanden van een apparaat naar een Azure-opslagblobcontainer te beheren.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 35e10c0f9babca7719ff496e7068ad1564670fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209151"
---
# <a name="upload-files-with-iot-hub"></a>Bestanden uploaden met IoT Hub

Zoals beschreven in het [artikel over de eindpunten van de IoT-hub,](iot-hub-devguide-endpoints.md) kan een apparaat het uploaden van een bestand starten door een melding te verzenden via een apparaatgericht eindpunt (**/apparaten/{deviceId}/bestanden).** Wanneer een apparaat iot-hub op de hoogte stelt dat een upload is voltooid, verzendt IoT Hub een meldingsbericht voor het uploaden van bestanden via het eindpunt **/berichten/servicebound/filenotifications** service.

In plaats van berichten te bemiddelen via IoT Hub zelf, fungeert IoT Hub in plaats daarvan als dispatcher voor een gekoppeld Azure Storage-account. Een apparaat vraagt een opslagtoken van IoT Hub aan dat specifiek is voor het bestand dat het apparaat wil uploaden. Het apparaat gebruikt de SAS URI om het bestand naar opslag te uploaden en wanneer de upload is voltooid, stuurt het apparaat een melding van voltooiing naar IoT Hub. IoT Hub controleert of het uploaden van bestanden is voltooid en voegt vervolgens een meldingsbericht voor bestandsupload toe aan het eindpunt voor bestandsmelding.

Voordat u een bestand vanaf een apparaat uploadt naar IoT Hub, moet u uw hub configureren door [er een Azure Storage-account](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) aan te koppelen.

Uw apparaat kan vervolgens [een upload initialiseren](iot-hub-devguide-file-upload.md#initialize-a-file-upload) en vervolgens [de IoT-hub op de hoogte stellen](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) wanneer de upload is voltooid. Wanneer een apparaat de IoT Hub meldt dat de upload is voltooid, kan de service optioneel een [meldinggenereren.](iot-hub-devguide-file-upload.md#file-upload-notifications)

### <a name="when-to-use"></a>Wanneer gebruikt u dit?

Gebruik het uploaden van bestanden om mediabestanden en grote telemetriebatches te verzenden die zijn geüpload door met tussenpozen verbonden apparaten of gecomprimeerd om bandbreedte te besparen.

Raadpleeg [communicatierichtlijnen voor apparaat tot cloud](iot-hub-devguide-d2c-guidance.md) als u twijfelt tussen het gebruik van gerapporteerde eigenschappen, apparaat-naar-cloudberichten of het uploaden van bestanden.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Een Azure Storage-account koppelen aan IoT Hub

Als u de functionaliteit voor het uploaden van bestanden wilt gebruiken, moet u eerst een Azure Storage-account koppelen aan de IoT Hub. U deze taak voltooien via de Azure-portal of programmatisch via de [REST-API's van de IoT Hub-bronprovider.](/rest/api/iothub/iothubresource) Zodra u een Azure Storage-account aan uw IoT Hub hebt gekoppeld, retourneert de service een SAS URI naar een apparaat wanneer het apparaat een aanvraag voor het uploaden van bestanden start.

De bestanden uploaden van uw apparaat naar de cloud met handleidingen voor het uploaden van [iot-hub](iot-hub-csharp-csharp-file-upload.md) bieden een volledige doorloop van het bestandsuploadproces. Deze handleidingen laten u zien hoe u de Azure-portal gebruiken om een opslagaccount te koppelen aan een IoT-hub.

> [!NOTE]
> De [Azure IoT SDKs](iot-hub-devguide-sdks.md) verwerken automatisch het ophalen van de SAS URI, uploaden het bestand en stellen IoT Hub op de hoogte van een voltooide upload.

## <a name="initialize-a-file-upload"></a>Een bestandsupload initialiseren
IoT Hub heeft een eindpunt speciaal voor apparaten om een SAS URI aan te vragen voor opslag om een bestand te uploaden. Om het uploadproces voor bestanden te starten, stuurt het apparaat een POST-verzoek naar `{iot hub}.azure-devices.net/devices/{deviceId}/files` met de volgende JSON-hoofdtekst:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub retourneert de volgende gegevens, die het apparaat gebruikt om het bestand te uploaden:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Afgeschaft: een bestandsupload initialiseren met een GET

> [!NOTE]
> In deze sectie wordt beschreven hoe u een SAS URI van IoT Hub ontvangt. Gebruik de eerder beschreven POST-methode.

IoT Hub heeft twee REST-eindpunten om het uploaden van bestanden te ondersteunen, een om de SAS URI voor opslag te krijgen en de andere om de IoT-hub op de hoogte te stellen van een voltooide upload. Het apparaat start het proces voor het uploaden `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`van bestanden door een GET naar de IoT-hub te sturen op . De IoT-hub retourneert:

* Een SAS URI die specifiek is voor het bestand dat moet worden geüpload.

* Een correlatie-ID die moet worden gebruikt zodra de upload is voltooid.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>IoT Hub op de hoogte stellen van een voltooide bestandsupload

Het apparaat uploadt het bestand naar opslag met behulp van de Azure Storage SDKs. Wanneer de upload is voltooid, stuurt `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` het apparaat een POST-verzoek naar met de volgende JSON-body:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

De waarde `isSuccess` van een Booleaan die aangeeft of het bestand is geüpload. De statuscode `statusCode` voor is de status voor het uploaden van het bestand naar opslag en de `statusDescription` status komt overeen met de `statusCode`.

## <a name="reference-topics"></a>Referentieonderwerpen:

De volgende referentieonderwerpen geven u meer informatie over het uploaden van bestanden vanaf een apparaat.

## <a name="file-upload-notifications"></a>Meldingen voor het uploaden van bestanden

Wanneer een apparaat de IoT Hub meldt dat een upload is voltooid, genereert IoT Hub optioneel een meldingsbericht. Dit bericht bevat de naam en opslaglocatie van het bestand.

Zoals uitgelegd in [Endpoints](iot-hub-devguide-endpoints.md), levert IoT Hub meldingen voor het uploaden van bestanden via een servicegericht eindpunt (**/berichten/servicebound/fileuploadmeldingen)** als berichten. De receive semantiek voor meldingen voor het uploaden van bestanden is dezelfde als voor berichten van cloud naar apparaat en heeft dezelfde [berichtlevenscyclus.](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle) Elk bericht dat wordt opgehaald uit het eindpunt van de bestandsuploadmelding is een JSON-record met de volgende eigenschappen:

| Eigenschap | Beschrijving |
| --- | --- |
| InqueuedTimeUtc |Tijdstempel die aangeeft wanneer de melding is gemaakt. |
| DeviceId |**DeviceId** van het apparaat dat het bestand heeft geüpload. |
| BlobUri BlobUri |URI van het geüploade bestand. |
| BlobName |Naam van het geüploade bestand. |
| LastUpdatedTime |Tijdstempel die aangeeft wanneer het bestand voor het laatst is bijgewerkt. |
| BlobSizeinbytes |Grootte van het geüploade bestand. |

**Voorbeeld**. In dit voorbeeld wordt de hoofdtekst van een meldingsbericht voor het uploaden van bestanden weergegeven.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Configuratieopties voor bestandsuploadmelding

Elke IoT-hub heeft de volgende configuratieopties voor meldingen voor het uploaden van bestanden:

| Eigenschap | Beschrijving | Bereik en standaard |
| --- | --- | --- |
| **inschakelenFileUploadMeldingen** |Hiermee bepaalt u of meldingen voor het uploaden van bestanden worden geschreven naar het eindpunt van bestandsmeldingen. |Bool. Standaard: True. |
| **fileNotifications.ttlAsIso8601** |Standaard TTL voor meldingen voor het uploaden van bestanden. |ISO_8601 interval tot 48H (minimaal 1 minuut). Standaard: 1 uur. |
| **fileNotifications.lockDuur** |De duur van de vergrendeling voor de wachtrij voor het uploaden van bestanden. |5 tot 300 seconden (minimaal 5 seconden). Standaard: 60 seconden. |
| **fileNotifications.maxDeliveryCount** |Maximaal aantal levering voor de file upload melding wachtrij. |1 tot 100. Standaard: 100. |

U deze eigenschappen instellen op uw IoT-hub met de Azure-portal, Azure CLI of PowerShell. Zie de onderwerpen onder [Bestandsupload configureren](iot-hub-configure-file-upload.md)voor meer informatie.

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere referentieonderwerpen in de IoT Hub-ontwikkelaarshandleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md) beschrijven de verschillende IoT-hubeindpunten voor run-time- en beheerbewerkingen.

* [Beperking en quota](iot-hub-devguide-quotas-throttling.md) beschrijven de quota en beperkingsgedragingen die van toepassing zijn op de IoT Hub-service.

* [Azure IoT-apparaat en service-SDK's](iot-hub-devguide-sdks.md) bevat de verschillende taal-SDK's die u gebruiken wanneer u zowel apparaat- als service-apps ontwikkelt die met IoT Hub werken.

* [IoT Hub-querytaal](iot-hub-devguide-query-language.md) beschrijft de querytaal die u gebruiken om informatie op te halen uit IoT Hub over uw apparaattweeling en taken.

* [IoT Hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub-ondersteuning voor het MQTT-protocol.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u bestanden uploaden vanaf apparaten met IoT Hub, bent u mogelijk geïnteresseerd in de volgende onderwerpen voor iot-hub-ontwikkelaars:

* [Apparaatidentiteiten beheren in IoT Hub](iot-hub-devguide-identity-registry.md)

* [Toegang tot IoT Hub regelen](iot-hub-devguide-security.md)

* [Apparaattweelingen gebruiken om status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)

* [Een directe methode op een apparaat aanroepen](iot-hub-devguide-direct-methods.md)

* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Zie de volgende IoT Hub-zelfstudie om een aantal concepten uit te proberen die in dit artikel worden beschreven:

* [Bestanden uploaden van apparaten naar de cloud met IoT Hub](iot-hub-csharp-csharp-file-upload.md)