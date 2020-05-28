---
title: IP-adres van beheer eindpunt detecteren
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over het ophalen van het open bare IP-eind punt voor het Azure SQL Managed instance Management endpoint en het controleren van de ingebouwde firewall beveiliging
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 88965c25702917f17a226cfa51de662703136aae
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045079"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Het IP-adres van het beheer eindpunt bepalen-Azure SQL Managed instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Het virtuele Azure SQL Managed instance-cluster bevat een beheer eindpunt dat door Azure wordt gebruikt voor beheer bewerkingen. Het beheer eindpunt wordt beveiligd met een ingebouwde firewall op het netwerk niveau en de verificatie op basis van wederzijdse certificaten op toepassings niveau. U kunt het IP-adres van het beheer eindpunt bepalen, maar u hebt geen toegang tot dit eind punt.

Als u het IP-adres van het beheer wilt bepalen, voert u een [DNS-zoek opdracht](/windows-server/administration/windows-commands/nslookup) uit op de FQDN van het SQL-beheerde exemplaar: `mi-name.zone_id.database.windows.net` . Hiermee wordt een DNS-vermelding geretourneerd die er als volgt uitziet `trx.region-a.worker.vnet.database.windows.net` . U kunt vervolgens een DNS-zoek opdracht uitvoeren op deze FQDN met ". vnet" verwijderd. Hiermee wordt het IP-adres van het beheer geretourneerd. 

Deze Power shell doet dit allemaal voor u als u vervangt door \<MI FQDN\> de DNS-vermelding van uw SQL Managed instance: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Zie [connectiviteits architectuur voor Azure SQL Managed instance](connectivity-architecture-overview.md)voor meer informatie over door SQL beheerde instanties en connectiviteit.
