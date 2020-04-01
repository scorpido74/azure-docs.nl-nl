---
title: Prestaties azure-app-services bewaken | Microsoft Documenten
description: Toepassingsprestatiebewaking voor Azure-app-services. Laad- en responstijd in grafieken, afhankelijkheidsgegevens en stel waarschuwingen in op prestaties.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d2134e059a446c18108e8dd16bcc74504b42b15a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437201"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service-prestaties bewaken

Het inschakelen van bewaking op uw ASP.NET en ASP.NET Core-gebaseerde webtoepassingen die worden uitgevoerd op [Azure App Services,](https://docs.microsoft.com/azure/app-service/) is nu eenvoudiger dan ooit. Waar u voorheen handmatig een site-extensie moest installeren, is de nieuwste extensie/agent nu standaard ingebouwd in de app-serviceafbeelding. In dit artikel u application insights-monitoring inschakelen en biedt u voorlopige richtlijnen voor het automatiseren van het proces voor grootschalige implementaties.

> [!NOTE]
> Het handmatig toevoegen van een Application Insights-site-extensie via **Development Tools** > **Extensions** wordt afgeschaft. Deze methode van extensie-installatie was afhankelijk van handmatige updates voor elke nieuwe versie. De nieuwste stabiele versie van de extensie is nu [vooraf geïnstalleerd](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) als onderdeel van de App Service-afbeelding. De bestanden bevinden `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` zich in en worden automatisch bijgewerkt met elke stabiele release. Als u de op agent gebaseerde instructies volgt om onderstaande controle in te schakelen, wordt de afgeschafte extensie automatisch voor u verwijderd.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Er zijn twee manieren om toepassingsbewaking voor gehoste Azure-toepassingen in te schakelen:

* **Agent-based application monitoring** (ApplicationInsightsAgent).  
    * Deze methode is het gemakkelijkst in te schakelen en er is geen geavanceerde configuratie vereist. Het wordt vaak aangeduid als "runtime" monitoring. Voor Azure App Services raden we aan om dit niveau van monitoring minimaal in te schakelen en vervolgens op basis van uw specifieke scenario te beoordelen of meer geavanceerde monitoring via handmatige instrumentatie nodig is.

* **Handmatig instrumenteren van de toepassing door middel van code** door het installeren van de Application Insights SDK.

    * Deze aanpak is veel meer aanpasbaar, maar het vereist [het toevoegen van een afhankelijkheid van de Application Insights SDK NuGet-pakketten.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) Deze methode betekent ook dat u de updates voor de nieuwste versie van de pakketten zelf moet beheren.

    * Als u aangepaste API-aanroepen moet maken om gebeurtenissen/afhankelijkheden bij te houden die niet standaard zijn vastgelegd met agentgebaseerde bewaking, moet u deze methode gebruiken. Bekijk de [API voor aangepaste gebeurtenissen en statistieken artikel](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) voor meer informatie. Dit is momenteel ook de enige ondersteunde optie voor Linux-gebaseerde workloads.

> [!NOTE]
> Als zowel agent-gebaseerde monitoring als handmatige SDK-gebaseerde instrumentatie wordt gedetecteerd, worden alleen de handmatige instrumentatie-instellingen gehonoreerd. Dit is om te voorkomen dat dubbele gegevens worden verzonden. Bekijk de [sectie probleemoplossing](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) hieronder voor meer informatie hierover.

## <a name="enable-agent-based-monitoring"></a>Agentgebaseerde bewaking inschakelen

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> De combinatie van APPINSIGHTS_JAVASCRIPT_ENABLED en urlCompressie wordt niet ondersteund. Zie voor meer informatie de uitleg in de [sectie probleemoplossing](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Selecteer Toepassingsinzichten** in het Azure-configuratiescherm voor uw app-service.

    ![Kies Application Insights onder Instellingen](./media/azure-web-apps/settings-app-insights-01.png)

   * Kies ervoor om een nieuwe bron te maken, tenzij u al een Application Insights-bron voor deze toepassing hebt ingesteld. 

     > [!NOTE]
     > Wanneer u op **OK** klikt om de nieuwe bron te maken, wordt u gevraagd om **bewakingsinstellingen toe**te passen. Als **u Doorgaan** selecteert, wordt uw nieuwe Application Insights-bron gekoppeld aan uw app-service, waardoor uw **app-service opnieuw wordt opgestart.** 

     ![Uw web-app instrumenteren](./media/azure-web-apps/create-resource-01.png)

2. Nadat u hebt opgegeven welke resource u wilt gebruiken, u kiezen hoe u wilt dat toepassingsinzichten gegevens verzamelen per platform voor uw toepassing. ASP.NET app-monitoring is standaard met twee verschillende niveaus van verzameling.

    ![Opties per platform kiezen](./media/azure-web-apps/choose-options-new.png)

   * .NET **Basic collectie** niveau biedt essentiële single-instance APM mogelijkheden.

   * .NET **Aanbevolen verzamelingsniveau:**
       * Hiermee voegt u CPU-, geheugen- en I/O-gebruikstrends toe.
       * Correleert microservices over de grenzen van aanvraag/afhankelijkheid.
       * Verzamelt gebruikstrends en maakt correlatie mogelijk van beschikbaarheidsresultaten naar transacties.
       * Verzamelt uitzonderingen die niet zijn afgehandeld door het hostproces.
       * Verbetert de nauwkeurigheid van apm-statistieken onder belasting wanneer steekproeven worden gebruikt.

3. Om instellingen zoals sampling te configureren, die u eerder via het bestand applicationinsights.config kon beheren, u nu met dezelfde instellingen communiceren via toepassingsinstellingen met een bijbehorend voorvoegsel. 

    * Als u bijvoorbeeld het oorspronkelijke bemonsteringspercentage wilt wijzigen, `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` u `100`een toepassingsinstelling maken van: en een waarde van.

    * Voor de lijst met ondersteunde instellingen voor adaptieve sampling telemetrieprocessor u de [code](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) en [bijbehorende documentatie](https://docs.microsoft.com/azure/azure-monitor/app/sampling)raadplegen.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

De volgende versies van .NET Core worden ondersteund: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

Het volledige framework van .NET Core, self-contained deployment en Linux-gebaseerde applicaties worden momenteel **niet ondersteund** met agent/extensie gebaseerde monitoring. ([Handmatige instrumentatie](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via code werkt in alle vorige scenario's.)

1. **Selecteer Toepassingsinzichten** in het Azure-configuratiescherm voor uw app-service.

    ![Kies Application Insights onder Instellingen](./media/azure-web-apps/settings-app-insights-01.png)

   * Kies ervoor om een nieuwe bron te maken, tenzij u al een Application Insights-bron voor deze toepassing hebt ingesteld. 

     > [!NOTE]
     > Wanneer u op **OK** klikt om de nieuwe bron te maken, wordt u gevraagd om **bewakingsinstellingen toe**te passen. Als **u Doorgaan** selecteert, wordt uw nieuwe Application Insights-bron gekoppeld aan uw app-service, waardoor uw **app-service opnieuw wordt opgestart.** 

     ![Uw web-app instrumenteren](./media/azure-web-apps/create-resource-01.png)

2. Nadat u hebt opgegeven welke resource u wilt gebruiken, u kiezen hoe u wilt dat Application Insights gegevens verzamelt per platform voor uw toepassing. .NET Core biedt **aanbevolen verzameling** of **uitgeschakeld** voor .NET Core 2.0, 2.1, 2.2 en 3.0.

    ![Opties per platform kiezen](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Selecteer in uw app-service-web-app onder **Instellingen** > **de optie Toepassingsinzichten** > **inschakelen**. Node.js agent based monitoring is momenteel in preview.

# <a name="java"></a>[Java](#tab/java)

Java App Service-gebaseerde webapplicaties ondersteunen momenteel geen automatische agent/extensiegebaseerde monitoring. Om monitoring voor uw Java-applicatie in te schakelen, moet u [uw toepassing handmatig instrumenteren.](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)

# <a name="python"></a>[Python](#tab/python)

Python App Service-gebaseerde webtoepassingen ondersteunen momenteel geen automatische agent/extensiegebaseerde monitoring. Om monitoring voor uw Python-toepassing in te schakelen, moet u [uw toepassing handmatig instrumenteren.](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

---

## <a name="enable-client-side-monitoring"></a>Client-side monitoring inschakelen

# <a name="net"></a>[.NET](#tab/net)

Client-side monitoring is opt-in voor ASP.NET. Ga als volgt te werk om client-side monitoring in te schakelen:

* Selecteer **Instellingen** >** **Toepassingsinstellingen****
   * Voeg onder Toepassingsinstellingen een nieuwe naam en **waarde** **voor app-instellingen** toe:

     Naam:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Waarde:`true`

   * Sla de instellingen op met **Opslaan** en start de app opnieuw met **Opnieuw opstarten**.

![Schermafbeelding van de gebruikersinterface van toepassingsinstellingen](./media/azure-web-apps/appinsights-javascript-enabled.png)

Als u de bewaking aan de clientzijde wilt uitschakelen, verwijdert u het bijbehorende sleutelwaardepaar uit de toepassingsinstellingen of stelt u de waarde in op false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Clientbewaking is **standaard ingeschakeld** voor .NET Core-apps met **aanbevolen verzameling,** ongeacht of de app-instelling 'APPINSIGHTS_JAVASCRIPT_ENABLED' aanwezig is.

Als u om de een of andere reden de bewaking aan de clientzijde wilt uitschakelen:

* Toepassingsinstellingen **instellingen** > **selecteren**
   * Voeg onder Toepassingsinstellingen een nieuwe naam en **waarde** **voor app-instellingen** toe:

     Naam:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Waarde:`false`

   * Sla de instellingen op met **Opslaan** en start de app opnieuw met **Opnieuw opstarten**.

![Schermafbeelding van de gebruikersinterface van toepassingsinstellingen](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Als u clientbewaking voor uw Node.js-toepassing wilt inschakelen, moet u [de JavaScript SDK aan uw toepassing handmatig toevoegen aan uw client.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="java"></a>[Java](#tab/java)

Als u clientbewaking voor uw Java-toepassing wilt inschakelen, moet u [de JavaScript SDK aan uw clientzijde handmatig aan uw toepassing toevoegen.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="python"></a>[Python](#tab/python)

Als u clientbewaking voor uw Python-toepassing wilt inschakelen, moet u [de JavaScript SDK aan uw toepassing handmatig toevoegen aan uw client.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

---

## <a name="automate-monitoring"></a>Monitoring automatiseren

Om telemetrieverzameling met Application Insights mogelijk te maken, hoeven alleen de toepassingsinstellingen te worden ingesteld:

   ![App-servicetoepassingsinstellingen met beschikbare instellingen voor Toepassingsstatistieken](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definities van toepassingsinstellingen

|Naam van app-instelling |  Definitie | Waarde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hoofdextensie, die runtime-monitoring regelt. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Alleen in de standaardmodus zijn essentiële functies ingeschakeld om optimale prestaties te verzekeren. | `default` of `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Hiermee bepaalt u of `InstrumentationEngine` de binaire herschrijvende engine wordt ingeschakeld. Deze instelling heeft gevolgen voor de prestaties en heeft gevolgen voor de koude start/opstarttijd. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Hiermee bepaalt u of SQL-& Azure-tabeltekst wordt vastgelegd samen met de afhankelijkheidsoproepen. Prestatiewaarschuwing: de koude opstarttijd van de toepassing wordt beïnvloed. Deze instelling `InstrumentationEngine`vereist de . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App-servicetoepassingsinstellingen met Azure Resource Manager

Toepassingsinstellingen voor App Services kunnen worden beheerd en geconfigureerd met [Azure Resource Manager-sjablonen.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Deze methode kan worden gebruikt bij het implementeren van nieuwe App Service-resources met Azure Resource Manager-automatisering of voor het wijzigen van de instellingen van bestaande resources.

De basisstructuur van de toepassingsinstellingen JSON voor een app-service vindt u:

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

Voor een voorbeeld van een Azure Resource Manager-sjabloon met toepassingsinstellingen die zijn geconfigureerd voor toepassingsinzichten, kan deze [sjabloon](https://github.com/Andrew-MSFT/BasicImageGallery) nuttig zijn, met name de sectie die begint op [regel 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatiseer het maken van een Application Insights-bron en een koppeling naar uw nieuw gemaakte App-service.

Als u een Azure Resource Manager-sjabloon wilt maken met alle standaardinstellingen voor toepassingsinzichten die zijn geconfigureerd, begint u het proces alsof u een nieuwe web-app wilt maken waarmee Application Insights is ingeschakeld.

**Opties voor automatisering selecteren**

   ![Menu app-webapp maken](./media/azure-web-apps/create-web-app.png)

Met deze optie wordt de nieuwste Azure Resource Manager-sjabloon gegenereerd met alle vereiste instellingen geconfigureerd.

  ![Sjabloon app-webapp](./media/azure-web-apps/arm-template.png)

Hieronder vindt u een voorbeeld, vervang alle exemplaren van `AppMonitoredSite` uw sitenaam:

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

### <a name="enabling-through-powershell"></a>Inschakelen via PowerShell

Om de toepassingsbewaking via PowerShell mogelijk te maken, hoeven alleen de onderliggende toepassingsinstellingen te worden gewijzigd. Hieronder vindt u een voorbeeld, waarmee toepassingsbewaking voor een website genaamd "AppMonitoredSite" in de brongroep "AppMonitoredRG" kan worden verzonden en gegevens kunnen worden verzonden naar de instrumentatiesleutel "012345678-abcd-ef01-2345-6789abcd".

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

## <a name="upgrade-monitoring-extensionagent"></a>Extensie/agent voor upgradebewaking

### <a name="upgrading-from-versions-289-and-up"></a>Upgraden van versies 2.8.9 en hoger

Upgraden vanaf versie 2.8.9 gebeurt automatisch, zonder extra acties. De nieuwe monitoring bits worden geleverd op de achtergrond van de doelgroep app service, en op de toepassing opnieuw op te starten zullen ze worden opgehaald.

Om te controleren welke versie van de extensie u uitvoert bezoek`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Schermafbeelding van urlpadhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade van versies 1.0.0 - 2.6.5

Vanaf versie 2.8.9 wordt de vooraf geïnstalleerde site-extensie gebruikt. Als u een eerdere versie bent, u op twee manieren updaten:

* [Upgrade door in te schakelen via de portal.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) (Zelfs als u de toepassingsinsights-extensie voor Azure App-service hebt geïnstalleerd, wordt de knop Gebruikersinterface alleen **ingeschakeld** weergegeven. Achter de schermen wordt de oude extensie van de privésite verwijderd.)

* [Upgrade via PowerShell:](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)

    1. Stel de toepassingsinstellingen in om de vooraf geïnstalleerde site-extensie ApplicationInsightsAgent in te schakelen. Zie [Inschakelen via powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Verwijder handmatig de extensie voor privésite met de naam Application Insights-extensie voor Azure App Service.

Als de upgrade wordt uitgevoerd vanaf een versie vóór 2.5.1, controleert u of de applicationInsigths dlls worden verwijderd uit de map met [toepassingsopslaglocatie, zie stappen voor het oplossen van problemen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Problemen oplossen

Hieronder vindt u onze stapsgewijze handleiding voor het oplossen van problemen voor extensie-/agentgebaseerde monitoring voor .NET- en .NET Core-gebaseerde toepassingen die worden uitgevoerd op Azure App Services.

> [!NOTE]
> Java-toepassingen worden alleen ondersteund op Azure App Services via handmatige SDK-gebaseerde instrumentatie en daarom zijn de onderstaande stappen niet van toepassing op deze scenario's.

1. Controleer of de toepassing `ApplicationInsightsAgent`wordt gecontroleerd via .
    * Controleer `ApplicationInsightsAgent_EXTENSION_VERSION` of de app-instelling is ingesteld op een waarde van "~2".
2. Zorg ervoor dat de toepassing voldoet aan de vereisten die moeten worden gecontroleerd.
    * Ga naar `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Schermafbeelding https://yoursitename.scm.azurewebsites/applicationinsights van de pagina Resultaten](./media/azure-web-apps/app-insights-sdk-status.png)

    * Bevestigen dat `Application Insights Extension Status` de is`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Als deze niet wordt uitgevoerd, volgt u de [bewakingsinstructies van Application Insights inschakelen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Controleer of de statusbron bestaat en eruit ziet als:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Als er geen vergelijkbare waarde aanwezig is, betekent dit dat de toepassing momenteel niet wordt uitgevoerd of niet wordt ondersteund. Om ervoor te zorgen dat de toepassing wordt uitgevoerd, probeert u handmatig naar de url/toepassingseindpunten van de toepassing te gaan, waardoor de runtime-informatie beschikbaar kan komen.

    * Bevestigen `IKeyExists` dat dit`true`
        * Als het `false`is, toe te voegen `APPINSIGHTS_INSTRUMENTATIONKEY` en `APPLICATIONINSIGHTS_CONNECTION_STRING` met uw ikey guid aan uw applicatie-instellingen.

    * Controleer of er geen `AppAlreadyInstrumented`vermeldingen `AppContainsDiagnosticSourceAssembly`zijn `AppContainsAspNetTelemetryCorrelationAssembly`voor , en .
        * Als een van deze vermeldingen bestaat, verwijdert u `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource`de `Microsoft.AspNet.TelemetryCorrelation`volgende pakketten uit uw toepassing: , , en .

De onderstaande tabel geeft een meer gedetailleerde uitleg van wat deze waarden betekenen, de onderliggende oorzaken ervan en aanbevolen oplossingen:

|Probleemwaarde|Uitleg|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Deze waarde geeft aan dat de extensie heeft gedetecteerd dat een bepaald aspect van de SDK al aanwezig is in de toepassing en een back-off zal zijn. Het kan te wijten `System.Diagnostics.DiagnosticSource`zijn `Microsoft.AspNet.TelemetryCorrelation`aan een verwijzing naar , , of`Microsoft.ApplicationInsights`  | Verwijder de verwijzingen. Sommige van deze verwijzingen worden standaard toegevoegd aan bepaalde Visual Studio-sjablonen en `Microsoft.ApplicationInsights`oudere versies van Visual Studio kunnen verwijzingen toevoegen naar .
|`AppAlreadyInstrumented:true` | Als de toepassing zich richt op .NET Core 2.1 of 2.2 en verwijst naar [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-pakket, dan brengt het Application Insights en extensie zal back-off. | Klanten op .NET Core 2.1,2.2 [wordt aangeraden](https://github.com/aspnet/Announcements/issues/287) om in plaats daarvan Microsoft.AspNetCore.App meta-pakket te gebruiken.|
|`AppAlreadyInstrumented:true` | Deze waarde kan ook worden veroorzaakt door de aanwezigheid van de bovenstaande dlls in de app-map van een eerdere implementatie. | Maak de map van de app schoon om ervoor te zorgen dat deze dlls worden verwijderd. Controleer zowel de opslaglocatiemap van uw lokale app als de map wwwroot in de App-service. (Om de wwwroot directory van uw App Service web app te controleren: Advanced Tools (Kudu) > Debug console > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Deze waarde geeft aan dat `Microsoft.AspNet.TelemetryCorrelation` extensie verwijzingen naar in de toepassing heeft gedetecteerd en een back-off zal zijn. | Verwijder de verwijzing.
|`AppContainsDiagnosticSourceAssembly**:true`|Deze waarde geeft aan dat `System.Diagnostics.DiagnosticSource` extensie verwijzingen naar in de toepassing heeft gedetecteerd en een back-off zal zijn.| Verwijder de verwijzing.
|`IKeyExists:false`|Deze waarde geeft aan dat de instrumentatiesleutel `APPINSIGHTS_INSTRUMENTATIONKEY`niet aanwezig is in app-instelling. Mogelijke oorzaken: De waarden kunnen per ongeluk zijn verwijderd, vergeten om de waarden in automatisering script, enz. | Controleer of de instelling aanwezig is in de instellingen van de App Service-toepassing.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED en urlCompressie wordt niet ondersteund

Als u APPINSIGHTS_JAVASCRIPT_ENABLED=true gebruikt in gevallen waarin inhoud is gecodeerd, u fouten krijgen zoals: 

- Fout herschrijven van 500 URL
- 500.53 URL herschrijven module fout met bericht Uitgaande herschrijfregels kunnen niet worden toegepast wanneer de inhoud van het HTTP-antwoord is gecodeerd ('gzip'). 

Dit is te wijten aan de APPINSIGHTS_JAVASCRIPT_ENABLED applicatie-instelling wordt ingesteld op true en content-encoding aanwezig zijn op hetzelfde moment. Dit scenario wordt nog niet ondersteund. De tijdelijke oplossing is om APPINSIGHTS_JAVASCRIPT_ENABLED uit de toepassingsinstellingen te verwijderen. Helaas betekent dit dat als javascript-instrumentatie aan de client/browser nog steeds vereist is, handmatige SDK-verwijzingen nodig zijn voor uw webpagina's. Volg de [instructies](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) voor handmatiginstrumentatie met de JavaScript SDK.

Voor de laatste informatie over de Application Insights agent/extensie, bekijk de [release notes](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP en WordPress worden niet ondersteund

PHP- en WordPress-sites worden niet ondersteund. Er is momenteel geen officieel ondersteunde SDK / agent voor server-side monitoring van deze workloads. Het handmatig instrumenteren van client-side transacties op een PHP- of WordPress-site door het javascript aan de clientzijde toe te voegen aan uw webpagina's kan echter worden uitgevoerd met behulp van de [JavaScript SDK.](https://docs.microsoft.com/azure/azure-monitor/app/javascript) 

## <a name="next-steps"></a>Volgende stappen
* [Voer de profiler uit in uw live app](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample): Azure Functions bewaken met Application Insights
* [Schakel diagnostische Azure-gegevens in](../platform/diagnostics-extension-to-application-insights.md) om te verzenden naar Application Insights.
* [Controleer metrische gegevens voor servicestatus](../platform/data-platform.md) om ervoor te zorgen dat de service beschikbaar is en reageert.
* [Ontvang waarschuwingsmeldingen](../platform/alerts-overview.md) wanneer er operationele gebeurtenissen plaatsvinden of metrische gegevens een drempelwaarde overschrijden.
* Gebruik [Application Insights voor JavaScript-apps en -webpagina's](javascript.md) om clienttelemetrie op te halen uit de browsers die een webpagina bezoeken.
* [Stel webtests voor beschikbaarheid in](monitor-web-app-availability.md) om te worden gewaarschuwd als uw site niet actief is.
