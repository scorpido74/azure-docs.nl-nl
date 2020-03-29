---
title: Query's voor logische apps weergeven en maken in Azure Monitor-logboeken
description: Query's weergeven en maken in Azure Monitor-logboeken voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908070"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Query's weergeven en maken voor bewaking en tracking in Azure Monitor-logboeken voor Azure Logic Apps

U de onderliggende query's weergeven die de resultaten van [Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) produceren en query's maken die de resultaten filteren op basis van uw specifieke criteria. U bijvoorbeeld berichten vinden op basis van een specifiek interchange control-nummer. Query's gebruiken de [Kusto-querytaal](https://aka.ms/LogAnalyticsLanguageReference), die u bewerken als u verschillende resultaten wilt weergeven. Zie [Azure Monitor-logboekquery's](../azure-monitor/log-query/query-language.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. Als u geen werkruimte loganalytics hebt, leest u [hoe u een werkruimte Log Analytics maakt.](../azure-monitor/learn/quick-create-workspace.md)

* Een logische app die is ingesteld met Azure Monitor-logboekregistratie en die informatie naar een log analytics-werkruimte verzendt. Meer informatie over [het instellen van Azure Monitor-logboeken voor uw logische app](../logic-apps/monitor-logic-apps.md).

* Als u een integratieaccount gebruikt, controleert u of u het account hebt ingesteld met Azure Monitor-logboekregistratie om die informatie naar een werkruimte van Log Analytics te verzenden. Meer informatie over het [instellen van Azure Monitor-logboekregistratie voor uw integratieaccount](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Query's achter resultaten weergeven

Voer de volgende stappen uit om de query weer te geven of te bewerken die de resultaten in uw werkruimteoverzicht produceert:

1. Selecteer op een resultatenpagina onderaan **Alles weergeven**.

   ![Bekijk alle resultaten](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   De pagina Logboeken wordt geopend en toont de query achter de vorige resultatenpagina.

   ![Logboekenpagina - queryweergave](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Op de pagina **Logboeken** u de volgende opties selecteren:

   * Als u de queryresultaten als tabel wilt weergeven, selecteert u **tabel**onder de queryeditor .

   * Als u de query wilt wijzigen, werkt u de querytekenreeks bij en selecteert **u Uitvoeren** om de resultaten in de tabel weer te geven.

## <a name="create-your-own-query"></a>Uw eigen query maken

Als u resultaten wilt zoeken of filteren op basis van specifieke eigenschappen of waarden, u uw eigen query maken door te beginnen met een lege query of een bestaande query te gebruiken. Zie [Aan de slag met logboekquery's in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)voor meer informatie.

1. Zoek en selecteer in de [Azure-portal](https://portal.azure.com)uw werkruimte Log Analytics.

1. Selecteer **Logboeken**in het menu werkruimte onder **Algemeen**.

1. Begin met een lege query of beschikbare bestaande query's.

   * Als u wilt controleren of bestaande query's beschikbaar zijn, selecteert u op de werkbalk **Query's** > op de werkbalk Voorbeelden**query's Geschiedenis**, waarin query's uit eerdere queryruns worden weergegeven, of selecteert u **Queryverkenner**, waarin vooraf gebouwde query's worden weergegeven.

     De B2B-oplossing van Logic Apps biedt bijvoorbeeld de volgende vooraf gebouwde query's:

     ![Begin met vooraf gebouwde query's met de oplossing 'Logic Apps B2B'-](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Als u wilt beginnen met een lege query, typt u in de queryeditor de [Kusto-querytaal](../azure-monitor/log-query/query-language.md) voor uw query.

     ![Begin met lege query](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Volgende stappen

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema’s voor X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste trackingschema's](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)