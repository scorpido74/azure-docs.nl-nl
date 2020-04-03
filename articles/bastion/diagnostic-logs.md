---
title: Diagnostische logboeken van Azure Bastion inschakelen en ermee werken
description: In dit artikel leest u hoe u diagnostische logboeken van Azure Bastion inschakelt en ermee werkt.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 97f0cdb1e93ef2ad06d2daa04b2f4893fd5dfac2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619265"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Diagnostische logboeken van Bastion inschakelen en ermee werken

Als gebruikers verbinding maken met workloads met Azure Bastion, kan Bastion diagnoses van de externe sessies registreren. U vervolgens de diagnose gebruiken om te bekijken welke gebruikers zijn verbonden met welke workloads, op welk tijdstip, van waar en andere dergelijke relevante logboekinformatie. Als u de diagnose wilt gebruiken, moet u diagnostische logboeken op Azure Bastion inschakelen. Met dit artikel u diagnostische logboeken inschakelen en vervolgens de logboeken bekijken.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Het diagnoselogboek inschakelen

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw Azure Bastion-bron en selecteer **Diagnostische instellingen** op de Azure Bastion-pagina.

   ![diagnose-instellingen](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selecteer **Diagnostische instellingen**en selecteer **+Diagnostische instelling toevoegen** om een bestemming voor de logboeken toe te voegen.

   ![diagnostische instelling toevoegen](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Selecteer **op** de pagina Diagnostische instellingen het type opslagaccount dat moet worden gebruikt voor het opslaan van diagnostische logboeken.

   ![opslaglocatie selecteren](./media/diagnostic-logs/3add-storage-account.png)
4. Wanneer u de instellingen voltooit, ziet deze er hetzelfde uit als dit voorbeeld:

   ![voorbeeldinstellingen](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Diagnoselogboek weergeven

Als u toegang wilt krijgen tot uw diagnostische logboeken, u direct het opgegeven opslagaccount gebruiken terwijl u de diagnose-instellingen inschakelt.

1. Navigeer naar de bron van uw opslagaccount en vervolgens naar **Containers.** U ziet de **blob insights-logs-bastionauditlogs** die zijn gemaakt in de blobcontainer voor uw opslagaccount.

   ![diagnose-instellingen](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Terwijl je naar de container navigeert, zie je verschillende mappen in je blog. Deze mappen geven de resourcehiërarchie voor uw Azure Bastion-bron aan.

   ![diagnostische instelling toevoegen](./media/diagnostic-logs/2-resource-h.png)
3. Navigeer naar de volledige hiërarchie van uw Azure Bastion-bron waarvan u de diagnostische gegevens wilt openen/weergeven. De 'y=', 'm=', 'd=', 'h=' en 'm=' geven respectievelijk het jaar, de maand, de dag, het uur en de minuut aan voor de diagnostische logboeken.

   ![opslaglocatie selecteren](./media/diagnostic-logs/3-resource-location.png)
4. Zoek het json-bestand dat is gemaakt door Azure Bastion en dat de gegevens van het diagnostisch logboek bevat voor de periode waarop u naartoe is genavigeerd.

5. Download het json-bestand uit uw opslagblobcontainer. Een voorbeeld vermelding uit het json-bestand wordt hieronder weergegeven voor referentie:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Volgende stappen

Lees de [Bastion FAQ](bastion-faq.md).
