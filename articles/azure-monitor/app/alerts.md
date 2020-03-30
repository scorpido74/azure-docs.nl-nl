---
title: Waarschuwingen instellen in Azure-toepassingsinzichten
description: Ontvang een melding over trage responstijden, uitzonderingen en andere prestatie- of gebruikswijzigingen in uw web-app.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295081"
---
# <a name="set-alerts-in-application-insights"></a>Waarschuwingen instellen in toepassingsstatistieken

[Azure Application Insights][start] kan u waarschuwen voor wijzigingen in de prestaties of gebruiksstatistieken in uw web-app. 

Application Insights bewaakt uw live-app op een [breed scala aan platforms][platforms] om u te helpen prestatieproblemen te diagnosticeren en gebruikspatronen te begrijpen.

Er zijn meerdere soorten waarschuwingen:

* [**Metrische waarschuwingen**](../../azure-monitor/platform/alerts-metric-overview.md) geven aan wanneer een statistiek een drempelwaarde voor een bepaalde periode kruist, zoals responstijden, uitzonderingsaantallen, CPU-gebruik of paginaweergaven.
* [**Logboekwaarschuwingen**](../../azure-monitor/platform/alerts-unified-log.md) worden gebruikt om waarschuwingen te beschrijven waarbij het waarschuwingssignaal is gebaseerd op een aangepaste Kusto-query.
* [**Webtests**][availability] geven aan wanneer uw site niet beschikbaar is op internet of langzaam reageert. [Meer informatie][availability].
* [**Proactieve diagnostiek**](../../azure-monitor/app/proactive-diagnostics.md) wordt automatisch geconfigureerd om u op de hoogte te stellen van ongebruikelijke prestatiepatronen.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Een uitzonderingswaarschuwing instellen met aangepaste logboekzoekopdrachten

In deze sectie gaan we door hoe u een op query gebaseerde uitzonderingswaarschuwing instellen. Stel dat we in dit voorbeeld een waarschuwing willen wanneer het mislukte percentage groter is dan 10% in de afgelopen 24 uur.

1. Ga naar uw Application Insight-bron in de Azure-portal.
2. Klik links onder Klik op **Waarschuwen**.

    ![Klik links onder klikwaarschuwing configureren](./media/alerts/1appinsightalert.png)

3. Selecteer boven aan het tabblad Waarschuwing de optie **Nieuwe waarschuwingsregel**.

     ![Klik boven aan het tabblad Waarschuwing op nieuwe waarschuwingsregel](./media/alerts/2createalert.png)

4. Uw resource moet automatisch worden geselecteerd. Als u een voorwaarde wilt instellen, klikt u op **Voorwaarde toevoegen**.

    ![Klik op Voorwaarde toevoegen](./media/alerts/3addcondition.png)

5. Selecteer op het tabblad Signaallogica configureren **Aangepaste logboekzoekopdrachten**

    ![Klik op aangepaste logboekzoekopdrachten](./media/alerts/4customlogsearch.png)

6. Voer op het aangepaste tabblad zoeken in logboeken uw query in het vak 'Zoeken' in. Voor dit voorbeeld gebruiken we de onderstaande Kusto-query.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Query typen in queryvak](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > U deze stappen ook toepassen op andere typen op query's gebaseerde waarschuwingen. U meer informatie over de Kusto query taal van deze [Kusto aan de slag doc](https://docs.microsoft.com/azure/kusto/concepts/) of deze SQL [kusto cheat sheet](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. Kies onder 'Waarschuwingslogica' of deze is gebaseerd op het aantal resultaten of metrische meting. Kies vervolgens de voorwaarde (groter dan, gelijk aan, minder dan) en een drempelwaarde. Terwijl u deze waarden wijzigt, ziet u mogelijk dat de zin van de voorwaardevoorbeeld wordt gewijzigd. In dit voorbeeld gebruiken we "gelijk aan".

    ![Typ onder Waarschuwingslogica uit de opties die zijn opgegeven op basis van en voorwaarde en typ vervolgens een drempelwaarde](./media/alerts/6alertlogic.png)

8. Stel onder 'Geëvalueerd op basis van' de periode en frequentie in. De periode hier moet overeenkomen met de waarde die we voor de periode in de bovenstaande query hebben geplaatst. Klik dan **op gereed**.

    ![Stel periode en frequentie onderaan in en klik op gereed](./media/alerts/7evaluate.png)

9. We zien nu de voorwaarde die we hebben gemaakt met de geschatte maandelijkse kosten. Hieronder onder ['Actiegroepen'](../platform/action-groups.md) u een nieuwe groep maken of een bestaande groep selecteren. Als u wilt, u de acties aanpassen.

    ![klik op de knoppen selecteren of maken onder actiegroep](./media/alerts/8actiongroup.png)

10. Voeg ten slotte uw waarschuwingsgegevens toe (naam van de waarschuwingsregel, beschrijving, ernst). Wanneer u klaar bent, klikt u onderaan op **Waarschuwingsregel maken.**

    ![Typ onder waarschuwingsdetail de naam van uw waarschuwingsregel, schrijf een beschrijving en kies een ernst](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Afmelden voor klassieke waarschuwingse-mailmeldingen

Deze sectie is van toepassing op **waarschuwingen voor klassieke beschikbaarheid,** **klassieke application insights-metrische waarschuwingen**en **waarschuwingen voor klassieke foutafwijkingen**.

U ontvangt e-mailberichten voor deze klassieke waarschuwingen als een van de volgende meldingen van toepassing is:

* Uw e-mailadres wordt weergegeven in het veld E-mailontvangers voor meldingen in de instellingen voor waarschuwingsregels.

* De optie om e-mailmeldingen te verzenden naar gebruikers met bepaalde rollen voor het abonnement wordt geactiveerd en u hebt een respectievelijke rol voor dat specifieke Azure-abonnement.

![Schermafbeelding van melding seinmelding](./media/alerts/alert-notification.png)

Om uw beveiliging en privacy beter te beheren, raden we u over het algemeen aan om de ontvangers van meldingen voor uw klassieke waarschuwingen expliciet op te geven in het veld **Ontvangers van meldingen.** De optie om alle gebruikers met bepaalde rollen op de hoogte te stellen, is bedoeld voor achterwaartse compatibiliteit.

Als u zich wilt afmelden voor e-mailberichten die zijn gegenereerd door een bepaalde waarschuwingsregel, verwijdert u uw e-mailadres uit het veld **Geadresseerden van meldingen.**

Als uw e-mailadres niet expliciet wordt vermeld, raden we u aan de optie uit te schakelen om alle leden automatisch op de hoogte te stellen van bepaalde rollen, en in plaats daarvan alle e-mails van gebruikers die meldingen voor die waarschuwingsregel moeten ontvangen, op te sommen in de e-mail van Kennisgeving het veld ontvangers.

## <a name="who-receives-the-classic-alert-notifications"></a>Wie ontvangt de (klassieke) waarschuwingsmeldingen?

Deze sectie is alleen van toepassing op klassieke waarschuwingen en helpt u uw waarschuwingsmeldingen te optimaliseren om ervoor te zorgen dat alleen uw gewenste ontvangers meldingen ontvangen. Voor meer informatie over het verschil tussen [klassieke waarschuwingen](../platform/alerts-classic.overview.md) en de nieuwe waarschuwingen ervaring, verwijzen naar de [waarschuwingen overzicht artikel](../platform/alerts-overview.md). Als u de melding wilt beheren in de nieuwe waarschuwingservaring, gebruikt u [actiegroepen](../platform/action-groups.md).

* We raden het gebruik van specifieke ontvangers aan voor klassieke waarschuwingsmeldingen.

* Voor waarschuwingen over statistieken van Application Insights (inclusief beschikbaarheidsstatistieken) wordt de optie **bulk/groep,** indien ingeschakeld, naar gebruikers met eigenaar, bijdrager of lezer in het abonnement verzendt. In feite zijn _alle_ gebruikers met toegang tot het abonnement de Application Insights-bron in het bereik en ontvangen ze meldingen.

> [!NOTE]
> Als u momenteel de optie **bulk/groep** gebruikt en uitschakelt, u de wijziging niet terugdraaien.

Gebruik de nieuwe waarschuwingservaring/bijna realtime waarschuwingen als u gebruikers op de hoogte moet stellen op basis van hun rollen. Met [actiegroepen](../platform/action-groups.md)u e-mailmeldingen configureren aan gebruikers met een van de rollen tussen de inzender/eigenaar/lezer (niet gecombineerd als één optie).

## <a name="automation"></a>Automation
* [PowerShell gebruiken om het instellen van waarschuwingen te automatiseren](../../azure-monitor/app/powershell-alerts.md)
* [Webhooks gebruiken om het reageren op waarschuwingen te automatiseren](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Zie ook
* [Webtests voor beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
* [Waarschuwingen voor het instellen automatiseren](../../azure-monitor/app/powershell-alerts.md)
* [Proactieve diagnoses](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

