---
title: Aangepaste DNS
titleSuffix: Azure SQL Managed Instance
description: In dit onderwerp worden configuratie opties beschreven voor een aangepaste DNS met Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 6a6cca1228c9891c023ce96532dc89b2f527e9a3
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655529"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Een aangepaste DNS configureren voor Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance moet worden geïmplementeerd in een [virtueel Azure-netwerk (VNet)](../../virtual-network/virtual-networks-overview.md). Er zijn enkele scenario's (bijvoorbeeld DB mail, gekoppelde servers naar andere SQL Server instanties in uw Cloud of hybride omgeving) waarvoor particuliere hostnamen moeten worden omgezet van een door SQL beheerd exemplaar. In dit geval moet u een aangepaste DNS-server in azure configureren. 

Omdat SQL Managed instance dezelfde DNS gebruikt voor de interne werking, configureert u de aangepaste DNS-server zodat deze open bare domein namen kan omzetten.

> [!IMPORTANT]
> Gebruik altijd een Fully Qualified Domain Name (FQDN) voor de e-mail server, voor het SQL Server exemplaar en voor andere services, zelfs als ze zich in uw privé-DNS-zone bevinden. Gebruik bijvoorbeeld `smtp.contoso.com` voor uw e-mail server omdat deze `smtp` niet correct kan worden omgezet. Als u een gekoppelde server of replicatie wilt maken die verwijst naar SQL Server Vm's binnen hetzelfde virtuele netwerk, hebt u ook een FQDN en een standaard-DNS-achtervoegsel nodig. Bijvoorbeeld `SQLVM.internal.cloudapp.net`. Zie [naam omzetting die gebruikmaakt van uw eigen DNS-server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)voor meer informatie.

> [!IMPORTANT]
> Het bijwerken van de DNS-servers van het virtuele netwerk heeft geen invloed op het SQL Managed instance. De DNS-configuratie van het SQL Managed instance wordt bijgewerkt nadat de DHCP-lease verloopt of na de platform upgrade, afhankelijk van wat er eerst gebeurt. **Gebruikers wordt aangeraden hun DNS-configuratie voor het virtuele netwerk in te stellen voordat ze hun eerste beheerde exemplaar maken.**

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor een zelf studie waarin wordt getoond hoe u een nieuw beheerd exemplaar maakt.
- Zie [vnet-configuratie voor beheerde instanties](connectivity-architecture-overview.md)voor meer informatie over het configureren van een vnet voor een beheerd exemplaar.
