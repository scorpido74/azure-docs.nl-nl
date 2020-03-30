---
title: Herstel na migratie naar Azure instellen met Azure-siteherstel
description: In dit artikel wordt beschreven hoe u machines voorbereidt op het instellen van noodherstel tussen Azure-regio's na migratie naar Azure met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74159110"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Herstel na noodgevallen instellen voor virtuele Azure-machines na migratie naar Azure 


Volg dit artikel als u [on-premises machines hebt gemigreerd naar Azure VM's](tutorial-migrate-on-premises-to-azure.md) met behulp van de [Site Recovery-service](site-recovery-overview.md) en u wilt nu de VM's instellen voor noodherstel naar een secundairAzure-gebied. In het artikel wordt beschreven hoe u ervoor zorgen dat de Azure VM-agent is geïnstalleerd op gemigreerde VM's en hoe u de service Site Recovery Mobility verwijdert die na migratie niet meer nodig is.



## <a name="verify-migration"></a>Migratie verifiëren

Voordat u herstel na noodgevallen instelt, moet u ervoor zorgen dat de migratie is voltooid zoals verwacht. Als u een migratie wilt voltooien, moet u na de failover de optie **Migratie voltooien** selecteren voor elke machine die u wilt migreren. 

## <a name="verify-the-azure-vm-agent"></a>De Azure VM-agent verifiëren

Elke Azure VM moet de [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) hebben geïnstalleerd. Als u Azure VM's wilt repliceren, installeert Site Recovery een extensie op de agent.

- Als de machine versie 9.7.0.0 of hoger van de Service Site Recovery Mobility uitvoert, wordt de Azure VM-agent automatisch geïnstalleerd door de Mobiliteitsservice op Windows VM's. Op eerdere versies van de Mobility-service installeert u de agent handmatig.
- Voor Linux VM's moet u de Azure VM-agent handmatig installeren. U hoeft de Azure VM-agent alleen te installeren als de mobiliteitsservice die op de gemigreerde machine is geïnstalleerd v9.6 of eerder is.


### <a name="install-the-agent-on-windows-vms"></a>De agent installeren op Windows VM's

Als u een versie van de mobiliteitsservice Site Recovery eerder dan 9.7.0.0 uitvoert of als u een andere behoefte hebt om de agent handmatig te installeren, gaat u als volgt te werk:  

1. Zorg ervoor dat u beheerdersmachtigingen op de VM hebt.
2. Download de [installatieprogramma VM Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Voer het installatiebestand uit.

#### <a name="validate-the-installation"></a>De installatie valideren
Ga als contact op om te controleren of de agent is geïnstalleerd:

1. Op de Azure VM ziet u in de map C:\WindowsAzure\Packages het bestand WaAppAgent.exe.
2. Klik met de rechtermuisknop op het bestand en selecteer in **Eigenschappen**het tabblad **Details.**
3. Controleer of in het veld **Productversie** 2.6.1198.718 of hoger wordt weergegeven.

[Meer informatie](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) over de installatie van agenten voor Windows.

### <a name="install-the-agent-on-linux-vms"></a>Installeer de agent op Linux VM's

Installeer de [Azure Linux VM-agent](../virtual-machines/extensions/agent-linux.md) als volgt:

1. Zorg ervoor dat u beheerdersmachtigingen op de machine hebt.
2. We raden u ten zeerste aan om de Linux VM-agent te installeren met behulp van een RPM of een DEB-pakket uit de pakketopslagplaats van uw distributie. Alle [goedgekeurde distributieproviders](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integreren het Azure Linux-agentpakket in hun afbeeldingen en repositories.
    - We raden u ten zeerste aan de agent alleen te updaten via een distributieopslagplaats.
    - We raden niet aan om de Linux VM-agent rechtstreeks van GitHub te installeren en bij te werken.
    -  Als de nieuwste agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributieondersteuning voor instructies over het installeren ervan. 

#### <a name="validate-the-installation"></a>De installatie valideren 

1. Voer deze opdracht uit: **ps -e** om ervoor te zorgen dat de Azure-agent op de Linux-VM wordt uitgevoerd.
2. Als het proces niet wordt uitgevoerd, start u het opnieuw op met de volgende opdrachten:
    - Voor Ubuntu: **service walinuxagent start**
    - Voor andere distributies: **service waagent start**


## <a name="uninstall-the-mobility-service"></a>De Mobiliteitsservice verwijderen

1. Verwijder de Mobiliteitsservice handmatig van de Azure VM met behulp van een van de volgende methoden. 
    - Voor Windows u in het Configuratiescherm > **Programma's toevoegen/verwijderen,** **Microsoft Azure Site Recovery Mobility Service/Master Target-server**verwijderen. Voer bij een opdrachtprompt met verhoogde bevoegdheid het als:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Meld u voor Linux aan als rootgebruiker. Ga in een terminal naar **/user/local/ASR**en voer de volgende opdracht uit:
        ```
        ./uninstall.sh -Y
        ```
2. Start de VM opnieuw voordat u replicatie configureert.

## <a name="next-steps"></a>Volgende stappen

[Controleer het oplossen van problemen](site-recovery-extension-troubleshoot.md) voor de extensie Siteherstel op de Azure VM-agent.
[Repliceer snel](azure-to-azure-quickstart.md) een Azure VM naar een secundaire regio.
