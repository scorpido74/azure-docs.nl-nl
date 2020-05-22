---
title: Toepassingen die worden uitgevoerd op Azure Functions bewaken met Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor naadloos kan worden geïntegreerd met uw toepassing die wordt uitgevoerd op Azure Functions, en biedt u de mogelijkheid om de prestaties te bewaken en de problemen met uw apps in de gaten te houden.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/20/2020
ms.openlocfilehash: a936c77abb9aed5886fae8b2ec4a10bb076b7cb5
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776739"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Functions met Azure Monitor bewaken Application Insights

[Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) biedt ingebouwde integratie met Azure-toepassing inzichten om functies te bewaken. 

Application Insights verzamelt logboek-, prestatie-en fout gegevens en detecteert automatisch prestatie afwijkingen. Application Insights bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht kunt krijgen in de manier waarop uw functies worden gebruikt. Wanneer u de zicht baarheid van uw toepassings gegevens hebt, kunt u de prestaties en bruikbaarheid continu verbeteren. U kunt zelfs Application Insights gebruiken tijdens de ontwikkeling van een lokale functie-app-project. 

De vereiste Application Insights instrumentatie is ingebouwd in Azure Functions. Het enige wat u nodig hebt, is een geldige instrumentatie sleutel om uw functie-app te verbinden met een Application Insights bron. De instrumentatie sleutel moet worden toegevoegd aan de toepassings instellingen wanneer de resource van de functie-app wordt gemaakt in Azure. Als uw functie-app deze sleutel nog niet heeft, kunt u deze hand matig instellen. Lees voor meer informatie over het [bewaken van Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Gedistribueerde tracering voor Java-toepassingen (open bare preview)


> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als open bare Preview voor Java-Azure Functions. Voor het verbruiks plan geldt een koude start implicatie van 8-9 seconden.

Als uw toepassingen in Java zijn geschreven, kunt u rijkere gegevens bekijken uit uw functions-toepassingen, waaronder, aanvragen, afhankelijkheden, logboeken en metrieken. Met de aanvullende gegevens kunt u ook end-to-end-trans acties bekijken en diagnosticeren en de toepassings toewijzing bekijken, waarmee veel trans acties worden weer gegeven met een topologische weer gave van de manier waarop de systemen communiceren, en wat de gemiddelde prestatie-en fout tarieven zijn.

De end-to-end diagnostische gegevens en de toepassings toewijzing bieden inzicht in één trans actie/aanvraag. Deze twee functies zijn zeer nuttig voor het vinden van de hoofd oorzaak van betrouwbaarheids problemen en prestatie knelpunten op basis van een aanvraag.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Hoe kan ik gedistribueerde tracering inschakelen voor Java-functie-apps?

Navigeer naar de Blade functies app-overzicht en ga naar configuraties. Klik onder toepassings instellingen op "+ nieuwe toepassings instelling". Voeg de volgende twee toepassings instellingen onder waarden toe en klik vervolgens op opslaan in de linkerbovenhoek. ZO!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Volgende stappen

* Meer instructies en informatie over het controleren van bewakings [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Een overzicht van [gedistribueerde tracering](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) weer geven
* Bekijk welke [toepassings toewijzing](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) voor uw bedrijf kan worden uitgevoerd
* Meer informatie over [aanvragen en afhankelijkheden voor java-apps](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* Meer informatie over [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) en [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)