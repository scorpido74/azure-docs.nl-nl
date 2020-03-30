---
title: Webhooks maken voor regels in Azure IoT Central | Microsoft Documenten
description: Maak webhooks in Azure IoT Central om andere toepassingen automatisch op de hoogte te stellen wanneer regels worden gestart.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158092"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Webhook-acties maken voor regels in Azure IoT Central

*Dit onderwerp is van toepassing op bouwers en beheerders.*

Webhooks stellen u in staat om uw IoT Central-app te verbinden met andere toepassingen en services voor bewaking en meldingen op afstand. Webhooks stellen automatisch andere toepassingen en services op de hoogte die u verbindt wanneer een regel wordt geactiveerd in uw IoT Central-app. Uw IoT Central-app stuurt een POST-verzoek naar het HTTP-eindpunt van de andere toepassing wanneer een regel wordt geactiveerd. De payload bevat apparaatdetails en regeltriggerdetails.

## <a name="set-up-the-webhook"></a>De webhook instellen

In dit voorbeeld maakt u verbinding met RequestBin om een melding te krijgen wanneer regels met behulp van webhooks worden gebruikt.

1. [Open RequestBin](https://requestbin.net/).

1. Maak een nieuwe RequestBin en kopieer de **URL van de prullenbak**.

1. Een [telemetrieregel maken](tutorial-create-telemetry-rules.md). Sla de regel op en voeg een nieuwe actie toe.

    ![Scherm voor het maken van webhook](media/howto-create-webhooks/webhookcreate.png)

1. Kies de webhook-actie en geef een weergavenaam op en plak de URL van de prullenbak als de URL van Callback.

1. Bewaar de regel.

Wanneer de regel wordt geactiveerd, ziet u een nieuwe aanvraag verschijnen in RequestBin.

## <a name="payload"></a>Nettolading

Wanneer een regel wordt geactiveerd, wordt een HTTP POST-verzoek ingediend naar de terugbel-URL met een json-payload met de telemetrie, het apparaat, de regel en de toepassingsgegevens. De payload kan er als volgt uitzien:

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

Momenteel is er geen programmatische manier van abonneren / afmelden van deze webhooks via een API.

Als je ideeën hebt om deze functie te verbeteren, plaats je je suggesties op ons [user voice-forum.](https://feedback.azure.com/forums/911455-azure-iot-central)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u webhooks instellen en gebruiken, is de voorgestelde volgende stap het [verkennen van het configureren van Azure Monitor-actiegroepen.](howto-use-action-groups.md)
