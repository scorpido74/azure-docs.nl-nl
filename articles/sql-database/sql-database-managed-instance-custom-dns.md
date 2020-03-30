---
title: Aangepaste DNS voor beheerde instantie
description: In dit onderwerp worden configuratieopties beschreven voor een aangepaste DNS met een Azure SQL Database Managed Instance.
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
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247075"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Een aangepaste DNS configureren voor Azure SQL Database Managed Instance

Een Azure SQL Database Managed Instance moet worden geïmplementeerd binnen een Virtual [Network (Azure Virtual Network).](../virtual-network/virtual-networks-overview.md) Er zijn een paar scenario's (bijvoorbeeld db-mail, gekoppelde servers met andere SQL-exemplaren in uw cloud of hybride omgeving) waarvoor privéhostnamen moeten worden opgelost vanuit het beheerde exemplaar. In dit geval moet u een aangepaste DNS configureren in Azure. 

Omdat Managed Instance dezelfde DNS gebruikt voor de interne werking, configureert u de aangepaste DNS-server zodat deze openbare domeinnamen kan oplossen.

> [!IMPORTANT]
> Gebruik altijd een volledig gekwalificeerde domeinnaam (FQDN) voor de mailserver, het SQL Server-exemplaar en voor andere services, zelfs als deze zich binnen uw privé-DNS-zone bevinden. Gebruik bijvoorbeeld `smtp.contoso.com` voor uw e-mailserver omdat `smtp` deze niet correct wordt opgelost. Voor het maken van een gekoppelde server of replicatie die verwijst naar SQL VM's binnen hetzelfde virtuele netwerk, is ook een FQDN en een standaard DNS-achtervoegsel vereist. Bijvoorbeeld `SQLVM.internal.cloudapp.net`. Zie [Naamomzetting die uw eigen DNS-server gebruikt](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)voor meer informatie.

> [!IMPORTANT]
> Het bijwerken van DNS-servers van virtuele netwerken heeft niet onmiddellijk invloed op Managed Instance. Managed Instance DNS-configuratie wordt bijgewerkt nadat de DHCP-lease is verlopen of na de platformupgrade, wat het geval is. **Gebruikers wordt geadviseerd om hun virtuele netwerk DNS-configuratie in te stellen voordat ze hun eerste Managed Instance maken.**

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md) een overzicht
- Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een zelfstudie waarin u laat zien hoe u een nieuw beheerde instantie maakt.
- Zie [VNet-configuratie voor beheerde exemplaren voor](sql-database-managed-instance-connectivity-architecture.md) informatie over het configureren van een VNet voor een beheerde instantie
