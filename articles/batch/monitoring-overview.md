---
title: Azure-batch controleren | Microsoft Documenten
description: Meer informatie over Azure-bewakingsservices, statistieken, diagnostische logboeken en andere bewakingsfuncties voor Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: d251229c522bd4d6daca894513eaae14d244d8a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025857"
---
# <a name="monitor-batch-solutions"></a>Batch-oplossingen controleren

Azure en de Batch-service bieden een reeks services, hulpprogramma's en API's om uw Batch-oplossingen te bewaken. Met dit overzichtsartikel u een monitoringaanpak kiezen die aan uw behoeften voldoet.

Zie [Azure-toepassingen en -resources bewaken](../monitoring-and-diagnostics/monitoring-overview.md)voor een overzicht van de Azure-componenten en -services die beschikbaar zijn om Azure-bronnen te bewaken.

## <a name="subscription-level-monitoring"></a>Monitoring op abonnementsniveau

Op abonnementsniveau, waaronder Batch-accounts, verzamelt het [Azure-activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md) operationele gebeurtenisgegevens in [verschillende categorieën.](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log)

Met name voor Batch-accounts verzamelt het activiteitenlogboek gebeurtenissen met betrekking tot het maken en verwijderen van accounts en sleutelbeheer.

Een manier om gebeurtenissen uit uw activiteitenlogboek op te halen, is door de Azure-portal te gebruiken. Klik**op Activiteitslogboek** **voor alle services** > . Of query voor gebeurtenissen met behulp van de Azure CLI, PowerShell-cmdlets of de Azure Monitor REST API. U ook het activiteitenlogboek exporteren of waarschuwingen voor [activiteitenlogboeken](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)configureren.

## <a name="batch-account-level-monitoring"></a>Controle op batchaccountniveau

Controleer elk Batch-account met behulp van functies van [Azure Monitor.](../azure-monitor/overview.md) Azure Monitor verzamelt [statistieken](../azure-monitor/platform/data-platform-metrics.md) en optioneel [diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md) voor resources die worden verdeeld op het niveau van een Batch-account, zoals pools, taken en taken. Verzamel en gebruik deze gegevens handmatig of programmatisch om activiteiten in uw Batch-account te controleren en problemen te diagnosticeren. Zie [Batchstatistieken, waarschuwingen en logboeken voor diagnostische evaluatie en monitoring voor](batch-diagnostics.md)meer informatie.
 
> [!NOTE]
> Statistieken zijn standaard beschikbaar in uw Batch-account zonder extra configuratie en ze hebben een rollende geschiedenis van 30 dagen. U moet diagnostische logboekregistratie inschakelen voor een Batch-account en u extra kosten maken voor het opslaan of verwerken van diagnostische logboekgegevens. 

## <a name="batch-resource-monitoring"></a>Controle van batchbronnen

Gebruik in uw Batch-toepassingen de Batch-API's om de status van uw resources te controleren of op te vragen, inclusief taken, taken, knooppunten en groepen. Bijvoorbeeld:

* [Taken en rekenknooppunten tellen op status](batch-get-resource-counts.md)
* [Query's maken om batchbronnen efficiënt weer te geven](batch-efficient-list-queries.md)
* [Taakafhankelijkheden maken](batch-task-dependencies.md)
* Een [taak voor taak voor taak voor taak voor taak voor een taak voor taak functie voor](/rest/api/batchservice/job/add#jobmanagertask)
* De [taakstatus controleren](/rest/api/batchservice/task/list#taskstate)
* De [knooppuntstatus controleren](/rest/api/batchservice/computenode/list#computenodestate)
* De [poolstatus controleren](/rest/api/batchservice/pool/get#poolstate)
* Het [poolgebruik in het account controleren](/rest/api/batchservice/pool/listusagemetrics)
* [Poolknooppunten tellen op status](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM-prestatiemeteritems en toepassingsbewaking

* [Application Insights](../azure-monitor/app/app-insights-overview.md) is een Azure-service die u gebruiken om de beschikbaarheid, prestaties en het gebruik van uw Batch-taken en -taken programmatisch te controleren. Download eenvoudig prestatiemeteritems van compute nodes (VM's) en aangepaste informatie voor taken buiten de VM's. 

  Zie bijvoorbeeld [Een Batch .NET-toepassing met Application Insights en](monitor-application-insights.md) het bijbehorende [codevoorbeeld](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)controleren en debuggen.

  > [!NOTE]
  > U extra kosten maken voor het gebruik van Application Insights. Bekijk de [prijsopties](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer) is een gratis, rijk uitgeruste, zelfstandige clienttool om Azure Batch-toepassingen te maken, te debuggen en te controleren. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows. Configureer optioneel uw Batch-oplossing om [Application Insights-gegevens weer](https://github.com/Azure/batch-insights) te geven, zoals VM-prestatiemeteritems in Batch Explorer.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Meer informatie over [diagnostische logboekregistratie](batch-diagnostics.md) met Batch.