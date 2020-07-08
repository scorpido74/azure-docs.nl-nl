---
title: Hoge Beschik baarheid-Azure Database for MySQL
description: In dit onderwerp vindt u informatie over hoge Beschik baarheid bij het gebruik van Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 50bf1502589ea0932fd45367c039e6e37135d761
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086055"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Concepten met hoge Beschik baarheid in Azure Database for MySQL
De Azure Database for MySQL-service biedt een gegarandeerd hoog niveau van Beschik baarheid. De financieel ondersteunde service level agreement (SLA) is 99,99% bij de algemene Beschik baarheid. Er is bijna geen toepassings tijd meer wanneer u deze service gebruikt.

## <a name="high-availability"></a>Hoge beschikbaarheid
Het model voor hoge Beschik baarheid (HA) is gebaseerd op ingebouwde mechanismen voor failover wanneer een onderbreking op knooppunt niveau optreedt. Een onderbreking op knooppunt niveau kan zich voordoen als gevolg van een hardwarestoring of als reactie op een service-implementatie.

Wijzigingen die zijn aangebracht in een Azure Database for MySQL database server, worden altijd uitgevoerd in de context van een trans actie. Wijzigingen worden synchroon vastgelegd in azure Storage wanneer de trans actie wordt doorgevoerd. Als er een onderbreking op knooppunt niveau optreedt, maakt de database server automatisch een nieuw knoop punt en koppelt deze gegevens opslag aan het nieuwe knoop punt. Actieve verbindingen worden verwijderd en eventuele trans acties met invlucht worden niet doorgevoerd.

## <a name="application-retry-logic-is-essential"></a>Logica voor opnieuw proberen van toepassing is essentieel
Het is belang rijk dat MySQL-database toepassingen zijn gebouwd voor het detecteren en opnieuw proberen van verbroken verbindingen en mislukte trans acties. Wanneer de toepassing opnieuw probeert, wordt de verbinding van de toepassing op transparante wijze omgeleid naar het nieuwe exemplaar, dat voor het mislukte exemplaar overneemt.

Een gateway wordt intern in azure gebruikt om de verbindingen met het nieuwe exemplaar om te leiden. Na een onderbreking duurt het hele failover-proces meestal tien seconden. Omdat de omleiding intern door de gateway wordt verwerkt, blijft de externe connection string hetzelfde voor de client toepassingen.

## <a name="scaling-up-or-down"></a>Omhoog of omlaag schalen
Net als bij het HA-model, wanneer een Azure Database for MySQL omhoog of omlaag wordt geschaald, een nieuw Server exemplaar met de opgegeven grootte wordt gemaakt. De bestaande gegevens opslag wordt losgekoppeld van het oorspronkelijke exemplaar en gekoppeld aan het nieuwe exemplaar.

Tijdens de schaal bewerking vindt een onderbreking van de database verbindingen plaats. De client toepassingen worden losgekoppeld en de open niet-doorgevoerde trans acties worden geannuleerd. Zodra de client toepassing de verbinding opnieuw probeert te maken of een nieuwe verbinding maakt, stuurt de gateway de verbinding naar het exemplaar met de nieuwe grootte.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [verwerken van tijdelijke connectiviteits fouten](concepts-connectivity.md)
- Meer informatie over het [repliceren van uw gegevens met replica's](howto-read-replicas-portal.md)
