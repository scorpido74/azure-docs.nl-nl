---
title: 'Zelfstudie: Problemen oplossen met Azure-monitor'
description: Ontdek hoe Azure Monitor en Log Analytics u helpt bij het bewaken van uw App Service-web-app. Azure Monitor maximaliseert de beschikbaarheid door een uitgebreide oplossing te leveren voor het bewaken van uw omgevingen.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: d543a9364311b2cf5f0258fbf9185d27bb1bfb2f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399525"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Zelfstudie: Een App Service-app oplossen met Azure Monitor

> [!NOTE]
> Azure Monitor-integratie met App Service is in [preview](https://aka.ms/appsvcblog-azmon).
>

[Azure App Service on Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximaliseert de beschikbaarheid en prestaties van uw toepassingen en services door een uitgebreide oplossing te bieden voor het verzamelen, analyseren en handelen op telemetrie vanuit uw cloud- en on-premises omgevingen.

In deze zelfstudie ziet u hoe u een app oplossen met [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview). De voorbeeld-app bevat code die bedoeld is om het geheugen uit te putten en HTTP 500-fouten te veroorzaken, zodat u het probleem diagnosticeren en oplossen met Azure Monitor.

Wanneer u klaar bent, wordt een voorbeeld-app uitgevoerd op App Service op Linux, geïntegreerd met [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-app configureren met Azure Monitor
> * Consolelogboeken verzenden naar Log Analytics
> * Logboekquery's gebruiken om fouten in webapps te identificeren en op te lossen

U kunt de stappen in deze zelfstudie volgen voor macOS, Linux en Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, heb je het volledige zeggen nodig:

- [Azure-abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure-resources maken

Eerst voert u lokaal verschillende opdrachten uit om een voorbeeld-app in te stellen die u met deze zelfstudie wilt gebruiken. De opdrachten klonen een voorbeeld-app, maken Azure-resources, maken een implementatiegebruiker en implementeren de app naar Azure. U wordt gevraagd om het opgegeven wachtwoord als onderdeel van het maken van de implementatiegebruiker. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Azure Monitor configureren (voorbeeld)

### <a name="create-a-log-analytics-workspace"></a>Een logboekanalysewerkruimte maken

Nu u de voorbeeld-app hebt geïmplementeerd in Azure App Service, configureert u bewakingsmogelijkheden om de app op te lossen wanneer zich problemen voordoen. Azure Monitor slaat logboekgegevens op in een Log Analytics-werkruimte. Een werkruimte is een container die gegevens- en configuratiegegevens bevat.

In deze stap maakt u een werkruimte Log Analytics om Azure Monitor met uw app te configureren.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Voor Azure Monitor Log Analytics betaalt u voor het innemen van gegevens en het bewaren van gegevens.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken

Diagnostische instellingen kunnen worden gebruikt om metrische gegevens voor bepaalde Azure-services te verzamelen in Azure Monitor Logs voor analyse met andere bewakingsgegevens met behulp van logboekquery's. Voor deze zelfstudie schakelt u de webserver en standaard uitvoer-/foutlogboeken in. Zie [ondersteunde logboektypen](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) voor een volledige lijst met logboektypen en -beschrijvingen.

U voert de volgende opdrachten uit om diagnostische instellingen voor AppServiceConsoleLogs (standaarduitvoer/fout) en AppServiceHTTPLogs (webserverlogboeken) te maken. Vervang _ \<>en_ _ \<>van de app-naam_ door uw waarden. 

> [!NOTE]
> De eerste twee `resourceID` opdrachten `workspaceID`en variabelen die in `az monitor diagnostic-settings create` de opdracht moeten worden gebruikt. Zie [Diagnostische instellingen maken met Azure CLI](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli) voor meer informatie over deze opdracht.
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

De voorbeeld-app ImageConverter converteert `JPG` `PNG`opgenomen afbeeldingen van . Een bug is bewust geplaatst in de code voor deze tutorial. Als u voldoende afbeeldingen selecteert, produceert de app een HTTP 500-fout tijdens het converteren van afbeeldingen. Stel je voor dat dit scenario niet werd overwogen tijdens de ontwikkelingsfase. U gebruikt Azure Monitor om de fout op te lossen.

### <a name="verify-the-app-is-works"></a>Controleren of de app werkt

Als u afbeeldingen `Tools` wilt `Convert to PNG`converteren, klikt u op en selecteert u .

![Klik op 'Extra' en selecteer 'Converteren naar PNG'](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Selecteer de eerste twee `convert`afbeeldingen en klik op . Hiermee wordt met succes geconverteerd.

![De eerste twee afbeeldingen selecteren](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>De app verbreken

Nu u de app hebt geverifieerd door twee afbeeldingen succesvol te converteren, proberen we de eerste vijf afbeeldingen om te zetten.

![Eerste vijf afbeeldingen converteren](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Deze actie mislukt en `HTTP 500` produceert een fout die niet is getest tijdens de ontwikkeling.

![De conversie resulteert in een HTTP 500-fout](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Logboekquery gebruiken om Azure Monitor-logboeken weer te geven

Laten we eens kijken welke logboeken beschikbaar zijn in de werkruimte Log Analytics. 

Klik op deze [koppeling naar de log-analysewerkruimte](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) om toegang te krijgen tot uw werkruimte in de Azure-portal.

Selecteer in de Azure-portal de werkruimte Log Analytics.

### <a name="log-queries"></a>Logboekquery's

Met logboekquery's u de waarde van de gegevens die in Azure Monitor-logboeken worden verzameld, volledig benutten. U gebruikt logboekquery's om de logboeken in zowel AppServiceHTTPLogs als AppServiceConsoleLogs te identificeren. Zie het [overzicht van logboekquery's](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) voor meer informatie over logboekquery's.

### <a name="view-appservicehttplogs-with-log-query"></a>AppServiceHTTPLogs weergeven met logboekquery

Nu we de app hebben geopend, bekijken we de gegevens die zijn `AppServiceHTTPLogs`gekoppeld aan HTTP-aanvragen, gevonden in de .

1. Klik `Logs` vanaf de linkernavigatie.

![Log Anlytics Worksace Logs](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Zoeken `appservice` naar en `AppServiceHTTPLogs`dubbelklik op .

![Werkruimtetabellen voor logboekanalyses](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Klik op `Run`.

![LOG Analytics Workspace App Service HTTP-logboeken](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

De `AppServiceHTTPLogs` query retourneert alle aanvragen in de afgelopen 24 uur. De `ScStatus` kolom bevat de HTTP-status. Als u `HTTP 500` de fouten `ScStatus` wilt diagnosticeren, beperkt u de query tot 500 en voert u de query uit, zoals hieronder wordt weergegeven:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>AppServiceConsoleLogs weergeven met logboekquery

Nu u de HTTP 500's hebt bevestigd, laten we eens kijken naar de standaard uitvoer/fouten van de app. Deze logboeken zijn te vinden in 'AppServiceConsoleLogs'.

(1) `+` Klik hierop om een nieuwe query te maken. 

(2) Dubbelklik op `AppServiceConsoleLogs` de `Run`tabel en klik op . 

Aangezien het converteren van vijf afbeeldingen resulteert in serverfouten, u `ResultDescription` zien of de app ook fouten schrijft door op fouten te filteren, zoals hieronder wordt weergegeven:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

In `ResultDescription` de kolom ziet u de volgende fout:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Lid worden van AppServiceHTTPLogs en AppServiceConsoleLogs

Nu u zowel HTTP 500's als standaardfouten hebt geïdentificeerd, moet u bevestigen of er een correlatie is tussen deze berichten. Vervolgens voegen jullie je bij de tabellen `TimeGenerated`op basis van de tijdstempel.

> [!NOTE]
> Er is een query voor u voorbereid die het volgende doet:
>
> - Filtert HTTPLogs voor 500 fouten
> - Consolelogboeken voor query's
> - Voegt zich bij de tabellen op`TimeGenerated`
>

Voer de volgende query uit:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

In `ResultDescription` de kolom ziet u de volgende fout tegelijk met fouten in de webserver:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Het bericht stelt geheugen is uitgeput `process.php`op lijn 20 van . U hebt nu bevestigd dat de toepassing een fout heeft veroorzaakt tijdens de HTTP 500-fout. Laten we eens kijken naar de code om het probleem te identificeren.

## <a name="identify-the-error"></a>De fout identificeren

Open in de lokale `process.php` map de map en kijk naar lijn 20. 

```php
imagepng($imgArray[$x], $filename);
```

Het eerste `$imgArray[$x]`argument, , is een variabele die alle JPGs (in-memory) nodig conversie. Echter, `imagepng` alleen moet het beeld wordt omgezet en niet alle beelden. Voorladen van afbeeldingen is niet nodig en kan de geheugenuitputting veroorzaken, wat leidt tot HTTP 500's. Laten we de code bijwerken om afbeeldingen on-demand te laden om te zien of het probleem is opgelost. Vervolgens verbetert u de code om het geheugenprobleem aan te pakken.

## <a name="fix-the-app"></a>De app herstellen

### <a name="update-locally-and-redeploy-the-code"></a>De code lokaal bijwerken en opnieuw implementeren

U brengt de `process.php` volgende wijzigingen aan om de geheugenuitputting te verwerken:

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

Het converteren van afbeeldingen mag niet langer leiden tot de HTTP 500-fouten.

![PHP-app uitgevoerd in Azure App Service](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Verwijder de diagnostische instelling met de volgende opdracht:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Wat u hebt geleerd:

> [!div class="checklist"]
> * Een web-app configureren met Azure Monitor
> * Verzonden logboeken naar Logboekanalyse
> * Gebruikte logboekquery's om fouten in webapps te identificeren en op te lossen

## <a name="next-steps"></a><a name="nextsteps"></a>Volgende stappen
* [Querylogboeken met Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)
* [Azure App-service oplossen in Visual Studio](../troubleshoot-dotnet-visual-studio.md)
* [App-logboeken analyseren in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
