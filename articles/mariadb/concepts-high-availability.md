---
title: Hoge beschikbaarheid - Azure Database voor MariaDB
description: In dit onderwerp vindt u informatie over hoge beschikbaarheid bij het gebruik van Azure Database voor MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a87646f6195a06cf0a5382cb248efa5516c953f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531988"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Concepten met hoge beschikbaarheid in Azure Database voor MariaDB
De Azure Database for MariaDB-service biedt een gegarandeerd hoog beschikbaarheidsniveau. De financieel gesteunde service level agreement (SLA) bedraagt 99,99% op algemene beschikbaarheid. Er is vrijwel geen toepassingsdowntijd bij het gebruik van deze service.

## <a name="high-availability"></a>Hoge beschikbaarheid
Het HA-model (High Availability) is gebaseerd op ingebouwde fail-over-mechanismen wanneer een onderbreking op nodeniveau optreedt. Er kan een onderbreking op nodeniveau optreden als gevolg van een hardwarefout of als reactie op een service-implementatie.

Wijzigingen in een Azure Database voor MariaDB-databaseserver vinden te allen tijde plaats in het kader van een transactie. Wijzigingen worden synchroon geregistreerd in Azure-opslag wanneer de transactie wordt vastgelegd. Als er een onderbreking op knooppuntniveau optreedt, maakt de databaseserver automatisch een nieuw knooppunt en koppelt de gegevensopslag aan het nieuwe knooppunt. Actieve verbindingen worden verbroken en transacties aan boord worden niet vastgelegd.

## <a name="application-retry-logic-is-essential"></a>Logica voor het opnieuw proberen van toepassingen is essentieel
Het is belangrijk dat MariaDB-databasetoepassingen zijn gebouwd om verbroken verbindingen en mislukte transacties te detecteren en opnieuw te proberen. Wanneer de toepassing opnieuw wordt geprobeerd, wordt de verbinding van de toepassing transparant doorgestuurd naar de nieuw gemaakte instantie, die het overneemt voor de mislukte instantie.

Intern in Azure wordt een gateway gebruikt om de verbindingen om te leiden naar het nieuwe exemplaar. Bij een onderbreking duurt het hele fail-over proces meestal tientallen seconden. Aangezien de omleiding intern door de gateway wordt afgehandeld, blijft de externe verbindingstekenreeks hetzelfde voor de clienttoepassingen.

## <a name="scaling-up-or-down"></a>Op- of afschalen
Net als bij het HA-model wordt een nieuwe serverinstantie met de opgegeven grootte gemaakt wanneer een Azure-database voor MariaDB wordt opgeschaald of verlaagd. De bestaande gegevensopslag wordt losgekoppeld van de oorspronkelijke instantie en gekoppeld aan de nieuwe instantie.

Tijdens de schaalbewerking treedt een onderbreking van de databaseverbindingen op. De clienttoepassingen worden losgekoppeld en niet-vastgelegde transacties worden geannuleerd. Zodra de clienttoepassing de verbinding opnieuw probeert of een nieuwe verbinding maakt, leidt de gateway de verbinding naar de nieuw formaat instantie.

## <a name="next-steps"></a>Volgende stappen
- Zie Azure Database for [MariaDB-overzicht voor](overview.md) een overzicht van de service
