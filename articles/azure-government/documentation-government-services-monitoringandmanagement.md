---
title: Azure Government Controle en beheer | Microsoft Docs
description: Dit biedt een vergelijking van de functies en richt lijnen voor het ontwikkelen van toepassingen voor Azure Government.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: 38eb44e6355ddcb5ac78f5d8bb2008ea5a0f0cdf
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127938"
---
# <a name="azure-government-monitoring--management"></a>Azure Government Controle en beheer
In dit artikel vindt u een overzicht van de verschillen en overwegingen voor de bewakings-en beheer Services voor de Azure Government omgeving.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Advisor is algemeen verkrijgbaar in Azure Government.

Zie [open bare documentatie voor Advisor](../advisor/advisor-overview.md)voor meer informatie.

### <a name="variations"></a>Verschillen
De volgende Advisor-aanbevelingen zijn momenteel niet beschikbaar in Azure Government:

* Hoge beschikbaarheid
  * Configureer uw VPN-gateway naar actief-actief voor verbindings tolerantie
  * Azure Service Health waarschuwingen maken om een melding te ontvangen wanneer Azure-problemen van invloed zijn op u
  * Traffic Manager-eind punten voor tolerantie configureren
  * Voorlopig verwijderen gebruiken voor uw Azure Storage-account
* Prestaties
  * De prestaties en betrouw baarheid van App Service verbeteren
  * U kunt de DNS-tijd op uw Traffic Manager-profiel verlagen zodat er sneller een failover wordt uitgevoerd naar goede eind punten
  * Prestaties van SQL Data Warehouse verbeteren
  * Premium Storage gebruiken
  * Uw opslag account migreren naar Azure Resource Manager
* Kosten
  * Koop gereserveerde exemplaren van virtuele machines om geld te besparen op kosten voor betalen per gebruik
  * Oningerichte ExpressRoute-circuits elimineren
  * Inactieve virtuele netwerkgateways verwijderen of opnieuw configureren

De berekening die wordt gebruikt om te adviseren dat u de juiste grootte of het uitschakelen van gelaagde virtuele machines moet volgen, is als volgt in Azure Government:

Advisor bewaakt het gebruik van uw virtuele machines gedurende 7 dagen en identificeert virtuele machines met weinig gebruik. Virtuele machines worden beschouwd als laag gebruik als het CPU-gebruik 5% of minder is en het netwerk gebruik lager is dan 2% of als de huidige werk belasting kan worden aangepast aan de grootte van een kleinere virtuele machine. Als u meer wilt weten over het identificeren van gefactureerde virtuele machines, kunt u de regel voor het CPU-gebruik aanpassen op basis van een abonnement.

## <a name="automation"></a>Automatisering
Automation is over het algemeen beschikbaar in Azure Government.

Zie [open bare documentatie voor Automation](../automation/automation-intro.md)voor meer informatie.

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate is algemeen beschikbaar in Azure Government.

Zie [Azure migrate-documentatie](../migrate/migrate-overview.md)voor meer informatie.

### <a name="variations"></a>Verschillen
De volgende Azure Migrate-functies zijn momenteel niet beschikbaar in Azure Government:

* De functionaliteit van de afhankelijkheids visualisatie is niet beschikbaar in Azure Government omdat Azure Migrate afhankelijk is van Servicetoewijzing voor afhankelijkheids visualisatie die momenteel niet beschikbaar is in Azure Government.
* U kunt alleen evaluaties maken voor Azure Government als doel regio's en met behulp van Azure Government aanbiedingen.

## <a name="backup"></a>Back-up maken
Backup is algemeen beschikbaar in Azure Government.

Zie [Azure Government backup](documentation-government-services-backup.md)voor meer informatie.

## <a name="policy"></a>Beleid
Het beleid is algemeen beschikbaar in Azure Government.

Zie [Azure Policy](../governance/policy/overview.md) voor meer informatie.

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery is algemeen beschikbaar in Azure Government.

Zie [site Recovery Commercial documentation](../site-recovery/site-recovery-overview.md)(Engelstalig) voor meer informatie.

### <a name="variations"></a>Verschillen
De volgende Site Recovery-functies zijn momenteel niet beschikbaar in Azure Government:
* E-mailmelding

| Site Recovery | Klassiek | Resource Manager |
| --- | --- | --- |
| VMware/fysiek  | Algemene beschikbaarheid | Algemene beschikbaarheid |
| Hyper-V | Algemene beschikbaarheid | Algemene beschikbaarheid |
| Site-naar-site | Algemene beschikbaarheid | Algemene beschikbaarheid |

De volgende Url's voor Site Recovery verschillen in Azure Government:

| Open bare Azure | Azure Government | Opmerkingen |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*. hypervrecoverymanager.windowsazure.us | Toegang tot de Site Recovery-service |
| \*.backup.windowsazure.com  | \*. backup.windowsazure.us | Toegang tot de beveiligings service |
| \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Voor het opslaan van de VM-moment opnamen |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | MySQL downloaden |

## <a name="monitor"></a>Bewaken
Azure Monitor is algemeen beschikbaar in Azure Government.

Zie [commerciële documentatie bewaken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)voor meer informatie.

### <a name="variations"></a>Verschillen
In de volgende secties vindt u meer informatie over verschillen en tijdelijke oplossingen voor functies van Azure Monitor in Azure Government:

#### <a name="action-groups"></a>Actiegroepen
Actie groepen zijn algemeen beschikbaar in Azure Government zonder verschillen van commerciële Azure.   

#### <a name="activity-log-alerts"></a>Waarschuwingen voor activiteitenlogboeken
Waarschuwingen voor activiteiten logboeken zijn algemeen beschikbaar in Azure Government zonder verschillen van commerciële Azure.

#### <a name="alerts-experience"></a>Waarschuwings ervaring
De UI-ervaring voor Unified Alerts is beschikbaar voor metrische gegevens en logboek waarschuwingen in Azure Government.

#### <a name="autoscale"></a>Automatisch schalen
Automatisch schalen is algemeen beschikbaar in Azure Government.

Als u Power shell/ARM/REST-aanroepen gebruikt om instellingen op te geven, stelt u de "locatie" van de automatisch schalen in op "USGov Virginia" of "USGov Iowa". De resource waarop automatisch schalen is gericht, kan in elke regio bestaan. Hieronder ziet u een voor beeld van de instelling:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Als u geïnteresseerd bent in het implementeren van automatisch schalen op uw resources, gebruikt u Power shell/ARM/rest-aanroepen om de instellingen op te geven.

Zie [open bare documentatie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings)voor meer informatie over het gebruik van Power shell.

#### <a name="metrics"></a>Metrische gegevens
Metrische gegevens zijn algemeen beschikbaar in Azure Government. Multi-dimensionale meet waarden worden echter alleen ondersteund via de REST API. De mogelijkheid om [meerdimensionale metrische gegevens weer te geven](../azure-monitor/platform/metrics-charts.md) is in de preview-versie van de Azure Government Portal.

#### <a name="metric-alerts"></a>Metrische waarschuwingen
De eerste generatie metrische waarschuwingen is algemeen beschikbaar in zowel Azure Government als commerciële Azure. De eerste generatie wordt *waarschuwingen (klassiek)* genoemd. De tweede generatie metrische waarschuwingen (ook wel [Unified Alerts-ervaring](../azure-monitor/platform/alerts-overview.md)genoemd) is nu ook beschikbaar, maar met een gereduceerde set resource providers [vergeleken met de open bare Cloud](../azure-monitor/platform/alerts-metric-near-real-time.md). Meer worden toegevoegd na verloop van tijd. 

De resources die momenteel worden ondersteund in de ervaring voor het maken van de tweede generatie zijn:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Micro soft. EventGrid/domeinen
- Micro soft. EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft.Insights/components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Micro soft. Network/natGateways
- Micro soft. Network/privateEndpoints
- Micro soft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Micro soft. PowerBIDedicated/capaciteiten
- Micro soft. relay/naam ruimten
- Microsoft.ServiceBus/namespaces
- Micro soft. SQL/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

> [!NOTE]
> Het maken van metrische waarschuwings regels voor meerdere resources op Virtual Machines wordt **momenteel niet ondersteund**. Dit artikel wordt bijgewerkt zodra deze functionaliteit beschikbaar wordt.

U kunt nog steeds [klassieke waarschuwingen](../azure-monitor/platform/alerts-classic.overview.md) gebruiken voor resources die nog niet beschikbaar zijn in de tweede generatie van waarschuwingen. 

Wanneer u Power shell/ARM/rest-aanroepen gebruikt om metrische waarschuwingen te maken, moet u de "locatie" van de metrische waarschuwing instellen op "USGov Virginia" of "USGov Iowa". Hieronder ziet u een voor beeld van de instelling:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Zie [open bare documentatie](../azure-monitor/platform/powershell-quickstart-samples.md)voor meer informatie over het gebruik van Power shell.


## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Bewaking op basis van agent/uitbrei ding voor Azure-app Services wordt **momenteel niet ondersteund**. Zodra deze functionaliteit beschikbaar wordt, wordt dit artikel bijgewerkt.

In deze sectie wordt de aanvullende configuratie beschreven die is vereist voor het gebruik van Application Insights in Azure Government. Voor meer informatie over Azure Monitor en Application Insights het afhandelen van de [volledige documentatie](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Application Insights inschakelen voor ASP.NET & ASP.NET Core met Visual Studio

Op dit moment is een kleine hand matige tijdelijke oplossing vereist Azure Government voor de enige manier om Application Insights in te scha kelen via de traditionele **telemetrie add Applications Insights** -knop in Visual Studio. Klanten die het bijbehorende probleem ondervinden, kunnen fout berichten zien, zoals _' er is geen Azure-abonnement gekoppeld aan dit account_ ' of _' het geselecteerde abonnement biedt geen ondersteuning voor Application Insights, zelfs niet_ als de resource provider van `microsoft.insights` de status geregistreerd heeft voor het abonnement. U moet de volgende stappen uitvoeren om dit probleem te verhelpen:

1. Scha kelen tussen Visual Studio om [de Azure Government Cloud te richten](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Maak (of als al een bestaande set) de variabele voor de gebruikers omgeving voor AzureGraphApiVersion als volgt: (als u een gebruikers omgevings variabele wilt maken, gaat u naar **configuratie scherm** > **systeem** > **geavanceerde systeem instellingen** > **Geavanceerde** > **omgevings variabelen**.)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Breng de gewenste wijzigingen aan in het Application Insights SDK-eind punt voor een [ASP.net](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) of [ASP.net core](#aspnet-core) afhankelijk van het project type.

### <a name="snapshot-debugger"></a>Snapshot Debugger

Snapshot Debugger is nu beschikbaar voor Azure Government klanten. Als u Snapshot Debugger wilt gebruiken, moet u ervoor zorgen dat u [Snapshot Collector versie 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) of hoger gebruikt. Volg vervolgens gewoon de standaard [documentatie van Snapshot debugger](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>Wijzigingen in SDK-eind punten

Als u gegevens van Application Insights naar de Azure Government regio wilt verzenden, moet u de standaard eindpunt adressen wijzigen die door de Application Insights Sdk's worden gebruikt. Elke SDK vereist iets verschillende wijzigingen.

### <a name="net-with-applicationinsightsconfig"></a>.NET met applicationinsights. config

> [!NOTE]
> Het bestand applicationinsights. config wordt automatisch overschreven wanneer een SDK-upgrade wordt uitgevoerd. Nadat u een SDK-upgrade hebt uitgevoerd, moet u de regio-specifieke eindpunt waarden opnieuw invoeren.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Wijzig het bestand appSettings. json in uw project als volgt om het belangrijkste eind punt aan te passen:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

De waarden voor dynamische metrische gegevens en het eind punt voor de profiel query kunnen alleen worden ingesteld via code. Als u de standaard waarden voor alle eindpunt waarden via code wilt overschrijven, moet u de volgende wijzigingen aanbrengen in de `ConfigureServices` methode van het `Startup.cs`-bestand:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

Installeer de volgende pakketten in uw functie project:

- Micro soft. ApplicationInsights versie 2.10.0
- Micro soft. ApplicationInsights. PerfCounterCollector versie 2.10.0
- Micro soft. ApplicationInsights. WindowsServer. TelemetryChannel-versie 2.10.0

Daarnaast kunt u de opstart code voor de functie toepassing ook toevoegen (of wijzigen):

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Wijzig het applicationinsights. XML-bestand om het standaard eindpunt adres te wijzigen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Wijzig het `application.properties`-bestand en voeg het volgende toe:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

De eind punten kunnen ook worden geconfigureerd via omgevings variabelen:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>Javascript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Firewall-uitzonde ringen

De Azure-toepassing Insights-service gebruikt een aantal IP-adressen. Mogelijk moet u deze adressen weten als de app die u bewaakt, wordt gehost achter een firewall.

> [!NOTE]
> Hoewel deze adressen statisch zijn, is het mogelijk dat we deze van tijd tot tijd moeten wijzigen. Alle Application Insights verkeer staat voor uitgaand verkeer met uitzonde ring van beschikbaarheids bewaking en webhooks, waarvoor binnenkomende firewall regels zijn vereist.

### <a name="outgoing-ports"></a>Uitgaande poorten
U moet een aantal uitgaande poorten in de firewall van uw server openen zodat de Application Insights SDK en/of Status Monitor gegevens naar de portal kunnen verzenden:

| Doel | URL | IP | Poorten |
| --- | --- | --- | --- |
| Telemetrie | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Azure Monitor-Logboeken is algemeen beschikbaar in Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Verschillen

* Oplossingen die beschikbaar zijn in Azure Government zijn onder andere:
  * [Netwerkprestatiemeter (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) -NPM is een netwerk bewakings oplossing op basis van de Cloud voor open bare en hybride Cloud omgevingen. Organisaties gebruiken NPM om de netwerk beschikbaarheid te bewaken over on-premises en Cloud omgevingen.  Monitor voor eind punten: een subnetwerk van NPM, controleert de verbinding met de netwerken van toepassingen.

De volgende Azure Monitor-logboeken-functies en-oplossingen zijn momenteel niet beschikbaar in Azure Government.

* Oplossingen in de preview-versie van Microsoft Azure, waaronder:
  * Servicetoewijzing
  * Windows 10 Upgrade Analytics-oplossing
  * Application Insights oplossing
  * Analyse oplossing voor Azure-netwerk beveiligings groep
  * Azure Automation Analytics-oplossing
  * Key Vault-analyse oplossing
* Oplossingen en functies waarvoor updates nodig zijn voor on-premises software, waaronder:
  * Surface Hub oplossing
* Functies die beschikbaar zijn in de preview-versie van wereld wijd Azure, waaronder:
  * Exporteren van gegevens naar Power BI
* Azure-metrische gegevens en Azure Diagnostics

De Url's voor Azure Monitor logboeken verschillen in Azure Government:

| Open bare Azure | Azure Government | Opmerkingen |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portal van Log Analytics-werk ruimten |
| *workspaceId*. ODS.opinsights.Azure.com |*workspaceId*. ODS.opinsights.Azure.us |[Gegevensverzamelaar-API](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*. ods.opinsights.azure.us |Agent communicatie- [firewall instellingen configureren](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*. oms.opinsights.azure.us |Agent communicatie- [firewall instellingen configureren](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*. blob.core.usgovcloudapi.net |Agent communicatie- [firewall instellingen configureren](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Geavanceerde analyse Portal- [firewall instellingen configureren](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Geavanceerde analyse Portal- [firewall instellingen configureren](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Geavanceerde analyse Portal- [firewall instellingen configureren](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Azure Automation- [firewall-instellingen configureren](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| N.v.t. | *. usgovtrafficmanager.net | Azure Traffic Manager- [firewall instellingen configureren](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

De volgende Azure Monitor-logboek functies gedragen zich op een andere manier in Azure Government:

* Als u uw System Center Operations Manager-beheer groep wilt verbinden met Azure Monitor-logboeken, moet u bijgewerkte Management Packs downloaden en importeren.
  + System Center Operations Manager 2016
    1. Installeer [Update pakket 2 voor System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importeer de Management Packs die zijn opgenomen als onderdeel van update pakket 2 in Operations Manager. Zie [How to import a Operations Manager Management Pack](https://technet.microsoft.com/library/hh212691.aspx)(Engelstalig) voor meer informatie over het importeren van een Management Pack van een schijf.
    3. Volg de stappen in [connect Operations Manager to Azure monitor logs](../azure-monitor/platform/om-agents.md)om Operations Manager aan Azure monitor-logboeken te koppelen.
  + System Center Operations Manager 2012 R2 UR3 (of hoger)/Operations Manager 2012 SP1 UR7 (of hoger)
    1. Down load en sla de [bijgewerkte Management Packs](https://go.microsoft.com/fwlink/?LinkId=828749)op.
    2. Pak het bestand uit dat u hebt gedownload.
    3. Importeer de Management Packs in Operations Manager. Zie [How to import a Operations Manager Management Pack](https://technet.microsoft.com/library/hh212691.aspx)(Engelstalig) voor meer informatie over het importeren van een Management Pack van een schijf.
    4. Volg de stappen in [connect Operations Manager to Azure monitor logs](../azure-monitor/platform/om-agents.md)om Operations Manager aan Azure monitor-logboeken te koppelen.

* Zie [Connect Configuration Manager to Azure monitor](../azure-monitor/platform/collect-sccm.md)voor meer informatie over het gebruik van computer groepen van Configuration Manager.

### <a name="frequently-asked-questions"></a>Veelgestelde vragen
* Kan ik gegevens migreren van Azure Monitor-Logboeken in Microsoft Azure naar Azure Government?
  * Nee. Het is niet mogelijk om gegevens of uw werk ruimte te verplaatsen van Microsoft Azure naar Azure Government.
* Kan ik scha kelen tussen Microsoft Azure en Azure Government werk ruimten van de Operations Management Suite-Portal?
  * Nee. De portals voor Microsoft Azure en Azure Government zijn gescheiden en delen geen informatie.

Zie voor meer informatie [Azure monitor logboeken van de open bare documentatie](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Scheduler
Zie de [documentatie van Azure scheduler](../scheduler/index.md)voor meer informatie over deze service en hoe u deze kunt gebruiken.

## <a name="azure-portal"></a>Azure-portal
[Hier](https://portal.azure.us)kunt u de Azure Government-Portal openen.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Zie [Azure Resource Manager-documentatie](../azure-resource-manager/management/overview.md)voor meer informatie over deze service en hoe u deze kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen
* Abonneren op de [Azure Government blog](https://blogs.msdn.microsoft.com/azuregov/)
* Hulp krijgen bij het Stack Overflow met behulp van [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Feedback geven of nieuwe functies aanvragen via het [Feedback forum van Azure Government](https://feedback.azure.com/forums/558487-azure-government)
