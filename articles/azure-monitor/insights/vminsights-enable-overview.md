---
title: Overzicht van Azure Monitor voor VM's inschakelen
description: Meer informatie over het implementeren en configureren van Azure Monitor voor VM's. Ontdek de systeemvereisten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/08/2020
ms.openlocfilehash: 5bb5d5dd5110f176b59a99f6a3aa223184158da5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982307"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Overzicht van Azure Monitor voor VM's inschakelen

In dit artikel vindt u een overzicht van de beschikbare opties om Azure Monitor voor VM's op uw virtuele machines in te schakelen om de status en prestaties te controleren. Ontdek toepassingsafhankelijkheden die worden uitgevoerd op Virtuele Azure-machines (VM's) en virtuele machineschaalsets, on-premises VM's of VM's die worden gehost in een andere cloudomgeving.  

Ga als volgt te werk om Azure Monitor voor VM's in te stellen:

* Schakel één Azure VM- of virtuele machineschaalset in door **Insights** rechtstreeks te selecteren in de vm- of virtuele machineschaalset.
* Schakel twee of meer Azure VM's en virtuele machineschaalsets in met Azure Policy. Deze methode zorgt ervoor dat op bestaande en nieuwe VM's en schaalsets de vereiste afhankelijkheden worden geïnstalleerd en correct geconfigureerd. Niet-conforme VM's en schaalsets worden gerapporteerd, zodat u beslissen of u deze wilt inschakelen en deze wilt herstellen.
* Schakel twee of meer Azure VM's of virtuele machineschaalsets in een opgegeven abonnement of resourcegroep in met PowerShell.
* Azure Monitor voor VM's inschakelen om VM's of fysieke computers te controleren die worden gehost in uw bedrijfsnetwerk of andere cloudomgeving.

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint of u de informatie in de volgende secties begrijpt. 

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicekaartoplossing.](service-map.md)  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor voor VM's ondersteunt een Log Analytics-werkruimte in de volgende regio's:

- VS - west-centraal
- VS - west
- VS - west 2
- VS - zuid-centraal
- VS - oost
- VS - oost 2
- VS - centraal
- VS - noord-centraal
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
>U Azure VM's in elke regio controleren. De VM's zelf zijn niet beperkt tot de regio's die worden ondersteund door de werkruimte Log Analytics.
>

Als u geen werkruimte loganalytics hebt, u er een maken met een van de bronnen:
* [Azure-CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

U ook een werkruimte maken terwijl u bewaking inschakelt voor één Azure VM- of virtuele machineschaalset in de Azure-portal.

Ga als u een scenario op schaal instelt waarin azure-beleid,Azure PowerShell- of Azure Resource Manager-sjablonen worden gebruikt in uw werkruimte Log Analytics:

* Installeer de *oplossingen servicemap* en *InfrastructureInsights.* U deze installatie voltooien met behulp van een meegeleverde Azure Resource Manager-sjabloon. Of selecteer **Werkruimte configureren**op het tabblad Aan **de slag** in de Azure-portal .
* Configureer de werkruimte Log Analytics om prestatiemeteritems te verzamelen.

Als u uw werkruimte wilt configureren voor het scenario op schaal, gebruikt u een van de volgende methoden:

* Azure [PowerShell gebruiken](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Selecteer **werkruimte configureren**op de pagina [**Beleidsdekking**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) azure monitor voor VMs . 

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

In de volgende tabel worden de Windows- en Linux-besturingssystemen weergegeven die Azure Monitor voor VM's ondersteunt. Later in deze sectie vindt u een volledige lijst met details over de belangrijkste en kleine Linux OS release en ondersteunde kernel versies.

|Versie van het besturingssysteem |Prestaties |Kaarten |
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
|Ubuntu 18.04, 16.04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4. | X<sup>1</sup> | |

<sup>1</sup> De prestatiefunctie van Azure Monitor voor VM's is alleen beschikbaar via Azure Monitor. Het is niet rechtstreeks beschikbaar vanuit het linkerdeelvenster van de Azure VM.

>[!NOTE]
>In het Linux-besturingssysteem:
> - Alleen standaard- en SMP Linux kernelversies worden ondersteund.
> - Niet-standaard kernel releases, zoals Physical Address Extension (PAE) en Xen, worden niet ondersteund voor een Linux-distributie. Een systeem met de releasetekenreeks van *2.6.16.21-0,8 xen* wordt bijvoorbeeld niet ondersteund.
> - Aangepaste kernels, waaronder recompilaties van standaardkernels, worden niet ondersteund.
> - CentOSPlus kernel wordt ondersteund.
> - De Linux kernel moet worden gepatcht voor de Spectre kwetsbaarheid. Raadpleeg uw Linux distributie leverancier voor meer informatie.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus (CentOSPlus)

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 18.04 | 5.0 (inclusief azure-tuned kernel)<br>4.18*<br>4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
|12 SP4 | 4.12.* (inclusief azure-tuned kernel) |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>De Microsoft Dependency-agent

De mapfunctie in Azure Monitor voor VM's krijgt de gegevens van de Microsoft Dependency-agent. De afhankelijkheidsagent vertrouwt op de Log Analytics-agent voor de verbinding met Log Analytics. Uw systeem moet dus de log-analyse-agent hebben geïnstalleerd en geconfigureerd met de afhankelijkheidsagent.

Of u Azure Monitor voor VM's nu inschakelt voor één Azure VM of de implementatiemethode op schaal gebruikt, gebruikt u de Azure VM Dependency agent-extensie voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) of [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) om de agent te installeren als onderdeel van de ervaring.

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicekaartoplossing.](service-map.md)  

In een hybride omgeving u de afhankelijkheidsagent handmatig downloaden en installeren of een geautomatiseerde methode gebruiken.

In de volgende tabel worden de verbonden bronnen beschreven die de functie Kaart ondersteunt in een hybride omgeving.

| Verbonden bron | Ondersteund | Beschrijving |
|:--|:--|:--|
| Windows-agents | Ja | Samen met de [Log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md)hebben Windows-agents de afhankelijkheidsagent nodig. Zie [ondersteunde besturingssystemen](#supported-operating-systems)voor meer informatie . |
| Linux-agents | Ja | Samen met de [Log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md)hebben Linux-agents de Afhankelijkheidsagent nodig. Zie [ondersteunde besturingssystemen](#supported-operating-systems)voor meer informatie . |
| Beheergroep System Center Operations Manager | Nee | |

U de afhankelijkheidsagent downloaden vanaf deze locaties:

| File | OS | Versie | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.3.9380 | 40763BD0A5B60707DF3F9E7BCC17D917F5CE995F2F5A4633D8B733F3BE143921  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.3.9380 | BB41BB59BDD293968F02A9EF821F9639406AAA1F1F67925DB9EE00D54A7F0B |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Als u de functies in Azure Monitor voor VM's wilt inschakelen en openen, moet u de rol *van de medewerker van Log Analytics* hebben. Als u prestatie-, status- en kaartgegevens wilt weergeven, moet u de rol *van de monitorlezer* voor de Azure VM hebben. De werkruimte Log Analytics moet zijn geconfigureerd voor Azure Monitor voor VM's.

Zie [Werkruimten beheren](../../azure-monitor/platform/manage-access.md)voor meer informatie over het beheren van toegang tot een Log Analytics-werkruimte.

## <a name="how-to-enable-azure-monitor-for-vms"></a>Azure-monitor inschakelen voor VM's

Azure Monitor voor VM's inschakelen met een van de methoden die in deze tabel worden beschreven:

| Implementatiestatus | Methode | Beschrijving |
|------------------|--------|-------------|
| Eén Azure VM- of virtuele machineschaalset | [Inschakelen vanaf de VM](vminsights-enable-single-vm.md) | U één Azure VM inschakelen door **Insights** rechtstreeks te selecteren in de vm- of virtuele machineschaalset. |
| Meerdere Azure VM's of virtuele machineschaalsets | [Inschakelen via Azure-beleid](vminsights-enable-at-scale-policy.md) | U meerdere Azure VM's inschakelen met Azure Policy en beschikbare beleidsdefinities. |
| Meerdere Azure VM's of virtuele machineschaalsets | [Inschakelen via Azure PowerShell- of Azure Resource Manager-sjablonen](vminsights-enable-at-scale-powershell.md) | U meerdere Azure VM's of virtuele machineschaalsets inschakelen voor een bepaald abonnement of resourcegroep met Azure PowerShell- of Azure Resource Manager-sjablonen. |
| Hybride cloud | [Inschakelen voor de hybride omgeving](vminsights-enable-hybrid-cloud.md) | U implementeren op VM's of fysieke computers die worden gehost in uw datacenter of andere cloudomgevingen. |

## <a name="management-packs"></a>Management packs

Wanneer Azure Monitor voor VM's is ingeschakeld en geconfigureerd met een Log Analytics-werkruimte, wordt een beheerpakket doorgestuurd naar alle Windows-computers die naar die werkruimte rapporteren. Als u [uw beheergroep System Center Operations Manager](../../azure-monitor/platform/om-agents.md) hebt geïntegreerd met de werkruimte Log Analytics, wordt het beheerpakket servicekaarten geïmplementeerd vanuit de beheergroep naar de Windows-computers die rapporteren aan de beheergroep.  

Het management pack heet *Microsoft.IntelligencePacks.ApplicationDependencyMonitor*. Zijn geschreven `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` aan omslag. De gegevensbron die het `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`beheerpakket gebruikt, is .

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiksgegevens

Microsoft verzamelt automatisch gebruiks- en prestatiegegevens door uw gebruik van de Azure Monitor-service. Microsoft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren. 

De mapfunctie bevat gegevens over de configuratie van uw software om nauwkeurige en efficiënte probleemoplossingsmogelijkheden te bieden. De gegevens bieden informatie zoals het besturingssysteem en de versie, IP-adres, DNS-naam en werkstationnaam. Microsoft verzamelt geen namen, adressen of andere contactgegevens.

Zie de [privacyverklaring van Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132)voor meer informatie over het verzamelen en gebruiken van gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu u bewaking voor uw VM hebt ingeschakeld, is bewakingsinformatie beschikbaar voor analyse in Azure Monitor voor VM's.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Monitor for VM-prestaties weergeven voor](vminsights-performance.md)meer informatie over het gebruik van de functie Prestatiecontrole voor prestaties van de prestaties van de prestaties . Zie Azure Monitor for [VM-toewijzing weergeven](vminsights-maps.md)om gedetecteerde toepassingsafhankelijkheden weer te geven.
