---
title: Uw omgeving beheren met Azure Resource Manager-sjablonen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het programmatisch beheren van uw Azure Time Series Insights-omgeving met Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: a670e32058794daeaa233464ba7d054f45ef25e3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536315"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Time Series Insights-bronnen maken met Azure Resource Manager-sjablonen

In dit artikel wordt beschreven hoe u Time Series Insights-resources maken en implementeren met [Azure Resource Manager-sjablonen,](https://docs.microsoft.com/azure/azure-resource-manager/)PowerShell en de resourceprovider Time Series Insights.

Time Series Insights ondersteunt de volgende bronnen:

   | Resource | Beschrijving |
   | --- | --- |
   | Omgeving | Een Time Series Insights-omgeving is een logische groepering van gebeurtenissen die worden gelezen van gebeurtenismakelaars, die zijn opgeslagen en beschikbaar worden gesteld voor query's. Lees Uw [Azure Time Series Insights-omgeving plannen voor](time-series-insights-environment-planning.md) meer informatie |
   | Bron van gebeurtenis | Een gebeurtenisbron is een verbinding met een gebeurtenisbroker van waaruit Time Series Insights gebeurtenissen in de omgeving leest en inneemt. Momenteel ondersteunde gebeurtenisbronnen zijn IoT Hub en Event Hub. |
   | Referentiegegevensset | Referentiegegevenssets bevatten metagegevens over de gebeurtenissen in de omgeving. Metagegevens in de referentiegegevenssets worden samengevoegd met gebeurtenissen tijdens het binnendringen. Referentiegegevenssets worden gedefinieerd als resources door hun eigenschappen van gebeurtenissleutel. De werkelijke metagegevens die deel uitmaakt van de referentiegegevensset worden geüpload of gewijzigd via API's voor gegevensvlak. |
   | Toegangsbeleid | Toegangsbeleid verleent machtigingen voor het uitgeven van gegevensquery's, het manipuleren van referentiegegevens in de omgeving en het delen van opgeslagen query's en perspectieven die aan de omgeving zijn gekoppeld. Lees Voor meer informatie [gegevenstoegang verlenen tot een Time Series Insights-omgeving met Azure-portal](time-series-insights-data-access.md) |

Een resourcemanagersjabloon is een JSON-bestand dat de infrastructuur en configuratie van resources in een resourcegroep definieert. In de volgende documenten worden sjabloonbestanden gedetailleerder beschreven:

- [Azure Resource Manager-sjabloonimplementatie](../azure-resource-manager/templates/overview.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft.TimeSeriesInsights-brontypen](/azure/templates/microsoft.timeseriesinsights/allversions)

De [quickstartsjabloon 201-timeseriesinsights-met-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) wordt gepubliceerd op GitHub. Deze sjabloon maakt een Time Series Insights-omgeving, een onderliggende gebeurtenisbron die is geconfigureerd om gebeurtenissen uit een gebeurtenishub te gebruiken, en toegangsbeleid dat toegang geeft tot de gegevens van de omgeving. Als een bestaande gebeurtenishub niet is opgegeven, wordt er een gemaakt met de implementatie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Implementatiesjabloon en -parameters opgeven

In de volgende procedure wordt beschreven hoe u PowerShell gebruiken om een Azure Resource Manager-sjabloon te implementeren waarmee een Time Series Insights-omgeving wordt gemaakt, een onderliggende gebeurtenisbron die is geconfigureerd om gebeurtenissen uit een gebeurtenishub te gebruiken, en toegangsbeleid dat toegang verleent tot de gegevens van de omgeving. Als een bestaande gebeurtenishub niet is opgegeven, wordt er een gemaakt met de implementatie.

1. Installeer Azure PowerShell door de instructies te volgen in [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Kloon of kopieer de [201-timeseriesinsights-environment-with-eventhub-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) van GitHub.

   * Een parametersbestand maken

     Als u een parametersbestand wilt maken, kopieert u het bestand [201-timeseriesinsights-environment-with-eventhub.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Vereiste parameters

     | Parameter | Beschrijving |
     | --- | --- |
     | eventHubNamespaceName | De naamruimte van de brongebeurtenishub. |
     | eventHubName | De naam van de brongebeurtenishub. |
     | consumerGroupName | De naam van de consumentengroep die de Time Series Insights-service zal gebruiken om de gegevens van de gebeurtenishub te lezen. **LET OP:** Om bronconflicten te voorkomen, moet deze consumentengroep worden gewijd aan de Time Series Insights-service en niet worden gedeeld met andere lezers. |
     | omgevingNaam | De naam van het milieu. De naam kan `<` `>`niet `%` `&`bestaan `:` `\\`uit: , , , , , `?`, `/`en alle controletekens. Alle andere tekens zijn toegestaan.|
     | eventSourceName | De naam van de onderliggende bron van de gebeurtenisbron. De naam kan `<` `>`niet `%` `&`bestaan `:` `\\`uit: , , , , , `?`, `/`en alle controletekens. Alle andere tekens zijn toegestaan. |

    <div id="optional-parameters"></div>

   * Optionele parameters

     | Parameter | Beschrijving |
     | --- | --- |
     | bestaandeEventHubResourceId | Een optionele resource-id van een bestaande gebeurtenishub die via de gebeurtenisbron wordt verbonden met de Time Series Insights-omgeving. **LET OP:** De gebruiker die de sjabloon implementeert, moet bevoegdheden hebben om de listkeys-bewerking in de gebeurtenishub uit te voeren. Als er geen waarde wordt doorgegeven, wordt er een nieuwe gebeurtenishub gemaakt door de sjabloon. |
     | omgevingDisplayName | Een optionele vriendelijke naam om weer te geven in tooling of gebruikersinterfaces in plaats van de omgevingsnaam. |
     | omgevingSkuName | De naam van de SKU. Lees voor meer informatie de [pagina Time Series Insights Pricing.](https://azure.microsoft.com/pricing/details/time-series-insights/)  |
     | omgevingSkuCapacity | De eenheidscapaciteit van de Sku. Lees voor meer informatie de [pagina Time Series Insights Pricing.](https://azure.microsoft.com/pricing/details/time-series-insights/)|
     | milieuDataRetentionTime | De minimale tijdspanne van de gebeurtenissen in de omgeving is beschikbaar voor query's. De waarde moet worden opgegeven in de ISO `P30D` 8601-notatie, bijvoorbeeld voor een bewaarbeleid van 30 dagen. |
     | gebeurtenisSourceDisplayNaam | Een optionele vriendelijke naam om weer te geven in tooling of gebruikersinterfaces in plaats van de naam van de gebeurtenisbron. |
     | gebeurtenisSourceTimestampPropertyName | De gebeurteniseigenschap die wordt gebruikt als tijdstempel van de gebeurtenisbron. Als een waarde niet is opgegeven voor timestampPropertyName of als null of empty-string is opgegeven, wordt de tijd voor het maken van gebeurtenissen gebruikt. |
     | eventSourceKeyName | De naam van de gedeelde toegangssleutel die de Time Series Insights-service gebruikt om verbinding te maken met de gebeurtenishub. |
     | toegang tot PolicyReaderObjectIds | Een lijst met object-id's van de gebruikers of toepassingen in Azure AD die readertoegang tot de omgeving moeten hebben. De service principal objectId kan worden verkregen door te bellen naar de **Get-AzADUser** of de **Get-AzADServicePrincipal** cmdlets. Het maken van een toegangsbeleid voor Azure AD-groepen wordt nog niet ondersteund. |
     | accessPolicyContributorObjectIds | Een lijst met object-id's van de gebruikers of toepassingen in Azure AD die inzendertoegang tot de omgeving moeten hebben. De service principal objectId kan worden verkregen door te bellen naar de **Get-AzADUser** of de **Get-AzADServicePrincipal** cmdlets. Het maken van een toegangsbeleid voor Azure AD-groepen wordt nog niet ondersteund. |

   * Als voorbeeld wordt het volgende parametersbestand gebruikt om een omgeving en een gebeurtenisbron te maken die gebeurtenissen leest vanuit een bestaande gebeurtenishub. Het creëert ook twee toegangsbeleid dat inzender toegang geeft tot het milieu.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```

    * Lees voor meer informatie het artikel [Parameters.](../azure-resource-manager/templates/parameter-files.md)

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>De snelstartsjabloon lokaal implementeren met PowerShell

> [!IMPORTANT]
> De onderstaande opdrachtregelbewerkingen beschrijven de [Az PowerShell-module](https://docs.microsoft.com/powershell/azure/overview).

1. Log in PowerShell in bij uw Azure-account.

    * Voer vanuit een PowerShell-prompt de volgende opdracht uit:

      ```powershell
      Connect-AzAccount
      ```

    * U wordt gevraagd zich aan te melden bij uw Azure-account. Voer na het aanmelden de volgende opdracht uit om uw beschikbare abonnementen weer te geven:

      ```powershell
      Get-AzSubscription
      ```

    * Met deze opdracht wordt een lijst met beschikbare Azure-abonnementen geretourneerd. Kies een abonnement voor de huidige sessie door de volgende opdracht uit te voeren. Vervang `<YourSubscriptionId>` de GUID voor het Azure-abonnement dat u wilt gebruiken:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Maak een nieuwe resourcegroep als deze niet bestaat.

   * Als u geen bestaande resourcegroep hebt, maakt u een nieuwe resourcegroep met de opdracht **Nieuw-AzResourceGroep.** Geef de naam op van de resourcegroep en locatie die u wilt gebruiken. Bijvoorbeeld:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Als dit is gelukt, wordt een overzicht van de nieuwe resourcegroep weergegeven.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Test de implementatie.

   * Valideer uw `Test-AzResourceGroupDeployment` implementatie door de cmdlet uit te voeren. Geef bij het testen van de implementatie parameters op die precies zo zijn als bij het uitvoeren van de implementatie.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. De implementatie maken

    * Als u de nieuwe `New-AzResourceGroupDeployment` implementatie wilt maken, voert u de cmdlet uit en geeft u de benodigde parameters op wanneer daarom wordt gevraagd. De parameters omvatten een naam voor uw implementatie, de naam van uw resourcegroep en het pad of de URL naar het sjabloonbestand. Als de parameter **Modus** niet is opgegeven, wordt de standaardwaarde **incrementeel** gebruikt. Lees [Incrementele en volledige implementaties](../azure-resource-manager/templates/deployment-modes.md)voor meer informatie.

    * Met de volgende opdracht wordt u gevraagd naar de vijf vereiste parameters in het PowerShell-venster:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * Als u in plaats daarvan een parametersbestand wilt opgeven, gebruikt u de volgende opdracht:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * U ook inlineparameters gebruiken wanneer u de implementatiecmdlet uitvoert. De opdracht is als volgt:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Als u een volledige implementatie [wilt](../azure-resource-manager/templates/deployment-modes.md) uitvoeren, stelt u de parameter **Modus** in op **Voltooien:**

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. De implementatie controleren

    * Als de resources met succes worden geïmplementeerd, wordt een overzicht van de implementatie weergegeven in het PowerShell-venster:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. De snelstartsjabloon implementeren via de Azure-portal

   * De startpagina van de quickstartsjabloon op GitHub bevat ook een knop **Deploy to Azure.** Als u erop klikt, wordt een pagina Met aangepaste implementatie geopend in de Azure-portal. Op deze pagina u waarden voor elk van de parameters invoeren of selecteren op de [vereiste parameters](#required-parameters) of [optionele parameterstabellen.](#optional-parameters) Als u na het invullen van de instellingen op de knop **Aankoop** klikt, wordt de implementatie van de sjabloon gestart.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Volgende stappen

- Lees [Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/)voor informatie over het programmatisch beheren van Time Series Insights-bronnen met BEHULP VAN REST API's.
