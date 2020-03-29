---
title: Firewallregels - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: In dit artikel worden firewallregels voor Azure Database voor PostgreSQL - Hyperscale (Citus) beschreven.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975564"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Firewallregels in Azure Database voor PostgreSQL - Hyperscale (Citus)
Azure Database voor PostgreSQL-serverfirewall voorkomt alle toegang tot uw knooppunt hyperscale (Citus) coördinator totdat u opgeeft welke computers toestemming hebben. De firewall verleent toegang tot de server op basis van het oorspronkelijke IP-adres van elk verzoek.
U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U firewallregels maken op serverniveau.

**Firewallregels:** Met deze regels kunnen clients toegang krijgen tot uw knooppunt van de Hyperscale-coördinator (Citus), dat wil zeggen alle databases binnen dezelfde logische server. Firewallregels op serverniveau kunnen worden geconfigureerd met behulp van de Azure-portal. Als u firewallregels op serverniveau wilt maken, moet u de eigenaar van het abonnement of een abonnementsbijdrager zijn.

## <a name="firewall-overview"></a>Firewalloverzicht
Alle databasetoegang tot uw coördinatorknooppunt wordt standaard geblokkeerd door de firewall. Als u uw server vanaf een andere computer wilt gebruiken, moet u een of meer firewallregels op serverniveau opgeven om toegang tot uw server in te schakelen. Gebruik de firewallregels om op te geven welk IP-adres van internet wordt toegestaan. De toegang tot de Azure-portalwebsite zelf wordt niet beïnvloed door de firewallregels.
Verbindingspogingen van internet en Azure moeten eerst door de firewall gaan voordat ze uw PostgreSQL-database kunnen bereiken, zoals in het volgende diagram wordt weergegeven:

![Voorbeeldstroom van hoe de firewall werkt](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Verbinding maken vanaf internet en vanuit Azure

Een Hyperscale (Citus) servergroep firewall bepaalt wie verbinding kan maken met het coördinatorknooppunt van de groep. De firewall bepaalt de toegang door een configureerbare lijst met regels te raadplegen. Elke regel is een IP-adres, of bereik van adressen, die zijn toegestaan in.

Wanneer de firewall verbindingen blokkeert, kan dit toepassingsfouten veroorzaken. Met behulp van de PostgreSQL JDBC driver, bijvoorbeeld, verhoogt een fout als deze:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

Zie [Firewallregels maken en beheren](howto-hyperscale-manage-firewall-using-portal.md) om te leren hoe de regels worden gedefinieerd.

## <a name="troubleshooting-the-database-server-firewall"></a>Problemen met de firewall van de databaseserver oplossen
Wanneer de toegang tot de Microsoft Azure Database for PostgreSQL - Hyperscale (Citus) service zich niet gedraagt zoals u verwacht, moet u rekening houden met deze punten:

* **Wijzigingen in de lijst met toegestane zijn nog niet van kracht geworden:** Er kan wel vijf minuten vertraging zijn voor wijzigingen in de Hyperscale (Citus) firewallconfiguratie die van kracht worden.

* **De gebruiker is niet geautoriseerd of er is een onjuist wachtwoord gebruikt:** Als een gebruiker geen machtigingen op de server heeft of als het gebruikte wachtwoord onjuist is, wordt de verbinding met de server geweigerd. Het maken van een firewall-instelling biedt clients alleen de mogelijkheid om verbinding te maken met uw server. elke client moet nog steeds de nodige beveiligingsreferenties opgeven.

Met behulp van een JDBC-client kan bijvoorbeeld de volgende fout worden weergegeven.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: wachtwoordverificatie mislukt voor gebruiker "yourusername"

* **Dynamisch IP-adres:** als u een internetverbinding hebt met dynamische IP-adressering en problemen ondervindt bij het passeren van de firewall, kunt u een van de volgende oplossingen proberen:

* Vraag uw Internet Service Provider (ISP) naar het IP-adresbereik dat is toegewezen aan uw clientcomputers die toegang hebben tot het knooppunt hyperscale (Citus) en voeg vervolgens het IP-adresbereik toe als firewallregel.

* Download in plaats daarvan statische IP-adressering voor uw clientcomputers en voeg vervolgens het statische IP-adres toe als firewallregel.

## <a name="next-steps"></a>Volgende stappen
Zie voor artikelen over het maken van firewallregels op serverniveau en firewall op databaseniveau:
* [Azure Database voor PostgreSQL-firewallregels maken en beheren met behulp van de Azure-portal](howto-hyperscale-manage-firewall-using-portal.md)
