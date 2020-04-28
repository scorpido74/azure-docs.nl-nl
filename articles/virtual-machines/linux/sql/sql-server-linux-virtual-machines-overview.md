---
title: Overzicht van SQL Server op virtuele Linux-machines in Azure | Microsoft Docs
description: Meer informatie over het uitvoeren van volledige SQL Server-edities op virtuele Linux-machines in Azure. U vindt hier rechtstreekse koppelingen naar alle installatiekopieën voor virtuele Linux-machines met SQL Server en gerelateerde inhoud.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d376672336845958fb6434a78177f42aca938229
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70081955"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Overzicht van SQL Server op virtuele machines in Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Met SQL Server op Azure Virtual Machines kunt u volledige versies van SQL Server in de cloud gebruiken zonder dat u on-premises hardware hoeft te beheren. SQL Server-VM's vereenvoudigen ook de licentiekosten als u betaalt naar gebruik.

Virtuele machines van Azure worden uitgevoerd in talloze verschillende [geografische regio's](https://azure.microsoft.com/regions/) ter wereld. Er zijn ook diverse [formaten](../sizes.md). In de galerie met installatiekopieën voor virtuele machines kunt u een SQL Server-VM met de juiste versie, de juiste editie en het juiste besturingssysteem maken. Daarom zijn virtuele machines een goede optie voor diverse verschillende SQL Server-werkbelastingen.

## <a name="get-started-with-sql-vms"></a><a id="create"></a>Aan de slag met SQL-Vm's

Als u aan de slag wilt, kiest u een installatiekopie voor een virtuele machine voor SQL Server met de vereiste versie, de vereiste editie en het vereiste besturingssysteem. De volgende gedeelten bevatten directe koppelingen naar Azure Portal voor de galerie met installatiekopieën van virtuele SQL Server-machines.

> [!TIP]
> Zie [de prijzenpagina voor virtuele Linux-machines met SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) voor informatie over de prijzen voor SQL-installatiekopieën.

| Versie | Besturingssysteem | Editie |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Zie [Overzicht van SQL Server op virtuele Azure-machines (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) voor de beschikbare installatiekopieën van SQL Server voor virtuele Windows-machines.

## <a name="installed-packages"></a><a id="packages"></a> Geïnstalleerde pakketten

Wanneer u SQL Server op Linux configureert, installeert u het database-engine-pakket en vervolgens verschillende optionele pakketten afhankelijk van uw vereisten. De installatiekopieën van SQL Server voor de virtuele Linux-machines installeren de meeste pakketten automatisch voor u. De volgende tabel laat zien welke pakketten zijn geïnstalleerd voor elke distributie.

| Distributie | [Database Engine](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Hulpprogramma's](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Zoek opdracht in volledige tekst](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA-invoegtoepassing](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nee](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nee](./media/sql-server-linux-virtual-machines-overview/no.png) | ![nee](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![ja](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Gerelateerde producten en services

### <a name="linux-virtual-machines"></a>Linux Virtual Machines

* [Overzicht van Virtual Machines](../overview.md)

### <a name="storage"></a>Storage

* [Inleiding tot Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netwerken

* [Overzicht van Virtual Network](../../../virtual-network/virtual-networks-overview.md)
* [IP-adressen in Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Een Fully Qualified Domain Name maken in Azure Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentatie voor SQL Server op Linux](https://docs.microsoft.com/sql/linux)
* [Vergelijking met Azure SQL Database](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Volgende stappen

Aan de slag met SQL Server op virtuele Linux-machines van Azure:

* [Create a SQL Server VM in the Azure portal](provision-sql-server-linux-virtual-machine.md) (Een SQL Server-VM maken in Azure Portal)

Vind antwoorden op veelgestelde vragen over virtuele SQL-machines op Linux:

* [Veelgestelde vragen over SQL Server op virtuele Linux-machines van Azure](sql-server-linux-faq.md)
