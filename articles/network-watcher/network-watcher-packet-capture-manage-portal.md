---
title: Pakketopnames beheren - Azure-portal
titleSuffix: Azure Network Watcher
description: Meer informatie over het beheren van de functie voor het vastleggen van pakketten van Network Watcher met behulp van de Azure-portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840824"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Pakketopnames beheren met Azure Network Watcher via de portal

Met Network Watcher-pakketopname u opnamesessies maken om het verkeer van en naar een virtuele machine bij te houden. Er zijn filters beschikbaar voor de opnamesessie om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Packet capture helpt bij het diagnosticeren van netwerkafwijkingen, zowel reactief als proactief. Andere toepassingen zijn het verzamelen van netwerkstatistieken, het verkrijgen van informatie over netwerkinbraken, het opsporen van client-servercommunicatie en nog veel meer. In staat zijn om op afstand te activeren pakket vangt, verlicht de last van het uitvoeren van een pakket vast te leggen handmatig op een gewenste virtuele machine, die kostbare tijd bespaart.

In dit artikel leert u een pakketopname te starten, te stoppen, te downloaden en te verwijderen. 

## <a name="before-you-begin"></a>Voordat u begint

Voor het vastleggen van pakketten is de volgende connectiviteit vereist:
* Uitgaande connectiviteit met een opslagaccount via poort 443.
* Inkomende en uitgaande connectiviteit naar 169.254.169.254
* Inkomende en uitgaande connectiviteit naar 168.63.129.16

Als een netwerkbeveiligingsgroep is gekoppeld aan de netwerkinterface of subnet waarin de netwerkinterface zich bevindt, moet u ervoor zorgen dat er regels bestaan die de vorige poorten toestaan. Op dezelfde manier kan het toevoegen van door de gebruiker gedefinieerde verkeersroutes aan uw netwerk de verbinding met de bovengenoemde IP's en poorten voorkomen. Zorg ervoor dat ze bereikbaar zijn. 

## <a name="start-a-packet-capture"></a>Een pakketopname starten

1. Navigeer in uw browser naar de [Azure-portal](https://portal.azure.com) en selecteer **Alle services**en selecteer **Vervolgens Netwerkwatcher** in de **sectie Netwerken**.
2. Selecteer **Pakketopname** onder **diagnostische hulpprogramma's voor netwerken**. Alle bestaande pakketopnames worden weergegeven, ongeacht hun status.
3. Selecteer **Toevoegen** om een pakketopname te maken. U waarden selecteren voor de volgende eigenschappen:
   - **Abonnement:** het abonnement waarvoor de virtuele machine moet worden gemaakt waarvoor u de pakketopname wilt maken, is binnen.
   - **Resourcegroep**: de resourcegroep van de virtuele machine.
   - **Target virtuele machine:** de virtuele machine waarvoor u de packet capture wilt maken.
   - **Naam voor pakketopname:** een naam voor het vastleggen van het pakket.
   - **Opslagaccount of -bestand:** Selecteer **Opslagaccount,** **Bestand**of beide. Als u **Bestand**selecteert, wordt de opname naar een pad binnen de virtuele machine geschreven.
   - **Lokaal bestandspad:** het lokale pad op de virtuele machine waar de pakketopname wordt opgeslagen (alleen geldig wanneer *bestand* is geselecteerd). Het pad moet een geldig pad zijn. Als u een Virtuele Linux-machine gebruikt, moet het pad beginnen met */var/captures.*
   - **Opslagaccounts:** selecteer een bestaand opslagaccount als u *het opslagaccount*hebt geselecteerd . Deze optie is alleen beschikbaar als u **Opslag**hebt geselecteerd.
   
     > [!NOTE]
     > Premium-opslagaccounts worden momenteel niet ondersteund voor het opslaan van pakketopnames.

   - **Maximale bytes per pakket**: het aantal bytes van elk pakket dat wordt vastgelegd. Als deze leeg blijft, worden alle bytes vastgelegd.
   - **Maximum aantal bytes per sessie**: het totale aantal bytes dat wordt vastgelegd. Zodra de waarde is bereikt, stopt de pakketopname.
   - **Tijdslimiet (seconden):** de tijdslimiet voordat de pakketopname wordt gestopt. De standaardinstelling is 18.000 seconden.
   - Filteren (optioneel). Selecteren **+ Filter toevoegen**
     - **Protocol:** Het protocol om te filteren voor het vastleggen van de pakketten. De beschikbare waarden zijn TCP, UDP en Any.
     - **Lokaal IP-adres:** hiermee worden de pakketopname filters weergegeven voor pakketten waarbij het lokale IP-adres overeenkomt met deze waarde.
     - **Lokale poort:** hiermee filtert u de pakketopname voor pakketten waarbij de lokale poort overeenkomt met deze waarde.
     - **Extern IP-adres:** hiermee filtert u de pakketopname voor pakketten waarbij het externe IP-adres overeenkomt met deze waarde.
     - **Externe poort:** hiermee filtert u de pakketopname voor pakketten waarbij de externe poort overeenkomt met deze waarde.
    
     > [!NOTE]
     > Poort- en IP-adreswaarden kunnen één waarde, bereik van waarden of een bereik, zoals 80-1024, voor poort zijn. U zoveel filters definiëren als u nodig hebt.

4. Selecteer **OK**.

Nadat de tijdslimiet voor het vastleggen van pakketten is verstreken, wordt de pakketopname gestopt en kan deze worden gecontroleerd. U ook handmatig een pakketopnamesessie stoppen.

> [!NOTE]
> De portal automatisch:
>  * Hiermee maakt u een netwerkwatcher in dezelfde regio als de regio waarin de virtuele machine die u hebt geselecteerd, bestaat, als de regio nog geen netwerkwatcher heeft.
>  * Hiermee voegt u de *AzureNetworkWatcherExtension* [Linux-](../virtual-machines/linux/extensions-nwa.md) of [Windows-extensie](../virtual-machines/windows/extensions-nwa.md) voor virtuele machines toe aan de virtuele machine als deze nog niet is geïnstalleerd.

## <a name="delete-a-packet-capture"></a>Een pakketopname verwijderen

1. Selecteer in de weergave packet capture de optie **...** aan de rechterkant van de pakketopname of klik met de rechtermuisknop op een bestaande pakketopname en selecteer **Verwijderen**.
2. U wordt gevraagd te bevestigen dat u de pakketopname wilt verwijderen. Selecteer **Ja**.

> [!NOTE]
> Als u een pakketopname verwijdert, wordt het vastleggensbestand in het opslagaccount of op de virtuele machine niet verwijderd.

## <a name="stop-a-packet-capture"></a>Een pakketopname stoppen

Selecteer in de weergave packet capture **...** aan de rechterkant van de pakketopname of klik met de rechtermuisknop op een bestaande pakketopname en selecteer **Stoppen**.

## <a name="download-a-packet-capture"></a>Een pakketopname downloaden

Zodra uw pakketopnamesessie is voltooid, wordt het opnamebestand geüpload naar blobopslag of naar een lokaal bestand op de virtuele machine. De opslaglocatie van de pakketopname wordt gedefinieerd tijdens het maken van de packet capture. Een handig hulpmiddel om toegang te krijgen tot vastleggen bestanden opgeslagen naar een opslagaccount is Microsoft Azure Storage Explorer, die u [downloaden.](https://storageexplorer.com/)

Als een opslagaccount is opgegeven, worden pakketopnamebestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Als u **Bestand** hebt geselecteerd toen u de opname hebt gemaakt, u het bestand bekijken of downloaden van het pad dat u op de virtuele machine hebt geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een waarschuwingspakketopname](network-watcher-alert-triggered-packet-capture.md)maken voor meer informatie over het automatiseren van pakketopnamen met waarschuwingen.
- Zie Een probleem met een probleem met het filteren van netwerkverkeer voor [virtuele machines diagnosticeren](diagnose-vm-network-traffic-filtering-problem.md)als u wilt bepalen of specifiek verkeer in of uit een virtuele machine is toegestaan.
