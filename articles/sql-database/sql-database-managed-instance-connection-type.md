---
title: Beheerde instantieverbindingstypen
description: Meer informatie over beheerde instantieverbindingstypen
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819461"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Door Azure SQL Database beheerde typen verbindingstypen

In dit artikel wordt uitgelegd hoe clients verbinding maken met de beheerde instantie van Azure SQL Database, afhankelijk van het verbindingstype. Scriptvoorbeelden om verbindingstypen te wijzigen zijn hieronder weergegeven, samen met overwegingen met betrekking tot het wijzigen van de standaardverbindingsinstellingen.

## <a name="connection-types"></a>Verbindingstypen

De instantie beheerd Azure SQL Database ondersteunt de volgende twee verbindingstypen:

- **Omleiden (aanbevolen):** Clients leggen rechtstreeks verbindingen met het knooppunt dat de database host. Als u connectiviteit met omleiding wilt inschakelen, moet u firewalls en netwerkbeveiligingsgroepen (NSG) openen om toegang te verlenen op poorten 1433 en 11000-11999. Pakketten gaan rechtstreeks naar de database, en dus zijn er latentie en doorvoer prestaties verbeteringen met behulp van Redirect over Proxy.
- **Proxy (standaard):** In deze modus gebruiken alle verbindingen een proxygatewaycomponent. Om connectiviteit mogelijk te maken, hoeft alleen poort 1433 voor particuliere netwerken en poort 3342 voor openbare verbinding te worden geopend. Als u deze modus kiest, kan dit leiden tot een hogere latentie en een lagere doorvoer, afhankelijk van de aard van de werkbelasting. We raden het verbindingsbeleid Redirect ten zeerste aan ten opzichte van het proxy-verbindingsbeleid voor de laagste latentie en de hoogste doorvoer.

## <a name="redirect-connection-type"></a>Verbindingstype omleiden

Het type omleidingsverbinding betekent dat nadat de TCP-sessie is ingesteld op de SQL-engine, de clientsessie het virtuele IP-doel van het virtuele clusterknooppunt van de load balancer verkrijgt. Volgende pakketten stromen rechtstreeks naar het virtuele clusterknooppunt, waarmee de gateway wordt omzeild. Het volgende diagram illustreert deze verkeersstroom.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Het type omleidingsverbinding werkt momenteel alleen voor privéeindpunt. Ongeacht de instelling van het verbindingstype, worden verbindingen die via het openbare eindpunt komen, via een proxy.

## <a name="proxy-connection-type"></a>Type proxyverbinding

Proxy-verbindingstype betekent dat de TCP-sessie wordt ingesteld met behulp van de gateway en alle volgende pakketten erdoorheen stromen. Het volgende diagram illustreert deze verkeersstroom.

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script om verbindingstype-instellingen te wijzigen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In het volgende PowerShell-script ziet u hoe u het verbindingstype voor een beheerde instantie wijzigt in Omleiding.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Volgende stappen

- [Een database herstellen naar een beheerde instantie](sql-database-managed-instance-get-started-restore.md)
- Meer informatie over het [configureren van een openbaar eindpunt voor beheerde instantie](sql-database-managed-instance-public-endpoint-configure.md)
- Meer informatie over [beheerde architectuur voor instantieconnectiviteit](sql-database-managed-instance-connectivity-architecture.md)