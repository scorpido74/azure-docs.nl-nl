---
title: Meerdere acties uitvoeren vanuit een Azure IoT Central-regel | Microsoft Docs
description: Meerdere acties uitvoeren vanuit één IoT Central regel en herbruikbare groepen met acties maken die u vanuit meerdere regels kunt uitvoeren.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: cc89033cedcdf185ce7e18c3981045e2a20569f5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952171"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Meerdere acties groeperen om uit te voeren vanuit een of meer regels

*Dit artikel is van toepassing op bouwers en beheerders.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In azure IoT Central maakt u regels om acties uit te voeren wanneer aan een voor waarde wordt voldaan. Regels zijn gebaseerd op telemetrie van apparaten of gebeurtenissen. U kunt bijvoorbeeld een operator waarschuwen wanneer de Tempe ratuur van een apparaat een drempel waarde overschrijdt. In dit artikel wordt beschreven hoe u [Azure monitor](../../azure-monitor/overview.md) *actie groepen* kunt gebruiken om meerdere acties aan een IOT Central regel te koppelen. U kunt een actie groep koppelen aan meerdere regels. Een [actie groep](../../azure-monitor/platform/action-groups.md) is een verzameling voor keuren voor meldingen die zijn gedefinieerd door de eigenaar van een Azure-abonnement.

## <a name="prerequisites"></a>Vereisten

- Een betalen naar gebruik-toepassing
- Een Azure-account en-abonnement om Azure Monitor actie groepen te maken en te beheren

## <a name="create-action-groups"></a>Actiegroepen maken

U kunt [actie groepen maken en beheren in de Azure Portal](../../azure-monitor/platform/action-groups.md) of met een [Azure Resource Manager sjabloon](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Een actie groep kan:

- Verzend meldingen zoals een e-mail, een SMS of een telefoon gesprek.
- Een actie uitvoeren zoals het aanroepen van een webhook.

De volgende scherm afbeelding toont een actie groep die e-mail-en SMS-meldingen verzendt en een webhook aanroept:

![Actie groep](media/howto-use-action-groups/actiongroup.png)

Als u een actie groep wilt gebruiken in een IoT Central regel, moet de actie groep zich in hetzelfde Azure-abonnement bevallen als de IoT Central-toepassing.

## <a name="use-an-action-group"></a>Een actie groep gebruiken

Als u een actie groep wilt gebruiken in uw IoT Central-toepassing, maakt u eerst een telemetrie of een regel voor een gebeurtenis. Wanneer u een actie aan de regel toevoegt, selecteert u **Azure monitor actie groepen**:

![Actie kiezen](media/howto-use-action-groups/chooseaction.png)

Kies een actie groep uit uw Azure-abonnement:

![Actie groep kiezen](media/howto-use-action-groups/chooseactiongroup.png)

Selecteer **Opslaan**. De actie groep wordt nu weer gegeven in de lijst met acties die moeten worden uitgevoerd wanneer de regel wordt geactiveerd:

![Opgeslagen actie groep](media/howto-use-action-groups/savedactiongroup.png)

De volgende tabel bevat een overzicht van de informatie die wordt verzonden naar de ondersteunde actie typen:

| Actietype | Uitvoer indeling |
| ----------- | -------------- |
| E-mail       | E-mail sjabloon standaard IoT Central |
| Sms         | Azure IoT Central-waarschuwing: $ {ApplicationName}-"$ {ruleNaam} ' geactiveerd op ' $ {DeviceName} ' op $ {triggerDate} $ {triggerTime} |
| Spraak       | Azure I. O. T Central alert: regel "$ {DeviceName}" geactiveerd op apparaat "$ {apparaatnaam}" op $ {triggerDate} $ {triggerTime}, in toepassing $ {ApplicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "gegevens": {[normale webhook Payload](#payload)}} |

De volgende tekst is een voor beeld van een SMS-bericht van een actie groep:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>In de volgende JSON ziet u een voor beeld van een webhook-actie Payload:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u actie groepen met regels kunt gebruiken, is de voorgestelde volgende stap om te leren hoe u [uw apparaten beheert](howto-manage-devices.md).
