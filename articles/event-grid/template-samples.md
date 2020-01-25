---
title: Voorbeeldsjablonen van Azure Resource Manager - Event Grid | Microsoft Docs
description: In dit artikel vindt u een lijst met Azure Resource Manager sjabloon voorbeelden voor Azure Event Grid op GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720619"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Azure Resource Manager-sjablonen voor Event Grid

Zie [Microsoft.EventGrid resource types](/azure/templates/microsoft.eventgrid/allversions) (Microsoft.EventGrid-resourcetypen) voor de JSON-syntaxis en eigenschappen die u kunt gebruiken in een sjabloon. De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor Event Grid.

| | |
|-|-|
|**Event Grid-abonnementen**||
| [Aangepast onderwerp en abonnement met WebHook-eindpunt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Hiermee wordt een aangepast Event Grid-onderwerp geïmplementeerd. Hiermee maakt u een abonnement op dat aangepaste onderwerp dat gebruikmaakt van een WebHook-eindpunt. |
| [Abonnement op aangepast onderwerp met EventHub-eindpunt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Hiermee maakt u een Event Grid-abonnement voor een aangepast onderwerp. Het abonnement maakt gebruik van een Event Hub voor het eindpunt. |
| [Azure-abonnement of abonnement op resourcegroep](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Hiermee abonneert u zich op gebeurtenissen voor een resourcegroep of neemt u een Azure-abonnement. De resourcegroep die u als het doel opgeeft tijdens de implementatie, is de bron van gebeurtenissen. Het abonnement maakt gebruik van een webhook voor het eindpunt. |
| [Blob-opslagaccount en -abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Hiermee implementeert u een Azure Blob-opslagaccount en abonneert u zich op gebeurtenissen van die opslagaccount. |
| | |
