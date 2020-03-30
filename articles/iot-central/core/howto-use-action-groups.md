---
title: Meerdere acties uitvoeren vanuit een Azure IoT Central-regel | Microsoft Documenten
description: Voer meerdere acties uit vanuit één IoT Central-regel en maak herbruikbare groepen acties die u uitvoeren vanuit meerdere regels.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157684"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Meerdere acties groeperen om uit te voeren vanaf een of meer regels

*Dit artikel is van toepassing op bouwers en beheerders.*

In Azure IoT Central maakt u regels om acties uit te voeren wanneer aan een voorwaarde is voldaan. Regels zijn gebaseerd op telemetrie of gebeurtenissen van het apparaat. U bijvoorbeeld een operator op de hoogte stellen wanneer de temperatuur van een apparaat een drempelwaarde overschrijdt. In dit artikel wordt beschreven hoe u [Azure Monitor-actiegroepen](../../azure-monitor/overview.md) *action groups* gebruikt om meerdere acties aan een IoT Central-regel toe te voegen. U een actiegroep aan meerdere regels koppelen. Een [actiegroep](../../azure-monitor/platform/action-groups.md) is een verzameling meldingsvoorkeuren die zijn gedefinieerd door de eigenaar van een Azure-abonnement.

## <a name="prerequisites"></a>Vereisten

- Een toepassing die is gemaakt met een standaardprijsplan
- Een Azure-account en -abonnement voor het maken en beheren van Azure Monitor-actiegroepen

## <a name="create-action-groups"></a>Actiegroepen maken

U [actiegroepen maken en beheren in de Azure-portal](../../azure-monitor/platform/action-groups.md) of met een [Azure Resource Manager-sjabloon.](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)

Een actiegroep kan:

- Stuur meldingen zoals een e-mail, een sms of bel.
- Voer een actie uit, zoals het aanroepen van een webhook.

De volgende schermafbeelding toont een actiegroep die e-mail- en sms-meldingen verzendt en een webhook aanroept:

![Actiegroep](media/howto-use-action-groups/actiongroup.png)

Als u een actiegroep wilt gebruiken in een IoT Central-regel, moet de actiegroep zich in hetzelfde Azure-abonnement bevinden als de IoT Central-toepassing.

## <a name="use-an-action-group"></a>Een actiegroep gebruiken

Als u een actiegroep wilt gebruiken in uw IoT Central-toepassing, maakt u eerst een regel. Wanneer u een actie aan de regel toevoegt, selecteert u **Azure Monitor-actiegroepen:**

![Actie kiezen](media/howto-use-action-groups/chooseaction.png)

Kies een actiegroep uit uw Azure-abonnement:

![Actiegroep kiezen](media/howto-use-action-groups/chooseactiongroup.png)

Selecteer **Opslaan**. De actiegroep wordt nu weergegeven in de lijst met acties die moeten worden uitgevoerd wanneer de regel wordt geactiveerd:

![Opgeslagen actiegroep](media/howto-use-action-groups/savedactiongroup.png)

In de volgende tabel worden de informatie die naar de ondersteunde actietypen wordt verzonden, samengevat:

| Actietype | Uitvoerindeling |
| ----------- | -------------- |
| Email       | Standaard IoT Central-e-mailsjabloon |
| Sms         | Azure IoT Central-waarschuwing: ${applicationName} - "${ruleName}" geactiveerd op "${deviceName}" op ${triggerDate} ${triggerTime} |
| Spraak       | Azure I.O.T Central alert: regel "${ruleName}" geactiveerd op apparaat "${deviceName}" bij ${triggerDate} ${triggerTime}, in toepassing ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](howto-create-webhooks.md#payload)}} |

De volgende tekst is een voorbeeld van sms-bericht van een actiegroep:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u actiegroepen met regels gebruiken, is de voorgestelde volgende stap om te leren hoe [u uw apparaten beheren.](howto-manage-devices.md)
