---
title: IP-adresverzameling azure Application Insights | Microsoft Documenten
description: Inzicht in hoe IP-adressen en geolocatie worden behandeld met Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656514"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolocatie en behandeling van IP-adres

In dit artikel wordt uitgelegd hoe geolocatie-lookup en IP-adresverwerking plaatsvinden in Application Insights, samen met hoe u het standaardgedrag wijzigen.

## <a name="default-behavior"></a>Standaardgedrag

Standaard worden IP-adressen tijdelijk verzameld, maar niet opgeslagen in Application Insights. Het basisproces verloopt als volgt:

IP-adressen worden verzonden naar Application Insights als onderdeel van telemetriegegevens. Bij het bereiken van het opnameeindpunt in Azure wordt het IP-adres gebruikt om een geolocatie-lookup uit te voeren met Behulp van [GeoLite2 van MaxMind.](https://dev.maxmind.com/geoip/geoip2/geolite2/) De resultaten van deze opzoek worden `client_City`gebruikt `client_StateOrProvince` `client_CountryOrRegion`om de volgende velden te vullen , . Op dit moment wordt het IP-adres verwijderd en `0.0.0.0` naar het `client_IP` veld geschreven.

* Browsertelemetrie: We verzamelen tijdelijk het IP-adres van de afzender. Het IP-adres wordt berekend aan de orde van de dag door het innameeindpunt.
* Servertelemetrie: De module Application Insights verzamelt tijdelijk het IP-adres van de client. Het wordt niet `X-Forwarded-For` verzameld als is ingesteld.

Dit gedrag is door het ontwerp om onnodige verzameling van persoonlijke gegevens te voorkomen. Waar mogelijk raden wij u aan het verzamelen van persoonsgegevens te vermijden. 

## <a name="overriding-default-behavior"></a>Overschrijvend standaardgedrag

Hoewel het standaardgedrag het verzamelen van persoonlijke gegevens tot een minimum moet beperken, bieden we nog steeds de flexibiliteit om IP-adresgegevens te verzamelen en op te slaan. Voordat we ervoor kiezen om persoonlijke gegevens zoals IP-adressen op te slaan, raden we u ten zeerste aan om te controleren of dit niet in overeenstemming is met nalevingsvereisten of lokale voorschriften waaraan u mogelijk onderworpen bent. Raadpleeg de [richtlijnen voor persoonsgegevens voor](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)meer informatie over de verwerking van persoonsgegevens in Application Insights.

## <a name="storing-ip-address-data"></a>IP-adresgegevens opslaan

Om IP-verzameling en -opslag `DisableIpMasking` mogelijk te maken, moet `true`de eigenschap van de component Application Insights worden ingesteld op . Deze eigenschap kan worden ingesteld via Azure Resource Manager-sjablonen of door de REST-API aan te roepen. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal 

Als u alleen het gedrag voor één Application Insights-bron hoeft te wijzigen, u dit het gemakkelijkst bereiken via de Azure-portal.  

1. Ga naar de bron Application Insights > **Exporttemplate Instellingen** > **exporteren** 

    ![Sjabloon exporteren](media/ip-collection/export-template.png)

2. Selecteren **Implementeren**

    ![Knop Implementeren gemarkeerd in het rood](media/ip-collection/deploy.png)

3. Selecteer **Sjabloon bewerken**. (Als uw sjabloon extra eigenschappen of bronnen heeft die niet in deze voorbeeldsjabloon worden weergegeven, gaat u voorzichtig te werk om ervoor te zorgen dat alle resources de implementatie van de sjabloon accepteren als een incrementele wijziging/update.)

    ![Sjabloon bewerken](media/ip-collection/edit-template.png)

4. Breng de volgende wijzigingen aan in de json voor uw resource en klik op **Opslaan:**

    ![Screenshot voegt een komma toe na "IbizaAIExtension" en voeg hieronder een nieuwe regel toe met "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Als u een fout ondervindt met de tekst: ** _de resourcegroep bevindt zich op een locatie die niet wordt ondersteund door een of meer bronnen in de sjabloon. Kies een andere resourcegroep._** Selecteer tijdelijk een andere resourcegroep in de vervolgkeuzelijst en selecteer vervolgens opnieuw de oorspronkelijke resourcegroep om de fout op te lossen.

5. Selecteer **Akkoord akkoord** > **gaan .** 

    ![Sjabloon bewerken](media/ip-collection/purchase.png)

    In dit geval wordt er niets nieuws gekocht, we werken alleen de config van de bestaande Application Insights-bron bij.

6. Zodra de implementatie is voltooid nieuwe telemetrie gegevens zullen worden opgenomen.

    Als u de sjabloon opnieuw zou selecteren en bewerken, ziet u alleen de standaardsjabloon en ziet u uw nieuwe eigenschap en de bijbehorende waarde niet. Als u geen IP-adresgegevens ziet en `"DisableIpMasking": true` wilt bevestigen dat dit is ingesteld. Voer de volgende PowerShell `Fabrikam-dev` uit: (Vervangen door de juiste naam van de resource- en resourcegroep.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Een lijst met eigenschappen wordt hierdoor geretourneerd. Een van de `DisableIpMasking: true`eigenschappen moet lezen. Als u de PowerShell uitvoert voordat u de nieuwe eigenschap implementeert met Azure Resource Manager, bestaat de eigenschap niet.

### <a name="rest-api"></a>Rest-API

De [Rest API](https://docs.microsoft.com/rest/api/azure/) payload om dezelfde wijzigingen aan te brengen is als volgt:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetrieinitialisator

Als u een flexibeler `DisableIpMasking` alternatief nodig hebt dan om alle of een deel van IP-adressen op te nemen, u een [telemetrieinitialisator](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) gebruiken om het IP geheel of gedeeltelijk naar een aangepast veld te kopiëren. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Kern

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Als u geen `ISupportProperties`toegang hebt, controleert u of u de nieuwste stabiele versie van de Application Insights SDK uitvoert. `ISupportProperties`zijn bedoeld voor hoge kardinaliteitswaarden, terwijl `GlobalProperties` ze meer geschikt zijn voor lage kardinaliteitswaarden zoals regionaam, omgevingsnaam, enz. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Telemetrieinitialisator inschakelen voor ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Telemetrieinitialisator inschakelen voor ASP.NET Core

U uw telemetrieinitialisator op dezelfde manier maken voor ASP.NET Core als ASP.NET maar om de initialisator in te schakelen, gebruikt u het volgende voorbeeld als referentie:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>JavaScript aan de clientzijde

In tegenstelling tot de SDK's aan de serverzijde berekent de Javascript SDK aan clientzijde geen IP-adres. Standaard wordt ip-adresberekening voor telemetrie aan de clientzijde uitgevoerd op het opnameeindpunt in Azure bij aankomst van telemetrie. Dit betekent dat als u clientgegevens naar een proxy verzendt en vervolgens doorstuurt naar het opnameeindpunt, ip-adresberekening het IP-adres van de proxy kan weergeven en niet de client. Als er geen proxy wordt gebruikt, mag dit geen probleem zijn.

Als u het IP-adres direct aan de clientzijde wilt berekenen, moet u uw eigen `ai.location.ip` aangepaste logica toevoegen om deze berekening uit te voeren en het resultaat gebruiken om de tag in te stellen. Wanneer `ai.location.ip` is ingesteld, wordt de berekening van het IP-adres niet uitgevoerd door het opnameeindpunt en wordt het opgegeven IP-adres gehonoreerd en gebruikt voor het uitvoeren van de geo-lookup. In dit scenario wordt het IP-adres standaard nog steeds op nul gezet. 

Als u het volledige IP-adres wilt behouden dat is berekend op basis van uw `ai.location.ip` aangepaste logica, u een telemetrieinitialisator gebruiken die de IP-adresgegevens kopieert die u hebt opgegeven in een afzonderlijk aangepast veld. Maar nogmaals in tegenstelling tot de server-side SDK's, zonder te vertrouwen op 3rd party bibliotheken of uw eigen aangepaste client-side IP-collectie logica van de client-side SDK zal niet berekenen van de IP voor u.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Bekijk de resultaten van uw telemetrieinitialisator

Als u vervolgens nieuw verkeer tegen uw site activeert en ongeveer 2-5 minuten wacht om ervoor te zorgen dat het tijd had om te worden ingenomen, u een Kusto-query uitvoeren om te zien of de ip-adresverzameling werkt:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nieuw verzamelde IP-adressen moeten `customDimensions_client-ip` in de kolom worden weergegeven. De `client-ip` standaardkolom heeft nog steeds alle 4 octetten die zijn gezeroed of alleen de eerste drie octetten weergeven, afhankelijk van hoe u de IP-adresverzameling op componentniveau hebt geconfigureerd. Als u lokaal test na het implementeren van de telemetrieinitialisator en de waarde die u ziet `customDimensions_client-ip` is `::1` dit is verwacht gedrag. `::1`vertegenwoordigt het loopback-adres in IPv6. Het is `127.0.01` gelijk aan in IPv4 en is het resultaat dat u zult zien bij het testen van localhost.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het verzamelen van persoonsgegevens](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) vindt u in Application Insights.

* Meer informatie over hoe [ip-adresverzameling](https://apmtips.com/blog/2016/07/05/client-ip-address/) in Application Insights werkt. (Dit is een oudere externe blog post geschreven door een van onze ingenieurs. Het dateert van voor het huidige standaardgedrag waarbij IP-adres wordt geregistreerd als `0.0.0.0`, `ClientIpHeaderTelemetryInitializer`maar het gaat in grotere diepte op de mechanica van de ingebouwde .)
