---
title: Een bestaand virtueel netwerk configureren
titleSuffix: Azure SQL Managed Instance
description: In dit artikel wordt beschreven hoe u een bestaand virtueel netwerk en subnet configureert waarin u Azure SQL Managed Instance kunt implementeren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e41a91320ed3226cb211de5ba3f6f0eb17da555f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044302"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Een bestaand virtueel netwerk configureren voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance moet worden geïmplementeerd in een [virtueel](../../virtual-network/virtual-networks-overview.md) Azure-netwerk en het toegewezen subnet alleen voor SQL Managed instances. U kunt het bestaande virtuele netwerk en subnet gebruiken als het is geconfigureerd volgens de vereisten voor het [virtuele netwerk van SQL Managed instance](connectivity-architecture-overview.md#network-requirements).

Als een van de volgende gevallen van toepassing is, kunt u uw netwerk valideren en wijzigen met behulp van het script dat in dit artikel wordt uitgelegd:

- U hebt een nieuw subnet dat nog niet is geconfigureerd.
- U weet niet zeker dat het subnet is afgestemd op de [vereisten](connectivity-architecture-overview.md#network-requirements).
- U wilt controleren of het subnet nog steeds voldoet aan de [netwerk vereisten](connectivity-architecture-overview.md#network-requirements) nadat u wijzigingen hebt aangebracht.

> [!Note]
> U kunt alleen een SQL Managed instance maken in virtuele netwerken die zijn gemaakt via het Azure Resource Manager-implementatie model. Virtuele Azure-netwerken die zijn gemaakt via het klassieke implementatie model, worden niet ondersteund. Bereken de grootte van het subnet door de richt lijnen te volgen in het artikel [grootte van subnet voor SQL Managed instances bepalen](vnet-subnet-determine-size.md) . U kunt het formaat van het subnet niet wijzigen nadat u de resources in hebt geïmplementeerd.
>
> Nadat het SQL Managed instance is gemaakt, wordt het verplaatsen van het exemplaar of VNet naar een andere resource groep of een ander abonnement niet ondersteund.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Een bestaand virtueel netwerk valideren en wijzigen

Als u een SQL Managed instance wilt maken in een bestaand subnet, raden we het volgende Power shell-script aan om het subnet voor te bereiden:

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

1. Validate: Hiermee worden het geselecteerde virtuele netwerk en subnet gevalideerd voor de netwerk vereisten voor SQL Managed instance.
2. Bevestigen: er wordt een set wijzigingen weer gegeven die moeten worden aangebracht om het subnet voor te bereiden voor de implementatie van SQL Managed instance. Er wordt ook om toestemming gevraagd.
3. Voorbereiden: het virtuele netwerk en het subnet worden correct geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Voor een zelf studie waarin wordt getoond hoe u een virtueel netwerk maakt, een SQL Managed instance maakt en een Data Base herstelt vanuit een back-up van een Data Base, raadpleegt u [een Azure SQL Managed instance maken](instance-create-quickstart.md).
- Zie [Configuring a Custom DNS](custom-dns-configure.md)(Engelstalig) voor DNS-problemen.
