---
title: Kan een virtueel netwerk in Azure niet verwijderen | Microsoft Documenten
description: Meer informatie over het oplossen van het probleem waarbij u een virtueel netwerk in Azure niet verwijderen.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056925"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Probleemoplossing: kan geen virtueel netwerk verwijderen in Azure

Mogelijk ontvangt u fouten wanneer u een virtueel netwerk in Microsoft Azure probeert te verwijderen. In dit artikel vindt u stappen voor het oplossen van problemen om u te helpen dit probleem op te lossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Hulp bij het oplossen van problemen 

1. [Controleer of een virtuele netwerkgateway wordt uitgevoerd in het virtuele netwerk.](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network)
2. [Controleer of een toepassingsgateway wordt uitgevoerd in het virtuele netwerk.](#check-whether-an-application-gateway-is-running-in-the-virtual-network)
3. [Controleer of Azure Active Directory Domain Service is ingeschakeld in het virtuele netwerk](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Controleer of het virtuele netwerk is verbonden met andere bronnen.](#check-whether-the-virtual-network-is-connected-to-other-resource)
5. [Controleer of een virtuele machine nog steeds actief is in het virtuele netwerk.](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network)
6. [Controleer of het virtuele netwerk vastzit in migratie.](#check-whether-the-virtual-network-is-stuck-in-migration)

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Controleren of een virtuele netwerkgateway wordt uitgevoerd in het virtuele netwerk

Als u het virtuele netwerk wilt verwijderen, moet u eerst de virtuele netwerkgateway verwijderen.

Ga voor klassieke virtuele netwerken naar de **overzichtspagina** van het klassieke virtuele netwerk in de Azure-portal. Als de gateway in het virtuele netwerk wordt uitgevoerd, ziet u in de sectie **VPN-verbindingen** het IP-adres van de gateway. 

![Controleren of gateway wordt uitgevoerd](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

Ga voor virtuele netwerken naar de **overzichtspagina** van het virtuele netwerk. Schakel **Verbonden apparaten in** voor de virtuele netwerkgateway.

![Controleer het aangesloten apparaat](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Voordat u de gateway verwijderen, verwijdert u eerst **verbindingsobjecten** in de gateway. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Controleren of een toepassingsgateway wordt uitgevoerd in het virtuele netwerk

Ga naar de **overzichtspagina** van het virtuele netwerk. Controleer de **verbonden apparaten** voor de toepassingsgateway.

![Controleer het aangesloten apparaat](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Als er een toepassingsgateway is, moet u deze verwijderen voordat u het virtuele netwerk verwijderen.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Controleren of Azure Active Directory Domain Service is ingeschakeld in het virtuele netwerk

Als de Active Directory Domain Service is ingeschakeld en verbonden is met het virtuele netwerk, u dit virtuele netwerk niet verwijderen. 

![Controleer het aangesloten apparaat](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Zie Azure Active [Directory Domain Services uitschakelen met de Azure-portal](../active-directory-domain-services/delete-aadds.md)als u de service wilt uitschakelen.

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Controleren of het virtuele netwerk is verbonden met andere bronnen

Controleer op Circuit Links, verbindingen en virtuele netwerkpeeringen. Elk van deze kan leiden tot een virtuele netwerk verwijdering te mislukken. 

De aanbevolen verwijderingsvolgorde is als volgt:

1. Gatewayverbindingen
2. Gateways
3. Ips
4. Virtuele netwerkpeeringen
5. App-serviceomgeving (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Controleren of een virtuele machine nog steeds actief is in het virtuele netwerk

Zorg ervoor dat er geen virtuele machine in het virtuele netwerk is.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Controleren of het virtuele netwerk vastzit in migratie

Als het virtuele netwerk vastzit in een migratiestatus, kan het niet worden verwijderd. Voer de volgende opdracht uit om de migratie af te breken en verwijder vervolgens het virtuele netwerk.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Volgende stappen

- [Virtueel Azure-netwerk](virtual-networks-overview.md)
- [Veelgestelde vragen over virtuele Azure-netwerken (FAQ)](virtual-networks-faq.md)
