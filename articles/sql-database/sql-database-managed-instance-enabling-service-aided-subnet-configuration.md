---
title: Service-aided subnetconfiguratie inschakelen voor Azure SQL Database Managed Instance
description: Service-aided subnetconfiguratie inschakelen voor Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533263"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Service-aided subnetconfiguratie inschakelen voor Azure SQL Database Managed Instance
Subnetconfiguratie met servicehulpprogramma's biedt geautomatiseerd netwerkconfiguratiebeheer voor subnetten die beheerde exemplaren hosten. Met service-aided subnet configuratie gebruiker blijft in volledige controle over de toegang tot gegevens (TDS verkeersstromen), terwijl beheerde instantie neemt de verantwoordelijkheid om een ononderbroken stroom van beheerverkeer te garanderen om SLA te voldoen.

Automatisch geconfigureerde netwerkbeveiligingsgroepen en routetabelregels zijn zichtbaar voor de klant en worden geannoteerd met voorvoegsel _Microsoft.Sql-managedInstances_UseOnly__.

Service-ondersteunde configuratie wordt automatisch ingeschakeld zodra u [subnet-delegatie](../virtual-network/subnet-delegation-overview.md) inschakelt voor `Microsoft.Sql/managedInstances` resourceprovider.

> [!IMPORTANT] 
> Zodra subnet-delegatie is ingeschakeld, u deze niet uitschakelen totdat u het laatste virtuele cluster uit het subnet hebt verwijderd. Zie het volgende [artikel](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)voor meer informatie over het verwijderen van virtueel cluster.

> [!NOTE] 
> Aangezien subnetconfiguratie met servicehulpprogramma essentieel is voor het onderhouden van SLA, is het vanaf 1 mei 2020 niet mogelijk om beheerde exemplaren te implementeren in subnetten die niet zijn gedelegeerd aan beheerde instantieresourceprovider. Op 1 juli 2020 worden alle subnetten met beheerde exemplaren automatisch gedelegeerd aan beheerde instantieresourceprovider. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Subnetdelegatie inschakelen voor nieuwe implementaties
Als u beheerde instantie wilt implementeren in `Microsoft.Sql/managedInstances` een leeg subnet, moet u het delegeren aan resourceprovider zoals beschreven in het volgende [artikel](../virtual-network/manage-subnet-delegation.md). _Houd er rekening mee `Microsoft.DBforPostgreSQL/serversv2` dat artikel waarnaar wordt verwezen bijvoorbeeld gebruik maakt van resourceprovider. In plaats daarvan `Microsoft.Sql/managedInstances` moet u de resourceprovider gebruiken._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Subnetdelegatie inschakelen voor bestaande implementaties

Om subnet-delegatie in te schakelen voor uw bestaande beheerde instantie-implementatie moet u het virtuele netwerksubnet achterhalen waar het wordt geplaatst. 

Om dit te `Virtual network/subnet` leren `Overview` kunt u controleren op de portal blade van uw beheerde instantie.

Als alternatief u de volgende PowerShell-opdrachten uitvoeren om dit te leren. Vervang **abonnement-id** door uw abonnements-ID. Vervang **ook de rg-naam** door de resourcegroep voor uw beheerde instantie en vervang **de mi-naam** door de naam van uw beheerde instantie.

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

Zodra u het beheerde instantiesubnet `Microsoft.Sql/managedInstances` hebt gevonden, moet u het delegeren aan resourceprovider zoals beschreven in het volgende [artikel](../virtual-network/manage-subnet-delegation.md). _Houd er rekening mee `Microsoft.DBforPostgreSQL/serversv2` dat artikel waarnaar wordt verwezen bijvoorbeeld gebruik maakt van resourceprovider. In plaats daarvan `Microsoft.Sql/managedInstances` moet u de resourceprovider gebruiken._


> [!IMPORTANT]
> Het inschakelen van service-ondersteunde configuratie veroorzaakt geen failover of onderbreking van de connectiviteit voor beheerde exemplaren die al in het subnet staan.
