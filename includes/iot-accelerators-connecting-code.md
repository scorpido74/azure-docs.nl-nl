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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176470"
---
### <a name="code-walkthrough"></a>Kennismaking met code

In deze sectie worden enkele van de belangrijkste onderdelen van de voorbeeld code beschreven en wordt uitgelegd hoe ze betrekking hebben op de oplossing voor externe controle.

Het volgende code fragment laat zien hoe de gerapporteerde eigenschappen die de mogelijkheden van het apparaat beschrijven, worden gedefinieerd. Deze eigenschappen zijn onder andere:

- De locatie van het apparaat om de oplossings versneller in te scha kelen om het apparaat toe te voegen aan de kaart.
- De huidige firmware versie.
- De lijst met methoden die het apparaat ondersteunt.
- Het schema van de telemetrie-berichten die door het apparaat worden verzonden.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Het voor beeld bevat een **serializeToJson** -functie waarmee deze gegevens structuur wordt geserialiseerd met behulp van de Parson-bibliotheek.

Het voor beeld bevat verschillende call back-functies die informatie naar de-console afdrukken wanneer de client communiceert met de oplossings versneller:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

In het volgende code fragment wordt de functie **device_method_callback** weer gegeven. Deze functie bepaalt welke actie moet worden ondernomen wanneer een methode aanroep wordt ontvangen van de oplossings versneller. De functie ontvangt een verwijzing naar de **Chiller** -gegevens structuur in de para meter **userContextCallback** . De waarde van **userContextCallback** wordt ingesteld wanneer de call back functie is geconfigureerd in de **hoofd** functie:

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Wanneer de oplossings versneller de firmware-update methode aanroept, deserialiseren het voor beeld de JSON-nettolading en start een achtergrond thread om het update proces te volt ooien. Het volgende code fragment toont de **do_firmware_update** die op de thread wordt uitgevoerd:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Het volgende code fragment laat zien hoe de client een telemetrie-bericht verzendt naar de oplossings versneller. De bericht eigenschappen bevatten het bericht schema om de oplossings versneller te helpen de telemetrie op het dash board weer te geven:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

De **belangrijkste** functie in het voor beeld:

- Initialiseert het SDK-subsysteem en sluit het af.
- Initialiseert de gegevens structuur **Chiller** .
- Hiermee worden de gerapporteerde eigenschappen verzonden naar de oplossings versneller.
- Hiermee configureert u de call back-functie van de methode van het apparaat.
- Verstuurt gesimuleerde telemetrie-waarden naar de oplossings versneller.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
