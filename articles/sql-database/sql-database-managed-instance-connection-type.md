---
title: Azure SQL Database-verbindings typen voor beheerd exemplaar | Microsoft Docs
description: Meer informatie over verbindings typen voor beheerde instanties
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 4ebd81f9c04bd739f1c18f636094040882b5ce36
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001768"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Verbindings typen voor beheerde instanties Azure SQL Database

In dit artikel wordt uitgelegd hoe clients verbinding maken met Azure SQL Database beheerde instantie, afhankelijk van het verbindings type. Hieronder vindt u voor beelden van scripts voor het wijzigen van de verbindings typen, samen met overwegingen met betrekking tot het wijzigen van de standaard connectiviteits instellingen.

## <a name="connection-types"></a>Verbindingstypen

Azure SQL Database Managed instance ondersteunt de volgende twee verbindings typen:

- **Omleiden (aanbevolen):** Clients maken rechtstreeks verbinding met het knoop punt dat als host fungeert voor de data base. Als u connectiviteit via omleiding wilt inschakelen, moet u firewalls en netwerk beveiligings groepen (NSG) openen om toegang toe te staan op de poorten 1433 en 11000-11999. Pakketten gaan rechtstreeks naar de data base en daarom zijn er verbeteringen in latentie en doorvoer prestaties met behulp van omleiding via een proxy.
- **Proxy (standaard instelling):** In deze modus gebruiken alle verbindingen een proxy gateway onderdeel. Om connectiviteit in te scha kelen, moet alleen poort 1433 voor particuliere netwerken en poort 3342 voor open bare verbinding worden geopend. Het kiezen van deze modus kan leiden tot hogere latentie en een lagere door Voer, afhankelijk van de aard van de werk belasting. U wordt aangeraden om het beleid voor omleidings verbindingen uit te voeren via het beleid voor proxy verbindingen voor de laagste latentie en de hoogste door voer.

## <a name="redirect-connection-type"></a>Verbindings type omleiden

Verbindings type omleiden betekent dat wanneer de TCP-sessie tot stand is gebracht met de SQL-engine, de client sessie het virtuele IP-doel van het virtuele cluster knooppunt van de load balancer verkrijgt. Volgende pakketten stromen rechtstreeks naar het virtuele cluster knooppunt, waarbij de gateway wordt omzeild. In het volgende diagram ziet u deze verkeers stroom.

![omleiding. png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Het verbindings type voor omleiding werkt momenteel alleen voor een persoonlijk eind punt. Ongeacht de instelling van het verbindings type, wordt de verbinding via het open bare eind punt tot stand gebracht via een proxy.

## <a name="proxy-connection-type"></a>Type proxy verbinding

Proxy Verbindings type betekent dat de TCP-sessie tot stand is gebracht met behulp van de gateway en dat alle volgende pakketten door de server worden gebruikt. In het volgende diagram ziet u deze verkeers stroom.

![proxy. png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Script voor het wijzigen van de instellingen van het verbindings type met Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Het volgende Power shell-script laat zien hoe u het verbindings type voor een beheerd exemplaar wijzigt in omleiden.

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

- [Een database herstellen naar een beheerd exemplaar](sql-database-managed-instance-get-started-restore.md)
- Meer informatie over het [configureren van een openbaar eind punt in een beheerd exemplaar](sql-database-managed-instance-public-endpoint-configure.md)
- Meer informatie over de [connectiviteits architectuur voor beheerde exemplaren](sql-database-managed-instance-connectivity-architecture.md)