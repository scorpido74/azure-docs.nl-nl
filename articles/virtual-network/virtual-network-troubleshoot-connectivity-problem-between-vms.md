---
title: Verbindingsproblemen tussen Azure VM's oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van de verbindingsproblemen tussen Azure VM's.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ab3ae45081ecc481cb90af8961174e23c86e84b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056807"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Problemen met de connectiviteit tussen virtuele Azure-machines oplossen

U verbindingsproblemen ondervinden tussen virtuele Azure-machines (VM's). In dit artikel vindt u stappen voor het oplossen van problemen om u te helpen dit probleem op te lossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptoom

Eén Azure VM kan geen verbinding maken met een andere Azure VM.

## <a name="troubleshooting-guidance"></a>Hulp bij het oplossen van problemen 

1. [Controleren of NIC verkeerd is geconfigureerd](#step-1-check-whether-nic-is-misconfigured)
2. [Controleren of netwerkverkeer wordt geblokkeerd door NSG of UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Controleren of netwerkverkeer wordt geblokkeerd door VM-firewall](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Controleren of vm-app of -service luistert op de poort](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Controleren of het probleem wordt veroorzaakt door SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Controleren of verkeer wordt geblokkeerd door ACL's voor de klassieke VM](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Controleren of het eindpunt is gemaakt voor de klassieke VM](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Proberen verbinding te maken met een VM-netwerkaandeel](#step-8-try-to-connect-to-a-vm-network-share)
9. [Inter-Vnet-connectiviteit controleren](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Volg deze stappen om het probleem op te lossen. Controleer na afloop van elke stap of het probleem is opgelost. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Stap 1: Controleren of NIC verkeerd is geconfigureerd

Volg de stappen in Hoe u de [netwerkinterface voor Azure Windows VM opnieuw instellen.](../virtual-machines/windows/reset-network-interface.md) 

Als het probleem optreedt nadat u de netwerkinterface (NIC) hebt gewijzigd, voert u de volgende stappen uit:

**Vmens met meerdere NIC's**

1. Voeg een NIC toe.
2. Los de problemen in de slechte NIC of verwijder de slechte NIC.  Voeg vervolgens de NIC opnieuw toe.

Zie [Netwerkinterfaces toevoegen aan of verwijderen van virtuele machines](virtual-network-network-interface-vm.md)voor meer informatie.

**VM MET één nic** 

- [Windows VM opnieuw implementeren](../virtual-machines/windows/redeploy-to-new-node.md)
- [Linux VM opnieuw implementeren](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Stap 2: Controleer of netwerkverkeer wordt geblokkeerd door NSG of UDR

Gebruik [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) en [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) om te bepalen of er een Network Security Group (NSG) of User-Defined Route (UDR) is die de verkeersstroom verstoort.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Stap 3: Controleren of netwerkverkeer wordt geblokkeerd door VM-firewall

Schakel de firewall uit en test het resultaat. Als het probleem is opgelost, controleert u de firewall-instellingen en schakelt u de firewall opnieuw in.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Stap 4: Controleren of vm-app of -service luistert op de poort

U een van de volgende methoden gebruiken om te controleren of de VM-app of -service op de poort luistert.

- Voer de volgende opdrachten uit om te controleren of de server op die poort luistert.

**Windows-VM**

    netstat –ano

**Linux VM**

    netstat -l

- Voer de **telnet-opdracht** uit op de virtuele machine zelf om de poort te testen. Als de test mislukt, is de toepassing of service niet geconfigureerd om op die poort te luisteren.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Stap 5: Controleer of het probleem wordt veroorzaakt door SNAT

In sommige scenario's wordt de VM geplaatst achter een load balance-oplossing die afhankelijk is van bronnen buiten Azure. Als u in deze scenario's met tussenpozen verbindingsproblemen ondervindt, kan het probleem worden veroorzaakt door [snat-poortuitputting.](../load-balancer/load-balancer-outbound-connections.md) Als u het probleem wilt oplossen, maakt u een VIP (of ILPIP voor klassiek) voor elke VM die zich achter de load balancer bevindt en veilig met NSG of ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Stap 6: Controleer of verkeer wordt geblokkeerd door ACL's voor de klassieke VM

Een toegangscontrolelijst (ACL) biedt de mogelijkheid om verkeer voor een eindpunt van een virtuele machine selectief toe te staan of te weigeren. Zie [De ACL beheren op een eindpunt voor](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)meer informatie .

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Stap 7: Controleren of het eindpunt is gemaakt voor de klassieke VM

Alle VM's die u in Azure maakt met behulp van het klassieke implementatiemodel, kunnen automatisch communiceren via een privénetwerkkanaal met andere virtuele machines in dezelfde cloudservice of virtueel netwerk. Computers op andere virtuele netwerken vereisen echter eindpunten om het binnenkomende netwerkverkeer naar een virtuele machine te leiden. Zie [Eindpunten instellen](../virtual-machines/windows/classic/setup-endpoints.md)voor meer informatie.

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Stap 8: Proberen verbinding te maken met een VM-netwerkaandeel

Als u geen verbinding maken met een VM-netwerkshare, kan het probleem worden veroorzaakt door niet-beschikbare NIC's in de VM. Zie De niet beschikbare [NIC's verwijderen](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics) als u de niet-beschikbare NIC's wilt verwijderen

### <a name="step-9-check-inter-vnet-connectivity"></a>Stap 9: Inter-Vnet-connectiviteit controleren

Gebruik [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) en [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) om te bepalen of er een NSG of UDR is die de verkeersstroom verstoort. U [hier](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)ook uw Inter-Vnet-configuratie verifiëren.

### <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.