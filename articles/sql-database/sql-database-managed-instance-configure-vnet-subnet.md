---
title: Een bestaand virtueel netwerk configureren voor beheerde instantie
description: In dit artikel wordt beschreven hoe u een bestaand virtueel netwerk en subnet configureert waar u Azure SQL Database Managed Instance implementeren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476946"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Een bestaand virtueel netwerk configureren voor Azure SQL Database Managed Instance

Azure SQL Database Managed Instance moet worden geïmplementeerd binnen een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md) en het subnet dat alleen is toegewezen aan Beheerde exemplaren. U het bestaande virtuele netwerk en subnet gebruiken als het is geconfigureerd volgens de [virtuele netwerkvereisten voor beheerde instantie.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)

Als een van de volgende gevallen op u van toepassing is, u uw netwerk valideren en wijzigen met behulp van het script dat in dit artikel wordt uitgelegd:

- Je hebt een nieuw subnet dat nog steeds niet is geconfigureerd.
- U weet niet zeker of het subnet is afgestemd op de [vereisten.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)
- U wilt controleren of het subnet nog steeds voldoet aan de [netwerkvereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements) nadat u wijzigingen hebt aangebracht.

> [!Note]
> U een beheerde instantie alleen maken in virtuele netwerken die zijn gemaakt via het Azure Resource Manager-implementatiemodel. Azure virtuele netwerken die zijn gemaakt via het klassieke implementatiemodel worden niet ondersteund. Bereken subnetgrootte door de richtlijnen te volgen in het artikel [De grootte van subnet voor Beheerde instanties bepalen.](sql-database-managed-instance-determine-size-vnet-subnet.md) U het formaat van het subnet niet wijzigen nadat u de resources binnenin hebt geïmplementeerd.
>
> Nadat een beheerde instantie is gemaakt, wordt het verplaatsen van de beheerde instantie of VNet naar een andere resourcegroep of abonnement niet ondersteund.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Een bestaand virtueel netwerk valideren en wijzigen

Als u een beheerde instantie wilt maken in een bestaand subnet, raden we het volgende PowerShell-script aan om het subnet voor te bereiden:

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

1. Valideren: het valideert het geselecteerde virtuele netwerk en subnet voor netwerkvereisten voor beheerde instance's.
2. Bevestigen: het toont de gebruiker een reeks wijzigingen die moeten worden aangebracht om het subnet voor te bereiden op de implementatie van beheerde instantie. Zij vraagt ook om toestemming.
3. Voorbereiden: Het virtuele netwerk en subnet worden correct geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md)een overzicht.
- Zie Een Azure SQL Database Managed Instance maken voor een zelfstudie die laat zien hoe u een virtueel netwerk maakt, een beheerde instantie maakt en een database herstelt van een databaseback.For a tutorial that shows how to create a virtual network, create a Managed Instance, create a Managed Instance and restore a database from a database backup, see [Create an Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Zie [Een aangepaste DNS configureren](sql-database-managed-instance-custom-dns.md)voor DNS-problemen.
