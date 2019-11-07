---
title: Herstel na nood geval instellen voor virtuele Azure-machines na de migratie naar Azure met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u machines voorbereidt voor het instellen van herstel na nood gevallen tussen Azure-regio's na de migratie naar Azure met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 081e1db4df0d77fa600e132a5585172d44f1196a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663030"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Herstel na noodgevallen instellen voor virtuele Azure-machines na migratie naar Azure 


Volg dit artikel als u [on-premises machines naar Azure-vm's hebt gemigreerd](tutorial-migrate-on-premises-to-azure.md) met behulp van de [site Recovery](site-recovery-overview.md) -service. u wilt nu de vm's ophalen die zijn ingesteld voor herstel na nood geval naar een secundaire Azure-regio. In het artikel wordt beschreven hoe u ervoor zorgt dat de Azure VM-agent is geïnstalleerd op gemigreerde Vm's en hoe u de Site Recovery Mobility-service verwijdert die niet meer nodig is na de migratie.



## <a name="verify-migration"></a>Migratie verifiëren

Voordat u herstel na nood gevallen instelt, controleert u of de migratie is voltooid zoals verwacht. Als u de migratie wilt volt ooien, moet u na de failover de optie **volledige migratie** selecteren voor elke computer die u wilt migreren. 

## <a name="verify-the-azure-vm-agent"></a>De Azure VM-agent controleren

Op elke virtuele machine van Azure moet de [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) zijn geïnstalleerd. Als u virtuele Azure-machines wilt repliceren, wordt door Site Recovery een uitbrei ding op de agent geïnstalleerd.

- Als op de machine versie 9.7.0.0 of hoger van de Site Recovery Mobility-service wordt uitgevoerd, wordt de Azure VM-agent automatisch geïnstalleerd door de Mobility-service op Windows-Vm's. In eerdere versies van de Mobility-service moet u de agent hand matig installeren.
- Voor virtuele Linux-machines moet u de Azure VM-agent hand matig installeren. U hoeft alleen de Azure VM-agent te installeren als de Mobility-service op de gemigreerde machine v 9.6 of eerder is geïnstalleerd.


### <a name="install-the-agent-on-windows-vms"></a>De agent installeren op Windows-Vm's

Als u een versie van de Site Recovery Mobility-service hebt die ouder is dan 9.7.0.0 of als u de agent hand matig moet installeren, gaat u als volgt te werk:  

1. Zorg ervoor dat u beschikt over beheerders machtigingen voor de virtuele machine.
2. Down load het [installatie programma](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)van de VM-agent.
3. Voer het installatie bestand uit.

#### <a name="validate-the-installation"></a>De installatie valideren
Controleren of de agent is geïnstalleerd:

1. Op de virtuele machine van Azure, in de map C:\WindowsAzure\Packages, ziet u het bestand WaAppAgent. exe.
2. Klik met de rechter muisknop op het bestand en selecteer in **Eigenschappen**het tabblad **Details** .
3. Controleer of in het veld **product versie** 2.6.1198.718 of hoger wordt weer gegeven.

Meer [informatie](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) over de installatie van de agent voor Windows.

### <a name="install-the-agent-on-linux-vms"></a>De agent installeren op virtuele Linux-machines

Installeer de [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) -agent als volgt hand matig:

1. Zorg ervoor dat u beschikt over beheerders machtigingen op de computer.
2. We raden u ten zeerste aan de Linux VM-agent te installeren met behulp van een RPM-of een DEB-pakket vanuit de pakket opslagplaats van uw distributie. Alle [gewaarmerkte distributie providers](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integreren het Azure Linux-agent pakket in hun installatie kopieën en opslag plaatsen.
    - We raden u ten zeerste aan de agent alleen bij te werken via een distributie opslagplaats.
    - Het wordt niet aanbevolen om de Linux-VM-agent rechtstreeks vanuit GitHub te installeren en bij te werken.
    -  Als de meest recente agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributie ondersteuning voor instructies over het installeren ervan. 

#### <a name="validate-the-installation"></a>De installatie valideren 

1. Voer deze opdracht uit: **PS-e** om ervoor te zorgen dat de Azure-agent wordt uitgevoerd op de virtuele Linux-machine.
2. Als het proces niet wordt uitgevoerd, start u het opnieuw met behulp van de volgende opdrachten:
    - Voor Ubuntu: **service walinuxagent start**
    - Voor andere distributies: **waagent van service starten**


## <a name="uninstall-the-mobility-service"></a>De Mobility-service verwijderen

1. Verwijder de Mobility-service hand matig van de virtuele Azure-machine met behulp van een van de volgende methoden. 
    - Voor Windows kunt u in het configuratie scherm > **Program Ma's toevoegen/verwijderen** **Microsoft Azure site Recovery Mobility service/Master doel server**verwijderen. Voer vanaf een opdracht prompt met verhoogde bevoegdheid het volgende uit:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Voor Linux meldt u zich aan als hoofd gebruiker. In een Terminal gaat u naar **/user/Local/ASR**en voert u de volgende opdracht uit:
        ```
        ./uninstall.sh -Y
        ```
2. Start de VM opnieuw op voordat u de replicatie configureert.

## <a name="next-steps"></a>Volgende stappen

[Bekijk de probleem oplossing](site-recovery-extension-troubleshoot.md) voor de uitbrei ding van de site Recovery op de Azure VM-agent.
Een Azure-VM [snel repliceren](azure-to-azure-quickstart.md) naar een secundaire regio.
