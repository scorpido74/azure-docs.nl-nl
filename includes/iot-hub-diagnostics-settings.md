---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792014"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Logboek registratie met Diagnostische instellingen inschakelen

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en ga naar uw IoT Hub.

2. Selecteer **instellingen voor diagnostische gegevens**.

3. Selecteer **Diagnostische gegevens inschakelen**.

   ![Diagnostische gegevens inschakelen](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Geef de diagnostische instellingen een naam.

5. Kies waar u de logboeken wilt verzenden. U kunt een combi natie van de drie opties selecteren:

   * Archiveren naar een opslagaccount
   * Streamen naar een Event Hub
   * Verzenden naar Log Analytics

6. Kies welke bewerkingen u wilt bewaken en schakel Logboeken in voor deze bewerkingen. De bewerkingen waarvan de diagnostische instellingen kunnen rapporteren, zijn:

   * Verbindingen
   * Apparaattelemetrie
   * Cloud-naar-apparaat-berichten
   * Bewerkingen voor apparaat-id's
   * Uploads van bestanden
   * Berichtroutering
   * Dubbele bewerkingen van het Cloud naar het apparaat
   * Dubbele bewerkingen van het apparaat naar de Cloud
   * Dubbele bewerkingen
   * Taak bewerkingen
   * Directe methoden  
   * Gedistribueerde tracering (preview-versie)
   * Configuraties
   * Streams van apparaat
   * Metrische gegevens van apparaat

6. Sla de nieuwe instellingen op. 

Als u Diagnostische instellingen wilt inschakelen met Power shell, gebruikt u de volgende code:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in het geconfigureerde archief doel op de Blade **Diagnostische instellingen** . Zie [logboek gegevens verzamelen en gebruiken van uw Azure-resources](../articles/azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.
