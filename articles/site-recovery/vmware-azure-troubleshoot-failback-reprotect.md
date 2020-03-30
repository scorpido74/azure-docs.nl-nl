---
title: Failback oplossen in VMware VM-noodherstel met Azure Site Recovery
description: In dit artikel worden manieren beschreven om failback- en herbeveiligingsproblemen op te lossen tijdens VMware VM-noodherstel naar Azure met Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498100"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Problemen met failback naar on-premises vanuit Azure oplossen

In dit artikel wordt beschreven hoe u problemen oplossen wanneer u Azure VM's niet terughaalt naar uw on-premises VMware-infrastructuur, nadat u azure hebt overgelaten met [Azure Site Recovery](site-recovery-overview.md).

Failback omvat in wezen twee belangrijke stappen. Voor de eerste stap, na failover, moet u Azure VM's opnieuw beveiligen naar on-premises, zodat ze beginnen te repliceren. De tweede stap is het uitvoeren van een failover van Azure om terug te mislukken naar uw on-premises site.

## <a name="common-issues"></a>Algemene problemen

- Als u een alleen-lezen vCenter-detectie van gebruikers uitvoert en virtuele machines beschermt, slaagt beveiliging en werkt failover. Tijdens de herbescherming mislukt failover omdat de datastores niet kunnen worden gedetecteerd. Een symptoom is dat de datastores niet worden vermeld tijdens herbescherming. Als u dit probleem wilt oplossen, u de vCenter-referenties bijwerken met een geschikt account met machtigingen en de taak vervolgens opnieuw proberen.
- Wanneer u een virtuele Linux-machine niet teruglaat en deze on-premises uitvoert, u zien dat het Network Manager-pakket van de machine is verwijderd. Deze verwijdering vindt plaats omdat het pakket Netwerkbeheer wordt verwijderd wanneer de virtuele machine wordt hersteld in Azure.
- Wanneer een virtuele Linux-machine is geconfigureerd met een statisch IP-adres en niet is overgegaan naar Azure, wordt het IP-adres overgenomen van DHCP. Wanneer u niet overgaat naar on-premises, blijft de virtuele machine DHCP gebruiken om het IP-adres te verwerven. Meld u handmatig aan bij de machine en stel het IP-adres indien nodig in op een statisch adres. Een virtuele Windows-machine kan zijn statische IP-adres opnieuw verkrijgen.
- Als u de ESXi 5.5 gratis editie of de vSphere 6 Hypervisor gratis editie gebruikt, slaagt failover, maar failback slaagt niet. Als u failback wilt inschakelen, u upgraden naar de evaluatielicentie van een van beide programma's.
- Als u de configuratieserver niet vanaf de processerver bereiken, gebruikt u Telnet om de verbinding met de configuratieserver op poort 443 te controleren. U ook proberen de configuratieserver vanaf de processerver te pingen. Een processerver moet ook een hartslag hebben wanneer deze is verbonden met de configuratieserver.
- Een Windows Server 2008 R2 SP1-server die is beveiligd als een fysieke on-premises server, kan niet worden teruggezet van Azure naar een on-premises site.
- U niet in de volgende omstandigheden falen:
    - U hebt machines gemigreerd naar Azure. [Meer informatie](migrate-overview.md#what-do-we-mean-by-migration).
    - U hebt een vm verplaatst naar een andere resourcegroep.
    - U hebt de Azure VM verwijderd.
    - U hebt de beveiliging van de VM uitgeschakeld.
    - U hebt de VM handmatig gemaakt in Azure. De machine had in eerste instantie on-premises moeten zijn beveiligd en moet niet worden overgedragen aan Azure voordat deze opnieuw werd beveiligd.
    - U alleen falen bij een ESXi-host. U VMware VM's of fysieke servers niet terugsturen naar Hyper-V-hosts, fysieke machines of VMware-werkstations.


## <a name="troubleshoot-reprotection-errors"></a>Fouten in herbeveiliging oplossen

In dit gedeelte worden veelvoorkomende herbeschermingsfouten beschreven en hoe u deze corrigeren.

### <a name="error-code-95226"></a>Foutcode 95226

**Reprotect is mislukt omdat de virtuele Azure-machine de on-premises configuratieserver niet kon bereiken.**

Deze fout treedt op wanneer:

* De Azure VM kan de on-premises configuratieserver niet bereiken. De VM kan niet worden gedetecteerd en geregistreerd op de configuratieserver.
* De InMage Scout-toepassingsservice wordt niet uitgevoerd op de Azure VM na failover. De service is nodig voor communicatie met de on-premises configuratieserver.

Los dit probleem als volgt op:

* Controleer of de Azure VM met het Azure VM kan communiceren met de on-premises configuratieserver. U een site-to-site VPN instellen voor uw on-premises datacenter of een Azure ExpressRoute-verbinding configureren met private peering op het virtuele netwerk van de Azure VM.
* Als de VM kan communiceren met de on-premises configuratieserver, meldt u zich aan bij de VM. Controleer vervolgens de InMage Scout applicatie service. Als u ziet dat deze niet wordt uitgevoerd, start u de service handmatig. Controleer of het begintype van de service is ingesteld op **Automatisch**.

### <a name="error-code-78052"></a>Foutcode 78052

**Bescherming kon niet worden voltooid voor de virtuele machine.**

Dit probleem kan optreden als er al een VM met dezelfde naam op de hoofddoelserver staat waarop u niet terugwerkt.

Los dit probleem als volgt op:

* Selecteer een andere hoofddoelserver op een andere host, zodat herbescherming de machine op een andere host maakt, waarbij de namen niet botsen.
* U vMotion ook gebruiken om het hoofddoel naar een andere host te verplaatsen waar de naambotsing niet zal plaatsvinden. Als de bestaande VM een verdwaalde machine is, wijzigt u de naam zodat de nieuwe VM op dezelfde ESXi-host kan worden gemaakt.


### <a name="error-code-78093"></a>Foutcode 78093

**De VM wordt niet uitgevoerd, in een hangende staat, of niet toegankelijk.**

Los dit probleem als volgt op:

Als u een mislukte VM opnieuw wilt beveiligen, moet de Azure VM worden uitgevoerd, zodat Mobility Service zich registreert bij de on-premises configuratieserver en kan beginnen met repliceren door te communiceren met de processerver. Als de machine zich op een onjuist netwerk bevindt of niet wordt uitgevoerd (reageert of niet wordt afgesloten), kan de configuratieserver de Mobiliteitsservice op de VM niet bereiken om opnieuw te worden beschermd.

* Start de VM opnieuw op, zodat deze on-premises kan communiceren.
* Start de taak opnieuw beveiligen nadat u de virtuele Azure-machine hebt gestart.

### <a name="error-code-8061"></a>Foutcode 8061

**De datastore is niet toegankelijk via ESXi host.**

Controleer de [vereisten voor hoofddoelvereisten en ondersteunde gegevensopslag](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) voor failback.


## <a name="troubleshoot-failback-errors"></a>Failback-fouten oplossen

In deze sectie worden veelvoorkomende fouten beschreven die u tijdens failback tegenkomen.

### <a name="error-code-8038"></a>Foutcode 8038

**Kan de on-premises virtuele machine niet ter sprake brengen vanwege de fout.**

Dit probleem treedt op wanneer de on-premises VM wordt weergegeven op een host die niet genoeg geheugen heeft ingericht. 

Los dit probleem als volgt op:

* Inrichten meer geheugen op de ESXi-host.
* Bovendien u vMotion gebruiken om de VM naar een andere ESXi-host te verplaatsen die voldoende geheugen heeft om de VM op te starten.
