---
title: Gebeurtenissen filteren voor Azure Event Grid
description: In dit artikel ziet u hoe u gebeurtenissen filteren (op gebeurtenistype, op onderwerp, door operators en gegevens, enz.) bij het maken van een abonnement op het eventgrid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454020"
---
# <a name="filter-events-for-event-grid"></a>Gebeurtenissen filteren op gebeurtenisraster

In dit artikel ziet u hoe u gebeurtenissen filtert bij het maken van een abonnement op het gebeurtenisraster. Zie [Gebeurtenisfiltering voor gebeurtenisrasterabonnementen begrijpen voor](event-filtering.md)meer informatie over de opties voor gebeurtenisfiltering .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filteren op gebeurtenistype

Wanneer u een abonnement op het gebeurtenisraster maakt, u opgeven welke [gebeurtenistypen](event-schema.md) u naar het eindpunt wilt verzenden. De voorbeelden in deze sectie maken gebeurtenisabonnementen voor een resourcegroep, maar beperken de gebeurtenissen die naar `Microsoft.Resources.ResourceWriteFailure` en `Microsoft.Resources.ResourceWriteSuccess`. Zie Filteren op geavanceerde operatoren en gegevensvelden als u meer flexibiliteit nodig hebt bij het filteren van gebeurtenissen op gebeurtenistypen.

Gebruik voor PowerShell `-IncludedEventType` de parameter bij het maken van het abonnement.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Gebruik voor Azure `--included-event-types` CLI de parameter. In het volgende voorbeeld wordt Azure CLI gebruikt in een Bash-shell:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Gebruik de `includedEventTypes` eigenschap voor een resourcemanagersjabloon.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filteren op onderwerp

U gebeurtenissen filteren op het onderwerp in de gebeurtenisgegevens. U een waarde opgeven die moet overeenkomen voor het begin of einde van het onderwerp. Zie Filteren op geavanceerde operatoren en gegevensvelden als u meer flexibiliteit nodig hebt bij het filteren van gebeurtenissen op onderwerp.

In het volgende PowerShell-voorbeeld maakt u een gebeurtenisabonnement dat aan het begin van het onderwerp wordt gefilterd. U gebruikt `-SubjectBeginsWith` de parameter om gebeurtenissen te beperken tot gebeurtenissen voor een specifieke resource. U geeft de bron-ID van een netwerkbeveiligingsgroep door.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

In het volgende PowerShell-voorbeeld wordt een abonnement gemaakt voor een blobopslag. Het beperkt gebeurtenissen tot gebeurtenissen met `.jpg`een onderwerp dat eindigt in .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

In het volgende Azure CLI-voorbeeld maakt u een gebeurtenisabonnement dat aan het begin van het onderwerp wordt gefilterd. U gebruikt `--subject-begins-with` de parameter om gebeurtenissen te beperken tot gebeurtenissen voor een specifieke resource. U geeft de bron-ID van een netwerkbeveiligingsgroep door.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

In het volgende Azure CLI-voorbeeld wordt een abonnement gemaakt voor een blob-opslag. Het beperkt gebeurtenissen tot gebeurtenissen met `.jpg`een onderwerp dat eindigt in .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

In het volgende voorbeeld van resourcebeheersjabloon maakt u een gebeurtenisabonnement dat aan het begin van het onderwerp wordt gefilterd. U gebruikt `subjectBeginsWith` de eigenschap om gebeurtenissen te beperken tot gebeurtenissen voor een specifieke resource. U geeft de bron-ID van een netwerkbeveiligingsgroep door.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

In het volgende sjabloonvoorbeeld ResourceManager wordt een abonnement gemaakt voor een blobopslag. Het beperkt gebeurtenissen tot gebeurtenissen met `.jpg`een onderwerp dat eindigt in .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filteren op operatoren en gegevens

Voor meer flexibiliteit in het filteren u operators en gegevenseigenschappen gebruiken om gebeurtenissen te filteren.

### <a name="subscribe-with-advanced-filters"></a>U abonneren met geavanceerde filters

Zie Geavanceerd filteren voor meer informatie over de operatoren en sleutels die u gebruiken voor geavanceerde [filtering.](event-filtering.md#advanced-filtering)

Met deze voorbeelden wordt een aangepast onderwerp gemaakt. Ze abonneren zich op het aangepaste onderwerp en filteren op een waarde in het gegevensobject. Gebeurtenissen waarop de eigenschap kleur is ingesteld op blauw, rood of groen, worden naar het abonnement verzonden.

Gebruik voor Azure CLI:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

U ziet dat er een [vervaldatum](concepts.md#event-subscription-expiration) is ingesteld voor het abonnement.

Gebruik voor PowerShell:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Testfilter

Als u het filter wilt testen, verzendt u een gebeurtenis met het kleurveld dat is ingesteld op groen. Omdat groen een van de waarden in het filter is, wordt de gebeurtenis naar het eindpunt geleverd.

Gebruik voor Azure CLI:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Gebruik voor PowerShell:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Als u een scenario wilt testen waarin de gebeurtenis niet wordt verzonden, verzendt u een gebeurtenis met het kleurveld ingesteld op geel. Geel is niet een van de waarden die in het abonnement zijn opgegeven, dus de gebeurtenis wordt niet aan uw abonnement geleverd.

Gebruik voor Azure CLI:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Gebruik voor PowerShell:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisnetbezorging controleren](monitor-event-delivery.md)voor informatie over het bewaken van gebeurtenisleveringen.
* Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie over de verificatiesleutel.
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
