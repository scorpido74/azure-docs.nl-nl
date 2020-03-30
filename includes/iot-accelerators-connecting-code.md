---
title: bestand opnemen
description: bestand opnemen
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176470"
---
### <a name="code-walkthrough"></a>Kennismaking met code

In deze sectie worden enkele van de belangrijkste onderdelen van de voorbeeldcode beschreven en wordt uitgelegd hoe deze zich verhouden tot de versneller van de oplossing op afstand.

In het volgende fragment ziet u hoe de gerapporteerde eigenschappen die de mogelijkheden van het apparaat beschrijven, worden gedefinieerd. Deze eigenschappen omvatten:

- De locatie van het apparaat om de oplossingsversneller in staat te stellen het apparaat aan de kaart toe te voegen.
- De huidige firmwareversie.
- De lijst met methoden die het apparaat ondersteunt.
- Het schema van de telemetrieberichten die door het apparaat worden verzonden.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Het voorbeeld bevat een **serializeToJson-functie** die deze gegevensstructuur serialiseert met behulp van de Parson-bibliotheek.

Het voorbeeld bevat verschillende callbackfuncties die informatie afdrukken op de console terwijl de client samenwerkt met de oplossingsversneller:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

In het volgende fragment ziet u de **functie device_method_callback.** Deze functie bepaalt de actie die moet worden ondernomen wanneer een methodeaanroep wordt ontvangen van de oplossingsversneller. De functie ontvangt een verwijzing naar de **Chiller-gegevensstructuur** in de parameter **userContextCallback.** De waarde van **userContextCallback** wordt ingesteld wanneer de terugbelfunctie is geconfigureerd in de **hoofdfunctie:**

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Wanneer de oplossingsversneller de firmware-updatemethode aanroept, deserialiseert het voorbeeld de JSON-payload en start het een achtergrondthread om het updateproces te voltooien. In het volgende fragment ziet u de **do_firmware_update** die op de thread wordt uitgevoerd:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

In het volgende fragment ziet u hoe de client een telemetriebericht naar de oplossingsversneller verzendt. De berichteigenschappen bevatten het berichtschema om de oplossingsversneller te helpen de telemetrie op het dashboard weer te geven:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

De **hoofdfunctie** in het monster:

- Initialiseert en sluit het SDK-subsysteem af.
- Initialiseert de **Chiller** chiller-gegevensstructuur.
- Hiermee worden de gerapporteerde eigenschappen naar de oplossingsversneller verstuurt.
- Hiermee configureert u de terugbelfunctie van de apparaatmethode.
- Hiermee verzendt u gesimuleerde telemetriewaarden naar de oplossingsversneller.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
