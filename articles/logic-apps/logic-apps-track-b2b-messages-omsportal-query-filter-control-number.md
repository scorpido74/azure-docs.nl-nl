---
title: Tracerings query's maken voor B2B-berichten
description: Query's maken voor het bijhouden van AS2-, X12-en EDIFACT-berichten in azure Log Analytics voor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 36cf45aa0f7d46b62caa586d1939ec52e67b1a3e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792854"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Tracerings query's maken voor B2B-berichten in Azure Monitor logboeken voor Azure Logic Apps

Als u AS2-, X12-of EDIFACT-berichten wilt vinden die u bijhoudt met [Azure monitor-logboeken](../log-analytics/log-analytics-overview.md), kunt u query's maken die worden gefilterd op basis van specifieke criteria. U kunt bijvoorbeeld berichten vinden op basis van een specifiek uitwisselings controle nummer.

> [!NOTE]
> Op deze pagina eerder beschreven stappen voor het uitvoeren van deze taken met de Microsoft Operations Management Suite (OMS), die [in januari 2019 worden buiten gebruik gesteld](../azure-monitor/platform/oms-portal-transition.md), worden deze stappen vervangen door Azure log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

* Een logische app die is ingesteld met diagnostische logboek registratie. Meer informatie [over het maken van een logische app](quickstart-create-first-logic-app-workflow.md) en [het instellen van logboek registratie voor die logische app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Een integratie account dat is ingesteld met bewaking en logboek registratie. Meer informatie [over het maken van een integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) en [het instellen van controle en logboek registratie voor dat account](../logic-apps/logic-apps-monitor-b2b-message.md).

* Als u dat nog niet hebt [gedaan, kunt u Diagnostische gegevens publiceren naar Azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) en het [bijhouden van berichten instellen in azure monitor logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Query's maken met filters

Als u berichten wilt zoeken op basis van specifieke eigenschappen of waarden, kunt u query's maken die gebruikmaken van filters. 

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Alle services**. Zoek in het zoekvak ' Log Analytics ' en selecteer **log Analytics**.

   ![Log Analytics selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Zoek en selecteer de Log Analytics-werk ruimte onder **log Analytics**. 

   ![Log Analytics werk ruimte selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Selecteer in het menu werk ruimte onder **Algemeen**de optie **Logboeken (klassiek)** of **Logboeken**. 

   In dit voor beeld ziet u hoe u de klassieke Logboeken weergeeft. 
   Als u **Logboeken weer geven** kiest in het gedeelte **uw log Analytics-ervaring maximaliseren** onder **Logboeken zoeken en analyseren**, krijgt u de **Logboeken (klassieke weer gave)** . 

   ![Klassieke logboeken weer geven](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Typ in het invoervak voor de query de naam van het veld dat u wilt zoeken. Wanneer u begint te typen, worden in de query-editor de mogelijke overeenkomsten en bewerkingen weer gegeven die u kunt gebruiken. Nadat u de query hebt gemaakt, kiest u **uitvoeren** of drukt u op ENTER.

   In dit voor beeld wordt gezocht naar overeenkomsten op **LogicAppB2B**. 
   Meer informatie over [het zoeken naar gegevens in azure monitor-logboeken](../log-analytics/log-analytics-log-searches.md).

   ![Begin met typen van query reeks](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Als u de periode wilt wijzigen die u wilt weer geven, selecteert u in het linkerdeel venster in de lijst duur of sleept u de schuif regelaar. 

   ![Tijds bestek wijzigen](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Kies **toevoegen**om een filter toe te voegen aan uw query. 

   ![Filter aan query toevoegen](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. Voer onder **filters toevoegen**de filter naam in die u wilt zoeken. Als u het filter hebt gevonden, selecteert u dat filter. Kies opnieuw **toevoegen** in het linkerdeel venster.

   Dit is bijvoorbeeld een andere query die zoekt op **type = = "AzureDiagnostics"** -gebeurtenissen en resultaten zoekt op basis van het uitwisselings controle nummer door het **event_record_messageProperties_interchangeControlNumber_s** filter te selecteren.

   ![Filter waarde selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Nadat u **toevoegen**hebt gekozen, wordt uw query bijgewerkt met de geselecteerde filter gebeurtenis en-waarde. 
   Uw vorige resultaten worden nu ook gefilterd. 

   Met deze query wordt bijvoorbeeld gezocht naar **type = = "AzureDiagnostics"** en worden resultaten gevonden op basis van een uitwisselings controle nummer met behulp van het **event_record_messageProperties_interchangeControlNumber_s** filter.

   ![Gefilterde resultaten](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Query opslaan

Voer de volgende stappen uit om de query op te slaan in de weer gave **Logboeken (klassiek)** :

1. Kies in uw query op de pagina **Logboeken (klassiek)** **Analytics**. 

   ![Kies analyse](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Kies **Opslaan**op de werk balk van de query.

   ![Kies 'Opslaan'](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Geef de details van de query op, bijvoorbeeld Geef uw query een naam, selecteer **query**en geef een categorie naam op. Als u bent klaar, kiest u **Opslaan**.

   ![Kies 'Opslaan'](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Als u opgeslagen query's wilt weer geven, gaat u terug naar de query-pagina. Kies **opgeslagen Zoek opdrachten**op de werk balk van de query.

   ![Kies ' opgeslagen Zoek opdrachten '](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Selecteer uw query onder **opgeslagen Zoek opdrachten**zodat u de resultaten kunt weer geven. 

   ![Uw query selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Bewerk de query om de query bij te werken, zodat u verschillende resultaten kunt vinden.

## <a name="find-and-run-saved-queries"></a>Opgeslagen query's zoeken en uitvoeren

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Alle services**. Zoek in het zoekvak ' Log Analytics ' en selecteer **log Analytics**.

   ![Log Analytics selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. Zoek en selecteer de Log Analytics-werk ruimte onder **log Analytics**. 

   ![Log Analytics werk ruimte selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Selecteer in het menu werk ruimte onder **Algemeen**de optie **Logboeken (klassiek)** of **Logboeken**. 

   In dit voor beeld ziet u hoe u de klassieke Logboeken weergeeft. 

1. Nadat de query pagina wordt geopend, kiest u in de query-werk balk **opgeslagen Zoek opdrachten**.

   ![Kies ' opgeslagen Zoek opdrachten '](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. Selecteer uw query onder **opgeslagen Zoek opdrachten**zodat u de resultaten kunt weer geven. 

   ![Uw query selecteren](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   De query wordt automatisch uitgevoerd, maar als de query om een bepaalde reden niet wordt uitgevoerd, kiest u **uitvoeren**in de query-editor.

## <a name="next-steps"></a>Volgende stappen

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema’s voor X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste tracking schema's](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)