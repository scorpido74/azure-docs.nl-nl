---
title: Release aantekeningen voor Application Insights | Microsoft Docs
description: Voeg implementatie-of build markeringen toe aan uw grafieken met metrische gegevens Verkenner in Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: f9ab1f1ef2b64c07a3b0c8ddf93d6ef4293517db
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668119"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Aantekeningen op metrische grafieken in Application Insights

Aantekeningen in [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) grafieken laten zien waar u een nieuwe build of andere belang rijke gebeurtenissen hebt geïmplementeerd. Aantekeningen maken het eenvoudig om te zien of uw wijzigingen van invloed zijn op de prestaties van uw toepassing. Ze kunnen automatisch worden gemaakt door het build-systeem van [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) . U kunt ook aantekeningen maken om elke gewenste gebeurtenis te markeren door ze te maken vanuit Power shell.

> [!NOTE]
> In dit artikel wordt de afgeschafte **klassieke metrische ervaring**weer gegeven. Aantekeningen zijn momenteel alleen beschikbaar in de klassieke ervaring en in **[werkmappen](../../azure-monitor/app/usage-workbooks.md)** . Zie [geavanceerde functies van Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md)voor meer informatie over de huidige ervaring met metrische gegevens.

![Voor beeld van aantekeningen](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Release annotaties with Azure pipelines build

Release aantekeningen zijn een functie van de Azure pipelines in de cloud van Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>De aantekeningen uitbreiding installeren (één keer)
Als u release aantekeningen wilt maken, moet u een van de vele Azure DevOps-uitbrei dingen installeren die beschikbaar zijn in de Visual Studio Marketplace.

1. Meld u aan bij uw [Azure DevOps](https://azure.microsoft.com/services/devops/) -project.
   
1. Selecteer op de pagina [uitbrei ding aantekeningen](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) voor de release van Visual Studio Marketplace de Azure DevOps-organisatie en selecteer vervolgens **installeren** om de extensie toe te voegen aan uw Azure DevOps-organisatie.
   
   ![Selecteer een Azure DevOps-organisatie en selecteer vervolgens installeren.](./media/annotations/1-install.png)
   
U hoeft de uitbrei ding slechts eenmaal te installeren voor uw Azure DevOps-organisatie. U kunt nu release aantekeningen configureren voor elk project in uw organisatie.

### <a name="configure-release-annotations"></a>Release aantekeningen configureren

Maak een afzonderlijke API-sleutel voor elk van de release sjablonen van Azure pipelines.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en open de Application Insights resource waarmee uw toepassing wordt bewaakt. Of als u er geen hebt, [maakt u een nieuwe Application Insights resource](../../azure-monitor/app/app-insights-overview.md).
   
1. Open het tabblad **API-toegang** en kopieer de **Application Insights-id**.
   
   ![Kopieer de toepassings-ID onder API-toegang.](./media/annotations/2-app-id.png)

1. Open of maak in een afzonderlijk browser venster de release sjabloon waarmee de implementaties van uw Azure-pijp lijnen worden beheerd.
   
1. Selecteer **taak toevoegen**en selecteer vervolgens de taak **aantekening van de Application Insights release** in het menu.
   
   ![Selecteer taak toevoegen en selecteer aantekening van Application Insights versie.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > De taak release annotatie ondersteunt momenteel alleen op Windows gebaseerde agents. het kan niet worden uitgevoerd op Linux-, macOS-of andere typen agents.
   
1. Plak onder **toepassings-id**de Application Insights-id die u hebt gekopieerd via het tabblad **API-toegang** .
   
   ![De Application Insights-ID plakken](./media/annotations/4-paste-app-id.png)
   
1. Klik in het venster Application Insights **API-toegang** op **API-sleutel maken**. 
   
   ![Op het tabblad API-toegang selecteert u API-sleutel maken.](./media/annotations/5-create-api-key.png)
   
1. Typ een beschrijving in het venster **API-sleutel maken** , selecteer **aantekeningen schrijven**en selecteer vervolgens **sleutel genereren**. Kopieer de nieuwe sleutel.
   
   ![Typ een beschrijving in het venster API-sleutel maken, selecteer aantekeningen schrijven en selecteer vervolgens sleutel genereren.](./media/annotations/6-create-api-key.png)
   
1. Selecteer in het venster release-sjabloon, op het tabblad **variabelen** , de optie **toevoegen** om een definitie van een variabele te maken voor de nieuwe API-sleutel.

1. Voer onder **naam**`ApiKey`in en plak onder **waarde**de API-sleutel die u hebt gekopieerd op het tabblad **API-toegang** .
   
   ![Ga naar het tabblad Azure DevOps-variabelen, selecteer toevoegen, noem de variabele ApiKey en plak de API-sleutel onder waarde.](./media/annotations/7-paste-api-key.png)
   
1. Selecteer **Opslaan** in het venster hoofd release sjabloon om de sjabloon op te slaan.

## <a name="view-annotations"></a>Aantekeningen weer geven
Wanneer u nu de release sjabloon gebruikt om een nieuwe release te implementeren, wordt er een aantekening naar Application Insights verzonden. De aantekeningen worden weer gegeven in grafieken in **Metrics Explorer**.

Selecteer een aantekening markering (licht grijze pijl) om details over de release te openen, met inbegrip van de aanvrager, de bron beheer vertakking, de release pijplijn en de omgeving.

![Selecteer een markering voor de vrijgave aantekening.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Aangepaste aantekeningen maken vanuit Power shell
U kunt het Power shell-script [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) van github gebruiken om aantekeningen te maken op basis van elk gewenst proces, zonder Azure DevOps te gebruiken. 

1. Maak een lokale kopie van [CreateReleaseAnnotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Volg de stappen in de voor gaande procedure om uw Application Insights-ID op te halen en een API-sleutel te maken op het tabblad Application Insights **API-toegang** .
   
1. Roep het Power shell-script aan met de volgende code en vervang de tijdelijke aanduidingen met punt haken door uw waarden. De `-releaseProperties` zijn optioneel. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

U kunt het script wijzigen, bijvoorbeeld om aantekeningen voor het verleden te maken.

## <a name="next-steps"></a>Volgende stappen

* [Werk items maken](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatisering met Power shell](../../azure-monitor/app/powershell.md)
