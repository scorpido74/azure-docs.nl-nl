---
title: Overzicht - Azure Serverless voor cloudgebaseerde apps en oplossingen
description: Meer informatie over het maken van cloudgebaseerde apps en oplossingen zonder u zorgen te maken over infrastructuur met Azure Logic Apps en Azure-functies
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666547"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure Serverless: overzicht voor het bouwen van cloudgebaseerde apps en oplossingen met Azure Logic Apps en Azure-functies

[Serverless-apps](https://azure.microsoft.com/solutions/serverless/) bieden voordelen zoals een hogere ontwikkelingssnelheid, minder code, eenvoud en schaalbaarheid. Dit artikel behandelt de verschillende kenmerken van serverloze oplossingen en Azure-serverloze aanbiedingen.

## <a name="what-is-serverless"></a>Wat is serverless?

Serverless betekent niet dat er geen servers zijn, maar ontwikkelaars hoeven zich geen zorgen te maken over servers. Een groot deel van de traditionele applicatie-ontwikkeling is het beantwoorden van vragen rond schaalvergroting, hosting en monitoring oplossingen om te voldoen aan de eisen van de applicatie. Met serverless worden deze vragen behandeld als onderdeel van de oplossing. Bovendien worden serverloze apps gefactureerd op basis van een op verbruik gebaseerd plan. Als de app nooit wordt gebruikt, worden er geen kosten in rekening gebracht. Deze functies helpen ontwikkelaars zich uitsluitend te richten op de bedrijfslogica van een oplossing.

De belangrijkste Azure-services voor serverless zijn [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) en [Azure-functies.](https://azure.microsoft.com/services/functions/) Beide oplossingen volgen de eerder beschreven principes en helpen ontwikkelaars om robuuste cloud-apps te bouwen met minimale code.

## <a name="what-is-azure-logic-apps"></a>Wat is Azure Logic Apps?

[Azure Logic Apps](logic-apps-overview.md) biedt een manier om schaalbare integraties en workflows in de cloud te vereenvoudigen en te implementeren. Deze service biedt een visuele ontwerper om uw proces te modelleren en te automatiseren als een reeks stappen die een workflow worden genoemd. Er zijn veel [connectoren](../connectors/apis-list.md) voor cloudservices en on-premises systemen die snel een serverloze app verbinden met andere API's. Elke logische app begint met een trigger, zoals 'Wanneer een account wordt toegevoegd aan Dynamics CRM'. Nadat de trigger is geactiveerd, kan de werkstroom combinaties van acties, conversies en voorwaardelijke logica uitvoeren. Logic Apps is een goede keuze bij het orkestreren van verschillende Azure-functies in een proces, vooral wanneer het proces interactie met een extern systeem of API vereist.

Als u aan de slag wilt met Logische Apps, begint u met [het maken van uw eerste logische app.](quickstart-create-first-logic-app-workflow.md) Zie de [verwijzing naar](logic-apps-workflow-definition-language.md)ontwikkelaars voor meer technische informatie over Logische Apps.

## <a name="what-is-azure-functions"></a>Wat is Azure Functions?

Azure Functions is een service voor het eenvoudig uitvoeren van code- of 'functies' in de cloud. U alleen de code schrijven die nodig is voor het huidige probleem, zonder u zorgen te maken over een hele app of de vereiste infrastructuur. Functies kunnen de ontwikkeling nog productiever maken en u uw ontwikkelingstaal naar keuze gebruiken, zoals C#, F#, Node.js, Python of PHP. U betaalt alleen voor de tijd dat uw code wordt uitgevoerd en Azure zo nodig wordt geschaald.

Als u aan de slag wilt met Azure-functies, begint u met [Uw eerste Azure-functie maken.](../azure-functions/functions-create-first-azure-function.md) Zie de [verwijzing naar](../azure-functions/functions-reference.md)de ontwikkelaar voor meer technische informatie over functies.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hoe kan ik serverloze apps bouwen en implementeren in Azure?

Azure biedt uitgebreide hulpprogramma's voor het ontwikkelen, implementeren en beheren van serverloze apps. U apps rechtstreeks bouwen in de Azure-portal, met [hulpprogramma's in Visual Studio](logic-apps-serverless-get-started-vs.md)of [Visual Studio Code.](quickstart-create-logic-apps-visual-studio-code.md) Nadat u uw app hebt gemaakt, u [die app snel implementeren met Azure Resource Manager-sjablonen.](logic-apps-deploy-azure-resource-manager-templates.md) Azure biedt ook bewaking, die u openen via de Azure-portal, via de API of SDK's of met geïntegreerde tooling voor Azure Monitor-logboeken en Toepassingsinzichten.

## <a name="next-steps"></a>Volgende stappen

* [Een serverloze app bouwen in Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Een dashboard voor klantinzichten maken met serverloos](logic-apps-scenario-social-serverless.md)
* [Implementatie van logische apps automatiseren](logic-apps-azure-resource-manager-templates-overview.md)
