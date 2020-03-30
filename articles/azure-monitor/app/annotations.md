---
title: Annotaties vrijgeven voor toepassingsinzichten | Microsoft Documenten
description: Voeg implementatie- of buildmarkeertekens toe aan de grafieken van de statistiekenverkenners in Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666510"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Annotaties op metrische grafieken in Application Insights

Annotaties in [grafieken van Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) geven aan waar u een nieuwe build of andere belangrijke gebeurtenissen hebt geïmplementeerd. Annotaties maken het gemakkelijk om te zien of uw wijzigingen enig effect hebben gehad op de prestaties van uw toepassing. Ze kunnen automatisch worden gemaakt door het [Azure Pipelines-buildsysteem.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) U ook aantekeningen maken om elke gebeurtenis die u wilt te markeren door ze te maken via PowerShell.

> [!NOTE]
> Dit artikel weerspiegelt de afgeschafte **klassieke metrische gegevens ervaring**. Annotaties zijn momenteel alleen beschikbaar in de klassieke ervaring en in **[werkmappen.](../../azure-monitor/app/usage-workbooks.md)** Zie Geavanceerde functies van Azure [Metrics Explorer](../../azure-monitor/platform/metrics-charts.md)voor meer informatie over de huidige ervaring met statistieken.

![Voorbeeld van annotaties](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Annotaties vrijgeven met Azure Pipelines-build

Releaseannotaties zijn een functie van de cloudgebaseerde Azure Pipelines-service van Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>De annotatiesextensie installeren (eenmalig)
Als u release-annotaties wilt maken, moet u een van de vele Azure DevOps-extensies installeren die beschikbaar zijn in de Visual Studio Marketplace.

1. Meld u aan bij uw [Azure DevOps-project.](https://azure.microsoft.com/services/devops/)
   
1. Selecteer op de pagina Van de uitbreiding van DevOps van Visual Studio Marketplace [Release Annotations](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) uw Azure DevOps-organisatie en selecteer **Vervolgens Installeren** om de extensie toe te voegen aan uw Azure DevOps-organisatie.
   
   ![Selecteer een Azure DevOps-organisatie en selecteer Vervolgens Installeren.](./media/annotations/1-install.png)
   
U hoeft de extensie slechts één keer te installeren voor uw Azure DevOps-organisatie. U nu release-annotaties configureren voor elk project in uw organisatie.

### <a name="configure-release-annotations"></a>Release-annotaties configureren

Maak een afzonderlijke API-sleutel voor elk van uw Azure Pipelines release sjablonen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en open de Application Insights-bron die uw toepassing bewaakt. Of als u er geen hebt, [maakt u een nieuwe Application Insights-bron.](../../azure-monitor/app/app-insights-overview.md)
   
1. Open het tabblad **API-toegang** en kopieer de **application insights-id**.
   
   ![Kopieer onder API Access de toepassings-id.](./media/annotations/2-app-id.png)

1. Open of maak in een apart browservenster de releasesjabloon die uw Azure Pipelines-implementaties beheert.
   
1. Selecteer **Taak toevoegen**en selecteer vervolgens de taak Voor het vrijgeven van **toepassingsinzichten** in het menu.
   
   ![Selecteer Taak toevoegen en selecteer Aantekening van release van toepassingsinzichten.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > De taak Annotatie vrijgeven ondersteunt momenteel alleen agents op basis van Windows. het zal niet draaien op Linux, macOS of andere soorten agents.
   
1. Plak **onder Toepassings-id**de Application Insights-id die u hebt gekopieerd van het tabblad **API Access.**
   
   ![De app-insights-id plakken](./media/annotations/4-paste-app-id.png)
   
1. Selecteer **API-sleutel maken**in het venster **Api Access voor** Application Insights . 
   
   ![Selecteer api-sleutel maken op het tabblad API Access.](./media/annotations/5-create-api-key.png)
   
1. Typ in het venster **API-sleutel maken** een beschrijving, selecteer **Aantekeningen schrijven**en selecteer vervolgens De **toets genereren**. Kopieer de nieuwe sleutel.
   
   ![Typ in het venster API-sleutel maken een beschrijving, selecteer Aantekeningen schrijven en selecteer vervolgens De toets genereren.](./media/annotations/6-create-api-key.png)
   
1. Selecteer in het venster releasesjabloon op het tabblad **Variabelen** de optie **Toevoegen** om een variabele definitie voor de nieuwe API-sleutel te maken.

1. Plak **Name**onder `ApiKey`Naam , voer de API-sleutel die u hebt gekopieerd uit het tabblad API **Access** in en onder **Waarde.**
   
   ![Selecteer op het tabblad Azure DevOps Variables de optie Toevoegen, de naam van de variabele ApiKey en plak de API-sleutel onder Waarde.](./media/annotations/7-paste-api-key.png)
   
1. Selecteer **Opslaan** in het venster sjabloon voor de hoofdontgrendeling om de sjabloon op te slaan.

## <a name="view-annotations"></a>Annotaties weergeven
Wanneer u de releasesjabloon nu gebruikt om een nieuwe release te implementeren, wordt er een annotatie naar Application Insights verzonden. De annotaties worden weergegeven in grafieken in **Metrics Explorer**.

Selecteer een annotatiemarkering (lichtgrijze pijl) om details over de release te openen, inclusief aanvrager, bronbeheerbranch, releasepijplijn en omgeving.

![Selecteer een markering voor de release-annotatie.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Aangepaste annotaties maken vanuit PowerShell
U het [CreateReleaseAnnotation PowerShell-script](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) van GitHub gebruiken om aantekeningen te maken van elk proces dat u wilt, zonder Azure DevOps te gebruiken. 

1. Maak een lokale kopie van [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Gebruik de stappen in de vorige procedure om uw Application Insights ID op te halen en maak een API-sleutel op het tabblad Application Insights **API Access.**
   
1. Roep het PowerShell-script aan met de volgende code en vervang de tijdelijke aanduidingen met hoekhaakjes door uw waarden. De `-releaseProperties` zijn optioneel. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

U het script wijzigen, bijvoorbeeld om aantekeningen voor het verleden te maken.

## <a name="next-steps"></a>Volgende stappen

* [Werkitems maken](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatisering met PowerShell](../../azure-monitor/app/powershell.md)
