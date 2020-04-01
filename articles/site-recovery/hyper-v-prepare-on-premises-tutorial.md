---
title: Voorbereiden op noodherstel van Hyper V VM's naar Azure met Azure Site Recovery
description: Meer informatie over het voorbereiden van on-premises Hyper-V VM's voor noodherstel naar Azure met Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b36e4688ebd6e929a56869a6bb191d98b2f1f432
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239838"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>On-premises Hyper-V-servers voorbereiden op herstel na noodgeval in Azure

In dit artikel wordt beschreven hoe u uw on-premises Hyper-V-infrastructuur voorbereiden wanneer u noodherstel van Hyper-VM's naar Azure wilt instellen met Azure [Site Recovery](site-recovery-overview.md).


Dit is de tweede zelfstudie in een reeks die u laat zien hoe u disaster recovery instellen op Azure voor on-premises Hyper-VVm's. In de eerste zelfstudie hebben we [de Azure-componenten ingesteld](tutorial-prepare-azure.md) die nodig zijn voor hyper-v-noodherstel.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bekijk de vereisten voor Hyper V en vmm-vereisten als uw Hyper-V-hosts worden beheerd door System Center VMM.
> * Bereid VMM voor indien van toepassing.
> * Controleer de toegang tot internet tot Azure-locaties.
> * Bereid VM's zo voor dat u ze openen na een fail-over naar Azure.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Bekijk het artikel in de sectie Hoe naar de inhoudsopgave van de site herstel voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u Azure hebt voorbereid zoals beschreven in de [eerste zelfstudie in deze serie.](tutorial-prepare-azure.md)

## <a name="review-requirements-and-prerequisites"></a>Vereisten en vereisten herzien

Zorg ervoor dat Hyper-V-hosts en VM's voldoen aan de vereisten.

1. [Controleer](hyper-v-azure-support-matrix.md#on-premises-servers) de vereisten voor on-premises servers.
2. [Controleer de vereisten](hyper-v-azure-support-matrix.md#replicated-vms) voor Hyper-V VM's die u wilt repliceren naar Azure.
3. Controleer Hyper-V host [networking](hyper-v-azure-support-matrix.md#hyper-v-network-configuration); en ondersteuning voor host- en [gastopslag](hyper-v-azure-support-matrix.md#hyper-v-host-storage) voor on-premises Hyper-V-hosts.
4. Controleer wat er na failover wordt ondersteund voor [Azure-netwerken](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [-opslag](hyper-v-azure-support-matrix.md#azure-storage) en [-rekenkracht](hyper-v-azure-support-matrix.md#azure-compute-features).
5. De on-premises VM's die u voor Azure repliceert, moeten overeenstemmen met [Azure VM-vereisten](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>VMM voorbereiden (optioneel)

Als Hyper-V-hosts worden beheerd door VMM, moet u de on-premises VMM-server voorbereiden. 

- Zorg ervoor dat de VMM-server ten minste één cloud heeft, met een of meer hostgroepen. De Hyper-V-host waarop VM's worden uitgevoerd, moet zich in de cloud bevinden.
- Bereid de VMM-server voor op netwerktoewijzing.

### <a name="prepare-vmm-for-network-mapping"></a>VMM voorbereiden op netwerktoewijzing

Als u VMM gebruikt, worden [netwerktoewijzingskaarten](site-recovery-network-mapping.md) tussen on-premises Vm VM-netwerken en virtuele Azure-netwerken toegewezen. Toewijzing zorgt ervoor dat Azure VM's zijn verbonden met het juiste netwerk wanneer ze worden gemaakt na een failover.

VMM als volgt voorbereiden op netwerktoewijzing:

1. Zorg ervoor dat u een [VMM-logisch netwerk hebt](https://docs.microsoft.com/system-center/vmm/network-logical) dat is gekoppeld aan de cloud waarin de Hyper-V-hosts zich bevinden.
2. Zorg ervoor dat u een [VM-netwerk](https://docs.microsoft.com/system-center/vmm/network-virtual) hebt gekoppeld aan het logische netwerk.
3. Sluit in VMM de VM's aan op het VM-netwerk.

## <a name="verify-internet-access"></a>Internettoegang verifiëren

1. Voor de toepassing van de zelfstudie is de eenvoudigste configuratie dat de Hyper-V-hosts en VMM-server directe toegang tot het internet hebben zonder een proxy te gebruiken. 
2. Zorg ervoor dat Hyper-V hosts, en de VMM-server indien relevant, toegang hebben tot de vereiste URL's hieronder.   
3. Als u de toegang per IP-adres regelt, moet u ervoor zorgen dat:
    - Ip-adresgebaseerde firewallregels kunnen verbinding maken met [IP-datacenter-IP-bereiken](https://www.microsoft.com/download/confirmation.aspx?id=41653)en de HTTPS-poort (443).
    - Sta IP-adresbereiken toe voor het Azure-gebied van uw abonnement.
    
### <a name="required-urls"></a>Vereiste URL's


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Tijdens een failoverscenario wilt u mogelijk verbinding maken met uw gerepliceerde on-premises netwerk.

Als u verbinding wilt maken met Windows VM's via RDP na een failover, geeft u de toegang als volgt toe:

1. Om toegang te krijgen via het internet, schakelt u voor de failover RDP in op de on-premises VM. Zorg ervoor dat TCP- en UDP-regels worden toegevoegd voor het **openbare** profiel en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.
2. Voor toegang via VPN tussen sites schakelt u RDP in op de on-premises computer. RDP moet worden toegestaan in de **Windows Firewall** -> **Toegestane apps en functies** voor **domein- en privénetwerken.**
   Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll**. [Meer informatie](https://support.microsoft.com/kb/3031135). Er mogen geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Als dat zo is, u zich pas aanmelden bij de virtuele machine als de update is voltooid.
3. Controleer na een failover **Diagnostische gegevens over opstarten** op de Windows Azure VM om een schermopname van de VM weer te geven. Als u geen verbinding kunt maken, controleer dan of de VM actief is en bekijk deze [tips voor het oplossen van problemen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Na failover hebt u toegang tot Azure VM's met hetzelfde IP-adres als de gerepliceerde on-premises VM of een ander IP-adres. [Meer informatie](concepts-on-premises-to-azure-networking.md) over het instellen van IP-adressering voor failover.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Disaster recovery instellen voor Azure voor Hyper V VM's](tutorial-hyper-v-to-azure.md)
> [Noodherstel instellen voor Azure voor Hyper V VM's in VMM-clouds](tutorial-hyper-v-vmm-to-azure.md)
