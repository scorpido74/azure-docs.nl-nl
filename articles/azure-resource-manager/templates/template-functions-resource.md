---
title: Sjabloonfuncties - resources
description: Beschrijft de functies die u moet gebruiken in een Azure Resource Manager-sjabloon om waarden over resources op te halen.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 641602218aa19b790eb6e7feabdb7b46a520b590
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478274"
---
# <a name="resource-functions-for-arm-templates"></a>Resourcefuncties voor ARM-sjablonen

Resourcebeheer biedt de volgende functies voor het verkrijgen van resourcewaarden in uw Azure Resource Manager-sjabloon (ARM):

* [extensieResourceId](#extensionresourceid)
* [lijst*](#list)
* [Providers](#providers)
* [Verwijzing](#reference)
* [resourceGroep](#resourcegroup)
* [Resourceid](#resourceid)
* [Abonnement](#subscription)
* [abonnementResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Zie [Implementatiewaardefuncties](template-functions-deployment.md)voor waarden uit parameters, variabelen of de huidige implementatie.

## <a name="extensionresourceid"></a>extensieResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Geeft als resultaat de resource-id voor een [extensiebron](../management/extension-resource-types.md), een resourcetype dat wordt toegepast op een andere resource om toe te voegen aan de mogelijkheden.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |tekenreeks |De resource-id voor de resource waarop de extensiebron wordt toegepast. |
| resourceType |Ja |tekenreeks |Type resource inclusief naamruimte van resourceprovider. |
| resourceName1 |Ja |tekenreeks |Naam van de bron. |
| resourceName2 |Nee |tekenreeks |Volgende resource naam segment, indien nodig. |

Ga door met het toevoegen van resourcenamen als parameters wanneer het resourcetype meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De basisindeling van de resource-id die door deze functie wordt geretourneerd, is:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Het bereiksegment varieert afhankelijk van de resource die wordt uitgebreid.

Wanneer de extensiebron op een **resource**wordt toegepast, wordt de resource-id in de volgende indeling geretourneerd:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensiebron wordt toegepast op een **resourcegroep,** is de indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensiebron op een **abonnement**wordt toegepast, is de indeling:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensiebron wordt toegepast op een **beheergroep,** is de indeling:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>voorbeeld van ExtensionResourceId

In het volgende voorbeeld wordt de resource-id voor een resourcegroepvergrendeling geretourneerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>lijst*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

De syntaxis voor deze functie varieert per naam van de lijstbewerkingen. Elke implementatie retourneert waarden voor het resourcetype dat een lijstbewerking ondersteunt. De naam van `list`de bewerking moet beginnen met . Enkele veelvoorkomende `listKeys` toepassingen `listSecrets`zijn en .

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceName of resourceIdentifier |Ja |tekenreeks |Unieke id voor de resource. |
| apiVersion |Ja |tekenreeks |API-versie van de runtime-status van resources. Typisch, in het formaat, **yyyy-mm-dd**. |
| functieWaarden |Nee |object | Een object met waarden voor de functie. Geef dit object alleen op voor functies die ondersteuning bieden voor het ontvangen van een object met parameterwaarden, zoals **listAccountSas** op een opslagaccount. Een voorbeeld van het doorgeven van functiewaarden wordt weergegeven in dit artikel. |

### <a name="valid-uses"></a>Geldig gebruik

De lijstfuncties kunnen alleen worden gebruikt in de eigenschappen van een resourcedefinitie en de sectie uitvoer van een sjabloon of implementatie. Wanneer u wordt gebruikt met [eigenschapsiteratie,](copy-properties.md)u de lijstfuncties gebruiken omdat `input` de expressie is toegewezen aan de eigenschap resource. U ze niet `count` gebruiken omdat het aantal moet worden bepaald voordat de lijstfunctie is opgelost.

### <a name="implementations"></a>Implementaties

De mogelijke toepassingen van lijst* worden weergegeven in de volgende tabel.

| Resourcetype | Functienaam |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Lijstsleutels |
| Microsoft.Automation/automationAccounts | [lijstSleutels](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [lijstsleutels](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experimenten/taken | [listoutputbestanden](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainLeden | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [lijstSleutels](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [lijstSleutels](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registers | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registers | [lijstReferenties](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registers | [lijstGebruikstoepassingen](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registers/webhooks | [lijstGebeurtenissen](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [lijstLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registers/taken | [lijstDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/vacatures | lijstReferenties |
| Microsoft.DataFactory/datafabrieken/gateways | listauthkeys |
| Microsoft.DataFactory/fabrieken/integratielooptijden | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [lijstSynchronisaties](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationInstellingen](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [lijstSynchronisaties](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [lijstsleutels](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [lijstsleutels](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [lijstsleutels](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [lijstsleutels](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [LijstVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schema's | [LijstToepasselijk](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [LijstToepasselijke schema's](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [LijstToepasselijke schema's](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [lijstVerbindingen](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [lijstSleutels](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [lijstDomeinaanbevelingen](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [lijstOvereenkomsten](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domeinen | [lijstSleutels](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/onderwerpen | [lijstSleutels](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/naamruimten/autorisatieregels | [lijstsleutels](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [lijstsleutels](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [lijstsleutels](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [Lijstprincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/gebruikers | [Lijstomgevingen](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/gebruikers | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/overeenkomsten | [lijstContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblages | [lijstContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [lijstCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [lijstContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [lijstContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schema's | [lijstContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/werkstromen | [lijstCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/werkstromen | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [lijstExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/herhalingen | [lijstExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [lijstCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versies/triggers | [lijstCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [lijstsleutels](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Werkruimten | listworkspacetoetsen |
| Microsoft.MachineLearningServices/werkruimten/computes | [lijstSleutels](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/werkruimten/computes | [lijstKnooppunten](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/werkruimten | [lijstSleutels](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [lijstSleutels](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [lijstStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [lijstContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [lijstPaden](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfiguraties |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [lijstsleutels](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/autorisatieregels | [lijstsleutels](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/werkruimten | [lijstSleutels](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [lijstImplementaties](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/naamruimten/autorisatieregels | [lijstsleutels](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | lijstsleutels |
| Microsoft.Relay/naamruimten/HybridConnections/autorisatieregels | [lijstsleutels](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/naamruimten/WcfRelays/autorisatieRegels | [lijstsleutels](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQuerytoetsen](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/naamruimten/autorisatieregels | [lijstsleutels](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [lijstsleutels](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/naamruimten/wachtrijen/autorisatieRegels | [lijstsleutels](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/naamruimten/onderwerpen/autorisatieRegels | [lijstsleutels](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [lijstsleutels](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [lijstAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [lijstsleutels](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [lijstServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/apparaten | [listFailoversets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/apparaten | [listFailoverDoelen](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [lijstActiveringssleutel](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [lijstPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | Lijststatus |
| microsoft.web/verbindingen | lijstconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locaties | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apiis/verbindingen | lijstverbindingssleutels |
| microsoft.web/apimanagementaccounts/apiis/verbindingen | lijstgeheimen |
| microsoft.web/sites/back-ups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functies | [lijstsleutels](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functies | [lijstgeheimen](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [lijstsleutels](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/sleuven/functies | [lijstgeheimen](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/back-ups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/sleuven/functies | [lijstgeheimen](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Als u wilt bepalen welke resourcetypen een lijstbewerking hebben, hebt u de volgende opties:

* Bekijk de [REST API-bewerkingen](/rest/api/) voor een resourceprovider en zoek naar lijstbewerkingen. Opslagaccounts hebben bijvoorbeeld de [listKeys-bewerking](/rest/api/storagerp/storageaccounts).
* Gebruik de [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell-cmdlet. In het volgende voorbeeld worden alle lijstbewerkingen voor opslagaccounts weergegeven:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Gebruik de volgende opdracht Azure CLI om alleen de lijstbewerkingen te filteren:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object is afhankelijk van de lijstfunctie die u gebruikt. De lijstSleutels voor een opslagaccount retourneert bijvoorbeeld de volgende indeling:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andere lijstfuncties hebben verschillende retourindelingen. Als u de indeling van een functie wilt weergeven, neemt u deze op in de sectie uitvoer zoals weergegeven in de voorbeeldsjabloon.

### <a name="remarks"></a>Opmerkingen

Geef de resource op met de resourcenaam of de [functie resourceId](#resourceid). Wanneer u een lijstfunctie gebruikt in dezelfde sjabloon waarmee de bron waarnaar wordt verwezen, wordt de bronnaam gebruikt.

Als u een **lijstfunctie** gebruikt in een resource die voorwaardelijk is geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een foutmelding weergegeven als de **lijstfunctie** verwijst naar een resource die niet bestaat. Gebruik de functie **als** om ervoor te zorgen dat de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeldsjabloon die wordt gebruikt als en lijst met een voorwaardelijk geïmplementeerde bron.

### <a name="list-example"></a>Voorbeeld van lijst

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ziet u hoe u de primaire en secundaire sleutels retourneren van een opslagaccount in de sectie uitvoer. Het retourneert ook een SAS-token voor het opslagaccount.

Als u het SAS-token wilt krijgen, geeft u een object door voor de vervaldatum. De vervaldatum moet in de toekomst zijn. Dit voorbeeld is bedoeld om te laten zien hoe u de lijstfuncties gebruikt. Normaal gesproken gebruikt u het SAS-token in een resourcewaarde in plaats van het als uitvoerwaarde terug te geven. Uitvoerwaarden worden opgeslagen in de implementatiegeschiedenis en zijn niet veilig.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

```json
providers(providerNamespace, [resourceType])
```

Retourneert informatie over een resourceprovider en de ondersteunde resourcetypen. Als u geen resourcetype opgeeft, retourneert de functie alle ondersteunde typen voor de resourceprovider.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |tekenreeks |Naamruimte van de provider |
| resourceType |Nee |tekenreeks |Het type resource binnen de opgegeven naamruimte. |

### <a name="return-value"></a>Retourwaarde

Elk ondersteund type wordt geretourneerd in de volgende indeling:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Arrayvolgorde van de geretourneerde waarden is niet gegarandeerd.

### <a name="providers-example"></a>Voorbeeld van providers

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ziet u hoe u de providerfunctie gebruikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Voor de **bronprovider en** het brontype **Microsoft.Web** retourneert het voorgaande voorbeeld een object in de volgende indeling:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>Verwijzing

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Geeft als resultaat een object dat de runtime-status van een resource vertegenwoordigt.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceName of resourceIdentifier |Ja |tekenreeks |Naam of unieke id van een resource. Wanneer u een resource in de huidige sjabloon verwijst, geeft u alleen de resourcenaam als parameter op. Wanneer u verwijst naar een eerder geïmplementeerde resource of wanneer de naam van de resource dubbelzinnig is, geeft u de resource-id op. |
| apiVersion |Nee |tekenreeks |API-versie van de opgegeven bron. Neem deze parameter op wanneer de resource niet in dezelfde sjabloon is ingericht. Typisch, in het formaat, **yyyy-mm-dd**. Zie [sjabloonverwijzing](/azure/templates/)voor geldige API-versies voor uw resource. |
| 'Vol' |Nee |tekenreeks |Waarde die aangeeft of het volledige resourceobject moet worden retourneren. Als u dit `'Full'`niet opgeeft, wordt alleen het eigenschappenobject van de resource geretourneerd. Het volledige object bevat waarden zoals de resource-id en de locatie. |

### <a name="return-value"></a>Retourwaarde

Elk resourcetype retourneert verschillende eigenschappen voor de referentiefunctie. De functie retourneert geen enkele vooraf gedefinieerde notatie. De geretourneerde waarde verschilt ook op basis van de vraag of u het volledige object hebt opgegeven. Als u de eigenschappen voor een resourcetype wilt weergeven, geeft u het object in de sectie uitvoer weer, zoals in het voorbeeld wordt weergegeven.

### <a name="remarks"></a>Opmerkingen

De referentiefunctie haalt de runtime-status op van een eerder geïmplementeerde resource of een resource die is geïmplementeerd in de huidige sjabloon. In dit artikel worden voorbeelden voor beide scenario's weergegeven.

Normaal gesproken gebruikt u de **referentiefunctie** om een bepaalde waarde van een object terug te sturen, zoals het blobeindpunt URI of volledig gekwalificeerde domeinnaam.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Gebruik `'Full'` wanneer u resourcewaarden nodig hebt die geen deel uitmaken van het eigenschappenschema. Als u bijvoorbeeld toegangsbeleid voor sleutelkluizen wilt instellen, krijgt u de identiteitseigenschappen voor een virtuele machine.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Geldig gebruik

De referentiefunctie kan alleen worden gebruikt in de eigenschappen van een resourcedefinitie en de sectie uitvoer van een sjabloon of implementatie. Wanneer u wordt gebruikt met [eigenschapsiteratie,](copy-properties.md)u de referentiefunctie gebruiken voor `input` omdat de expressie is toegewezen aan de eigenschap resource. U het niet `count` gebruiken omdat het aantal moet worden bepaald voordat de referentiefunctie is opgelost.

U de referentiefunctie in de uitvoer van een [geneste sjabloon](linked-templates.md#nested-template) niet gebruiken om een resource die u hebt geïmplementeerd in de geneste sjabloon terug te sturen. Gebruik in plaats daarvan een [gekoppelde sjabloon](linked-templates.md#linked-template).

Als u de **referentiefunctie** gebruikt in een resource die voorwaardelijk is geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd.  Er wordt een foutmelding weergegeven als de **referentiefunctie** verwijst naar een resource die niet bestaat. Gebruik de functie **als** om ervoor te zorgen dat de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeldsjabloon die wordt gebruikt als en verwijzen naar een voorwaardelijk geïmplementeerde bron.

### <a name="implicit-dependency"></a>Impliciete afhankelijkheid

Met de referentiefunctie verklaart u impliciet dat de ene resource afhankelijk is van een andere resource als de bron waarnaar wordt verwezen binnen dezelfde sjabloon is ingericht en u verwijst naar de resource met de naam (niet resource-ID). U hoeft niet ook gebruik te maken van de dependsOn eigenschap. De functie wordt pas geëvalueerd nadat de bron waarnaar is verwezen, de implementatie heeft voltooid.

### <a name="resource-name-or-identifier"></a>Naam of id van resource

Wanneer u verwijst naar een resource die in dezelfde sjabloon is geïmplementeerd, geeft u de naam van de resource op.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Wanneer u verwijst naar een resource die niet in dezelfde sjabloon is geïmplementeerd, geeft u de bron-id op.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Om onduidelijkheid te voorkomen over welke resource u verwijst, u een volledig gekwalificeerde resource-id verstrekken.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Bij het maken van een volledig gekwalificeerde verwijzing naar een resource is de volgorde om segmenten van het type en de naam te combineren niet alleen een samenvoeging van de twee. Gebruik in plaats daarvan na de naamruimte een reeks *tekst/naamparen* van minst specifiek naar het meest specifiek:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Bijvoorbeeld:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` is, is niet correct

Als u het maken van een `resourceId()` resource-id wilt vereenvoudigen, gebruikt u de functies die in dit document worden beschreven in plaats van de `concat()` functie.

### <a name="get-managed-identity"></a>Beheerde identiteit krijgen

[Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) zijn [extensiebrontypen](../management/extension-resource-types.md) die impliciet zijn gemaakt voor sommige resources. Omdat de beheerde identiteit niet expliciet is gedefinieerd in de sjabloon, moet u verwijzen naar de bron waarop de identiteit wordt toegepast. Gebruiken `Full` om alle eigenschappen te krijgen, inclusief de impliciet gemaakte identiteit.

Als u bijvoorbeeld de tenant-id wilt krijgen voor een beheerde identiteit die wordt toegepast op een virtuele machineschaalset, gebruikt u het als:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Referentievoorbeeld

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) wordt een resource geïmplementeerd en wordt verwezen naar die bron.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

In het voorgaande voorbeeld worden de twee objecten geretourneerd. Het object Properties bevindt zich in de volgende indeling:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Het volledige object bevindt zich in de volgende indeling:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) wordt verwezen naar een opslagaccount dat niet in deze sjabloon is geïmplementeerd. Het opslagaccount bestaat al binnen hetzelfde abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Geeft als resultaat een object dat de huidige resourcegroep vertegenwoordigt.

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object heeft de volgende indeling:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

De eigenschap **managedBy** wordt alleen geretourneerd voor resourcegroepen die resources bevatten die worden beheerd door een andere service. Voor beheerde toepassingen, Databricks en AKS is de waarde van de eigenschap de resource-id van de beheerbron.

### <a name="remarks"></a>Opmerkingen

De `resourceGroup()` functie kan niet worden gebruikt in een sjabloon die [op abonnementsniveau](deploy-to-subscription.md)wordt geïmplementeerd. Het kan alleen worden gebruikt in sjablonen die worden geïmplementeerd in een resourcegroep. U `resourceGroup()` de functie gebruiken in een [gekoppelde of geneste sjabloon (met binnenbereik)](linked-templates.md) die zich richt op een brongroep, zelfs wanneer de bovenliggende sjabloon wordt geïmplementeerd in het abonnement. In dat scenario wordt de gekoppelde of geneste sjabloon geïmplementeerd op het niveau van de resourcegroep. Zie [Azure-resources implementeren voor meer dan één abonnement of resourcegroep voor](cross-resource-group-deployment.md)meer informatie over het targeten van een resourcegroep in een implementatie op abonnementsniveau.

Een gemeenschappelijk gebruik van de functie resourceGroep is het maken van resources op dezelfde locatie als de resourcegroep. In het volgende voorbeeld wordt de locatie van de resourcegroep gebruikt voor een standaardparameterwaarde.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

U de functie resourceGroep ook gebruiken om tags uit de resourcegroep toe te passen op een resource. Zie [Tags toepassen uit resourcegroep voor](../management/tag-resources.md#apply-tags-from-resource-group)meer informatie .

Wanneer u geneste sjablonen gebruikt om te implementeren in meerdere resourcegroepen, u het bereik opgeven voor de evaluatie van de functie resourceGroep. Zie [Azure-resources implementeren voor meer dan één abonnement of resourcegroep voor](cross-resource-group-deployment.md)meer informatie.

### <a name="resource-group-example"></a>Voorbeeld van resourcegroep

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) worden de eigenschappen van de resourcegroep geretourneerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

In het voorgaande voorbeeld wordt een object in de volgende indeling geretourneerd:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Geeft als resultaat de unieke id van een resource. U gebruikt deze functie wanneer de naam van de resource dubbelzinnig is of niet is ingericht in dezelfde sjabloon. De indeling van de geretourneerde id is afhankelijk van de vraag of de implementatie plaatsvindt op het bereik van een resourcegroep, abonnement, beheergroep of tenant.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |tekenreeks (in GUID-indeling) |Standaardwaarde is het huidige abonnement. Geef deze waarde op wanneer u een resource in een ander abonnement moet ophalen. Geef deze waarde alleen op wanneer u implementeert op het bereik van een resourcegroep of -abonnement. |
| resourceGroupName |Nee |tekenreeks |Standaardwaarde is de huidige resourcegroep. Geef deze waarde op wanneer u een resource in een andere resourcegroep moet ophalen. Geef deze waarde alleen op bij het implementeren op het bereik van een resourcegroep. |
| resourceType |Ja |tekenreeks |Type resource inclusief naamruimte van resourceprovider. |
| resourceName1 |Ja |tekenreeks |Naam van de bron. |
| resourceName2 |Nee |tekenreeks |Volgende resource naam segment, indien nodig. |

Ga door met het toevoegen van resourcenamen als parameters wanneer het resourcetype meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

Wanneer de sjabloon wordt geïmplementeerd in het bereik van een resourcegroep, wordt de resource-id geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Wanneer de [resource-id](deploy-to-subscription.md)wordt gebruikt in een implementatie op abonnementsniveau, wordt deze geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Wanneer de [resource-id](deploy-to-management-group.md) wordt geretourneerd in een implementatie op managementgroepniveau of implementatie op tenantniveau, wordt de resource-id in de volgende indeling geretourneerd:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Zie het als nodig zijn om de id in andere indelingen te krijgen:

* [extensieResourceId](#extensionresourceid)
* [abonnementResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Opmerkingen

Het aantal parameters dat u opgeeft, is afhankelijk van de vraag of de resource een bovenliggende of onderliggende bron is en of de resource zich in dezelfde abonnements- of resourcegroep bevindt.

Als u de resource-id voor een bovenliggende resource in dezelfde abonnements- en resourcegroep wilt krijgen, geeft u het type en de naam van de resource op.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Als u de resource-id voor een onderliggende bron wilt krijgen, moet u aandacht besteden aan het aantal segmenten in het resourcetype. Geef een resourcenaam op voor elk segment van het resourcetype. De naam van het segment komt overeen met de resource die bestaat voor dat deel van de hiërarchie.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Als u de resource-id voor een resource in hetzelfde abonnement, maar in een andere resourcegroep wilt krijgen, geeft u de naam van de brongroep op.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Als u de resource-id voor een resource in een andere abonnements- en resourcegroep wilt krijgen, geeft u de naam van de abonnements-id en de brongroep op.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Vaak moet u deze functie gebruiken wanneer u een opslagaccount of virtueel netwerk gebruikt in een alternatieve brongroep. In het volgende voorbeeld ziet u hoe een resource uit een externe resourcegroep eenvoudig kan worden gebruikt:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Voorbeeld van resource-id

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) wordt de bron-id voor een opslagaccount in de resourcegroep geretourneerd:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

De uitvoer van het voorgaande voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| sameRGOutput | Tekenreeks | /abonnementen/{current-sub-id}/resourceGroepen/voorbeeldgroep/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Tekenreeks | /abonnementen/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Tekenreeks | /abonnementen/1111111-1111-1111-1111-1111-1111111111/resourceGroepen/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| geneste resourceoutput | Tekenreeks | /abonnementen/{current-sub-id}/resourceGroepen/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>abonnement

```json
subscription()
```

Retourneert details over het abonnement voor de huidige implementatie.

### <a name="return-value"></a>Retourwaarde

De functie retourneert de volgende notatie:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Opmerkingen

Wanneer u geneste sjablonen gebruikt om te implementeren op meerdere abonnementen, u de ruimte opgeven voor het evalueren van de abonnementsfunctie. Zie [Azure-resources implementeren voor meer dan één abonnement of resourcegroep voor](cross-resource-group-deployment.md)meer informatie.

### <a name="subscription-example"></a>Voorbeeld van een abonnement

In de volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) wordt de abonnementsfunctie weergegeven die in de sectie uitvoer wordt aangeroepen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>abonnementResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Geeft als resultaat de unieke id voor een resource die is geïmplementeerd op abonnementsniveau.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |tekenreeks (in GUID-indeling) |Standaardwaarde is het huidige abonnement. Geef deze waarde op wanneer u een resource in een ander abonnement moet ophalen. |
| resourceType |Ja |tekenreeks |Type resource inclusief naamruimte van resourceprovider. |
| resourceName1 |Ja |tekenreeks |Naam van de bron. |
| resourceName2 |Nee |tekenreeks |Volgende resource naam segment, indien nodig. |

Ga door met het toevoegen van resourcenamen als parameters wanneer het resourcetype meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-id op te halen voor resources die zijn [geïmplementeerd in het abonnement](deploy-to-subscription.md) in plaats van een resourcegroep. De geretourneerde ID verschilt van de waarde die wordt geretourneerd door de [resourceId-functie](#resourceid) door geen resourcegroepwaarde op te nemen.

### <a name="subscriptionresourceid-example"></a>voorbeeld van subscriptionResourceID

In de volgende sjabloon wordt een ingebouwde rol toebedeeld. U het implementeren in een resourcegroep of -abonnement. Het maakt gebruik van de functie subscriptionResourceId om de resource-ID voor ingebouwde rollen te krijgen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Geeft als resultaat de unieke id voor een resource die is geïmplementeerd op tenantniveau.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceType |Ja |tekenreeks |Type resource inclusief naamruimte van resourceprovider. |
| resourceName1 |Ja |tekenreeks |Naam van de bron. |
| resourceName2 |Nee |tekenreeks |Volgende resource naam segment, indien nodig. |

Ga door met het toevoegen van resourcenamen als parameters wanneer het resourcetype meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-id op te halen voor een resource die is geïmplementeerd in de tenant. De geretourneerde ID verschilt van de waarden die worden geretourneerd door andere resource-ID-functies door resourcegroep- of abonnementswaarden niet op te nemen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Sjablonen voor Azure Resource Manager ontwerpen](template-syntax.md)voor een beschrijving van de secties in een Azure Resource Manager-sjabloon.
* Zie [Gekoppelde sjablonen gebruiken met Azure Resource Manager](linked-templates.md)als u meerdere sjablonen wilt samenvoegen.
* Zie Meerdere exemplaren van resources maken [in Azure Resource Manager](copy-resources.md)als u een bepaald aantal keren wilt herhalen bij het maken van een type resource.
* Zie Een toepassing implementeren [met Azure Resource Manager-sjabloon](deploy-powershell.md)als u wilt zien hoe u de sjabloon implementeren die u hebt gemaakt.

