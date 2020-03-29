---
title: Geen verbinding maken met Azure VM's omdat de RDP-poort niet is ingeschakeld in NSG | Microsoft Documenten
description: Meer informatie over het oplossen van een probleem waarbij RDP uitvalt vanwege de NSG-configuratie in de Azure-portal | Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 6c877690cf27edd73f1d828b8a1dda6f4f34e780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918169"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Kan niet op afstand verbinding maken met een VM omdat RDP-poort niet is ingeschakeld in NSG

In dit artikel wordt uitgelegd hoe u een probleem oplossen waarbij u geen verbinding maken met een virtuele azure-machine (VM) van Azure Windows omdat de RDP-poort (Remote Desktop Protocol) niet is ingeschakeld in de netwerkbeveiligingsgroep (NSG).


## <a name="symptom"></a>Symptoom

U geen RDP-verbinding maken met een VM in Azure omdat de RDP-poort niet wordt geopend in de netwerkbeveiligingsgroep.

## <a name="solution"></a>Oplossing 

Wanneer u een nieuwe virtuele machine maakt, wordt al het verkeer van internet standaard geblokkeerd. 

Voer de volgende stappen uit om de RDP-poort in te schakelen in een NSG:
1. Meld u aan bij [de Azure-portal](https://portal.azure.com).
2. Selecteer **in Virtuele machines**de VM die het probleem heeft. 
3. Selecteer **Netwerken**in **Instellingen**. 
4. Controleer **in binnenkomende poortregels**of de poort voor RDP correct is ingesteld. Het volgende is een voorbeeld van de configuratie: 

    **Prioriteit**: 300 </br>
    **Naam**: Port_3389 </br>
    **Haven(Bestemming)**: 3389 </br>
    **Protocol**: TCP  </br>
    **Bron**: </br>
    **Bestemmingen**: </br>
    **Actie**: Toestaan </br>

Als u het bron-IP-adres opgeeft, staat deze instelling verkeer alleen toe vanaf een specifiek IP-adres of een bepaald IP-adres dat verbinding maakt met de vm. Zorg ervoor dat de computer die u gebruikt om de RDP-sessie te starten binnen het bereik ligt.

Zie [netwerkbeveiligingsgroep](../../virtual-network/security-overview.md)voor meer informatie over NSG's.

> [!NOTE]
> RDP-poort 3389 is blootgesteld aan het internet. Daarom raden we u aan deze poort alleen te gebruiken voor tests. Voor productieomgevingen raden we u aan een VPN of privéverbinding te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie [Een algemene RDP-fout oplossen in Azure VM](./troubleshoot-rdp-general-error.md)als de RDP-poort al is ingeschakeld in NSG.



