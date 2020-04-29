---
title: Waarschuwingen instellen in Azure-toepassing Insights
description: Ontvang een melding over trage reactie tijden, uitzonde ringen en andere wijzigingen in de prestaties of het gebruik van uw web-app.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295081"
---
# <a name="set-alerts-in-application-insights"></a>Waarschuwingen instellen in Application Insights

[Azure-toepassing Insights][start] kan u waarschuwen voor wijzigingen in de metrische gegevens voor prestaties of gebruik in uw web-app. 

Application Insights bewaakt uw Live-App op een [groot aantal verschillende platformen][platforms] om u te helpen bij het vaststellen van prestatie problemen en het begrijpen van gebruiks patronen.

Er zijn meerdere typen waarschuwingen:

* Met [**metrische waarschuwingen**](../../azure-monitor/platform/alerts-metric-overview.md) wordt u gewaarschuwd wanneer een metriek een drempel waarde voor een bepaalde periode kruist, zoals reactie tijden, uitzonderings aantallen, CPU-gebruik of pagina weergaven.
* [**Waarschuwingen**](../../azure-monitor/platform/alerts-unified-log.md) in het logboek worden gebruikt om waarschuwingen te beschrijven waarbij het waarschuwings signaal is gebaseerd op een aangepaste Kusto-query.
* [**Webtests**][availability] laten u weten wanneer uw site niet beschikbaar is op internet of langzaam reageert. [Meer informatie][availability].
* [**Proactieve diagnoses**](../../azure-monitor/app/proactive-diagnostics.md) worden automatisch geconfigureerd om u te informeren over ongebruikelijke prestatie patronen.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Een uitzonderings waarschuwing instellen met behulp van aangepaste zoek opdracht in Logboeken

In deze sectie wordt uitgelegd hoe u een uitzonderings waarschuwing op basis van een query kunt instellen. Voor dit voor beeld laten we weten dat er een waarschuwing moet worden weer geven wanneer het aantal mislukte aanvragen in de afgelopen 24 uur groter is dan 10%.

1. Ga in het Azure Portal naar de Insight-resource van uw toepassing.
2. Aan de linkerkant onder Klik op **waarschuwing**configureren.

    ![Aan de linkerkant onder Klik op waarschuwing configureren](./media/alerts/1appinsightalert.png)

3. Selecteer de **nieuwe waarschuwings regel**boven aan het tabblad waarschuwing.

     ![Klik boven aan het tabblad waarschuwing op nieuwe waarschuwings regel](./media/alerts/2createalert.png)

4. De resource moet automatisch worden geselecteerd. Klik op **voor waarde toevoegen**om een voor waarde in te stellen.

    ![Klik op voor waarde toevoegen](./media/alerts/3addcondition.png)

5. Selecteer in het tabblad signaal logica configureren de optie **aangepaste logboek zoekactie**

    ![Klik op aangepast logboek zoeken](./media/alerts/4customlogsearch.png)

6. Voer in het tabblad Aangepaste logboek zoek opdracht uw query in het vak Zoek query in. Voor dit voor beeld gebruiken we de onderstaande Kusto-query.
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

    ![Query type in zoek opdracht](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > U kunt deze stappen ook Toep assen op andere typen waarschuwingen op basis van query's. Meer informatie over de Kusto-query taal vindt u in deze [Kusto aan](https://docs.microsoft.com/azure/kusto/concepts/) de slag-doc of dit [SQL to Kusto Cheat-blad](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet) .

7. Kies onder ' waarschuwings logica ' of het is gebaseerd op het aantal resultaten of metrische meet waarden. Kies vervolgens de voor waarde (groter dan, gelijk aan, kleiner dan) en een drempel waarde. Wanneer u deze waarden wijzigt, is het mogelijk dat de preview-zin van de voor waarde wordt gewijzigd. In dit voor beeld gebruiken we ' is gelijk aan '.

    ![Kies onder waarschuwings logica de opties die zijn opgegeven voor op basis van en voor waarde en typ vervolgens een drempel waarde](./media/alerts/6alertlogic.png)

8. Stel bij ' geëvalueerd op basis van ' de periode en frequentie in. De periode die u hier opgeeft, moet overeenkomen met de waarde die we in de bovenstaande query hebben opgegeven voor de periode. Klik vervolgens op **gereed**.

    ![Stel onderaan de periode en frequentie in en klik vervolgens op gereed](./media/alerts/7evaluate.png)

9. Nu ziet u de voor waarde die we hebben gemaakt met de geschatte maandelijkse kosten. Onder [actie groepen](../platform/action-groups.md) kunt u een nieuwe groep maken of een bestaande selecteren. Als u wilt, kunt u de acties aanpassen.

    ![Klik op de knoppen selecteren of maken onder actie groep](./media/alerts/8actiongroup.png)

10. Voeg ten slotte uw waarschuwings details toe (naam, beschrijving, Ernst) van de waarschuwings regel. Wanneer u klaar bent, klikt u onderaan op **waarschuwings regel maken** .

    ![Typ onder Details van waarschuwing uw naam van de waarschuwings regel, schrijf een beschrijving en kies een Ernst](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>E-mail meldingen voor klassieke waarschuwingen afmelden

Deze sectie is van toepassing op waarschuwingen voor **klassieke Beschik baarheid**, **klassieke Application Insights metrische waarschuwingen**en **waarschuwingen voor klassieke fout afwijkingen**.

U ontvangt e-mail meldingen voor deze klassieke waarschuwingen als een van de volgende van toepassing is:

* Uw e-mail adres wordt weer gegeven in het veld ontvangers van e-mail berichten in de instellingen voor waarschuwings regels.

* De optie voor het verzenden van e-mail meldingen naar gebruikers die bepaalde rollen voor het abonnement hebben, wordt geactiveerd en u houdt een rol voor dat specifieke Azure-abonnement.

![Scherm opname van waarschuwings melding](./media/alerts/alert-notification.png)

Om uw veiligheid en privacy beter te beheren, wordt u aangeraden de ontvangers van meldingen voor uw klassieke waarschuwingen expliciet op te geven in het veld ontvangers van het **e-mail bericht** . De optie om alle gebruikers op de hoogte te stellen van bepaalde rollen is bedoeld voor achterwaartse compatibiliteit.

Als u zich wilt afmelden voor e-mail meldingen die door een bepaalde waarschuwings regel worden gegenereerd, verwijdert u uw e-mail adres uit het veld ontvangers van het e-mail **bericht**

Als uw e-mail adres niet expliciet wordt vermeld, raden we u aan om de optie om alle leden van bepaalde rollen automatisch te melden en in plaats daarvan alle gebruikers-e-mails weer te geven die meldingen voor die waarschuwings regel moeten ontvangen in het veld ontvangers van de meldings-e-mail.

## <a name="who-receives-the-classic-alert-notifications"></a>Wie ontvangt de (klassieke) waarschuwings meldingen?

Deze sectie is alleen van toepassing op klassieke waarschuwingen en helpt u bij het optimaliseren van uw waarschuwings meldingen om ervoor te zorgen dat alleen de gewenste ontvangers meldingen ontvangen. Raadpleeg het [artikel overzicht van waarschuwingen](../platform/alerts-overview.md)voor meer informatie over het verschil tussen [klassieke waarschuwingen](../platform/alerts-classic.overview.md) en de nieuwe waarschuwingen. Als u waarschuwings meldingen wilt beheren in de nieuwe waarschuwing, gebruikt u [actie groepen](../platform/action-groups.md).

* We raden u aan specifieke ontvangers te gebruiken voor klassieke waarschuwings meldingen.

* Voor waarschuwingen over Application Insights metrische gegevens (inclusief beschikbaarheids gegevens), de optie voor het selectie vakje voor **bulk/groep** , indien ingeschakeld, stuurt de gebruiker naar gebruikers met de rol van eigenaar, bijdrager of lezer in het abonnement. In feite hebben _alle_ gebruikers met toegang tot het abonnement de Application Insights-resource binnen bereik en ontvangen ze meldingen.

> [!NOTE]
> Als u momenteel de optie voor het selectie vakje **massaal/groep** gebruikt en deze functie uitschakelt, kunt u de wijziging niet meer ongedaan maken.

Gebruik de nieuwe waarschuwings ervaring/bijna realtime waarschuwingen als u gebruikers op basis van hun rollen wilt waarschuwen. Met [actie groepen](../platform/action-groups.md)kunt u e-mail meldingen configureren voor gebruikers met een van de rollen Inzender/eigenaar/lezer (niet gecombineerd als één optie).

## <a name="automation"></a>Automation
* [Power shell gebruiken voor het automatiseren van het instellen van waarschuwingen](../../azure-monitor/app/powershell-alerts.md)
* [Webhooks gebruiken om het reageren op waarschuwingen te automatiseren](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Zie ook
* [Webtests voor beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md)
* [Instellen van waarschuwingen automatiseren](../../azure-monitor/app/powershell-alerts.md)
* [Proactieve diagnoses](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

