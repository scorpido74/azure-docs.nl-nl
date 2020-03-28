---
title: Aangepaste dashboards maken in Azure Application Insights | Microsoft Docs
description: Zelfstudie over het maken van aangepaste KPI-dashboards met behulp van Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: caf8b1899cad95ade6297e78e8f2cf35939ef189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661631"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Aangepaste KPI-dashboards maken met behulp van Azure Application Insights

U kunt in Azure Portal meerdere dashboards maken die elk tegels bevatten die gegevens weergeven vanuit meerdere Azure-resources in verschillende resourcegroepen en abonnementen.  U kunt verschillende grafieken en weergaven van Azure Application Insights vastmaken om aangepaste dashboards te maken die u een volledig overzicht geven van de status en prestaties van uw toepassing. Deze zelfstudie helpt u bij het maken van een aangepast dashboard met meerdere typen gegevens en visualisaties uit Azure Application Insights.  Procedures voor:

> [!div class="checklist"]
> * Een aangepast dashboard maken in Azure
> * Een tegel uit de Tegelgalerie toevoegen
> * Standaard metrische gegevens in Application Insights aan het dashboard toevoegen
> * Een aangepaste grafiek met metrische gegevens uit Application Insights aan het dashboard toevoegen
> * De resultaten van een query Logboeken (Analytics) toevoegen aan het dashboard



## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

- Implementeer een .NET-toepassing in Azure en [schakel de Application Insights-SDK](../../azure-monitor/app/asp-net.md)in.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure
Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-a-new-dashboard"></a>Een nieuw dashboard maken
Een dashboard kan resources uit meerdere toepassingen, resourcegroepen en abonnementen bevatten.  Begin deze zelfstudie met het maken van een nieuw dashboard voor uw toepassing.  

1. Selecteer in het dashboarddeelvenster de optie **Nieuw dashboard**.

   ![Nieuw dashboard](media/tutorial-app-dashboards/1newdashboard.png)

1. Typ een naam voor het dashboard.
1. Bekijk de **Tegelgalerie** voor de verschillende tegels die u aan het dashboard kunt toevoegen.  Naast het toevoegen van tegels uit de galerie, u grafieken en andere weergaven rechtstreeks vastmaken van Application Insights naar het dashboard.
1. Zoek de tegel **Markdown** en sleep deze naar uw dashboard.  Met deze tegel u tekst toevoegen die is opgemaakt in markdown, wat ideaal is voor het toevoegen van beschrijvende tekst aan uw dashboard.
1. Voeg tekst toe aan de tegeleigenschappen en wijzig het formaat van de tegel op het canvas van het dashboard.
    
    ![Tegel Markdown bewerken](media/tutorial-app-dashboards/2dashboard-text.png)

1. Klik op **Aanpassen voltooid** boven aan het scherm om de modus voor het aanpassen van tegels af te sluiten.

## <a name="add-health-overview"></a>Statusoverzicht toevoegen
Een dashboard met statische tekst is niet erg interessant, dus voeg nu een tegel toe van Application Insights om informatie over uw toepassing weer te geven.  U kunt Application Insights-tegels toevoegen uit de Tegelgalerie of ze rechtstreeks vanuit Application Insights-schermen vastmaken.  Hiermee kunt u grafieken en weergaven die u al kent, configureren voordat u ze aan uw dashboard vastmaakt.  Voeg eerst het standaardstatusoverzicht voor uw toepassing toe.  Hiervoor is geen configuratie vereist en er is minimale aanpassing mogelijk in het dashboard.


1. Open uw **Application Insights**-resource op het startscherm.
2. Klik **Overview** in het deelvenster Overzicht ![op](media/tutorial-app-dashboards/pushpin.png) het pictogram pin-pictogram om de tegel toe te voegen aan het laatste dashboard dat u hebt weergegeven.  
 
3. Rechtsboven verschijnt er een melding dat uw tegel is vastgemaakt aan uw dashboard. Klik op **Aan dashboard vastgemaakt** in de melding om terug te keren naar het dashboard of om het dashboarddeelvenster te gebruiken.
4. De tegel is nu aan uw dashboard toegevoegd. Selecteer **Bewerken** om de plaatsing van de tegel te wijzigen. Klik erop en sleep de tegel naar de gewenste positie. Klik vervolgens op **Aanpassen voltooid**. Uw dashboard heeft nu een tegel met nuttige informatie.

    ![Dashboard met Overzicht tijdlijn](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Aangepaste grafiek met metrische gegevens toevoegen
In het venster **Metrische gegevens** kunt u waarden die gedurende een periode door Application Insights zijn verzameld, met optionele filters en groepering in een grafiek weergeven.  Net als alle andere items in Application Insights kunt u deze grafiek aan het dashboard toevoegen.  Hiervoor is wel enige aanpassing vereist.

1. Open uw **Application Insights**-resource in het startscherm.
1. Selecteer **Metrische gegevens**.  
2. Er is al een lege grafiek gemaakt en u wordt gevraagd een waarde toe te voegen.  Voeg een waarde toe aan de grafiek en eventueel een filter en groepering.  In het volgende voorbeeld ziet u het aantal serververzoeken gegroepeerd op of ze geslaagd zijn.  Het resultaat is een actieve weergave van geslaagde en mislukte aanvragen.

    ![Waarde toevoegen](media/tutorial-app-dashboards/metrics.png)

4. Selecteer **Vastmaken aan dashboard** aan de rechterkant. Hierdoor wordt de weergave toegevoegd aan het laatste dashboard dat u hebt gebruikt.

3.  Rechtsboven verschijnt er een melding dat uw tegel is vastgemaakt aan uw dashboard. Klik op **Aan dashboard vastgemaakt** in de melding om terug te keren naar het dashboard of om de dashboardblade te gebruiken.

4. De tegel is nu aan uw dashboard toegevoegd. Selecteer **Bewerken** om de plaatsing van de tegel te wijzigen. Klik erop en sleep de tegel naar de gewenste positie. Klik vervolgens op **Aanpassen voltooid**.

## <a name="add-logs-analytics-query"></a>Query Logboeken (Analytics) toevoegen
Azure Application Insights Logs (Analytics) biedt een uitgebreide querytaal waarmee u alle gegevens die zijn verzameld Application Insights analyseren. Net als grafieken en andere weergaven u de uitvoer van een logboekquery toevoegen aan uw dashboard.

Aangezien Azure Applications Insights Logs (Analytics) een afzonderlijke service is, moet u uw dashboard delen om een logboekquery op te nemen. Wanneer u een Azure-dashboard deelt, publiceert u het als een Azure-bron, waardoor het beschikbaar kan zijn voor andere gebruikers en bronnen.  

1. Klik bovenaan het dashboardscherm op **Delen**.

    ![Dashboard publiceren](media/tutorial-app-dashboards/8dashboard-share.png)

2. Behoud de **Dashboardnaam** en selecteer de **Abonnementsnaam** om het dashboard te delen.  Klik op **Publish**.  Het dashboard is nu beschikbaar voor andere services en abonnementen.  Desgewenst kunt u specifieke gebruikers opgeven die toegang tot het dashboard moeten krijgen.
1. Open uw **Application Insights**-resource in het startscherm.
2. Klik links op **Logboeken (Analytics)** om de portal Logboeken (Analytics) te openen.
3. Typ de volgende query die de 10 meest aangevraagde pagina's en het bijbehorende aantal aanvragen retourneert:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Klik op **Uitvoeren** om de resultaten van de query te valideren.
5. Klik op het pictogram pin ![Speldpictogram](media/tutorial-app-dashboards/pushpin.png) en selecteer de naam van uw dashboard. De reden dat u met deze optie een dashboard selecteert in tegenstelling tot de vorige stappen waarin het laatste dashboard is gebruikt, is omdat de console Logboeken (Analytics) een afzonderlijke service is en moet worden geselecteerd uit alle beschikbare gedeelde dashboards.

5. Voordat u teruggaat naar het dashboard, voegt u een andere query toe, maar deze keer wordt deze weergegeven als een grafiek, zodat u de verschillende manieren ziet om een logboekquery in een dashboard te visualiseren. Begin met de volgende query die de top 10 bewerkingen met de meeste uitzonderingen weergeeft.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecteer **Grafiek** en schakel vervolgens over naar een **Ringdiagram** om de uitvoer weer te geven.

    ![Grafiek Logboeken (Analytics)](media/tutorial-app-dashboards/11querychart.png)

6. Klik op het pictogram pin ![Speldpictogram](media/tutorial-app-dashboards/pushpin.png) rechtsboven om de grafiek vast te maken aan uw dashboard en selecteer deze keer de koppeling om terug te keren naar uw dashboard.
4. De resultaten van de query's worden nu aan uw dashboard toegevoegd in de indeling die u hebt geselecteerd.  Klik en sleep elke query naar de gewenste plek en klik vervolgens op **Aanpassen voltooid**.
5. Het potloodpictogram selecteren ![Potloodpictogram](media/tutorial-app-dashboards/pencil.png) op elke titel om hen een beschrijvende titel te geven.

5. Selecteer **Delen** om de wijzigingen op uw dashboard opnieuw te publiceren. Dit bevat nu verschillende grafieken en visualisaties vanuit Application Insights.


## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u aangepaste dashboards maakt, kunt u de verdere Application Insights-documentatie bekijken, waaronder een casestudy.

> [!div class="nextstepaction"]
> [Diepe diagnostische gegevens](../../azure-monitor/app/devops.md)
