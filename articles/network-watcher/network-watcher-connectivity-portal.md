---
title: Problemen met verbindingen oplossen - Azure-portal
titleSuffix: Azure Network Watcher
description: Meer informatie over het gebruik van de verbindingsprobleemmogelijkheden van Azure Network Watcher met behulp van de Azure-portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e405a91b1ea541b4ed3328fdb3bf80ca82731c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283236"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Problemen met verbindingen met Azure Network Watcher oplossen met de Azure-portal

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [Powershell](network-watcher-connectivity-powershell.md)
> - [Azure-CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Meer informatie over het gebruik van verbindingsproblemen om te controleren of een directe TCP-verbinding van een virtuele machine naar een bepaald eindpunt kan worden gemaakt.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de volgende bronnen hebt:

* Een exemplaar van Network Watcher in de regio die u wilt oplossen van een verbinding.
* Virtuele machines om verbindingen met op te lossen.

> [!IMPORTANT]
> Het oplossen van verbindingsproblemen vereist `AzureNetworkWatcherExtension` dat de VM die u oplost, de VM-extensie heeft geïnstalleerd. Voor het installeren van de extensie op een Windows VM bezoek [Azure Network Watcher Agent virtuele machine extensie voor Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en voor Linux VM bezoek Azure Network Watcher Agent virtuele machine extensie voor [Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). De extensie is niet vereist op het eindpunt van de bestemming.

## <a name="check-connectivity-to-a-virtual-machine"></a>De verbinding met een virtuele machine controleren

In dit voorbeeld wordt de verbinding met een virtuele bestemmingsmachine via poort 80 gecontroleerd.

Navigeer naar uw netwerkwatcher en klik op **Problemen met verbinding**. Selecteer de virtuele machine om de connectiviteit van te controleren. Kies **in** de sectie Bestemming de optie **Selecteer een virtuele machine** en kies de juiste virtuele machine en poort om te testen.

Zodra u op **Controleren**klikt, wordt de verbinding tussen de virtuele machines op de opgegeven poort gecontroleerd. In het voorbeeld is de doel-VM onbereikbaar, wordt een lijst met hop weergegeven.

![Verbindingsresultaten controleren voor een virtuele machine][1]

## <a name="check-remote-endpoint-connectivity"></a>Externe eindpuntconnectiviteit controleren

Als u de verbinding en latentie naar een extern eindpunt wilt controleren, kiest u de knop **Handmatig weergeven** in de sectie **Doel,** voert u de url en de poort in en klikt u op **Controleren**.  Dit wordt gebruikt voor externe eindpunten zoals websites en opslageindpunten.

![Verbindingsresultaten voor een website controleren][2]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatiseren van pakketopnames met virtuele machinewaarschuwingen door [het maken van een waarschuwingsgeactiveerde pakketopname te](network-watcher-alert-triggered-packet-capture.md) bekijken

Find if certain traffic is allowed in or out of your VM by visiting [Check IP flow verify](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png