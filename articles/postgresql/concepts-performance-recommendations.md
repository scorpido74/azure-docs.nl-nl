---
title: Prestatieaanbevelingen - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt de functie Prestatieaanbeveling beschreven in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768466"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Prestatieaanbevelingen in Azure Database voor PostgreSQL - Single Server

**Geldt voor:** Azure Database voor PostgreSQL - Single Server-versies 9.6, 10, 11

De functie Prestatieaanbevelingen analyseert uw databases om aangepaste suggesties te maken voor betere prestaties. Om de aanbevelingen te produceren, wordt in de analyse gekeken naar verschillende databasekenmerken, waaronder schema. Stel [Query Store](concepts-query-store.md) op uw server in om de functie Prestatieaanbevelingen volledig te gebruiken. Nadat u een prestatieaanbeveling hebt geïmplementeerd, moet u de prestaties testen om de impact van deze wijzigingen te evalueren. 

## <a name="permissions"></a>Machtigingen
De machtigingen van **Eigenaar** of **Inzender** zijn vereist voor het uitvoeren van analyses met de functie Prestatieaanbevelingen.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties
De functie [Prestatieaanbevelingen](concepts-performance-recommendations.md) analyseert workloads op de server om indexen te analyseren met de mogelijkheid om prestaties te verbeteren.

Open **prestatieaanbevelingen** in de sectie **Intelligente prestaties** van de menubalk op de Azure-portalpagina voor uw PostgreSQL-server.

![Landingspagina van Prestatieaanbevelingen](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecteer **Analyseren** en een database kiezen, waarmee de analyse wordt gestart. Afhankelijk van uw werkbelasting kan het enkele minuten duren voordat de analyse is voltooid. Wanneer de analyse is voltooid, verschijnt er een melding in de portal. Analyse voert een diepgaand onderzoek van uw database. Wij raden u aan analyses uit te voeren tijdens daluren. 

In het venster **Aanbevelingen** wordt een lijst met aanbevelingen weergegeven als deze zijn gevonden.

![Nieuwe pagina Prestatieaanbevelingen](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Aanbevelingen worden niet automatisch toegepast. Als u de aanbeveling wilt toepassen, kopieert u de querytekst en voert u deze uit van uw client naar keuze. Vergeet niet om te testen en te controleren om de aanbeveling te evalueren. 

## <a name="recommendation-types"></a>Aanbevelingstypen

Momenteel worden twee soorten aanbevelingen ondersteund: *Index-* en *dropindex maken*.

### <a name="create-index-recommendations"></a>Indexaanbevelingen maken
*Indexaanbevelingen maken,* stellen nieuwe indexen voor om de meest voorkomende of tijdrovende query's in de werkbelasting te versnellen. Voor dit aanbevelingstype moet [queryarchief](concepts-query-store.md) worden ingeschakeld. Query Store verzamelt querygegevens en biedt de gedetailleerde queryruntime- en frequentiestatistieken die de analyse gebruikt om de aanbeveling te doen.

### <a name="drop-index-recommendations"></a>Aanbevelingen voor drop-index
Naast het detecteren van ontbrekende indexen, analyseert Azure Database for PostgreSQL de prestaties van bestaande indexen. Als een index zelden of overbodig wordt gebruikt, raadt de analyzer aan deze te laten vallen.

## <a name="considerations"></a>Overwegingen
* Prestatieaanbevelingen zijn niet beschikbaar voor [gelezen replica's.](concepts-read-replicas.md)
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [bewaking en afstemming](concepts-monitoring.md) in Azure Database for PostgreSQL.

