---
title: Overzicht van Azure Monitor voor VM's (preview) inschakelen | Microsoft Docs
description: Meer informatie over het implementeren en configureren van Azure Monitor voor VM's. Ontdek de systeem vereisten.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 44422f66f6fc995dcaf96947ea05b183c7131ea3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669570"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Overzicht van Azure Monitor voor VM's inschakelen (preview)

In dit artikel vindt u een overzicht van de beschik bare opties voor het instellen van Azure Monitor voor VM's. Gebruik Azure Monitor voor VM's om de status en prestaties te bewaken. Detecteer toepassings afhankelijkheden die worden uitgevoerd op virtuele machines van Azure (Vm's) en virtuele-machine schaal sets, on-premises Vm's of Vm's die worden gehost in een andere cloud omgeving.  

Azure Monitor voor VM's instellen:

* Schakel een schaalset voor een virtuele Azure-machine in of virtual machines door **inzichten (preview)** rechtstreeks te selecteren op de virtuele machine of in de VM-schaalset.
* Schakel twee of meer Azure-Vm's en virtuele-machine schaal sets in met behulp van Azure Policy. Deze methode zorgt ervoor dat de vereiste afhankelijkheden op bestaande en nieuwe virtuele machines en schaal sets worden geïnstalleerd en op de juiste wijze zijn geconfigureerd. Niet-compatibele Vm's en schaal sets worden gerapporteerd, zodat u kunt beslissen of u ze wilt inschakelen en deze wilt herstellen.
* Inschakelen van twee of meer virtuele Azure-machines of virtuele machine-schaalsets voor een opgegeven abonnement of resourcegroep met behulp van PowerShell.
* Schakel Azure Monitor voor VM's in om Vm's of fysieke computers te bewaken die worden gehost in uw bedrijfs netwerk of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorg ervoor dat u weet dat de informatie in de volgende secties. 

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

### <a name="log-analytics"></a>Log Analytics

Azure Monitor voor VM's ondersteunt een Log Analytics-werk ruimte in de volgende regio's:

- VS - west-centraal
- VS - west
- VS - west 2
- VS - zuid-centraal
- VS - oost
- VS - oost2
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
>U kunt Azure-Vm's implementeren vanuit elke regio. Deze Vm's zijn niet beperkt tot de regio's die worden ondersteund door de Log Analytics-werk ruimte.
>

Als u geen werk ruimte hebt, kunt u er een maken met behulp van een van deze resources:
* [De Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

U kunt ook een werk ruimte maken terwijl u controle inschakelt voor één virtuele machine in azure of voor de schaalset van VM'S in de Azure Portal.

Als u een schaal scenario wilt instellen dat gebruikmaakt van Azure Policy-, Azure PowerShell-of Azure Resource Manager sjablonen, in uw Log Analytics-werk ruimte:

* Installeer de ServiceMap en InfrastructureInsights oplossingen. U kunt deze installatie volt ooien met behulp van een meegeleverde Azure Resource Manager sjabloon. Klik op het tabblad **aan de slag** op **werk ruimte configureren**.
* Configureer de werkruimte voor logboekanalyse voor het verzamelen van prestatiemeteritems.

Gebruik een van de volgende methoden om uw werk ruimte te configureren voor het schaal scenario:

* Gebruik [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Selecteer op de pagina dekking van Azure Monitor voor VM's [**beleid**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de optie **werk ruimte configureren**. 

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende tabel geeft een lijst van de Windows-en Linux-besturings systemen die Azure Monitor voor VM's ondersteunt. Verderop in dit gedeelte vindt u een volledige lijst met informatie over de belangrijkste en secundaire versie van het Linux-besturings systeem en de ondersteunde kernel-versies.

|Versie van het besturingssysteem |Prestaties |Kaarten |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|WindowsServer 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10-1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| 
|Ubuntu 18,04, 16,04 | X | X |
|CentOS Linux 7, 6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | |

<sup>1</sup> de functie prestaties van Azure monitor voor VM's is alleen beschikbaar vanuit Azure monitor. Het is niet rechtstreeks beschikbaar vanuit het linkerdeel venster van de Azure-VM.

>[!NOTE]
>In het Linux-besturings systeem:
> - Alleen standaard- en SMP Linux kernelversies worden ondersteund.
> - Niet-standaard kernel versies, zoals fysieke Address Extension (PAE) en Xen, worden niet ondersteund voor een Linux-distributie. Bijvoorbeeld, een systeem met de release reeks *2.6.16.21-0,8-xen* wordt niet ondersteund.
> - Aangepaste kernels, met inbegrip van hercompilaties van standaard-kernels, worden niet ondersteund.
> - De CentOSPlus-kernel wordt ondersteund.
> - Er moet een patch worden uitgevoerd voor de Linux-kernel voor het Spectre-beveiligings probleem. Neem contact op met de leverancier van de Linux-distributie voor meer informatie.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 7,6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 18,04 | 5,0 (inclusief door Azure afgestemde kernel)<br>4,18 *<br>4,15* |
| 16.04.3 | 4,15. * |
| 16.04 | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
|12 SP4 | 4,12. * (inclusief door Azure afgestemde kernel) |
|12 SP3 | 4.4. * |
|12 SP2 | 4.4. * |

#### <a name="debian"></a>Debian 

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 9 | 4,9 | 

### <a name="the-microsoft-dependency-agent"></a>De agent voor Microsoft Dependency

De functie map in Azure Monitor voor VM's haalt gegevens op uit de micro soft-afhankelijkheids agent. De agent voor afhankelijkheden, is afhankelijk van de Log Analytics-agent voor de verbinding met Log Analytics. Daarom moet op uw systeem de Log Analytics-agent zijn geïnstalleerd en geconfigureerd met de afhankelijkheids agent.

Of u Azure Monitor voor VM's voor één virtuele Azure-machine inschakelt of u de implementatie methode op schaal gebruikt, gebruikt u de Azure VM dependency agent-extensie voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) of [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) om de agent te installeren als onderdeel van de ervaring.

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

In een hybride omgeving kunt u de afhankelijkheids agent hand matig downloaden en installeren of een geautomatiseerde methode gebruiken.

De volgende tabel beschrijft de verbonden bronnen die ondersteuning biedt voor de kaart-functie in een hybride omgeving.

| Verbonden bron | Ondersteund | Beschrijving |
|:--|:--|:--|
| Windows-agents | Ja | Naast de [log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md), hebben Windows-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Linux-agents | Ja | Naast de [log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md)hebben Linux-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Beheergroep System Center Operations Manager | Nee | |

U kunt de afhankelijkheids agent downloaden van de volgende locaties:

| File | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Als u de functies in Azure Monitor voor VM's wilt inschakelen en gebruiken, moet u de rol *log Analytics Inzender* hebben. Als u de prestaties, de status en de kaart gegevens wilt bekijken, moet u de rol *bewakings lezer* hebben voor de Azure-VM. De Log Analytics-werkruimte moet worden geconfigureerd voor Azure Monitor voor virtuele machines.

Zie [werk ruimten beheren](../../azure-monitor/platform/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Azure Monitor voor VM's inschakelen (preview)

Schakel Azure Monitor voor VM's in met een van de methoden die in deze tabel worden beschreven:

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Eén Azure VM of schaalset voor virtuele machines | [Inschakelen vanaf de VM](vminsights-enable-single-vm.md) | U kunt één virtuele Azure-machine inschakelen door **inzichten (preview)** rechtstreeks te selecteren op basis van de VM of virtuele-machine schaalset. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Inschakelen via Azure Policy](vminsights-enable-at-scale-policy.md) | U kunt meerdere virtuele machines van Azure inschakelen met behulp van Azure Policy en beschik bare beleids definities. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Azure PowerShell-of Azure Resource Manager-sjablonen inschakelen](vminsights-enable-at-scale-powershell.md) | U kunt meerdere Azure Vm's of virtuele-machine schaal sets inschakelen voor een opgegeven abonnement of resource groep met behulp van Azure PowerShell-of Azure Resource Manager sjablonen. |
| Hybride cloud | [Inschakelen voor de hybride omgeving](vminsights-enable-hybrid-cloud.md) | U kunt implementeren op Vm's of fysieke computers die worden gehost in uw Data Center of in andere Cloud omgevingen. |

## <a name="performance-counters-enabled"></a>Prestatiemeteritems ingeschakeld 

Azure Monitor voor VM's configureert een Log Analytics werk ruimte voor het verzamelen van de prestatie meter items die worden gebruikt. De volgende tabellen geven een lijst van de objecten en tellers die elke 60 seconden worden verzameld.

>[!NOTE]
>De volgende lijst met prestatie meter items die worden ingeschakeld door Azure Monitor voor VM's, beperkt u niet tot het inschakelen van aanvullende prestatie meter items die u moet verzamelen van Vm's die rapporteren aan de werk ruimte. Als u deze items uitschakelt, voor komt u dat de reeks prestatie grafieken die deel uitmaken van de prestatie functie, het resource gebruik van uw Vm's niet kan weer geven.

### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems

|Objectnaam |Naam van het prestatiemeteritem |
|------------|-------------|
|Logische schijf |% vrije ruimte |
|Logische schijf |Gemiddelde Lees tijd schijf |
|Logische schijf |Gemiddelde tijd schijf overdracht |
|Logische schijf |Gemiddelde schrijf tijd schijf |
|Logische schijf |bytes per seconde |
|Logische schijf |gelezen bytes per seconde |
|Logische schijf |leesbewerkingen per seconde |
|Logische schijf |Schijfoverdrachten per seconde |
|Logische schijf |geschreven Bytes per seconde |
|Logische schijf |schrijfbewerkingen per seconde |
|Logische schijf |Beschikbare Megabytes |
|Geheugen |Beschikbare megabytes (MB) |
|Netwerkadapter |Ontvangen bytes per seconde |
|Netwerkadapter |Verzonden bytes per seconde |
|Processor |Percentage processortijd |

### <a name="linux-performance-counters"></a>Linux-prestatiemeteritems

|Objectnaam |Naam van het prestatiemeteritem |
|------------|-------------|
|Logische schijf |Percentage gebruikte ruimte |
|Logische schijf |gelezen bytes per seconde |
|Logische schijf |leesbewerkingen per seconde |
|Logische schijf |Schijfoverdrachten per seconde |
|Logische schijf |geschreven Bytes per seconde |
|Logische schijf |schrijfbewerkingen per seconde |
|Logische schijf |Beschikbare Megabytes |
|Logische schijf |Logische schijf Bytes per seconde |
|Geheugen |Beschikbaar geheugen in megabytes |
|Netwerk |Totaal aantal ontvangen Bytes |
|Netwerk |Totaal aantal verzonden Bytes |
|Processor |Percentage processortijd |

## <a name="management-packs"></a>Management packs

Wanneer Azure Monitor voor VM's is ingeschakeld en geconfigureerd met een Log Analytics-werk ruimte, wordt een management pack doorgestuurd naar alle Windows-computers die aan die werk ruimte rapporteren. Als u [uw System Center Operations Manager-beheer groep hebt geïntegreerd](../../azure-monitor/platform/om-agents.md) met de log Analytics-werk ruimte, wordt de servicetoewijzing Management Pack van de beheer groep geïmplementeerd naar de Windows-computers die rapporteren aan de beheer groep.  

De management pack heet *micro soft. intelligence packs. ApplicationDependencyMonitor*. De map die is geschreven naar `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`. De gegevens bron die door de management pack wordt gebruikt, is `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiksgegevens

Microsoft verzamelt automatisch gebruiks- en -gegevens door uw gebruik van de Azure Monitor-service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren. 

De kaart functie bevat gegevens over de configuratie van uw software om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden. De gegevens bevatten informatie zoals het besturings systeem en de versie, het IP-adres, de DNS-naam en de naam van het werk station. Microsoft biedt geen namen, adressen of andere contactgegevens verzameld.

Zie de [privacyverklaring voor micro soft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132)voor meer informatie over het verzamelen en gebruiken van gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu u bewaking hebt ingeschakeld voor uw virtuele machine, zijn bewakings gegevens beschikbaar voor analyse in Azure Monitor voor VM's.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure monitor voor VM's prestaties weer geven](vminsights-performance.md)voor meer informatie over het gebruik van de functie voor prestatie bewaking. Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven.
