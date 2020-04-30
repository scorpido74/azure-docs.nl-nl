---
title: 'Snelstartgids: een Event Hub maken met behulp van Power shell-Azure Event Hubs'
description: In deze snelstart wordt beschreven hoe u een event hub maakt met behulp van Azure PowerShell en vervolgens gebeurtenissen verzendt en ontvangt met behulp van .NET Standard SDK.
services: event-hubs
author: spelluru
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 89ec1957e75fa45eef6c7939a77e5cc1b3cf7806
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162120"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Snelstart: Een event hub maken met behulp van Azure PowerShell

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze snelstart maakt u een Event Hub met behulp van Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account][] voordat u begint.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of hoger.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal gebruikt, moet u de nieuwste versie van PowerShell uitvoeren om deze snelstart te kunnen voltooien. Als u Azure PowerShell wilt installeren of upgraden, raadpleegt u [Azure PowerShell installeren en configureren](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische verzameling van Azure-resources. U hebt een resourcegroep nodig om een Event Hub te maken. 

In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio VS - oost. Vervang `myResourceGroup` door de naam van de resourcegroep die u wilt gebruiken:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Zodra uw resourcegroep is gemaakt, maakt u een Event Hubs-naamruimte in die resourcegroep. Een Event Hubs-naamruimte biedt een unieke Fully Qualified Domain Name waarin uw Event Hub kunt maken. Vervang `namespace_name` door een unieke naam voor de naamruimte:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Een Event Hub maken

Nu u een ​​Event Hubs-naamruimte hebt, maakt u een Event Hub binnen die naamruimte:  
Toegestane periode voor `MessageRetentionInDays` ligt tussen één en zeven dagen.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gefeliciteerd! U hebt Azure PowerShell gebruikt om een ​​Event Hubs-naamruimte en een Event Hub binnen die naamruimte te maken. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u de Event Hubs-naamruimte gemaakt en voorbeeldtoepassingen gebruikt om gebeurtenissen te verzenden en ontvangen vanuit uw Event Hub. Voor stapsgewijze instructies voor het verzenden van gebeurtenissen naar (of) het ontvangen van gebeurtenissen van een Event Hub, raadpleegt u de zelf studies voor het **verzenden en ontvangen van gebeurtenissen** : 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [OK](event-hubs-go-get-started-send.md)
- [C (alleen verzenden)](event-hubs-c-getstarted-send.md)
- [Apache Storm (alleen ontvangen)](event-hubs-storm-getstarted-receive.md)


[maakt u een gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
