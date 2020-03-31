---
title: Bewaking van azure-servicefabric-toepassingsniveau
description: Meer informatie over gebeurtenissen en logboeken op toepassings- en serviceniveau die worden gebruikt om Azure Service Fabric-clusters te controleren en te diagnosticeren.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464719"
---
# <a name="application-logging"></a>Toepassingslogboeken

Het instrumenteren van uw code is niet alleen een manier om inzicht te krijgen in uw gebruikers, maar ook de enige manier waarop u weten of er iets mis is in uw toepassing, en om te diagnosticeren wat er moet worden opgelost. Hoewel het technisch gezien mogelijk is om een debugger aan een productieservice te koppelen, is het geen gangbare praktijk. Het is dus belangrijk om gedetailleerde instrumentatiegegevens te hebben.

Sommige producten automatisch instrument uw code. Hoewel deze oplossingen goed kunnen werken, is handmatige instrumentatie bijna altijd nodig om specifiek te zijn voor uw bedrijfslogica. Uiteindelijk moet u voldoende informatie hebben om de toepassing forensisch te debuggen. Service Fabric-toepassingen kunnen worden geinstrumenteerd met elk logging-framework. Dit document beschrijft een paar verschillende benaderingen voor het instrumenteren van uw code en wanneer u de ene benadering boven de andere moet kiezen. 

Zie [Logboekregistratie toevoegen aan uw Service Fabric-toepassing voor](service-fabric-how-to-diagnostics-log.md)voorbeelden over het gebruik van deze suggesties.

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights heeft een rijke integratie met Service Fabric out of the box. Gebruikers kunnen de Nuget-pakketten van AI Service Fabric toevoegen en gegevens en logboeken ontvangen die zijn gemaakt en verzameld, zichtbaar in de Azure-portal. Daarnaast worden gebruikers aangemoedigd om hun eigen telemetrie toe te voegen om hun toepassingen te diagnosticeren en te debuggen en bij te houden welke services en delen van hun toepassing het meest worden gebruikt. De klasse [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) in de SDK biedt vele manieren om telemetrie in uw toepassingen bij te houden. Bekijk een voorbeeld van hoe u instrumenteren en toepassingsinzichten aan uw toepassing toevoegen in onze zelfstudie voor [het monitoren en diagnosticeren van een .NET-toepassing](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Wanneer u een Service Fabric-oplossing maakt op basis van een sjabloon in Visual Studio, wordt een **gebeurtenisbron**-afgeleide klasse (**ServiceEventSource** of **ActorEventSource**) gegenereerd. Er wordt een sjabloon gemaakt waarin u gebeurtenissen voor uw toepassing of service toevoegen. De naam **EventSource** **moet** uniek zijn en moet worden hernoemd&gt;-vanuit het standaardsjabloontekenreeksmycompany-oplossingsproject.&lt;&gt;&lt; Het hebben van meerdere **EventSource-definities** die dezelfde naam gebruiken, veroorzaakt een probleem bij het uitvoeren van de tijd. Elke gedefinieerde gebeurtenis moet een unieke id hebben. Als een id niet uniek is, treedt er een runtime-fout op. Sommige organisaties wijzen bereiken van waarden vooraf toe voor id's om conflicten tussen afzonderlijke ontwikkelteams te voorkomen. Zie voor meer informatie [de blog van Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) of de [MSDN-documentatie.](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)

## <a name="aspnet-core-logging"></a>ASP.NET Core-logboekregistratie

Het is belangrijk om zorgvuldig te plannen hoe u uw code instrument. Het juiste instrumentatieplan kan u helpen voorkomen dat uw codebasis mogelijk wordt gedestabiliseerd en vervolgens de code opnieuw moet worden ingeroepen. Om risico's te verminderen, u een instrumentatiebibliotheek kiezen zoals [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), die deel uitmaakt van Microsoft ASP.NET Core. ASP.NET Core heeft een [ILogger-interface](/dotnet/api/microsoft.extensions.logging.ilogger) die u gebruiken met de provider van uw keuze, terwijl het minimaliseren van het effect op de bestaande code. U de code gebruiken in ASP.NET Core op Windows en Linux, en in het volledige .NET Framework, zodat uw instrumentatiecode is gestandaardiseerd.

## <a name="next-steps"></a>Volgende stappen

Zodra u uw logging provider hebt gekozen om uw applicaties en services instrument, uw logs en gebeurtenissen moeten worden samengevoegd voordat ze kunnen worden verzonden naar een analyse platform. Lees [meer](service-fabric-diagnostics-event-analysis-appinsights.md) over Application Insights en [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) om een aantal van de aanbevolen Azure Monitor-aanbevolen opties beter te begrijpen.
