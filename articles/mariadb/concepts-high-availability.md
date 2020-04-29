---
title: Hoge Beschik baarheid-Azure Database for MariaDB
description: In dit onderwerp vindt u informatie over hoge Beschik baarheid bij het gebruik van Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a87646f6195a06cf0a5382cb248efa5516c953f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79531988"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Concepten met hoge Beschik baarheid in Azure Database for MariaDB
De Azure Database for MariaDB-service biedt een gegarandeerd hoog niveau van Beschik baarheid. De financieel ondersteunde service level agreement (SLA) is 99,99% bij de algemene Beschik baarheid. Er is bijna geen toepassings tijd meer wanneer u deze service gebruikt.

## <a name="high-availability"></a>Hoge beschikbaarheid
Het model voor hoge Beschik baarheid (HA) is gebaseerd op ingebouwde mechanismen voor failover wanneer een onderbreking op knooppunt niveau optreedt. Een onderbreking op knooppunt niveau kan zich voordoen als gevolg van een hardwarestoring of als reactie op een service-implementatie.

Wijzigingen die zijn aangebracht in een Azure Database for MariaDB database server, worden altijd uitgevoerd in de context van een trans actie. Wijzigingen worden synchroon vastgelegd in azure Storage wanneer de trans actie wordt doorgevoerd. Als er een onderbreking op knooppunt niveau optreedt, maakt de database server automatisch een nieuw knoop punt en koppelt deze gegevens opslag aan het nieuwe knoop punt. Actieve verbindingen worden verwijderd en eventuele trans acties met invlucht worden niet doorgevoerd.

## <a name="application-retry-logic-is-essential"></a>Logica voor opnieuw proberen van toepassing is essentieel
Het is belang rijk dat MariaDB database toepassingen zijn gebouwd voor het detecteren en opnieuw proberen van verbroken verbindingen en mislukte trans acties. Wanneer de toepassing opnieuw probeert, wordt de verbinding van de toepassing op transparante wijze omgeleid naar het nieuwe exemplaar, dat voor het mislukte exemplaar overneemt.

Een gateway wordt intern in azure gebruikt om de verbindingen met het nieuwe exemplaar om te leiden. Na een onderbreking duurt het hele failover-proces meestal tien seconden. Omdat de omleiding intern door de gateway wordt verwerkt, blijft de externe connection string hetzelfde voor de client toepassingen.

## <a name="scaling-up-or-down"></a>Omhoog of omlaag schalen
Net als bij het HA-model, wanneer een Azure Database for MariaDB omhoog of omlaag wordt geschaald, een nieuw Server exemplaar met de opgegeven grootte wordt gemaakt. De bestaande gegevens opslag wordt losgekoppeld van het oorspronkelijke exemplaar en gekoppeld aan het nieuwe exemplaar.

Tijdens de schaal bewerking vindt een onderbreking van de database verbindingen plaats. De client toepassingen worden losgekoppeld en de open niet-doorgevoerde trans acties worden geannuleerd. Zodra de client toepassing de verbinding opnieuw probeert te maken of een nieuwe verbinding maakt, stuurt de gateway de verbinding naar het exemplaar met de nieuwe grootte.

## <a name="next-steps"></a>Volgende stappen
- Zie [overzicht van Azure database for MariaDB](overview.md) voor een overzicht van de service
