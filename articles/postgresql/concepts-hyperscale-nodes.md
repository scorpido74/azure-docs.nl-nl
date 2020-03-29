---
title: Knooppunten – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Meer informatie over de twee typen knooppunten, coördinatoren en werknemers, vindt u in een servergroep in Azure Database voor PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973999"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Knooppunten in Azure-database voor PostgreSQL – Hyperscale (Citus)

Met het hyperscale-hostingtype (Citus) kan Azure Database voor PostgreSQL-servers (knooppunten genoemd) met elkaar coördineren in een 'shared nothing'-architectuur. De knooppunten in een servergroep bevatten gezamenlijk meer gegevens en gebruiken meer CPU-cores dan mogelijk zou zijn op één server. De architectuur maakt het ook mogelijk de database te schalen door het toevoegen van meer knooppunten aan de servergroep.

## <a name="coordinator-and-workers"></a>Coördinator en werknemers

Elke servergroep heeft een coördinatorknooppunt en meerdere werknemers. Toepassingen sturen hun vragen naar het coördinatorknooppunt, dat het doorgeeft aan de betrokken werknemers en hun resultaten verzamelt. Toepassingen kunnen niet rechtstreeks verbinding maken met werknemers.

Voor elke query leidt de coördinator deze door naar één werkknooppunt of parallel met meerdere gegevens, afhankelijk van of de vereiste gegevens op één knooppunt of meerdere gegevens worden uitgevoerd. De coördinator bepaalt wat te doen door metadatatabellen te raadplegen. Deze tabellen volgen de DNS-namen en status van werknemersknooppunten en de verdeling van gegevens over knooppunten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over hoe knooppunten [gedistribueerde gegevens](concepts-hyperscale-distributed-data.md) opslaan
