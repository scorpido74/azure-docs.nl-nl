---
title: Firewall regels van Azure Database for MariaDB server
description: Meer informatie over het gebruik van firewall regels om verbindingen met uw Azure Database for MariaDB server in te scha kelen.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 89c4bce33b80e988a9da363a89854e921bee30b0
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973661"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Firewall regels van Azure Database for MariaDB server
Firewalls verhinderen alle toegang tot uw database server totdat u opgeeft welke computers zijn gemachtigd. De firewall verleent toegang tot de server op basis van het oorspronkelijke IP-adres van elke aanvraag.

Als u een firewall wilt configureren, maakt u firewall regels die bereiken van acceptabele IP-adressen opgeven. U kunt Firewall regels maken op server niveau.

**Firewall regels:** Met deze regels kunnen clients toegang krijgen tot uw hele Azure Database for MariaDB server, dat wil zeggen, alle data bases binnen dezelfde logische server. Firewall regels op server niveau kunnen worden geconfigureerd met behulp van de Azure Portal-of Azure CLI-opdrachten. Als u firewall regels op server niveau wilt maken, moet u de eigenaar van het abonnement of een mede werker van het abonnement zijn.

## <a name="firewall-overview"></a>Firewalloverzicht
Alle database toegang tot uw Azure Database for MariaDB-server wordt standaard geblokkeerd door de firewall. Als u uw server vanaf een andere computer wilt gebruiken, moet u een of meer firewall regels op server niveau opgeven om toegang tot uw server mogelijk te maken. Gebruik de firewall regels om op te geven welke IP-adresbereiken van Internet moeten worden toegestaan. De firewall regels hebben geen invloed op de Azure Portal website zelf.

Verbindings pogingen via internet en Azure moeten eerst door de firewall worden door gegeven voordat ze uw Azure Database for MariaDB-data base kunnen bereiken, zoals in het volgende diagram wordt weer gegeven:

![Voor beeld van de werking van de firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Verbinding maken vanuit internet
Firewall regels op server niveau zijn van toepassing op alle data bases op de Azure Database for MariaDB-server.

Als het IP-adres van de aanvraag binnen een van de bereiken ligt die zijn opgegeven in de firewall regels op server niveau, wordt de verbinding verleend.

Als het IP-adres van de aanvraag buiten het bereik valt dat is opgegeven in een van de firewall regels op database niveau of op server niveau, mislukt de verbindings aanvraag.

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Azure-verbindingen moeten zijn ingeschakeld om toepassingen van Azure toe te staan om verbinding te maken met uw Azure Database for MariaDB server. Als u bijvoorbeeld een Azure Web Apps-toepassing wilt hosten, of een toepassing die wordt uitgevoerd in een Azure-VM of als u verbinding wilt maken vanuit een Azure Data Factory Data Management Gateway. De resources hoeven zich niet in dezelfde Virtual Network (VNet) of resource groep voor de firewall regel te bevinden om deze verbindingen in te scha kelen. Wanneer een toepassing vanuit Azure probeert verbinding te maken met uw databaseserver, verifieert de firewall of Azure-verbindingen zijn toegestaan. Er zijn een aantal methoden om deze typen verbindingen in te scha kelen. Een firewallinstelling waarvan het begin- en eindadres gelijk zijn aan 0.0.0.0 geeft aan dat deze verbindingen zijn toegestaan. U kunt ook de optie toegang tot **Azure-Services toestaan** in op in de Portal instellen in het deel **venster verbindings beveiliging** en **op** **Opslaan**klikken. Als de verbindings poging niet is toegestaan, wordt de Azure Database for MariaDB server niet bereikt door de aanvraag.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorg dan dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

![Toegang tot Azure-Services in de portal toestaan configureren](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Firewallregels programmatisch beheren
Naast de Azure Portal kunnen Firewall regels programmatisch worden beheerd met behulp van de Azure CLI. 

<!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-firewall-issues"></a>Problemen met de firewall oplossen
Houd rekening met de volgende punten wanneer toegang tot de Microsoft Azure data base for MariaDB-Server service niet werkt zoals verwacht:

* **Wijzigingen in de acceptatie lijst zijn nog niet doorgevoerd:** Het kan vijf minuten duren voordat wijzigingen in de Azure Database for MariaDB Server-firewallconfiguratie van kracht zijn.

* **De aanmelding is niet geautoriseerd of er is een onjuist wacht woord gebruikt:** Als een aanmelding geen machtigingen heeft op de Azure Database for MariaDB server of het gebruikte wacht woord onjuist is, wordt de verbinding met de Azure Database for MariaDB-server geweigerd. Door een firewallinstellingen te maken, krijgen clients alleen de mogelijkheid om te proberen verbinding te maken met de server. Elke client moet alsnog de benodigde beveiligingsreferenties opgeven.

* **Dynamisch IP-adres:** Als u een Internet verbinding hebt met dynamische IP-adres sering en u problemen ondervindt met het verkrijgen van de firewall, kunt u een van de volgende oplossingen proberen:

   * Vraag uw Internet provider (ISP) naar het IP-adres bereik dat is toegewezen aan uw client computers die toegang hebben tot de Azure Database for MariaDB server en voeg het IP-adres bereik vervolgens toe als een firewall regel.

   * Vraag in plaats daarvan statische IP-adressen voor uw clientcomputers en voeg de IP-adressen als firewallregels toe.

* **Het IP-adres van de server lijkt openbaar te zijn:** Verbindingen met de Azure Database for MariaDB-server worden doorgestuurd via een openbaar toegankelijke Azure-gateway. Het daadwerkelijke IP-adres van de server wordt echter beschermd door de firewall. Raadpleeg het artikel [over de connectiviteitsarchitectuur](concepts-connectivity-architecture.md) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen
- [Azure Database for MariaDB firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->
