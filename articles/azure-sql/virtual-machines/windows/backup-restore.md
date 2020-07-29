---
title: Back-ups maken en herstellen voor SQL Server op virtuele machines in azure | Microsoft Docs
description: Beschrijft de overwegingen voor back-up en herstel voor SQL Server-data bases die worden uitgevoerd op Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 6a03a91eeb9296e60aa147f97634a15e8d344209
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293036"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Back-ups maken en herstellen voor SQL Server op virtuele machines in azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dit artikel bevat richt lijnen voor de opties voor back-up en herstel die beschikbaar zijn voor SQL Server die worden uitgevoerd op een virtuele Windows-machine (VM) in Azure. Azure Storage onderhoudt drie exemplaren van elke Azure VM-schijf om bescherming tegen gegevens verlies of fysieke beschadiging van gegevens te garanderen. In tegens telling tot SQL Server on-premises hoeft u zich niet te richten op hardwarestoringen. U moet echter wel een back-up maken van uw SQL Server-data bases om te beschermen tegen toepassings-of gebruikers fouten, zoals het invoegen of verwijderen van gegevens per ongeluk. In dit geval is het belang rijk dat u kunt herstellen naar een bepaald tijdstip.

In het eerste deel van dit artikel vindt u een overzicht van de beschik bare opties voor back-up en herstel. Dit wordt gevolgd door secties die meer informatie over elke strategie bieden.

## <a name="backup-and-restore-options"></a>Opties voor back-up en herstel

De volgende tabel bevat informatie over verschillende opties voor back-up en herstel voor SQL Server op Azure-Vm's:

| Strategie | SQL-versies | Beschrijving |
|---|---|---|
| [Automatische back-up](#automated) | 2014<br/> 2016<br/> 2017 | Met automatische back-up kunt u regel matige back-ups plannen voor alle data bases op een SQL Server-VM. Back-ups worden Maxi maal 30 dagen opgeslagen in azure Storage. Vanaf SQL Server 2016 biedt automatische back-up v2 extra opties, zoals het configureren van hand matige planning en de frequentie van volledige en logboek back-ups. |
| [Azure Backup voor SQL-VM's](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup biedt een back-upfunctie voor bedrijfs klasse voor SQL Server op Azure-Vm's. Met deze service kunt u back-ups centraal beheren voor meerdere servers en duizenden data bases. Data bases kunnen worden hersteld naar een bepaald punt in de tijd in de portal. Het biedt een aanpasbaar Bewaar beleid waarmee back-ups voor jaren kunnen worden onderhouden. |
| [Hand matige back-up](#manual) | Alle | Afhankelijk van uw versie van SQL Server zijn er verschillende technieken om hand matig back-ups te maken en te herstellen SQL Server op Azure VM. In dit scenario bent u verantwoordelijk voor het maken van een back-up van uw data bases en de opslag locatie en het beheer van deze back-ups. |

In de volgende secties wordt elke optie uitvoeriger beschreven. De laatste sectie van dit artikel bevat een samen vatting in de vorm van een functie matrix.

## <a name="automated-backup"></a><a id="automated"></a>Automatische back-up

Automatische back-up biedt een automatische back-upservice voor SQL Server Standard-en Enter prise-edities die worden uitgevoerd op een Windows-VM in Azure. Deze service wordt verzorgd door de [SQL Server IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md), die automatisch wordt geïnstalleerd op SQL Server installatie kopieën van virtuele Windows-machines in de Azure Portal.

Voor alle data bases wordt een back-up gemaakt naar een Azure Storage-account dat u configureert. Back-ups kunnen Maxi maal 30 dagen worden versleuteld en bewaard.

SQL Server 2016 en hogere Vm's bieden meer aanpassings opties met automatische back-ups v2. Deze verbeteringen zijn onder andere:

- Back-ups van de systeem database
- Hand matig back-upschema en tijd venster
- Volledige back-upfrequentie en logboek bestand

Als u een Data Base wilt herstellen, moet u de vereiste back-upbestand (en) in het opslag account zoeken en een herstel bewerking uitvoeren op uw SQL-VM met behulp van SQL Server Management Studio (SSMS) of Transact-SQL-opdrachten.

Raadpleeg een van de volgende artikelen voor meer informatie over het configureren van automatische back-ups voor virtuele SQL-machines:

- **SQL Server 2016/2017**: [automatische back-up v2 voor Azure virtual machines](automated-backup.md)
- **SQL Server 2014**: [geautomatiseerde back-up voor SQL Server 2014 virtual machines](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Azure Backup voor virtuele SQL-machines

[Azure backup](/azure/backup/) biedt een back-upfunctie voor bedrijfs klasse voor SQL Server op Azure-vm's. Alle back-ups worden opgeslagen en beheerd in een Recovery Services kluis. Er zijn verschillende voor delen die deze oplossing biedt, met name voor ondernemingen:

- Geen back-up van de **infra structuur**: u hoeft geen back-upservers of opslag locaties te beheren.
- **Schaal**: Beveilig veel SQL-vm's en duizenden data bases.
- **Betalen**naar gebruik: deze mogelijkheid is een afzonderlijke service van Azure backup, maar net als bij alle Azure-Services betaalt u alleen voor wat u gebruikt.
- **Centraal beheer en controle**: u kunt al uw back-ups centraal beheren, met inbegrip van andere werk belastingen die Azure Backup ondersteunt, vanuit één dash board in Azure.
- Op **beleid gebaseerde back-up en retentie**: Maak standaard back-upbeleid voor reguliere back-ups. Bewaar beleid voor het bewaren van back-ups voor jaren.
- **Ondersteuning voor SQL always on**: detecteer en beveilig een SQL Server altijd op configuratie en zorg ervoor dat de back-upvoorkeur voor Back-upgroepen wordt geaccepteerd.
- **herstel punt doelstelling van 15 minuten (RPO)**: Configureer back-ups van SQL-transactie logboeken tot Maxi maal elke 15 minuten.
- **Herstel**naar een bepaald tijdstip: gebruik de portal om data bases te herstellen naar een specifiek punt, zonder dat u hand matig meerdere volledige, differentiële en logboek back-ups hoeft te herstellen.
- **Geconsolideerde e-mail waarschuwingen voor mislukte pogingen**: Configureer geconsolideerde e-mail meldingen voor eventuele fouten.
- **Op rollen gebaseerd toegangs beheer**: Bepaal wie back-up-en herstel bewerkingen kan beheren via de portal.

Bekijk de volgende video voor een kort overzicht van hoe het werkt samen met een demo:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Deze Azure Backup oplossing voor virtuele SQL-machines is algemeen beschikbaar. Zie [back-ups maken van SQL Server Data Base naar Azure](../../../backup/backup-azure-sql-database.md)voor meer informatie.

## <a name="manual-backup"></a><a id="manual"></a>Hand matige back-up

Als u back-up-en herstel bewerkingen op uw SQL-Vm's hand matig wilt beheren, zijn er verschillende opties, afhankelijk van de versie van SQL Server die u gebruikt. Zie een van de volgende artikelen op basis van uw versie van SQL Server voor een overzicht van het maken en herstellen van back-ups:

- [Back-up en herstel voor SQL Server 2016 en hoger](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Back-up en herstel voor SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Back-up en herstel voor SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Back-up en herstel voor SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Back-up en herstel voor SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

In de volgende secties worden verschillende hand matige back-up-en herstel opties uitvoeriger beschreven.

### <a name="backup-to-attached-disks"></a>Back-up naar gekoppelde schijven

Voor SQL Server op virtuele machines in azure kunt u systeem eigen back-up-en herstel technieken gebruiken met behulp van gekoppelde schijven op de virtuele machine voor het doel van de back-upbestanden. Er is echter een limiet voor het aantal schijven dat u kunt koppelen aan een virtuele machine van Azure, op basis van de [grootte van de virtuele machine](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Er is ook de overhead van schijf beheer die u kunt overwegen.

Zie [een volledige database back-up maken](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)voor een voor beeld van het hand matig maken van een back-up van een volledige data base met behulp van SQL Server Management Studio (SSMS) of Transact-SQL.

### <a name="backup-to-url"></a>Back-up naar URL

Vanaf SQL Server 2012 SP1 CU2 kunt u rechtstreeks een back-up maken en herstellen naar Microsoft Azure Blob-opslag, die ook wel back-up naar URL wordt genoemd. SQL Server 2016 heeft ook de volgende verbeteringen geïntroduceerd voor deze functie:

| 2016-uitbrei ding | Details |
| --- | --- |
| **Striping** |Bij het maken van een back-up naar Microsoft Azure Blob-opslag biedt SQL Server 2016 ondersteuning voor het maken van back-ups naar meerdere blobs voor het inschakelen van back-ups van grote data bases tot Maxi maal 12,8 TB. |
| **Back-up van moment opname** |Dankzij het gebruik van Azure snap shots biedt SQL Server back-up van bestanden moment opnamen bijna momentane back-ups en snelle herstel bewerkingen voor database bestanden die zijn opgeslagen met behulp van de Azure Blob Storage-service. Met deze mogelijkheid kunt u uw back-up-en herstel beleid vereenvoudigen. Back-ups van bestands momentopnamen bieden ook ondersteuning voor herstel naar een bepaald tijdstip. Zie [moment opnamen van back-ups voor database bestanden in azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)voor meer informatie. |

Zie voor meer informatie de een van de volgende artikelen op basis van uw versie van SQL Server:

- **SQL Server 2016/2017**: [back-up naar URL SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014-back-up naar URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012-back-up naar URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Beheerde back-up

Vanaf SQL Server 2014 automatiseert beheerde back-up het maken van back-ups naar Azure Storage. Achter de schermen maakt beheerde back-up gebruik van de functie voor back-up naar URL, zoals beschreven in de vorige sectie van dit artikel. Beheerde back-up is ook de onderliggende functie die ondersteuning biedt voor de geautomatiseerde back-upservice van de SQL Server-machine.

Vanaf SQL Server 2016 heeft beheerde back-up extra opties gekregen voor het plannen, de back-up van de systeem database en de frequentie van de back-up van het logboek.

Zie voor meer informatie een van de volgende artikelen op basis van uw versie van SQL Server:

- [Beheerde back-up naar Microsoft Azure voor SQL Server 2016 en hoger](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Beheerde back-up naar Microsoft Azure voor SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Beslissings matrix

De volgende tabel bevat een overzicht van de mogelijkheden van elke back-up-en herstel optie voor SQL Server virtuele machines in Azure.

| Optie | Automatische back-up | Azure Backup voor SQL | Hand matige back-up |
|---|---|---|---|
| Vereist extra Azure-service |   | ![Ja](./media/backup-restore/yes.png) |   |
| Back-upbeleid configureren in Azure Portal | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) |   |
| Data bases herstellen in Azure Portal |   | ![Ja](./media/backup-restore/yes.png) |   |
| Meerdere servers beheren in één dash board |   | ![Ja](./media/backup-restore/yes.png) |   |
| Terugzetten naar eerder tijdstip | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) |
| Beoogd herstel punt (RPO) van 15 minuten | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) |
| Retentie beleid voor back-ups op korte termijn (dagen) | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) |   |
| Bewaar beleid voor lange termijn back-ups (maanden, jaren) |   | ![Ja](./media/backup-restore/yes.png) |   |
| Ingebouwde ondersteuning voor SQL Server altijd |   | ![Ja](./media/backup-restore/yes.png) |   |
| Back-up naar Azure Storage account (s) | ![Ja](./media/backup-restore/yes.png)geautomatiseerde | ![Ja](./media/backup-restore/yes.png)geautomatiseerde | ![Ja](./media/backup-restore/yes.png)(door de klant beheerd) |
| Beheer van opslag-en back-upbestanden | | ![Ja](./media/backup-restore/yes.png) |  |
| Back-up naar gekoppelde schijven op de VM |   |   | ![Ja](./media/backup-restore/yes.png) |
| Centrale, aanpas bare back-uprapporten |   | ![Ja](./media/backup-restore/yes.png) |   |
| Geconsolideerde e-mail waarschuwingen voor fouten |   | ![Ja](./media/backup-restore/yes.png) |   |
| Bewaking aanpassen op basis van Azure Monitor logboeken |   | ![Ja](./media/backup-restore/yes.png) |   |
| Back-uptaken controleren met SSMS of Transact-SQL-scripts | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) | ![Ja](./media/backup-restore/yes.png) |
| Data bases herstellen met SSMS of Transact-SQL-scripts | ![Ja](./media/backup-restore/yes.png) |   | ![Ja](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Volgende stappen

Als u uw implementatie van SQL Server op de Azure-VM wilt plannen, kunt u de richt lijnen voor het inrichten vinden in de volgende hand leiding: [een Windows SQL Server virtuele machine inrichten in de Azure Portal](create-sql-vm-portal.md).

Hoewel back-up en herstel kan worden gebruikt om uw gegevens te migreren, zijn er mogelijk gemakkelijker gegevens migratie paden om te SQL Server op de VM. Zie [een Data Base migreren naar SQL Server op Azure VM](migrate-to-vm-from-sql-server.md)voor een volledige bespreking van migratie opties en aanbevelingen.
