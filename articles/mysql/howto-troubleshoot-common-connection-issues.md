---
title: Verbindingsproblemen oplossen - Azure Database voor MySQL
description: Meer informatie over het oplossen van verbindingsproblemen met Azure Database voor MySQL, inclusief tijdelijke fouten die opnieuw moeten worden geprobeerd, firewallproblemen en onderbrekingen.
keywords: mysql-verbinding, verbindingstekenreeks, verbindingsproblemen, tijdelijke fout, verbindingsfout
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: a68f351276c47f623728a279ad551744c61b6d14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063231"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Verbindingsproblemen met Azure Databases for MySQL oplossen

Verbindingsproblemen kunnen worden veroorzaakt door verschillende dingen, waaronder:

* Firewallinstellingen
* Time-out verbinding
* Onjuiste inloggegevens
* Maximale limiet bereikt voor sommige Azure Database voor MySQL-resources
* Problemen met de infrastructuur van de dienst
* Onderhoud in de service
* De rekentoewijzing van de server wordt gewijzigd door het aantal vCores te schalen of naar een andere servicelaag te gaan

Over het algemeen kunnen verbindingsproblemen met Azure Database voor MySQL als volgt worden geclassificeerd:

* Tijdelijke fouten (kortstondig of intermitterend)
* Permanente of niet-tijdelijke fouten (fouten die zich regelmatig voordoen)

## <a name="troubleshoot-transient-errors"></a>Tijdelijke fouten oplossen

Tijdelijke fouten optreden wanneer onderhoud wordt uitgevoerd, het systeem een fout ondervindt met de hardware of software of u de vCores of servicelaag van uw server wijzigt. De Azure Database for MySQL-service heeft een ingebouwde hoge beschikbaarheid en is ontworpen om dit soort problemen automatisch te beperken. Uw toepassing verliest echter zijn verbinding met de server voor een korte periode van meestal minder dan 60 seconden. Sommige gebeurtenissen kunnen soms langer duren om te beperken, bijvoorbeeld wanneer een grote transactie een langdurig herstel veroorzaakt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen om tijdelijke verbindingsproblemen op te lossen

1. Controleer het [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) op bekende storingen die zich hebben voorgedaan tijdens de tijd waarin de fouten door de toepassing zijn gerapporteerd.
2. Toepassingen die verbinding maken met een cloudservice zoals Azure Database voor MySQL, moeten tijdelijke fouten verwachten en een nieuwe logica implementeren om deze fouten te verwerken in plaats van deze als toepassingsfouten voor gebruikers te vernemen. Controleer [de afhandeling van tijdelijke verbindingsfouten voor Azure Database voor MySQL](concepts-connectivity.md) voor aanbevolen procedures en ontwerprichtlijnen voor het verwerken van tijdelijke fouten.
3. Als een server de resourcelimieten nadert, kunnen fouten tijdelijk verbindingsprobleem lijken te zijn. Zie [Beperkingen in Azure Database voor MySQL](concepts-limits.md).
4. Als er verbindingsproblemen blijven optreden of als de duur waarvoor uw toepassing de fout tegenkomt meer dan 60 seconden bedraagt of als u meerdere exemplaren van de fout op een bepaalde dag ziet, dient u een Azure-ondersteuningsaanvraag in door **Ondersteuning op** de [Azure Support-site](https://azure.microsoft.com/support/options) te selecteren.

## <a name="troubleshoot-persistent-errors"></a>Permanente fouten oplossen

Als de toepassing voortdurend geen verbinding maakt met Azure Database voor MySQL, wordt meestal een probleem met een van de volgende opties aangegeven:

* Serverfirewallconfiguratie: controleer of de firewall van de Azure Database for MySQL-server is geconfigureerd om verbindingen van uw client toe te staan, inclusief proxyservers en gateways.
* Clientfirewallconfiguratie: de firewall op uw client moet verbindingen met uw databaseserver toestaan. IP-adressen en poorten van de server die u niet hebben, moeten worden toegestaan, evenals toepassingsnamen zoals MySQL in sommige firewalls.
* Gebruikersfout: u hebt mogelijk verkeerd getypte verbindingsparameters, zoals de servernaam in de verbindingstekenreeks of een ontbrekend * \@servernaamachtervoegsel* in de gebruikersnaam.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Stappen om permanente verbindingsproblemen op te lossen

1. Firewallregels [firewall rules](howto-manage-firewall-using-portal.md) instellen om het IP-adres van de client toe te staan. Alleen voor tijdelijke testdoeleinden stelt u een firewallregel in met 0.0.0.0 als start-IP-adres en met 255.255.255.255 als het uiteindelijke IP-adres. Hierdoor wordt de server geopend voor alle IP-adressen. Als dit uw verbindingsprobleem oplost, verwijdert u deze regel en maakt u een firewallregel voor een op de juiste manier beperkt IP-adres- of adresbereik.
2. Zorg er op alle firewalls tussen de client en het internet voor dat poort 3306 open is voor uitgaande verbindingen.
3. Controleer uw verbindingstekenreeks en andere verbindingsinstellingen. Controleren [Hoe u toepassingen verbinden met Azure Database voor MySQL](howto-connection-string.md).
4. Controleer de servicestatus in het dashboard. Als u denkt dat er een regionale storing is, raadpleegt u [Overzicht van bedrijfscontinuïteit met Azure Database voor MySQL](concepts-business-continuity.md) voor stappen om te herstellen naar een nieuwe regio.

## <a name="next-steps"></a>Volgende stappen

* [Verwerking van tijdelijke verbindingsfouten voor Azure Database voor MySQL](concepts-connectivity.md)
