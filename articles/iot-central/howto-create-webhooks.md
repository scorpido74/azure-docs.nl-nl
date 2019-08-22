---
title: Webhooks maken voor regels in azure IoT Central | Microsoft Docs
description: Webhooks maken in azure IoT Central om andere toepassingen automatisch te waarschuwen wanneer regels worden gestart.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 508e8b4b3a909e87f538f67b1ad9a5efdbcd9551
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876027"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-acties maken voor regels in azure IoT Central

*Dit onderwerp is van toepassing op bouwers en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Met webhooks kunt u uw IoT Central-app verbinden met andere toepassingen en services voor externe controle en meldingen. Webhooks melden automatisch andere toepassingen en services die u maakt wanneer een regel wordt geactiveerd in uw IoT Central-app. De IoT Central-app verzendt een POST-aanvraag naar het HTTP-eind punt van de andere toepassing wanneer een regel wordt geactiveerd. De payload bevat details over het apparaat en de regel trigger.

## <a name="set-up-the-webhook"></a>De webhook instellen

In dit voor beeld maakt u verbinding met RequestBin om een melding te ontvangen wanneer regels worden gestart met webhooks.

1. Open [RequestBin](https://requestbin.net/).

1. Maak een nieuwe RequestBin en kopieer de **URL van de opslag locatie**.

1. Een telemetrie- [regel](howto-create-telemetry-rules.md) of een [gebeurtenis regel](howto-create-event-rules.md)maken. Sla de regel op en voeg een nieuwe actie toe.

    ![Scherm webhook maken](media/howto-create-webhooks/webhookcreate.png)

1. Kies de actie webhook en geef een weergave naam op en plak de URL van de opslag plaats als de URL voor terugbellen.

1. Sla de regel op.

Wanneer de regel wordt geactiveerd, ziet u nu een nieuwe aanvraag wordt weer gegeven in RequestBin.

## <a name="payload"></a>Nettolading

Wanneer een regel wordt geactiveerd, wordt er een HTTP POST-aanvraag verzonden naar de call back-URL met een JSON-nettolading met de metingen, het apparaat, de regel en de toepassings gegevens. Voor een telemetrie-regel ziet de payload er als volgt uit:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Bekende beperkingen

Er is momenteel geen programmatische manier om u te abonneren op of afmelden bij deze webhooks via een API.

Als u ideeën hebt voor het verbeteren van deze functie, plaatst u uw suggesties op ons [UserVoice-forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u webhooks kunt instellen en gebruiken, is de voorgestelde volgende stap het [maken van werk stromen in Microsoft flow](howto-add-microsoft-flow.md)verkennen.
