---
title: Een beheerd exemplaar configureren
description: Meer informatie over het configureren en beheren van Azure SQL Database Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357623"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Een beheerd exemplaar gebruiken in Azure SQL Database

In dit artikel vindt u verschillende hand leidingen, scripts en uitleg die u kunnen helpen bij het beheren en configureren van uw beheerde exemplaar.

## <a name="migration"></a>Migratie

- [Migreren naar een beheerd exemplaar](sql-database-managed-instance-migrate.md) : meer informatie over het aanbevolen migratie proces en hulpprogram ma's voor migratie naar een beheerd exemplaar.

- [TDe-certificaat migreren naar een beheerd exemplaar](sql-database-managed-instance-migrate-tde-certificate.md) : als uw SQL Server-Data Base wordt beveiligd met transparent Data Encryption (TDE), moet u het certificaat migreren dat door een beheerd exemplaar kan worden gebruikt voor het ontsleutelen van de back-up die u wilt herstellen in Azure.

## <a name="network-configuration"></a>Netwerkconfiguratie

- De [grootte van een beheerd exemplaar van het subnet bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md) : het beheerde exemplaar wordt in het subnet reserveren dat niet kan worden gewijzigd nadat u de resources in hebt toegevoegd. Daarom moet u berekenen welk IP-adres bereik voor het subnet is vereist, afhankelijk van het aantal en de typen exemplaren dat u in het subnet wilt implementeren.
- [Nieuw VNet en subnet maken voor een beheerd exemplaar](sql-database-managed-instance-create-vnet-subnet.md) : Azure VNet en subnet waar u uw beheerde exemplaren wilt implementeren, moeten worden geconfigureerd volgens de [hier beschreven netwerk vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements). In deze hand leiding vindt u de eenvoudigste manier om uw nieuwe VNet en subnet te maken die op de juiste wijze zijn geconfigureerd voor beheerde exemplaren.
- [Een bestaand vnet en subnet voor een beheerd exemplaar configureren](sql-database-managed-instance-configure-vnet-subnet.md) : als u uw bestaande vnet en subnet wilt configureren voor het implementeren van beheerde exemplaren in, kunt u hier het script vinden dat de [netwerk vereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements) controleert en het configureren van uw subnet volgens de vereisten.
- [Aangepaste DNS configureren](sql-database-managed-instance-custom-dns.md) : u moet aangepaste DNS configureren als u via een gekoppelde server van DB e-mail profielen toegang wilt krijgen tot externe resources op de aangepaste domeinen van uw beheerde exemplaar.
- [Netwerk configuratie synchroniseren](sql-database-managed-instance-sync-network-configuration.md) : Hoewel u [uw app hebt geïntegreerd met een Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md), kunt&#39;u verbinding maken met een beheerd exemplaar. U kunt proberen om de netwerk configuratie voor uw service abonnement te vernieuwen.
- [IP-adres van beheer eindpunt zoeken](sql-database-managed-instance-find-management-endpoint-ip-address.md) : beheerde instantie gebruikt open bare eind punt voor beheer doeleinden. U kunt het IP-adres van het beheer eindpunt bepalen met behulp van het script dat hier wordt beschreven.
- [Controleer of](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) het beheerde exemplaar van de firewall beveiliging is beveiligd met een ingebouwde firewall waarmee het verkeer alleen op de benodigde poorten is toegestaan. U kunt de ingebouwde firewall regels controleren en controleren met behulp van het script dat in deze hand leiding wordt beschreven.
- [Connect-toepassingen](sql-database-managed-instance-connect-app.md) : het beheerde exemplaar wordt in uw eigen persoonlijke Azure VNet geplaatst met een privé IP-adres. Meer informatie over verschillende patronen voor het verbinden van de toepassingen met uw beheerde exemplaar.

## <a name="feature-configuration"></a>Configuratie van onderdelen

- Met [transactionele replicatie](replication-with-sql-database-managed-instance.md) kunt u uw gegevens repliceren tussen beheerde instanties, of van on-premises SQL Server naar een beheerd exemplaar, en omgekeerd. Meer informatie over het gebruik en configureren van transactie replicatie in deze hand leiding.
- [Detectie van bedreigingen configureren](sql-database-managed-instance-threat-detection.md) : [bedreigingen detectie](sql-database-threat-detection-overview.md) is een ingebouwde Azure SQL database functie die verschillende mogelijke aanvallen detecteert zoals SQL-injectie of toegang vanaf verdachte locaties. In deze hand leiding vindt u informatie over het inschakelen en configureren van [detectie van bedreigingen](sql-database-threat-detection-overview.md) voor een beheerd exemplaar.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [hand leidingen voor afzonderlijke data bases](sql-database-howto-single-database.md)