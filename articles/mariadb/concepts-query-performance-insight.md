---
title: Queryprestatieinzicht - Azure-database voor MariaDB
description: In dit artikel wordt de functie QueryPerformance Insight beschreven in Azure Database voor MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527841"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Inzicht in queryprestaties in Azure Database for MariaDB

**Geldt voor:** Azure-database voor MariaDB 10.2

Met Query Performance Insight u snel bepalen wat uw langstlopende query's zijn, hoe deze in de loop van de tijd veranderen en welke wachttijden deze beïnvloeden.

## <a name="common-scenarios"></a>Algemene scenario's

### <a name="long-running-queries"></a>Langdurige query's

- Het identificeren van langstlopende query's in de afgelopen X-uren
- Top N-query's identificeren die op resources wachten
 
### <a name="wait-statistics"></a>Wachtstatistieken

- De aard van de wachttijd op een query begrijpen
- Trends voor resource wachttijden begrijpen en waar resourcetwist bestaat

## <a name="permissions"></a>Machtigingen

De machtigingen **Eigenaar** of **Inzender** zijn vereist om de tekst van de query's weer te geven in Query Performance Insight. Met de machtiging **Lezer** kunt u grafieken en tabellen weergeven maar geen tekst opvragen.

## <a name="prerequisites"></a>Vereisten

Als u inzicht in queryprestaties wilt uitvoeren, moeten gegevens bestaan in het [queryarchief](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Prestatieinzichten bekijken

De weergave [Query Performance Insight](concepts-query-performance-insight.md) in de Azure Portal toont visualisaties van belangrijke informatie uit de Query Store.

Selecteer **queryprestatieinzicht** op de portalpagina van uw Azure Database voor MariaDB-server onder de sectie **Intelligente prestaties** van de menubalk.

### <a name="long-running-queries"></a>Langdurige query's

Op het tabblad **Langlopende query's** worden de top 5 query's weergegeven op basis van de gemiddelde duur per uitvoering, samengevoegd in intervallen van 15 minuten. U meer query's weergeven door te selecteren in de vervolgkeuzelijst **Aantal query's.** Het is mogelijk dat de grafiekkleuren voor een specifieke query-id verschillen wanneer u dit doet.

U kunt in de grafiek klikken en slepen om de tijdspanne te beperken tot een specifiek tijdvenster. U ook de in- en uitzoompictogrammen gebruiken om respectievelijk een kleinere of grotere periode weer te geven.

![Langlopende query's van queryprestaties](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Wachtstatistieken 

> [!NOTE]
> Wachtstatistieken zijn bedoeld voor het oplossen van problemen met queryprestaties. Het wordt aanbevolen om alleen te worden ingeschakeld voor probleemoplossingsdoeleinden. <br>Als u het foutbericht ontvangt in de Azure-portal "*Het probleem dat is opgetreden voor 'Microsoft.DBforMariaDB'; kan niet voldoen aan het verzoek. Als dit probleem blijft bestaan of onverwacht is, neem dan contact op met de ondersteuning met deze informatie.*" tijdens het bekijken van wachtstatistieken, gebruik maken van een kleinere periode.

Wachtstatistieken geven een overzicht van de wachtgebeurtenissen die plaatsvinden tijdens het uitvoeren van een specifieke query. Meer informatie over de typen wachtgebeurtenissen vindt u in de documentatie van de [MySQL-engine](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecteer het tabblad **Wachtstatistieken** om de bijbehorende visualisaties voor wachttijden in de server weer te geven.

Query's die worden weergegeven in de weergave wachtstatistieken worden gegroepeerd op de query's die de grootste wachttijden vertonen tijdens het opgegeven tijdsinterval.

![QueryPerformance Insight wacht op statistieken](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [monitoring en tuning](concepts-monitoring.md) in Azure Database voor MariaDB.