---
title: 'Zelf studie: problemen oplossen met Azure Monitor'
description: Lees hoe u met Azure Monitor en Log Analytics uw App Service-web-app kunt bewaken. Azure Monitor maximaliseert de beschik baarheid door een uitgebreide oplossing te leveren voor het bewaken van uw omgevingen.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 1/29/2019
ms.openlocfilehash: c0df972d893e82d7b49d5b3ed2a2bbf7067548c2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201813"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Zelf studie: problemen met Azure Monitor oplossen met een App Service-app

[App Service onder Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximaliseert de beschik baarheid en prestaties van uw toepassingen en services door een uitgebreide oplossing te bieden voor het verzamelen, analyseren en uitvoeren van telemetrie in uw Cloud-en on-premises omgevingen.

In deze zelf studie ziet u hoe u een app oplost met [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview). De voor beeld-app bevat code die is bedoeld voor het gebruik van geheugen en die HTTP 500-fouten veroorzaken, zodat u het probleem kunt vaststellen en oplossen met behulp van Azure Monitor.

Wanneer u klaar bent, hebt u een voor beeld van een app die wordt uitgevoerd op App Service Linux geïntegreerd met [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-app configureren met Azure Monitor
> * Console logboeken naar Log Analytics verzenden
> * Logboek query's gebruiken om fouten in de web-app op te sporen en op te lossen

U kunt de stappen in deze zelfstudie volgen voor macOS, Linux en Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u het volgende nodig:

- [Azure-abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure-resources maken

Eerst voert u meerdere opdrachten lokaal uit voor het instellen van een voor beeld-app voor gebruik in deze zelf studie. Met de opdrachten kloont u een voor beeld-app, maakt u Azure-resources, maakt u een implementatie gebruiker en implementeert u de app in Azure. U wordt gevraagd om het wacht woord dat u hebt opgegeven als onderdeel van het maken van de implementatie gebruiker. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor"></a>Azure Monitor configureren

### <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte maken

Nu u de voor beeld-app hebt geïmplementeerd op Azure App Service, configureert u de functionaliteit voor het controleren van problemen met de app wanneer er problemen optreden. Azure Monitor worden logboek gegevens opgeslagen in een Log Analytics-werk ruimte. Een werk ruimte is een container die gegevens-en configuratie gegevens bevat.

In deze stap maakt u een Log Analytics werk ruimte om Azure Monitor met uw app te configureren.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Voor Azure Monitor Log Analytics betaalt u voor gegevens opname en gegevens retentie.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken

Diagnostische instellingen kunnen worden gebruikt voor het verzamelen van metrische gegevens voor bepaalde Azure-Services in Azure Monitor logboeken voor analyse met andere bewakings informatie met behulp van logboek query's. Voor deze zelf studie schakelt u de webserver en standaard logboeken voor uitvoer/fouten in. Zie [ondersteunde logboek typen](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) voor een volledige lijst met logboek typen en beschrijvingen.

U voert de volgende opdrachten uit om Diagnostische instellingen te maken voor AppServiceConsoleLogs (standaard uitvoer/-fout) en AppServiceHTTPLogs (webserver logboeken). Vervang _\<app-naam >_ en _\<werkruimte naam >_ met uw waarden. 

> [!NOTE]
> De eerste twee opdrachten, `resourceID` en `workspaceID`, zijn variabelen die moeten worden gebruikt in de `az monitor diagnostic-settings create`-opdracht. Zie [Diagnostische instellingen maken met behulp van Azure cli](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli) voor meer informatie over deze opdracht.
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>Problemen met de app oplossen

Blader naar `http://<app-name>.azurewebsites.net`.

De voor beeld-app, ImageConverter, converteert opgenomen afbeeldingen van `JPG` naar `PNG`. Een bug is opzettelijk in de code voor deze zelf studie geplaatst. Als u voldoende installatie kopieën selecteert, produceert de app een HTTP 500-fout tijdens de conversie van de installatie kopie. Stel dat dit scenario tijdens de ontwikkelings fase niet werd overwogen. U gebruikt Azure Monitor om de fout op te lossen.

### <a name="verify-the-app-is-works"></a>Controleren of de app werkt

Als u afbeeldingen wilt converteren, klikt u op `Tools` en selecteert u `Convert to PNG`.

![Klik op Hulpprogram Ma's en selecteer converteren naar PNG](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selecteer de eerste twee afbeeldingen en klik op `convert`. Hiermee wordt de conversie voltooid.

![De eerste twee afbeeldingen selecteren](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>De app opdelen

Nu u de app hebt gecontroleerd door twee afbeeldingen te converteren, zullen we proberen de eerste vijf installatie kopieën te converteren.

![Eerste vijf afbeeldingen converteren](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Deze actie mislukt en resulteert in een `HTTP 500` fout die niet is getest tijdens de ontwikkeling.

![De conversie resulteert in een HTTP 500-fout](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Logboek query gebruiken om Azure Monitor logboeken weer te geven

Laten we eens kijken welke logboeken er beschikbaar zijn in de Log Analytics-werk ruimte. 

Klik op deze [log Analytics werkruimte koppeling](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) om toegang te krijgen tot uw werk ruimte in de Azure Portal.

Selecteer uw Log Analytics-werk ruimte in de Azure Portal.

### <a name="log-queries"></a>Logboekquery's

Met logboek query's kunt u de waarde van de gegevens die in Azure Monitor logboeken worden verzameld, volledig benutten. U gebruikt logboek query's om de logboeken in zowel AppServiceHTTPLogs als AppServiceConsoleLogs te identificeren. Zie het [overzicht van de logboek query](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) voor meer informatie over logboek query's.

### <a name="view-appservicehttplogs-with-log-query"></a>AppServiceHTTPLogs weer geven met logboek query

Nu de app is geopend, bekijken we de gegevens die zijn gekoppeld aan HTTP-aanvragen in de `AppServiceHTTPLogs`.

1. Klik op `Logs` in de linkernavigatiebalk.

![Logboeken Anlytics Worksace-logboeken](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Zoek `appservice` en dubbel klik op `AppServiceHTTPLogs`.

![Log Analytics-werkruimte tabellen](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klik op `Run`.

![Log Analytics werk ruimte App Service HTTP-logboeken](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

De `AppServiceHTTPLogs` query retourneert alle aanvragen in de afgelopen 24 uur. De kolom `ScStatus` bevat de HTTP-status. Als u de `HTTP 500` fouten wilt vaststellen, beperkt u de `ScStatus` tot 500 en voert u de query uit, zoals hieronder wordt weer gegeven:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>AppServiceConsoleLogs weer geven met logboek query

Nu u de HTTP-500 hebt bevestigd, gaan we eens kijken naar de standaard uitvoer/-fouten van de app. Deze logboeken zijn te vinden in ' AppServiceConsoleLogs '.

(1) Klik op `+` om een nieuwe query te maken. 

(2) dubbel klik op de tabel `AppServiceConsoleLogs` en klik op `Run`. 

Omdat het converteren van vijf installatie kopieën resulteert in Server fouten, kunt u zien of de app ook fouten schrijft door `ResultDescription` te filteren op fouten, zoals hieronder wordt weer gegeven:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

In de kolom `ResultDescription` ziet u de volgende fout:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>AppServiceHTTPLogs en AppServiceConsoleLogs toevoegen

Nu u zowel HTTP-500 als standaard fouten hebt geïdentificeerd, moet u controleren of er een correlatie bestaat tussen deze berichten. Vervolgens voegt u de tabellen samen toe op basis van het tijds tempel `TimeGenerated`.

> [!NOTE]
> Er is een query voor bereid voor u die het volgende doet:
>
> - Filters HTTPLogs voor 500-fouten
> - Query-console logboeken
> - Voegt de tabellen op `TimeGenerated`
>

Voer de volgende query uit:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

In de kolom `ResultDescription` ziet u de volgende fout op hetzelfde moment als webserver fouten:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Het geheugen voor de status van berichten is uitgeput op regel 20 van `process.php`. U hebt nu bevestigd dat de toepassing een fout heeft veroorzaakt tijdens de HTTP 500-fout. Laten we eens kijken naar de code om het probleem te identificeren.

## <a name="identify-the-error"></a>De fout identificeren

Open in de lokale map de `process.php` en zoek naar regel 20. 

```php
imagepng($imgArray[$x], $filename);
```

Het eerste argument `$imgArray[$x]`is een variabele met alle JPGs (in het geheugen) die moeten worden geconverteerd. `imagepng` hoeft echter alleen de afbeelding te converteren en niet alle installatie kopieën. Het is niet nodig om installatie kopieën vooraf te laden en de geheugen ontdubbeling wordt mogelijk veroorzaakt door HTTP-500. We gaan de code bijwerken om afbeeldingen op aanvraag te laden om te zien of het probleem wordt opgelost. Vervolgens gaat u de code verbeteren om het geheugen probleem op te lossen.

## <a name="fix-the-app"></a>De app oplossen

### <a name="update-locally-and-redeploy-the-code"></a>De code lokaal bijwerken en opnieuw implementeren

U brengt de volgende wijzigingen aan `process.php` voor het afhandelen van de geheugen uitputting:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Leg uw wijzigingen vast in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Naar de Azure-app bladeren

Blader naar `http://<app-name>.azurewebsites.net`. 

Het converteren van installatie kopieën mag niet langer de HTTP 500-fouten opleveren.

![PHP-app uitgevoerd in Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Verwijder de diagnostische instelling met de volgende opdracht:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Wat u hebt geleerd:

> [!div class="checklist"]
> * Een web-app geconfigureerd met Azure Monitor
> * De logboeken zijn verzonden naar Log Analytics
> * Gebruikte logboek query's voor het identificeren en oplossen van fouten in web-apps

## <a name="nextsteps"></a> Volgende stappen
* [Query's uitvoeren op Logboeken met Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)
* [Problemen met Azure App Service oplossen in Visual Studio](../troubleshoot-dotnet-visual-studio.md)
* [App-Logboeken in HDInsight analyseren](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
