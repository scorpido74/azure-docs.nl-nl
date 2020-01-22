---
title: Overzicht van Azure Event Hubs API | Microsoft Docs
description: In dit artikel vindt u een overzicht van de beschik bare Api's (runtime en beheer) voor het gebruik van de Azure Event Hubs-service.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: c852bdeb30efe6acf626ae67028ec1ccb9e0b6db
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310965"
---
# <a name="available-event-hubs-apis"></a>Beschik bare Event Hubs-Api's

In dit artikel wordt de set beschik bare API-clients beschreven die u kunt gebruiken voor het beheren van Event Hubs-resources.

## <a name="runtime-apis"></a>Runtime-Api's

In de volgende sectie worden alle momenteel beschik bare Azure Event Hubs runtime-clients beschreven. Hoewel sommige van deze bibliotheken ook beperkte beheer functionaliteit bevatten, zijn er ook [specifieke bibliotheken](#management-apis) die zijn toegewezen aan beheer bewerkingen. De belangrijkste focus van deze bibliotheken is het verzenden en ontvangen van berichten van een Event Hub.

Zie [aanvullende informatie](#additional-information)voor meer informatie over de huidige status van elke runtime bibliotheek.

| Taal/platform | Clientpakket | EventProcessorHost-pakket | Opslagplaats |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Knooppunt | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Aanvullende informatie

#### <a name="net"></a>.NET

Het .NET-ecosysteem heeft meerdere runtimes, dus er zijn meerdere .NET-bibliotheken voor Event Hubs. De .NET Standard-bibliotheek kan worden uitgevoerd met behulp van .NET core of de .NET Framework, terwijl de .NET Framework bibliotheek alleen kan worden uitgevoerd in een .NET Framework omgeving. Zie [Framework-versies](https://docs.microsoft.com/dotnet/articles/standard/frameworks)(Engelstalig) voor meer informatie over .NET Framework versies.

#### <a name="node"></a>Knooppunt

De [node. JS-bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) bevindt zich momenteel in de preview-versie en wordt beheerd als een kant project door micro soft-werk nemers en externe inzenders. Alle bijdragen, inclusief bron code, zijn welkom en worden gecontroleerd.

## <a name="management-apis"></a>Beheer API’s

De volgende tabel geeft een lijst van alle momenteel beschik bare beheer bibliotheken. Geen van deze bibliotheken bevat runtime-bewerkingen en is uitsluitend bedoeld voor het beheren van Event Hubs entiteiten.

| Taal/platform | Management pakket | Opslagplaats |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
