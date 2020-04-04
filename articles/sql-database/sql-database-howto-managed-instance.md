---
title: Een beheerde instantie configureren
description: Meer informatie over het configureren en beheren van azure SQL Database managed instance.
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
ms.openlocfilehash: b56375388f6293d27bcd2f2548d8b20205a92b15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638035"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Een beheerde instantie gebruiken in Azure SQL Database

In dit artikel vindt u verschillende handleidingen, scripts en uitleg die u kunnen helpen bij het beheren en configureren van uw beheerde instantie.

## <a name="migration"></a>Migratie

- [Migratie naar een beheerde instantie](sql-database-managed-instance-migrate.md) : meer informatie over het aanbevolen migratieproces en de hulpprogramma's voor migratie naar een beheerde instantie.

- [TDE-certificaat migreren naar een beheerde instantie](sql-database-managed-instance-migrate-tde-certificate.md) : als uw SQL Server-database is beveiligd met transparante gegevensversleuteling (TDE), moet u het certificaat migreren dat een beheerde instantie kan gebruiken om de back-up te decoderen die u in Azure wilt herstellen.

## <a name="network-configuration"></a>Netwerkconfiguratie

- [Grootte van een beheerd instantiesubnet bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md) : de beheerde instantie wordt in het subnet geplaatst dat niet kan worden aangepast zodra u de resources binnenvoegt. Daarom moet u berekenen welk IP-bereik van adressen nodig zou zijn voor het subnet, afhankelijk van het aantal en de typen exemplaren die u in het subnet wilt implementeren.
- [Maak nieuwe VNet en subnet voor een beheerde instantie](sql-database-managed-instance-create-vnet-subnet.md) - Azure VNet en subnet waar u uw beheerde exemplaren wilt implementeren, moeten worden geconfigureerd volgens de [hier beschreven netwerkvereisten.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) In deze handleiding vindt u de eenvoudigste manier om uw nieuwe VNet en subnet goed geconfigureerd voor beheerde exemplaren te maken.
- [Configureer bestaande VNet en subnet voor een beheerde instantie](sql-database-managed-instance-configure-vnet-subnet.md) - als u uw bestaande VNet en subnet wilt configureren om beheerde exemplaren binnenin te implementeren, vindt u hier het script dat de [netwerkvereisten](sql-database-managed-instance-connectivity-architecture.md#network-requirements) controleert en uw subnet configureert volgens de vereisten.
- [Aangepaste DNS configureren](sql-database-managed-instance-custom-dns.md) : u moet aangepaste DNS configureren als u toegang wilt krijgen tot externe bronnen op de aangepaste domeinen van uw beheerde instantie via gekoppelde server van db-e-mailprofielen.
- [Netwerkconfiguratie synchroniseren](sql-database-managed-instance-sync-network-configuration.md) - Het kan voorkomen dat u uw app hebt [geïntegreerd met een Azure Virtual Network,](../app-service/web-sites-integrate-with-vnet.md)maar dat u geen verbinding&#39;met een beheerde instantie. Een ding dat u proberen is het vernieuwen van de netwerkconfiguratie voor uw serviceplan.
- [Ip-adres voor beheereindpunt zoeken](sql-database-managed-instance-find-management-endpoint-ip-address.md) : beheerde instantie gebruikt openbaar eindpunt voor beheerdoeleinden. U het IP-adres van het beheereindpunt bepalen met behulp van het script dat hier wordt beschreven.
- [Controleer de ingebouwde firewallbeveiliging](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) : beheerde instantie is beveiligd met een ingebouwde firewall waarmee het verkeer alleen op de benodigde poorten kan worden gebruikt. U de ingebouwde firewallregels controleren en verifiëren met behulp van het script dat in deze handleiding wordt beschreven.
- [Toepassingen verbinden](sql-database-managed-instance-connect-app.md) : beheerde instantie wordt in uw eigen azure VNet met privé-IP-adres geplaatst. Meer informatie over verschillende patronen voor het verbinden van de toepassingen met uw beheerde instantie.

## <a name="feature-configuration"></a>Functieconfiguratie

- [Transactionele replicatie](replication-with-sql-database-managed-instance.md) stelt u in staat om uw gegevens te repliceren tussen beheerde exemplaren of van on-premises SQL Server naar een beheerde instantie, en vice versa. Meer informatie over het gebruik en configureren van transactiereplicatie vindt u in deze handleiding.
- [Bedreigingsdetectie configureren](sql-database-managed-instance-threat-detection.md) – [bedreigingsdetectie](sql-database-threat-detection-overview.md) is een ingebouwde Azure SQL Database-functie die verschillende potentiële aanvallen detecteert, zoals SQL Injection of toegang vanaf verdachte locaties. In deze handleiding u leren hoe u [bedreigingsdetectie](sql-database-threat-detection-overview.md) in- en configureert voor een beheerde instantie.
- [Met het maken van waarschuwingen](sql-database-managed-instance-alerts.md) u waarschuwingen instellen op bewaakte statistieken, zoals CPU-gebruik, opslagruimteverbruik, IOPS en anderen voor beheerde bijvoorbeeld. In deze handleiding leert u hoe u waarschuwingen voor beheerde bijvoorbeeld inschakelen en configureren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [handleidingen voor afzonderlijke databases](sql-database-howto-single-database.md)
