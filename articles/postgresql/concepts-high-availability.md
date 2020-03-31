---
title: Hoge beschikbaarheid - Azure Database voor PostgreSQL - Single Server
description: In dit artikel vindt u informatie over hoge beschikbaarheid in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768568"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Concepten met hoge beschikbaarheid in Azure Database voor PostgreSQL - Single Server
De Azure Database for PostgreSQL-service biedt een gegarandeerd hoog beschikbaarheidsniveau. De financieel gesteunde service level agreement (SLA) bedraagt 99,99% op algemene beschikbaarheid. Er is vrijwel geen toepassingsdowntijd bij het gebruik van deze service.

## <a name="high-availability"></a>Hoge beschikbaarheid
Het HA-model (High Availability) is gebaseerd op ingebouwde failovermechanismen wanneer een onderbreking op nodeniveau optreedt. Er kan een onderbreking op nodeniveau optreden als gevolg van een hardwarefout of als reactie op een service-implementatie.

Wijzigingen in een Azure Database voor PostgreSQL-databaseserver vinden te allen tijde plaats in het kader van een transactie. Wijzigingen worden synchroon geregistreerd in Azure-opslag wanneer de transactie wordt vastgelegd. Als er een onderbreking op knooppuntniveau optreedt, maakt de databaseserver automatisch een nieuw knooppunt en koppelt de gegevensopslag aan het nieuwe knooppunt. Actieve verbindingen worden verbroken en transacties aan boord worden niet vastgelegd.

## <a name="application-retry-logic-is-essential"></a>Logica voor het opnieuw proberen van toepassingen is essentieel
Het is belangrijk dat PostgreSQL-databasetoepassingen zijn gebouwd om verbroken verbindingen en mislukte transacties te detecteren en opnieuw te proberen. Wanneer de toepassing opnieuw wordt geprobeerd, wordt de verbinding van de toepassing transparant doorgestuurd naar de nieuw gemaakte instantie, die het overneemt voor de mislukte instantie.

Intern in Azure wordt een gateway gebruikt om de verbindingen om te leiden naar het nieuwe exemplaar. Bij een onderbreking duurt het hele fail-over proces meestal tientallen seconden. Aangezien de omleiding intern door de gateway wordt afgehandeld, blijft de externe verbindingstekenreeks hetzelfde voor de clienttoepassingen.

## <a name="scaling-up-or-down"></a>Op- of afschalen
Net als bij het HA-model wordt een nieuwe serverinstantie met de opgegeven grootte gemaakt wanneer een Azure-database voor PostgreSQL wordt opgeschaald of omlaag wordt geschaald. De bestaande gegevensopslag wordt losgekoppeld van de oorspronkelijke instantie en gekoppeld aan de nieuwe instantie.

Tijdens de schaalbewerking treedt een onderbreking van de databaseverbindingen op. De clienttoepassingen worden losgekoppeld en niet-vastgelegde transacties worden geannuleerd. Zodra de clienttoepassing de verbinding opnieuw probeert of een nieuwe verbinding maakt, leidt de gateway de verbinding naar de nieuw formaat instantie. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het verwerken van tijdelijke verbindingsfouten](concepts-connectivity.md)
- Meer informatie over het repliceren van [uw gegevens met gelezen replica's](howto-read-replicas-portal.md)
