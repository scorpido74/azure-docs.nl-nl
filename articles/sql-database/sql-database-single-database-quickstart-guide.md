---
title: Quick Start-afzonderlijke data bases
description: Informatie over hoe u snel aan de slag gaat met individuele databases in Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 6070b53e5f906bc378402d98275b8f798f57b505
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674434"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Aan de slag met individuele databases in Azure SQL Database

[Een individuele database](sql-database-single-index.yml) is een volledig beheerde PaaS-database-als-een-service (DbaaS) die ideaal is als opslag-engine voor moderne toepassingen in de cloud. In dit gedeelte leert u hoe u snel een individuele SQL Database maakt en configureert.

## <a name="quickstart-overview"></a>Overzicht van quickstarts

Dit gedeelte bevat een overzicht van beschikbare artikelen waarmee u snel aan de slag kunt met individuele databases. In de volgende quickstarts leert u snel een individuele database te maken, een firewallregel voor databaseservers te configureren en vervolgens een database te importeren in de nieuwe individuele database via een `.bacpac`-bestand:

- [Een individuele database maken met Azure Portal](sql-database-single-database-get-started.md).
- Nadat de database is gemaakt, moet u uw [database beveiligen door firewallregels te configureren](sql-database-server-level-firewall-rule.md).
- Als u een bestaande database hebt in een SQL Server die u wilt migreren naar Azure, moet u [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) installeren. Dit analyseert uw databases in een SQL Server en zoekt problemen die de migratie naar een individuele database zouden kunnen blokkeren. Als u geen problemen vindt, kunt u uw database als een `.bacpac`-bestand exporteren en [het bestand importeren met de Azure-portal of SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Beheerbewerkingen automatiseren

U kunt PowerShell of Azure CLI gebruiken om uw database te maken, configureren en schalen.

- [Een afzonderlijke data base maken en configureren met Power shell](scripts/sql-database-create-and-configure-database-powershell.md) of [Azure cli](scripts/sql-database-create-and-configure-database-cli.md)
- [Uw afzonderlijke data base bijwerken en resources schalen met Power shell](scripts/sql-database-monitor-and-scale-database-powershell.md) of [Azure cli](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migreren naar een individuele database met minimale downtime

Met de artikelen in deze quickstarts kunt u uw database snel maken of importeren in Azure met een `.bacpac`-bestand. `.bacpac`- en `.dacpac`-bestanden zijn echter ontworpen om snel databases te verplaatsen tussen verschillende versies van SQL Server en implementatieopties binnen Azure SQL Database of om continue integratie in uw DevOps-pijplijn te implementeren. Deze methode is echter niet ontworpen voor de migratie van uw productiedatabases met minimale downtime, omdat u moet stoppen met het toevoegen van nieuwe gegevens, wachten tot de export van de brondatabase naar een `.bacpac`-bestand voltooid is en vervolgens wachten totdat het importeren in Azure SQL Database voltooid is. Al dit wachten leidt tot downtime van uw toepassing, met name voor grote databases. Als u uw productiedatabase verplaatst, hebt u waarschijnlijk een betere migratiemethode nodig met minimale downtime. Om de database met minimale downtime te migreren, gebruikt u de [Database Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json). Dit wordt bereikt doordat DMS de aan de brondatabase aangebrachte wijzigingen stapsgewijs pusht naar de individuele database die wordt teruggezet. Op deze manier kunt u snel uw toepassing van de bron- naar de doeldatabase overzetten met minimale downtime.

## <a name="hands-on-learning-modules"></a>Praktijk gerichte trainings modules

Met de volgende Microsoft Learn modules kunt u gratis meer informatie over Azure SQL Database.

- [Een Azure SQL database inrichten om toepassings gegevens op te slaan](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Een ASP.NET-toepassing ontwikkelen en configureren die een query uitvoert op een Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Uw Azure SQL Database beveiligen](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Volgende stappen

- Zoek een [lijst met ondersteunde functies in Azure SQL Database](sql-database-features.md).
- Leer hoe u uw [database veiliger maakt](sql-database-security-tutorial.md).
- U vindt meer uitgebreide informatie in [Individuele database gebruiken in Azure SQL Database](sql-database-howto-single-database.md).
- Meer voorbeeldscripts zoeken die zijn geschreven in [PowerShell](sql-database-powershell-samples.md) en [Azure CLI](sql-database-cli-samples.md).
- Meer informatie over de [Management-API](sql-database-single-databases-manage.md) die u kunt gebruiken om uw databases te configureren.
- [Bepaal het juiste Azure SQL database/Managed instance SKU voor uw on-premises data base](/sql/dma/dma-sku-recommend-sql-db/).
