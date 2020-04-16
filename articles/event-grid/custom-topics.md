---
title: Aangepaste onderwerpen in Azure Event Grid
description: Beschrijft aangepaste onderwerpen in Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 166a48d74d32c8b0a3a59310e693ea96ada29116
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394356"
---
# <a name="custom-topics-in-azure-event-grid"></a>Aangepaste onderwerpen in Azure Event Grid
Een gebeurtenisrasteronderwerp biedt een eindpunt waarin de bron gebeurtenissen verzendt. De uitgever maakt het onderwerp van het gebeurtenisraster en bepaalt of een gebeurtenisbron één onderwerp of meer dan één onderwerp nodig heeft. Een onderwerp wordt gebruikt voor een verzameling gerelateerde evenementen. Om te reageren op bepaalde soorten gebeurtenissen, bepalen abonnees op welke onderwerpen ze zich moeten abonneren.

**Aangepaste onderwerpen** zijn toepassings- en onderwerpen van derden. Wanneer u een aangepast onderwerp maakt of u er toegang toe krijgt, ziet u dat aangepaste onderwerp in uw abonnement. 

Bij het ontwerpen van uw toepassing hebt u flexibiliteit bij het bepalen van het aantal onderwerpen dat u wilt maken. Voor grote oplossingen maakt u een **aangepast onderwerp** voor elke categorie **gerelateerde gebeurtenissen.** Neem bijvoorbeeld een toepassing die gebeurtenissen over het bewerken van gebruikersaccounts en het verwerken van orders verzendt. De kans is klein dat er gebeurtenis-handlers zijn die beide gebeurteniscategorieën kunnen verwerken. Maak daarom twee aangepaste onderwerpen en laat gebeurtenis-handlers zich abonneren op het onderwerp dat hen interesseert. Voor kleine oplossingen u er de voorkeur aan geven om alle gebeurtenissen naar één onderwerp te sturen. Gebeurtenisabonnees kunnen filteren op de gewenste gebeurtenistypen.

## <a name="event-schema"></a>Gebeurtenisschema
Zie [Azure Event Grid-gebeurtenisschema](event-schema.md)voor een gedetailleerd overzicht van het gebeurtenisschema van De gebeurtenis . Voor aangepaste onderwerpen bepaalt de gebeurtenis-uitgever het **gegevensobject.** De gegevens op het hoogste niveau moeten dezelfde velden hebben als standaardgebeurtenissen met resources.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

In de volgende secties vindt u koppelingen naar zelfstudies om aangepaste onderwerpen te maken met Azure-portal-, CLI-, PowerShell- en Azure Resource Manager-sjablonen (ARM). 


## <a name="azure-portal-tutorials"></a>Azure-portalzelfstudies
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: aangepaste gebeurtenissen maken en routeren met de Azure-portal](custom-event-quickstart-portal.md) | Laat zien hoe u de portal gebruiken om aangepaste gebeurtenissen te verzenden. |
| [Snelstart: aangepaste gebeurtenissen routeren naar Azure Queue-opslag](custom-event-to-queue-storage.md) | Beschrijft hoe u aangepaste gebeurtenissen naar een wachtrijopslag verzendt. |
| [How to: post op aangepast onderwerp](post-to-custom-topic.md) | Laat zien hoe je een evenement plaatst in een aangepast onderwerp. |


## <a name="azure-cli-tutorials"></a>Azure CLI-zelfstudies
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: aangepaste gebeurtenissen maken en routeren met Azure CLI](custom-event-quickstart.md) | Hier wordt uitgelegd hoe u Azure CLI gebruikt om aangepaste gebeurtenissen te verzenden. |
| [Azure CLI: aangepaste gebeurtenisrasteronderwerp maken](./scripts/event-grid-cli-create-custom-topic.md)|Voorbeeldscript dat een aangepast onderwerp maakt. Het script haalt het eindpunt en een sleutel op.|
| [Azure CLI: u abonneren op gebeurtenissen voor een aangepast onderwerp](./scripts/event-grid-cli-subscribe-custom-topic.md)|Voorbeeldscript waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het stuurt gebeurtenissen naar een WebHook.|

## <a name="azure-powershell-tutorials"></a>Azure PowerShell-zelfstudies
|Titel  |Beschrijving  |
|---------|---------|
| [Snelstart: aangepaste gebeurtenissen maken en routeren met Azure PowerShell](custom-event-quickstart-powershell.md) | Hier wordt uitgelegd hoe u Azure PowerShell gebruiken om aangepaste gebeurtenissen te verzenden. |
| [PowerShell: aangepaste gebeurtenisraster-onderwerp maken](./scripts/event-grid-powershell-create-custom-topic.md)|Voorbeeldscript dat een aangepast onderwerp maakt. Het script haalt het eindpunt en een sleutel op.|
| [PowerShell: abonneer je op evenementen voor een aangepast onderwerp](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Voorbeeldscript waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het stuurt gebeurtenissen naar een WebHook.|

## <a name="arm-template-tutorials"></a>ARM-sjabloonzelfstudies
|Titel  |Beschrijving  |
|---------|---------|
| [Resource Manager-sjabloon: aangepast onderwerp en WebHook-eindpunt](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Een resourcemanagersjabloon waarmee een aangepast onderwerp en een aangepast abonnement voor dat aangepaste onderwerp wordt gemaakt. Het stuurt gebeurtenissen naar een WebHook. |
| [Resource Manager-sjabloon: aangepast onderwerp en eindpunt van gebeurtenishubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Een resourcemanagersjabloon waarmee een abonnement wordt gemaakt voor een aangepast onderwerp. Het verzendt gebeurtenissen naar een Azure Event Hubs. |

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Systeemonderwerpen](system-topics.md)
- [Domeinen](event-domains.md)