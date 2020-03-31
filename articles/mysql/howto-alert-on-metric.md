---
title: Metrische waarschuwingen configureren - Azure-portal - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u metrische waarschuwingen voor Azure Database voor MySQL configureert en opent vanuit de Azure-portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c917804b42bc987228bbb2542682fe9fb308a467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067878"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>De Azure-portal gebruiken om waarschuwingen in te stellen voor metrische gegevens voor Azure Database voor MySQL 

In dit artikel ziet u hoe u Azure Database voor MySQL-waarschuwingen instelt met behulp van de Azure-portal. U een waarschuwing ontvangen op basis van monitoringstatistieken voor uw Azure-services.

De waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven statistiek een drempelwaarde overschrijdt die u toewijst. De waarschuwing activeert zowel wanneer de voorwaarde voor het eerst wordt voldaan, en daarna wanneer die voorwaarde niet meer wordt voldaan. 

U een waarschuwing configureren om de volgende acties uit te voeren wanneer deze wordt geactiveerd:
* E-mailmeldingen verzenden naar de servicebeheerder en medebeheerders
* Stuur e-mail naar aanvullende e-mails die u opgeeft.
* Een webhook aanroepen

U informatie over waarschuwingsregels configureren en ontvangen met:
* [Azure-portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure-CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Een waarschuwingsregel maken op een statistiek van de Azure-portal
1. Selecteer in de [Azure-portal](https://portal.azure.com/)de Azure Database voor MySQL-server die u wilt controleren.

2. Selecteer onder de sectie **Controle** van de zijbalk de optie **Waarschuwingen** zoals weergegeven:

   ![Waarschuwingsregels selecteren](./media/howto-alert-on-metric/2-alert-rules.png)

3. Selecteer **Metrische waarschuwing toevoegen** (+ pictogram).

4. De pagina **Regel maken** wordt geopend zoals hieronder wordt weergegeven. Vul de vereiste informatie in:

   ![Metriekwaarschuwingsformulier toevoegen](./media/howto-alert-on-metric/4-add-rule-form.png)

5. Selecteer **voorwaarde toevoegen**in de sectie **Voorwaarde** .

6. Selecteer een statistiek in de lijst met signalen waarop u moet worden gewaarschuwd. Selecteer in dit voorbeeld 'Opslagpercentage'.
   
   ![Metrische waarde selecteren](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Configureer de waarschuwingslogica inclusief de **voorwaarde** (bijvoorbeeld. Drempel (hoger dan"), **drempel** (ex. 85 procent), **Time Aggregation**, **Period** of time the metric rule must be satisfied before the alert triggers (ex. "In de laatste 30 minuten"), en **Frequentie**.
   
   Selecteer **Gereed** wanneer voltooid.

   ![Metrische waarde selecteren](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. Selecteer **in** de sectie **Actiegroepen Nieuw maken** om een nieuwe groep te maken om meldingen op de waarschuwing te ontvangen.

9. Vul het formulier 'Actiegroep toevoegen' in met een naam, korte naam, abonnement en resourcegroep.

10. Een **actietype E-mail/SMS/Push/Voice** configureren.
    
    Kies 'E-mail azure resource manager-rol' om eigenaren van abonnementen, bijdragers en lezers te selecteren om meldingen te ontvangen.
   
    Geef eventueel een geldige URI op in het veld **Webhook** als u wilt dat deze wordt aangeroepen wanneer de waarschuwing wordt geactiveerd.

    Selecteer **OK** wanneer deze is voltooid.

    ![Actiegroep](./media/howto-alert-on-metric/10-action-group-type.png)

11. Geef een naam van de waarschuwingsregel, beschrijving en ernst op.

    ![Actiegroep](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Selecteer **Waarschuwingsregel maken** om de waarschuwing te maken.

    Binnen een paar minuten is de waarschuwing actief en wordt geactiveerd zoals eerder beschreven.

## <a name="manage-your-alerts"></a>De waarschuwingen beheren
Nadat u een waarschuwing hebt gemaakt, u deze selecteren en de volgende acties uitvoeren:

* Bekijk een grafiek met de metrische drempel waarde en de werkelijke waarden van de vorige dag die relevant zijn voor deze waarschuwing.
* **De** waarschuwingsregel bewerken of **verwijderen.**
* **Schakel** de waarschuwing uit of schakel deze **in** als u meldingen tijdelijk wilt stoppen of hervatten.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het configureren van webhooks in waarschuwingen](../azure-monitor/platform/alerts-webhooks.md).
* Krijg een [overzicht van de verzameling statistieken](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) om ervoor te zorgen dat uw service beschikbaar en responsief is.
