---
title: Azure Resource Manager-sjabloon functies-resources | Microsoft Docs
description: Hierin worden de functies beschreven die u kunt gebruiken in een Azure Resource Manager sjabloon om waarden over resources op te halen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: tomfitz
ms.openlocfilehash: dc39c727526f55039a5e18a8fd2aeeb4f25234a6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965636"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Resource functies voor Azure Resource Manager sjablonen

Resource Manager biedt de volgende functies voor het ophalen van resource waarden:

* [extensionResourceId](#extensionresourceid)
* [orderverzamellijst](#list)
* [hardwareproviders](#providers)
* [referentielaag](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [abonnement](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Zie [implementatie waarde Functions](resource-group-template-functions-deployment.md)om waarden op te halen uit para meters, variabelen of de huidige implementatie.

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Retourneert de resource-ID voor een [extensie resource](extension-resource-types.md), een resource type dat wordt toegepast op een andere resource om aan de mogelijkheden ervan toe te voegen.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |string |De resource-ID voor de resource waarop de uitbreidings resource wordt toegepast. |
| resourceType |Ja |string |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Ja |string |De naam van de resource. |
| resourceName2 |Nee |string |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retour waarde

De basis indeling van de resource-ID die wordt geretourneerd door deze functie is:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Het bereik segment varieert van de resource die wordt uitgebreid.

Wanneer de extensie resource wordt toegepast op een **resource**, wordt de resource-id geretourneerd met de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **resource groep**, is de notatie:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **abonnement**, is de indeling:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wanneer de extensie resource wordt toegepast op een **beheer groep**, is de indeling:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId-voor beeld

In het volgende voor beeld wordt de resource-ID voor een resource groeps vergrendeling geretourneerd.

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

## <a name="list"></a>orderverzamellijst

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

De syntaxis voor deze functie is afhankelijk van de naam van de lijst bewerkingen. Elke implementatie retourneert waarden voor het resource type dat een lijst bewerking ondersteunt. De naam van de bewerking moet beginnen met `list`. Enkele algemene gebruiks vormen zijn `listKeys` en `listSecrets`. 

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| ResourceName of resourceIdentifier |Ja |string |De unieke id voor de resource. |
| apiVersion |Ja |string |API-versie van de runtime status van de resource. Doorgaans in de notatie **jjjj-mm-dd**. |
| functionValues |Nee |object | Een object met waarden voor de functie. Geef dit object alleen op voor functies die ondersteuning bieden voor het ontvangen van een object met parameter waarden, zoals **listAccountSas** op een opslag account. In dit artikel wordt een voor beeld gegeven van het door geven van functie waarden. | 

### <a name="valid-uses"></a>Geldige toepassingen

De functies List kunnen alleen worden gebruikt in de eigenschappen van een resource definitie en de sectie outputs van een sjabloon of implementatie. In combi natie met [eigenschaps herhaling](resource-group-create-multiple.md#property-iteration)kunt u de functies list voor `input` gebruiken omdat de expressie wordt toegewezen aan de eigenschap resource. U kunt deze niet gebruiken met `count` omdat het aantal moet worden bepaald voordat de lijst functie wordt opgelost.

### <a name="implementations"></a>Implementaties

Het mogelijke gebruik van lijst * wordt weer gegeven in de volgende tabel.

| Resourcetype | Functie naam |
| ------------- | ------------- |
| Micro soft. AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Micro soft. AppConfiguration/configurationStores | Listkeys ophalen |
| Micro soft. Automation/automationAccounts | [Listkeys ophalen](/rest/api/automation/keys/listbyautomationaccount) |
| Micro soft. batch/batchAccounts | [listkeys ophalen](/rest/api/batchmanagement/batchaccount/getkeys) |
| Micro soft. BatchAI/werk ruimten/experimenten/taken | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Micro soft. Block Chain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Micro soft. Block Chain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Micro soft. BotService/botServices/channels | listChannelWithKeys |
| Micro soft. cache/redis | [Listkeys ophalen](/rest/api/redis/redis/listkeys) |
| Micro soft. CognitiveServices/accounts | [Listkeys ophalen](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Micro soft. ContainerRegistry/registers | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Micro soft. ContainerRegistry/registers | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Micro soft. ContainerRegistry/registers | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Micro soft. ContainerRegistry/registers/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Micro soft. ContainerRegistry/registers/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Micro soft. ContainerRegistry/registers/taken | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Micro soft. container service/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Micro soft. container service/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Micro soft. container service/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Micro soft. DataBox/Jobs | listCredentials |
| Micro soft. DataFactory/datafactories/gateways | listauthkeys |
| Micro soft. DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Micro soft. DataLakeAnalytics/accounts/Storage accounts/containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Micro soft. devices/iotHubs | [listkeys ophalen](/rest/api/iothub/iothubresource/listkeys) |
| Micro soft. apparaten/provisioningServices/sleutels | [listkeys ophalen](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Micro soft. devices/provisioningServices | [listkeys ophalen](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Micro soft. DevTestLab/Labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Micro soft. DevTestLab/Labs/planningen | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Micro soft. DevTestLab/Labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Micro soft. DevTestLab/Labs/informatie | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Micro soft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Micro soft. DocumentDB/databaseAccounts | [Listkeys ophalen](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Micro soft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Micro soft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Micro soft. EventGrid/domeinen | [Listkeys ophalen](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Micro soft. EventGrid/topics | [Listkeys ophalen](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Micro soft. EventHub/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/eventhub/namespaces/listkeys) |
| Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/authorizationRules | [listkeys ophalen](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules | [listkeys ophalen](/rest/api/eventhub/eventhubs/listkeys) |
| Micro soft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Micro soft. LabServices/gebruikers | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Micro soft. LabServices/gebruikers | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Micro soft. Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts/assembly's | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Micro soft. Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Micro soft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Micro soft. Logic/integrationAccounts/-schema's | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Micro soft. Logic/werk stromen | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Micro soft. Logic/werk stromen | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Micro soft. Logic/werk stromen/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Micro soft. Logic/werk stromen/versies/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Micro soft. MachineLearning/webservices | [listkeys ophalen](/rest/api/machinelearning/webservices/listkeys) |
| Micro soft. MachineLearning/werk ruimten | listworkspacekeys |
| Micro soft. MachineLearningServices/werk ruimten/reken kracht | Listkeys ophalen |
| Micro soft. MachineLearningServices/werk ruimten | Listkeys ophalen |
| Micro soft. Maps/accounts | [Listkeys ophalen](/rest/api/maps-management/accounts/listkeys) |
| Micro soft. Media/Media Services/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Micro soft. Media/Media Services/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Micro soft. Media/Media Services/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Micro soft. Media/Media Services/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Micro soft. Network/applicationSecurityGroups | listIpConfigurations |
| Micro soft. notification hubs/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/notificationhubs/namespaces/listkeys) |
| Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules | [listkeys ophalen](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Micro soft. OperationalInsights/werk ruimten | [Listkeys ophalen](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Micro soft. relay/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/relay/namespaces/listkeys) |
| Micro soft. relay/naam ruimten/disasterRecoveryConfigs/authorizationRules | listkeys ophalen |
| Micro soft. relay/naam ruimten/HybridConnections/authorizationRules | [listkeys ophalen](/rest/api/relay/hybridconnections/listkeys) |
| Micro soft. relay/naam ruimten/WcfRelays/authorizationRules | [listkeys ophalen](/rest/api/relay/wcfrelays/listkeys) |
| Micro soft. Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Micro soft. Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Micro soft. ServiceBus/naam ruimten/authorizationRules | [listkeys ophalen](/rest/api/servicebus/namespaces/listkeys) |
| Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/authorizationRules | [listkeys ophalen](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Micro soft. ServiceBus/naam ruimten/wacht rijen/authorizationRules | [listkeys ophalen](/rest/api/servicebus/queues/listkeys) |
| Micro soft. ServiceBus/naam ruimten/onderwerpen/authorizationRules | [listkeys ophalen](/rest/api/servicebus/topics/listkeys) |
| Micro soft. SignalRService/Signa lering | [listkeys ophalen](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys ophalen](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Micro soft. StorSimple/managers/apparaten | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Micro soft. StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Micro soft. StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Micro soft. Web/connectionGateways | ListStatus |
| micro soft. Web/verbindingen | listconsentlinks |
| Micro soft. Web/customApis | listWsdlInterfaces |
| micro soft. Web/locaties | listwsdlinterfaces |
| micro soft. Web/apimanagementaccounts/api's/Connections | listconnectionkeys |
| micro soft. Web/apimanagementaccounts/api's/Connections | listsecrets |
| micro soft. web/sites/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| micro soft. web/sites/hybridconnectionnamespaces/relays | [listkeys ophalen](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| micro soft. web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| micro soft. web/sites/sleuven/functies | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Als u wilt bepalen welke resource typen een lijst bewerking hebben, hebt u de volgende opties:

* Bekijk de [rest API bewerkingen](/rest/api/) voor een resource provider en zoek naar lijst bewerkingen. Opslag accounts hebben bijvoorbeeld de Listkeys ophalen- [bewerking](/rest/api/storagerp/storageaccounts).
* Gebruik de Power shell [-cmdlet Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . In het volgende voor beeld worden alle lijst bewerkingen voor opslag accounts opgehaald:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Gebruik de volgende Azure CLI-opdracht om alleen de bewerkingen van de lijst te filteren:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Retour waarde

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

Andere lijst functies hebben verschillende retour indelingen. Als u de indeling van een functie wilt zien, neemt u deze op in de sectie outputs, zoals wordt weer gegeven in de voorbeeld sjabloon.

### <a name="remarks"></a>Opmerkingen

Geef de resource op met behulp van de resource naam of de [functie resourceId](#resourceid). Wanneer u een lijst functie gebruikt in dezelfde sjabloon die de resource van de verwijzing implementeert, gebruikt u de resource naam.

Als u een **lijst** functie gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een fout bericht weer **geven** als de functie List verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](resource-group-template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en-lijst met een voorwaardelijke geïmplementeerde resource.

### <a name="list-example"></a>Voor beeld van lijst

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ziet u hoe u de primaire en secundaire sleutels uit een opslag account retourneert in de sectie outputs. Er wordt ook een SAS-token voor het opslag account geretourneerd. 

Om het SAS-token op te halen, geeft u een object voor de verloop tijd door. De verloop tijd moet in de toekomst liggen. Dit voor beeld is bedoeld om te laten zien hoe u de lijst functies gebruikt. Normaal gesp roken gebruikt u het SAS-token in een resource waarde in plaats van het als uitvoer waarde te retour neren. Uitvoer waarden worden opgeslagen in de implementatie geschiedenis en zijn niet beveiligd.

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

## <a name="providers"></a>hardwareproviders

```json
providers(providerNamespace, [resourceType])
```

Retourneert informatie over een resource provider en de ondersteunde resource typen. Als u geen resource type opgeeft, retourneert de functie alle ondersteunde typen voor de resource provider.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |string |Naam ruimte van de provider |
| resourceType |Nee |string |Het type resource binnen de opgegeven naam ruimte. |

### <a name="return-value"></a>Retour waarde

Elk ondersteund type wordt in de volgende indeling geretourneerd: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Matrix volgorde van de geretourneerde waarden is niet gegarandeerd.

### <a name="providers-example"></a>Voor beeld van providers

In de volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ziet u hoe u de functie provider gebruikt:

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

Voor het resource type **micro soft. Web** resource provider en **sites** retourneert het voor gaande voor beeld een object in de volgende indeling:

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

## <a name="reference"></a>Referentielaag

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Retourneert een-object dat de runtime status van een resource aangeeft.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| ResourceName of resourceIdentifier |Ja |string |De naam of de unieke id van een resource. Als u naar een resource in de huidige sjabloon verwijst, geeft u alleen de resource naam op als para meter. Wanneer u verwijst naar een eerder geïmplementeerde resource, geeft u de resource-ID op. |
| apiVersion |Nee |string |API-versie van de opgegeven resource. Neem deze para meter op wanneer de resource niet binnen dezelfde sjabloon is ingericht. Doorgaans in de notatie **jjjj-mm-dd**. Zie voor een geldige API-versie voor uw resource [sjabloon verwijzing](/azure/templates/). |
| Waard |Nee |string |Waarde die aangeeft of het volledige Resource-object moet worden geretourneerd. Als u `'Full'`niet opgeeft, wordt alleen het eigenschappen object van de resource geretourneerd. Het volledige object bevat waarden zoals de resource-ID en de locatie. |

### <a name="return-value"></a>Retour waarde

Elk resource type retourneert verschillende eigenschappen voor de verwijzings functie. De functie retourneert geen enkele vooraf gedefinieerde notatie. De geretourneerde waarde verschilt ook op basis van het feit of u het volledige object hebt opgegeven. Als u de eigenschappen voor een resource type wilt zien, retourneert u het object in de sectie outputs, zoals wordt weer gegeven in het voor beeld.

### <a name="remarks"></a>Opmerkingen

De functie Reference haalt de runtime status op van een eerder geïmplementeerde resource of een resource die in de huidige sjabloon is geïmplementeerd. In dit artikel vindt u voor beelden van beide scenario's.

Normaal gesp roken gebruikt u de functie **Reference** om een bepaalde waarde van een object te retour neren, zoals de URI van het BLOB-eind punt of Fully Qualified Domain name.

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

Gebruik `'Full'` wanneer u resource waarden nodig hebt die geen deel uitmaken van het eigenschappen schema. Als u bijvoorbeeld een sleutel kluis toegangs beleid wilt instellen, haalt u de identiteits eigenschappen voor een virtuele machine op.

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

De functie Reference kan alleen worden gebruikt in de eigenschappen van een resource definitie en de sectie outputs van een sjabloon of implementatie. Bij gebruik in combi natie met [eigenschaps herhaling](resource-group-create-multiple.md#property-iteration)kunt u de functie Reference gebruiken voor `input`, omdat de expressie wordt toegewezen aan de eigenschap resource. U kunt deze niet gebruiken met `count` omdat het aantal moet worden bepaald voordat de verwijzings functie wordt opgelost.

U kunt de functie Reference niet gebruiken in de uitvoer van een [geneste sjabloon](resource-group-linked-templates.md#nested-template) om een resource te retour neren die u in de geneste sjabloon hebt geïmplementeerd. Gebruik in plaats daarvan een [gekoppelde sjabloon](resource-group-linked-templates.md#external-template).

Als u de functie **Reference** gebruikt in een resource die voorwaardelijk wordt geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd.  Er wordt een fout bericht weer geven als de **verwijzings** functie verwijst naar een resource die niet bestaat. Gebruik de functie **als** om te controleren of de functie alleen wordt geëvalueerd wanneer de resource wordt geïmplementeerd. Zie de [functie als](resource-group-template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en verwijst naar een voorwaardelijk geïmplementeerde resource.

### <a name="implicit-dependency"></a>Impliciete afhankelijkheid

Als u de functie Reference gebruikt, declareert u impliciet dat de ene resource afhankelijk is van een andere resource als de resource waarnaar wordt verwezen in dezelfde sjabloon is ingericht en u naar de resource verwijst met de naam (niet Resource-ID). U hoeft niet ook de eigenschap dependsOn te gebruiken. De functie wordt niet geëvalueerd tot de resource waarnaar wordt verwezen, de implementatie heeft voltooid.

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

de juiste `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` voor de `Microsoft.Compute/virtualMachines/myVM/extensions/myExt` is onjuist

### <a name="reference-example"></a>Referentie voorbeeld

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) implementeert een resource en verwijst naar die resource.

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

In het voor gaande voor beeld worden de twee objecten geretourneerd. Het eigenschappen object heeft de volgende indeling:

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

Het volledige object heeft de volgende indeling:

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

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) verwijst naar een opslag account dat niet in deze sjabloon is geïmplementeerd. Het opslag account bestaat al in hetzelfde abonnement.

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

Retourneert een object dat de huidige resource groep vertegenwoordigt. 

### <a name="return-value"></a>Retour waarde

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

De eigenschap **managedBy** wordt alleen geretourneerd voor resource groepen die resources bevatten die worden beheerd door een andere service. Voor beheerde toepassingen, Databricks en AKS is de waarde van de eigenschap de resource-ID van de beheer bron.

### <a name="remarks"></a>Opmerkingen

De functie `resourceGroup()` kan niet worden gebruikt in een sjabloon die is [geïmplementeerd op het abonnements niveau](deploy-to-subscription.md). Deze kan alleen worden gebruikt in sjablonen die zijn geïmplementeerd in een resource groep.

Een veelvoorkomend gebruik van de functie resourceGroup is het maken van resources op dezelfde locatie als de resource groep. In het volgende voor beeld wordt de locatie van de resource groep gebruikt om de locatie voor een website toe te wijzen.

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

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) retourneert de eigenschappen van de resource groep.

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

In het voor gaande voor beeld wordt een object in de volgende indeling geretourneerd:

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

Retourneert de unieke id van een resource. U gebruikt deze functie als de resource naam niet eenduidig of niet binnen dezelfde sjabloon is ingericht. 

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |teken reeks (in GUID-indeling) |De standaard waarde is het huidige abonnement. Geef deze waarde op als u een resource in een ander abonnement wilt ophalen. |
| resourceGroupName |Nee |string |De standaard waarde is de huidige resource groep. Geef deze waarde op wanneer u een resource in een andere resource groep wilt ophalen. |
| resourceType |Ja |string |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Ja |string |De naam van de resource. |
| resourceName2 |Nee |string |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retour waarde

De resource-ID wordt geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Bij gebruik in een [implementatie op abonnements niveau](deploy-to-subscription.md)wordt de resource-id in de volgende indeling geretourneerd:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Als u de ID in andere indelingen wilt ophalen, raadpleegt u:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

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

Vaak moet u deze functie gebruiken wanneer u een opslag account of een virtueel netwerk in een andere resource groep gebruikt. In het volgende voor beeld ziet u hoe een resource uit een externe resource groep eenvoudig kan worden gebruikt:

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

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) retourneert de resource-id voor een opslag account in de resource groep:

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

De uitvoer van het vorige voor beeld met de standaard waarden is:

| Naam | Type | Waarde |
| ---- | ---- | ----- |
| sameRGOutput | Tekenreeks | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Tekenreeks | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Tekenreeks | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Tekenreeks | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>abonnement

```json
subscription()
```

Retourneert Details over het abonnement voor de huidige implementatie. 

### <a name="return-value"></a>Retour waarde

De functie retourneert de volgende indeling:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Voor beeld van abonnement

De volgende [voorbeeld sjabloon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) toont de abonnements functie die is aangeroepen in de sectie outputs. 

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

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Retourneert de unieke id voor een resource die is geïmplementeerd op het abonnements niveau.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nee |teken reeks (in GUID-indeling) |De standaard waarde is het huidige abonnement. Geef deze waarde op als u een resource in een ander abonnement wilt ophalen. |
| resourceType |Ja |string |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Ja |string |De naam van de resource. |
| resourceName2 |Nee |string |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retour waarde

De id wordt geretourneerd in de volgende indeling:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-ID op te halen voor resources die zijn [geïmplementeerd in het abonnement](deploy-to-subscription.md) in plaats van een resource groep. De geretourneerde ID wijkt af van de waarde die wordt geretourneerd door de functie [resourceId](#resourceid) door de waarde van een resource groep niet op te nemen.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID-voor beeld

De volgende sjabloon wijst een ingebouwde rol toe. U kunt de app implementeren voor een resource groep of abonnement. De functie subscriptionResourceId wordt gebruikt om de resource-ID voor ingebouwde rollen op te halen.

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

Retourneert de unieke id voor een resource die is geïmplementeerd op Tenant niveau.

### <a name="parameters"></a>Parameters

| Parameter | Verplicht | Type | Beschrijving |
|:--- |:--- |:--- |:--- |
| resourceType |Ja |string |Het type resource, inclusief de naam ruimte van de resource provider. |
| resourceName1 |Ja |string |De naam van de resource. |
| resourceName2 |Nee |string |Volgend resource naam segment, indien nodig. |

Ga door met het toevoegen van resource namen als para meters wanneer het resource type meer segmenten bevat.

### <a name="return-value"></a>Retour waarde

De id wordt geretourneerd in de volgende indeling:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Opmerkingen

U gebruikt deze functie om de resource-ID op te halen voor een resource die wordt geïmplementeerd op de Tenant. De geretourneerde ID wijkt af van de waarden die worden geretourneerd door andere resource-ID-functies, met inbegrip van resource groep-of abonnements waarden.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager sjablonen ontwerpen](resource-group-authoring-templates.md)voor een beschrijving van de secties in een Azure Resource Manager sjabloon.
* Zie [gekoppelde sjablonen gebruiken met Azure Resource Manager](resource-group-linked-templates.md)om meerdere sjablonen samen te voegen.
* Als u een bepaald aantal keer wilt herhalen bij het maken van een type resource, raadpleegt u [meerdere exemplaren van resources maken in azure Resource Manager](resource-group-create-multiple.md).
* Zie [een toepassing implementeren met Azure Resource Manager sjabloon](resource-group-template-deploy.md)voor meer informatie over het implementeren van de sjabloon die u hebt gemaakt.

