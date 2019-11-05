---
title: Voorbeeld van Azure CLI-script - abonneren op een Azure-abonnement | Microsoft Docs
description: Voorbeeld van Azure CLI-script - abonneren op een Azure-abonnement
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: 9d1aa53ede323c2bb536c74eeaaba9fd28b01712
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549304"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Abonneren op gebeurtenissen voor een Azure-abonnement met Azure CLI

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een Azure-abonnement.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Voor het voorbeeldscript van de preview is de Event Grid-extensie vereist. Voer `az extension add --name eventgrid` uit om deze te installeren.

## <a name="sample-script---stable"></a>Voorbeeldscript - stabiel

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Voorbeeldscript - preview-extensie

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Hiermee wordt een Event Grid-abonnement gemaakt. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - extensieversie | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Query Event Grid subscriptions](../query-event-subscriptions.md) (Query's uitvoeren op Event Grid-abonnementen) voor informatie over het uitvoeren van query's op abonnementen.
* Raadpleeg de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.
