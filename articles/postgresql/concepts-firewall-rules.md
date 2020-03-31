---
title: Firewallregels - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt beschreven hoe u firewallregels gebruikt om verbinding te maken met Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157267"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Firewallregels in Azure Database voor PostgreSQL - Single Server
Azure Database for PostgreSQL-serverfirewall voorkomt alle toegang tot uw databaseserver totdat u opgeeft welke computers toestemming hebben. De firewall verleent toegang tot de server op basis van het oorspronkelijke IP-adres van elk verzoek.
U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U firewallregels maken op serverniveau.

**Firewallregels:** Met deze regels kunnen clients toegang krijgen tot uw volledige Azure-database voor PostgreSQL Server, dat wil zeggen alle databases binnen dezelfde logische server. Firewallregels op serverniveau kunnen worden geconfigureerd met behulp van de Azure-portal of met Azure CLI-opdrachten. Als u firewallregels op serverniveau wilt maken, moet u de eigenaar van het abonnement of een abonnementsbijdrager zijn.

## <a name="firewall-overview"></a>Firewalloverzicht
Alle toegang tot de database tot uw Azure Database voor PostgreSQL-server wordt standaard geblokkeerd door de firewall. Als u uw server vanaf een andere computer wilt gebruiken, moet u een of meer firewallregels op serverniveau opgeven om toegang tot uw server in te schakelen. Gebruik de firewallregels om op te geven welk IP-adres van internet wordt toegestaan. De toegang tot de Azure-portalwebsite zelf wordt niet beïnvloed door de firewallregels.
Verbindingspogingen van internet en Azure moeten eerst door de firewall gaan voordat ze uw PostgreSQL-database kunnen bereiken, zoals in het volgende diagram wordt weergegeven:

![Voorbeeldstroom van hoe de firewall werkt](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Verbinding maken vanuit internet
Firewallregels op serverniveau zijn van toepassing op alle databases op dezelfde Azure Database voor PostgreSQL-server. Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de firewallregels op serverniveau, wordt de verbinding toegestaan.
Als het IP-adres van de aanvraag niet binnen de bereiken valt die zijn opgegeven in een van de firewallregels op serverniveau, mislukt de verbindingsaanvraag.
Als uw toepassing bijvoorbeeld verbinding maakt met het JDBC-stuurprogramma voor PostgreSQL, u deze fout tegenkomen bij het proberen verbinding te maken wanneer de firewall de verbinding blokkeert.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Het wordt aanbevolen om het uitgaande IP-adres van een toepassing of service te vinden en expliciet toegang te verlenen tot die afzonderlijke IP-adressen of -bereiken. U bijvoorbeeld het uitgaande IP-adres van een Azure App-service vinden of een openbaar IP-adres gebruiken dat is gekoppeld aan een virtuele machine of andere bron (zie hieronder voor informatie over het maken met het privé-IP-adres van een virtuele machine via serviceeindpunten). 

Als er geen vast uitgaand IP-adres beschikbaar is voor uw Azure-service, u overwegen verbindingen in te schakelen vanaf alle AZURE-datacenter-IP-adressen. Deze instelling kan vanuit de Azure-portal worden ingeschakeld door de optie **Toegang tot Azure-services toestaan in** te stellen in **het** **beveiligingsdeelvenster Verbinding** en op Opslaan te **drukken.** Vanuit Azure CLI doet een firewallregelinstelling met start- en eindadres gelijk aan 0.0.0.0 het equivalent. Als de verbindingspoging niet is toegestaan, bereikt de aanvraag de Azure Database voor PostgreSQL-server niet.

> [!IMPORTANT]
> Met de optie **Toegang tot Azure-services toestaan,** configureert u de firewall zodat alle verbindingen van Azure, inclusief verbindingen van de abonnementen van andere klanten, kunnen worden toegestaan. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

![Toegang toestaan tot Azure-services in de portal configureren](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Verbinding maken vanaf een VNet
Als u vanaf een VNet-verbinding met uw Azure Database voor PostgreSQL-server wilt maken, u [vnet-serviceeindpunten gebruiken.](./concepts-data-access-and-security-vnet.md) 

## <a name="programmatically-managing-firewall-rules"></a>Firewallregels programmatisch beheren
Naast de Azure-portal kunnen firewallregels programmatisch worden beheerd met Azure CLI.
Zie ook [Azure Database maken en beheren voor PostgreSQL-firewallregels met Azure CLI](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Firewallproblemen oplossen
Houd rekening met de volgende punten wanneer de toegang tot de Microsoft Azure Database for PostgreSQL Server-service zich niet gedraagt zoals u verwacht:

* **Wijzigingen in de lijst met toegestane zijn nog niet van kracht geworden:** Er kan wel vijf minuten vertraging optreden voor wijzigingen in de Azure Database voor de firewallconfiguratie van PostgreSQL Server.

* **De login is niet geautoriseerd of er is een onjuist wachtwoord gebruikt:** Als een aanmelding geen machtigingen heeft op de Azure Database voor PostgreSQL-server of als het gebruikte wachtwoord onjuist is, wordt de verbinding met de Azure Database voor PostgreSQL-server geweigerd. Het maken van een firewall-instelling biedt clients alleen de mogelijkheid om verbinding te maken met uw server. elke client moet nog steeds de nodige beveiligingsreferenties opgeven.

   Met behulp van een JDBC-client kan bijvoorbeeld de volgende fout worden weergegeven.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: wachtwoordverificatie mislukt voor gebruiker "yourusername"

* **Dynamisch IP-adres:** als u een internetverbinding hebt met dynamische IP-adressering en problemen ondervindt bij het passeren van de firewall, kunt u een van de volgende oplossingen proberen:

   * Vraag uw Internet Service Provider (ISP) naar het IP-adresbereik dat is toegewezen aan uw clientcomputers die toegang hebben tot de Azure Database voor PostgreSQL Server en voeg vervolgens het IP-adresbereik toe als firewallregel.

   * Download in plaats daarvan statische IP-adressering voor uw clientcomputers en voeg vervolgens het statische IP-adres toe als firewallregel.

* **Het IP-adres van de server lijkt openbaar te zijn:** Verbindingen met de Azure Database voor PostgreSQL-server worden doorgestuurd via een openbaar toegankelijke Azure-gateway. Het daadwerkelijke IP-adres van de server wordt echter beschermd door de firewall. Ga voor meer informatie naar het [artikel over connectiviteitsarchitectuur.](concepts-connectivity-architecture.md) 

## <a name="next-steps"></a>Volgende stappen
Zie voor artikelen over het maken van firewallregels op serverniveau en firewall op databaseniveau:
* [Azure Database voor PostgreSQL-firewallregels maken en beheren met behulp van de Azure-portal](howto-manage-firewall-using-portal.md)
* [Azure Database voor PostgreSQL-firewallregels maken en beheren met Azure CLI](howto-manage-firewall-using-cli.md)
- [VNet-serviceeindpunten in Azure Database voor PostgreSQL](./concepts-data-access-and-security-vnet.md)
