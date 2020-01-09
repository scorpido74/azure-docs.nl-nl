---
title: Azure-toepassing Insights-processen automatiseren met Microsoft Flow
description: Meer informatie over hoe u Microsoft Flow kunt gebruiken om Herhaal bare processen snel te automatiseren met behulp van de Application Insights-connector.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: 7d9c966051c2756d7936fa079a0aac68e694425d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407548"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Azure-toepassing Insights-processen automatiseren met de connector voor Microsoft Flow

Vindt u het herhaaldelijk om dezelfde query's op uw telemetriegegevens uit te voeren om te controleren of uw service goed werkt? Wilt u deze query's automatiseren om trends en afwijkingen op te sporen en vervolgens uw eigen werk stromen rond hen te bouwen? De Azure-toepassing Insights-connector voor Microsoft Flow is het juiste hulp programma voor deze doel einden.

Met deze integratie kunt u nu talloze processen automatiseren zonder dat u maar één regel code hoeft te schrijven. Nadat u een stroom hebt gemaakt met behulp van een Application Insights actie, voert de stroom automatisch uw Application Insights Analytics-query uit.

U kunt ook extra acties toevoegen. Microsoft Flow maakt honderden acties beschikbaar. U kunt bijvoorbeeld Microsoft Flow gebruiken om automatisch een e-mail melding te verzenden of een bug in azure DevOps te maken. U kunt ook een van de vele [sjablonen](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) gebruiken die beschikbaar zijn voor de connector voor Microsoft flow. Deze sjablonen versnellen het proces van het maken van een stroom.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Een stroom maken voor Application Insights

In deze zelf studie leert u hoe u een stroom maakt die gebruikmaakt van het algoritme voor de automatische cluster analyse om kenmerken te groeperen in de gegevens voor een webtoepassing. De stroom verzendt de resultaten automatisch per e-mail, maar één voor beeld van hoe u Microsoft Flow en Application Insights Analytics samen kunt gebruiken.

### <a name="step-1-create-a-flow"></a>Stap 1: een stroom maken

1. Meld u aan bij [Microsoft flow](https://flow.microsoft.com)en selecteer vervolgens **mijn stromen**.
2. Klik op **Nieuw** en vervolgens **gepland, leeg**.

    ![Nieuwe stroom maken op basis van gepland leeg](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Stap 2: een trigger maken voor uw stroom

1. Vul in het pop-upvenster **een geplande stroom**in en geef aan hoe vaak u de stroom wilt uitvoeren.

    ![Terugkeer patroon van schema instellen met behulp van de frequentie en het interval](./media/automate-with-flow/2-schedule.png)

1. Klik op **Maken**.

### <a name="step-3-add-an-application-insights-action"></a>Stap 3: een Application Insights actie toevoegen

1. Zoeken naar **Application Insights**.
2. Klik op **Azure-toepassing Insights-visualiseren Analytics-query**.

    ![Kies een actie: Azure-toepassing inzichten visualiseren analyse query](./media/automate-with-flow/3-visualize.png)

3. Selecteer **Nieuwe stap**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Stap 4: verbinding maken met een Application Insights-resource

U hebt een toepassings-ID en een API-sleutel voor uw resource nodig om deze stap te volt ooien. U kunt ze ophalen uit de Azure Portal, zoals wordt weer gegeven in het volgende diagram:

![Toepassings-ID in de Azure Portal](./media/automate-with-flow/5apiaccess.png)

![API-sleutel in de Azure Portal](./media/automate-with-flow/6apikey.png)

Geef een naam op voor de verbinding, samen met de toepassings-ID en API-sleutel.

   ![Venster Microsoft Flow verbinding](./media/automate-with-flow/4-connection.png)

Als het vak verbinding niet meteen wordt weer gegeven, klikt u in de rechter bovenhoek van het vak op het weglatings teken om de query in te voeren. Selecteer vervolgens mijn verbindingen of gebruik een bestaand account.

Klik op **Maken**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Stap 5: de analyse query en het grafiek type opgeven
Met dit voor beeld worden de mislukte aanvragen in de afgelopen dag geselecteerd en wordt er een relatie met de uitzonde ringen die zijn opgetreden als onderdeel van de bewerking. Analytics correleert deze op basis van de operation_Id-id. De query segmenteert vervolgens de resultaten met behulp van de algoritme van de autocluster.

Wanneer u uw eigen query's maakt, controleert u of ze correct werken in Analytics voordat u deze toevoegt aan uw stroom.

- Voeg de volgende analyse query toe en selecteer het grafiek type voor de HTML-tabel. Selecteer vervolgens **nieuwe stap**.

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
    
    ![Configuratie venster Analyse query](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Stap 6: de stroom configureren voor het verzenden van e-mail

1. Zoek naar **Office 365 Outlook**.
2. Klik op **Office 365 Outlook-een E-mail verzenden**.

    ![Office 365 Outlook-selectie venster](./media/automate-with-flow/6-outlook.png)

1. In het venster **een E-mail verzenden** :

   a. Typ het e-mail adres van de ontvanger.

   b. Typ een onderwerp voor het e-mail bericht.

   c. Klik op een wille keurige plaats in het vak **hoofd tekst** en selecteer in het menu met dynamische inhoud dat aan de rechter kant wordt geopend, de optie **hoofd tekst**.

   e. Selecteer **Geavanceerde opties weer geven**

1. In het menu dynamische inhoud:

    a. Selecteer de naam van de **bijlage**.

    b. Selecteer de inhoud van de **bijlage**.
    
    c. Selecteer in het vak **is HTML** de optie **Ja**.

    ![Office 365 Outlook-configuratie](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Stap 7: uw stroom opslaan en testen

Klik op **Opslaan**.

U kunt wachten tot de trigger deze actie uitvoert. u kunt ook op ![beker glas klikken](./media/automate-with-flow/testicon.png) pictogram bovenaan **testen** .

Na het selecteren van **test**:

1. Selecteer **Ik voer de trigger actie uit**.
2. Selecteer **uitvoerings stroom**.

Wanneer de stroom wordt uitgevoerd, ontvangen de ontvangers die u in de e-mail lijst hebt opgegeven een e-mail bericht zoals hieronder.

![Voor beeld-e-mail](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van [Analytics-query's](../../azure-monitor/log-query/get-started-queries.md).
- Meer informatie over [Microsoft flow](https://ms.flow.microsoft.com).

<!--Link references-->
