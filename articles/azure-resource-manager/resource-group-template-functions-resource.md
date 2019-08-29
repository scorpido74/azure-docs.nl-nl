---
title: Azure Resource Manager-sjabloonfuncties - resources | Microsoft Docs
description: Beschrijft de functies in een Azure Resource Manager-sjabloon gebruikt voor het ophalen van waarden over resources.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/20/2019
ms.author: tomfitz
ms.openlocfilehash: eddd99be9d4a30e3e71c806a3f98c6be6800e8fb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095757"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Functies van de resource voor Azure Resource Manager-sjablonen

Resource Manager biedt de volgende functies voor het ophalen van waarden van resources:

* [lijst met *](#list)
* [Providers](#providers)
* [Verwijzing](#reference)
* [resourceGroup](#resourcegroup)
* [ResourceId](#resourceid)
* [abonnement](#subscription)

Waarden van parameters, variabelen of de huidige implementatie Zie [waarde implementatiefuncties](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>orderverzamellijst

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

De syntaxis voor deze functie is afhankelijk van de naam van de lijst bewerkingen. Elke implementatie retourneert waarden voor het resource type dat een lijst bewerking ondersteunt. De naam van de bewerking moet `list`beginnen met. Enkele veelvoorkomende gebruiks vormen `listKeys` zijn `listSecrets`en. 

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceName of resourceIdentifier |Ja |string |De unieke id voor de resource. |
| apiVersion |Ja |string |API-versie van de runtimestatus van de resource. Normaal gesproken in de indeling, **jjjj-mm-dd**. |
| functionValues |Nee |object | Een object met waarden voor de functie. Geef alleen dit object voor functies die ondersteuning bieden voor ontvangst van een object met parameterwaarden, zoals **listAccountSas** op een storage-account. In dit artikel wordt een voor beeld gegeven van het door geven van functie waarden. | 

### <a name="implementations"></a>Implementaties

Het mogelijke gebruik van lijst * wordt weer gegeven in de volgende tabel.

| Resourcetype | Functienaam |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Listkeys ophalen |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | listChannelWithKeys |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Micro soft. ContainerRegistry/registers | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Micro soft. ContainerRegistry/registers | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Micro soft. ContainerRegistry/registers | [listPolicies](/rest/api/containerregistry/registries/listpolicies) |
| Micro soft. ContainerRegistry/registers | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Micro soft. ContainerRegistry/registers/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Micro soft. ContainerRegistry/registers/taken | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Micro soft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Micro soft. EventGrid/domeinen | [listKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Micro soft. EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Micro soft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Micro soft. Logic/werk stromen/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Micro soft. Logic/werk stromen/versies/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | Listkeys ophalen |
| Microsoft.MachineLearningServices/workspaces | Listkeys ophalen |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Micro soft. relay/naam ruimten/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys ophalen |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Micro soft. relay/naam ruimten/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Micro soft. StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Micro soft. StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| micro soft. web/sites/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| micro soft. web/sites/sleuven/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Om te bepalen welke resourcetypen de bewerking voor een lijst met hebt, hebt u de volgende opties:

* Weergave de [REST API-bewerkingen](/rest/api/) voor een resourceprovider, en zoekt u bewerkingen na opvragen. Bijvoorbeeld storage-accounts hebben de [listKeys bewerking](/rest/api/storagerp/storageaccounts).
* Gebruik de Power shell [-cmdlet Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Het volgende voorbeeld haalt alle bewerkingen na opvragen voor storage-accounts:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Gebruik de volgende Azure CLI-opdracht voor het filteren van alleen de bewerkingen na opvragen:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object is afhankelijk van de functie van de lijst die u gebruikt. De Listkeys ophalen voor een opslag account retourneert bijvoorbeeld de volgende indeling:

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

Andere functies van de lijst met hebben verschillende retour bestandsindelingen. Als u wilt zien van de indeling van een functie, opnemen in de uitvoersectie zoals wordt weergegeven in de voorbeeldsjabloon.

### <a name="remarks"></a>Opmerkingen

De resource met behulp van de resourcenaam opgeven of de [resourceId functie](#resourceid). Wanneer u een lijst functie gebruikt in dezelfde sjabloon die de resource van de verwijzing implementeert, gebruikt u de resource naam.

Als u een **lijst** functie gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een fout bericht weer **geven** als de functie List verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](resource-group-template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en-lijst met een voorwaardelijke geïmplementeerde resource.

### <a name="list-example"></a>Voor beeld van lijst

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) laat zien hoe de primaire en secundaire sleutels uit een opslagaccount in de uitvoersectie geretourneerd. Deze retourneert ook een SAS-token voor het opslagaccount. 

Om het SAS-token op te halen, geeft u een object voor de verloop tijd door. De verloop tijd moet in de toekomst liggen. In dit voorbeeld is bedoeld om ziet u hoe u de lijst met functies. Normaal gesproken u zou het SAS-token in de waarde van een resource gebruiken in plaats een uitvoerwaarde terug. Uitvoerwaarden worden opgeslagen in de geschiedenis van de implementatie en niet is beveiligd.

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

## <a name="providers"></a>Providers

`providers(providerNamespace, [resourceType])`

Retourneert informatie over een resourceprovider en de ondersteunde resourcetypen. Als u een resourcetype niet opgeeft, retourneert de functie de ondersteunde typen voor de resourceprovider.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |string |Namespace van de provider |
| ResourceType |Nee |string |Het type resource binnen de opgegeven naamruimte. |

### <a name="return-value"></a>Retourwaarde

Elk type ondersteunde wordt geretourneerd in de volgende indeling: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Matrix volgorde van de geretourneerde waarden wordt niet gegarandeerd.

### <a name="providers-example"></a>Voor beeld van providers

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) laat zien hoe u de provider-functie te gebruiken:

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

Voor de **Microsoft.Web** resourceprovider en **sites** brontype in het voorgaande voorbeeld retourneert een object in de volgende indeling:

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

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Retourneert een object waarmee de runtimestatus van een resource.

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceName of resourceIdentifier |Ja |string |Naam of de unieke id van een resource. Wanneer u verwijst naar een resource in de huidige sjabloon, geef de naam van de resource als parameter. Wanneer u verwijst naar een eerder geïmplementeerde resource, geeft u de resource-ID op. |
| apiVersion |Nee |string |API-versie van de opgegeven resource. Deze parameter opgeeft als de bron is niet in dezelfde sjabloon ingericht. Normaal gesproken in de indeling, **jjjj-mm-dd**. Zie voor een geldige API-versie voor uw resource [sjabloon verwijzing](/azure/templates/). |
| 'Volledige' |Nee |string |De waarde die aangeeft of het volledige resource-object te retourneren. Als u geen opgeeft `'Full'`, alleen het object voor eigenschappen van de resource wordt geretourneerd. De volledige-object bevat waarden, zoals de resource-ID en de locatie. |

### <a name="return-value"></a>Retourwaarde

Elk resourcetype geeft als resultaat van de verschillende eigenschappen voor de referentie-functie. De functie retourneren niet een enkele, vooraf gedefinieerde indeling. De geretourneerde waarde verschilt ook, op basis van of u het volledige object hebt opgegeven. Overzicht van de eigenschappen voor een resourcetype, het object in de uitvoersectie zoals wordt weergegeven in het voorbeeld te retourneren.

### <a name="remarks"></a>Opmerkingen

De functie verwijzing haalt de runtimestatus van een eerder geïmplementeerde resource of een resource in de huidige sjabloon wordt geïmplementeerd. In dit artikel ziet u voorbeelden voor beide scenario's.

Meestal gebruikt u de **verwijzing** functie voor het retourneren van een bepaalde waarde van een object, zoals het eindpunt van blob-URI of de volledig gekwalificeerde domeinnaam.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Gebruik `'Full'` wanneer u waarden in de resource die geen deel uitmaken van het schema eigenschappen nodig hebt. Bijvoorbeeld, om toegangsbeleid voor sleutelkluizen, Hiermee worden de identiteitseigenschappen voor een virtuele machine.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
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

### <a name="valid-uses"></a>Geldige toepassingen

De referentie-functie kan alleen worden gebruikt in de eigenschappen van de resourcedefinitie van een en de uitvoersectie van een sjabloon of de implementatie. In combi natie met [eigenschaps herhaling](resource-group-create-multiple.md#property-iteration)kunt u de functie Reference gebruiken `input` voor omdat de expressie wordt toegewezen aan de resource-eigenschap. U kunt dit `count` niet gebruiken omdat het aantal moet worden bepaald voordat de verwijzings functie wordt opgelost.

U kunt de functie Reference niet gebruiken in de uitvoer van een [geneste sjabloon](resource-group-linked-templates.md#nested-template) om een resource te retour neren die u in de geneste sjabloon hebt geïmplementeerd. Gebruik in plaats daarvan een [gekoppelde sjabloon](resource-group-linked-templates.md#external-template-and-external-parameters).

Als u de functie **Reference** gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd.  Er **wordt** een fout bericht weer geven als de verwijzings functie verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](resource-group-template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en verwijst naar een voorwaardelijk geïmplementeerde resource.

### <a name="implicit-dependency"></a>Impliciete afhankelijkheid

Met behulp van de referentie-functie, declareert u impliciet dat een resource afhankelijk van een andere resource is als de bron waarnaar wordt verwezen, is ingericht in dezelfde sjabloon en u naar de resource met de naam (geen resource-ID verwijst). U hoeft niet te gebruiken ook de eigenschap DEPENDSON te maken. De functie wordt niet geëvalueerd totdat de resource waarnaar wordt verwezen, de implementatie is voltooid.

### <a name="resource-name-or-identifier"></a>Resource naam of-id

Wanneer u verwijst naar een resource die in dezelfde sjabloon is geïmplementeerd, geeft u de naam van de resource op.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Wanneer u verwijst naar een resource die niet in dezelfde sjabloon is geïmplementeerd, geeft u de resource-ID op.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

U kunt een volledig gekwalificeerde resource naam opgeven om te voor komen dat u dubbel zinnigheid weet over welke resource u verwijst.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

Bij het samen stellen van een volledig gekwalificeerde verwijzing naar een resource is de volg orde voor het combi neren van segmenten van het type en naam niet gewoon een samen voeging van de twee. Gebruik in plaats daarvan een reeks van *type-en naam* paren van minst specifieke voor de meest specifieke kenmerken:

**{resource-Provider-namespace}/{Parent-resource-type}/{Parent-resource-name} [/{Child-resource-type}/{Child-resource-name}]**

Bijvoorbeeld:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`is correct `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` is niet juist

### <a name="reference-example"></a>Referentie voorbeeld

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) implementeert een resource, en verwijst naar die resource.

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

Het vorige voorbeeld retourneert de twee objecten. Het object voor eigenschappen is in de volgende indeling:

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

Het volledige object verkeert in de volgende indeling:

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

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) verwijst naar een opslagaccount die in deze sjabloon is niet geïmplementeerd. Het opslagaccount bestaat al binnen hetzelfde abonnement.

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

`resourceGroup()`

Retourneert een object dat de huidige resourcegroep vertegenwoordigt. 

### <a name="return-value"></a>Retourwaarde

Het geretourneerde object is in de volgende indeling:

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

De eigenschap **managedBy** wordt alleen geretourneerd voor resource groepen die resources bevatten die worden beheerd door een andere service. Voor beheerde toepassingen, Databricks en AKS is de waarde van de eigenschap de resource-ID van de beheer bron.

### <a name="remarks"></a>Opmerkingen

De `resourceGroup()` functie kan niet worden gebruikt in een sjabloon die is [geïmplementeerd op het abonnements niveau](deploy-to-subscription.md). Deze kan alleen worden gebruikt in sjablonen die zijn geïmplementeerd in een resource groep.

Een algemene gebruik van de resourceGroup-functie is het maken van resources in dezelfde locatie als de resourcegroep. Het volgende voorbeeld wordt de locatie voor resourcegroep om toe te wijzen de locatie voor een website.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

U kunt ook de functie resourceGroup gebruiken om labels van de resource groep toe te passen op een resource. Zie [labels Toep assen op resource groep](resource-group-using-tags.md#apply-tags-from-resource-group)voor meer informatie.

### <a name="resource-group-example"></a>Voor beeld van resource groep

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) worden de eigenschappen van de resourcegroep geretourneerd.

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

Het vorige voorbeeld retourneert een object in de volgende indeling:

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

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Retourneert de unieke id van een resource. U kunt deze functie gebruiken als de resourcenaam van de niet eenduidig of niet ingericht binnen dezelfde sjabloon is. 

### <a name="parameters"></a>Parameters

| Parameter | Vereist | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |tekenreeks (In GUID-indeling) |Standaard wordt het huidige abonnement. Deze waarde opgeven wanneer u nodig hebt om op te halen van een resource in een ander abonnement. |
| resourceGroupName |Nee |string |Standaardwaarde is de huidige resourcegroep. Deze waarde opgeven wanneer u nodig hebt om op te halen van een resource in een andere resourcegroep. |
| ResourceType |Ja |string |Het type resource, met inbegrip van de naamruimte van de resource-provider. |
| resourceName1 |Ja |string |De naam van de resource. |
| resourceName2 |Nee |string |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retourwaarde

De id wordt geretourneerd in de volgende indeling:

**/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}**


### <a name="remarks"></a>Opmerkingen

Het aantal para meters dat u opgeeft, varieert op basis van het feit of de resource een bovenliggende of onderliggende resource is en of de resource zich in hetzelfde abonnement of dezelfde resource groep bevindt.

Als u de resource-ID voor een bovenliggende resource in hetzelfde abonnement en dezelfde resource groep wilt ophalen, geeft u het type en de naam van de resource op.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Als u de resource-ID voor een onderliggende resource wilt ophalen, moet u rekening best Eden aan het aantal segmenten in het resource type. Geef een resource naam op voor elk segment van het resource type. De naam van het segment komt overeen met de resource die bestaat voor dat deel van de hiërarchie.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Als u de resource-ID voor een resource in hetzelfde abonnement maar een andere resource groep wilt ophalen, geeft u de naam van de resource groep op.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Als u de resource-ID voor een resource in een ander abonnement en een andere resource groep wilt ophalen, geeft u de abonnements-ID en de naam van de resource groep op.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Wanneer u gebruikt met een [implementatie op abonnements niveau](deploy-to-subscription.md), `resourceId()` kan de functie alleen de id ophalen van resources die op dat niveau zijn geïmplementeerd. U kunt bijvoorbeeld de ID van een beleids definitie of roldefinitie ophalen, maar niet de ID van een opslag account. Voor implementaties naar een resource groep is het tegenovergestelde waar. U kunt de resource-ID van resources die zijn geïmplementeerd op abonnements niveau niet ophalen.

Als u de resource-ID van een resource op abonnements niveau wilt ophalen bij het implementeren van het abonnements bereik, gebruikt u:

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Vaak het geval is, moet u deze functie wilt gebruiken bij het gebruik van een storage-account of een virtueel netwerk in een andere resourcegroep. Het volgende voorbeeld laat zien hoe een resource vanaf een externe resourcegroep eenvoudig kan worden gebruikt:

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

### <a name="resource-id-example"></a>Resource-ID-voor beeld

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) geeft als resultaat de resource-ID voor een opslagaccount in de resourcegroep:

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

De uitvoer uit het vorige voorbeeld met de standaardwaarden is:

| Name | Type | Waarde |
| ---- | ---- | ----- |
| sameRGOutput | Reeks | /Subscriptions/{Current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Reeks | /Subscriptions/{Current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Reeks | /Subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Reeks | /Subscriptions/{Current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/servername/databases/databaseName |

## <a name="subscription"></a>subscription

`subscription()`

Retourneert informatie over het abonnement voor de huidige implementatie. 

### <a name="return-value"></a>Retourwaarde

De functie geeft als resultaat van de volgende indeling:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Voor beeld van abonnement

De volgende [voorbeeldsjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) toont de abonnement-functie in de uitvoersectie aangeroepen. 

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

## <a name="next-steps"></a>Volgende stappen

* Zie voor een beschrijving van de secties in een Azure Resource Manager-sjabloon, [Authoring Azure Resource Manager-sjablonen](resource-group-authoring-templates.md).
* U kunt meerdere sjablonen samenvoegen, Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md).
* Op een opgegeven aantal keren herhalen bij het maken van een type resource, Zie [meerdere exemplaren van resources maken in Azure Resource Manager](resource-group-create-multiple.md).
* Zie voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt, [een toepassing implementeren met Azure Resource Manager-sjabloon](resource-group-template-deploy.md).

