---
title: Voorbeeld van Azure CLI-script - Aangepast onderwerp maken | Microsoft Docs
description: Dit artikel bevat een voor beeld van een Azure CLI-script dat laat zien hoe u een Azure Event Grid aangepast onderwerp maakt.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c6e027d234104d5e45c632dd73e79d8f069deb5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720840"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Aangepast Event Grid-onderwerp maken met Azure CLI

Met dit script wordt een aangepast Event Grid-onderwerp gemaakt.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het aangepaste onderwerp te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Hiermee wordt een aangepast Event Grid-onderwerp gemaakt. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Query Event Grid subscriptions](../query-event-subscriptions.md) (Query's uitvoeren op Event Grid-abonnementen) voor informatie over het uitvoeren van query's op abonnementen.
* Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.
