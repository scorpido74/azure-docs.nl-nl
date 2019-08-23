---
title: Overzicht van Azure Monitor voor VM's (preview) inschakelen | Microsoft Docs
description: Meer informatie over het implementeren en configureren van Azure Monitor voor VM's. Ontdek de systeem vereisten.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: f395ba5d63463aa177b453d187d025a4461eff28
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905579"
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

- US - west-centraal
- VS-West 2<sup>1</sup>
- East US
- Canada - midden
- Verenigd Koninkrijk Zuid
- Europa -west
- Azië - zuidoost
- Australië-oost<sup>1</sup>
- Australië-zuidoost<sup>1</sup>

<sup>1</sup> deze regio momenteel de status-functie van Azure Monitor biedt geen ondersteuning voor virtuele machines.

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

|Versie van het besturingssysteem |Prestaties |Kaarten |Status |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|WindowsServer 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|Windows 10-1803 | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 14,04, 16,04, 18,04 | X | X | X |
|CentOS Linux 6, 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8, 9,4 | X<sup>1</sup> | | X |

<sup>1</sup> prestaties van de functie van Azure Monitor voor virtuele machines is alleen beschikbaar via Azure Monitor. Het is niet rechtstreeks beschikbaar vanuit het linkerdeel venster van de Azure-VM.

>[!NOTE]
>De functie status van Azure Monitor voor VM's biedt geen ondersteuning [](../../virtual-machines/windows/nested-virtualization.md) voor geneste virtualisatie in een Azure-VM.
>

>[!NOTE]
>In het Linux-besturings systeem:
> - Alleen standaard- en SMP Linux kernelversies worden ondersteund.
> - Niet-standaard kernel versies, zoals fysieke Address Extension (PAE) en Xen, worden niet ondersteund voor een Linux-distributie. Bijvoorbeeld, een systeem met de tekenreeks voor de release van *2.6.16.21-0.8-xen* wordt niet ondersteund.
> - Aangepaste kernels, met inbegrip van hercompilaties van standaard-kernels, worden niet ondersteund.
> - De CentOSPlus-kernel wordt ondersteund.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versie van het besturingssysteem | Kernelversie |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versie van het besturingssysteem | Kernelversie
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |
|12 SP4 | 4.4. * |
|12 SP4 | Door Azure afgestemde kernel |

### <a name="the-microsoft-dependency-agent"></a>De agent voor Microsoft Dependency

De functie map in Azure Monitor voor VM's haalt gegevens op uit de micro soft-afhankelijkheids agent. De agent voor afhankelijkheden, is afhankelijk van de Log Analytics-agent voor de verbinding met Log Analytics. Daarom moet op uw systeem de Log Analytics-agent zijn geïnstalleerd en geconfigureerd met de afhankelijkheids agent.

Of u Azure Monitor voor VM's voor één virtuele Azure-machine inschakelt of u de implementatie methode op schaal gebruikt, gebruikt u de Azure VM dependency agent-extensie om de agent te installeren als onderdeel van de ervaring.

>[!NOTE]
>De volgende informatie die in deze sectie wordt beschreven, is ook van toepassing op de [servicetoewijzing oplossing](service-map.md).  

In een hybride omgeving kunt u de afhankelijkheids agent hand matig downloaden en installeren. Als uw Vm's buiten Azure worden gehost, gebruikt u een automatische implementatie methode.

De volgende tabel beschrijft de verbonden bronnen die ondersteuning biedt voor de kaart-functie in een hybride omgeving.

| Verbonden bron | Ondersteund | Description |
|:--|:--|:--|
| Windows-agents | Ja | Naast de [log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md), hebben Windows-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Linux-agents | Ja | Naast de [log Analytics-agent voor Linux](../../azure-monitor/platform/log-analytics-agent.md)hebben Linux-agents de afhankelijkheids agent nodig. Zie [ondersteunde besturings systemen](#supported-operating-systems)voor meer informatie. |
| Beheergroep System Center Operations Manager | Nee | |

U kunt de afhankelijkheids agent downloaden van de volgende locaties:

| File | OS | Versie | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.1 | FCF9C1D9B20AD414051B49EE79144E595CCC411EB6D444D6D5B5A7B1874DCDEC |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Als u de functies in Azure Monitor voor VM's wilt inschakelen en gebruiken, moet u de rol *log Analytics Inzender* hebben. Als u de prestaties, de status en de kaart gegevens wilt bekijken, moet u de rol *bewakings lezer* hebben voor de Azure-VM. De Log Analytics-werkruimte moet worden geconfigureerd voor Azure Monitor voor virtuele machines.

Zie voor meer informatie over het beheren van toegang tot een Log Analytics-werkruimte [werkruimten beheren](../../azure-monitor/platform/manage-access.md).

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Azure Monitor voor VM's inschakelen (preview)

Schakel Azure Monitor voor VM's in met een van de methoden die in deze tabel worden beschreven:

| Implementatie status | Methode | Description |
|------------------|--------|-------------|
| Eén Azure VM of schaalset voor virtuele machines | [Inschakelen vanaf de VM](vminsights-enable-single-vm.md) | U kunt één virtuele Azure-machine inschakelen door **inzichten (preview)** rechtstreeks te selecteren op basis van de VM of virtuele-machine schaalset. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Inschakelen via Azure Policy](vminsights-enable-at-scale-policy.md) | U kunt meerdere virtuele machines van Azure inschakelen met behulp van Azure Policy en beschik bare beleids definities. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Azure PowerShell-of Azure Resource Manager-sjablonen inschakelen](vminsights-enable-at-scale-powershell.md) | U kunt meerdere Azure Vm's of virtuele-machine schaal sets inschakelen voor een opgegeven abonnement of resource groep met behulp van Azure PowerShell-of Azure Resource Manager sjablonen. |
| Hybride cloud | [Inschakelen voor de hybride omgeving](vminsights-enable-hybrid-cloud.md) | U kunt implementeren op Vm's of fysieke computers die worden gehost in uw Data Center of in andere Cloud omgevingen. |

## <a name="performance-counters-enabled"></a>Prestatiemeteritems ingeschakeld 

Azure Monitor voor VM's configureert een Log Analytics werk ruimte voor het verzamelen van de prestatie meter items die worden gebruikt. De volgende tabellen geven een lijst van de objecten en tellers die elke 60 seconden worden verzameld.

### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems

|Objectnaam |Naam van het prestatiemeteritem |
|------------|-------------|
|Logische schijf |Percentage vrije ruimte |
|Logische schijf |Gem. Schijf sec/lezen |
|Logische schijf |Gem. Schijfoverdrachten per seconde |
|Logische schijf |Gem. Schijf sec/schrijven |
|Logische schijf |Schijf Bytes per seconde |
|Logische schijf |Bytes gelezen op schijf/sec |
|Logische schijf |Schijf lezen per seconde |
|Logische schijf |Schijfoverdrachten per seconde |
|Logische schijf |Bytes geschreven naar schijf/sec |
|Logische schijf |Schijf schrijven per seconde |
|Logische schijf |Beschikbare Megabytes |
|Geheugen |Beschikbare megabytes (MB) |
|-Netwerkadapter |Ontvangen bytes per seconde |
|-Netwerkadapter |Verzonden bytes per seconde |
|Processor |% Processortijd |

### <a name="linux-performance-counters"></a>Linux-prestatiemeteritems

|Objectnaam |Naam van het prestatiemeteritem |
|------------|-------------|
|Logische schijf |Percentage gebruikte ruimte |
|Logische schijf |Bytes gelezen op schijf/sec |
|Logische schijf |Schijf lezen per seconde |
|Logische schijf |Schijfoverdrachten per seconde |
|Logische schijf |Bytes geschreven naar schijf/sec |
|Logische schijf |Schijf schrijven per seconde |
|Logische schijf |Beschikbare Megabytes |
|Logische schijf |Logische schijf Bytes per seconde |
|Geheugen |Beschikbaar geheugen in megabytes |
|Netwerk |Totaal aantal ontvangen Bytes |
|Netwerk |Totaal aantal verzonden Bytes |
|Processor |% Processortijd |

## <a name="management-packs"></a>Management packs

Wanneer Azure Monitor voor VM's is ingeschakeld en geconfigureerd met een Log Analytics-werk ruimte, wordt een management pack doorgestuurd naar alle Windows-computers die aan die werk ruimte rapporteren. Als u [uw System Center Operations Manager-beheer groep hebt geïntegreerd](../../azure-monitor/platform/om-agents.md) met de log Analytics-werk ruimte, wordt de servicetoewijzing Management Pack van de beheer groep geïmplementeerd naar de Windows-computers die rapporteren aan de beheer groep.  

De management pack heet *micro soft. intelligence packs. ApplicationDependencyMonitor*. Het is geschreven naar de%Programfiles%\Microsoft monitoring Agent\Agent\Health service State\Management packs \ map. De gegevensbron die gebruikmaakt van het managementpack is % Program files%\Microsoft bewaking Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiksgegevens

Microsoft verzamelt automatisch gebruiks- en -gegevens door uw gebruik van de Azure Monitor-service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren. 

De kaart functie bevat gegevens over de configuratie van uw software om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden. De gegevens bevatten informatie zoals het besturings systeem en de versie, het IP-adres, de DNS-naam en de naam van het werk station. Microsoft biedt geen namen, adressen of andere contactgegevens verzameld.

Zie voor meer informatie over het verzamelen van gegevens en het gebruik, de [privacyverklaring van Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu u bewaking hebt ingeschakeld voor uw virtuele machine, zijn bewakings gegevens beschikbaar voor analyse in Azure Monitor voor VM's.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van de Health-functie, [weergave Azure Monitor voor virtuele machines Health](vminsights-health.md). Afhankelijkheden van gedetecteerde toepassingen, Zie [weergave Azure Monitor voor virtuele machines kaart](vminsights-maps.md).
