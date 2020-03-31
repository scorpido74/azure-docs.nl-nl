---
title: Updates en componentupgrades in Azure Site Recovery
description: Biedt een overzicht van updates van de Azure Site Recovery-service en componentupgrades.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257821"
---
# <a name="service-updates-in-site-recovery"></a>Service-updates in Site recovery

In dit artikel vindt u een overzicht van [Azure Site Recovery-updates](site-recovery-overview.md) en wordt beschreven hoe u siteherstelcomponenten upgraden.

Site Recovery publiceert regelmatig service-updates. Updates bevatten nieuwe functies, ondersteuningsverbeteringen, componentupdates en bugfixes. Om te profiteren van de nieuwste functies en oplossingen, raden we u aan de nieuwste versies van Site Recovery-componenten uit te voeren. 
 
 
## <a name="updates-support"></a>Ondersteuning voor updates

### <a name="support-statement-for-azure-site-recovery"></a>Ondersteuningsinstructie voor Azure Site Recovery

We raden u aan altijd te upgraden naar de nieuwste componentversies:

**Bij elke nieuwe versie 'N' van een Azure Site Recovery-component die is uitgebracht, worden alle versies onder 'N-4' als buiten de ondersteuning beschouwd.** 

> [!IMPORTANT]
> Officiële ondersteuning is voor het upgraden van > N-4 versie naar N-versie. Als u bijvoorbeeld n-6 uitvoert, moet u eerst upgraden naar N-4 en vervolgens upgraden naar N.


### <a name="links-to-currently-supported-update-rollups"></a>Koppelingen naar momenteel ondersteunde update-rollups

 Bekijk de laatste update rollup (versie N) in [dit artikel](site-recovery-whats-new.md). Vergeet niet dat Site Recovery ondersteuning biedt voor N-4-versies.



## <a name="component-expiry"></a>Het verlopen van onderdelen

Site recovery waarschuwt u over verlopen onderdelen (of het naderen van het verstrijken) per e-mail (als u zich hebt geabonneerd op e-mailmeldingen) of op het dashboard van de kluis in de portal.

- Bovendien verschijnt er in de infrastructuurweergave voor uw scenario in de portal een knop **Update** beschikbaar naast het onderdeel wanneer er updates beschikbaar zijn. Deze knop verwijst u door naar een link voor het downloaden van de nieuwste componentversie.
-  Meldingen van vaults-dashboard zijn niet beschikbaar als u Hyper-V VM's repliceert. 

E-mails meldingen worden verzonden als volgt.

**Tijd** | **Frequentie**
--- | ---
60 dagen voor het verstrijken van de component | Eenmaal tweewekelijks
Volgende 53 dagen | Eén keer per week
Laatste 7 dagen | Eenmaal per dag
Na afloop | Eenmaal tweewekelijks


### <a name="upgrading-outside-official-support"></a>Upgraden van externe officiële ondersteuning

Als het verschil tussen uw componentversie en de nieuwste versie groter is dan vier, wordt dit als niet-ondersteuning beschouwd. Upgrade in dit geval als volgt: 

1. Upgrade het momenteel geïnstalleerde onderdeel naar uw huidige versie plus vier. Als uw versie bijvoorbeeld 9.16 is, u upgraden naar 9,20.
2. Upgrade vervolgens naar de volgende compatibele versie. Dus in ons voorbeeld, na het upgraden van 9,16 naar 9,20, upgrade naar 9,24. 

Volg hetzelfde proces voor alle relevante componenten.

### <a name="support-for-latest-operating-systemskernels"></a>Ondersteuning voor de nieuwste besturingssystemen/kernels

> [!NOTE]
> Als u een onderhoudsvenster hebt gepland en er een reboot in is opgenomen, raden we u aan eerst siteherstelcomponenten te upgraden en vervolgens verder te gaan met de rest van de geplande activiteiten in het onderhoudsvenster.

1. Controleer voordat u de versies van het besturingssysteem/de kernel upgradet of de doelversie wordt ondersteund Site Recovery. 

    - [Azure VM-ondersteuning.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - [Ondersteuning voor VMware/fysieke servers](vmware-physical-azure-support-matrix.md#replicated-machines)
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) ondersteuning.
2. Bekijk [beschikbare updates](site-recovery-whats-new.md) om erachter te komen wat u wilt upgraden.
3. Upgrade naar de nieuwste siteherstelversie.
4. Upgrade het besturingssysteem/kernel naar de vereiste versies.
5. Opnieuw opstarten.


Dit proces zorgt ervoor dat het besturingssysteem/de nieuwste versie wordt geüpgraded naar de nieuwste versie en dat de nieuwste wijzigingen in siteherstel die nodig zijn om de nieuwe versie te ondersteunen, op de machine worden geladen.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Herstel na noodgevallen van virtuele Azure-machines naar Azure

In dit scenario raden we u ten zeerste [aan automatische updates in te schakelen.](azure-to-azure-autoupdate.md) U Site Recovery toestaan om updates als volgt te beheren:

- Tijdens het proces van replicatie inschakelen.
- Door de instellingen voor extensie-updates in de kluis in te stellen.

Als u updates handmatig wilt beheren, gaat u als volgt te werk:

1. Klik in de kluis > **gerepliceerde items**op deze melding boven aan het scherm: 
    
    **Er is een update van de nieuwe replicatieagent voor siteherstel beschikbaar. Klik om ->te installeren**

4. Selecteer de VM's waarvoor u de update wilt toepassen en klik op **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/physical server disaster recovery to Azure

1. Op basis van uw huidige versie en de [ondersteuningsverklaring](#support-statement-for-azure-site-recovery)installeert u de update eerst op de on-premises configuratieserver met behulp van [deze instructies.](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server) 
2. Als u processervers hebt, werkt u deze vervolgens bij met behulp van [deze instructies.](vmware-azure-manage-process-server.md#upgrade-a-process-server)
3. Raadpleeg [dit](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artikel om de mobiliteitsagent op elke beveiligde machine bij te werken.

### <a name="reboot-after-mobility-service-upgrade"></a>Opnieuw opstarten na upgrade van mobility-service

Een reboot wordt aanbevolen na elke upgrade van de Mobility-service, om ervoor te zorgen dat alle laatste wijzigingen op de bronmachine worden geladen.

Een reboot is niet verplicht, tenzij het verschil tussen de agentversie tijdens de laatste reboot en de huidige versie groter is dan vier.

Het voorbeeld in de tabel laat zien hoe dit werkt.

|**Agent-versie (laatste reboot)** | **Upgrade naar** | **Verplichte reboot?**|
|---------|---------|---------|
|9.16 |  9.18 | Niet verplicht|
|9.16 | 9.19 | Niet verplicht|
| 9.16 | 9.20 | Niet verplicht
 | 9.16 | 9.21 | Verplicht.<br/><br/> Upgrade naar 9,20 en start opnieuw op voordat je een upgrade uitvoert naar 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Herstel na noodgevallen van virtuele Hyper-V-machines naar Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Tussen een Hyper-V-site en Azure

1. Download de update voor de Microsoft Azure Site Recovery Provider.
2. Installeer de provider op elke Hyper-V-server die is geregistreerd in Site recovery. Als u een cluster uitvoert, u upgraden naar alle clusterknooppunten.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Tussen een on-premises VMM-site en Azure
1. Download de update voor de Microsoft Azure Site Recovery Provider.
2. Installeer de provider op de VMM-server. Als VMM in een cluster wordt geïmplementeerd, installeert u de provider op alle clusterknooppunten.
3. Installeer de nieuwste Microsoft Azure Recovery Services-agent op alle Hyper-V-hosts of clusterknooppunten.


## <a name="between-two-on-premises-vmm-sites"></a>Tussen twee on-premises VMM-sites
1. Download de nieuwste update voor de Microsoft Azure Site Recovery Provider.
2. Installeer de nieuwste provider op de VMM-server die de secundaire herstelsite beheert. Als VMM in een cluster wordt geïmplementeerd, installeert u de provider op alle clusterknooppunten.
3. Nadat de herstelsite is bijgewerkt, installeert u de provider op de VMM-server die de primaire site beheert.

## <a name="next-steps"></a>Volgende stappen

Volg onze [Azure Updates-pagina](https://azure.microsoft.com/updates/?product=site-recovery) om nieuwe updates en releases bij te houden.
