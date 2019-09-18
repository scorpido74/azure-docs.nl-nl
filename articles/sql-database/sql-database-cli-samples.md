---
title: Azure CLI-scriptvoorbeelden voor SQL Database | Microsoft Docs
description: Azure CLI-scriptvoorbeelden voor het maken en beheren van Azure SQL Database-servers, elastische pools, databases en firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 5366fb1d32020bfbcfaba36c60c0eb5441e92070
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71055245"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure CLI-voorbeelden voor Azure SQL Database

Azure SQL Database kan worden geconfigureerd met <a href="/cli/azure">Azure CLI</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-poolstabsingle-database"></a>[Eén data base & elastische Pools](#tab/single-database)

De volgende tabel bevat koppelingen naar Azure CLI-scriptvoorbeelden voor Azure SQL Database.

| |  |
|---|---|
|**Een individuele database en een elastische pool maken**||
| [Een individuele database maken en een firewallregel configureren](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Met dit voorbeeld van een CLI-script wordt één Azure SQL-database gemaakt en een regel voor een firewall op serverniveau geconfigureerd. |
| [Elastische pools maken en pooldatabases verplaatsen](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | In dit voorbeeld van een CLI-script worden elastische SQL-pools gemaakt, Azure SQL-pooldatabases verplaatst en rekenkrachten gewijzigd.|
|**Een individuele database en een elastische pool schalen**||
| [Een individuele database schalen](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | In dit voorbeeld van een CLI-script wordt één Azure SQL-database naar een andere rekenkracht geschaald nadat de grootte van de database is opgevraagd. |
| [Een elastische pool schalen](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | In dit voorbeeld van een CLI-script wordt een elastische SQL-pool naar een andere rekenkracht geschaald.  |
|**Failover-groepen**||
| [Eén data base aan een failovergroep toevoegen](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Met dit CLI-script maakt u een Data Base en een failovergroep, voegt u de data base toe aan de failovergroep en voert u een failover uit naar de secundaire server.|
|||

Meer informatie over de [Single Database Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instancetabmanaged-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

De volgende tabel bevat koppelingen naar Azure CLI-scriptvoorbeelden voor Azure SQL Database - Beheerd exemplaar.

| |  |
|---|---|
| [Een beheerd exemplaar maken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Dit CLI-script laat zien hoe u een beheerd exemplaar maakt. |
| [Een beheerd exemplaar bijwerken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Dit CLI-script laat zien hoe u een beheerd exemplaar bijwerkt. |
| [Een database verplaatsen naar een ander beheerd exemplaar](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Dit CLI-script laat zien hoe u een back-up van een database kunt terugzetten van het ene naar het andere exemplaar. |
|||

Lees meer informatie over de [Managed Instance Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) en bekijk [hier andere voorbeelden](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

---
