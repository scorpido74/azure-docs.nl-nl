---
title: De configuratie van het service-geaidede subnet inschakelen voor het beheerde exemplaar van Azure SQL Database
description: De configuratie van het service-geaidede subnet inschakelen voor het beheerde exemplaar van Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: f22c713af700225ffe6954460c36217abdea5eea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770156"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>De configuratie van het service-geaidede subnet inschakelen voor het beheerde exemplaar van Azure SQL Database
Service-aided subnet-configuratie biedt geautomatiseerd netwerk configuratie beheer voor subnetten die als host fungeren voor beheerde instanties. Met Service-aided subnet-configuratie gebruiker behoudt de volledige controle over de toegang tot gegevens (TDS-verkeer stromen) terwijl het beheerde exemplaar verantwoordelijk is voor een ononderbroken stroom van beheer verkeer om te voldoen aan de SLA.

Automatisch geconfigureerde netwerk beveiligings groepen en route tabel regels zijn zichtbaar voor de klant en worden gemarkeerd met het voor voegsel _micro soft. SQL-managedInstances_UseOnly__.

De service-geaidede configuratie wordt automatisch ingeschakeld wanneer u [subnet-delegering](../virtual-network/subnet-delegation-overview.md) voor de resource provider inschakelt `Microsoft.Sql/managedInstances` .

> [!IMPORTANT] 
> Zodra de subnet-delegering is ingeschakeld, kunt u deze niet uitschakelen totdat u het laatste virtuele cluster verwijdert uit het subnet. Zie het volgende [artikel](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)voor meer informatie over het verwijderen van een virtueel cluster.

> [!NOTE] 
> Omdat de configuratie van het service-geaidede subnet essentieel is voor het onderhouden van SLA, vanaf 1 mei 2020, is het niet mogelijk om beheerde exemplaren te implementeren in subnetten die niet worden overgedragen aan de resource provider van het beheerde exemplaar. Op 1 juli 2020 worden alle subnetten met beheerde exemplaren automatisch overgedragen aan de resource provider van het beheerde exemplaar. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Subnet-delegering inschakelen voor nieuwe implementaties
Als u een beheerd exemplaar wilt implementeren in een leeg subnet, moet u het overdragen aan de `Microsoft.Sql/managedInstances` resource provider zoals beschreven in het volgende [artikel](../virtual-network/manage-subnet-delegation.md). _Houd er rekening mee dat in het artikel waarnaar wordt verwezen, `Microsoft.DBforPostgreSQL/serversv2` bijvoorbeeld een resource provider wordt gebruikt. U moet `Microsoft.Sql/managedInstances` in plaats daarvan resource provider gebruiken._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Subnet-delegering inschakelen voor bestaande implementaties

Als u subnet-delegering wilt inschakelen voor de bestaande implementatie van het beheerde exemplaar, moet u het subnet van het virtuele netwerk vinden waar het is geplaatst. 

Als u dit wilt weten, kunt u `Virtual network/subnet` de `Overview` Portal-Blade van uw beheerde exemplaar controleren.

Als alternatief kunt u de volgende Power shell-opdrachten uitvoeren om dit te leren. Vervang **abonnement-id** door uw abonnements-id. Vervang ook de **naam van RG** door de resource groep voor uw beheerde exemplaar en vervang de **mi-naam** door de naam van uw beheerde exemplaar.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Zodra u het subnet van het beheerde exemplaar hebt gevonden, moet u het delegeren aan de `Microsoft.Sql/managedInstances` resource provider zoals beschreven in het volgende [artikel](../virtual-network/manage-subnet-delegation.md). _Houd er rekening mee dat in het artikel waarnaar wordt verwezen, `Microsoft.DBforPostgreSQL/serversv2` bijvoorbeeld een resource provider wordt gebruikt. U moet `Microsoft.Sql/managedInstances` in plaats daarvan resource provider gebruiken._


> [!IMPORTANT]
> Het inschakelen van service-aided Configuration veroorzaakt geen failover of onderbreking in connectiviteit voor beheerde exemplaren die zich al in het subnet bevinden.
