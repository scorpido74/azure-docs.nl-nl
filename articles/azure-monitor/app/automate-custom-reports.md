---
title: Aangepaste rapporten automatiseren met Azure Application Insights-gegevens
description: Aangepaste dagelijkse/wekelijkse/maandelijkse rapporten automatiseren met Azure Application Insights-gegevens
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: d91595a863901fcc420611ac644c7856e74320dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655120"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Aangepaste rapporten automatiseren met Azure Application Insights-gegevens

Periodieke rapporten helpen een team op de hoogte te houden van hoe hun bedrijfskritieke services het doen. Ontwikkelaars, DevOps/SRE-teams en hun managers kunnen productief zijn met geautomatiseerde rapporten die betrouwbaar inzichten leveren zonder dat iedereen zich moet aanmelden bij de portal. Dergelijke rapporten kunnen ook helpen bij het identificeren van geleidelijke toename van latencies, belasting of uitval percentages die mogelijk geen waarschuwingsregels veroorzaken.

Elke onderneming heeft zijn unieke rapportagebehoeften, zoals: 

* Specifieke percentielaggregaties van metrische gegevens of aangepaste statistieken in een rapport.
* Hebben verschillende rapporten voor dagelijkse, wekelijkse en maandelijkse roll-ups van gegevens voor verschillende doelgroepen.
* Segmentatie op aangepaste kenmerken zoals regio of omgeving. 
* Groepeer sommige AI-resources samen in één rapport, zelfs als ze zich in verschillende abonnementen of resourcegroepen, enz.
* Afzonderlijke rapporten met gevoelige statistieken die naar selectieve doelgroepen worden verzonden.
* Rapporten aan belanghebbenden die mogelijk geen toegang hebben tot de portalbronnen.

> [!NOTE] 
> De wekelijkse Application Insights digest e-mail stond geen aanpassingen toe en wordt stopgezet ten gunste van de aangepaste opties die hieronder worden vermeld. De laatste wekelijkse samenvatting e-mail zal worden verzonden op 11 juni 2018. Configureer een van de volgende opties om vergelijkbare aangepaste rapporten te krijgen (gebruik de onderstaande query).

## <a name="to-automate-custom-report-emails"></a>Aangepaste rapporte-mails automatiseren

U [toepassingsinzichtengegevens programmatisch opvragen](https://dev.applicationinsights.io/) om aangepaste rapporten in een planning te genereren. Met de volgende opties u snel aan de slag:

* [Rapporten automatiseren met Microsoft Flow](automate-with-flow.md)
* [Rapporten automatiseren met Logic Apps](automate-with-logic-apps.md)
* Gebruik de [Azure-functiesjabloon 'Application](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) Insights scheduled digest' in het scenario Monitoring. Deze functie maakt gebruik van SendGrid om de e-mail te leveren. 

    ![Azure-functiesjabloon](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Voorbeeldquery voor een wekelijkse samenvattingse e-mail
In de volgende query wordt weergegeven dat u zich aansluiten bij meerdere gegevenssets voor een wekelijks e-mailachtige samenvatting. Pas het aan zoals vereist en gebruik het met een van de bovenstaande opties om een wekelijks rapport te automatiseren.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Plandigest-rapport voor toepassingsinzichten

1. Selecteer in de Azure-portal de optie **Een Resource** > **Compute** > **Function App maken**.

   ![Schermafbeelding van een schermafbeelding van de Azure Resource Function App](./media/automate-custom-reports/function-app-01.png)

2. Voer de juiste gegevens voor uw app in en selecteer _Maken_. (Application Insights _On_ is alleen vereist als u uw nieuwe functie-app wilt controleren met Application Insights)

   ![Schermafbeelding van een schermafbeelding van de app-instellingen van Azure Resource-functie](./media/automate-custom-reports/function-app-02.png)

3. Zodra de implementatie van uw nieuwe functie-app is voltooid, selecteert u **Ga naar resource**.

4. Selecteer **Nieuwe functie**.

   ![Een nieuwe functieschermafbeelding maken](./media/automate-custom-reports/function-app-03.png)

5. Selecteer de **_geplande samenvattingssjabloon Toepassingsinzichten_**.

     > [!NOTE]
     > Functie-apps worden standaard gemaakt met runtime-versie 2.x. U moet [azure-functies runtime versie](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1.x** targeten om de geplande samenvattingsjabloon application insights te gebruiken.  ![schermafbeelding van runtime](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Schermafbeelding van de sjabloon nieuwe functietoepassingsinzichten](./media/automate-custom-reports/function-app-04.png)

6. Voer een geschikt e-mailadres voor de ontvanger in voor uw rapport en selecteer **Maken**.

   ![Schermafbeelding functie-instellingen](./media/automate-custom-reports/function-app-05.png)

7. Selecteer de > **toepassingsinstellingen**van het **functie-app-platform.** > **Platform features**

    ![Schermafbeelding van Azure Function Application-instellingen](./media/automate-custom-reports/function-app-07.png)

8. Maak drie nieuwe toepassingsinstellingen ``AI_APP_ID``met ``AI_APP_KEY``de ``SendGridAPI``juiste bijbehorende waarden , en . Selecteer **Opslaan**.

     ![Schermafbeelding van de interface voor functie-integratie](./media/automate-custom-reports/function-app-08.png)
    
    (De AI_ waarden vindt u onder API Access voor de Application Insights Resource waarover u wilt rapporteren. Als u geen API-sleutel voor Application Insights hebt, is er de optie om **API-sleutel te maken**.)
    
   * AI_APP_ID = Toepassings-id
   * AI_APP_KEY = API-sleutel
   * SendGridAPI =SendGrid API-sleutel

     > [!NOTE]
     > Als je geen SendGrid-account hebt, kun je er een maken. De documentatie van SendGrid voor Azure-functies vindt [u hier.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid) Als u alleen een minimale uitleg wilt over het instellen van SendGrid en het genereren van een API-sleutel, wordt aan het einde van dit artikel een api-sleutel verstrekt. 

9. Selecteer **Integreren** en klik onder Uitvoer op **SendGrid ($return)**.

     ![Schermafbeelding van uitvoer](./media/automate-custom-reports/function-app-09.png)

10. Selecteer onder de **instelling SendGridAPI Key App**de nieuw gemaakte app-instelling voor **SendGridAPI**.

     ![Schermafbeelding van functie-app uitvoeren](./media/automate-custom-reports/function-app-010.png)

11. Voer de functie-app uit en test deze.

     ![Schermafbeelding van de test](./media/automate-custom-reports/function-app-11.png)

12. Controleer uw e-mail om te bevestigen dat het bericht is verzonden/ontvangen.

     ![Schermafbeelding van onderwerpregel e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid met Azure

Deze stappen zijn alleen van toepassing als u nog geen SendGrid-account hebt geconfigureerd.

1. Selecteer in de Azure-portal **Een bronzoekopdracht** maken voor **SendGrid-e-mailbezorging** > Klik op **> maken** en vul de sendgrid-specifieke maakinstructies in. 

     ![Schermafbeelding van SendGrid-bron maken](./media/automate-custom-reports/function-app-13.png)

2. Eenmaal gemaakt onder SendGrid-accounts selecteert u **Beheren**.

     ![Schermafbeelding van de api-toets instellingen](./media/automate-custom-reports/function-app-14.png)

3. Dit zal de site van SendGrid lanceren. Selecteer **API-sleutels** > **voor**instellingen .

     ![Schermafbeelding van API-sleutel-app maken en weergeven](./media/automate-custom-reports/function-app-15.png)

4. Maak een API-sleutel > kies **& weergave maken** (Bekijk de documentatie van SendGrid over beperkte toegang om te bepalen welk niveau van machtigingen geschikt is voor uw API-sleutel. Full Access wordt hier geselecteerd voor bijvoorbeeld doeleinden.)

   ![Schermafbeelding van volledige toegang](./media/automate-custom-reports/function-app-16.png)

5. Kopieer de hele sleutel, deze waarde is wat je nodig hebt in je functie-app-instellingen als de waarde voor SendGridAPI

   ![Schermafbeelding van API-sleutel kopiëren](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van [Analytics-query's](../../azure-monitor/log-query/get-started-queries.md).
* Meer informatie over [het programmatisch opvragen van Application Insights-gegevens](https://dev.applicationinsights.io/)
* Meer informatie over [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Meer informatie over [Microsoft Flow](https://ms.flow.microsoft.com).
