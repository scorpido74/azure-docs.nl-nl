---
title: Verbindingstypen
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over de verbindings typen van Azure SQL Managed instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 6c6774fb462a21e721b19ae53d1d018d780b28ae
ms.sourcegitcommit: 581aaca8956b1717b7bc1c1d7710c782c22e6320
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517317"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Verbindings typen van Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt uitgelegd hoe clients verbinding maken met een beheerd exemplaar van Azure SQL, afhankelijk van het verbindings type. Hieronder vindt u voor beelden van scripts voor het wijzigen van de verbindings typen, samen met overwegingen met betrekking tot het wijzigen van de standaard connectiviteits instellingen.

## <a name="connection-types"></a>Verbindingstypen

Azure SQL Managed instance ondersteunt de volgende twee verbindings typen:

- **Omleiden (aanbevolen):** Clients maken rechtstreeks verbinding met het knoop punt dat als host fungeert voor de data base. Als u connectiviteit via omleiding wilt inschakelen, moet u firewalls en netwerk beveiligings groepen (NSG) openen om toegang toe te staan op de poorten 1433 en 11000-11999. Pakketten gaan rechtstreeks naar de data base en daarom zijn er verbeteringen in latentie en doorvoer prestaties met behulp van omleiding via een proxy.
- **Proxy (standaard instelling):** In deze modus gebruiken alle verbindingen een proxy gateway onderdeel. Om connectiviteit in te scha kelen, moet alleen poort 1433 voor particuliere netwerken en poort 3342 voor open bare verbinding worden geopend. Het kiezen van deze modus kan leiden tot hogere latentie en een lagere door Voer, afhankelijk van de aard van de werk belasting. U wordt aangeraden om het beleid voor omleidings verbindingen uit te voeren via het beleid voor proxy verbindingen voor de laagste latentie en de hoogste door voer.

## <a name="redirect-connection-type"></a>Verbindings type omleiden

Wanneer de TCP-sessie in het verbindings type omleiding tot stand is gebracht, haalt de client sessie het virtuele IP-doel adres van het virtuele cluster knooppunt op uit het load balancer. Volgende pakketten stromen rechtstreeks naar het virtuele cluster knooppunt, waarbij de gateway wordt omzeild. In het volgende diagram ziet u deze verkeers stroom.

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Het verbindings type omleiding werkt momenteel alleen voor een persoonlijk eind punt. Ongeacht de instelling van het verbindings type, wordt de verbinding via het open bare eind punt tot stand gebracht via een proxy.

## <a name="proxy-connection-type"></a>Type proxy verbinding

In het Proxy Verbindings type wordt de TCP-sessie tot stand gebracht met behulp van de gateway en alle daaropvolgende pakketten stroomt. In het volgende diagram ziet u deze verkeers stroom.

![proxy.png](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>Verbindings type wijzigen

- **Via de portal:** Als u het verbindings type wilt wijzigen met behulp van de Azure Portal, opent u de Virtual Network pagina en gebruikt u de instelling voor het **verbindings type** om het verbindings type te wijzigen en de wijzigingen op te slaan.

- **Script voor het wijzigen van instellingen voor het verbindings type met behulp van Power shell:**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Het volgende Power shell-script laat zien hoe u het verbindings type voor een beheerd exemplaar wijzigt in `Redirect` .

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

- [Een Data Base herstellen naar een beheerd exemplaar van SQL](restore-sample-database-quickstart.md)
- Meer informatie over het [configureren van een openbaar eind punt op een SQL-beheerd exemplaar](public-endpoint-configure.md)
- Meer informatie over de [connectiviteits architectuur van SQL Managed instance](connectivity-architecture-overview.md)
