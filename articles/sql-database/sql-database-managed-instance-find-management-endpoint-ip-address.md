---
title: Eindpunt beheer van beheervan beheer ontdekken
description: Meer informatie over het beheren van Azure SQL Database Managed Instance-beheer-eindpunt en het verifiëren van de ingebouwde firewallbeveiliging
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825723"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Het IP-adres voor beheereindpunten bepalen

Het virtuele cluster Azure SQL Database Managed Instance bevat een beheereindpunt dat Microsoft gebruikt voor beheerbewerkingen. Het beheereindpunt wordt beveiligd met een ingebouwde firewall op netwerkniveau en wederzijdse certificaatverificatie op toepassingsniveau. U het IP-adres van het beheereindpunt bepalen, maar u hebt geen toegang tot dit eindpunt.

Als u het IP-adres van het beheer wilt bepalen, `mi-name.zone_id.database.windows.net`doet u een DNS-lookup op uw beheerde instantie FQDN: . Dit retourneert een DNS-item dat is als `trx.region-a.worker.vnet.database.windows.net`. U dan een DNS-lookup op deze FQDN doen met .vnet verwijderd. Dit retourneert het IP-adres van het beheer. 

Deze PowerShell doet het allemaal voor \<u als\> u MI FQDN `mi-name.zone_id.database.windows.net`vervangt door de DNS-vermelding van uw beheerde instantie:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Zie [Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md)voor meer informatie over beheerde exemplaren en connectiviteit.
