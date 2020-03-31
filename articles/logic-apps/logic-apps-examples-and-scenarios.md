---
title: Voorbeelden & veelvoorkomende scenario's
description: Voorbeelden, algemene scenario's, zelfstudies en walkthroughs voor Azure Logic Apps zoeken
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164624"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Veelvoorkomende scenario's, voorbeelden, zelfstudies en walkthroughs voor Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) helpt u bij het orkestreren en integreren van verschillende services door [honderden kant-en-klare connectors aan te](../connectors/apis-list.md)bieden, variërend van on-premises SQL Server of SAP tot Azure Cognitive Services. De Logic Apps-service is 'serverloos', zodat u zich geen zorgen hoeft te maken over schaal of instanties. Het enige wat u hoeft te doen is de werkstroom definiëren met een trigger en de acties die de werkstroom uitvoert. Het onderliggende platform verwerkt schaal, beschikbaarheid en prestaties. Logic Apps is vooral handig voor use cases en scenario's waarin u acties voor meerdere systemen en services moet coördineren.

In dit artikel worden veelvoorkomende uitgangspunten, voorbeelden en scenario's beschreven om meer te weten te komen over de mogelijkheden en patronen die Azure Logic Apps ondersteunt.

## <a name="common-starting-points-for-logic-app-workflows"></a>Algemene uitgangspunten voor logische app-werkstromen

Elke logische app begint met een [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts)en slechts één trigger, die de werkstroom van uw logische app start en alle gegevens doorgeeft als onderdeel van die trigger. Sommige connectors bieden triggers, die in deze typen worden geleverd:

* *Polling triggers*: Controleert regelmatig een service eindpunt voor nieuwe gegevens. Wanneer er nieuwe gegevens bestaan, maakt en voert de trigger een nieuwe werkstroominstantie uit met de gegevens als invoer.

* *Push triggers*: Luistert naar gegevens op een service eindpunt en wacht tot een specifieke gebeurtenis plaatsvindt. Wanneer de gebeurtenis plaatsvindt, wordt de trigger onmiddellijk geactiveerd, waardoor een nieuwe werkstroominstantie wordt gemaakt en uitgevoerd die alle beschikbare gegevens als invoer gebruikt.

Hier volgen voorbeelden die veelgebruikte triggers beschrijven:

* *Polling* triggers:

  * [ **Met de recidieftrigger** ](../connectors/connectors-native-recurrence.md) u de begindatum en -tijd instellen plus de herhaling voor het afvuren van uw logica-app. U bijvoorbeeld de dagen van de week en de tijden van de dag selecteren voor het activeren van uw logische app. Zie deze onderwerpen voor meer informatie:<p>

    * [Terugkerende en geautomatiseerde taken, processen en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Zelfstudie: Geautomatiseerde, op planning gebaseerde terugkerende werkstromen maken met Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Met de trigger **Wanneer een e-mail wordt ontvangen,** kan uw logische app controleren op nieuwe e-mail van elke e-mailprovider die wordt ondersteund door Logic Apps, bijvoorbeeld [Office 365 Outlook,](../connectors/connectors-create-api-office365-outlook.md) [Gmail,](https://docs.microsoft.com/connectors/gmail/) [Outlook.com,](https://docs.microsoft.com/connectors/outlook/)enzovoort. Zie deze onderwerpen voor meer informatie:<p>

    * [Zelfstudie: Geautomatiseerde werkstromen op basis van goedkeuring maken met Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Zelfstudie: Taken automatiseren om e-mails te verwerken met Azure Logic Apps, Azure-functies en Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * De [ **HTTP-trigger** ](../connectors/connectors-native-http.md) kan een serviceeindpunt oproepen via HTTP of HTTPS. Zie [Werkstromen bellen, activeren of nesten met HTTP-eindpunten](../logic-apps/logic-apps-http-endpoint.md)voor meer informatie.

* *Push* triggers:

  * De [ **trigger van Aanvraag** ](../connectors/connectors-native-reqres.md) kan binnenkomende HTTPS-verzoeken ontvangen.

  * De [ **HTTP Webhook-trigger** ](../connectors/connectors-native-webhook.md) schrijft zich op een serviceeindpunt door een *terugbel-URL* met die service te registreren. Op die manier kan de service de trigger gewoon op de hoogte stellen wanneer de opgegeven gebeurtenis plaatsvindt, zodat de trigger de service niet hoeft te peilen.

Nadat de opgegeven gebeurtenis is uitgevoerd, wordt de trigger geactiveerd, waardoor een nieuwe logische app-werkstroominstantie wordt gemaakt en de acties in de werkstroom worden uitgevoerd. U hebt toegang tot alle gegevens van de trigger in de hele werkstroom. Bijvoorbeeld, de Twitter **Op een nieuwe tweet** trigger passeert de tweet inhoud in de logica app draaien. Probeer de volgende snelstartonderwerpen om aan de slag te gaan met Azure Logic Apps:

* [Snelstart: uw eerste geautomatiseerde werkstroom maken met Azure Logic Apps - Azure-portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Snelstart: geautomatiseerde taken, processen en werkstromen maken met Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Snelstart: geautomatiseerde logica-app-werkstromen maken en beheren met Behulp van Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Mogelijkheden voor stroom- en foutafhandeling regelen

Logische apps bevatten uitgebreide mogelijkheden voor geavanceerde besturingsstroom, zoals voorwaarden, switches, lussen en scopes. Om veerkrachtige oplossingen te garanderen, u ook fout- en uitzonderingsverwerking implementeren in uw werkstromen.

* Verschillende acties uitvoeren op basis [van voorwaardelijke instructies](../logic-apps/logic-apps-control-flow-conditional-statement.md) en van verklaring [wisselen](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Stappen herhalen of items in arrays en verzamelingen verwerken met lussen](../logic-apps/logic-apps-control-flow-loops.md)
* [Groepsacties samen met scopes](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Fout- en uitzonderingsafhandeling toevoegen aan een werkstroom](../logic-apps/logic-apps-exception-handling.md)
* [Use case: Hoe een zorgbedrijf logic app exception handling gebruikt voor HL7 FHIR-workflows](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Aangepaste API's en connectoren maken

Voor systemen en services die geen gepubliceerde connectors hebben, u ook logische apps uitbreiden.

* [Aangepaste API's maken om aan te bellen vanuit Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Controleer regelmatig op nieuwe gegevens of gebeurtenissen met behulp van het stemtriggerpatroon](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Wachten en luisteren naar nieuwe gegevens of gebeurtenissen met behulp van de webhook trigger patroon](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Langlopende taken uitvoeren met behulp van het actiepatroon polling](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Langlopende taken uitvoeren met behulp van het actiepatroon webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Aangepaste connectors in Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Bouw business-to-business (B2B) oplossingen

Voor oplossingen voor bedrijfsintegratie en naadloze communicatie tussen organisaties u geautomatiseerde schaalbare workflows voor deze scenario's bouwen met behulp van het Enterprise Integration Pack (EIP) met Azure Logic Apps. Hoewel organisaties verschillende protocollen en formaten gebruiken, kunnen ze berichten elektronisch uitwisselen. De EIP transformeert verschillende formaten in een formaat dat de systemen van uw organisatie kunnen verwerken en ondersteunt industriestandaard protocollen, waaronder AS2, X12, EDIFACT en RosettaNet. Als u deze oplossingen wilt bouwen, maakt u een integratieaccount, een afzonderlijke Azure-bron die een veilige, schaalbare en beheerbare container biedt voor de artefacten die u definieert en gebruikt met uw logische app-werkstromen. Artefacten omvatten bijvoorbeeld handelspartners, overeenkomsten, kaarten, schema's, certificaten en batchconfiguraties.

* [Overzicht: B2B-oplossingen voor bedrijfsintegratie met Azure Logic Apps en Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Integratieaccounts maken en beheren voor B2B-bedrijfsintegraties in Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Toegang tot virtuele azure-netwerkbronnen

Soms hebben uw logische apps en integratieaccounts toegang nodig tot beveiligde bronnen, zoals virtuele machines (VM's) en andere systemen of services die zich in een virtueel Azure-netwerk bevinden. Als u deze toegang wilt instellen, u een integratieserviceomgeving (ISE) maken waar u uw logische apps bouwen en uitvoeren. Een ISE is een privé- en geïsoleerd exemplaar van de Logic Apps-service die gebruikmaakt van speciale bronnen zoals opslag en afzonderlijk wordt uitgevoerd van de openbare, 'globale', logica-apps-service met meerdere tenant's. Het scheiden van uw geïsoleerde privé-instantie en de openbare globale instantie helpt ook de impact te verminderen die andere Azure-tenants kunnen hebben op de prestaties van uw apps, die ook wel het effect 'luidruchtige buren' worden genoemd.

* [Overzicht: Toegang tot virtuele Azure-netwerkbronnen vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Logische apps implementeren, beheren en bewaken

U logische apps volledig ontwikkelen en implementeren met Visual Studio, Azure DevOps of andere bronbeheer- en geautomatiseerde buildtools. Om implementatie voor werkstromen en afhankelijke verbindingen in een resourcesjabloon te ondersteunen, gebruiken logische apps Azure-bronimplementatiesjablonen. Visual Studio-hulpprogramma's genereren deze sjablonen automatisch, die u inchecken bij bronbeheer voor versiebeheer. Voor meldings- en diagnostische logboeken voor de status van werkstroombeheer biedt Azure Logic Apps ook bewaking en waarschuwingen.

### <a name="deploy"></a>Implementeren

* [Snelstart: geautomatiseerde taken, processen en werkstromen maken met Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Overzicht: Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager-sjablonen maken om de implementatie voor Azure Logic Apps te automatiseren](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sjablonen implementeren voor Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Voorbeeld: Verbinding maken met Azure Service Bus-wachtrijen vanuit Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Verbinding maken met Azure Storage-accounts vanuit Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Een functie-app-actie instellen voor Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Voorbeeld: Verbinding maken met een integratieaccount van Azure Logic Apps en implementeren met Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Voorbeeld: Azure-pijplijnen orkestreren met Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Beheren

* [Logische apps beheren met Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Integratieaccounts maken en beheren voor B2B-bedrijfsintegraties](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Ise (Integration Service Environment) beheren in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Controleren

* [Runstatus controleren, triggergeschiedenis controleren en waarschuwingen instellen voor Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor B2B-berichten in Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Query's weergeven en maken voor bewaking en tracking in Azure Monitor-logboeken voor Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Inhoudstypen, conversies en transformaties verwerken

U meerdere inhoudstypen openen, converteren en transformeren met behulp van de vele functies in de taal van de azure Logic [Apps-werkstroomdefinitie](https://aka.ms/logicappsdocs). U bijvoorbeeld converteren tussen een tekenreeks, JSON `@json()` `@xml()` en XML met de en werkstroomexpressies. De Logic Apps-engine behoudt inhoudstypen om inhoudsoverdracht op een verliesloze manier tussen services te ondersteunen.

* [Inhoudstypen verwerken in Azure](../logic-apps/logic-apps-content-type.md) `application/`Logic `application/octet-stream`Apps , zoals , en`multipart/formdata`
* [Naslaggids voor het gebruik van functies in expressies voor Azure Logic Apps en Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Taalschema voor werkstroomdefinitie voor Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Andere integraties en mogelijkheden

Azure Logic Apps integreert met veel services, zoals Azure-functies, Azure API Management, Azure App Service en aangepaste HTTP-eindpunten, bijvoorbeeld REST en SOAP.

* [Azure-functies aanroepen vanuit Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Zelfstudie: Logische apps bellen of activeren met Azure-functies en Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Zelfstudie: Een dashboard voor streaming klantinzichten maken met Azure Logic Apps en Azure-functies](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Zelfstudie: Een functie maken die integreert met Azure Logic Apps en Azure Cognitive Services om het sentiment onder twitterberichten te analyseren](../azure-functions/functions-twitter-email.md)
* [Zelfstudie: Een sociaal dashboard bouwen met Een AI-aangedreven sociaal dashboard met Power BI en Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Zelfstudie: Wijzigingen in virtuele machines controleren met Azure Event Grid en Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Zelfstudie: Bewaking en meldingen op afstand van IoT met Azure Logic Apps die uw IoT-hub en -postvak met elkaar verbinden](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: SOAP-services bellen met Azure Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>End-to-end scenario 's

* [Whitepaper: End-to-end casemanagement-integratie met Azure-services, zoals Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Verhalen van klanten

Ontdek hoe Azure Logic Apps, samen met andere Azure-services en Microsoft-producten, [deze bedrijven](https://aka.ms/logic-apps-customer-stories) heeft geholpen hun flexibiliteit te verbeteren en zich te concentreren op hun kernactiviteiten door complexe processen te vereenvoudigen, te organiseren, te automatiseren en te orkestreren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [connectors voor Logische Apps](../connectors/apis-list.md)
* Meer informatie over [B2B-bedrijfsintegratiescenario's met Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
