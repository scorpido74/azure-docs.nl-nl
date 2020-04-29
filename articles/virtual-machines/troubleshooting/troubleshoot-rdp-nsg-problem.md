---
title: Kan geen verbinding maken met virtuele machines van Azure omdat de RDP-poort niet is ingeschakeld in NSG | Microsoft Docs
description: Meer informatie over het oplossen van een probleem waarbij RDP mislukt vanwege de configuratie van NSG in de Azure Portal | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918169"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>Kan niet extern verbinding maken met een virtuele machine omdat de RDP-poort niet is ingeschakeld in NSG

In dit artikel wordt uitgelegd hoe u een probleem oplost waarbij u geen verbinding kunt maken met een virtuele machine van Azure Windows (VM), omdat de Remote Desktop Protocol RDP-poort niet is ingeschakeld in de netwerk beveiligings groep (NSG).


## <a name="symptom"></a>Symptoom

U kunt geen RDP-verbinding maken met een virtuele machine in azure omdat de RDP-poort niet is geopend in de netwerk beveiligings groep.

## <a name="solution"></a>Oplossing 

Wanneer u een nieuwe virtuele machine maakt, wordt alle verkeer van Internet standaard geblokkeerd. 

Voer de volgende stappen uit om de RDP-poort in te scha kelen in een NSG:
1. Meld u aan bij [de Azure Portal](https://portal.azure.com).
2. Selecteer in **virtual machines**de virtuele machine waarop het probleem zich voordoet. 
3. Selecteer in **instellingen**de optie **netwerken**. 
4. Controleer in **Binnenkomende poort regels**of de poort voor RDP juist is ingesteld. Hier volgt een voor beeld van de configuratie: 

    **Prioriteit**: 300 </br>
    **Naam**: Port_3389 </br>
    **Poort (bestemming)**: 3389 </br>
    **Protocol**: TCP  </br>
    **Bron**: any </br>
    **Doelen**: any </br>
    **Actie**: toestaan </br>

Als u het bron-IP-adres opgeeft, staat deze instelling alleen verkeer toe van een specifiek IP-adres of bereik van IP-adressen om verbinding te maken met de virtuele machine. Zorg ervoor dat de computer die u gebruikt om de RDP-sessie te starten, binnen het bereik valt.

Zie [netwerk beveiligings groep](../../virtual-network/security-overview.md)voor meer informatie over nsg's.

> [!NOTE]
> RDP-poort 3389 wordt blootgesteld aan Internet. Daarom wordt u aangeraden deze poort alleen te gebruiken voor het testen. Voor productie omgevingen wordt u aangeraden een VPN-of particuliere verbinding te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Als de RDP-poort al is ingeschakeld in NSG, raadpleegt u [problemen oplossen met een algemene RDP-fout in azure VM](./troubleshoot-rdp-general-error.md).



