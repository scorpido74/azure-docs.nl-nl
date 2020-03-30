---
title: Azure Application Insights-processen automatiseren met Logic Apps
description: Ontdek hoe u herhaalbare processen snel automatiseren door de Application Insights-connector toe te voegen aan uw logische app.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 4a0944e661932d86fac75f78c4faf5be751806c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473163"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatiseer Application Insights-processen met Logic Apps

Merkt u dat u herhaaldelijk dezelfde query's uitvoert op uw telemetriegegevens om te controleren of uw service naar behoren functioneert? Bent u op zoek naar deze query's te automatiseren voor het vinden van trends en afwijkingen en vervolgens bouwen van uw eigen workflows om hen heen? De Azure Application Insights-connector voor Logic Apps is hiervoor het juiste hulpmiddel.

> [!NOTE]
> De Azure Application Insights-connector is vervangen door de [Azure Monitor-connector](../platform/logicapp-flow-connector.md) die is geïntegreerd met Azure Active Directory in plaats van een API-sleutel te vereisen en u ook gegevens ophalen uit een Log Analytics-werkruimte.

Met deze integratie u tal van processen automatiseren zonder één regel code te schrijven. U een logische app maken met de Application Insights-connector om elk Application Insights-proces snel te automatiseren. 

U ook extra acties toevoegen. Met de functie Logic Apps van Azure App Service zijn honderden acties beschikbaar. Door bijvoorbeeld een logische app te gebruiken, u automatisch een e-mailmelding verzenden of een bug maken in Azure DevOps. U ook een van de vele beschikbare [sjablonen](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) gebruiken om het proces van het maken van uw logische app te versnellen. 

## <a name="create-a-logic-app-for-application-insights"></a>Een logische app maken voor Toepassingsinzichten

In deze zelfstudie leert u hoe u een logische app maakt waarmee het algoritme van Analytics-autocluster wordt gebruikt om kenmerken in de gegevens voor een webtoepassing te groeperen. De stroom stuurt de resultaten automatisch per e-mail, slechts één voorbeeld van hoe u Application Insights Analytics en Logic Apps samen gebruiken. 

### <a name="step-1-create-a-logic-app"></a>Stap 1: Een logische app maken
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik **op Een resource maken,** selecteer Web + **Mobiel**en selecteer vervolgens **Logic App**.

    ![Venster Nieuwe logische app](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Stap 2: Een trigger maken voor uw logica-app
1. Selecteer **Recidief**in het venster **Logic App Designer** onder Start met een algemene **trigger**.

    ![Logica App Designer-venster](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Typ **1 in** het vak Interval **en** selecteer **Day**in het vak Interval 1 en vervolgens**Frequentie.**

    ![Logica App Designer "Herhaling" venster](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Stap 3: Een actie Toepassingsinzichten toevoegen
1. Klik **op Nieuwe stap**.

1. Typ **Azure Application Insights**in het zoekvak Een **actie** kiezen .

1. Klik **onder Acties**op Azure Application Insights - Query Analytics **visualiseren**.

    ![Logica App Designer "Kies een actie" venster](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Stap 4: Verbinding maken met een Application Insights-bron

Om deze stap te voltooien, hebt u een toepassings-id en een API-sleutel voor uw bron nodig. U ze ophalen uit de Azure-portal, zoals in het volgende diagram wordt weergegeven:

![Toepassings-id in de Azure-portal](./media/automate-with-logic-apps/5apiaccess.png)

![Toepassings-id in de Azure-portal](./media/automate-with-logic-apps/6apikey.png)

Geef een naam op voor uw verbinding, de toepassings-id en de API-sleutel.

![Verbindingsvenster logic App Designer-stroom](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Stap 5: Het query- en grafiektype analytics opgeven
In het volgende voorbeeld selecteert de query de mislukte aanvragen van de laatste dag en correleert deze met uitzonderingen die zijn opgetreden als onderdeel van de bewerking. Analytics correleert de mislukte aanvragen op basis van de operation_Id-id. De query segmentert vervolgens de resultaten met behulp van het autoclusteralgoritme. 

Wanneer u uw eigen query's maakt, controleert u of ze goed werken in Analytics voordat u deze toevoegt aan uw stroom.

1. Voeg in het vak **Query** de volgende Analytics-query toe:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. Selecteer **html-tabel**in het vak **Grafiektype** .

    ![Configuratievenster van Analytics-query's](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Stap 6: De logische app configureren om e-mail te verzenden

1. Klik **op Nieuwe stap**.

1. Typ **Office 365 Outlook**in het zoekvak .

1. Klik **op Office 365 Outlook - Een e-mail verzenden**.

    ![Office 365 Outlook-selectie](./media/automate-with-logic-apps/9sendemail.png)

1. Ga als volgt te werk in **het venster Een e-mail verzenden:**

   a. Typ het e-mailadres van de ontvanger.

   b. Typ een onderwerp voor de e-mail.

   c. Klik ergens in het vak **Hoofd tekst** en selecteer vervolgens in het menu dynamische inhoud dat rechts wordt **geopend, Lichaam**.
    
   d. Klik **op** de vervolgkeuzelijst Nieuwe parameter toevoegen en selecteer Bijlagen en IS HTML.

      ![Office 365 Outlook-configuratie](./media/automate-with-logic-apps/10emailbody.png)

      ![Office 365 Outlook-configuratie](./media/automate-with-logic-apps/11emailparameter.png)

1. Ga als volgt te werk in het menu dynamische inhoud:

    a. Selecteer **Naam van bijlage**.

    b. Selecteer **Inhoud van bijlage**.
    
    c. Selecteer **Ja**in het vak **IS HTML** .

      ![Scherm voor configuratie van office 365-e-mail](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Stap 7: Uw logica-app opslaan en testen
* Klik **op Opslaan** om de wijzigingen op te slaan.

U wachten tot de trigger de logische app uitvoert, of u de logische app onmiddellijk uitvoeren door **Uitvoeren te**selecteren.

![Scherm logische app maken](./media/automate-with-logic-apps/13save.png)

Wanneer uw logische app wordt uitgevoerd, ontvangen de geadresseerden die u in de e-maillijst hebt opgegeven, een e-mail die er als volgt uitziet:

![E-mailbericht van logische app](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van [Analytics-query's](../../azure-monitor/log-query/get-started-queries.md).
- Meer informatie over [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





