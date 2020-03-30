---
title: Back-up maken en herstellen voor SQL Server in Azure VM's | Microsoft Documenten
description: Beschrijft back-up- en hersteloverwegingen voor SQL Server-databases die worden uitgevoerd op Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249774"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Back-upmaken en herstellen voor SQL Server in Azure Virtual Machines

In dit artikel vindt u richtlijnen voor de back-up- en herstelopties die beschikbaar zijn voor SQL Server die in een virtuele Windows-machine in Azure worden uitgevoerd. Azure Storage onderhoudt drie kopieën van elke Azure VM-schijf om bescherming te garanderen tegen gegevensverlies of fysieke gegevensbeschadiging. In tegenstelling tot on-premises hoeft u zich dus niet te concentreren op hardwarefouten. U moet echter nog steeds een back-up maken van uw SQL Server-databases om te beschermen tegen toepassings- of gebruikersfouten, zoals onbedoelde gegevensinvoegingen of verwijderingen. In deze situatie is het belangrijk om te kunnen herstellen naar een specifiek punt in de tijd.

Het eerste deel van dit artikel geeft een overzicht van de beschikbare back-up- en herstelopties. Dit wordt gevolgd door secties die meer informatie over elke strategie geven.

## <a name="backup-and-restore-options"></a>Back-up- en herstelopties

In de volgende tabel vindt u informatie over verschillende back-up- en herstelopties voor SQL Server die op Azure VM's wordt uitgevoerd:

| Strategie | SQL-versies | Beschrijving |
|---|---|---|
| [Geautomatiseerde back-up](#automated) | 2014<br/> 2016<br/> 2017 | Met geautomatiseerde back-ups u regelmatig back-ups plannen voor alle databases op een SQL Server VM. Back-ups worden maximaal 30 dagen opgeslagen in Azure-opslag. Vanaf SQL Server 2016 biedt Automated Backup v2 extra opties, zoals het configureren van handmatige planning en de frequentie van volledige en logboekback-ups. |
| [Azure Backup voor SQL-VM's](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup biedt een back-upmogelijkheid van enterprise-klasse voor SQL Server die wordt uitgevoerd in Azure VM's. Met deze service u back-ups voor meerdere servers en duizenden databases centraal beheren. Databases kunnen worden hersteld naar een specifiek punt in de tijd in de portal. Het biedt een aanpasbaar bewaarbeleid dat back-ups jarenlang kan onderhouden. |
| [Handmatige back-up](#manual) | Alle | Afhankelijk van uw versie van SQL Server zijn er verschillende technieken om SQL Server handmatig te back-upten en te herstellen die op een Azure VM wordt uitgevoerd. In dit scenario bent u verantwoordelijk voor de manier waarop een back-up van uw databases wordt gemaakt en de opslaglocatie en het beheer van deze back-ups. |

In de volgende secties wordt elke optie nader beschreven. Het laatste gedeelte van dit artikel geeft een samenvatting in de vorm van een functiematrix.

## <a name="automated-backup"></a><a id="automated"></a>Geautomatiseerde back-up

Automated Backup biedt een automatische back-upservice voor SQL Server Standard- en Enterprise-edities die worden uitgevoerd in een Windows VM in Azure. Deze service wordt geleverd door de [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md), die automatisch wordt geïnstalleerd op SQL Server Windows virtuele machine afbeeldingen in de Azure-portal.

Van alle databases wordt een back-up gemaakt van een Azure-opslagaccount dat u configureert. Back-ups kunnen tot 30 dagen worden versleuteld en bewaard.

SQL Server 2016 en hogere VM's bieden meer aanpassingsopties met Automated Backup v2. Deze verbeteringen omvatten:

- Back-ups van systeemdatabase
- Handmatig back-upschema en tijdvenster
- Volledige en log-bestandsback-upfrequentie

Als u een database wilt herstellen, moet u het vereiste back-upbestand(en) in het opslagaccount zoeken en een herstel uitvoeren op uw SQL VM met SQL Server Management Studio (SSMS) of Transact-SQL-opdrachten.

Zie een van de volgende artikelen voor meer informatie over het configureren van geautomatiseerde back-ups voor SQL VM's:

- **SQL Server 2016/2017**: [Geautomatiseerde back-up v2 voor Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Geautomatiseerde back-up voor virtuele SQL Server 2014-machines](virtual-machines-windows-sql-automated-backup.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Azure Backup voor SQL VM's

[Azure Backup](/azure/backup/) biedt een back-upmogelijkheid van enterprise-klasse voor SQL Server die wordt uitgevoerd in Azure VM's. Alle back-ups worden opgeslagen en beheerd in een kluis van Recovery Services. Er zijn verschillende voordelen die deze oplossing biedt, vooral voor ondernemingen:

- **Back-up met zero-infrastructure:** u hoeft geen back-upservers of opslaglocaties te beheren.
- **Schaal:** Bescherm veel SQL VM's en duizenden databases.
- **Pay-As-You-Go:** Deze mogelijkheid is een aparte service die wordt geleverd door Azure Backup, maar zoals bij alle Azure-services betaalt u alleen voor wat u gebruikt.
- **Centraal beheer en bewaking:** beheer al uw back-ups centraal, inclusief andere workloads die Azure Backup ondersteunt, vanuit één dashboard in Azure.
- **Beleidsgestuurde back-up en retentie:** maak standaard back-upbeleid voor regelmatige back-ups. Stel bewaarbeleid op om back-ups jarenlang te onderhouden.
- **Ondersteuning voor SQL Always On:** Detecteer en bescherm een SQL Server Always On-configuratie en honoreer de back-upvoorkeur voor back-upbeschikbaarheidsgroep.
- **RPO (15 minuten Herstelpuntdoelstelling):** SQL-transactielogboekback-ups tot elke 15 minuten configureren.
- **Moment in de tijd herstellen:** Gebruik de portal om databases te herstellen naar een specifiek punt in de tijd zonder handmatig meerdere volledige, differentiële en logboekbackups handmatig te herstellen.
- **Geconsolideerde e-mailwaarschuwingen voor fouten:** Configureer geconsolideerde e-mailmeldingen voor eventuele fouten.
- **Op rollen gebaseerd toegangscontrole:** bepaal wie back-up- en herstelbewerkingen via de portal kan beheren.

Bekijk de volgende video voor een snel overzicht van hoe het werkt samen met een demo:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Deze Azure Backup-oplossing voor SQL VM's is algemeen beschikbaar. Zie [Back-up van SQL Server-database naar Azure](../../../backup/backup-azure-sql-database.md)voor meer informatie.

## <a name="manual-backup"></a><a id="manual"></a>Handmatige back-up

Als u handmatig back-up- en herstelbewerkingen op uw SQL-VM's wilt beheren, zijn er verschillende opties, afhankelijk van de versie van SQL Server die u gebruikt. Zie een van de volgende artikelen op basis van uw versie van SQL Server voor een overzicht van back-up en herstel:

- [Back-up maken en herstellen voor SQL Server 2016 en hoger](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Back-up maken en herstellen voor SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Back-up maken en herstellen voor SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Back-up maken en herstellen voor SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Back-up maken en herstellen voor SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

In de volgende secties worden verschillende handmatige back-up- en herstelopties nader beschreven.

### <a name="backup-to-attached-disks"></a>Back-up naar aangesloten schijven

Voor SQL Server die in Azure VM's wordt uitgevoerd, u native back-up- en hersteltechnieken gebruiken met gekoppelde schijven op de VM voor de bestemming van de back-upbestanden. Er is echter een limiet aan het aantal schijven dat u koppelen aan een virtuele Azure-machine, op basis van de [grootte van de virtuele machine.](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Er is ook de overhead van schijfbeheer te overwegen.

Zie [Een volledige databaseback-up maken](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)voor een voorbeeld van het handmatig maken van een volledige databaseback-up met SQL Server Management Studio (SSMS) of Transact-SQL.

### <a name="backup-to-url"></a>Back-up naar URL

Vanaf SQL Server 2012 SP1 CU2 u een back-up maken en rechtstreeks herstellen naar Microsoft Azure Blob-opslag, die ook wel back-up naar URL wordt genoemd. SQL Server 2016 introduceerde ook de volgende verbeteringen voor deze functie:

| Verbetering 2016 | Details |
| --- | --- |
| **Striping** |Wanneer u een back-up maakt van Microsoft Azure blob-opslag, ondersteunt SQL Server 2016 back-ups van meerdere blobs om back-ups van grote databases mogelijk te maken, tot een maximum van 12,8 TB. |
| **Momentopnameback-up** |Door het gebruik van Azure-snapshots biedt SQL Server File-Snapshot Backup bijna onmiddellijke back-ups en snelle herstelingen voor databasebestanden die zijn opgeslagen met de Azure Blob-opslagservice. Met deze mogelijkheid u uw back-up- en herstelbeleid vereenvoudigen. File-snapshot back-up ondersteunt ook point-in-time restore. Zie [Momentopnameback-ups voor databasebestanden in Azure voor](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)meer informatie. |

Zie de volgende artikelen op basis van uw versie van SQL Server voor meer informatie:

- **SQL Server 2016/2017**: [SQL Server Backup naar URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Beheerde back-up

Vanaf SQL Server 2014 automatiseert Managed Backup het maken van back-ups voor Azure-opslag. Achter de schermen maakt Managed Backup gebruik van de functie Back-up naar URL die in het vorige gedeelte van dit artikel is beschreven. Managed Backup is ook de onderliggende functie die de SQL Server VM Automated Backup-service ondersteunt.

Vanaf SQL Server 2016 kreeg Managed backup extra opties voor planning, systeemdatabaseback-up en volledige en logback-upfrequentie.

Zie een van de volgende artikelen op basis van uw versie van SQL Server voor meer informatie:

- [Beheerde back-up naar Microsoft Azure voor SQL Server 2016 en hoger](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Beheerde back-up naar Microsoft Azure voor SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Beslissingsmatrix

In de volgende tabel worden de mogelijkheden van elke back-up- en hersteloptie voor virtuele SQL Server-machines in Azure samengevat.

|| **Geautomatiseerde back-up** | **Azure Backup voor SQL** | **Handmatige back-up** |
|---|---|---|---|
| Vereist extra Azure-service |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Back-upbeleid configureren in Azure-portal | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Databases herstellen in Azure-portal |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Meerdere servers in één dashboard beheren |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Terugzetten naar eerder tijdstip | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Doelstelling van 15 minuten herstelpunt (RPO) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Beleid voor back-upbehoud op korte termijn (dagen) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Beleid voor back-upbehoud op lange termijn (maanden, jaren) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Ingebouwde ondersteuning voor SQL Server Always On |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Back-up naar Azure Storage-account(s) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisch) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatisch) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(klant beheerd) |
| Beheer van opslag- en back-upbestanden | | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Back-up naar gekoppelde schijven op de VM |   |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Centrale aanpasbare back-uprapporten |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Geconsolideerde e-mailwaarschuwingen voor fouten |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Controle aanpassen op basis van Azure Monitor-logboeken |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Back-uptaken controleren met SSMS- of Transact-SQL-scripts | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Databases herstellen met SSMS- of Transact-SQL-scripts | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Ja](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Volgende stappen

Als u uw implementatie van SQL Server in een Azure VM plant, vindt u provisioningsrichtlijnen in de volgende handleiding: [Een virtuele windows server inrichten in de Azure-portal.](virtual-machines-windows-portal-sql-server-provision.md)

Hoewel back-up en herstel kunnen worden gebruikt om uw gegevens te migreren, zijn er mogelijk eenvoudigere gegevensmigratiepaden naar SQL Server op een Azure VM. Zie [Een database migreren naar SQL Server op een Azure VM](virtual-machines-windows-migrate-sql.md)voor een volledige bespreking van migratieopties en aanbevelingen.
