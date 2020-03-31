---
title: Overzicht van Azure Relay API | Microsoft Documenten
description: Dit artikel bevat een overzicht van de beschikbare Azure Relay API's (.NET Standard, .NET Framework, Node.js, enz.)
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513963"
---
# <a name="available-relay-apis"></a>Beschikbare Relay-API's

## <a name="runtime-apis"></a>Runtime API's

In de volgende tabel worden alle momenteel beschikbare relayruntimeclients weergegeven.

De [aanvullende informatiesectie](#additional-information) bevat meer informatie over de status van elke runtime-bibliotheek.

| Taal/Platform | Beschikbare functie | Clientpakket | Opslagplaats |
| --- | --- | --- | --- |
| .NET Standard | Hybride verbindingen | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF-relay | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N.v.t. |
| Knooppunt | Hybride verbindingen | [Websockets:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-verzoeken:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Aanvullende informatie

#### <a name="net"></a>.NET

Het .NET-ecosysteem heeft meerdere looptijden, vandaar dat er meerdere .NET-bibliotheken zijn voor de Relay. De .NET-standaardbibliotheek kan worden uitgevoerd met .NET Core of het .NET Framework, terwijl de .NET Framework-bibliotheek alleen kan worden uitgevoerd in een .NET Framework-omgeving. Zie [frameworkversies](/dotnet/articles/standard/frameworks)voor meer informatie over .NET Frameworks.

De .NET Framework-bibliotheek ondersteunt alleen het WCF-programmeermodel en is gebaseerd `net.tcp` op een eigen binair protocol op basis van het WCF-transport. Dit protocol en deze bibliotheek worden onderhouden voor achterwaartse compatibiliteit met bestaande toepassingen.

De .NET-standaardbibliotheek is gebaseerd op de open protocoldefinitie voor het relay voor hybride verbindingen die voortbouwt op HTTP en WebSockets. De bibliotheek ondersteunt een streamabstractie via Websockets en een eenvoudige API-beweging voor het beantwoorden van HTTP-verzoeken voor het beantwoorden van HTTP-aanvragen. In het voorbeeld [van de web-API](https://github.com/Azure/azure-relay-dotnet) ziet u hoe hybride verbindingen kunnen worden geïntegreerd met ASP.NET Core voor webservices.

#### <a name="nodejs"></a>Node.js

De modules Hybride verbindingen in de bovenstaande tabel vervangen of wijzigen bestaande Node.js-modules met alternatieve implementaties die luisteren op de Azure Relay-service in plaats van de lokale netwerkstack.

De `hyco-https` module wijzigt en overschrijft gedeeltelijk de `http` kern `https`Node.js-modules en biedt een HTTPS-listenerimplementatie die compatibel is met veel bestaande Node.js-modules en toepassingen die afhankelijk zijn van deze kernmodules.

De `hyco-ws` `hyco-websocket` en modules `ws` wijzigen `websocket` de populaire en modules voor Node.js, het verstrekken van alternatieve luisteraar implementaties die modules en toepassingen vertrouwen op een van beide modules om te werken achter de Hybrid Connections Relay.

Details over deze modules zijn te vinden in de [GitHub-repository met Azure-Relay-Node.](https://github.com/Azure/azure-relay-node)

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over Azure Relay naar de volgende koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Veelgestelde vragen over Relay](relay-faq.md)
