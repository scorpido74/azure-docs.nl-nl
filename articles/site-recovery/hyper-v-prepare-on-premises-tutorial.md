---
title: On-premises Hyper-V-servers voorbereiden op herstel na nood geval van virtuele Hyper-V-machines naar Azure
description: Meer informatie over het voorbereiden van on-premises Hyper-V-Vm's voor herstel na nood gevallen naar Azure met de Azure Site Recovery-service.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b17e42378daf51543a2664315f2971d15c288611
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813725"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>On-premises Hyper-V-servers voorbereiden op herstel na nood geval naar Azure

In dit artikel wordt beschreven hoe u uw on-premises Hyper-V-infra structuur voorbereidt wanneer u herstel na nood gevallen van Hyper-Vm's naar Azure wilt instellen met behulp van [Azure site Recovery](site-recovery-overview.md).


Dit is de tweede zelf studie in een serie die laat zien hoe u herstel na nood gevallen voor Azure instelt voor on-premises virtuele machines van Hyper-V. In de eerste zelf studie [stellen we de Azure-onderdelen](tutorial-prepare-azure.md) in die nodig zijn voor herstel na nood gevallen voor Hyper-V.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bekijk de vereisten voor Hyper-V en VMM als uw Hyper-V-hosts worden beheerd door System Center VMM.
> * Bereid VMM voor indien van toepassing.
> * Controleer Internet toegang tot Azure-locaties.
> * Bereid Vm's voor, zodat u deze na een failover naar Azure kunt gebruiken.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg het artikel in het gedeelte How to van de inhouds opgave van Site Recovery voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u Azure hebt voor bereid zoals beschreven in de [eerste zelf studie in deze serie](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Vereisten en voor waarden controleren

Zorg ervoor dat Hyper-V-hosts en-Vm's voldoen aan de vereisten.

1. [Controleer](hyper-v-azure-support-matrix.md#on-premises-servers) de vereisten voor on-premises servers.
2. [Controleer de vereisten](hyper-v-azure-support-matrix.md#replicated-vms) voor virtuele Hyper-V-machines die u naar Azure wilt repliceren.
3. Controleer het [netwerk](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)van Hyper-V-hosts. en ondersteuning voor host-en gast [opslag](hyper-v-azure-support-matrix.md#hyper-v-host-storage) voor on-premises Hyper-V-hosts.
4. Controleer wat er na failover wordt ondersteund voor [Azure-netwerken](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [-opslag](hyper-v-azure-support-matrix.md#azure-storage) en [-rekenkracht](hyper-v-azure-support-matrix.md#azure-compute-features).
5. De on-premises VM's die u voor Azure repliceert, moeten overeenstemmen met [Azure VM-vereisten](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>VMM voorbereiden (optioneel)

Als Hyper-V-hosts worden beheerd door VMM, moet u de on-premises VMM-server voorbereiden. 

- Zorg ervoor dat de VMM-server ten minste één Cloud heeft, met een of meer hostgroepen. De Hyper-V-host waarop de virtuele machines worden uitgevoerd, moeten zich in de cloud bevinden.
- De VMM-server voorbereiden voor netwerk toewijzing.

### <a name="prepare-vmm-for-network-mapping"></a>VMM voorbereiden voor netwerk toewijzing

Als u VMM gebruikt, worden [netwerk toewijzingen](site-recovery-network-mapping.md) toegewezen tussen on-PREMISES VMM VM-netwerken en virtuele netwerken van Azure. Toewijzing zorgt ervoor dat Azure-Vm's zijn verbonden met het juiste netwerk wanneer ze worden gemaakt na een failover.

Bereid VMM voor netwerk toewijzing als volgt voor:

1. Zorg ervoor dat u een [logisch VMM-netwerk](https://docs.microsoft.com/system-center/vmm/network-logical) hebt dat is gekoppeld aan de Cloud waarin de Hyper-V-hosts zich bevinden.
2. Zorg ervoor dat er een [VM-netwerk](https://docs.microsoft.com/system-center/vmm/network-virtual) is gekoppeld aan het logische netwerk.
3. Verbind de virtuele machines in VMM met het VM-netwerk.

## <a name="verify-internet-access"></a>Internet toegang controleren

1. Voor de doel einden van deze zelf studie is de eenvoudigste configuratie voor de Hyper-V-hosts en de VMM-server rechtstreeks toegang tot internet zonder gebruik te maken van een proxy. 
2. Zorg ervoor dat Hyper-V-hosts en de VMM-server, indien van toepassing, toegang hebben tot de vereiste Url's hieronder.   
3. Als u de toegang beheert op basis van het IP-adres, moet u ervoor zorgen dat:
    - Op IP-adres gebaseerde firewall regels kunnen verbinding maken met [IP-bereiken van Azure Data Center](https://www.microsoft.com/download/confirmation.aspx?id=41653)en de HTTPS-poort (443).
    - IP-adresbereiken toestaan voor de Azure-regio van uw abonnement.
    
### <a name="required-urls"></a>Vereiste Url's


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Tijdens een failover-scenario kunt u verbinding maken met uw gerepliceerde on-premises netwerk.

Als u na een failover verbinding wilt maken met Windows-Vm's met behulp van RDP, kunt u als volgt toegang verlenen:

1. Om toegang te krijgen via het internet, schakelt u voor de failover RDP in op de on-premises VM. Zorg ervoor dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.
2. Voor toegang via VPN tussen sites schakelt u RDP in op de on-premises computer. RDP moet toegestaan zijn in de **Windows Firewall** -> **Toegestane apps en onderdelen** voor **Domein en Privé**-netwerken.
   Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll**. [Meer informatie](https://support.microsoft.com/kb/3031135). Er mogen geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Als dat het geval is, kunt u zich niet aanmelden bij de virtuele machine totdat de update is voltooid.
3. Controleer na een failover **Diagnostische gegevens over opstarten** op de Windows Azure VM om een schermopname van de VM weer te geven. Als u geen verbinding kunt maken, controleer dan of de VM actief is en bekijk deze [tips voor het oplossen van problemen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Na een failover kunt u toegang krijgen tot Azure-Vm's met hetzelfde IP-adres als de gerepliceerde on-premises VM of een ander IP-adres. Meer [informatie](concepts-on-premises-to-azure-networking.md) over het instellen van IP-adres sering voor failover.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Herstel na nood geval instellen op Azure voor hyper-v-vm's](tutorial-hyper-v-to-azure.md)
> herstel[na nood geval instellen op Azure voor virtuele Hyper-v-machines in VMM-Clouds](tutorial-hyper-v-vmm-to-azure.md)
