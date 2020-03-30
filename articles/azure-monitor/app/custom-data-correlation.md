---
title: Azure-toepassingsinzichten | Microsoft Documenten
description: Correleer gegevens van Application Insights naar andere gegevenssets, zoals gegevensverrijking of opzoektabellen, niet-Application Insights-gegevensbronnen en aangepaste gegevens.
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082758"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Toepassingsinzichten correleren met aangepaste gegevensbronnen

Application Insights verzamelt verschillende gegevenstypen: uitzonderingen, traces, paginaweergaven en andere. Hoewel dit vaak voldoende is om de prestaties, betrouwbaarheid en het gebruik van uw toepassing te onderzoeken, zijn er gevallen waarin het nuttig is om de gegevens die zijn opgeslagen in Application Insights te correleren met andere volledig aangepaste gegevenssets.

Enkele situaties waarin u aangepaste gegevens wilt, zijn:

- Gegevensverrijking of opzoektabellen: vul bijvoorbeeld een servernaam aan met de eigenaar van de server en de lablocatie waar deze kan worden gevonden 
- Correlatie met niet-Application Insights-gegevensbronnen: correleer bijvoorbeeld gegevens over een aankoop in een webwinkel met informatie van uw inkoopafhandelingsservice om te bepalen hoe nauwkeurig uw schattingen van uw verzendtijd waren 
- Volledig aangepaste gegevens: veel van onze klanten houden van de querytaal en prestaties van het Azure Monitor-logboekplatform dat Application Insights steunt en deze willen gebruiken om gegevens op te vragen die helemaal niet gerelateerd zijn aan Application Insights. Bijvoorbeeld, om de prestaties van het zonnepaneel te volgen als onderdeel van een smart home installatie zoals [hier](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)beschreven.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Aangepaste gegevens correleren met Application Insights-gegevens 

Aangezien Application Insights wordt ondersteund door het krachtige Azure Monitor-logboekplatform, kunnen we de volledige kracht van Azure Monitor gebruiken om de gegevens in te nemen. Vervolgens schrijven we query's met behulp van de operator 'join'die deze aangepaste gegevens correleert met de gegevens die voor ons beschikbaar zijn in Azure Monitor-logboeken. 

## <a name="ingesting-data"></a>Gegevens opnemen

In deze sectie bekijken we hoe u uw gegevens in Azure Monitor-logboeken krijgen.

Als u er nog geen hebt, indient u een nieuwe werkruimte Log Analytics door [deze instructies](../learn/quick-collect-azurevm.md) op te volgen en de stap 'Een werkruimte maken' op te nemen.

Om te beginnen met het verzenden van logboekgegevens naar Azure Monitor. Er zijn verschillende opties:

- Voor een synchrone mechanisme u rechtstreeks de [API voor gegevensverzamelaar](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) aanroepen of onze Logic App-connector gebruiken : zoek gewoon naar Azure Log Analytics en kies de optie Gegevens verzenden:

  ![Schermafbeelding kiezen en actie voeren](./media/custom-data-correlation/01-logic-app-connector.png)  

- Gebruik de API voor gegevensverzamelaar om een verwerkingspijplijn te maken voor een asynchrone optie. Zie [dit artikel](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) voor meer informatie.

## <a name="correlating-data"></a>Correleren van gegevens

Application Insights is gebaseerd op het Azure Monitor-logboekplatform. We kunnen daarom [cross-resource joins](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search) gebruiken om alle gegevens die we in Azure Monitor hebben opgenomen te correleren met onze Application Insights-gegevens.

We kunnen bijvoorbeeld onze labvoorraad en -locaties opnemen in een tabel met de naam 'LabLocations_CL' in een Log Analytics-werkruimte genaamd "myLA". Als we vervolgens onze aanvragen wilden bekijken die zijn bijgehouden in de Application Insights-app genaamd "myAI" en de machinenamen die de aanvragen hebben ingediend, wilden correleren met de locaties van deze machines die zijn opgeslagen in de eerder genoemde aangepaste tabel, kunnen we de volgende query uitvoeren De context Application Insights of Azure Monitor:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [API-verwijzing voor Gegevensverzamelaar.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)
- Voor meer informatie over [cross-resource joins](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
