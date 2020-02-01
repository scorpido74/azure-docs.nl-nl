---
title: Voor beelden & algemene scenario's
description: Hier vindt u voor beelden, veelvoorkomende scenario's, zelf studies en scenario's voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 5b72ee02c2bbf811293a2bcdb15590e16e300a02
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906684"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Veelvoorkomende scenario's, voor beelden, zelf studies en scenario's voor Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) helpt u bij het organiseren en integreren van verschillende services door [honderden kant-en-klare connectors](../connectors/apis-list.md)te bieden, variërend van on-premises SQL Server of SAP naar Azure Cognitive Services. De Logic Apps-service is ' serverloos ', dus u hoeft zich geen zorgen te maken over schalen of instanties. U hoeft alleen de werk stroom te definiëren met een trigger en de acties die de werk stroom uitvoert. Het onderliggende platform zorgt voor schaal, Beschik baarheid en prestaties. Logic Apps is vooral nuttig voor gebruik van gevallen en scenario's waarbij u meerdere acties op meerdere systemen moet coördineren.

Hier volgen enkele algemene voor beelden en scenario's voor meer informatie over de vele patronen en mogelijkheden die Azure Logic Apps ondersteunt.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Populaire start punten voor logische app-werk stromen

Elke logische app begint met een [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts)en slechts één trigger, waarmee de werk stroom van de logische app wordt gestart en de gegevens worden door gegeven als onderdeel van deze trigger. Sommige connectors bieden triggers die in de volgende typen voor komen:

* *Polling triggers*: controleert regel matig een service-eind punt op nieuwe gegevens. Wanneer er nieuwe gegevens bestaan, maakt en voert de trigger een nieuwe werk stroom instantie met de gegevens als invoer.

* *Push triggers*: luistert naar gegevens op een service-eind punt en wacht totdat er een bepaalde gebeurtenis plaatsvindt. Wanneer de gebeurtenis plaatsvindt, wordt de trigger onmiddellijk geactiveerd, wordt er een nieuwe werk stroom instantie gemaakt en uitgevoerd die beschik bare gegevens als invoer gebruikt.

Hier volgen enkele veelvoorkomende voor beelden van triggers:

* Polling

  * Met de trigger voor [ **terugkeer patroon** ](../connectors/connectors-native-recurrence.md) kunt u de begin datum en-tijd en het terugkeer patroon instellen voor het activeren van uw logische app. U kunt bijvoorbeeld de dagen van de week en tijdstippen van de dag selecteren voor het activeren van uw logische app. Zie de volgende onderwerpen voor meer informatie:

    * [Terugkerende geautomatiseerde taken, processen en werk stromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Zelf studie: verkeer controleren volgens een schema met Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * De trigger wanneer een e-mail wordt ontvangen, laat uw logische app controleren op nieuwe e-mail van elke e-mail provider die wordt ondersteund door Logic Apps, bijvoorbeeld [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/), enzovoort. Zie de volgende onderwerpen voor meer informatie: 

    * [Zelf studie: aanvragen voor adressen lijsten beheren met Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Zelf studie: het verwerken van e-mails en bijlagen met Azure Logic Apps automatiseren](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Met de [ **http-** trigger](../connectors/connectors-native-http.md) kan uw logische app een opgegeven service-eind punt controleren door te communiceren via http.
  
* Push

  * Met de [ **aanvraag** trigger](../connectors/connectors-native-reqres.md) kan uw logische app HTTP-aanvragen ontvangen en in real-time reageren op gebeurtenissen op een of andere manier.

  * De [ **http-webhook** trigger](../connectors/connectors-native-webhook.md) meldt zich aan bij een service-eind punt door een *call back-URL* bij die service te registreren. Op die manier kan de service alleen een melding sturen wanneer de opgegeven gebeurtenis plaatsvindt, zodat de trigger de service niet hoeft te pollen.

Nadat er een melding over nieuwe gegevens of een gebeurtenis is ontvangen, wordt de trigger geactiveerd, wordt er een nieuw exemplaar van de werk stroom voor de logische app gemaakt en worden de acties in de werk stroom uitgevoerd. U kunt toegang krijgen tot alle gegevens van de trigger in de werk stroom. Met de trigger ' on a New Tweet ' wordt de Tweet-inhoud bijvoorbeeld door gegeven aan de uitvoering van de logische app. Voer de volgende Snelstartgids uit om aan de slag te gaan met Azure Logic Apps:

* [Snelstartgids: uw eerste geautomatiseerde werk stroom maken met Azure Logic Apps in het Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Snelstartgids: geautomatiseerde taken, processen en werk stromen maken met Azure Logic Apps met behulp van Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Snelstartgids: geautomatiseerde werk stromen voor logische apps maken en beheren met Visual Studio code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Reageren op Triggers en acties uitbreiden

Voor systemen en services die mogelijk geen connectors hebben gepubliceerd, kunt u logische apps ook uitbreiden.

* [Aangepaste triggers of acties maken](../logic-apps/logic-apps-create-api-app.md)
* [Langlopende acties voor werk stroom uitvoeringen instellen](../logic-apps/logic-apps-create-api-app.md)
* [Reageren op externe gebeurtenissen en acties met webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Werk stromen aanroepen, activeren of nesten met synchrone reacties op HTTP-aanvragen](../logic-apps/logic-apps-http-endpoint.md)
* [Zelf studie: bouw binnen enkele minuten een sociaal dash board met Logic Apps en Power BI](https://aka.ms/logicappsdemo)
* [Video: reageren op Twilio SMS-webhooks en een tekst antwoord verzenden](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Mogelijkheden voor controle stroom, fout afhandeling en logboek registratie

Logic apps bevatten uitgebreide mogelijkheden voor geavanceerde controle stromen, zoals voor waarden, switches, lussen en bereiken. Voor een robuuste oplossing kunt u ook fout-en uitzonderings afhandeling in uw werk stromen implementeren. Voor meldings-en Diagnostische logboeken voor de uitvoerings status van de werk stroom biedt Azure Logic Apps ook bewaking en waarschuwingen.

* Verschillende acties uitvoeren op basis van [voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md) en [Switch instructies](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Herhaal de stappen of verwerk items in matrices en verzamelingen met lussen](../logic-apps/logic-apps-control-flow-loops.md)
* [Acties groeperen in combi natie met scopes](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Verwerking van fouten en uitzonde ringen in een werk stroom](../logic-apps/logic-apps-exception-handling.md)
* [Use-case: hoe een gezondheids zorg die gebruikmaakt van de verwerking van logische-app-uitzonde ringen voor HL7 FHIR-werk stromen](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Bewaking, logboek registratie en waarschuwingen voor bestaande Logic apps inschakelen](../logic-apps/monitor-logic-apps.md)
* [Bewakings-en diagnostische logboek registratie inschakelen bij het maken van logische apps](../logic-apps/monitor-logic-apps-log-analytics.md)

## <a name="deploy-and-manage-logic-apps"></a>Logic Apps implementeren en beheren

U kunt Logic apps volledig ontwikkelen en implementeren met Visual Studio, Azure DevOps of een ander broncode beheer en hulpprogram ma's voor automatisch bouwen. Voor de ondersteuning van de implementatie van werk stromen en afhankelijke verbindingen in een resource sjabloon maakt Logic apps gebruik van Azure resource-implementatie sjablonen. Visual Studio-hulpprogram ma's genereren deze sjablonen automatisch, die u in broncode beheer kunt inchecken voor het maken van versies.

* [Logische apps maken en implementeren met Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Bewaking, logboek registratie en waarschuwingen voor bestaande Logic apps inschakelen](../logic-apps/monitor-logic-apps.md)
* [Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Voor beeld: verbinding maken met Azure Service Bus wacht rijen van Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voor beeld: verbinding maken met Azure Storage accounts vanuit Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voor beeld: een actie van een functie-app instellen voor Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voor beeld: verbinding maken met een integratie account van Azure Logic Apps en implementeren met Azure-pijp lijnen in azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Inhouds typen, conversies en trans formaties binnen een uitvoering

U kunt meerdere inhouds typen openen, converteren en transformeren met behulp van de vele functies in de [werk stroom definitie taal](https://aka.ms/logicappsdocs)van Azure Logic apps. U kunt bijvoorbeeld converteren tussen een teken reeks, JSON en XML met de `@json()` en `@xml()` werk stroom expressies. De Logic Apps-Engine behoudt inhouds typen voor de ondersteuning van inhouds overdracht met een verlies vrije manier tussen services.

* [Werk stroom expressies gebruiken in Logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Niet-JSON-inhouds typen verwerken](../logic-apps/logic-apps-content-type.md), zoals `application/xml`, `application/octet-stream`en `multipart/formdata`
* [Schema voor werk stroom definitie taal voor Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Andere integraties en mogelijkheden

Logic apps bieden ook integratie met veel services, zoals Azure Functions, Azure API Management, Azure-app Services en aangepaste HTTP-eind punten, bijvoorbeeld REST en SOAP.

* [Een real-time sociaal dash board maken met Azure Serverloos](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Azure Functions van Logic apps aanroepen](../logic-apps/logic-apps-azure-functions.md)
* [Zelf studie: logische Apps activeren met Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Zelf studie: wijzigingen van virtuele machines bewaken met Azure Event Grid en Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Zelf studie: een functie maken die kan worden geïntegreerd met Azure Logic Apps en Azure Cognitive Services voor het analyseren van Twitter post sentiment](../azure-functions/functions-twitter-email.md)
* [Zelf studie: externe bewaking van IoT en meldingen met Azure Logic Apps verbinding maken met uw IoT hub en Postvak](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: SOAP-eind punten aanroepen vanuit Logic apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>End-to-end scenario 's

* [Technisch document: end-to-end case management-integratie met Azure-Services, zoals Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Klantverhalen

Lees hoe Azure Logic Apps, samen met andere Azure-Services en micro soft-producten, de flexibiliteit van [deze bedrijven](https://aka.ms/logic-apps-customer-stories) kan verbeteren en zich kan richten op hun kern bedrijven door complexe processen te vereenvoudigen, te organiseren, te automatiseren en te beheren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connectors voor Logic apps](../connectors/apis-list.md)
* Meer informatie over [B2B Enter prise Integration-scenario's met Azure Logic apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
