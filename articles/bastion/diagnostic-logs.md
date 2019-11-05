---
title: De diagnostische logboeken van Azure Bastion inschakelen en gebruiken | Microsoft Docs
description: In dit artikel vindt u informatie over het inschakelen en gebruiken van Diagnostische logboeken van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 8ae421b48f3cfa8055f636052c990f99e0c775b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512403"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Bastion Diagnostische logboeken inschakelen en gebruiken

Wanneer gebruikers verbinding maken met werk belastingen met behulp van Azure Bastion, kan Bastion de diagnostische gegevens van de externe sessies registreren. Vervolgens kunt u de diagnostische gegevens gebruiken om te zien welke gebruikers zijn verbonden met welke werk belastingen, op welk tijdstip, waar en andere relevante logboek gegevens. Als u de diagnostische gegevens wilt gebruiken, moet u Diagnostische logboeken inschakelen op Azure Bastion. Dit artikel helpt u bij het inschakelen van Diagnostische logboeken en het weer geven van de logboeken.

## <a name="enable"></a>Het diagnostische logboek inschakelen

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Azure Bastion-resource en selecteer **Diagnostische instellingen** op de pagina Azure Bastion.

   ![Diagnostische instellingen](./media/diagnostic-logs/1diagnostics-settings.png)
2. Selecteer **instellingen voor diagnostische gegevens**en selecteer vervolgens **Diagnostische instelling toevoegen** om een doel voor de Logboeken toe te voegen.

   ![diagnostische instelling toevoegen](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Selecteer op de pagina **Diagnostische instellingen** het type opslag account dat moet worden gebruikt voor het opslaan van Diagnostische logboeken.

   ![opslag locatie selecteren](./media/diagnostic-logs/3add-storage-account.png)
4. Wanneer u de instellingen hebt voltooid, ziet deze er ongeveer uit als in dit voor beeld:

   ![voorbeeld instellingen](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>Diagnostische logboek weer geven

Voor toegang tot uw Diagnostische logboeken kunt u rechtstreeks het opslag account gebruiken dat u hebt opgegeven tijdens het inschakelen van de diagnostische instellingen.

1. Navigeer naar de resource van uw opslag account en vervolgens naar **containers**. U ziet de BLOB **Insights-logs-bastionauditlogs** die u hebt gemaakt in de BLOB-container van uw opslag account.

   ![Diagnostische instellingen](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Terwijl u in de container navigeert, ziet u verschillende mappen in uw blog. Deze mappen geven de resource hiërarchie voor uw Azure Bastion-resource aan.

   ![diagnostische instelling toevoegen](./media/diagnostic-logs/2-resource-h.png)
3. Ga naar de volledige hiërarchie van uw Azure Bastion-resource waarvan u de diagnostische logboeken wilt openen of weer geven. De ' y = ', ' = ', 'd = ', ' h = ' en ' = ' geven het jaar, de maand, de dag, het uur en het aantal minuten voor de diagnostische logboeken aan.

   ![opslag locatie selecteren](./media/diagnostic-logs/3-resource-location.png)
4. Zoek het JSON-bestand dat is gemaakt door Azure Bastion dat de diagnostische logboek gegevens bevat voor de tijds periode naar.

5. Down load het JSON-bestand van uw opslag-BLOB-container. Hieronder ziet u een voor beeld van een vermelding uit het JSON-bestand:

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

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
