---
title: Aangepaste DNS voor het beheerde exemplaar
description: In dit onderwerp worden configuratie opties beschreven voor een aangepaste DNS met een Azure SQL Database beheerd exemplaar.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 3161d931c6e912c05c595db7f3c790da454dd5ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823359"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Een aangepaste DNS configureren voor Azure SQL Database Managed instance

Een Azure SQL Database beheerd exemplaar moet worden geïmplementeerd in een [virtueel Azure-netwerk (VNet)](../virtual-network/virtual-networks-overview.md). Er zijn enkele scenario's (bijvoorbeeld DB mail, gekoppelde servers naar andere SQL-exemplaren in uw Cloud of hybride omgeving) waarvoor de namen van particuliere hosts moeten worden omgezet van het beheerde exemplaar. In dit geval moet u een aangepaste DNS-server in azure configureren. 

Omdat voor het beheerde exemplaar dezelfde DNS wordt gebruikt voor de interne werking, moet u de aangepaste DNS-server zo configureren dat open bare domein namen kunnen worden omgezet.

> [!IMPORTANT]
> Gebruik altijd een Fully Qualified Domain Name (FQDN) voor de e-mail server, de SQL Server instantie en voor andere services, zelfs als ze zich in uw privé-DNS-zone bevinden. Gebruik bijvoorbeeld `smtp.contoso.com` voor uw e-mail server, omdat `smtp` niet correct kan worden omgezet. Als u een gekoppelde server of replicatie wilt maken die verwijst naar SQL-Vm's binnen hetzelfde virtuele netwerk, hebt u ook een FQDN en een standaard-DNS-achtervoegsel nodig. Bijvoorbeeld `SQLVM.internal.cloudapp.net`. Zie [naam omzetting die gebruikmaakt van uw eigen DNS-server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)voor meer informatie.

> [!IMPORTANT]
> Het bijwerken van de DNS-servers van het virtuele netwerk heeft geen invloed op het beheerde exemplaar direct. De DNS-configuratie van het beheerde exemplaar wordt bijgewerkt nadat de DHCP-lease verloopt of na het platform upgarade, afhankelijk van wat er eerst gebeurt. **Gebruikers wordt aangeraden hun DNS-configuratie voor het virtuele netwerk in te stellen voordat ze hun eerste beheerde exemplaar maken.**

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar](sql-database-managed-instance.md) ? voor een overzicht.
- Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor een zelf studie waarin wordt getoond hoe u een nieuw beheerd exemplaar maakt.
- Zie [vnet-configuratie voor beheerde instanties](sql-database-managed-instance-connectivity-architecture.md) voor meer informatie over het configureren van een vnet voor een beheerd exemplaar
