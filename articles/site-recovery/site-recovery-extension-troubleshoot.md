---
title: Problemen met de Azure VM-extensie oplossen voor herstel na noodgevallen met Azure Site Recovery
description: Problemen met de Azure VM-extensie oplossen voor herstel na noodgevallen met Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190727"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Problemen met Azure VM-extensie oplossen

In dit artikel vindt u stappen voor het oplossen van problemen met problemen waarmee u azure-fouten met betrekking tot de VM-agent en -extensie oplossen.


## <a name="azure-site-recovery-extension-time-out"></a>Time-out azure site recovery-extensie  

Foutbericht: "Taakuitvoering is een time-out tijdens het bijhouden van de extensiebewerking die moet worden gestart"<br>
Foutcode: "151076"

 Azure Site Recovery heeft een extensie op de virtuele machine geïnstalleerd als onderdeel van een beveiligingstaak inschakelen. Een van de volgende voorwaarden kan voorkomen dat de bescherming wordt geactiveerd en ervoor zorgen dat de taak mislukt. Voer de volgende stappen voor het oplossen van problemen uit en probeer uw bewerking opnieuw uit:

- [De agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [De extensie Siteherstel wordt niet bijgewerkt of geladen](#the-site-recovery-extension-fails-to-update-or-load)

Foutbericht: 'Vorige extensie siteherstel neemt meer tijd in beslag dan verwacht'.<br>
Foutcode: "150066"

- [De agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [De status van de extensie Siteherstel is onjuist](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Beveiliging mislukt omdat de VM-agent niet reageert

Foutbericht: "Taakuitvoering is een time-out tijdens het bijhouden van de extensiebewerking die moet worden gestart."<br>
Foutcode: "151099"

Deze fout kan optreden als de Azure-gastagent in de virtuele machine niet in de gereedstaat is.

U de status van Azure-gastagent controleren in de [Azure-portal.](https://portal.azure.com/) Ga naar de virtuele machine die u probeert te beveiligen en controleer de status in de status > van de > **VM-instellingen** >  **VM****Properties****eigenschappenagent**. De meeste van de tijd, de status van de agent is klaar na het opnieuw opstarten van de virtuele machine. Als u echter niet opnieuw opstarten of als u nog steeds met het probleem te maken hebt, voert u de volgende stappen voor het oplossen van problemen uit:

- [De agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Foutbericht: "Taakuitvoering is een time-out tijdens het bijhouden van de extensiebewerking die moet worden gestart."<br>
Foutcode: "151095"

Deze fout treedt op wanneer de agentversie op de Linux-machine verouderd is. Voer de volgende stappen voor het oplossen van problemen uit:

- [De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Oorzaken en oplossingen

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>De agent is geïnstalleerd in de VM, maar reageert niet (voor Windows VM's)

#### <a name="solution"></a>Oplossing
De VM-agent is mogelijk beschadigd of de service is mogelijk gestopt. Het opnieuw installeren van de VM-agent helpt om de nieuwste versie te krijgen. Het helpt ook de communicatie met de service opnieuw op te starten.

1. Bepaal of de Windows Azure Guest Agent-service wordt uitgevoerd in de VM-services (services.msc). Start de Windows Azure Guest Agent-service opnieuw.    
1. Als de Windows Azure Guest Agent-service niet zichtbaar is in services, opent u het Configuratiescherm. Ga naar **Programma's en functies** om te zien of de Windows Guest Agent-service is geïnstalleerd.
1. Als de Windows Azure Guest Agent wordt weergegeven in **Programma's en onderdelen,** verwijdert u de Windows Azure Guest Agent.
1. Download en installeer de [nieuwste versie van de agent MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U hebt beheerdersrechten nodig om de installatie te voltooien.
1. Controleer of de Windows Azure Guest Agent-service wordt weergegeven in services.
1. Start de beveiligingstaak opnieuw.

Controleer ook of [Microsoft .NET 4.5 is geïnstalleerd](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) in de VM. U hebt .NET 4.5 nodig om met de service te communiceren.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>De agent die in de VM is geïnstalleerd, is verouderd (voor Linux VM's)

#### <a name="solution"></a>Oplossing
De meeste agent-gerelateerde of extensie-gerelateerde fouten voor Linux VM's worden veroorzaakt door problemen die van invloed zijn op een verouderde VM-agent. Volg de volgende algemene richtlijnen om dit probleem op te lossen:

1. Volg de instructies voor [het bijwerken van de Linux VM-agent.](../virtual-machines/linux/update-agent.md)

   > [!NOTE]
   > We raden u *ten zeerste aan* de agent alleen te updaten via een distributieopslagplaats. We raden u af om de agentcode rechtstreeks van GitHub te downloaden en bij te werken. Als de nieuwste agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributieondersteuning voor instructies over het installeren ervan. Als u wilt controleren op de meest recente agent, gaat u naar de Windows [Azure Linux-agentpagina](https://github.com/Azure/WALinuxAgent/releases) in de GitHub-opslagplaats.

1. Controleer of de Azure-agent op de VM wordt uitgevoerd door de volgende opdracht uit te voeren:`ps -e`

   Als het proces niet wordt uitgevoerd, start u het opnieuw op met de volgende opdrachten:

   - Voor Ubuntu:`service walinuxagent start`
   - Voor andere distributies:`service waagent start`

1. [De automatische herstartagent configureren](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Bescherming van de virtuele machine mogelijk maken.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>De extensie Siteherstel wordt niet bijgewerkt of geladen

De extensiestatus wordt weergegeven als 'Leeg', 'NotReady' of 'Overgang'.

#### <a name="solution"></a>Oplossing

Verwijder de extensie en start de bewerking opnieuw.

Ga als een verwijderen uit de extensie:

1. Ga in de [Azure-portal](https://portal.azure.com/)naar de VM met back-upfouten.
1. Selecteer **Instellingen**.
1. Selecteer **Extensies**.
1. Selecteer **Siteherstelextensie**.
1. Selecteer **Verwijderen**.

Als de VMSnapshot-extensie voor Linux VM niet wordt weergegeven in de Azure-portal, [werkt u de Azure Linux Agent bij](../virtual-machines/linux/update-agent.md). Voer dan de bescherming uit.

Wanneer u deze stappen voltooit, wordt de extensie opnieuw geïnstalleerd tijdens de beveiliging.
