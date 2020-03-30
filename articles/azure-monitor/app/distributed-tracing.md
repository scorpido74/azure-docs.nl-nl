---
title: Gedistribueerde tracering in Azure Application Insights | Microsoft Documenten
description: Biedt informatie over de ondersteuning van Microsoft voor gedistribueerde tracering via ons partnerschap in het OpenCensus-project
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c2f384370c3ceaf24164e4a27adc05b1a1e1ddf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294966"
---
# <a name="what-is-distributed-tracing"></a>Wat is Distributed Tracing?

De komst van moderne cloud- en [microservices-architecturen](https://azure.com/microservices) heeft geleid tot eenvoudige, onafhankelijk inzetbare services die kunnen helpen de kosten te verlagen en tegelijkertijd de beschikbaarheid en doorvoer te verhogen. Maar terwijl deze bewegingen hebben gemaakt individuele diensten gemakkelijker te begrijpen als een geheel, ze hebben de algemene systemen moeilijker te redeneren over en debuggen.

In monolithische architecturen zijn we gewend geraakt aan het debuggen met callstacks. Call stacks zijn briljante tools voor het weergeven van de stroom van uitvoering (methode A genaamd Methode B, die methode C genoemd), samen met details en parameters over elk van deze oproepen. Dit is geweldig voor monolieten of services die op één proces worden uitgevoerd, maar hoe debugken we wanneer de oproep een procesgrens overschrijdt, niet alleen een verwijzing op de lokale stack? 

Dat is waar gedistribueerde opsporing komt in.  

Distributed tracing is het equivalent van call stacks voor moderne cloud- en microservices-architecturen, met de toevoeging van een simplistische performance profiler. In Azure Monitor bieden we twee ervaringen voor het consumeren van gedistribueerde traceringsgegevens. De eerste is onze [transactiediagnostische](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) weergave, die is als een callstack met een tijdsdimensie toegevoegd. De transactiediagnostische weergave biedt inzicht in één transactie/aanvraag en is handig voor het vinden van de hoofdoorzaak van betrouwbaarheidsproblemen en prestatieknelpunten per aanvraag.

Azure Monitor biedt ook een [toepassingskaartweergave](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) die veel transacties samenvoegt om een topologische weergave te geven van hoe de systemen op elkaar inwerken en wat de gemiddelde prestatie- en foutpercentages zijn. 

## <a name="how-to-enable-distributed-tracing"></a>Gedistribueerde tracering inschakelen

Het inschakelen van gedistribueerde tracering over de services in een toepassing is net zo eenvoudig als het toevoegen van de juiste SDK of bibliotheek aan elke service, op basis van de taal waarin de service is geïmplementeerd.

## <a name="enabling-via-application-insights-sdks"></a>Inschakelen via Application Insights SDKs

De Application Insights SDKs voor .NET, .NET Core, Java, Node.js en JavaScript ondersteunen alle native distributed tracing. Instructies voor het installeren en configureren van elke Application Insights SDK zijn hieronder beschikbaar:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [Javascript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Met de juiste Application Insights SDK geïnstalleerd en geconfigureerd, wordt tracing informatie automatisch verzameld voor populaire frameworks, bibliotheken en technologieën door SDK afhankelijkheid auto-verzamelaars. De volledige lijst met ondersteunde technologieën is beschikbaar in [de documentatie voor automatische verzameling afhankelijkheid.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)

 Bovendien kan elke technologie handmatig worden bijgehouden met een oproep tot [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) op de [TelemetryClient.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)

## <a name="enable-via-opencensus"></a>Inschakelen via OpenCensus

Naast de Application Insights SDKs ondersteunt Application Insights ook distributed tracing via [OpenCensus.](https://opencensus.io/) OpenCensus is een open source, vendor-agnostische, enkele distributie van bibliotheken om metrics verzameling en gedistribueerde tracering voor diensten te bieden. Het stelt de open source-gemeenschap ook in staat om gedistribueerde tracering mogelijk te maken met populaire technologieën zoals Redis, Memcached of MongoDB. [Microsoft werkt samen met verschillende andere monitoring- en cloudpartners aan OpenCensus.](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/)

[Python](opencensus-python.md) 

De OpenCensus-website onderhoudt API-referentiedocumentatie voor [Python](https://opencensus.io/api/python/trace/usage.html) en [Go,](https://godoc.org/go.opencensus.io)evenals verschillende handleidingen voor het gebruik van OpenCensus. 

## <a name="next-steps"></a>Volgende stappen

* [Gebruikshandleiding OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Toepassingskaart](./../../azure-monitor/app/app-map.md)
* [End-to-end prestatiebewaking](./../../azure-monitor/learn/tutorial-performance.md)
