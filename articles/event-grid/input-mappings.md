---
title: Aangepast veld toegewezen aan Azure Event Grid-schema
description: In dit artikel wordt beschreven hoe u uw aangepaste schema converteert naar het Azure Event Grid schema wanneer uw gebeurtenis gegevens niet overeenkomen met Event Grid schema.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721656"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Aangepaste velden toewijzen aan Event Grid-schema

Als uw gebeurtenisgegevens komt niet overeen met de verwachte [Event Grid schema](event-schema.md), u kunt nog steeds Event Grid op route-gebeurtenis voor abonnees. In dit artikel wordt beschreven hoe u uw schema toegewezen aan het Event Grid-schema.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Preview-functie installeren

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Gebeurtenisschema in het oorspronkelijke

Stel dat u hebt een toepassing die gebeurtenissen in de volgende indeling verzendt:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Hoewel deze indeling komt niet overeen met de vereiste schema, wordt er Event Grid kunt u uw velden toewijzen aan het schema. Of u kunt de waarden in het oorspronkelijke schema ontvangen.

## <a name="create-custom-topic-with-mapped-fields"></a>Aangepast onderwerp maken met toegewezen velden

Bij het maken van een aangepast onderwerp, Geef op hoe velden van uw oorspronkelijke gebeurtenis naar het event grid-schema toe te wijzen. Er zijn drie waarden die u gebruiken om aan te passen van de toewijzing:

* De **invoerschema** waarde geeft het type van het schema. De beschikbare opties zijn een CloudEvents-schema, aangepaste gebeurtenisschema of Event Grid-schema. De standaardwaarde is Event Grid-schema. Bij het maken van aangepaste toewijzing tussen het schema en het event grid-schema, moet u aangepaste gebeurtenisschema gebruiken. Wanneer gebeurtenissen zich in een CloudEvents-schema, gebruikt u een Cloudevents-schema.

* De **standaardwaarden toewijzen** -eigenschap geeft de standaardwaarden voor de velden in het schema voor Event Grid. U kunt standaardwaarden instellen voor `subject`, `eventtype`, en `dataversion`. Meestal gebruikt u deze parameter wanneer uw aangepaste schema bevat geen een veld dat overeenkomt met een van deze drie velden. U kunt bijvoorbeeld opgeven die gegevensversie is altijd ingesteld op **1.0**.

* De **velden toewijzen** waardetoewijzingen velden uit uw schema naar het event grid-schema. Geeft u waarden in een door spaties gescheiden sleutel/waarde-paren. Gebruik de naam van het event grid-veld voor de naam van de sleutel. Gebruik de naam van het veld voor de waarde. U kunt de namen van de sleutels voor `id`, `topic`, `eventtime`, `subject`, `eventtype`, en `dataversion`.

Als u wilt een aangepast onderwerp maken met Azure CLI, gebruikt u:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Gebruik voor PowerShell:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Abonneren op event grid-onderwerp

Wanneer u zich abonneert op het aangepaste onderwerp, geeft u het schema dat u gebruiken wilt voor het ontvangen van de gebeurtenissen. U opgeven dat het een CloudEvents-schema, aangepaste gebeurtenisschema of Event Grid-schema. De standaardwaarde is Event Grid-schema.

Het volgende voorbeeld zich abonneert op een event grid-onderwerp en de Event Grid-schema wordt gebruikt. Gebruik voor Azure CLI:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

Het volgende voorbeeld wordt de invoer-schema van de gebeurtenis:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Het volgende voorbeeld zich abonneert op een event grid-onderwerp en de Event Grid-schema wordt gebruikt. Gebruik voor PowerShell:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

Het volgende voorbeeld wordt de invoer-schema van de gebeurtenis:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Gebeurtenis publiceren naar onderwerp

U kunt nu een gebeurtenis verzenden naar het aangepaste onderwerp en het resultaat van de toewijzing te bekijken. Het volgende script voor het plaatsen van een gebeurtenis in de [voorbeeldschema](#original-event-schema):

Gebruik voor Azure CLI:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Gebruik voor PowerShell:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Bekijk nu uw WebHook-eindpunt. De twee abonnementen gebeurtenissen in verschillende schema's geleverd.

Het eerste abonnement gebruikt gebeurtenisschema in het raster. De indeling van de geleverde gebeurtenis is:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Deze velden bevatten de toewijzingen van het aangepaste onderwerp. **myEventTypeField** is toegewezen aan **type gebeurtenis**. De standaardwaarden voor **DataVersion** en **onderwerp** worden gebruikt. De **gegevens** object bevat de oorspronkelijke gebeurtenis schemavelden.

Het tweede abonnement gebruikt de invoer-schema. De indeling van de geleverde gebeurtenis is:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

U ziet dat de oorspronkelijke velden zijn geleverd.

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over de bezorging van gebeurtenissen en nieuwe pogingen, [bezorging van berichten van Event Grid en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Als u wilt snel aan de slag met Event Grid, Zie [aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md).
