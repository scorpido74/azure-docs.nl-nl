---
title: Quickstart - SQL-beheerde instantie
description: Leer hoe u snel aan de slag kunt gaan met Azure SQL Database - beheerd exemplaar
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 602de3e23eb5419958f84b071e2220550d1d04d0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73821720"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Aan de slag met beheerd exemplaar voor Azure SQL Database

Met de implementatieoptie [beheerd exemplaar](sql-database-managed-instance-index.yml) wordt een database gemaakt die vrijwel 100% compatibel is met de meest recente on-premises (Enterprise Edition) SQL Server-database-engine. Het biedt een systeemeigen implementatie van een [virtueel netwerk (VNet)](../virtual-network/virtual-networks-overview.md) die algemene beveiligingskwesties afhandelt, en een [bedrijfsmodel](https://azure.microsoft.com/pricing/details/sql-database/) die geschikt is voor klanten met on-premises SQL Server. In dit artikel leert u hoe u snel een beheerd exemplaar kunt configureren en maken en uw databases kunt migreren.

## <a name="quickstart-overview"></a>Quickstart-overzicht

Met de volgende quickstarts kunt u snel een beheerd exemplaar maken, een virtuele machine of punt-naar-site-VPN-verbinding voor clienttoepassingen configureren en een database herstellen voor uw nieuwe beheerde exemplaar met behulp van een `.bak`-bestand.

### <a name="configure-environment"></a>Omgeving configureren

Als eerste stap moet u uw eerste beheerde exemplaar maken met de netwerkomgeving waarin het wordt geplaatst en verbinding inschakelen vanaf de computer of virtuele machine waarop u query's voor het beheerde exemplaar uitvoert. U kunt de volgende handleidingen gebruiken:

- [Create a managed instance using the Azure portal](sql-database-managed-instance-get-started.md) (Een beheerd exemplaar maken via de Azure-portal). In de Azure-portal configureert u de noodzakelijke parameters (gebruikersnaam/wachtwoord, aantal kernen en maximale hoeveelheid opslagruimte), en maakt u automatisch een Azure-netwerkomgeving zonder de netwerkdetails en infrastructuurvereisten te hoeven kennen. Zorg ervoor dat u een [abonnementstype](sql-database-managed-instance-resource-limits.md#supported-subscription-types) hebt waarmee het beheerde exemplaar momenteel mag worden gemaakt. Als u uw eigen netwerk wilt gebruiken of het netwerk wilt aanpassen, raadpleegt u [configure an existing virtual network for Azure SQL Database managed instance](sql-database-managed-instance-configure-vnet-subnet.md) (Een bestaand virtueel netwerk voor beheerde exemplaren van Azure SQL Database configureren) of [create a virtual network for Azure SQL Database managed instance](sql-database-managed-instance-create-vnet-subnet.md) (Een virtueel netwerk voor beheerde exemplaren van Azure SQL Database maken).
- Een beheerd exemplaar wordt gemaakt in een eigen VNet zonder openbaar eindpunt. Voor toegang tot clienttoepassingen u **een VM maken in hetzelfde VNet (ander subnet)** of een **point-to-site VPN-verbinding met het VNet maken vanaf uw clientcomputer** met behulp van een van deze quickstarts:
  - Schakel [openbaar eindpunt](sql-database-managed-instance-public-endpoint-configure.md) in uw beheerde instantie in om rechtstreeks vanuit uw omgeving toegang te krijgen tot uw gegevens.
  - Ga naar [Azure Virtual Machine in the managed instance VNet](sql-database-managed-instance-configure-vm.md) (Virtuele Azure-machine maken in het VNet van het beheerde exemplaar) om de Azure-VM te maken voor connectiviteit van de clienttoepassing, waaronder SQL Server Management Studio.
  - Stel [point-to-site VPN connection to your managed instance](sql-database-managed-instance-configure-p2s.md) (punt-naar-site-VPN-verbinding voor uw beheerde exemplaar) in vanaf uw clientcomputer met SQL Server Management Studio en andere clienttoepassingen voor connectiviteit. Dit is de andere van twee opties voor connectiviteit met uw beheerde exemplaar en met het desbetreffende VNet.

  > [!NOTE]
  > U kunt ook een ExpressRoute- of site-naar-site-verbinding van uw lokale netwerk gebruiken. Deze methoden worden echter niet besproken in deze quickstarts.

Als alternatief voor het handmatig maken van Managed Instance u [PowerShell,](scripts/sql-database-create-configure-managed-instance-powershell.md) [PowerShell met Resource Manager-sjabloon](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)of [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) gebruiken om dit proces te scripten en te automatiseren.

### <a name="migrate-your-databases"></a>Uw databases migreren

Nadat u een beheerd exemplaar hebt gemaakt en toegang hebt geconfigureerd, kunt u uw databases migreren die zich op de on-premises SQL Server of Azure-VM's bevinden. De migratie mislukt als de brondatabase die u wilt migreren niet-ondersteunde functies bevat. Om fouten te voorkomen en de compatibiliteit te controleren, kunt u [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) installeren. DMA analyseert uw databases op SQL Server en vindt eventuele problemen die de migratie naar een beheerd exemplaar zouden kunnen blokkeren, zoals de aanwezigheid van [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)-bestanden of meerdere logboekbestanden. Als u deze problemen oplost, zijn uw databases klaar om naar het beheerde exemplaar te worden gemigreerd. [Database Experimentation Assistant](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) is een ander handig hulpmiddel dat uw werkbelasting op SQL Server kan vastleggen en deze opnieuw kan afspelen op een beheerd exemplaar, zodat u kunt bepalen of zich prestatieproblemen gaan voordoen als u migreert naar een beheerd exemplaar.

Als u zeker bent dat u uw database naar een beheerd exemplaar kunt migreren, kunt u de systeemeigen opslagmogelijkheden van SQL Server gebruiken om vanuit een `.bak`-bestand een database terug te zetten in een beheerd exemplaar. U kunt deze methode gebruiken voor het migreren van databases vanuit een SQL Server-database-engine die on-premises is geïnstalleerd of een Azure-VM. Zie [Een database terugzetten naar een beheerd exemplaar](sql-database-managed-instance-get-started-restore.md) voor een quickstart. In deze quickstart voert u met de Transact-SQL-opdracht `RESTORE` een terugzetting uit vanuit een `.bak`-bestand dat is opgeslagen in Azure Blob-opslag.

> [!TIP]
> Zie SQL `BACKUP` Server backup [to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)als u de opdracht Transact-SQL wilt gebruiken om een back-up van uw database in Azure Blob-opslag te maken.

Met deze quickstarts kunt u snel back-ups van databases configureren, maken en op een beheerd exemplaar terugzetten. In sommige scenario's moet u de implementatie van een beheerd exemplaar en de vereiste netwerkomgeving aanpassen of automatiseren. Deze scenario's worden hieronder beschreven.

## <a name="customize-network-environment"></a>Netwerkomgeving aanpassen

Hoewel het VNet/subnet automatisch kan worden geconfigureerd met [de Azure-portal](sql-database-managed-instance-get-started.md) wanneer het exemplaar wordt gemaakt, kan het een goed idee zijn om het te maken voordat u begint de beheerde exemplaren te maken, zodat u de parameters van het VNet en subnet kunt configureren. De eenvoudigste manier om de netwerkomgeving te maken en configureren is om de sjabloon voor de [implementatie van Azure Resource](sql-database-managed-instance-create-vnet-subnet.md) te gebruiken. Deze sjabloon maakt en configureert het netwerk en subnet waar het exemplaar wordt geplaatst. U hoeft slechts de implementatieknop voor Azure Resource Manager in te drukken en het formulier met parameters in te vullen.

Als alternatief kunt u ook dit [PowerShell-script](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) gebruiken om het netwerk automatisch te laten maken.

Als u al een VNet en subnet hebt waar u uw beheerde exemplaar wilt implementeren, dan moet u er zeker van zijn dat uw VNet en subnet aan de [netwerkvereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements) voldoen. Gebruik dit [PowerShell-script om te controleren of uw subnet correct is geconfigureerd](sql-database-managed-instance-configure-vnet-subnet.md). Dit script valideert het netwerk en rapporteert problemen. Het laat u ook weten wat er moet worden gewijzigd en stelt voor deze noodzakelijke wijzigingen aan uw VNet/subnet uit te voeren. Voer dit script uit als u uw VNet/subnet niet handmatig wilt configureren. U kunt het ook uitvoeren als uw netwerkinfrastructuur een belangrijke herconfiguratie heeft ondergaan. Als u uw eigen netwerk wilt maken en configureren, leest u [de connectiviteitsarchitectuur](sql-database-managed-instance-connectivity-architecture.md) en deze [ultieme handleiding voor het maken en configureren van een beheerde instantieomgeving.](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)

## <a name="migrate-to-a-managed-instance"></a>Migreren naar een beheerde instantie

Met de artikelen in deze quickstarts kunt u snel een beheerd exemplaar instellen en uw databases verplaatsen met de systeemeigen `RESTORE`-functie. Dit is een goed uitgangspunt als u snel een proof-of-concept wilt uitvoeren en wilt controleren of uw oplossing kan werken op Managed Instance. 

Echter, om uw productie database of zelfs dev / test databases die u wilt gebruiken voor een aantal prestatie-test migreren, moet u overwegen met behulp van een aantal extra technieken, zoals:
- Prestatietests - U moet de basislijnprestaties van uw SQL Server-instantie meten en deze vergelijken met de prestaties op de bestemming beheerde instantie waar u de database hebt gemigreerd. Meer informatie over de [aanbevolen procedures voor prestatievergelijking](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Online migratie - `RESTORE` Met de native die in dit artikel wordt beschreven, moet u wachten tot de databases zijn hersteld (en gekopieerd naar Azure Blob-opslag als deze daar nog niet zijn opgeslagen). Dit heeft downtime voor uw toepassing ten gevolge, met name voor grote databases. Als u de productiedatabase wilt verplaatsen, gebruikt u de [Database Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) om uw database met minimale downtime te migreren. Dit wordt bereikt doordat DMS de aan de brondatabase aangebrachte wijzigingen stapsgewijs pusht naar de database met de beheerde exemplaardatabase die wordt teruggezet. Op deze manier kunt u snel uw toepassing van de bron- naar de doeldatabase overzetten met minimale downtime.

Meer informatie over het [aanbevolen migratieproces](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Volgende stappen

- Vind [hier een lijst met ondersteunde functies in het beheerde exemplaar](sql-database-features.md) en [hier details en bekende problemen](sql-database-managed-instance-transact-sql-information.md).
- Meer informatie over de [technische kenmerken van het beheerde exemplaar](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).
- U vindt meer uitgebreide informatie in [Beheerd exemplaar gebruiken in Azure SQL Database](sql-database-howto-managed-instance.md).
- [Identificeer de juiste Azure SQL Database/Managed Instance SKU voor uw on-premises database.](/sql/dma/dma-sku-recommend-sql-db/)
