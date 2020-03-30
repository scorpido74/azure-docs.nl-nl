---
title: Azure Monitoring REST API-walkthrough
description: Aanvragen verifiëren en de Azure Monitor REST API gebruiken om beschikbare metrische definities en metrische waarden op te halen.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.openlocfilehash: f52776fc6b5f5f530cc368a2f148a2ff63fb5b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294623"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Monitoring REST API-walkthrough

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In dit artikel ziet u hoe u verificatie uitvoert, zodat uw code de [Microsoft Azure Monitor REST API Reference](https://docs.microsoft.com/rest/api/monitor/)kan gebruiken.

De Azure Monitor API maakt het mogelijk om de beschikbare standaardmetrische definities, granulariteit en metrische waarden programmatisch op te halen. De gegevens kunnen worden opgeslagen in een afzonderlijk gegevensarchief, zoals Azure SQL Database, Azure Cosmos DB of Azure Data Lake. Van daaruit kan indien nodig aanvullende analyse worden uitgevoerd.

Naast het werken met verschillende metrische gegevenspunten, maakt de Monitor API het ook mogelijk om waarschuwingsregels op te sommen, activiteitslogboeken te bekijken en nog veel meer. Zie de Microsoft Azure Monitor [REST API-naslaginformatie](https://docs.microsoft.com/rest/api/monitor/)voor een volledige lijst met beschikbare bewerkingen.

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor-aanvragen verifiëren

De eerste stap is het verifiëren van de aanvraag.

Alle taken die zijn uitgevoerd met de Azure Monitor API gebruiken het Azure Resource Manager-verificatiemodel. Daarom moeten alle aanvragen worden geverifieerd met Azure Active Directory (Azure AD). Een benadering om de clienttoepassing te verifiëren is het maken van een Azure AD-serviceprincipal en het verificatietoken (JWT) ophalen. In het volgende voorbeeldscript wordt een Azure AD-serviceprincipal gemaakt via PowerShell. Raadpleeg voor een meer gedetailleerde doorloop de documentatie over [het gebruik van Azure PowerShell om een serviceprincipal te maken voor toegang tot bronnen.](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps) Het is ook mogelijk om een serviceprincipal te [maken via de Azure-portal.](../../active-directory/develop/howto-create-service-principal-portal.md)

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Als u de Azure Monitor API wilt opvragen, moet de clienttoepassing de eerder gemaakte serviceprincipal gebruiken om te verifiëren. In het volgende voorbeeld van PowerShell-script wordt één benadering weergegeven, waarbij de [Active Directory Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) wordt gebruikt om het JWT-verificatietoken te verkrijgen. Het JWT-token wordt doorgegeven als onderdeel van een parameter HTTP Authorization in aanvragen voor de Azure Monitor REST API.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Na het verifiëren kunnen query's vervolgens worden uitgevoerd tegen de Azure Monitor REST API. Er zijn twee nuttige vragen:

1. De metrische definities voor een resource weergeven
2. De metrische waarden ophalen

> [!NOTE]
> Raadpleeg de [Azure REST API-verwijzing](https://docs.microsoft.com/rest/api/azure/)voor meer informatie over het verifiëren met de Azure REST API.
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Metrische definities ophalen (multidimensionale API)

Gebruik de [REST API voor Azure Monitor Metric](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) om toegang te krijgen tot de lijst met statistieken die beschikbaar zijn voor een service.

**Methode**: GET

**Aanvraag URI**:\/\/https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinities?api-versie=*{apiVersion}*

Als u bijvoorbeeld de metrische definities voor een Azure Storage-account wilt ophalen, wordt de aanvraag als volgt weergegeven:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Als u metrische definities wilt ophalen met behulp van de restAPI met multidimensionale Azure Monitor-statistieken, gebruikt u '2018-01-01' als API-versie.
>
>

De resulterende JSON-antwoordbody zou vergelijkbaar zijn met het volgende voorbeeld: (Merk op dat de tweede statistiek afmetingen heeft)

```json
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Dimensiewaarden ophalen (multidimensionale API)

Zodra de beschikbare metrische definities bekend zijn, kunnen er enkele statistieken zijn die dimensies hebben. Voordat u de statistiek opvraagt, wilt u mogelijk weten wat het waardenbereik is dat een dimensie heeft. Op basis van deze dimensiewaarden u er vervolgens voor kiezen om de statistieken te filteren of te segmenteren op basis van dimensiewaarden terwijl u naar metrische gegevens zoekt.  Gebruik hiervoor de [API voor Azure Monitor Metrics REST.](https://docs.microsoft.com/rest/api/monitor/metrics)

Gebruik de naam 'waarde' van de statistiek (niet de 'gelokaliseerde waarde') voor filteraanvragen. Als er geen filters zijn opgegeven, wordt de standaardstatistiek geretourneerd. Het gebruik van deze API staat slechts één dimensie toe om een wildcardfilter te hebben.

> [!NOTE]
> Als u dimensiewaarden wilt ophalen met de Azure Monitor REST API, gebruikt u '2018-01-01' als API-versie.
>
>

**Methode**: GET

**Vraag URI**\:: https -management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name} {resource-name} {resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadata&api-versie=*{apiVersion }*

Als u bijvoorbeeld de lijst met dimensiewaarden wilt ophalen die zijn uitgestoten voor de dimensie 'API-naam' voor de statistiek 'Transacties', waarbij de geotypedimensie = 'Primair' gedurende het opgegeven tijdsbereik, de aanvraag als volgt is:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

De resulterende JSON-antwoordinstantie zou vergelijkbaar zijn met het volgende voorbeeld:

```json
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Metrische waarden ophalen (multidimensionale API)

Zodra de beschikbare metrische definities en mogelijke dimensiewaarden bekend zijn, is het mogelijk om de gerelateerde metrische waarden op te halen.  Gebruik hiervoor de [API voor Azure Monitor Metrics REST.](https://docs.microsoft.com/rest/api/monitor/metrics)

Gebruik de naam 'waarde' van de statistiek (niet de 'gelokaliseerde waarde') voor filteraanvragen. Als er geen dimensiefilters zijn opgegeven, wordt de opgerolde geaggregeerde statistiek geretourneerd. Als een metrische query meerdere tijdreeksen retourneert, u de queryparameters 'Boven' en 'Orderby' gebruiken om een beperkte geordende lijst met tijdreeksen terug te sturen.

> [!NOTE]
> Als u multidimensionale metrische waarden wilt ophalen met de Azure Monitor REST API, gebruikt u '2018-01-01' als API-versie.
>
>

**Methode**: GET

**Aanvraag**URI https://management.azure.com/subscriptions/: *{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&aggregatie=*{aggreation}*&api-versie=*{ apiVersion}*

Als u bijvoorbeeld de top 3 API's, in dalende waarde, wilt ophalen met het aantal 'Transacties' tijdens een bereik van 5 min, waarbij het GeotType 'Primair' was, is de aanvraag als volgt:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

De resulterende JSON-antwoordinstantie zou vergelijkbaar zijn met het volgende voorbeeld:

```json
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Metrische definities ophalen

Gebruik de [REST API voor Azure Monitor Metric](https://msdn.microsoft.com/library/mt743621.aspx) om toegang te krijgen tot de lijst met statistieken die beschikbaar zijn voor een service.

**Methode**: GET

**Aanvraag URI**:\/\/https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinities?api-versie=*{apiVersion}*

Als u bijvoorbeeld de metrische definities voor een Azure Logic App wilt ophalen, wordt de aanvraag als volgt weergegeven:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Als u metrische definities wilt ophalen met de Azure Monitor REST API, gebruikt u '2016-03-01' als API-versie.
>
>

De resulterende JSON-antwoordinstantie zou vergelijkbaar zijn met het volgende voorbeeld:

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Zie De lijst [met metrische definities voor een resource in Azure Monitor REST API-documentatie voor](https://msdn.microsoft.com/library/azure/mt743621.aspx) meer informatie.

## <a name="retrieve-metric-values"></a>Metrische waarden ophalen

Zodra de beschikbare metrische definities bekend zijn, is het mogelijk om de gerelateerde metrische waarden op te halen. Gebruik de naam 'waarde' van de statistiek (niet de 'gelokaliseerde waarde') voor filteraanvragen (haal bijvoorbeeld de metrische gegevenspunten 'CpuTime' en 'Requests' op). Als er geen filters zijn opgegeven, wordt de standaardstatistiek geretourneerd.

> [!NOTE]
> Als u metrische waarden wilt ophalen met de Azure Monitor REST API, gebruikt u '2016-09-01' als API-versie.
>
>

**Methode**: GET

**Vraag URI:**`https://management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Als u bijvoorbeeld de metrieke gegevenspunten uitgevoerd geslaagd voor het opgegeven tijdsbereik wilt ophalen en gedurende een tijdskorrel van 1 uur, is de aanvraag als volgt:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

De resulterende JSON-antwoordinstantie zou vergelijkbaar zijn met het volgende voorbeeld:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Als u meerdere gegevens of aggregatiepunten wilt ophalen, voegt u de metrische definitienamen en aggregatietypen toe aan het filter, zoals te zien is in het volgende voorbeeld:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

De resulterende JSON-antwoordinstantie zou vergelijkbaar zijn met het volgende voorbeeld:

```json
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>ARMClient gebruiken

Een aanvullende benadering is het gebruik van [ARMClient](https://github.com/projectkudu/armclient) op uw Windows-machine. ARMClient verwerkt de Azure AD-verificatie (en resulterende JWT-token) automatisch. In de volgende stappen wordt het gebruik van ARMClient beschreven voor het ophalen van metrische gegevens:

1. Installeer [Chocolatey](https://chocolatey.org/) en [ARMClient.](https://github.com/projectkudu/armclient)
2. Typ in een terminalvenster *armclient.exe-login*. Als u dit doet, moet u zich aanmelden bij Azure.
3. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typ *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Als u bijvoorbeeld de metrische definities voor een specifieke Logic App wilt ophalen, geeft u de volgende opdracht op:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>De bron-id ophalen

Het gebruik van de REST API kan echt helpen om de beschikbare metrische definities, granulariteit en gerelateerde waarden te begrijpen. Die informatie is handig bij het gebruik van de [Azure Management Library.](https://msdn.microsoft.com/library/azure/mt417623.aspx)

Voor de vorige code is de te gebruiken resource-id het volledige pad naar de gewenste Azure-bron. Als u bijvoorbeeld wilt opvragen tegen een Azure Web App, is de resource-id:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{sitenaam}/*

De volgende lijst bevat een paar voorbeelden van resource-id-indelingen voor verschillende Azure-bronnen:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastic SQL Pool** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Servicebus** - /abonnementen/*{subscription-id}*/resourceGroepen/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Virtuele machineschaalsets** - /abonnementen/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM's** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Gebeurtenishubs** - /abonnementen/*{subscription-id}*/resourceGroepen/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Er zijn alternatieve benaderingen voor het ophalen van de bron-id, waaronder het gebruik van Azure Resource Explorer, het bekijken van de gewenste bron in de Azure-portal en via PowerShell of de Azure CLI.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Als u de resource-id voor een gewenste bron wilt vinden, is een handige benadering het gebruik van het Azure [Resource Explorer-hulpprogramma.](https://resources.azure.com) Navigeer naar de gewenste bron en bekijk de weergegeven id, zoals in de volgende schermafbeelding:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

De resource-id kan ook worden verkregen via de Azure-portal. Ga hiervoor naar de gewenste resource en selecteer Eigenschappen. De resource-id wordt weergegeven in de sectie Eigenschappen, zoals te zien is in de volgende schermafbeelding:

![Alt 'Resource-id weergegeven in het blad Eigenschappen in de Azure-portal'](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

De resource-id kan ook worden opgehaald met Azure PowerShell-cmdlets. Als u bijvoorbeeld de bron-id voor een Azure Logic App wilt verkrijgen, voert u de cmdlet Get-AzureLogicApp uit, zoals in het volgende voorbeeld:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Het resultaat moet vergelijkbaar zijn met het volgende voorbeeld:

```output
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure-CLI

Als u de bron-id voor een Azure Storage-account wilt ophalen met de Azure CLI, voert u de `az storage account show` opdracht uit, zoals in het volgende voorbeeld wordt weergegeven:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Het resultaat moet vergelijkbaar zijn met het volgende voorbeeld:

```json
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps zijn nog niet beschikbaar via de Azure CLI, dus een Azure Storage-account wordt weergegeven in het voorgaande voorbeeld.
>
>

## <a name="retrieve-activity-log-data"></a>Gegevens van het activiteitenlogboek ophalen

Naast metrische definities en gerelateerde waarden is het ook mogelijk om de Azure Monitor REST API te gebruiken om aanvullende interessante inzichten met betrekking tot Azure-bronnen op te halen. Als voorbeeld is het mogelijk om gegevens van het [activiteitslogboek](https://msdn.microsoft.com/library/azure/dn931934.aspx) op te vragen. In het volgende voorbeeld wordt aangetoond dat de Azure Monitor REST API wordt gebruikt om gegevens van het activiteitenlogboek op te vragen binnen een specifiek datumbereik voor een Azure-abonnement:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk het [overzicht van monitoring](../../azure-monitor/overview.md).
* Bekijk de [ondersteunde statistieken met Azure Monitor](metrics-supported.md).
* Bekijk de [Microsoft Azure Monitor REST API-referentie](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Bekijk de [Azure-beheerbibliotheek](https://msdn.microsoft.com/library/azure/mt417623.aspx).
