---
title: Overzicht van SQL Server op Azure Virtual Machines voor Linux | Microsoft Docs
description: Meer informatie over het uitvoeren van volledige SQL Server-edities op Azure Virtual Machines voor Linux. U vindt hier rechtstreekse koppelingen naar alle installatiekopieën voor virtuele Linux-machines met SQL Server en gerelateerde inhoud.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6e6038e2c0aa4f6b41c4a4da9bde6e98555ceb31
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89613571"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Overzicht van SQL Server op virtuele machines in Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Met SQL Server op Azure Virtual Machines kunt u volledige versies van SQL Server in de Cloud gebruiken zonder dat u on-premises hardware hoeft te beheren. SQL Server-VM's vereenvoudigen ook de licentiekosten als u betaalt naar gebruik.

Virtuele machines van Azure worden uitgevoerd in talloze verschillende [geografische regio's](https://azure.microsoft.com/regions/) ter wereld. Er zijn ook diverse [formaten](../../../virtual-machines/windows/sizes.md). In de galerie met installatiekopieën voor virtuele machines kunt u een SQL Server-VM met de juiste versie, de juiste editie en het juiste besturingssysteem maken. Daarom zijn virtuele machines een goede optie voor diverse verschillende SQL Server-werkbelastingen. 

Als u geen ervaring hebt met Azure SQL, raadpleegt u de *SQL Server op de overzichts video van Azure VM* van onze diep gaande [Azure SQL-video reeks](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Aan de slag met SQL Server-Vm's

Als u aan de slag wilt, kiest u een installatiekopie voor een virtuele machine voor SQL Server met de vereiste versie, de vereiste editie en het vereiste besturingssysteem. De volgende gedeelten bevatten directe koppelingen naar Azure Portal voor de galerie met installatiekopieën van virtuele SQL Server-machines.

> [!TIP]
> Zie [de pagina met prijzen voor Linux-vm's met SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)voor meer informatie over het begrijpen van de prijzen voor SQL Server installatie kopieën.

| Versie | Besturingssysteem | Editie |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enter prise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enter prise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 SP5 | [Enter prise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Zie [overzicht van SQL Server op Azure virtual machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)voor een overzicht van de beschik bare SQL Server installatie kopieën voor virtuele machines voor Windows.

## <a name="installed-packages"></a><a id="packages"></a> Geïnstalleerde pakketten

Wanneer u SQL Server on Linux configureert, installeert u het data base-engine pakket en vervolgens verschillende optionele pakketten, afhankelijk van uw vereisten. De installatiekopieën van SQL Server voor de virtuele Linux-machines installeren de meeste pakketten automatisch voor u. De volgende tabel laat zien welke pakketten zijn geïnstalleerd voor elke distributie.

| Distributie | [Data base-engine](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Hulpprogramma's](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server-Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Zoekopdracht in volledige tekst](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA-invoegtoepassing](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL en data base-engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en hulpprogram ma's](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL-en SQL Server-Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en zoeken in volledige tekst](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL en HA-invoeg toepassing](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES en data base-engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en hulpprogram ma's](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES-en SQL Server-Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en zoeken in volledige tekst](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES en SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES en HA-invoeg toepassing](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu en data base-engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en hulpprogram ma's](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu-en SQL Server-Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en zoeken in volledige tekst](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu en HA-invoeg toepassing](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Gerelateerde producten en services

### <a name="linux-virtual-machines"></a>Virtuele Linux-machines

* [Overzicht van Azure Virtual Machines](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Storage

* [Inleiding tot Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netwerken

* [Overzicht van Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../../virtual-network/public-ip-addresses.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentatie over SQL Server on Linux](https://docs.microsoft.com/sql/linux)
* [Vergelijking met Azure SQL Database](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met SQL Server on Linux virtuele machines:

* [Create a SQL Server VM in the Azure portal](sql-vm-create-portal-quickstart.md) (Een SQL Server-VM maken in Azure Portal)

Antwoorden vinden op veelgestelde vragen over SQL Server Vm's op Linux:

* [Veelgestelde vragen over SQL Server op virtuele machines van Azure](frequently-asked-questions-faq.md)
