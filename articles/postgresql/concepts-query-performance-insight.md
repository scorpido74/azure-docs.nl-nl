---
title: QueryPerformance Insight - Azure Database voor PostgreSQL - Single Server
description: In dit artikel wordt de functie QueryPerformance Insight beschreven in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768381"
---
# <a name="query-performance-insight"></a>Inzicht in queryprestaties 

**Geldt voor:** Azure Database voor PostgreSQL - Single Server-versies 9.6, 10, 11

Met Query Performance Insight u snel bepalen wat uw langstlopende query's zijn, hoe deze in de loop van de tijd veranderen en welke wachttijden deze beïnvloeden.

## <a name="permissions"></a>Machtigingen
De machtigingen **Eigenaar** of **Inzender** zijn vereist om de tekst van de query's weer te geven in Query Performance Insight. Met de machtiging **Lezer** kunt u grafieken en tabellen weergeven maar geen tekst opvragen.

## <a name="prerequisites"></a>Vereisten
Als u inzicht in queryprestaties wilt uitvoeren, moeten gegevens bestaan in het [queryarchief](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Prestatieinzichten bekijken
De weergave [Query Performance Insight](concepts-query-performance-insight.md) in de Azure Portal toont visualisaties van belangrijke informatie uit de Query Store. 

Selecteer **queryprestatieinzicht** op de portalpagina van uw Azure Database voor PostgreSQL-server onder de sectie **Intelligente prestaties** van de menubalk.

![Langlopende query's van queryprestaties](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

Op het tabblad **Langlopende query's** worden de vijf belangrijkste query's weergegeven op basis van de gemiddelde duur per uitvoering, geaggregeerd in intervallen van 15 minuten. U kunt meer query's weergeven door een optie te selecteren in de vervolgkeuzelijst **Aantal query's**. Het is mogelijk dat de grafiekkleuren voor een specifieke query-id verschillen wanneer u dit doet.

U kunt in de grafiek klikken en slepen om de tijdspanne te beperken tot een specifiek tijdvenster. U ook de in- en uitzoompictogrammen gebruiken om respectievelijk een kleinere of grotere periode te bekijken.

De tabel onder de grafiek geeft meer details over de langlopende query's in dat tijdvenster.

Selecteer het tabblad **Wachtstatistieken** om de bijbehorende visualisaties voor wachttijden in de server weer te geven.

![QueryPerformance Insight wacht op statistieken](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Overwegingen
* Queryprestatieinzicht is niet beschikbaar voor [gelezen replica's.](concepts-read-replicas.md)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [bewaking en afstemming](concepts-monitoring.md) in Azure Database for PostgreSQL.


