---
title: Waarschuwingen configureren - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: In dit artikel wordt beschreven hoe u metrische waarschuwingen voor Azure Database voor PostgreSQL - Hyperscale (Citus) configureert en opent.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063150"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>De Azure-portal gebruiken om waarschuwingen in te stellen voor metrische gegevens voor Azure Database voor PostgreSQL - Hyperscale (Citus)

In dit artikel ziet u hoe u Azure Database instelt voor PostgreSQL-waarschuwingen met behulp van de Azure-portal. U een waarschuwing ontvangen op basis [van monitoringstatistieken](concepts-hyperscale-monitoring.md) voor uw Azure-services.

We stellen een waarschuwing in om te activeren wanneer de waarde van een opgegeven statistiek een drempelwaarde overschrijdt. De waarschuwing wordt geactiveerd wanneer de voorwaarde voor het eerst wordt voldaan en blijft daarna worden geactiveerd.

U een waarschuwing configureren om de volgende acties uit te voeren wanneer deze wordt geactiveerd:
* Stuur e-mailmeldingen naar de servicebeheerder en medebeheerders.
* Stuur e-mail naar aanvullende e-mails die u opgeeft.
* Bel een webhook.

U informatie over waarschuwingsregels configureren en ontvangen met:
* [Azure-portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure-CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Een waarschuwingsregel maken op een statistiek van de Azure-portal
1. Selecteer in de [Azure-portal](https://portal.azure.com/)de Azure Database voor PostgreSQL-server die u wilt controleren.

2. Selecteer onder de sectie **Controle** van de zijbalk de optie **Waarschuwingen** zoals weergegeven:

   ![Waarschuwingsregels selecteren](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selecteer **Nieuwe waarschuwingsregel** (+ pictogram).

4. De pagina **Regel maken** wordt geopend zoals hieronder wordt weergegeven. Vul de vereiste informatie in:

   ![Metriekwaarschuwingsformulier toevoegen](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Selecteer In de sectie **Voorwaarde** de optie **Toevoegen**.

6. Selecteer een statistiek in de lijst met signalen waarop u moet worden gewaarschuwd. Selecteer in dit voorbeeld 'Opslagpercentage'.
   
   ![Metrische waarde selecteren](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configureer de waarschuwingslogica:

    * **Operator** (ex. "Groter dan")
    * **Drempelwaarde** (ex. 85 procent)
    * **De hoeveelheid aggregatiemoet** aan de metrische regel worden voldaan voordat de waarschuwing wordt geactiveerd (ex. "In de afgelopen 30 minuten")
    * en **frequentie van de evaluatie** (bijvoorbeeld "1 minuut")
   
   Selecteer **Gereed** wanneer voltooid.

   ![Metrische waarde selecteren](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Selecteer **in** de sectie **Actiegroepen Nieuw maken** om een nieuwe groep te maken om meldingen op de waarschuwing te ontvangen.

9. Vul het formulier 'Actiegroep toevoegen' in met een naam, korte naam, abonnement en resourcegroep.

    ![Actiegroep](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Een **actietype E-mail/SMS/Push/Voice** configureren.
    
    Kies 'E-mail azure resource manager-rol' om meldingen te verzenden naar eigenaren van abonnementen, bijdragers en lezers.
   
    Selecteer **OK** wanneer deze is voltooid.

    ![Actiegroep](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Geef een naam van de waarschuwingsregel, beschrijving en ernst op.

    ![Actiegroep](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selecteer **Waarschuwingsregel maken** om de waarschuwing te maken.

    Binnen een paar minuten is de waarschuwing actief en wordt geactiveerd zoals eerder beschreven.

### <a name="managing-alerts"></a>Meldingen beheren

Nadat u een waarschuwing hebt gemaakt, u deze selecteren en de volgende acties uitvoeren:

* Bekijk een grafiek met de metrische drempel waarde en de werkelijke waarden van de vorige dag die relevant zijn voor deze waarschuwing.
* **De** waarschuwingsregel bewerken of **verwijderen.**
* **Schakel** de waarschuwing uit of schakel deze **in** als u meldingen tijdelijk wilt stoppen of hervatten.

## <a name="suggested-alerts"></a>Voorgestelde waarschuwingen

### <a name="disk-space"></a>Schijfruimte

Monitoring en alerting is belangrijk voor elke productie Hyperscale (Citus) servergroep. De onderliggende PostgreSQL-database vereist vrije schijfruimte om correct te werken. Als de schijf vol raakt, gaat het knooppunt van de databaseserver offline en weigert te starten totdat er ruimte beschikbaar is. Op dat moment is er een microsoft-ondersteuningsverzoek nodig om de situatie op te lossen.

We raden u aan schijfruimtewaarschuwingen in te stellen op elk knooppunt in elke servergroep, zelfs voor niet-productiegebruik. Waarschuwingen voor het gebruik van schijfruimte bieden de waarschuwing vooraf die nodig is om in te grijpen en knooppunten gezond te houden. Voor de beste resultaten, probeer een reeks waarschuwingen op 75%, 85% en 95% gebruik. De te kiezen percentages zijn afhankelijk van de innamesnelheid van gegevens, omdat de schijf sneller sneller wordt gevuld met snelle gegevensopname.

Als de schijf zijn ruimtelimiet nadert, probeert u deze technieken om meer vrije ruimte te krijgen:

* Bekijk het beleid voor het bewaren van gegevens. Verplaats oudere gegevens naar koude opslag indien mogelijk.
* Overweeg [knooppunten toe](howto-hyperscale-scaling.md#add-worker-nodes) te voegen aan de servergroep en shards opnieuw in evenwicht te brengen. Herbalancering verdeelt de gegevens over meer computers.
* Overweeg de capaciteit van werknemersknooppunten te [vergroten.](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) Elke werknemer kan maximaal 2 TiB opslagruimte hebben. Het toevoegen van knooppunten moet echter worden geprobeerd voordat het formaat van knooppunten wordt wijzigen, omdat het toevoegen van knooppunten sneller is voltooid.

### <a name="cpu-usage"></a>CPU-gebruik

Het monitoren van CPU-gebruik is handig om een basislijn voor prestaties vast te stellen. U bijvoorbeeld merken dat het CPU-gebruik meestal rond de 40-60% ligt. Als cpu-gebruik plotseling rond de 95% begint te zweven, u een afwijking herkennen. Het CPU-gebruik kan een weerspiegeling zijn van organische groei, maar het kan ook een verdwaalde query onthullen. Wanneer u een CPU-waarschuwing maakt, stelt u een lange aggregatiegranulariteit in om langdurige verhogingen te vangen en tijdelijke pieken te negeren.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het configureren van webhooks in waarschuwingen](../azure-monitor/platform/alerts-webhooks.md).
* Krijg een [overzicht van de verzameling statistieken](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) om ervoor te zorgen dat uw service beschikbaar en responsief is.
