---
title: Hoge beschikbaarheid – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Concepten met hoge beschikbaarheid en noodherstel
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975530"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hoge beschikbaarheid in Azure Database voor PostgreSQL – Hyperscale (Citus)

Ha (High availability) voorkomt databasedowntime door stand-by replica's van elk knooppunt in een servergroep te behouden. Als een knooppunt naar beneden gaat, schakelt Hyperscale binnenkomende verbindingen over van het mislukte knooppunt naar de stand-by. Failover gebeurt binnen een paar minuten, en gepromote knooppunten hebben altijd nieuwe gegevens via PostgreSQL synchrone streaming replicatie.

Om gebruik te kunnen maken van HA op het coördinatorknooppunt, moeten databasetoepassingen verbroken en opnieuw verwijderde verbindingen en mislukte transacties detecteren en opnieuw proberen. De nieuw gepromoveerde coördinator is toegankelijk met dezelfde verbindingstekenreeks.

Herstel kan worden opgesplitst in drie fasen: detectie, failover, en volledig herstel.  Hyperscale voert periodieke gezondheidscontroles uit op elk knooppunt en na vier mislukte controles wordt vastgesteld dat een knooppunt is uitgeschakeld. Hyperscale bevordert vervolgens een stand-by naar primaire knooppuntstatus (failover) en voorziet in een nieuwe stand-by-to-be.
Streaming replicatie begint, waardoor het nieuwe knooppunt up-to-date.  Wanneer alle gegevens zijn gerepliceerd, is het knooppunt volledig hersteld.

### <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [inschakelen van hoge beschikbaarheid](howto-hyperscale-high-availability.md) in een Hyperscale-servergroep.
