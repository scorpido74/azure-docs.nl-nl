---
title: Overzicht van Azure Event Hubs API | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de beschikbare API's (runtime en beheer) voor het gebruik van de Azure Event Hubs-service.
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
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162222"
---
# <a name="available-event-hubs-apis"></a>Beschikbare API's voor gebeurtenishubs

In dit artikel wordt de set beschikbare API-clients beschreven die u gebruiken voor het beheren van gebeurtenishubsbronnen.

## <a name="runtime-apis"></a>Runtime API's

In de volgende sectie worden alle momenteel beschikbare Azure Event Hubs runtime clients beschreven. Hoewel sommige van deze bibliotheken ook beperkte beheerfunctionaliteit bevatten, zijn er ook [specifieke bibliotheken](#management-apis) die zich toeleggen op beheerbewerkingen. De kernfocus van deze bibliotheken is het verzenden en ontvangen van berichten vanuit een gebeurtenishub.

Zie [aanvullende informatie](#additional-information)voor meer informatie over de huidige status van elke runtime-bibliotheek.

| Taal/Platform | Clientpakket | EventProcessorHost-pakket | Opslagplaats |
| --- | --- | --- | --- |
| .NET Standard | [NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet (NuGet)](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N.v.t. |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Knooppunt | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N.v.t. | [GitHub GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N.v.t. | N.v.t. | [GitHub GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Aanvullende informatie

#### <a name="net"></a>.NET

Het .NET-ecosysteem heeft meerdere looptijden, dus er zijn meerdere .NET-bibliotheken voor gebeurtenishubs. De .NET-standaardbibliotheek kan worden uitgevoerd met .NET Core of het .NET Framework, terwijl de .NET Framework-bibliotheek alleen kan worden uitgevoerd in een .NET Framework-omgeving. Zie frameworkversies voor meer informatie over .NET [Framework-versies.](https://docs.microsoft.com/dotnet/articles/standard/frameworks)

#### <a name="node"></a>Knooppunt

De [JavaScript-bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) is momenteel in preview en wordt onderhouden als een nevenproject door Microsoft-medewerkers en externe bijdragers. Alle bijdragen inclusief broncode zijn welkom en worden beoordeeld.

## <a name="management-apis"></a>Beheer API’s

In de volgende tabel worden alle momenteel beschikbare beheerspecifieke bibliotheken weergegeven. Geen van deze bibliotheken bevat runtime-bewerkingen en is uitsluitend bedoeld voor het beheer van entiteiten van Gebeurtenishubs.

| Taal/Platform | Beheerpakket | Opslagplaats |
| --- | --- | --- |
| .NET Standard | [NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van gebeurtenishubs](event-hubs-what-is-event-hubs.md)
* [Een gebeurtenishub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
