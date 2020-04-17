---
title: Een nieuwe Azure Application Insights-bron maken | Microsoft Documenten
description: Handmatig application Insights-monitoring instellen voor een nieuwe live-applicatie.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537573"
---
# <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Azure Application Insights geeft gegevens over uw toepassing weer in een Microsoft *Azure-bron.* Het maken van een nieuwe resource is daarom onderdeel van het instellen van [Application Insights om een nieuwe toepassing te controleren.][start] Nadat u uw nieuwe resource hebt gemaakt, u de instrumentatiesleutel downloaden en deze gebruiken om de Application Insights SDK te configureren. De instrumentatiesleutel koppelt uw telemetrie aan de resource.

## <a name="sign-in-to-microsoft-azure"></a>Aanmelden bij Microsoft Azure

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Meld u aan bij de [Azure-portal](https://portal.azure.com)en maak een Application Insights-bron:

![Klik op het bord '+' in de linkerbovenhoek. Selecteer hulpprogramma's voor ontwikkelaars, gevolgd door Application Insights](./media/create-new-resource/new-app-insights.png)

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | Unieke waarde | Naam die de app identificeert die u controleert. |
   | **Resourcegroep**     | myResourceGroup      | Naam voor de nieuwe of bestaande brongroep om App Insights-gegevens te hosten. |
   | **Locatie** | VS - oost | Kies een locatie bij u in de buurt of in de buurt van waar uw app wordt gehost. |

> [!NOTE]
> Hoewel u dezelfde resourcenaam gebruiken in verschillende resourcegroepen, kan het nuttig zijn om een wereldwijd unieke naam te gebruiken. Dit kan handig zijn als u van plan bent [om query's met verschillende bronnen uit](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) te voeren, omdat dit de vereiste syntaxis vereenvoudigt.

Voer de juiste waarden in de vereiste velden in en selecteer **Controleren + maken**.

![Voer waarden in de vereiste velden in en selecteer vervolgens 'controleren + maken'.](./media/create-new-resource/review-create.png)

Wanneer uw app is gemaakt, wordt een nieuw deelvenster geopend. In dit deelvenster ziet u prestatie- en gebruiksgegevens over uw bewaakte toepassing. 

## <a name="copy-the-instrumentation-key"></a>De instrumentatietoets kopiëren

De instrumentatiesleutel identificeert de bron waaraan u uw telemetriegegevens wilt koppelen. U moet de instrumentatiesleutel kopiëren en toevoegen aan de code van uw toepassing.

![Klik op de instrumentatietoets en kopieer](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>De SDK in uw app installeren

Installeer de Application Insights SDK in uw app. Deze stap is sterk afhankelijk van het type van uw toepassing.

Gebruik de instrumentatiesleutel om de SDK te configureren [die u in uw toepassing installeert.][start]

De SDK bevat standaardmodules die telemetrie verzenden zonder dat u extra code hoeft te schrijven. Als u acties van gebruikers wilt bijhouden of problemen in meer detail wilt diagnosticeren, [gebruikt u de API][api] om uw eigen telemetrie te verzenden.

## <a name="creating-a-resource-automatically"></a>Automatisch een resource maken

### <a name="powershell"></a>PowerShell

Een nieuwe Application Insights-bron maken

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Voorbeeld

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultaten

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Raadpleeg de [Azure PowerShell-documentatie](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)voor de volledige PowerShell-documentatie voor deze cmdlet en voor meer informatie over het ophalen van de instrumentatiesleutel.

### <a name="azure-cli-preview"></a>Azure CLI (voorbeeld)

Als u toegang wilt krijgen tot de preview Application Insights Azure CLI-opdrachten die u eerst moet uitvoeren:

```azurecli
 az extension add -n application-insights
```

Als u de `az extension add` opdracht niet uitvoert, ziet u een foutbericht met de tekst:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu u het volgende uitvoeren om uw Application Insights-bron te maken:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Voorbeeld

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultaten

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Raadpleeg de Azure CLI-documentatie voor deze opdracht voor de volledige Azure CLI-documentatie voor deze opdracht en raadpleeg de Azure CLI-documentatie voor de volledige Azure [CLI-documentatie.](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Volgende stappen
* [Diagnostische gegevens doorzoeken](../../azure-monitor/app/diagnostic-search.md)
* [Metrische gegevens verkennen](../../azure-monitor/platform/metrics-charts.md)
* [Analytics-query's schrijven](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
