---
title: Problemen met Azure Site Recovery agents oplossen | Microsoft Docs '
description: Bevat informatie over symptomen, oorzaken en oplossingen van Azure Site Recovery agent fouten.
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: carmonm
ms.openlocfilehash: 0de5a9843b8029c1e1926ae296f43fc95b48106c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930131"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Problemen met de Azure Site Recovery-agent oplossen

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van Azure Site Recovery fouten met betrekking tot de VM-agent en-extensie.


## <a name="azure-site-recovery-extension-time-out"></a>Time-out van Azure Site Recovery extensie  

Fout bericht: er is een time-out opgetreden bij het uitvoeren van de taak tijdens het bijhouden van de extensie bewerking die moet worden gestart<br>
Fout code: "151076"

 Azure Site Recovery een uitbrei ding op de virtuele machine installeren als onderdeel van de functie voor het inschakelen van beveiliging. Een van de volgende omstandigheden kan verhinderen dat de beveiliging wordt geactiveerd en dat de taak mislukt. Voer de volgende stappen voor probleem oplossing uit en voer de bewerking opnieuw uit:

**Oorzaak 1: [de agent is geïnstalleerd in de VM, maar is niet reageren (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Oorzaak 3: [de site Recovery extensie kan niet worden bijgewerkt of geladen](#the-site-recovery-extension-fails-to-update-or-load)**  

Fout bericht: de bewerking voor de extensie van een vorige site Recovery neemt meer tijd in beslag dan verwacht.<br>
Fout code: "150066"<br>

**Oorzaak 1: [de agent is geïnstalleerd in de VM, maar is niet reageren (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Oorzaak 3: [de status van de site Recovery extensie is onjuist](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>De beveiliging is mislukt omdat de VM-agent niet reageert

Fout bericht: er is een time-out opgetreden bij het uitvoeren van de taak tijdens het bijhouden van de extensie bewerking die moet worden gestart.<br>
Fout code: "151099"<br>

Deze fout kan optreden als de Azure-gast agent op de virtuele machine niet de status gereed heeft.
U kunt de status van de Azure-gast agent controleren in [Azure Portal](https://portal.azure.com/). Ga naar de virtuele machine die u wilt beveiligen en controleer de status in ' VM >-Instellingen > Eigenschappen > agent status '. De meeste tijd wordt de status van de agent gereed nadat de virtuele machine opnieuw is opgestart. Als opnieuw opstarten echter niet mogelijk is of als u nog steeds het probleem ondervindt, voert u de volgende stappen voor probleem oplossing uit.

**Oorzaak 1: [de agent is geïnstalleerd in de VM, maar is niet reageren (voor Windows-vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Oorzaak 2: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Fout bericht: er is een time-out opgetreden bij het uitvoeren van de taak tijdens het bijhouden van de extensie bewerking die moet worden gestart.<br>
Fout code: "151095"<br>

Dit gebeurt wanneer de agent versie op de Linux-computer oud is. Voer de volgende stap voor het oplossen van problemen uit.<br>
  **Oorzaak 1: [de agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Oorzaken en oplossingen

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)

#### <a name="solution"></a>Oplossing
De VM-agent is mogelijk beschadigd of de service is gestopt. Als u de VM-agent opnieuw installeert, krijgt u de meest recente versie. Het helpt ook om communicatie met de service opnieuw te starten.

1. Bepaal of de service ' Windows Azure Guest agent ' wordt uitgevoerd in de VM-Services (Services. msc). Probeer de Windows Azure Guest Agent-service opnieuw te starten.    
2. Als de Windows Azure Guest Agent-service niet wordt weer gegeven in Services, gaat u in het configuratie scherm naar **Program ma's en onderdelen** om te bepalen of de Windows Guest Agent-service is geïnstalleerd.
4. Als de Windows Azure-gast agent wordt weer gegeven in **Program ma's en onderdelen**, verwijdert u de Windows-gast agent.
5. Down load en installeer de [nieuwste versie van de agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U moet over beheerders rechten beschikken om de installatie te volt ooien.
6. Controleer of de services van Windows Azure Guest agent worden weer gegeven in Services.
7. Start de beveiligings taak opnieuw.

Controleer ook of [Microsoft .NET 4,5 is geïnstalleerd](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) in de VM. .NET 4,5 is vereist voor de VM-agent om te communiceren met de service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)

#### <a name="solution"></a>Oplossing
De meeste fout-en extensie-gerelateerde storingen voor Linux-Vm's worden veroorzaakt door problemen die van invloed zijn op een verouderde VM-agent. Volg deze algemene richt lijnen om dit probleem op te lossen:

1. Volg de instructies voor [het bijwerken van de Linux VM-agent](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > We *raden u ten zeerste* aan de agent alleen bij te werken via een distributie opslagplaats. Het is niet raadzaam de agent code rechtstreeks vanuit GitHub te downloaden en bij te werken. Als de meest recente agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributie ondersteuning voor instructies over het installeren ervan. Als u de meest recente agent wilt controleren, gaat u naar de pagina van de [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) in de GitHub-opslag plaats.

2. Zorg ervoor dat de Azure-agent op de virtuele machine wordt uitgevoerd door de volgende opdracht uit te voeren: `ps -e`

   Als het proces niet wordt uitgevoerd, start u het opnieuw met behulp van de volgende opdrachten:

   * Voor Ubuntu: `service walinuxagent start`
   * Voor andere distributies: `service waagent start`

3. [Configureer de agent voor automatisch opnieuw opstarten](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Schakel de beveiliging van de virtuele machine in.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>De uitbrei ding van de Site Recovery kan niet worden bijgewerkt of geladen
Als de status van de uitbrei dingen ' empty ', ' geen loopvlak ' of overgangen is.

#### <a name="solution"></a>Oplossing

Verwijder de extensie en start de bewerking opnieuw.

De uitbrei ding verwijderen:

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de virtuele machine waarop een back-up is mislukt.
2. Selecteer **instellingen**.
3. Selecteer **Extensies**.
4. Selecteer **site Recovery extensie**.
5. Selecteer **Verwijderen**.

Als de VMSnapshot-extensie niet wordt weer gegeven in de Azure Portal, werkt u voor de Linux-VM naar de [Azure Linux-agent](../virtual-machines/linux/update-agent.md)en voert u de beveiliging uit. 

Als u deze stappen uitvoert, wordt de extensie tijdens de beveiliging opnieuw geïnstalleerd.


