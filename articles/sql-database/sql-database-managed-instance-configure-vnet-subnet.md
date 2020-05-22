---
title: Een bestaand virtueel netwerk voor een beheerd exemplaar configureren
description: In dit artikel wordt beschreven hoe u een bestaand virtueel netwerk en subnet configureert waarin u Azure SQL Database beheerde instantie kunt implementeren.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 4e678edad2c59205e76598991b36d296404a3163
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773622"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Een bestaand virtueel netwerk configureren voor Azure SQL Database Managed Instance

Azure SQL Managed Instance moet alleen worden geïmplementeerd in een [virtueel netwerk van Azure](../virtual-network/virtual-networks-overview.md) en het subnet dat is toegewezen aan beheerde exemplaren. U kunt het bestaande virtuele netwerk en subnet gebruiken als deze zijn geconfigureerd volgens de [vereisten voor virtuele netwerken van Managed Instance](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Als een van de volgende gevallen van toepassing is, kunt u uw netwerk valideren en wijzigen met behulp van het script dat in dit artikel wordt uitgelegd:

- U hebt een nieuw subnet dat nog niet is geconfigureerd.
- U weet niet zeker dat het subnet is afgestemd op de [vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- U wilt controleren of het subnet nog steeds voldoet aan de [netwerk vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements) nadat u wijzigingen hebt aangebracht.

> [!Note]
> U kunt alleen een beheerd exemplaar maken in virtuele netwerken die zijn gemaakt via het Azure Resource Manager-implementatie model. Virtuele Azure-netwerken die zijn gemaakt via het klassieke implementatie model, worden niet ondersteund. Bereken de grootte van het subnet door de richt lijnen te volgen in het artikel [grootte van subnet voor beheerde instanties bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md) . U kunt het formaat van het subnet niet wijzigen nadat u de resources in hebt geïmplementeerd.
>
> Nadat een beheerd exemplaar is gemaakt, wordt het beheerde exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Een bestaand virtueel netwerk valideren en wijzigen

Als u een beheerd exemplaar wilt maken binnen een bestaand subnet, raden we het volgende PowerShell-script aan om het subnet voor te bereiden:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Het script bereidt het subnet in drie stappen voor:

1. Validate: Hiermee worden het geselecteerde virtuele netwerk en subnet voor beheerde instantie netwerk vereisten gevalideerd.
2. Bevestigen: de gebruiker ziet een set wijzigingen die moeten worden aangebracht om het subnet voor te bereiden voor de implementatie van beheerde exemplaren. Er wordt ook om toestemming gevraagd.
3. Voorbereiden: het virtuele netwerk en het subnet worden correct geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-database-managed-instance.md)voor een overzicht.
- Voor een zelf studie waarin wordt getoond hoe u een virtueel netwerk maakt, een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-up van de data base, raadpleegt u [een Azure SQL database beheerd exemplaar maken](sql-database-managed-instance-get-started.md).
- Zie [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md)(Engelstalig) voor DNS-problemen.
