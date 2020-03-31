---
title: Firewallregels - Azure Database voor MySQL
description: Meer informatie over het gebruik van firewallregels om verbindingen met uw Azure Database voor MySQL-server in te schakelen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: a82d2317314c79a82fe80c5a25afc950fb728815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155193"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database voor MySQL-serverfirewallregels
Firewalls voorkomen dat alle toegang tot uw databaseserver totdat u opgeeft welke computers toestemming hebben. De firewall verleent toegang tot de server op basis van het oorspronkelijke IP-adres van elk verzoek.

Als u een firewall wilt configureren, maakt u firewallregels die bereiken van acceptabele IP-adressen opgeven. U firewallregels maken op serverniveau.

**Firewallregels:** Met deze regels kunnen clients toegang krijgen tot uw volledige Azure Database voor MySQL-server, dat wil zeggen alle databases binnen dezelfde logische server. Firewallregels op serverniveau kunnen worden geconfigureerd met behulp van de Azure-portal of Azure CLI-opdrachten. Als u firewallregels op serverniveau wilt maken, moet u de eigenaar van het abonnement of een abonnementsbijdrager zijn.

## <a name="firewall-overview"></a>Firewalloverzicht
Alle databasetoegang tot uw Azure Database voor MySQL-server wordt standaard geblokkeerd door de firewall. Als u uw server vanaf een andere computer wilt gebruiken, moet u een of meer firewallregels op serverniveau opgeven om toegang tot uw server in te schakelen. Gebruik de firewallregels om op te geven welk IP-adres van internet wordt toegestaan. De toegang tot de Azure-portalwebsite zelf wordt niet beïnvloed door de firewallregels.

Verbindingspogingen van internet en Azure moeten eerst door de firewall gaan voordat ze uw Azure Database voor MySQL-database kunnen bereiken, zoals in het volgende diagram wordt weergegeven:

![Voorbeeldstroom van hoe de firewall werkt](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Verbinding maken vanuit internet
Firewallregels op serverniveau zijn van toepassing op alle databases op de Azure Database voor MySQL-server.

Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de firewallregels op serverniveau, wordt de verbinding verleend.

Als het IP-adres van de aanvraag buiten de bereiken valt die zijn opgegeven in een van de firewallregels op databaseniveau of op serverniveau, mislukt de verbindingsaanvraag.

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Het wordt aanbevolen om het uitgaande IP-adres van een toepassing of service te vinden en expliciet toegang te verlenen tot die afzonderlijke IP-adressen of -bereiken. U bijvoorbeeld het uitgaande IP-adres van een Azure App-service vinden of een openbaar IP-adres gebruiken dat is gekoppeld aan een virtuele machine of andere bron (zie hieronder voor informatie over het maken met het privé-IP-adres van een virtuele machine via serviceeindpunten). 

Als er geen vast uitgaand IP-adres beschikbaar is voor uw Azure-service, u overwegen verbindingen in te schakelen vanaf alle AZURE-datacenter-IP-adressen. Deze instelling kan vanuit de Azure-portal worden ingeschakeld door de optie **Toegang tot Azure-services toestaan in** te stellen in **het** **beveiligingsdeelvenster Verbinding** en op Opslaan te **drukken.** Vanuit Azure CLI doet een firewallregelinstelling met start- en eindadres gelijk aan 0.0.0.0 het equivalent. Als de verbindingspoging niet is toegestaan, bereikt de aanvraag de Azure Database voor MySQL-server niet.

> [!IMPORTANT]
> Met de optie **Toegang tot Azure-services toestaan,** configureert u de firewall zodat alle verbindingen van Azure, inclusief verbindingen van de abonnementen van andere klanten, kunnen worden toegestaan. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

![Toegang toestaan tot Azure-services in de portal configureren](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Verbinding maken vanaf een VNet
Als u vanaf een VNet veilig verbinding wilt maken met uw Azure Database voor MySQL-server, u [vnet-serviceeindpunten gebruiken.](./concepts-data-access-and-security-vnet.md) 

## <a name="programmatically-managing-firewall-rules"></a>Firewallregels programmatisch beheren
Naast de Azure-portal kunnen firewallregels programmatisch worden beheerd met behulp van de Azure CLI. Zie ook [Azure Database maken en beheren voor MySQL-firewallregels met Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Firewallproblemen oplossen
Houd rekening met de volgende punten wanneer de toegang tot de Microsoft Azure Database for MySQL-serverservice zich niet gedraagt zoals verwacht:

* **Wijzigingen in de lijst met toegestane zijn nog niet van kracht geworden:** Er kan wel vijf minuten vertraging optreden voor wijzigingen in de Azure Database voor MySQL Server-firewallconfiguratie.

* **De login is niet geautoriseerd of er is een onjuist wachtwoord gebruikt:** Als een aanmelding geen machtigingen heeft op de Azure Database voor MySQL-server of als het gebruikte wachtwoord onjuist is, wordt de verbinding met de Azure Database voor MySQL-server geweigerd. Door een firewallinstellingen te maken, krijgen clients alleen de mogelijkheid om te proberen verbinding te maken met de server. Elke client moet alsnog de benodigde beveiligingsreferenties opgeven.

* **Dynamisch IP-adres:** Als u een internetverbinding hebt met dynamische IP-adressering en u problemen ondervindt om via de firewall te komen, u een van de volgende oplossingen proberen:

   * Vraag uw Internet Service Provider (ISP) naar het IP-adresbereik dat is toegewezen aan uw clientcomputers die toegang hebben tot de Azure Database voor MySQL-server en voeg vervolgens het IP-adresbereik toe als firewallregel.

   * Vraag in plaats daarvan statische IP-adressen voor uw clientcomputers en voeg de IP-adressen als firewallregels toe.

* **Het IP-adres van de server lijkt openbaar te zijn:** Verbindingen met de Azure Database for MySQL-server worden doorgestuurd via een openbaar toegankelijke Azure-gateway. Het daadwerkelijke IP-adres van de server wordt echter beschermd door de firewall. Ga voor meer informatie naar het [artikel over connectiviteitsarchitectuur.](concepts-connectivity-architecture.md) 

## <a name="next-steps"></a>Volgende stappen

* [Azure Database voor MySQL-firewallregels maken en beheren met behulp van de Azure-portal](./howto-manage-firewall-using-portal.md)
* [Azure Database voor MySQL-firewallregels maken en beheren met Azure CLI](./howto-manage-firewall-using-cli.md)
- [VNet-serviceeindpunten in Azure Database voor MySQL](./concepts-data-access-and-security-vnet.md)
