---
title: Logische apps bewaken met Azure Monitor
description: Inzichten en fouten opsporen voor het oplossen van problemen met logische apps en de uitvoering van problemen met Azure Monitor-logboeken
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791895"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Inzichten en gegevens voor Logic apps opsporen met behulp van Azure Monitor-logboeken

Als u uitgebreide fout opsporingsgegevens over uw Logic Apps wilt controleren en verkrijgen, schakelt u [Azure monitor logboeken](../log-analytics/log-analytics-overview.md) in wanneer u uw logische app maakt. Azure Monitor logboeken bieden diagnostische logboek registratie en bewaking voor uw Logic apps wanneer u de Logic Apps beheer oplossing installeert in de Azure Portal. Deze oplossing biedt ook geaggregeerde informatie voor uw logische app-uitvoeringen met specifieke details zoals status, uitvoerings tijd, status van opnieuw verzenden en correlatie-Id's. In dit artikel wordt beschreven hoe u Azure Monitor logboeken inschakelt, zodat u runtime-gebeurtenissen en-gegevens kunt weer geven voor de uitvoeringen van logische apps.

In dit onderwerp wordt beschreven hoe u Azure Monitor Logboeken kunt instellen wanneer u uw logische app maakt. Als u Azure Monitor logboeken wilt inschakelen voor bestaande Logic apps, volgt u deze stappen om [Diagnostische logboek registratie in te scha kelen en runtime gegevens van logische apps naar Azure monitor-logboeken te verzenden](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Op deze pagina worden eerder stappen beschreven voor het uitvoeren van deze taken met de Microsoft Operations Management Suite (OMS), die zijn [ingetrokken in januari 2019](../azure-monitor/platform/oms-portal-transition.md), en worden deze stappen vervangen door [Azure monitor logboeken](../azure-monitor/platform/data-platform-logs.md), die de term log Analytics vervangen. Logboek gegevens worden nog steeds opgeslagen in een Log Analytics-werk ruimte en worden nog steeds verzameld en geanalyseerd door dezelfde Log Analytics-service. Zie [Azure monitor terminologie wijzigen](../azure-monitor/terminology.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u een Log Analytics-werk ruimte nodig. Meer informatie [over het maken van een log Analytics-werk ruimte](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Logboek registratie inschakelen voor nieuwe logische apps

1. Maak in [Azure Portal](https://portal.azure.com)uw logische app. Selecteer in het hoofd menu van Azure **een resource maken** > **integratie** > **logische app**.

   ![Een nieuwe logische app maken](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. Voer onder **Logic app**de volgende stappen uit:

   1. Geef een naam op voor uw logische app en selecteer uw Azure-abonnement.

   1. Een Azure-resource groep maken of selecteren. Selecteer de locatie voor uw logische app.

   1. Selecteer **aan**onder **log Analytics**.

   1. Selecteer in de lijst **log Analytics-werk ruimte** de werk ruimte waar u de gegevens van de logische app-uitvoeringen wilt verzenden.

      ![Informatie over logische app opgeven](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Nadat u deze stap hebt voltooid, maakt Azure uw logische app, die nu aan uw Log Analytics-werk ruimte is gekoppeld. Met deze stap wordt ook de oplossing voor Logic Apps beheer automatisch geïnstalleerd in uw werk ruimte.

   1. Als u gereed bent, selecteert u **Maken**.

1. Als u de uitvoeringen van de logische app wilt weer geven, [gaat u verder met deze stappen](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Logic Apps-beheer oplossing installeren

Als u Azure Monitor logboeken al hebt ingesteld tijdens het maken van uw logische app, slaat u deze stap over. U hebt de Logic Apps-beheer oplossing al geïnstalleerd.

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Alle services**. Zoek in het zoekvak ' Log Analytics-werk ruimten ' en selecteer Log Analytics- **werk ruimten**.

   ![Selecteer ' Log Analytics werk ruimten '](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. Selecteer onder **log Analytics werk ruimten**uw werk ruimte.

   ![Uw Log Analytics-werk ruimte selecteren](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Selecteer in het deel venster Overzicht onder **aan de slag met Log Analytics** > **bewakings oplossingen configureren**de optie **oplossingen weer geven**.

   ![Selecteer oplossingen weer geven](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Selecteer onder **overzicht**de optie **toevoegen**.

   ![Selecteer toevoegen](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. Nadat de **Marketplace** is geopend, voert u in het zoekvak ' Logic apps-beheer ' in en selecteert u **Logic apps beheer**.

   ![Selecteer Logic Apps beheer](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. Selecteer **maken**in het deel venster beschrijving van oplossing.

   ![Selecteer ' maken ' voor ' Logic Apps-beheer '](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Controleer en bevestig de Log Analytics-werk ruimte waar u de oplossing wilt installeren en selecteer opnieuw **maken** .

   ![Selecteer ' maken ' voor ' Logic Apps-beheer '](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Nadat Azure de oplossing heeft geïmplementeerd voor de Azure-resource groep met uw Log Analytics-werk ruimte, wordt de oplossing weer gegeven in het deel venster samen vatting van de werk ruimte.

   ![Deel venster werkruimte overzicht](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Uitvoerings gegevens van logische app weer geven

Wanneer de logische app wordt uitgevoerd, kunt u de status en het aantal weer geven voor die uitvoeringen op de tegel **Logic apps beheer** .

1. Ga naar uw Log Analytics-werk ruimte en selecteer **werkruimte samenvatting** > **Logic apps beheer**.

   ![Uitvoerings status en aantal van logische app](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Hier worden de logische app-uitvoeringen gegroepeerd op naam of op uitvoerings status. Deze pagina bevat ook details over fouten in acties of triggers voor uitvoeringen van de logische app.

   ![Status samenvatting voor de uitvoeringen van logische apps](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Als u alle uitvoeringen voor een specifieke logische app of status wilt weer geven, selecteert u de rij voor een logische app of een status.

   Hier volgt een voor beeld waarin alle uitvoeringen voor een specifieke logische app worden weer gegeven:

   ![Uitvoeringen en status van logische apps weer geven](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Deze pagina heeft geavanceerde opties: 

   * Kolom met **bijgehouden eigenschappen** : voor een logische app waarvoor u bijgehouden eigenschappen, die zijn gegroepeerd op acties, kunt u de eigenschappen uit deze kolom weer geven. Als u deze bijgehouden eigenschappen wilt weer geven, selecteert u **weer gave**. Gebruik het kolom filter om de bijgehouden eigenschappen te doorzoeken.

      ![Bijgehouden eigenschappen voor een logische app weer geven](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Nieuw toegevoegde bijgehouden eigenschappen kunnen 10-15 minuten duren voordat ze de eerste keer worden weer gegeven. Meer informatie [over het toevoegen van bijgehouden eigenschappen aan uw logische app](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Opnieuw verzenden**: u kunt een of meer Logic apps-uitvoeringen opnieuw indienen die zijn mislukt, geslaagd of nog actief zijn. Schakel de selectie vakjes in voor de uitvoeringen die u opnieuw wilt indienen en selecteer vervolgens **opnieuw verzenden**.

     ![Uitvoeringen van logische app opnieuw verzenden](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Als u uw resultaten wilt filteren, kunt u zowel client-side als server-side filters uitvoeren.

   * **Filter aan client zijde**: Selecteer voor elke kolom de gewenste filters, bijvoorbeeld:

     ![Voor beeld van kolom filters](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filter aan de server zijde**: als u een bepaald tijd venster wilt selecteren of als u het aantal uitvoeringen wilt beperken dat wordt weer gegeven, gebruikt u het bereik besturings element boven aan de pagina. Standaard worden slechts 1.000 records per keer weer gegeven.

     ![Het tijd venster wijzigen](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Als u alle acties en de bijbehorende Details voor een specifieke uitvoering wilt weer geven, selecteert u de rij voor de uitvoering van een logische app.

   Hier volgt een voor beeld waarin alle acties en triggers worden weer gegeven voor het uitvoeren van een specifieke logische app:

   ![Acties voor het uitvoeren van een logische app weer geven](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Selecteer op de pagina resultaten de optie **Alles bekijken**om de query achter de resultaten weer te geven of om alle resultaten weer te geven. Hiermee opent u de pagina **Logboeken** .

   ![Alle resultaten weer geven](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   Op de pagina **Logboeken** kunt u de volgende opties kiezen:

   * Als u de query resultaten in een tabel wilt weer geven, selecteert u **tabel**.

   * Query's gebruiken [Kusto-query taal](https://aka.ms/LogAnalyticsLanguageReference), die u kunt bewerken als u andere resultaten wilt weer geven. Als u de query wilt wijzigen, werkt u de query reeks bij en selecteert u **uitvoeren** om de resultaten in de tabel weer te geven. 

     ![Log Analytics-query weergave](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Volgende stappen

* [B2B-berichten bewaken](../logic-apps/logic-apps-monitor-b2b-message.md)