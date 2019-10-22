---
title: Gedistribueerde tracering in Azure-toepassing Insights | Microsoft Docs
description: Bevat informatie over de ondersteuning van micro soft voor gedistribueerde tracering via onze lokale doorstuur server en samen werking in het opentellings project
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0b51617966ad85037ad29f3e5005a17f66602e01
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677122"
---
# <a name="what-is-distributed-tracing"></a>Wat is gedistribueerde tracering?

De komst van moderne Cloud-en micro [Services](https://azure.com/microservices) -architecturen heeft tot eenvoudige, onafhankelijk Implementeer bare Services geleid die de kosten kunnen helpen verlagen en de beschik baarheid en door Voer verhogen. Maar hoewel deze bewegingen afzonderlijke services in een geheel eenvoudiger te begrijpen hebben gemaakt, hebben ze het algemene systeem moeilijker gemaakt om redenen te krijgen en fouten op te sporen.

In monolithische architecturen zijn we gebruikt voor het opsporen van fouten met Call stacks. Aanroep Stacks zijn schitterende hulp middelen voor het weer geven van de uitvoering van de stroom (methode A aangeroepen methode B, die methode C wordt genoemd), samen met details en para meters voor elk van deze aanroepen. Dit is ideaal voor Monoliths of services die worden uitgevoerd op één proces, maar hoe kunnen we fouten opsporen wanneer de aanroep over een proces grens komt, niet alleen een verwijzing naar de lokale stack? 

Dat is waar gedistribueerde tracering beschikbaar is in.  

Gedistribueerde tracering is het equivalent van aanroep stacks voor moderne Cloud-en micro Services-architecturen, met toevoeging van een vereenvoudigde-prestatie Profiler dat is opgetreden in. In Azure Monitor bieden we twee ervaringen voor het gebruiken van gedistribueerde tracerings gegevens. De eerste is de weer gave van de [Diagnostische gegevens over trans acties](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) , die als een aanroep stack met een tijd dimensie is toegevoegd in. De weer gave voor de diagnostische gegevens van de trans actie biedt inzicht in één trans actie/aanvraag en is handig voor het vinden van de hoofd oorzaak van betrouwbaarheids problemen en prestatie knelpunten op basis van een aanvraag.

Azure Monitor biedt ook een weer gave van een [toepassings overzicht](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) waarmee veel trans acties worden weer gegeven met een topologische weer gave van de manier waarop de systemen communiceren, en wat de gemiddelde prestatie-en fout tarieven zijn. 

## <a name="how-to-enable-distributed-tracing"></a>Gedistribueerde tracering inschakelen

Het inschakelen van gedistribueerde tracering over de services in een toepassing is net zo eenvoudig als het toevoegen van de juiste SDK of bibliotheek aan elke service, op basis van de taal waarin de service is geïmplementeerd.

## <a name="enabling-via-application-insights-sdks"></a>Inschakelen via Application Insights Sdk's

De Application Insights Sdk's voor .NET, .NET core, Java, node. js en Java script bieden allemaal ondersteuning voor gedistribueerde tracering. Instructies voor het installeren en configureren van elke Application Insights SDK zijn hieronder beschikbaar:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Met de juiste Application Insights SDK geïnstalleerd en geconfigureerd, wordt tracerings informatie automatisch verzameld voor populaire frameworks, Bibliotheken en technologieën door de auto-Collector van de SDK-afhankelijkheden. De volledige lijst met ondersteunde technologieën is beschikbaar in [de documentatie voor het automatisch verzamelen van afhankelijkheden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 Daarnaast kan elke technologie hand matig worden gevolgd door een aanroep van [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) op de [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Inschakelen via opentellingen

Naast de Application Insights Sdk's ondersteunt Application Insights ook gedistribueerde tracering via [Opentellingen](https://opencensus.io/). Opentelling is een open source, leverancier-neutraal, één distributie van bibliotheken voor het verzamelen van metrische gegevens en gedistribueerde tracering voor services. Daarnaast kan de open source-community gedistribueerde tracering inschakelen met populaire technologieën zoals redis, memcached of MongoDB. [Micro soft werkt samen met Opentellingen met verschillende andere bewakings-en Cloud partners](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Als u gedistribueerde tracerings mogelijkheden wilt toevoegen aan een toepassing met opentellingen, moet u eerst [de Application Insights lokale doorstuur server installeren en configureren](./../../azure-monitor/app/opencensus-local-forwarder.md). Vervolgens configureert u opentellingen om gedistribueerde tracerings gegevens via de lokale doorstuur server te routeren. Zowel [python](./../../azure-monitor/app/opencensus-python.md) als [Go](./../../azure-monitor/app/opencensus-go.md) worden ondersteund.

De opentellings website houdt API-referentie documentatie voor [python](https://opencensus.io/api/python/trace/usage.html) en [Go](https://godoc.org/go.opencensus.io), evenals verschillende hand leidingen voor het gebruik van opentellingen. 

## <a name="next-steps"></a>Volgende stappen

* [Gebruiks overzicht van opentellingen python](https://opencensus.io/api/python/trace/usage.html)
* [Toepassings overzicht](./../../azure-monitor/app/app-map.md)
* [End-to-end prestatie bewaking](./../../azure-monitor/learn/tutorial-performance.md)
