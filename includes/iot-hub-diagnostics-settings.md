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
ms.openlocfilehash: 3778ec2fac13aee29ce361402a535ca70fd56c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75750703"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Logboekregistratie inschakelen met diagnostische instellingen

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw IoT-hub.

2. Selecteer **Diagnostische instellingen**.

3. Selecteer **Diagnostische gegevens inschakelen**.

   ![Diagnostische gegevens inschakelen](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Geef de diagnostische instellingen een naam.

5. Kies waar u de logboeken wilt verzenden. U een combinatie van de drie opties selecteren:

   * Archiveren naar een opslagaccount
   * Streamen naar een Event Hub
   * Verzenden naar Log Analytics

6. Kies welke bewerkingen u wilt controleren en schakel logboeken in voor deze bewerkingen. De bewerkingen waarover diagnostische instellingen kunnen rapporteren, zijn:

   * Verbindingen
   * Apparaattelemetrie
   * Cloud-to-device berichten
   * Apparaatidentiteitsbewerkingen
   * Bestandsuploads
   * Berichtroutering
   * Cloud-to-device twin operations
   * Dubbele bewerkingen van device-to-cloud
   * Dubbele operaties
   * Taakbewerkingen
   * Directe methoden  
   * Gedistribueerde tracering (voorbeeld)
   * Configuraties
   * Apparaatstreams
   * Apparaatstatistieken

6. Sla de nieuwe instellingen op. 

Als u diagnostische instellingen met PowerShell wilt inschakelen, gebruikt u de volgende code:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nieuwe instellingen worden van kracht in ongeveer 10 minuten. Daarna worden logboeken weergegeven in het geconfigureerde archiefdoel op het **pagina-instellingen voor diagnostische instellingen.** Zie [Logboekgegevens verzamelen en gebruiken van uw azure-bronnen](../articles/azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostische gegevens.
