---
title: Aangepast veld toewijzen aan azure-gebeurtenisrasterschema
description: In dit artikel wordt beschreven hoe u uw aangepaste schema converteren naar het Azure Event Grid-schema wanneer uw gebeurtenisgegevens niet overeenkomen met het schema van gebeurtenisraster.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721656"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Aangepaste velden toewijzen aan Event Grid-schema

Als uw gebeurtenisgegevens niet overeenkomen met het verwachte [gebeurtenisrasterschema,](event-schema.md)u gebeurtenisraster nog steeds gebruiken om gebeurtenis naar abonnees te leiden. In dit artikel wordt beschreven hoe u uw schema toewijzen aan het schema voor gebeurtenisraster.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Preview-functie installeren

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Origineel gebeurtenisschema

Stel dat u een toepassing hebt die gebeurtenissen in de volgende indeling verzendt:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Hoewel die indeling niet overeenkomt met het vereiste schema, u met Gebeurtenisraster uw velden toewijzen aan het schema. U ook de waarden in het oorspronkelijke schema ontvangen.

## <a name="create-custom-topic-with-mapped-fields"></a>Aangepaste onderwerp maken met toegewezen velden

Wanneer u een aangepast onderwerp maakt, geeft u op hoe u velden van uw oorspronkelijke gebeurtenis toewijzen aan het gebeurtenisrasterschema. Er zijn drie waarden die u gebruikt om de toewijzing aan te passen:

* De waarde van het **invoerschema** geeft het type schema op. De beschikbare opties zijn het CloudEvents-schema, het aangepaste gebeurtenisschema of het gebeurtenisrasterschema. De standaardwaarde is het schema gebeurtenisraster. Wanneer u aangepaste toewijzing maakt tussen uw schema en het gebeurtenisrasterschema, gebruikt u het aangepaste gebeurtenisschema. Wanneer gebeurtenissen zich in het cloudgebeurtenissenschema bevinden, gebruikt u het cloudevents-schema.

* De eigenschap **standaardwaarden toewijzen** geeft standaardwaarden op voor velden in het schema gebeurtenisraster. U standaardwaarden `subject` `eventtype`instellen `dataversion`voor, en. Normaal gesproken gebruikt u deze parameter wanneer uw aangepaste schema geen veld bevat dat overeenkomt met een van deze drie velden. U bijvoorbeeld opgeven dat de gegevensversie altijd is ingesteld op **1.0**.

* De **waarde voor toewijzingsvelden** brengt velden van uw schema naar het gebeurtenisrasterschema toe. U geeft waarden op in door de ruimte gescheiden sleutel-/waardeparen. Gebruik voor de sleutelnaam de naam van het veld gebeurtenisraster. Gebruik voor de waarde de naam van uw veld. U belangrijke `id`namen `topic` `eventtime`gebruiken `subject` `eventtype`voor `dataversion`,,, en.

Als u een aangepast onderwerp wilt maken met Azure CLI, gebruikt u:

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

## <a name="subscribe-to-event-grid-topic"></a>U abonneren op het onderwerp gebeurtenisraster

Wanneer u zich abonneert op het aangepaste onderwerp, geeft u het schema op dat u wilt gebruiken voor het ontvangen van de gebeurtenissen. U geeft het cloudgebeurtenissenschema, het aangepaste gebeurtenisschema of het schema voor gebeurtenisrasterop. De standaardwaarde is het schema gebeurtenisraster.

In het volgende voorbeeld wordt een gebeurtenisrasteronderwerp geabonneerd en wordt het schema gebeurtenisraster gebruikt. Gebruik voor Azure CLI:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

In het volgende voorbeeld wordt het invoerschema van de gebeurtenis gebruikt:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

In het volgende voorbeeld wordt een gebeurtenisrasteronderwerp geabonneerd en wordt het schema gebeurtenisraster gebruikt. Gebruik voor PowerShell:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

In het volgende voorbeeld wordt het invoerschema van de gebeurtenis gebruikt:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Gebeurtenis naar onderwerp publiceren

U bent nu klaar om een gebeurtenis naar het aangepaste onderwerp te verzenden en het resultaat van de toewijzing te bekijken. Het volgende script om een gebeurtenis in het voorbeeldschema te [plaatsen:](#original-event-schema)

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

Kijk nu eens naar je WebHook-eindpunt. De twee abonnementen leverden gebeurtenissen in verschillende schema's.

Het eerste abonnement maakte gebruik van het gebeurtenisrasterschema. Het formaat van het geleverde evenement is:

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

Deze velden bevatten de toewijzingen van het aangepaste onderwerp. **myEventTypeField** is toegewezen aan **EventType**. De standaardwaarden voor **DataVersion** en **Onderwerp** worden gebruikt. Het object **Gegevens** bevat de oorspronkelijke gebeurtenisschemavelden.

In het tweede abonnement werd het invoergebeurtenisschema gebruikt. Het formaat van het geleverde evenement is:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Merk op dat de oorspronkelijke velden zijn geleverd.

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het bezorgen en opnieuw proberen van [gebeurtenissen, het verzenden van het gebeurtenisrasterbericht en het opnieuw proberen](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
