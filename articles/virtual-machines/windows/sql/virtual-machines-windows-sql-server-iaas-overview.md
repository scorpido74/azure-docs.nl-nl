---
title: Overzicht van SQL Server op virtuele Windows-machines in Azure | Microsoft Docs
description: Meer informatie over het uitvoeren van volledige SQL Server-edities op Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7d8d1505a268976161636abd0ed2d24398978284
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374291"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Wat is SQL Server op Azure Virtual Machines? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

Met [SQL Server op Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) kunt u volledige versies van SQL Server in de cloud gebruiken zonder dat u on-premises hardware hoeft te beheren. SQL Server-VM's vereenvoudigen ook de licentiekosten als u betaalt naar gebruik.

Virtuele machines van Azure worden uitgevoerd in talloze verschillende [geografische regio's](https://azure.microsoft.com/regions/) ter wereld. Er zijn ook diverse [formaten](../sizes.md). In de galerie met installatiekopieën voor virtuele machines kunt u een SQL Server-VM met de juiste versie, de juiste editie en het juiste besturingssysteem maken. Daarom zijn virtuele machines een goede optie voor diverse verschillende SQL Server-werkbelastingen.

## <a name="automated-updates"></a>Automatische updates

Azure VM's voor SQL Server kunnen [geautomatiseerde patching](virtual-machines-windows-sql-automated-patching.md) gebruiken om een onderhoudsvenster te plannen voor het automatisch installeren van belangrijke Windows- en SQL Server-updates.

## <a name="automated-backups"></a>Automatische back-ups

Azure VM's voor SQL Server kunnen gebruikmaken van [geautomatiseerde back-ups](virtual-machines-windows-sql-automated-backup-v2.md), waarbij er regelmatig back-ups in Blob Storage worden gemaakt van uw database. U kunt deze techniek ook handmatig gebruiken. Zie [Azure Storage gebruiken voor back-up en herstel van SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md) voor meer informatie.

## <a name="high-availability"></a>Hoge beschikbaarheid

Als u hoge beschikbaarheid nodig hebt, overweeg dan SQL Server-beschikbaarheidsgroepen te configureren. Hierbij combineert u meerdere virtuele machines van Azure voor SQL Server tot een virtueel netwerk. U kunt uw oplossing voor hoge beschikbaarheid configureren of sjablonen in Azure Portal gebruiken voor automatische configuratie. Zie [Hoge beschikbaarheid en herstel na een noodgeval voor SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md) voor een overzicht van alle opties voor hoge beschikbaarheid.

## <a name="performance"></a>Prestaties

Virtuele Azure-machines bieden verschillende groottes om te voldoen aan diverse workloadeisen. Virtuele SQL-machines bieden bovendien een geautomatiseerde opslagconfiguratie die is geoptimaliseerd voor uw prestatievereisten. Zie [Opslagconfiguratie voor SQL Server-VM's](virtual-machines-windows-sql-server-storage-configuration.md) voor meer informatie over het configureren van opslag voor virtuele SQL-machines. Zie [Best practices voor prestaties voor SQL Server op virtuele machines van Azure](virtual-machines-windows-sql-performance.md) om de prestaties af te stemmen.

## <a name="get-started-with-sql-vms"></a>Aan de slag met VM's voor SQL

Als u aan de slag wilt, kiest u een installatiekopie voor een virtuele machine voor SQL Server met de vereiste versie, de vereiste editie en het vereiste besturingssysteem. De volgende gedeelten bevatten directe koppelingen naar Azure Portal voor de galerie met installatiekopieën van virtuele SQL Server-machines.

> [!TIP]
> Zie [Richtlijnen voor prijzen van virtuele Azure-machines met SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md) voor informatie over de prijzen voor SQL-installatiekopieën. 

### <a id="payasyougo"></a> Betalen per gebruik
De volgende tabel bevat een matrix met SQL Server-installatiekopieën voor betalen naar gebruik.

| Versie | Besturingssysteem | Versie |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enter prise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Zie [Overzicht van SQL Server op virtuele Azure-machines (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md) voor de beschikbare installatiekopieën van SQL Server voor virtuele Linux-machines.

> [!NOTE]
> Het is nu mogelijk om de licentie voor een SQL Server-VM waarbij per gebruik wordt betaald, te wijzigen in uw eigen licentie. Zie [Het licentiemodel voor een SQL-VM wijzigen](virtual-machines-windows-sql-ahb.md) voor meer informatie. 

### <a id="BYOL"></a> Neem uw eigen licentie mee
U kunt ook uw eigen licentie gebruiken (Bring Your Own Licence, BYOL). In dit scenario betaalt u alleen voor de virtuele machine, zonder eventuele extra kosten voor SQL Server-licentieverlening.  Als u uw eigen licentie hebt, kan dit u in de loop van de tijd geld besparen voor doorlopende productieworkloads. Zie [Prijsinformatie voor Azure VM's van SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md#byol) voor de vereisten om van deze optie gebruik te maken.

Als u uw eigen licentie wilt meenemen, converteert u een SQL-VM waarbij per gebruik wordt betaald, of implementeert u een installatiekopie met **{BYOL}** vooraf ingesteld. Zie [Het licentiemodel voor een SQL-VM wijzigen](virtual-machines-windows-sql-ahb.md) voor meer informatie over het schakelen van uw licentiemodel tussen betalen per gebruik en BYOL. 

| Versie | Besturingssysteem | Versie |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Het is mogelijk om een oudere installatie kopie te implementeren van SQL Server die niet beschikbaar is in de Azure Portal met behulp van Power shell. Gebruik de volgende opdracht om alle beschikbare installatiekopieën te bekijken met behulp van Powershell:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Raadpleeg voor meer informatie over het implementeren van SQL Server-VM's met behulp van PowerShell [Over het inrichten van SQL Server-machines met Azure PowerShell](virtual-machines-windows-ps-sql-create.md).


### <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine
Nadat u de SQL Server-VM hebt gemaakt, verbindt u deze met toepassingen of hulpprogramma's, zoals SQL Server Management Studio (SSMS). Zie [Verbinding maken met een virtuele SQL Server-machine op Azure](virtual-machines-windows-sql-connect.md) voor instructies.

### <a name="migrate-your-data"></a>Uw gegevens migreren
Als u een bestaande database hebt, doet u er verstandig aan deze te verplaatsen naar uw nieuw ingerichte virtuele SQL-machine. Zie [Een database migreren naar SQL Server op een virtuele machine in Azure](virtual-machines-windows-migrate-sql.md) voor een lijst met migratieopties en begeleiding bij de migratie.

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure SQL-resources maken en beheren met de Azure Portal

De Azure Portal biedt één pagina waar u [al uw Azure SQL-resources](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) kunt beheren, met inbegrip van uw virtuele SQL-machines.

Om toegang te krijgen tot de pagina **Azure SQL-resources** , selecteert u **Azure SQL** in het menu Azure portal of zoekt en selecteert u **Azure SQL** op een wille keurige pagina.

![Zoeken naar Azure SQL](./media/quickstart-sql-vm-create-portal/search-for-azure-sql.png)

> [!NOTE]
> **Azure SQL** biedt een snelle en eenvoudige manier om toegang te krijgen tot al uw SQL-data bases, elastische Pools, database servers, SQL Managed instances en virtuele SQL-machines. Azure SQL is geen service of resource. 

Als u bestaande resources wilt beheren, selecteert u het gewenste item in de lijst. Als u nieuwe Azure SQL-resources wilt maken, selecteert u **+ toevoegen**. 

![Azure SQL-resource maken](./media/quickstart-sql-vm-create-portal/create-azure-sql-resource.png)

Nadat u **+ toevoegen**hebt geselecteerd, kunt u aanvullende informatie over de verschillende opties bekijken door **Details weer geven** op een wille keurige tegel te selecteren.

![Details van de tegel data bases](./media/quickstart-sql-vm-create-portal/sql-vm-details.png)

Zie deze artikelen voor meer informatie:

- [Een afzonderlijke data base maken](../../../sql-database/sql-database-single-database-get-started.md)
- [Een pool voor Elastic Database maken](../../../sql-database/sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Een beheerd exemplaar maken](../../../sql-database/sql-database-managed-instance-get-started.md)
- [Een virtuele SQL-machine maken](quickstart-sql-vm-create-portal.md)

## <a id="lifecycle"></a> Vernieuwingsbeleid voor installatiekopieën van virtuele SQL-machines
Azure onderhoudt slechts één installatiekopie van een virtuele machine voor elk ondersteunde combinatie van besturingssysteem, versie en editie. Dit betekent dat installatiekopieën na verloop van tijd worden vernieuwd en dat oudere installatiekopieën worden verwijderd. Zie de sectie **Installatiekopieën** in de [veelgestelde vragen over virtuele machines met SQL Server](virtual-machines-windows-sql-server-iaas-faq.md#images) voor meer informatie.

## <a name="customer-experience-improvement-program-ceip"></a>Programma voor kwaliteitsverbetering (CEIP)
Het programma voor kwaliteitsverbetering (CEIP) is standaard ingeschakeld. Met dit programma worden regelmatig rapporten naar Microsoft verzonden zodat SQL Server kan worden verbeterd. Er hoeft voor het programma voor kwaliteitsverbetering geen beheertaak worden uitgevoerd, tenzij u het na het inrichten wilt uitschakelen. U kunt het programma voor kwaliteitsverbetering aanpassen of uitschakelen door via Extern bureaublad verbinding te maken met de virtuele machine. Voer vervolgens het hulpprogramma **SQL Server-fouten- en gebruiksrapportage** uit. Volg de instructies voor het uitschakelen van rapportage. Zie de [privacyverklaring van SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement) voor meer informatie over gegevensverzameling.

## <a name="related-products-and-services"></a>Gerelateerde producten en services
### <a name="windows-virtual-machines"></a>Virtuele Windows-machines
* [Overzicht van Virtual Machines](../overview.md)

### <a name="storage"></a>Storage
* [Inleiding tot Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Networking
* [Overzicht van Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentatie over SQL Server](https://docs.microsoft.com/sql/index)
* [Vergelijking met Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met SQL Server op virtuele machines van Azure:

* [Create a SQL Server VM in the Azure portal](quickstart-sql-vm-create-portal.md) (Een SQL Server-VM maken in Azure Portal)

Antwoorden op veelgestelde vragen over SQL-VM's:

* [Veelgestelde vragen over SQL Server op virtuele machines van Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Referentie architecturen weer geven voor het uitvoeren van toepassingen met N-tier op SQL Server in IaaS

* [Windows N-tier-toepassing op Azure met SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Een N-tier-toepassing uitvoeren in meerdere Azure-regio's voor hoge Beschik baarheid](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
