---
title: Verbindingsproblemen oplossen - Azure Database voor MariaDB
description: Meer informatie over het oplossen van verbindingsproblemen met Azure Database voor MariaDB, inclusief tijdelijke fouten die opnieuw moeten worden geprobeerd, firewallproblemen en onderbrekingen.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 6817807d0a85b1465beafc86045ea1e0dd8a31ec
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770350"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Verbindingsproblemen met Azure Database for MariaDB oplossen

Verbindingsproblemen kunnen worden veroorzaakt door verschillende dingen, waaronder:

* Firewallinstellingen
* Time-out verbinding
* Onjuiste inloggegevens
* Maximale limiet bereikt voor sommige Azure Database voor MariaDB-resources
* Problemen met de infrastructuur van de dienst
* Onderhoud in de service
* De rekentoewijzing van de server wordt gewijzigd door het aantal vCores te schalen of naar een andere servicelaag te gaan

Over het algemeen kunnen verbindingsproblemen met Azure Database voor MariaDB als volgt worden geclassificeerd:

* Tijdelijke fouten (kortstondig of intermitterend)
* Permanente of niet-tijdelijke fouten (fouten die zich regelmatig voordoen)

## <a name="troubleshoot-transient-errors"></a>Tijdelijke fouten oplossen

Tijdelijke fouten optreden wanneer onderhoud wordt uitgevoerd, het systeem een fout ondervindt met de hardware of software of u de vCores of servicelaag van uw server wijzigt. De Azure Database for MariaDB-service heeft een ingebouwde hoge beschikbaarheid en is ontworpen om dit soort problemen automatisch te beperken. Uw toepassing verliest echter zijn verbinding met de server voor een korte periode van meestal minder dan 60 seconden. Sommige gebeurtenissen kunnen soms langer duren om te beperken, bijvoorbeeld wanneer een grote transactie een langdurig herstel veroorzaakt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen om tijdelijke verbindingsproblemen op te lossen

1. Controleer het [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) op bekende storingen die zich hebben voorgedaan tijdens de tijd waarin de fouten door de toepassing zijn gerapporteerd.
2. Toepassingen die verbinding maken met een cloudservice zoals Azure Database voor MariaDB, moeten tijdelijke fouten verwachten en een nieuwe logica implementeren om deze fouten te verwerken in plaats van deze als toepassingsfouten voor gebruikers te vernemen. Controleer [de afhandeling van tijdelijke verbindingsfouten voor Azure Database voor MariaDB](concepts-connectivity.md) voor aanbevolen procedures en ontwerprichtlijnen voor het verwerken van tijdelijke fouten.
3. Als een server de resourcelimieten nadert, kunnen fouten tijdelijk verbindingsprobleem lijken te zijn. Zie [Beperkingen in Azure Database voor MariaDB](concepts-limits.md).
4. Als er verbindingsproblemen blijven optreden of als de duur waarvoor uw toepassing de fout tegenkomt meer dan 60 seconden bedraagt of als u meerdere exemplaren van de fout op een bepaalde dag ziet, dient u een Azure-ondersteuningsaanvraag in door **Ondersteuning op** de [Azure Support-site](https://azure.microsoft.com/support/options) te selecteren.

## <a name="troubleshoot-persistent-errors"></a>Permanente fouten oplossen

Als de toepassing voortdurend geen verbinding maakt met Azure Database voor MariaDB, wordt meestal een probleem met een van de volgende opties aangegeven:

* Firewallconfiguratie: de Azure Database voor MariaDB-server of firewall aan de clientzijde blokkeert verbindingen.
* Netwerkherconfiguratie aan de clientzijde: er is een nieuw IP-adres of een proxyserver toegevoegd.
* Gebruikersfout: u bijvoorbeeld verkeerd getypte verbindingsparameters hebben, zoals de servernaam * \@* in de verbindingstekenreeks of een ontbrekend servernaamachtervoegsel in de gebruikersnaam.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Stappen om permanente verbindingsproblemen op te lossen

1. Firewallregels [firewall rules](howto-manage-firewall-portal.md) instellen om het IP-adres van de client toe te staan. Alleen voor tijdelijke testdoeleinden stelt u een firewallregel in met 0.0.0.0 als start-IP-adres en met 255.255.255.255 als het uiteindelijke IP-adres. Hierdoor wordt de server geopend voor alle IP-adressen. Als dit uw verbindingsprobleem oplost, verwijdert u deze regel en maakt u een firewallregel voor een op de juiste manier beperkt IP-adres- of adresbereik.
2. Zorg er op alle firewalls tussen de client en het internet voor dat poort 3306 open is voor uitgaande verbindingen.
3. Controleer uw verbindingstekenreeks en andere verbindingsinstellingen. Controleren [Hoe u toepassingen koppelen aan Azure Database voor MariaDB](howto-connection-string.md).
4. Controleer de servicestatus in het dashboard. Als u denkt dat er een regionale storing is, raadpleegt u [Overzicht van bedrijfscontinuïteit met Azure Database voor MariaDB](concepts-business-continuity.md) voor stappen om te herstellen naar een nieuwe regio.

## <a name="next-steps"></a>Volgende stappen

* [Verwerking van tijdelijke verbindingsfouten voor Azure Database voor MariaDB](concepts-connectivity.md)
