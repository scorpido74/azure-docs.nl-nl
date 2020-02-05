---
title: Webhooks maken voor regels in azure IoT Central | Microsoft Docs
description: Webhooks maken in azure IoT Central om andere toepassingen automatisch te waarschuwen wanneer regels worden gestart.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: db4e48a7bff9127810b051a9ab63bbe9d78cf6da
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022423"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-acties maken voor regels in azure IoT Central

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Met webhooks kunt u uw IoT Central-app verbinden met andere toepassingen en services voor externe controle en meldingen. Webhooks melden automatisch andere toepassingen en services die u maakt wanneer een regel wordt geactiveerd in uw IoT Central-app. De IoT Central-app verzendt een POST-aanvraag naar het HTTP-eind punt van de andere toepassing wanneer een regel wordt geactiveerd. De payload bevat details over het apparaat en de regel trigger.

## <a name="set-up-the-webhook"></a>De webhook instellen

In dit voor beeld maakt u verbinding met RequestBin om een melding te ontvangen wanneer regels worden gestart met webhooks.

1. Open [RequestBin](https://requestbin.net/).

1. Maak een nieuwe RequestBin en kopieer de **URL van de opslag locatie**.

1. Maak een [telemetrie-regel](tutorial-create-telemetry-rules.md). Sla de regel op en voeg een nieuwe actie toe.

    ![Scherm webhook maken](media/howto-create-webhooks/webhookcreate.png)

1. Kies de actie webhook en geef een weergave naam op en plak de URL van de opslag plaats als de URL voor terugbellen.

1. Sla de regel op.

Wanneer de regel wordt geactiveerd, ziet u nu een nieuwe aanvraag wordt weer gegeven in RequestBin.

## <a name="payload"></a>nettolading

Wanneer een regel wordt geactiveerd, wordt er een HTTP POST-aanvraag verzonden naar de call back-URL met een JSON-nettolading met de telemetrie, het apparaat, de regel en de toepassings gegevens. De payload kan er als volgt uitzien:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Bekende beperkingen

Er is momenteel geen programmatische manier om u te abonneren op of afmelden bij deze webhooks via een API.

Als u ideeën hebt voor het verbeteren van deze functie, plaatst u uw suggesties op het [forum voor gebruikers spraak](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u webhooks kunt instellen en gebruiken, is de voorgestelde volgende stap het configureren van [Azure monitor actie groepen](howto-use-action-groups.md)verkennen.
