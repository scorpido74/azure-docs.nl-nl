---
title: Overzicht van Azure Monitor voor VM's inschakelen
description: Meer informatie over het implementeren en configureren van Azure Monitor voor VM's. Ontdek de systeem vereisten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 87d8b26110eba647975de577e9d7b5b0ed138266
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84423969"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Overzicht van Azure Monitor voor VM's inschakelen

Dit artikel bevat een overzicht van de beschik bare opties om Azure Monitor voor VM's op uw virtuele machines in te scha kelen om de status en prestaties te controleren. Detecteer toepassings afhankelijkheden die worden uitgevoerd op virtuele machines van Azure (Vm's) en virtuele-machine schaal sets, on-premises Vm's of Vm's die worden gehost in een andere cloud omgeving.  

Azure Monitor voor VM's instellen:

* Schakel een schaalset voor een virtuele Azure-machine in of virtual machines door **inzichten** rechtstreeks te selecteren in de VM of virtuele-machine schaalset.
* Schakel twee of meer Azure-Vm's en virtuele-machine schaal sets in met behulp van Azure Policy. Deze methode zorgt ervoor dat de vereiste afhankelijkheden op bestaande en nieuwe virtuele machines en schaal sets worden geïnstalleerd en op de juiste wijze zijn geconfigureerd. Niet-compatibele Vm's en schaal sets worden gerapporteerd, zodat u kunt beslissen of u ze wilt inschakelen en deze wilt herstellen.
* Schakel twee of meer virtuele Azure-machines of virtual machine Scale sets in voor een opgegeven abonnement of resource groep met behulp van Power shell.
* Schakel Azure Monitor voor VM's in om Vm's of fysieke computers te bewaken die worden gehost in uw bedrijfs netwerk of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de informatie in de volgende secties begrijpt. 

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor voor VM's ondersteunt een Log Analytics-werk ruimte in de volgende regio's:

- VS - west-centraal
- VS - west
- VS - west 2
- VS - zuid-centraal
- VS - oost
- VS - oost 2
- VS - centraal
- VS - noord-centraal
- US Gov VA
- Canada - midden
- Verenigd Koninkrijk Zuid
- Europa - noord
- Europa -west
- Azië - oost
- Azië - zuidoost
- India - centraal
- Japan - oost
- Australië - oost
- Australië - zuidoost

>[!NOTE]
>U kunt virtuele Azure-machines bewaken in elke regio. De Vm's zijn niet beperkt tot de regio's die worden ondersteund door de Log Analytics-werk ruimte.
>

Als u geen Log Analytics-werk ruimte hebt, kunt u er een maken met behulp van een van de volgende resources:
* [Azure-CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure-portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

U kunt ook een werk ruimte maken terwijl u controle inschakelt voor één virtuele machine in azure of voor de schaalset van VM'S in de Azure Portal.

Als u een schaal scenario wilt instellen dat gebruikmaakt van Azure Policy-, Azure PowerShell-of Azure Resource Manager sjablonen, in uw Log Analytics-werk ruimte:

* Installeer de *ServiceMap* -en *InfrastructureInsights* -oplossingen. U kunt deze installatie volt ooien met behulp van een meegeleverde Azure Resource Manager sjabloon. U kunt ook op het tabblad **aan de slag** in de Azure Portal **werk ruimte configureren**selecteren.
* Configureer de Log Analytics-werk ruimte voor het verzamelen van prestatie meter items.

Gebruik een van de volgende methoden om uw werk ruimte te configureren voor het schaal scenario:

* Gebruik [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Selecteer op de pagina dekking van Azure Monitor voor VM's [**beleid**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de optie **werk ruimte configureren**. 

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende tabel geeft een lijst van de Windows-en Linux-besturings systemen die Azure Monitor voor VM's ondersteunt. Verderop in dit gedeelte vindt u een volledige lijst met informatie over de belangrijkste en secundaire versie van het Linux-besturings systeem en de ondersteunde kernel-versies.

|Besturingssysteemversie |Prestaties |Maps |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9,4, 8 | X<sup>1</sup> | |

<sup>1</sup> de functie prestaties van Azure monitor voor VM's is alleen beschikbaar vanuit Azure monitor. Het is niet rechtstreeks beschikbaar vanuit het linkerdeel venster van de Azure-VM.

>[!NOTE]
>In het Linux-besturings systeem:
> - Alleen standaard- en SMP Linux kernelversies worden ondersteund.
> - Niet-standaard kernel-releases, zoals Physical Address Extension (PAE) en xen, worden niet ondersteund voor Linux-distributie. Bijvoorbeeld, een systeem met de release reeks *2.6.16.21-0,8-xen* wordt niet ondersteund.
> - Aangepaste kernels, met inbegrip van hercompilaties van standaard-kernels, worden niet ondersteund.
> - De CentOSPlus-kernel wordt ondersteund.
> - Er moet een patch worden uitgevoerd voor de Linux-kernel voor het Spectre-beveiligings probleem. Neem contact op met de leverancier van de Linux-distributie voor meer informatie.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 7,6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 6,10 | 2.6.32-754 |
| 6,9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 6,10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6,9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 18,04 | 5.3.0-1020<br>5,0 (inclusief door Azure afgestemde kernel)<br>4,18* <br> 4,15* |
| 16.04.3 | 4,15. * |
| 16,04 | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enter prise server

| Besturingssysteemversie | Kernelversie |
|:--|:--|
|12 SP4 | 4,12. * (inclusief door Azure afgestemde kernel) |
|12 SP3 | 4,4. * |
|12 SP2 | 4,4. * |

#### <a name="debian"></a>Debian 

| Besturingssysteemversie | Kernelversie |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>De micro soft-afhankelijkheids agent

De functie map in Azure Monitor voor VM's haalt gegevens op uit de micro soft-afhankelijkheids agent. De afhankelijkheids agent is afhankelijk van de Log Analytics-agent voor de verbinding met Log Analytics. Daarom moet op uw systeem de Log Analytics-agent zijn geïnstalleerd en geconfigureerd met de afhankelijkheids agent.

Of u Azure Monitor voor VM's voor één virtuele Azure-machine inschakelt of u de implementatie methode op schaal gebruikt, gebruikt u de Azure VM dependency agent-extensie voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) of [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) om de agent te installeren als onderdeel van de ervaring.

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

In een hybride omgeving kunt u de afhankelijkheids agent hand matig downloaden en installeren of een geautomatiseerde methode gebruiken.

De volgende tabel beschrijft de verbonden bronnen die de kaart functie ondersteunt in een hybride omgeving.

| Verbonden bron | Ondersteund | Beschrijving |
|:--|:--|:--|
| Windows-agents | Ja | Naast de [log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md), hebben Windows-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Linux-agents | Ja | Naast de [log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md)hebben Linux-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Beheergroep System Center Operations Manager | Nee | |

U kunt de afhankelijkheids agent downloaden van de volgende locaties:

| Bestand | Besturingssysteem | Versie | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Als u de functies in Azure Monitor voor VM's wilt inschakelen en gebruiken, moet u de rol *log Analytics Inzender* hebben. Als u de prestaties, de status en de kaart gegevens wilt bekijken, moet u de rol *bewakings lezer* hebben voor de Azure-VM. De Log Analytics-werk ruimte moet zijn geconfigureerd voor Azure Monitor voor VM's.

Zie [werk ruimten beheren](../../azure-monitor/platform/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

## <a name="how-to-enable-azure-monitor-for-vms"></a>Azure Monitor voor VM's inschakelen

Schakel Azure Monitor voor VM's in met een van de methoden die in deze tabel worden beschreven:

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Eén Azure VM of schaalset voor virtuele machines | [Inschakelen vanaf de VM](vminsights-enable-single-vm.md) | U kunt één virtuele Azure-machine inschakelen door **inzichten** rechtstreeks te selecteren in de VM of virtuele-machine schaalset. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Inschakelen via Azure Policy](vminsights-enable-at-scale-policy.md) | U kunt meerdere virtuele machines van Azure inschakelen met behulp van Azure Policy en beschik bare beleids definities. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Azure PowerShell-of Azure Resource Manager-sjablonen inschakelen](vminsights-enable-at-scale-powershell.md) | U kunt meerdere Azure Vm's of virtuele-machine schaal sets inschakelen voor een opgegeven abonnement of resource groep met behulp van Azure PowerShell-of Azure Resource Manager sjablonen. |
| Hybride cloud | [Inschakelen voor de hybride omgeving](vminsights-enable-hybrid-cloud.md) | U kunt implementeren op Vm's of fysieke computers die worden gehost in uw Data Center of in andere Cloud omgevingen. |

## <a name="management-packs"></a>Management packs

Wanneer Azure Monitor voor VM's is ingeschakeld en geconfigureerd met een Log Analytics-werk ruimte, wordt een management pack doorgestuurd naar alle Windows-computers die aan die werk ruimte rapporteren. Als u [uw System Center Operations Manager-beheer groep hebt geïntegreerd](../../azure-monitor/platform/om-agents.md) met de log Analytics-werk ruimte, wordt de servicetoewijzing Management Pack van de beheer groep geïmplementeerd naar de Windows-computers die rapporteren aan de beheer groep.  

De management pack heet *micro soft. intelligence packs. ApplicationDependencyMonitor*. De map die is geschreven naar `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` . De gegevens bron die de management pack gebruikt, is `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll` .

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiks gegevens

Micro soft verzamelt automatisch gebruiks-en prestatie gegevens via uw gebruik van de Azure Monitor service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren. 

De kaart functie bevat gegevens over de configuratie van uw software om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden. De gegevens bevatten informatie zoals het besturings systeem en de versie, het IP-adres, de DNS-naam en de naam van het werk station. Micro soft verzamelt geen namen, adressen of andere contact gegevens.

Zie de [privacyverklaring voor micro soft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132)voor meer informatie over het verzamelen en gebruiken van gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu u bewaking hebt ingeschakeld voor uw virtuele machine, zijn bewakings gegevens beschikbaar voor analyse in Azure Monitor voor VM's.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure monitor voor VM's prestaties weer geven](vminsights-performance.md)voor meer informatie over het gebruik van de functie voor prestatie bewaking. Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven.
