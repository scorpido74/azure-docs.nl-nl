---
title: Aanbevelingen voor prestaties-Azure Database for MySQL
description: In dit artikel wordt de functie aanbevolen prestatie aanbeveling in Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: cba07b84a1ddc2b9362c818ae2d3747b98379f2e
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402648"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Aanbevelingen in Azure Database for MySQL voor prestaties

**Van toepassing op:** Azure Database for MySQL 5,7, 8,0

Met de functie voor prestatie verbeteringen worden uw data bases geanalyseerd om aangepaste suggesties te maken voor betere prestaties. Voor het produceren van de aanbevelingen kijken de analyse naar verschillende database kenmerken, waaronder schema. Schakel het [query archief](concepts-query-store.md) op de server in om de functie aanbevelingen voor prestaties volledig te benutten. Als het prestatie schema is uitgeschakeld, schakelt het inschakelen van query Store performance_schema en een subset van performance schema-instrumenten in die voor de functie zijn vereist. Nadat u een aanbevolen prestatie aanbeveling hebt geïmplementeerd, moet u de prestaties testen om de impact van deze wijzigingen te evalueren.

## <a name="permissions"></a>Machtigingen

De machtigingen van **Eigenaar** of **Inzender** zijn vereist voor het uitvoeren van analyses met de functie Prestatieaanbevelingen.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties

De functie [Prestatieaanbevelingen](concepts-performance-recommendations.md) analyseert workloads op de server om indexen te analyseren met de mogelijkheid om prestaties te verbeteren.

Open **prestatie aanbevelingen** van het gedeelte **intelligente prestaties** van de menu balk op de pagina Azure portal voor uw MySQL-server.

![Landingspagina van Prestatieaanbevelingen](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Selecteer **analyseren** en kies een Data Base, waarmee de analyse wordt gestart. Afhankelijk van uw werk belasting kan het enkele minuten duren voordat de analyse is voltooid. Wanneer de analyse is voltooid, verschijnt er een melding in de portal. Analyse voert een grondige controle uit van uw data base. We raden u aan om analyses uit te voeren tijdens rustige Peri Oden.

In het venster **aanbevelingen** wordt een lijst met aanbevelingen weer gegeven als deze zijn gevonden en de gerelateerde query-id die deze aanbeveling heeft gegenereerd. Met de query-ID kunt u de [mysql. query_store](concepts-query-store.md#mysqlquery_store) weer gave gebruiken om meer te weten te komen over de query.

![Nieuwe pagina prestatie aanbevelingen](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Aanbevelingen worden niet automatisch toegepast. Als u de aanbeveling wilt Toep assen, kopieert u de query tekst en voert u deze uit vanaf de client van uw keuze. Vergeet niet om te testen en te controleren om de aanbeveling te evalueren.

## <a name="recommendation-types"></a>Aanbevelings typen

Momenteel worden alleen aanbevelingen voor het *maken van indexen* ondersteund.

### <a name="create-index-recommendations"></a>Aanbevelingen voor index maken

Bij het *maken van index* aanbevelingen worden nieuwe indexen voorgesteld om de meest frequente uitvoeringen of tijdrovende query's in de werk belasting te versnellen. Voor dit aanbevelings type moet het [query archief](concepts-query-store.md) worden ingeschakeld. In query Store worden query gegevens verzameld en worden de gedetailleerde query runtime en de frequentie statistieken beschreven die de analyse gebruikt om de aanbeveling te doen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [bewaken en afstemmen](concepts-monitoring.md) van Azure database for MySQL.