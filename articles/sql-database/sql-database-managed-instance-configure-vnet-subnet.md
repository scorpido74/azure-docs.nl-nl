---
title: Een bestaand virtueel netwerk voor Azure SQL Database beheerde instantie configureren
description: In dit artikel wordt beschreven hoe u een bestaand virtueel netwerk en subnet configureert waarin u Azure SQL Database beheerde instantie kunt implementeren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 01/15/2019
ms.openlocfilehash: 0f6ababa9d25798f4b150c322d48d4c7af04a511
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689374"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Een bestaand virtueel netwerk voor Azure SQL Database beheerde instantie configureren

Azure SQL Database beheerde instantie moet worden geïmplementeerd in een virtueel Azure- [netwerk](../virtual-network/virtual-networks-overview.md) en het toegewezen subnet alleen voor beheerde exemplaren. U kunt het bestaande virtuele netwerk en subnet gebruiken als het is geconfigureerd volgens de vereisten voor het [virtuele netwerk van het beheerde exemplaar](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Als een van de volgende gevallen van toepassing is, kunt u uw netwerk valideren en wijzigen met behulp van het script dat in dit artikel wordt uitgelegd:

- U hebt een nieuw subnet dat nog niet is geconfigureerd.
- U weet niet zeker dat het subnet is afgestemd op de [vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- U wilt controleren of het subnet nog steeds voldoet aan de [netwerk vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements) nadat u wijzigingen hebt aangebracht.

> [!Note]
> U kunt alleen een beheerd exemplaar maken in virtuele netwerken die zijn gemaakt via het Azure Resource Manager-implementatie model. Virtuele Azure-netwerken die zijn gemaakt via het klassieke implementatie model, worden niet ondersteund. Bereken de grootte van het subnet door de richt lijnen te volgen in het artikel [grootte van subnet voor beheerde instanties bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md) . U kunt het formaat van het subnet niet wijzigen nadat u de resources in hebt geïmplementeerd.
>
> Nadat een beheerd exemplaar is gemaakt, wordt het beheerde exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Een bestaand virtueel netwerk valideren en wijzigen

Als u een beheerd exemplaar binnen een bestaand subnet wilt maken, raden we het volgende Power shell-script aan om het subnet voor te bereiden:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Het script bereidt het subnet voor in drie stappen:

1. Validate: Hiermee worden het geselecteerde virtuele netwerk en subnet voor beheerde instantie netwerk vereisten gevalideerd.
2. Bevestigen: de gebruiker ziet een set wijzigingen die moeten worden aangebracht om het subnet voor te bereiden voor de implementatie van beheerde exemplaren. Er wordt ook om toestemming gevraagd.
3. Voorbereiden: het virtuele netwerk en het subnet worden correct geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-database-managed-instance.md)voor een overzicht.
- Voor een zelf studie waarin wordt getoond hoe u een virtueel netwerk maakt, een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-up van de data base, raadpleegt u [een Azure SQL database beheerd exemplaar maken](sql-database-managed-instance-get-started.md).
- Zie [Configuring a Custom DNS](sql-database-managed-instance-custom-dns.md)(Engelstalig) voor DNS-problemen.
