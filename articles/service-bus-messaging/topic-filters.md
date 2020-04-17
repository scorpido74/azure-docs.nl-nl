---
title: Onderwerpenfilters voor Azure Service Bus | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe abonnees kunnen definiëren welke berichten ze van een onderwerp willen ontvangen door filters op te geven.
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: fb6092b7ccb3d1a4214f8d26119d9dc50b0ed317
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482052"
---
# <a name="topic-filters-and-actions"></a>Onderwerpfilters en acties

Abonnees kunnen definiëren welke berichten ze willen ontvangen van een onderwerp. Deze berichten worden opgegeven in de vorm van een of meer benoemde abonnementsregels. Elke regel bestaat uit een voorwaarde die bepaalde berichten selecteert en een actie die het geselecteerde bericht annoteert. Voor elke regelvoorwaarde waaraan wordt voldaan, produceert het abonnement een kopie van het bericht, dat anders voor elke overeenkomende regel anders kan worden geannoteerd.

Elk nieuw gemaakt onderwerpabonnement heeft een initiële standaardabonnementsregel. Als u niet expliciet een filtervoorwaarde voor de regel opgeeft, is het toegepaste filter het **echte** filter waarmee alle berichten in het abonnement kunnen worden geselecteerd. De standaardregel heeft geen bijbehorende annotatieactie.

Service Bus ondersteunt drie filtervoorwaarden:

-   *Booleaanse filters* - **TrueFilter** en **FalseFilter** zorgen ervoor dat alle aankomende berichten **(true)** of geen van de aankomende berichten **(false)** worden geselecteerd voor het abonnement.

-   *SQL-filters* - Een **SqlFilter** bevat een SQL-achtige voorwaardelijke expressie die in de broker wordt geëvalueerd tegen de door gebruikers gedefinieerde eigenschappen en systeemeigenschappen van de binnenkomende berichten. Alle systeemeigenschappen moeten vooraf `sys.` zijn vastgelegd in de voorwaardelijke expressie. De [SQL-taalsubset voor filtervoorwaardentests](service-bus-messaging-sql-filter.md) voor`EXISTS`het bestaan van`IS NULL`eigenschappen ( ), null-waarden ( ), logische NOT/AND/OR, relationele operatoren, eenvoudige numerieke rekenkunde en eenvoudige tekstpatroon die overeenkomt met `LIKE`.

-   *Correlatiefilters* - Een **CorrelationFilter** bevat een reeks voorwaarden die zijn gekoppeld aan een of meer gebruikers- en systeemeigenschappen van een binnenkomend bericht. Een gemeenschappelijk gebruik is om te matchen met de eigenschap **CorrelationId,** maar de toepassing kan er ook voor kiezen om te matchen met de volgende eigenschappen:

    - **Contenttype**
     - **Label**
     - **Messageid**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **Sessionid** 
     - **Aan**
     - door de gebruiker gedefinieerde eigenschappen. 
     
     Er bestaat een overeenkomst wanneer de waarde van een aankomend bericht voor een eigenschap gelijk is aan de waarde die is opgegeven in het correlatiefilter. Voor tekenreeksexpressies is de vergelijking hoofdlettergevoelig. Bij het opgeven van meerdere overeenkomsteigenschappen combineert het filter deze als een logische EN voorwaarde, wat betekent dat het filter overeenkomt, alle voorwaarden moeten overeenkomen.

Alle filters evalueren berichteigenschappen. Filters kunnen de berichttekst niet evalueren.

Complexe filterregels vereisen verwerkingscapaciteit. Met name het gebruik van SQL-filterregels zorgt voor een lagere algehele doorvoer van berichten op het niveau van het abonnement, het onderwerp en de naamruimte. Waar mogelijk moeten toepassingen correlatiefilters kiezen boven SQL-achtige filters, omdat ze veel efficiënter zijn in de verwerking en minder invloed hebben op de doorvoer.

## <a name="actions"></a>Acties

Met SQL-filtervoorwaarden u een actie definiëren die het bericht kan annoteren door eigenschappen en waarden toe te voegen, te verwijderen of te vervangen. De actie [maakt gebruik van een SQL-achtige expressie](service-bus-messaging-sql-filter.md) die losjes leunt op de sql-update-instructiesyntaxis. De actie wordt gedaan op het bericht nadat het is gekoppeld en voordat het bericht is geselecteerd in het abonnement. De wijzigingen in de berichteigenschappen zijn privé voor het bericht dat in het abonnement is gekopieerd.

## <a name="usage-patterns"></a>Gebruikspatronen

Het eenvoudigste gebruiksscenario voor een onderwerp is dat elk abonnement een kopie krijgt van elk bericht dat naar een onderwerp wordt verzonden, waardoor een uitzendpatroon mogelijk is.

Filters en acties maken nog twee groepen patronen mogelijk: partitionering en routering.

Partitionering maakt gebruik van filters om berichten op een voorspelbare en wederzijds exclusieve manier over verschillende bestaande onderwerpabonnementen te distribueren. Het scheidingspatroon wordt gebruikt wanneer een systeem wordt uitgeschaald om veel verschillende contexten te verwerken in functioneel identieke compartimenten die elk een subset van de totale gegevens bevatten; bijvoorbeeld klantprofielinformatie. Met partitionering legt een uitgever het bericht in een onderwerp in zonder enige kennis van het partitionerende model. Het bericht wordt vervolgens verplaatst naar het juiste abonnement waarvan het vervolgens kan worden opgehaald door de berichthandler van de partitie.

Routering gebruikt filters om berichten op een voorspelbare manier over onderwerpabonnementen te distribueren, maar niet noodzakelijkerwijs exclusief. In combinatie met de functie [automatisch doorsturen](service-bus-auto-forwarding.md) kunnen onderwerpfilters worden gebruikt om complexe routeringsgrafieken te maken in een naamruimte van servicebus voor berichtdistributie binnen een Azure-gebied. Met Azure Functions of Azure Logic Apps die fungeren als een brug tussen azure servicebusnaamruimten, u complexe algemene topologieën maken met directe integratie in line-of-business-toepassingen.


> [!NOTE]
> Op dit moment u in de Azure-portal geen filterregels voor abonnementen opgeven. U een van de ondersteunde SDKs- of Azure Resource Manager-sjablonen gebruiken om abonnementsregels te definiëren. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende voorbeelden: 

- [.NET - Eenvoudige zelfstudie verzenden en ontvangen met filters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET - Onderwerpfilters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Javascript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/javascript/advanced/topicFilters.js)
- [Script typen](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/samples/typescript/src/advanced/topicFilters.ts)
- [Azure Resource Manager-sjabloon](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


