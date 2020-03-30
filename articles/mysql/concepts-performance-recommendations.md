---
title: Prestatieaanbevelingen - Azure Database voor MySQL
description: In dit artikel wordt de functie Prestatieaanbeveling in Azure Database voor MySQL beschreven
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c7779d82ddd6e5fd1bf7fcd983937ea6c10dab1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537071"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Aanbevelingen in Azure Database for MySQL voor prestaties

**Geldt voor:** Azure-database voor MySQL 5.7

De functie Prestatieaanbevelingen analyseert uw databases om aangepaste suggesties te maken voor betere prestaties. Om de aanbevelingen te produceren, wordt in de analyse gekeken naar verschillende databasekenmerken, waaronder schema. Stel [Query Store](concepts-query-store.md) op uw server in om de functie Prestatieaanbevelingen volledig te gebruiken. Als het prestatieschema is uitgeschakeld, schakelt u Query Store in performance_schema en een subset van prestatieschema-instrumenten die nodig zijn voor de functie. Nadat u een prestatieaanbeveling hebt geïmplementeerd, moet u de prestaties testen om de impact van deze wijzigingen te evalueren.

## <a name="permissions"></a>Machtigingen

De machtigingen van **Eigenaar** of **Inzender** zijn vereist voor het uitvoeren van analyses met de functie Prestatieaanbevelingen.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties

De functie [Prestatieaanbevelingen](concepts-performance-recommendations.md) analyseert workloads op de server om indexen te analyseren met de mogelijkheid om prestaties te verbeteren.

**Prestatieaanbevelingen openen** in de sectie **Intelligente prestaties** van de menubalk op de Azure-portalpagina voor uw MySQL-server.

![Landingspagina van Prestatieaanbevelingen](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecteer **Analyseren** en een database kiezen, waarmee de analyse wordt gestart. Afhankelijk van uw werkbelasting kan het enkele minuten duren voordat de analyse is voltooid. Wanneer de analyse is voltooid, verschijnt er een melding in de portal. Analyse voert een diepgaand onderzoek van uw database. Wij raden u aan analyses uit te voeren tijdens daluren.

In het venster **Aanbevelingen** wordt een lijst met aanbevelingen weergegeven als deze zijn gevonden en de bijbehorende query-id die deze aanbeveling heeft gegenereerd. Met de query-id u de weergave [mysql.query_store](concepts-query-store.md#mysqlquery_store) gebruiken om meer te weten te komen over de query.

![Nieuwe pagina Prestatieaanbevelingen](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Aanbevelingen worden niet automatisch toegepast. Als u de aanbeveling wilt toepassen, kopieert u de querytekst en voert u deze uit van uw client naar keuze. Vergeet niet om te testen en te controleren om de aanbeveling te evalueren.

## <a name="recommendation-types"></a>Aanbevelingstypen

Momenteel worden alleen *Aanbevelingen voor index maken* ondersteund.

### <a name="create-index-recommendations"></a>Indexaanbevelingen maken

*Indexaanbevelingen maken,* stellen nieuwe indexen voor om de meest voorkomende of tijdrovende query's in de werkbelasting te versnellen. Voor dit aanbevelingstype moet [queryarchief](concepts-query-store.md) worden ingeschakeld. Query Store verzamelt querygegevens en biedt de gedetailleerde queryruntime- en frequentiestatistieken die de analyse gebruikt om de aanbeveling te doen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [monitoring en tuning](concepts-monitoring.md) in Azure Database voor MySQL.