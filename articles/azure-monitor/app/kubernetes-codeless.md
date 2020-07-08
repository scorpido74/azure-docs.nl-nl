---
title: Bewaak toepassingen op Azure Kubernetes service (AKS) met Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor naadloos kan worden geïntegreerd met uw toepassing die wordt uitgevoerd op Kubernetes, en u kunt de problemen met uw apps in geen enkel moment herkennen.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83773760"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Bewaking van nul-toepassings controle voor Kubernetes-Azure Monitor Application Insights

> [!IMPORTANT]
>  Op dit moment kunt u bewaking inschakelen voor uw java-apps die worden uitgevoerd op Kubernetes zonder uw code te gebruiken. Gebruik de [zelfstandige Java-Agent](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Hoewel de oplossing om toepassings bewaking naadloos in te scha kelen in de Works voor andere talen, gebruikt u de Sdk's om uw apps te bewaken die worden uitgevoerd op AKS: [ASP.net core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [Java script](https://docs.microsoft.com/azure/azure-monitor/app/javascript)en [python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Toepassings bewaking zonder de code te instrumenteren
Momenteel kunt u met alleen Java toepassings bewaking inschakelen zonder de code te instrumenteren. Als u toepassingen in andere talen wilt bewaken, gebruikt u de Sdk's. 

## <a name="java"></a>Java
Zodra de Java-Agent is ingeschakeld, worden er automatisch een groot aantal aanvragen, afhankelijkheden, logboeken en metrische gegevens van de meest gebruikte bibliotheken en frameworks verzameld.

Volg [de gedetailleerde instructies](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) voor het bewaken van uw java-apps die worden uitgevoerd in Kubernetes-apps, evenals andere omgevingen. 

## <a name="other-languages"></a>Andere talen

Voor de toepassingen in andere talen raden we u aan de Sdk's te gebruiken:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) en [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Bekijk een overzicht van [gedistribueerde tracering](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) en zie welke [toepassings toewijzing](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) voor uw bedrijf kan worden uitgevoerd