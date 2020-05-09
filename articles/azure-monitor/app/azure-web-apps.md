---
title: Prestaties van Azure app Services controleren | Microsoft Docs
description: Bewaking van toepassings prestaties voor Azure app Services. Grafiek belasting en respons tijd, afhankelijkheids informatie en waarschuwingen instellen voor prestaties.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0f4d4dedab30839db56cb47ac7ac103413f2d4be
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733444"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service-prestaties bewaken

Het inschakelen van controle op uw ASP.NET-en ASP.NET Core op webtoepassingen die worden uitgevoerd op [Azure-app Services](https://docs.microsoft.com/azure/app-service/) is nu nog eenvoudiger dan ooit. Voordat u een site-uitbrei ding hand matig moest installeren, is de meest recente extensie/agent nu standaard in de app service-installatie kopie ingebouwd. Dit artikel begeleidt u bij het inschakelen van Application Insights bewaking en voorziet in voorlopige richt lijnen voor het automatiseren van het proces voor grootschalige implementaties.

> [!NOTE]
> Het hand matig toevoegen van een Application Insights site- > **uitbrei ding** via **hulpprogram ma's voor ontwikkel aars**is afgeschaft. Deze methode van extensie-installatie is afhankelijk van hand matige updates voor elke nieuwe versie. De laatste stabiele versie van de uitbrei ding is nu [vooraf geïnstalleerd](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) als onderdeel van de app service-installatie kopie. De bestanden bevinden zich `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` in en worden automatisch bijgewerkt met elke stabiele versie. Als u de op agents gebaseerde instructies volgt om de controle hieronder in te scha kelen, wordt de afgeschafte uitbrei ding automatisch verwijderd.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Er zijn twee manieren om toepassings bewaking in te scha kelen voor door Azure-app Services gehoste toepassingen:

* **Toepassings bewaking op basis van agents** (ApplicationInsightsAgent).  
    * Deze methode is het gemakkelijkst in te scha kelen en er is geen geavanceerde configuratie vereist. Dit wordt vaak aangeduid als runtime-bewaking. Voor Azure-app Services wordt u aangeraden ten minste dit bewakings niveau in te scha kelen en vervolgens op basis van uw specifieke scenario kunt u evalueren of er meer geavanceerde bewaking via hand matige instrumentatie nodig is.

* **Hand matig instrumenteer de toepassing via code** door de Application Insights SDK te installeren.

    * Deze benadering is veel meer aanpasbaar, maar het is wel nodig om [een afhankelijkheid toe te voegen aan de Application INSIGHTS SDK NuGet-pakketten](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Deze methode betekent ook dat u de updates voor de meest recente versie van de pakketten zelf moet beheren.

    * Als u aangepaste API-aanroepen wilt maken voor het bijhouden van gebeurtenissen/afhankelijkheden die niet standaard worden vastgelegd met bewaking op basis van agents, moet u deze methode gebruiken. Bekijk de [API voor het artikel aangepaste gebeurtenissen en metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) voor meer informatie. Dit is momenteel de enige optie die wordt ondersteund voor op Linux gebaseerde workloads.

> [!NOTE]
> Als zowel bewaking op basis van een agent als hand matige instrumentatie op basis van een SDK wordt gedetecteerd, worden alleen de instellingen voor hand matige instrumentatie gehonoreerd. Dit is om te voor komen dat dubbele gegevens worden verzonden. Raadpleeg voor meer informatie de [sectie probleem oplossing](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) hieronder.

## <a name="enable-agent-based-monitoring"></a>Bewaking op basis van agent inschakelen

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> De combi natie van APPINSIGHTS_JAVASCRIPT_ENABLED en urlCompression wordt niet ondersteund. Zie de uitleg in de [sectie probleem oplossing](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)voor meer informatie.


1. **Selecteer Application Insights** in het deel venster Azure van het configuratie scherm voor uw app service.

    ![Kies onder instellingen de optie Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Kies ervoor om een nieuwe resource te maken, tenzij u al een Application Insights resource voor deze toepassing hebt ingesteld. 

     > [!NOTE]
     > Wanneer u op **OK** klikt om de nieuwe resource te maken, wordt u gevraagd de **controle-instellingen toe te passen**. Als u **door gaan** selecteert, wordt uw nieuwe Application Insights-bron gekoppeld aan uw app service, waardoor ook **de app service opnieuw wordt gestart**. 

     ![Uw web-app instrumenteren](./media/azure-web-apps/create-resource-01.png)

2. Nadat u hebt opgegeven welke resource moet worden gebruikt, kunt u kiezen hoe Application Insights gegevens per platform voor uw toepassing moet verzamelen. ASP.NET app monitoring is standaard ingeschakeld met twee verschillende verzamelings niveaus.

    ![Opties per platform kiezen](./media/azure-web-apps/choose-options-new.png)
 
 Hieronder vindt u een overzicht van de gegevens die voor elke route worden verzameld:
        
|  | .NET Basic-verzameling | .NET aanbevolen verzameling |
| --- | --- | --- |
| Voegt trends toe voor CPU, geheugen en I/O-gebruik |Ja |Ja |
| Verzamelt gebruikstrends en maakt correlatie mogelijk van beschikbaarheidsresultaten tot transacties | Ja |Ja |
| Verzamelt uitzonderingen die niet zijn verwerkt door het hostproces | Ja |Ja |
| Verbetert de nauwkeurigheid van metrische APM-gegevens onder belasting, wanneer steekproeven worden gebruikt | Ja |Ja |
| Correleert microservices over aanvraag-/afhankelijkheidsgrenzen | Nee (alleen APM-mogelijkheden met één instantie) |Ja |

3. Voor het configureren van instellingen zoals steek proeven, die u eerder kunt beheren via het applicationinsights. config-bestand, hebt u nu de opdracht om met dezelfde instellingen te werken via toepassings instellingen met een bijbehorend voor voegsel. 

    * Als u bijvoorbeeld het eerste sampling percentage wilt wijzigen, kunt u een toepassings instelling maken van: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` en een waarde van `100`.

    * Raadpleeg de [code](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) en de [bijbehorende documentatie](https://docs.microsoft.com/azure/azure-monitor/app/sampling)voor een lijst met ondersteunde instellingen voor adaptieve bemonsterings-telemetrie.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

De volgende versies van .NET core worden ondersteund: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2, ASP.NET Core 3,0

Het is **niet mogelijk** om het volledige Framework te richten op basis van .net core, op zichzelf gebaseerde implementatie en op Linux gebaseerde toepassingen die op agent/op extensie gebaseerde bewaking niet worden ondersteund. ([Hand matige instrumentatie](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via code werkt in alle eerdere scenario's.)

1. **Selecteer Application Insights** in het deel venster Azure van het configuratie scherm voor uw app service.

    ![Kies onder instellingen de optie Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Kies ervoor om een nieuwe resource te maken, tenzij u al een Application Insights resource voor deze toepassing hebt ingesteld. 

     > [!NOTE]
     > Wanneer u op **OK** klikt om de nieuwe resource te maken, wordt u gevraagd de **controle-instellingen toe te passen**. Als u **door gaan** selecteert, wordt uw nieuwe Application Insights-bron gekoppeld aan uw app service, waardoor ook **de app service opnieuw wordt gestart**. 

     ![Uw web-app instrumenteren](./media/azure-web-apps/create-resource-01.png)

2. Nadat u hebt opgegeven welke resource moet worden gebruikt, kunt u kiezen hoe Application Insights gegevens per platform wilt verzamelen voor uw toepassing. .NET core biedt **Aanbevolen verzameling** of **uitgeschakeld** voor .net Core 2,0, 2,1, 2,2 en 3,0.

    ![Opties per platform kiezen](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Selecteer in de app service-Web-app onder **instellingen** > **de optie Application Insights** > **inschakelen**. Bewaking op basis van node. js-agent is momenteel beschikbaar als preview-versie.

# <a name="java"></a>[Java](#tab/java)

Java-webtoepassingen op basis van App Service ondersteunen momenteel geen automatische bewaking op basis van agent/extensie. Als u bewaking voor uw Java-toepassing wilt inschakelen, moet u [uw toepassing hand matig instrumenteren](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Python App Service gebaseerde webtoepassingen bieden momenteel geen ondersteuning voor automatische bewaking op basis van agent/extensie. Als u bewaking voor uw python-toepassing wilt inschakelen, moet u [uw toepassing hand matig instrumenteren](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Bewaking aan client zijde inschakelen

# <a name="net"></a>[.NET](#tab/net)

Bewaking aan client zijde is opt-in voor ASP.NET. Bewaking aan client zijde inschakelen:

* **Instellingen** selecteren > * * * * toepassings instellingen * * * *
   * Voeg onder toepassings instellingen een nieuwe naam en **waarde**voor de **app-instelling** toe:

     Naam`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Waarde:`true`

   * Sla de instellingen op met **Opslaan** en start de app opnieuw met **Opnieuw opstarten**.

![Scherm opname van de gebruikers interface voor toepassings instellingen](./media/azure-web-apps/appinsights-javascript-enabled.png)

Als u bewaking aan client zijde wilt uitschakelen, verwijdert u het gekoppelde sleutel waardepaar uit de toepassings instellingen of stelt u de waarde in op false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Bewaking aan client zijde is **standaard ingeschakeld** voor .net Core-Apps met **Aanbevolen verzameling**, ongeacht of de app-instelling ' APPINSIGHTS_JAVASCRIPT_ENABLED ' aanwezig is.

Als u de bewaking aan client zijde om een of andere reden wilt uitschakelen:

* **Instellingen** > voor**Toepassings instellingen** selecteren
   * Voeg onder toepassings instellingen een nieuwe naam en **waarde**voor de **app-instelling** toe:

     naam`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Waarde:`false`

   * Sla de instellingen op met **Opslaan** en start de app opnieuw met **Opnieuw opstarten**.

![Scherm opname van de gebruikers interface voor toepassings instellingen](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Als u bewaking aan client zijde voor uw node. js-toepassing wilt inschakelen, moet u [de Java script-SDK aan de client zijde hand matig toevoegen aan uw toepassing](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

Als u bewaking aan client zijde voor uw Java-toepassing wilt inschakelen, moet u [de Java script SDK aan de client zijde hand matig toevoegen aan uw toepassing](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

Als u bewaking aan client zijde voor uw python-toepassing wilt inschakelen, moet u [de Java script SDK aan de client zijde hand matig toevoegen aan uw toepassing](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>Bewaking automatiseren

Als u telemetrie-verzameling met Application Insights wilt inschakelen, moeten alleen de toepassings instellingen worden ingesteld:

   ![Toepassings instellingen App Service met beschik bare Application Insights instellingen](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definities van toepassings instellingen

|Naam van app-instelling |  Definitie | Waarde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hoofd extensie, waarmee runtime bewaking wordt beheerd. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  In de standaard modus worden alleen essentiële functies ingeschakeld om optimale prestaties te garanderen. | `default` of `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Hiermee wordt bepaald of de engine `InstrumentationEngine` voor het herschrijven van binaire bestanden wordt ingeschakeld. Deze instelling heeft invloed op de prestaties en is koude start/Startup-tijd. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Hiermee wordt bepaald of SQL & Azure-tabel tekst wordt vastgelegd samen met de afhankelijkheids aanroepen. Prestatie waarschuwing: de start tijd van de toepassing wordt beïnvloed. Deze instelling vereist de `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Toepassings instellingen App Service met Azure Resource Manager

Toepassings instellingen voor App Services kunnen worden beheerd en geconfigureerd met [Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Deze methode kan worden gebruikt bij het implementeren van nieuwe App Service resources met Azure Resource Manager Automation of voor het wijzigen van de instellingen van bestaande resources.

De basis structuur van de JSON van de toepassings instellingen voor een app service vindt u hieronder:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Voor een voor beeld van een Azure Resource Manager sjabloon met toepassings instellingen die zijn geconfigureerd voor Application Insights, kan deze [sjabloon](https://github.com/Andrew-MSFT/BasicImageGallery) handig zijn, met name de sectie die begint op [regel 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatiseer het maken van een Application Insights resource en een koppeling naar de zojuist gemaakte App Service.

Als u een Azure Resource Manager sjabloon wilt maken met alle standaard instellingen voor Application Insights geconfigureerd, start u het proces alsof u een nieuwe web-app wilt maken waarvoor Application Insights is ingeschakeld.

**Opties voor Automation** selecteren

   ![Menu App Service web-app maken](./media/azure-web-apps/create-web-app.png)

Met deze optie wordt de meest recente Azure Resource Manager sjabloon gegenereerd met alle vereiste instellingen geconfigureerd.

  ![App Service web-app-sjabloon](./media/azure-web-apps/arm-template.png)

Hieronder ziet u een voor beeld, waarbij u `AppMonitoredSite` alle exemplaren van met de naam van uw site vervangt:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Inschakelen via Power shell

Als u de toepassings bewaking via Power shell wilt inschakelen, moeten alleen de onderliggende toepassings instellingen worden gewijzigd. Hieronder ziet u een voor beeld waarin toepassings bewaking wordt ingeschakeld voor een website met de naam ' AppMonitoredSite ' in de resource groep ' AppMonitoredRG ' en waarmee gegevens worden geconfigureerd om te worden verzonden naar de instrumentatie sleutel ' 012345678-ABCD-ef01-2345-6789abcd '.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Bewakings uitbreiding/agent bijwerken

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade uitvoeren van versies 2.8.9 en hoger

Een upgrade van versie 2.8.9 wordt automatisch uitgevoerd zonder dat er extra acties worden uitgevoerd. De nieuwe bewakings-bits worden op de achtergrond bezorgd bij de doel-app-service en bij het opnieuw opstarten van de toepassing worden ze opgenomen.

Controleren welke versie van de uitbrei ding u gaat uitvoeren`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Scherm opname van URL-padhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade uitvoeren van versies 1.0.0-2.6.5

Vanaf versie 2.8.9 wordt de extensie van de vooraf geïnstalleerde site gebruikt. Als u een eerdere versie hebt, kunt u op een van de volgende twee manieren bijwerken:

* [Upgrade door via de portal in te scha kelen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Zelfs als u de Application Insights extensie voor Azure App Service hebt geïnstalleerd, wordt in de gebruikers interface alleen de knop **inschakelen** weer gegeven. Achter de schermen wordt de extensie van de oude persoonlijke site verwijderd.)

* [Upgrade via Power shell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Stel de toepassings instellingen in om de vooraf geïnstalleerde site-uitbrei ding ApplicationInsightsAgent in te scha kelen. Zie [Enable through Power shell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)(Engelstalig).
    2. Verwijder de extensie van de persoonlijke site met de naam Application Insights extensie voor Azure App Service hand matig.

Als de upgrade wordt uitgevoerd vanaf een eerdere versie dan 2.5.1, controleert u of de dll-bestanden van de ApplicationInsigths zijn verwijderd uit de map Application bin [Zie probleemoplossings stappen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Problemen oplossen

Hieronder vindt u stapsgewijze richt lijnen voor het oplossen van problemen met de bewaking van extensies en agents voor .NET-en .NET core-toepassingen die worden uitgevoerd op Azure-app Services.

> [!NOTE]
> Java-toepassingen worden alleen ondersteund op Azure-app Services via hand matige instrumentatie op basis van SDK en daarom zijn de volgende stappen niet van toepassing op deze scenario's.

1. Controleer of de toepassing wordt bewaakt via `ApplicationInsightsAgent`.
    * Controleer of `ApplicationInsightsAgent_EXTENSION_VERSION` de app-instelling is ingesteld op de waarde "~ 2".
2. Zorg ervoor dat de toepassing voldoet aan de vereisten die moeten worden bewaakt.
    * Ga naar `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Scherm afbeelding https://yoursitename.scm.azurewebsites/applicationinsights van resultaten pagina](./media/azure-web-apps/app-insights-sdk-status.png)

    * Bevestig dat de `Application Insights Extension Status` is`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Als deze niet wordt uitgevoerd, volgt u de instructies voor het [inschakelen van Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Controleer of de status bron bestaat en ziet er als volgt uit:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Als er geen vergelijk bare waarde aanwezig is, betekent dit dat de toepassing momenteel niet wordt uitgevoerd of niet wordt ondersteund. Om ervoor te zorgen dat de toepassing wordt uitgevoerd, probeert u de toepassings-URL/toepassings eindpunten hand matig te bezoeken, waardoor de runtime gegevens beschikbaar worden.

    * Bevestig dat `IKeyExists``true`
        * Als dit het `false`geval is `APPINSIGHTS_INSTRUMENTATIONKEY` , `APPLICATIONINSIGHTS_CONNECTION_STRING` voegt u en met uw iKey-GUID toe aan uw toepassings instellingen.

    * Controleer of er geen vermeldingen zijn voor `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, en `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Als een van deze vermeldingen bestaat, verwijdert u de volgende pakketten uit uw toepassing `Microsoft.ApplicationInsights`: `System.Diagnostics.DiagnosticSource`, en `Microsoft.AspNet.TelemetryCorrelation`.

De onderstaande tabel bevat een gedetailleerdere uitleg van de betekenis van deze waarden, de onderliggende oorzaken en aanbevolen oplossingen:

|Probleem waarde|Uitleg|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Deze waarde geeft aan dat de uitbrei ding heeft gedetecteerd dat er al een aspect van de SDK aanwezig is in de toepassing en dat deze wordt teruggedraaid. Dit kan worden veroorzaakt door een verwijzing naar `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`of`Microsoft.ApplicationInsights`  | Verwijder de verwijzingen. Sommige van deze verwijzingen worden standaard toegevoegd vanuit bepaalde Visual Studio-sjablonen, en oudere versies van Visual Studio kunnen verwijzingen toevoegen aan `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Als de toepassing is gericht op .NET Core 2,1 of 2,2, en verwijst naar [micro soft. AspNetCore. all](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta package, wordt het Application Insights en wordt er een uitbrei ding van de extensie. | Klanten op .NET Core 2.1 2.2 worden [Aanbevolen](https://github.com/aspnet/Announcements/issues/287) het meta-pakket micro soft. AspNetCore. app te gebruiken.|
|`AppAlreadyInstrumented:true` | Deze waarde kan ook worden veroorzaakt door de aanwezigheid van de bovenstaande dll-bestanden in de app-map van een vorige implementatie. | Reinig de app-map om er zeker van te zijn dat deze DLL-bestanden worden verwijderd. Controleer de bin-map van uw lokale app en de map Wwwroot op het App Service. (Ga als volgt te werk om de map wwwroot van uw App Service web-app te controleren: Advanced tools (kudu) > debug console > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Deze waarde geeft aan dat de uitbrei `Microsoft.AspNet.TelemetryCorrelation` ding verwijzingen naar in de toepassing heeft gedetecteerd en dat deze wordt teruggedraaid. | Verwijder de verwijzing.
|`AppContainsDiagnosticSourceAssembly**:true`|Deze waarde geeft aan dat de uitbrei `System.Diagnostics.DiagnosticSource` ding verwijzingen naar in de toepassing heeft gedetecteerd en dat deze wordt teruggedraaid.| Verwijder de verwijzing.
|`IKeyExists:false`|Deze waarde geeft aan dat de instrumentatie sleutel niet aanwezig is in de AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`,. Mogelijke oorzaken: de waarden zijn mogelijk per ongeluk verwijderd, verg eten de waarden in het Automation-script in te stellen, enzovoort. | Zorg ervoor dat de instelling aanwezig is in de App Service toepassings instellingen.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED en urlCompression worden niet ondersteund

Als u APPINSIGHTS_JAVASCRIPT_ENABLED = True gebruikt in gevallen waar inhoud is gecodeerd, kunnen er fouten optreden zoals: 

- 500 URL herschrijf fout
- 500,53 de module voor het herschrijven van de URL is een fout opgetreden in een bericht uitgaande herschrijf regels kunnen niet worden toegepast wanneer de inhoud van het HTTP-antwoord is gecodeerd (' gzip '). 

Dit komt doordat de instelling van de APPINSIGHTS_JAVASCRIPT_ENABLED toepassing wordt ingesteld op True en er tegelijkertijd inhouds codering wordt weer gegeven. Dit scenario wordt nog niet ondersteund. De tijdelijke oplossing is om APPINSIGHTS_JAVASCRIPT_ENABLED te verwijderen uit de toepassings instellingen. Dit betekent dat als er nog steeds een Java script-instrumentatie aan de client/browser zijde is vereist, hand matige SDK-verwijzingen nodig zijn voor uw webpagina's. Volg de [instructies](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) voor hand matige instrumentatie met de Java script SDK.

Bekijk de opmerkingen bij de [release](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)voor de meest recente informatie over de Application Insights agent/uitbrei ding.

### <a name="php-and-wordpress-are-not-supported"></a>PHP en WordPress worden niet ondersteund

PHP-en WordPress-sites worden niet ondersteund. Er is momenteel geen officieel ondersteunde SDK/agent voor bewaking aan server zijde van deze werk belastingen. Hand matig de client-side-trans acties op een PHP-of WordPress-site instrumenteren door de Java script-client toe te voegen aan uw webpagina's kan worden uitgevoerd met behulp van de [Java script SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

### <a name="connection-string-and-instrumentation-key"></a>Verbindings reeks en instrumentatie sleutel

Wanneer bewaking zonder code wordt gebruikt, is alleen de connection string vereist. We raden u echter aan om de instrumentatie sleutel in te stellen om achterwaartse compatibiliteit met oudere versies van de SDK te behouden wanneer hand matige instrumentatie wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
* [Voer de profiler uit in uw live app](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample): Azure Functions bewaken met Application Insights
* [Schakel diagnostische Azure-gegevens in](../platform/diagnostics-extension-to-application-insights.md) om te verzenden naar Application Insights.
* [Controleer metrische gegevens voor servicestatus](../platform/data-platform.md) om ervoor te zorgen dat de service beschikbaar is en reageert.
* [Ontvang waarschuwingsmeldingen](../platform/alerts-overview.md) wanneer er operationele gebeurtenissen plaatsvinden of metrische gegevens een drempelwaarde overschrijden.
* Gebruik [Application Insights voor JavaScript-apps en -webpagina's](javascript.md) om clienttelemetrie op te halen uit de browsers die een webpagina bezoeken.
* [Stel webtests voor beschikbaarheid in](monitor-web-app-availability.md) om te worden gewaarschuwd als uw site niet actief is.
