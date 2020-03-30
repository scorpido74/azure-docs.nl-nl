---
title: Azure-monitorlogboeken gebruiken met Azure Logic Apps en Power Automate
description: Ontdek hoe u Azure Logic Apps en Power Automate gebruiken om herhaalbare processen snel te automatiseren met behulp van de Azure Monitor-connector.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480008"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Azure Monitor Logs-connector voor Logische apps en Stroom
[Met Azure Logic Apps](/azure/logic-apps/) en [Power Automate](https://ms.flow.microsoft.com) u geautomatiseerde werkstromen maken met behulp van honderden acties voor verschillende services. Met de Azure Monitor Logs-connector u werkstromen maken die gegevens ophalen uit een log Analytics-werkruimte of een Application Insights-toepassing in Azure Monitor. In dit artikel worden de acties beschreven die bij de connector zijn opgenomen en wordt een walkthrough verstrekt om een werkstroom te bouwen met behulp van deze gegevens.

U bijvoorbeeld een logische app maken om Azure Monitor-logboekgegevens te gebruiken in een e-mailmelding van Office 365, een bug in Azure DevOps te maken of een Slack-bericht te plaatsen.  U een werkstroom activeren via een eenvoudig schema of van een actie in een verbonden service, zoals wanneer een e-mail of een tweet wordt ontvangen. 

## <a name="actions"></a>Acties
In de volgende tabel worden de acties beschreven die zijn opgenomen in de Azure Monitor Logs-connector. Met beide u een logboekquery uitvoeren op basis van een Log Analytics-werkruimte of toepassing Application Insights. Het verschil zit hem in de manier waarop de gegevens worden geretourneerd.

> [!NOTE]
> De Azure Monitor Logs-connector vervangt de [Azure Log Analytics-connector](https://docs.microsoft.com/connectors/azureloganalytics/) en de [Azure Application Insights-connector.](https://docs.microsoft.com/connectors/applicationinsights/) Deze connector biedt dezelfde functionaliteit als de andere en is de voorkeursmethode voor het uitvoeren van een query ten opzichte van een Log Analytics-werkruimte of een Application Insights-toepassing.


| Actie | Beschrijving |
|:---|:---|
| [Query- en lijstresultaten uitvoeren](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Retourneert elke rij als zijn eigen object. Gebruik deze actie wanneer u met elke rij afzonderlijk wilt werken in de rest van de werkstroom. De actie wordt meestal gevolgd door een [Voor elke activiteit](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Query- en en visualiseerresultaten uitvoeren](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Retourneert alle rijen in de resultaatset als één opgemaakt object. Gebruik deze actie wanneer u het resultaat dat is ingesteld in de rest van de werkstroom wilt gebruiken, zoals het verzenden van de resultaten in een e-mail.  |

## <a name="walkthroughs"></a>Rondleidingen
De volgende zelfstudies illustreren het gebruik van de Azure Monitor-connectors in Azure Logic Apps. U dit zelfde voorbeeld uitvoeren met Power Automate, het enige verschil is hoe u de eerste werkstroom maakt en deze uitvoert wanneer deze is voltooid. De configuratie van de werkstroom en acties is hetzelfde tussen beide. Zie [Een stroom maken van een sjabloon in Power Automate](https://docs.microsoft.com/power-automate/get-started-logic-template) om aan de slag te gaan.


### <a name="create-a-logic-app"></a>Een logische app maken

Ga naar **Logische apps** in de Azure-portal en klik op **Toevoegen**. Selecteer een **abonnementsgroep**, **resourcegroep**en **regio** om de nieuwe logische app op te slaan en geef deze vervolgens een unieke naam. U **de** instelling Log Analytics inschakelen om informatie te verzamelen over runtime-gegevens en -gebeurtenissen zoals beschreven in [Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps.](../../logic-apps/monitor-logic-apps-log-analytics.md) Deze instelling is niet vereist voor het gebruik van de Azure Monitor Logs-connector.

![Logische app maken](media/logicapp-flow-connector/create-logic-app.png)


Klik **op Controleren + maken** en maak vervolgens **.** Wanneer de implementatie is voltooid, klikt u op **Ga naar resource om** de Logic Apps Designer te **openen.**

### <a name="create-a-trigger-for-the-logic-app"></a>Een trigger maken voor de logische app
Selecteer **Recidief**onder **Start met een algemene trigger**. Hiermee wordt een logische app gemaakt die automatisch wordt uitgevoerd met een regelmatig interval. Selecteer **in** het vak Frequentie van de actie **Day** en voer in het vak **Interval** **1** in om de werkstroom eenmaal per dag uit te voeren.

![Herhalingsactie](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Walkthrough: Gevisualiseerde resultaten mailen
In de volgende zelfstudie ziet u hoe u een logische app maakt die de resultaten van een Azure Monitor-logboekquery per e-mail verzendt. 

### <a name="add-azure-monitor-logs-action"></a>Azure Monitor-logboeken toevoegen: actie
Klik **op + Nieuwe stap** om een actie toe te voegen die wordt uitgevoerd na de herhalingsactie. Typ **azure-monitor** onder **Een actie kiezen**en selecteer Azure Monitor **Logs**.

![Azure Monitor-logboeken, actie](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Klik op **Azure Log Analytics – Query uitvoeren en resultaten visualiseren.**

![Query uitvoeren en resultatenactiviteit visualiseren](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Azure Monitor-logboeken toevoegen: actie

Selecteer de **abonnements-** en **brongroep** voor uw werkruimte Log Analytics. Selecteer *Log Analytics Workspace* voor het **resourcetype** en selecteer vervolgens de naam van de werkruimte onder **Resourcenaam**.

Voeg de volgende logboekquery toe aan het **venster Query.**  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selecteer *Instellen in query* voor het **tijdbereik** en de **HTML-tabel** voor het **grafiektype**.
   
![Query uitvoeren en resultatenactiviteit visualiseren](media/logicapp-flow-connector/run-query-visualize-action.png)

De e-mail wordt verzonden door het account dat is gekoppeld aan de huidige verbinding. U een ander account opgeven door op **Verbinding wijzigen**te klikken.

### <a name="add-email-action"></a>E-mailactie toevoegen

Klik **op + Nieuwe stap**en klik vervolgens op + Een actie **toevoegen**. Typ **outlook** **onder Een actie kiezen**en selecteer Office **365 Outlook**.

![Outlook-connector selecteren](media/logicapp-flow-connector/select-outlook-connector.png)

Selecteer **Een e-mail verzenden (V2)**.

![Office 365 Outlook-selectievenster](media/logicapp-flow-connector/select-mail-action.png)

Klik ergens in het vak **Hoofdtekst** om een **dynamisch inhoudsvenster** te openen dat wordt geopend met waarden uit de vorige acties in de logische app. Selecteer **Meer weergeven** en vervolgens **Hoofdtekst,** de resultaten van de query in de actie Log Analytics.

![Hoofdtekst selecteren](media/logicapp-flow-connector/select-body.png)

Geef het e-mailadres van een ontvanger op in het venster **Aan** en een onderwerp voor de e-mail in **Onderwerp**. 

![E-mailactie](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Uw logische app opslaan en testen
Klik **op Opslaan** en voer **deze** uit om een testrun van de logische app uit te voeren.

![Opslaan en uitvoeren](media/logicapp-flow-connector/save-run.png)


Wanneer de logische app is voltooid, controleert u de e-mail van de opgegeven ontvanger.  U had een e-mail moeten ontvangen met een lichaam dat vergelijkbaar is met het volgende:

![Voorbeeld e-mail](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboekquery's in Azure Monitor](../log-query/log-query-overview.md).
- Meer informatie over [Logische Apps](/azure/logic-apps/)
- Meer informatie over [Microsoft Flow](https://ms.flow.microsoft.com).

