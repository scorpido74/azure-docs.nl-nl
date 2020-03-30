---
title: Prijsbegeleiding & beheerskosten
description: Biedt aanbevolen procedures voor het kiezen van het juiste SQL Server-prijsmodel voor virtuele machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981717"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Prijsrichtlijnen voor Azure SQL Server-VM's

In dit artikel vindt u prijsrichtlijnen voor [virtuele SQL Server-machines](virtual-machines-windows-sql-server-iaas-overview.md) in Azure. Er zijn verschillende opties die van invloed zijn op de kosten, en het is belangrijk om het juiste beeld te kiezen dat kosten in evenwicht brengt met zakelijke vereisten.

> [!TIP]
> Als u alleen een kostenraming voor een specifieke combinatie van SQL Server-editie en virtuele machinegrootte hoeft te achterhalen, raadpleegt u de prijspagina voor [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) of [Linux.](https://azure.microsoft.com/pricing/details/virtual-machines/linux) Selecteer uw platform en SQL Server-editie in de lijst **OS/Software.**
>
> ![Pagina UI op vm-prijsbepaling](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Of gebruik de [prijscalculator](https://azure.microsoft.com/pricing/#explore-cost) om een virtuele machine toe te voegen en te configureren. 

## <a name="free-licensed-sql-server-editions"></a>SQL Server-edities met gratis licentie

Als u een proof of concept wilt ontwikkelen, testen of bouwen, gebruikt u de gratis gelicentieerde **SQL Server Developer-editie.** Deze editie heeft alle functies van sql server enterprise-editie, zodat u elk type toepassing bouwen en testen. U de developer-editie echter niet in productie uitvoeren. Een SQL Server Developer edition VM brengt alleen kosten met zich mee voor de kosten van de VM, omdat er geen bijbehorende SQL Server-licentiekosten zijn.

Als u een lichtgewicht workload in productie wilt uitvoeren (<4 cores, <1 GB geheugen, <10 GB/database), gebruikt u de sql **server express-editie**met een vrije licentie. Een SQL Server Express edition VM brengt ook alleen kosten met zich mee voor de kosten van de VM.

Voor deze ontwikkelings-/test- en lichtgewicht productieworkloads u ook geld besparen door een kleiner VM-formaat te kiezen dat overeenkomt met deze workloads. De DS1v2 kan een goede keuze in sommige scenario's.

Zie de volgende koppelingen als u een SQL Server 2017 Azure VM met een van deze afbeeldingen wilt maken:

| Platform | Afbeeldingen met vrij gelicentieerde afbeeldingen |
|---|---|
| Windows Server 2016 | [SQL Server 2017-ontwikkelaar Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017-ontwikkelaar Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017-ontwikkelaar Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017-ontwikkelaar Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Betaalde SQL Server-edities

Als u een niet-lichtgewicht productiewerkbelasting hebt, gebruikt u een van de volgende SQL Server-edities:

| SQL Server Edition | Workload |
|-----|-----|
| Web | Kleine websites |
| Standard | Kleine tot middelgrote workloads |
| Enterprise | Grote of bedrijfskritieke workloads|

U hebt twee opties om te betalen voor SQL Server-licenties voor deze edities: *betalen per gebruik* of uw eigen licentie *(BYOL) meenemen.*

## <a name="pay-per-usage"></a>Betalen per gebruik

**Het betalen van de SQL Server-licentie per gebruik** (ook wel **pay as you go**genoemd) betekent dat de kosten per seconde voor het uitvoeren van de Azure VM de kosten van de SQL Server-licentie omvatten. U de prijzen voor de verschillende SQL Server-edities (Web, Standard, Enterprise) bekijken op de azure VM-prijspagina voor [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) of [Linux.](https://azure.microsoft.com/pricing/details/virtual-machines/linux)

De kosten zijn hetzelfde voor alle versies van SQL Server (2012 SP3 tot 2017). De licentiekosten per seconde zijn afhankelijk van het aantal VM-vCPU's.

Het betalen van sql server-licenties per gebruik wordt aanbevolen voor:

- **Tijdelijke of periodieke werkbelastingen**. Bijvoorbeeld een app die een evenement elk jaar een paar maanden moet ondersteunen, of bedrijfsanalyses op maandag.

- **Workloads met een onbekende levensduur of schaal.** Bijvoorbeeld een app die mogelijk niet vereist is in een paar maanden, of waarvoor mogelijk meer of minder rekenkracht nodig is, afhankelijk van de vraag.

Zie de volgende koppelingen als u een SQL Server 2017 Azure VM wilt maken met een van deze pay-as-you-go-afbeeldingen:

| Platform | Gelicentieerde afbeeldingen |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standaard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standaard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standaard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standaard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Wanneer u een virtuele SQL Server-machine maakt in de portal, worden in het venster **Een grootte kiezen** een geschatte kosten weergegeven. Het is belangrijk op te merken dat deze schatting is alleen de rekenkosten voor het uitvoeren van de VM, samen met eventuele OS licentiekosten (Windows of derden Linux besturingssystemen).
>
> ![Vm-grootteblad kiezen](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Het bevat geen extra SQL Server-licentiekosten voor web-, standaard- en Enterprise-edities. Als u de meest nauwkeurige prijsschatting wilt krijgen, selecteert u uw besturingssysteem en SQL Server-editie op de prijspagina voor [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) of [Linux.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

> [!NOTE]
> Het is nu mogelijk om het licentiemodel te wijzigen van pay-per-use om uw eigen licentie (BYOL) en terug te brengen. Zie [Het licentiemodel voor een SQL-VM wijzigen](virtual-machines-windows-sql-ahb.md) voor meer informatie. 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Neem je eigen licentie mee (BYOL)

**Het meenemen van uw eigen SQL Server-licentie via License Mobility**, ook wel **BYOL**genoemd, betekent het gebruik van een bestaande SQL Server-volumelicentie met Software Assurance in een Azure VM. Een SQL Server VM die BYOL gebruikt, brengt alleen kosten in rekening voor de kosten van het uitvoeren van de VM, niet voor SQL Server-licenties, aangezien u al licenties en softwaregarantie hebt aangeschaft via een Volume Licensing-programma.

> [!IMPORTANT]
> BYOL-afbeeldingen vereisen een Enterprise Agreement with Software Assurance. Ze zijn momenteel niet beschikbaar als onderdeel van de Azure Cloud Solution Partner (CSP). CSP-klanten kunnen hun eigen licentie meenemen door een pay-as-you-go-afbeelding te implementeren en vervolgens het [Azure Hybrid Benefit in te schakelen.](virtual-machines-windows-sql-ahb.md)

> [!NOTE]
> De BYOL-afbeeldingen zijn momenteel alleen beschikbaar voor virtuele Windows-machines. U SQL Server echter handmatig installeren op een VM met alleen Linux.However, you can manually install SQL Server on a Linux-only VM. Zie de richtlijnen in de [Veelgestelde vragen over Linux SQL VM](../../linux/sql/sql-server-linux-faq.md).

Het meenemen van uw eigen SQL-licenties via License Mobility wordt aanbevolen voor:

- **Continue workloads**. Bijvoorbeeld een app die 24x7 bedrijfsactiviteiten moet ondersteunen.

- **Workloads met een bekende levensduur en schaal.** Bijvoorbeeld een app die nodig is voor het hele jaar en die de vraag is voorspeld.

Als u BYOL wilt gebruiken met een SQL Server VM, moet u een licentie hebben voor SQL Server Standard of Enterprise and [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), wat een vereiste optie is via sommige volumelicentieprogramma's en een optionele aankoop met anderen. Het prijsniveau dat wordt geboden via Volume Licensing-programma's varieert, afhankelijk van het type overeenkomst en de hoeveelheid en of inzet voor SQL Server. Maar als vuistregel geldt dat het meenemen van uw eigen licentie voor continue productieworkloads de volgende voordelen heeft:

| BYOL-uitkering | Beschrijving |
|-----|-----|
| **Kostenbesparingen** | Het [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) biedt tot 55% besparingen. Zie [Switch-licentiemodel voor](virtual-machines-windows-sql-ahb.md) meer informatie |
| **Gratis passieve secundaire replica** | Een ander voordeel van het meenemen van uw eigen licentie is de [gratis licenties voor één passieve secundaire replica](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server voor doeleinden met hoge beschikbaarheid. Dit vermindert de helft van de licentiekosten van een zeer beschikbare SQL Server-implementatie (bijvoorbeeld met Always On Availability Groups). De rechten om de passieve secundaire draaien worden verstrekt via de Fail-Over Servers Software Assurance voordeel. |

Zie de VM's die zijn gekoppeld aan "{BYOL}" als u een SQL Server 2017 Azure VM wilt maken met een van deze afbeeldingen met uw eigen licentie:

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 Standaard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Laat ons binnen 10 dagen weten hoeveel SQL Server-licenties u in Azure gebruikt. De links naar de vorige afbeeldingen hebben instructies over hoe dit te doen.

> [!NOTE]
> Het is nu mogelijk om het licentiemodel te wijzigen van pay-per-use om uw eigen licentie (BYOL) en terug te brengen. Zie [Het licentiemodel voor een SQL-VM wijzigen](virtual-machines-windows-sql-ahb.md) voor meer informatie. 



## <a name="reduce-costs"></a>Kosten verlagen

Om onnodige kosten te voorkomen, kiest u een optimale grootte van de virtuele machine en overweegt u intermitterende uitschakelingen voor niet-continue workloads.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>Uw VM correct formaat

De licentiekosten van SQL Server zijn direct gerelateerd aan het aantal vCPU's. Kies een VM-grootte die overeenkomt met uw verwachte behoeften voor CPU, geheugen, opslag en I/O-bandbreedte. Zie [Windows VM-formaten](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) en Linux [VM-formaten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor een volledige lijst met opties voor machinegrootte.

Er zijn nieuwe machineformaten die goed werken met bepaalde typen SQL Server-workloads. Deze machines grootte handhaven hoge niveaus van geheugen, opslag, en I/O bandbreedte, maar ze hebben een lagere gevirtualiseerde core count. Denk bijvoorbeeld aan het volgende voorbeeld:

| VM-grootte | vCPUs | Geheugen | Maximale schijven | Maximale I/O-doorvoer | SQL-licentiekosten | Totale kosten (Compute + licenties) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51.200 IOP's of 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51.200 IOP's of 768 MB/s | 75% lager | 57% lager |

> [!IMPORTANT]
> Dit is een point-in-time voorbeeld. Voor de meest recente specificaties verwijzen we je naar de artikelen over machineformaten en de Azure-prijspagina voor [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en [Linux.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

In het vorige voorbeeld u zien dat de specificaties voor **Standard_DS14v2** en **Standard_DS14-4v2** identiek zijn, behalve voor vCPU's. Het achtervoegsel **-4v2** aan het einde van de **Standard_DS14-4v2-machinegrootte** geeft het aantal actieve vCPU's aan. Omdat SQL Server-licentiekosten gekoppeld zijn aan het aantal vCPU's, verlaagt dit de kosten van de VM aanzienlijk in scenario's waarin de extra vCPU's niet nodig zijn. Dit is een voorbeeld, en er zijn veel machineformaten met beperkte vCPU's die zijn geïdentificeerd met dit achtervoegselpatroon. Zie het blogbericht [Waarin nieuwe Azure VM-formaten worden aangekondigd voor meer kosteneffectief databasewerk,](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)voor meer informatie.

### <a name="shut-down-your-vm-when-possible"></a>Uw VM uitschakelen indien mogelijk

Als u workloads gebruikt die niet continu worden uitgevoerd, u overwegen de virtuele machine tijdens de inactieve perioden uit te schakelen. U betaalt alleen voor wat u gebruikt.

Als u bijvoorbeeld SQL Server op een Azure VM gewoon uitprobeert, wilt u geen kosten in rekening brengen door deze per ongeluk wekenlang te laten werken. Een oplossing is het gebruik van de [automatische shutdown functie](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![SQL VM automatisch afsluiten](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatische uitschakeling maakt deel uit van een grotere reeks vergelijkbare functies die worden geleverd door [Azure DevTest Labs.](https://azure.microsoft.com/services/devtest-lab)

Voor andere werkstromen u overwegen azure VM's automatisch af te sluiten en opnieuw te starten met een scriptoplossing, zoals [Azure Automation.](https://azure.microsoft.com/services/automation/)

> [!IMPORTANT]
> Het afsluiten en deallocatie van uw VM is de enige manier om kosten te voorkomen. Gewoon stoppen of het gebruik van energie-opties af te sluiten van de VM nog steeds gebruikskosten.

## <a name="next-steps"></a>Volgende stappen

Zie [Onverwachte kosten voorkomen met Azure-facturering en kostenbeheer](../../../cost-management-billing/manage/getting-started.md)voor algemene richtlijnen voor Azure-prijzen. Zie de prijspagina van Azure VM Azure voor Windows [VM's](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en [Linux VM's](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)voor de nieuwste prijzen voor virtuele machines, waaronder SQL Server.

Zie de volgende artikelen voor een overzicht van SQL Server die op Azure Virtual Machines wordt uitgevoerd:

- [Overzicht van SQL Server op Windows VM's](virtual-machines-windows-sql-server-iaas-overview.md)
- [Overzicht van SQL Server op Linux VM's](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
