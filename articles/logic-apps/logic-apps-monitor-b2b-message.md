---
title: B2B-berichten bewaken met Azure Monitor
description: Diagnostische logboek registratie instellen voor AS2-, X12-en EDIFACT-berichten in Azure Logic Apps met behulp van Azure Monitor
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: cd96376f764ec3075b916bf2207ec6ee3dd3fcbd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791939"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Diagnostische logboek registratie voor B2B-berichten in Azure Logic Apps instellen met behulp van Azure Monitor

Nadat u B2B-communicatie tussen handels partners in uw integratie account hebt ingesteld, kunnen deze partners berichten uitwisselen met elkaar. Als u wilt controleren of deze communicatie werkt zoals verwacht, kunt u AS2-, X12-en EDIFACT-berichten controleren en diagnostische logboek registratie instellen voor uw integratie account met [Azure monitor-logboeken](../log-analytics/log-analytics-overview.md). Met deze service worden uw Cloud-en on-premises omgevingen gecontroleerd, kunt u hun Beschik baarheid en prestaties behouden en runtime Details en-gebeurtenissen verzamelen voor uitgebreidere fout opsporing. U kunt deze gegevens ook gebruiken met andere services, zoals Azure Storage en Azure Event Hubs.

> [!NOTE]
> Deze pagina kan nog steeds verwijzingen bevatten naar Microsoft Operations Management Suite (OMS), die [in januari 2019 worden buiten gebruik gesteld](../azure-monitor/platform/oms-portal-transition.md), maar deze stappen worden zo mogelijk vervangen door Azure log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

* Een logische app die is ingesteld met diagnostische logboek registratie. Meer informatie [over het maken van een logische app](quickstart-create-first-logic-app-workflow.md) en [het instellen van logboek registratie voor die logische app](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Nadat u aan de vorige vereisten hebt voldaan, hebt u ook een Log Analytics-werk ruimte nodig die u kunt gebruiken voor het controleren en bijhouden van B2B-communicatie via Azure Monitor-Logboeken. Als u geen Log Analytics-werk ruimte hebt, leert u [hoe u een log Analytics-werk ruimte maakt](../azure-monitor/learn/quick-create-workspace.md).

* Een integratie account dat is gekoppeld aan uw logische app. Meer informatie [over het maken van een integratie account met een koppeling naar uw logische app](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Diagnostische logboek registratie inschakelen

U kunt logboek registratie rechtstreeks vanuit uw integratie account of [via de Azure Monitor-service](#azure-monitor-service)inschakelen. Azure Monitor biedt eenvoudige bewaking met gegevens op infrastructuur niveau. Meer informatie over [Azure monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Logboek registratie inschakelen vanuit integratie account

1. Zoek en selecteer uw integratie account in de [Azure Portal](https://portal.azure.com). Onder **bewaking**selecteert u **Diagnostische instellingen**.

   ![Zoek en selecteer uw integratie account, selecteer Diagnostische instellingen](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Zoek en selecteer het integratie account. Selecteer in filter lijsten de waarden die van toepassing zijn op uw integratie account.
Wanneer u klaar bent, kiest u **Diagnostische instelling toevoegen**.

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | Het Azure-abonnement dat is gekoppeld aan uw integratie account | 
   | **Resourcegroep** | <*Azure-resource-group-name*> | De Azure-resource groep voor uw integratie account | 
   | **Resourcetype** | **Integratieaccounts** | Het type voor de Azure-resource waarvoor u logboek registratie wilt inschakelen | 
   | **Resource** | <*Integration-account-name*> | De naam voor uw Azure-resource waarvoor u logboek registratie wilt inschakelen | 
   ||||  

   Bijvoorbeeld:

   ![Diagnostische gegevens instellen voor uw integratie account](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Geef een naam op voor de nieuwe diagnostische instelling en selecteer uw Log Analytics-werk ruimte en de gegevens die u wilt registreren.

   1. Selecteer **verzenden naar log Analytics**. 

   1. Selecteer onder **log Analytics** **configureren**. 

   1. Selecteer onder **OMS-werk ruimten**de log Analytics werk ruimte die u wilt gebruiken voor logboek registratie. 

      > [!NOTE]
      > OMS-werk ruimten worden vervangen door Log Analytics werk ruimten. 

   1. Selecteer onder **logboek**de categorie **IntegrationAccountTrackingEvents** en kies **Opslaan**.

   Bijvoorbeeld: 

   ![Azure Monitor logboeken instellen, zodat u Diagnostische gegevens naar een logboek kunt verzenden](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. [Stel nu bijhouden in voor uw B2B-berichten in azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Logboek registratie via Azure Monitor inschakelen

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het hoofd menu van Azure de optie **monitor**. Onder **instellingen**, selecteert u **instellingen voor diagnostische gegevens**. 

   ![Selecteer controleren > Diagnostische instellingen > uw integratie account](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Zoek en selecteer het integratie account. Selecteer in filter lijsten de waarden die van toepassing zijn op uw integratie account.
Wanneer u klaar bent, kiest u **Diagnostische instelling toevoegen**.

   | Eigenschap | Waarde | Beschrijving | 
   |----------|-------|-------------|
   | **Abonnement** | <*Azure-subscription-name*> | Het Azure-abonnement dat is gekoppeld aan uw integratie account | 
   | **Resourcegroep** | <*Azure-resource-group-name*> | De Azure-resource groep voor uw integratie account | 
   | **Resourcetype** | **Integratieaccounts** | Het type voor de Azure-resource waarvoor u logboek registratie wilt inschakelen | 
   | **Resource** | <*Integration-account-name*> | De naam voor uw Azure-resource waarvoor u logboek registratie wilt inschakelen | 
   ||||  

   Bijvoorbeeld:

   ![Diagnostische gegevens instellen voor uw integratie account](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Geef een naam op voor de nieuwe diagnostische instelling en selecteer uw Log Analytics-werk ruimte en de gegevens die u wilt registreren.

   1. Selecteer **verzenden naar log Analytics**. 

   1. Selecteer onder **log Analytics** **configureren**. 

   1. Selecteer onder **OMS-werk ruimten**de log Analytics werk ruimte die u wilt gebruiken voor logboek registratie. 

      > [!NOTE]
      > OMS-werk ruimten worden vervangen door Log Analytics werk ruimten. 

   1. Selecteer onder **logboek**de categorie **IntegrationAccountTrackingEvents** en kies **Opslaan**.

   Bijvoorbeeld: 

   ![Azure Monitor logboeken instellen, zodat u Diagnostische gegevens naar een logboek kunt verzenden](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. [Stel nu bijhouden in voor uw B2B-berichten in azure monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Diagnostische gegevens met andere services gebruiken

Naast Azure Monitor-Logboeken kunt u uitbreiden hoe u de diagnostische gegevens van uw logische app gebruikt met andere Azure-Services, bijvoorbeeld: 

* [Azure Diagnostics-logboeken archiveren in Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure Diagnostics logboeken streamen naar Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

U kunt vervolgens realtime-bewaking krijgen door telemetrie en analyses uit andere services te gebruiken, zoals [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) en [Power bi](../azure-monitor/platform/powerbi.md). Bijvoorbeeld:

* [Gegevens streamen van Event Hubs naar Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyseer streaminggegevens met Stream Analytics en maak een real-time analyse dashboard in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Op basis van de opties die u wilt instellen, moet u ervoor zorgen dat u eerst [een Azure-opslag account maakt](../storage/common/storage-create-storage-account.md) of [een Azure-Event hub maakt](../event-hubs/event-hubs-create.md). U kunt vervolgens de doelen selecteren waarnaar u de diagnostische gegevens wilt verzenden.
De Bewaar periode is alleen van toepassing wanneer u ervoor kiest om een opslag account te gebruiken.

![Gegevens verzenden naar een Azure Storage-account of Event Hub](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Ondersteunde tracking schema's

Azure ondersteunt deze tracking schema typen, die allemaal vaste schema's hebben, met uitzonde ring van het aangepaste type.

* [Volgschema voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema voor X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste volgschema's](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Volgende stappen

* [B2B-berichten bijhouden in Azure Monitor-logboeken](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "B2B-berichten bijhouden in Azure Monitor-logboeken")
* [Meer informatie over de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Meer informatie over Enterprise Integration Pack")

