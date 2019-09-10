---
title: Overzicht-zonder Azure server
description: Maak krachtige oplossingen in de Cloud zonder dat u zich zorgen hoeft te maken over de infra structuur
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 363002712bdd06e74360de9af186f5a458a4999d
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "68385353"
---
# <a name="overview-azure-serverless-with-azure-logic-apps-and-azure-functions"></a>Overzicht: Azure server zonder Azure Logic Apps en Azure Functions

[Serverloze](https://azure.microsoft.com/solutions/serverless/) apps bieden voor delen zoals verhoogde ontwikkelings snelheid, gereduceerde code, eenvoud en schaal. In dit artikel worden de verschillende kenmerken van serverloze oplossingen en Azure serverloze aanbiedingen besproken.

## <a name="what-is-serverless"></a>Wat is serverloze?

Serverloos betekent niet dat er geen servers zijn, maar dat ontwikkel aars geen zorgen hoeven te maken over servers. Een groot deel van traditionele toepassings ontwikkeling is het beantwoorden van vragen over schaal-, hosting-en bewakings oplossingen om te voldoen aan de vereisten van de toepassing. Met serverloze worden deze vragen gezien als onderdeel van de oplossing. Daarnaast worden serverloze apps gefactureerd op basis van een op verbruik gebaseerd abonnement. Als de app nooit wordt gebruikt, worden er geen kosten in rekening gebracht. Deze functies helpen ontwikkel aars zich alleen te concentreren op de bedrijfs logica van een oplossing.

De basis-Azure-Services voor serverloze zijn [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) en [Azure functions](https://azure.microsoft.com/services/functions/). Beide oplossingen volgen de eerder beschreven principes en helpen ontwikkel aars om robuuste Cloud-apps met minimale code te bouwen.

## <a name="what-is-azure-logic-apps"></a>Wat is Azure Logic Apps?

[Azure Logic apps](logic-apps-overview.md) biedt een manier om schaal bare integraties en werk stromen in de cloud te vereenvoudigen en te implementeren. Deze service biedt een visuele ontwerper om uw proces te model leren en te automatiseren als een reeks stappen die een werk stroom wordt genoemd. Er zijn veel [connectors](../connectors/apis-list.md) in Cloud Services en on-premises systemen waarmee snel een serverloze app kan worden verbonden met andere api's. Elke logische app begint met een trigger, bijvoorbeeld ' wanneer een account wordt toegevoegd aan Dynamics CRM '. Nadat de trigger is geactiveerd, kan de werk stroom combi Naties van acties, conversies en voorwaardelijke logica uitvoeren. Logic Apps is een fantastische keuze bij het indelen van verschillende Azure Functions in een proces, met name wanneer het proces interactie met een extern systeem of API vereist.

Begin met [het maken van uw eerste logische app](quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan met Logic apps. Zie voor meer technische informatie over Logic Apps de [naslag voor ontwikkel aars](logic-apps-workflow-definition-language.md).

## <a name="what-is-azure-functions"></a>Wat is Azure Functions?

Azure Functions is een service waarmee u eenvoudig stukjes code of ' functions ' in de cloud kunt uitvoeren. U kunt alleen de code schrijven die nodig is voor het huidige probleem, zonder dat u zich zorgen hoeft te maken over een volledige app of de vereiste infra structuur. Met functies kunnen ontwikkel aars nog productiever worden, en kunt u uw gewenste programmeer taal gebruiken, zoals C#, F#node. js, python of php. U betaalt alleen voor de tijd dat uw code wordt uitgevoerd en Azure wordt zo nodig geschaald.

Begin met [het maken van uw eerste Azure-functie](../azure-functions/functions-create-first-azure-function.md)om aan de slag te gaan met Azure functions. Zie Naslag informatie voor [ontwikkel aars](../azure-functions/functions-reference.md)voor meer technische gegevens over functies.

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>Hoe kan ik serverloze apps bouwen en implementeren in azure?

Azure biedt uitgebreide hulp middelen voor het ontwikkelen, implementeren en beheren van serverloze apps. U kunt apps rechtstreeks bouwen in de Azure Portal, met [hulpprogram ma's in Visual Studio](logic-apps-serverless-get-started-vs.md)of [Visual Studio code](quickstart-create-logic-apps-visual-studio-code.md). Nadat u uw app hebt gemaakt, kunt u [die app snel implementeren met Azure Resource Manager sjablonen](logic-apps-deploy-azure-resource-manager-templates.md). Azure biedt ook bewaking, die u via de Azure Portal kunt openen, via de API of Sdk's, of met geïntegreerde hulp middelen voor Azure Monitor logboeken en Application Insights.

## <a name="next-steps"></a>Volgende stappen

* [Een serverloze app maken in Visual Studio](logic-apps-serverless-get-started-vs.md)
* [Een dash board voor klanten inzichten maken met serverloos](logic-apps-scenario-social-serverless.md)
* [Implementatie van logische apps automatiseren](logic-apps-azure-resource-manager-templates-overview.md)
