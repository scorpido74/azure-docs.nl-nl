---
title: Overzicht van Azure Monitor voor VM's (preview) inschakelen | Microsoft Docs
description: Meer informatie over het implementeren en configureren van Azure Monitor voor VM's. Ontdek de systeem vereisten.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/24/2019
ms.openlocfilehash: 83c2264cd3aa3025e911db5079747930d1ae096b
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809453"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Overzicht van Azure Monitor voor VM's inschakelen (preview)

In dit artikel vindt u een overzicht van de beschik bare opties voor het instellen van Azure Monitor voor VM's. Gebruik Azure Monitor voor VM's om de status en prestaties te bewaken. Detecteer toepassings afhankelijkheden die worden uitgevoerd op virtuele machines van Azure (Vm's) en virtuele-machine schaal sets, on-premises Vm's of Vm's die worden gehost in een andere cloud omgeving.  

Azure Monitor voor VM's instellen:

* Schakel een schaalset voor een virtuele Azure-machine in of virtual machines door **inzichten (preview)** rechtstreeks te selecteren op de virtuele machine of in de VM-schaalset.
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
- VS-West 2<sup>1</sup>
- VS - oost
- Oost-VS2<sup>1</sup>
- Canada-Midden
- VK - zuid
- Europa-noord<sup>1</sup>
- Europa - west
- Azië - zuidoost
- Japan-Oost<sup>1</sup>
- Australië-oost<sup>1</sup>
- Australië-zuidoost<sup>1</sup>

<sup>1</sup> deze regio biedt momenteel geen ondersteuning voor de status functie van Azure monitor voor VM's.

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

* Installeer de ServiceMap-en InfrastructureInsights-oplossingen. U kunt deze installatie volt ooien met behulp van een meegeleverde Azure Resource Manager sjabloon. Klik op het tabblad **aan de slag** op **werk ruimte configureren**.
* Configureer de Log Analytics-werk ruimte voor het verzamelen van prestatie meter items.

Gebruik een van de volgende methoden om uw werk ruimte te configureren voor het schaal scenario:

* Gebruik [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace).
* Selecteer op de pagina dekking van Azure Monitor voor VM's [**beleid**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview) de optie **werk ruimte configureren**. 

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende tabel geeft een lijst van de Windows-en Linux-besturings systemen die Azure Monitor voor VM's ondersteunt. Verderop in dit gedeelte vindt u een volledige lijst met informatie over de belangrijkste en secundaire versie van het Linux-besturings systeem en de ondersteunde kernel-versies.

|Versie van het besturingssysteem |Prestaties |Kaarten |Gezondheid |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|Windows 10 1803 | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6, 7| X | X| X |
|Ubuntu 18,04, 16,04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 9,4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> de functie prestaties van Azure monitor voor VM's is alleen beschikbaar vanuit Azure monitor. Het is niet rechtstreeks beschikbaar vanuit het linkerdeel venster van de Azure-VM.

>[!NOTE]
>De functie status van Azure Monitor voor VM's biedt geen ondersteuning voor [geneste virtualisatie](../../virtual-machines/windows/nested-virtualization.md) in een Azure-VM.
>

>[!NOTE]
>In het Linux-besturings systeem:
> - Alleen standaard- en SMP Linux kernelversies worden ondersteund.
> - Niet-standaard kernel-releases, zoals Physical Address Extension (PAE) en xen, worden niet ondersteund voor Linux-distributie. Bijvoorbeeld, een systeem met de release reeks *2.6.16.21-0,8-xen* wordt niet ondersteund.
> - Aangepaste kernels, met inbegrip van hercompilaties van standaard-kernels, worden niet ondersteund.
> - De CentOSPlus-kernel wordt ondersteund.
> - Er moet een patch worden uitgevoerd voor de Linux-kernel voor het Spectre-beveiligings probleem. Neem contact op met de leverancier van de Linux-distributie voor meer informatie.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versie van het besturingssysteem | Kernel-versie |
|:--|:--|
| 7,6 | 3.10.0-957 |
| 7,5 | 3.10.0-862 |
| 7,4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versie van het besturingssysteem | Kernel-versie |
|:--|:--|
| 6,10 | 2.6.32-754 |
| 6,9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| Versie van het besturingssysteem | Kernel-versie |
|:--|:--|
| 6,10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6,9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versie van het besturingssysteem | Kernel-versie |
|:--|:--|
| 18,04 | 5,0 (inclusief door Azure afgestemde kernel)<br>4,18 *<br>4.15* |
| 16.04.3 | 4,15. * |
| 16,04 | 4,13. \*<br>4,11. \*<br>4,10. \*<br>4,8. \*<br>4,4. \* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enter prise server

| Versie van het besturingssysteem | Kernel-versie |
|:--|:--|
|12 SP4 | 4,12. * (inclusief door Azure afgestemde kernel) |
|12 SP3 | 4,4. * |
|12 SP2 | 4,4. * |

#### <a name="debian"></a>Debian 

| Versie van het besturingssysteem | Kernel-versie |
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

| Bestand | Besturingssysteem | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>Toegangsbeheer op basis van rollen

Als u de functies in Azure Monitor voor VM's wilt inschakelen en gebruiken, moet u de rol *log Analytics Inzender* hebben. Als u de prestaties, de status en de kaart gegevens wilt bekijken, moet u de rol *bewakings lezer* hebben voor de Azure-VM. De Log Analytics-werk ruimte moet zijn geconfigureerd voor Azure Monitor voor VM's.

Zie [werk ruimten beheren](../../azure-monitor/platform/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>Azure Monitor voor VM's inschakelen (preview)

Schakel Azure Monitor voor VM's in met een van de methoden die in deze tabel worden beschreven:

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Eén Azure VM of schaalset voor virtuele machines | [Inschakelen vanaf de VM](vminsights-enable-single-vm.md) | U kunt één virtuele Azure-machine inschakelen door **inzichten (preview)** rechtstreeks te selecteren op basis van de VM of virtuele-machine schaalset. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Inschakelen via Azure Policy](vminsights-enable-at-scale-policy.md) | U kunt meerdere virtuele machines van Azure inschakelen met behulp van Azure Policy en beschik bare beleids definities. |
| Meerdere Azure Vm's of virtuele-machine schaal sets | [Azure PowerShell-of Azure Resource Manager-sjablonen inschakelen](vminsights-enable-at-scale-powershell.md) | U kunt meerdere Azure Vm's of virtuele-machine schaal sets inschakelen voor een opgegeven abonnement of resource groep met behulp van Azure PowerShell-of Azure Resource Manager sjablonen. |
| Hybride cloud | [Inschakelen voor de hybride omgeving](vminsights-enable-hybrid-cloud.md) | U kunt implementeren op Vm's of fysieke computers die worden gehost in uw Data Center of in andere Cloud omgevingen. |

## <a name="performance-counters-enabled"></a>Prestatie meter items ingeschakeld 

Azure Monitor voor VM's configureert een Log Analytics werk ruimte voor het verzamelen van de prestatie meter items die worden gebruikt. De volgende tabellen geven een lijst van de objecten en tellers die elke 60 seconden worden verzameld.

### <a name="windows-performance-counters"></a>Windows-prestatiemeteritems

|Object naam |Tellernaam |
|------------|-------------|
|LogicalDisk |Percentage beschik bare ruimte |
|LogicalDisk |Gemiddelde Lees tijd schijf |
|LogicalDisk |Gemiddelde tijd schijf overdracht |
|LogicalDisk |Gemiddelde schrijf tijd schijf |
|LogicalDisk |Geschreven en gelezen bytes per seconde |
|LogicalDisk |Gelezen bytes per seconde |
|LogicalDisk |Lees bewerkingen per seconde |
|LogicalDisk |Schijf overdrachten per seconde |
|LogicalDisk |Geschreven bytes per seconde |
|LogicalDisk |Schrijf bewerkingen per seconde |
|LogicalDisk |Beschik bare mega bytes |
|Geheugen |Beschik bare Mbytes |
|Netwerk adapter |Ontvangen bytes per seconde |
|Netwerk adapter |Verzonden bytes per seconde |
|Processor |Percentage processor tijd |

### <a name="linux-performance-counters"></a>Linux-prestatie meter items

|Object naam |Tellernaam |
|------------|-------------|
|Logische schijf |Percentage gebruikte ruimte |
|Logische schijf |Gelezen bytes per seconde |
|Logische schijf |Lees bewerkingen per seconde |
|Logische schijf |Schijf overdrachten per seconde |
|Logische schijf |Geschreven bytes per seconde |
|Logische schijf |Schrijf bewerkingen per seconde |
|Logische schijf |Beschik bare mega bytes |
|Logische schijf |Bytes van logische schijf per seconde |
|Geheugen |Beschikbaar geheugen in mega bytes |
|Netwerk |Totaal aantal ontvangen bytes |
|Netwerk |Totaal aantal verzonden bytes |
|Processor |Percentage processor tijd |

## <a name="management-packs"></a>Management packs

Wanneer Azure Monitor voor VM's is ingeschakeld en geconfigureerd met een Log Analytics-werk ruimte, wordt een management pack doorgestuurd naar alle Windows-computers die aan die werk ruimte rapporteren. Als u [uw System Center Operations Manager-beheer groep hebt geïntegreerd](../../azure-monitor/platform/om-agents.md) met de log Analytics-werk ruimte, wordt de servicetoewijzing Management Pack van de beheer groep geïmplementeerd naar de Windows-computers die rapporteren aan de beheer groep.  

De management pack heet *micro soft. intelligence packs. ApplicationDependencyMonitor*. De map die is geschreven naar `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\`. De gegevens bron die door de management pack wordt gebruikt, is `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`.

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiks gegevens

Micro soft verzamelt automatisch gebruiks-en prestatie gegevens via uw gebruik van de Azure Monitor service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren. 

De kaart functie bevat gegevens over de configuratie van uw software om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden. De gegevens bevatten informatie zoals het besturings systeem en de versie, het IP-adres, de DNS-naam en de naam van het werk station. Micro soft verzamelt geen namen, adressen of andere contact gegevens.

Zie de [privacyverklaring voor micro soft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132)voor meer informatie over het verzamelen en gebruiken van gegevens.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

Nu u bewaking hebt ingeschakeld voor uw virtuele machine, zijn bewakings gegevens beschikbaar voor analyse in Azure Monitor voor VM's.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure monitor voor VM's status weer geven](vminsights-health.md)voor meer informatie over het gebruik van de status functie. Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven.
