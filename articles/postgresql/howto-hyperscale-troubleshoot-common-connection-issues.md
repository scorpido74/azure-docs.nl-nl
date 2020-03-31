---
title: Problemen met verbindingen oplossen - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Meer informatie over het oplossen van verbindingsproblemen met Azure Database voor PostgreSQL - Hyperscale (Citus)
keywords: postgresql-verbinding, verbindingstekenreeks, verbindingsproblemen, tijdelijke fout, verbindingsfout
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977502"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Verbindingsproblemen met Azure Database voor PostgreSQL - Hyperscale (Citus) oplossen

Verbindingsproblemen kunnen worden veroorzaakt door verschillende dingen, zoals:

* Firewallinstellingen
* Time-out verbinding
* Onjuiste aanmeldingsgegevens
* Verbindingslimiet bereikt voor servergroep
* Problemen met de infrastructuur van de dienst
* Onderhoudsonderhoud
* De coördinator knooppunt niet over naar nieuwe hardware

Over het algemeen kunnen verbindingsproblemen met Hyperscale als volgt worden geclassificeerd:

* Tijdelijke fouten (kortstondig of intermitterend)
* Permanente of niet-tijdelijke fouten (fouten die zich regelmatig voordoen)

## <a name="troubleshoot-transient-errors"></a>Tijdelijke fouten oplossen

Tijdelijke fouten optreden om een aantal redenen. De meest voorkomende zijn systeemonderhoud, fout met hardware of software en coördinator node vCore-upgrades.

Het inschakelen van hoge beschikbaarheid voor knooppunten van hyperscale-serverskan dit soort problemen automatisch beperken. Uw aanvraag moet echter nog steeds bereid zijn om de verbinding kort te verliezen. Ook andere gebeurtenissen kunnen langer duren om te beperken, zoals wanneer een grote transactie een langdurig herstel veroorzaakt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen om tijdelijke verbindingsproblemen op te lossen

1. Controleer het [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) op bekende storingen die zich hebben voorgedaan tijdens de tijd waarin de toepassing fouten rapporteerde.
2. Toepassingen die verbinding maken met een cloudservice zoals Hyperscale (Citus) moeten tijdelijke fouten verwachten en gracieus reageren. Toepassingen moeten bijvoorbeeld logica voor nieuwe apparaten implementeren om deze fouten te verwerken in plaats van ze te vereersten als toepassingsfouten voor gebruikers.
3. Als de servergroep de resourcelimieten nadert, kunnen fouten tijdelijke verbindingsproblemen lijken. Het verhogen van knooppunt-RAM, het toevoegen van werknemersknooppunten en het opnieuw in evenwicht brengen van gegevens kan helpen.
4. Als verbindingsproblemen blijven bestaan of langer dan 60 seconden duren of meer dan één keer per dag optreden, dient u een Azure-ondersteuningsaanvraag in door Ondersteuning op de [Azure Support-site](https://azure.microsoft.com/support/options) **te** selecteren.

## <a name="troubleshoot-persistent-errors"></a>Permanente fouten oplossen

Als de toepassing voortdurend geen verbinding maakt met Hyperscale (Citus), zijn de meest voorkomende oorzaken een fout van de firewall of een fout van de gebruiker.

* Coördinator node firewall configuratie: Zorg ervoor dat de Hyperscale server firewall is geconfigureerd om verbindingen van uw client, met inbegrip van proxy servers en gateways mogelijk te maken.
* Clientfirewallconfiguratie: de firewall op uw client moet verbindingen met uw databaseserver toestaan. Sommige firewalls vereisen dat niet alleen toepassing op naam, maar waardoor de IP-adressen en poorten van de server.
* Gebruikersfout: Controleer de verbindingstekenreeks opnieuw. Mogelijk hebt u verkeerd getypte parameters zoals de servernaam. U verbindingstekenreeksen voor verschillende taalframeworks en psql vinden in de Azure-portal. Ga naar de pagina **Verbindingstekenreeksen** in de servergroep Hyperscale (Citus). Houd er ook rekening mee dat Hyperscale (Citus) clusters slechts één database hebben en de vooraf gedefinieerde naam **citus**is.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Stappen om permanente verbindingsproblemen op te lossen

1. Firewallregels [firewall rules](howto-hyperscale-manage-firewall-using-portal.md) instellen om het IP-adres van de client toe te staan. Alleen voor tijdelijke testdoeleinden stelt u een firewallregel in met 0.0.0.0 als start-IP-adres en met 255.255.255.255 als het uiteindelijke IP-adres. Met die regel wordt de server geopend voor alle IP-adressen. Als de regel uw verbindingsprobleem oplost, verwijdert u deze en maakt u een firewallregel voor een op de juiste manier beperkt IP-adres- of adresbereik.
2. Zorg er op alle firewalls tussen de client en het internet voor dat poort 5432 open is voor uitgaande verbindingen.
3. Controleer uw verbindingstekenreeks en andere verbindingsinstellingen.
4. Controleer de servicestatus in het dashboard.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de concepten [firewallregels in Azure Database voor PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md)
* Bekijk hoe [u firewallregels voor Azure Database voor PostgreSQL - Hyperscale (Citus) beheert](howto-hyperscale-manage-firewall-using-portal.md)
