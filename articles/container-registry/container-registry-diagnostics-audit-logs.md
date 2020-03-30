---
title: Bronlogboeken verzamelen & analyseren
description: Recordeer en analyseer gebeurtenisgebeurtenissen voor het Azure Container Registry, zoals verificatie, afbeeldingspush en afbeeldingsuittrekken.
ms.topic: article
ms.date: 01/03/2020
ms.openlocfilehash: 00f9468721126bd166051df47cec1596356e9b54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409640"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Azure Container Registry-logboeken voor diagnostische evaluatie en controle

In dit artikel wordt uitgelegd hoe u logboekgegevens verzamelt voor een Azure-containerregister met behulp van functies van [Azure Monitor.](../azure-monitor/overview.md) Azure Monitor verzamelt [bronlogboeken](../azure-monitor/platform/platform-logs-overview.md) (voorheen *diagnostische logboeken*genoemd) voor door gebruikers gestuurde gebeurtenissen in uw register. Verzamel en gebruik deze gegevens om te voldoen aan behoeften zoals:

* Verificatiegebeurtenissen voor het auditregister om de beveiliging en naleving te waarborgen 

* Geef een volledig activiteitenspoor op registerartefacten zoals pull- en pull-gebeurtenissen, zodat u operationele problemen met uw register diagnosticeren 

Het verzamelen van bronlogboekgegevens met Azure Monitor kan extra kosten met zich meebrengen. Zie [Azure Monitor-prijzen](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Repository-gebeurtenissen

De volgende gebeurtenissen op archiefniveau voor afbeeldingen en andere artefacten worden momenteel geregistreerd:

* **Evenementen pushen**
* **Gebeurtenissen trekken**
* **Gebeurtenissen onttaggen**
* **Gebeurtenissen verwijderen** (inclusief gebeurtenissen voor het verwijderen van opslagplaatsen)

Gebeurtenissen op repository-niveau die momenteel niet zijn geregistreerd: gebeurtenissen verwijderen.

## <a name="registry-resource-logs"></a>Registerbronlogboeken

Resourcelogboeken bevatten informatie die wordt uitgestoten door Azure-bronnen die hun interne bewerking beschrijven. Voor een Azure-containerregister bevatten de logboeken verificatie- en repository-levelgebeurtenissen die zijn opgeslagen in de volgende tabellen. 

* **ContainerRegistryLoginEvents** - Registerverificatiegebeurtenissen en -status, inclusief de binnenkomende identiteit en het IP-adres
* **ContainerRegistryRepositoryEvents** - Bewerkingen zoals push en pull voor afbeeldingen en andere artefacten in registerrepositories
* **AzureMetrics** - [Container register metrics](../azure-monitor/platform/metrics-supported.md#microsoftcontainerregistryregistries) zoals aggregated push and pull counts.

Loggegevens bevatten voor bewerkingen:
  * Status van succes of mislukking
  * Stempels voor begin- en eindtijd

Naast resourcelogboeken biedt Azure een [activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md), één record op abonnementsniveau van Azure-beheergebeurtenissen, zoals het maken of verwijderen van een containerregister.

## <a name="enable-collection-of-resource-logs"></a>Verzameling van bronlogboeken inschakelen

Verzameling van bronlogboeken voor een containerregister is standaard niet ingeschakeld. Schakel expliciet diagnostische instellingen in voor elk register dat u wilt controleren. Zie Diagnostische instelling maken [om platformlogboeken en -statistieken in Azure te verzamelen](../azure-monitor/platform/diagnostic-settings.md)voor opties om diagnostische instellingen in te schakelen.

Als u bijvoorbeeld logboeken en statistieken voor een containerregister in bijna realtime wilt weergeven in Azure Monitor, verzamelt u de bronlogboeken in een werkruimte Log Analytics. Ga als volgt te werk om deze diagnostische instelling in te schakelen met de Azure-portal:

1. Als u nog geen werkruimte hebt, maakt u een werkruimte met de [Azure-portal](../azure-monitor/learn/quick-create-workspace.md). Als u de latentie bij het verzamelen van gegevens wilt minimaliseren, moet u ervoor zorgen dat de werkruimte zich in **dezelfde regio** bevindt als uw containerregister.
1. Selecteer in de portal het register en selecteer **Controle > diagnostische instellingen > Diagnostische instelling toevoegen.**
1. Voer een naam in voor de instelling en selecteer **Verzenden naar logboekanalyse**.
1. Selecteer de werkruimte voor de diagnostische logboeken van het register.
1. Selecteer de logboekgegevens die u wilt verzamelen en klik op **Opslaan**.

In de volgende afbeelding ziet u de creatie van een diagnostische instelling voor een register met behulp van de portal.

![Diagnostische instellingen inschakelen](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Verzamel alleen de gegevens die u nodig hebt, het balanceren van de kosten en uw monitoringbehoeften. Als u bijvoorbeeld alleen verificatiegebeurtenissen hoeft te controleren, selecteert u alleen het loge **ContainerRegistryLoginEvents.** 

## <a name="view-data-in-azure-monitor"></a>Gegevens weergeven in Azure Monitor

Nadat u het verzamelen van diagnostische logboeken in Log Analytics hebt ingeschakeld, kan het enkele minuten duren voordat gegevens worden weergegeven in Azure Monitor. Als u de gegevens in de portal wilt weergeven, selecteert u het register en selecteert u **> logboeken controleren**. Selecteer een van de tabellen die gegevens voor het register bevat. 

Query's uitvoeren om de gegevens weer te geven. Er worden verschillende voorbeeldquery's verstrekt of uw eigen query's uitgevoerd. In de volgende query worden bijvoorbeeld de meest recente 24-uursgegevens opgehaald uit de tabel **ContainerRegistryRepositoryEvents:**

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

In de volgende afbeelding ziet u voorbeelduitvoer:

![Query uitvoeren op logboekgegevens](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Zie Aan de slag met Azure Monitor Log Analytics of probeer de [demo-omgeving](https://portal.loganalytics.io/demo)loganalytics van Logboekanalyse voor een zelfstudie over het gebruik van Log Analytics in de [Azure-portal.](../azure-monitor/log-query/get-started-portal.md) 

Zie [Overzicht van logboekquery's in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over logboekquery's.

### <a name="additional-query-examples"></a>Aanvullende queryvoorbeelden

#### <a name="100-most-recent-registry-events"></a>100 meest recente registergebeurtenissen

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer , OperationName , Identity , Repository , DurationMs , Region , ResultType
```

## <a name="additional-log-destinations"></a>Aanvullende logboekbestemmingen

Naast het verzenden van de logboeken naar Log Analytics, of als alternatief, is een veelvoorkomend scenario het selecteren van een Azure Storage-account als een logboekbestemming. Als u logboeken wilt archiveren in Azure Storage, maakt u een opslagaccount voordat u archivering via de diagnostische instellingen inschakelt.

U diagnostische logboekgebeurtenissen ook streamen naar een [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs kunnen miljoenen gebeurtenissen per seconde opnemen, die u vervolgens transformeren en opslaan met elke realtime analyseprovider. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het gebruik van [Log Analytics](../azure-monitor/log-query/get-started-portal.md) en het maken van [logboekquery's](../azure-monitor/log-query/get-started-queries.md).
* Zie [Overzicht van Azure-platformlogboeken](../azure-monitor/platform/platform-logs-overview.md) voor meer informatie over platformlogboeken die beschikbaar zijn op verschillende lagen Azure.

