---
title: Voorbeeld van Azure PowerShell-script - abonneren op resourcegroep | Microsoft Docs
description: In dit artikel vindt u een voor beeld van een Azure PowerShell script dat laat zien hoe u zich abonneert op Event Grid gebeurtenissen voor een resource groep.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e285f628d1ec3afce55dc8fe4efa50a361dab5ed
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720798"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>Abonneren op gebeurtenissen voor een resourcegroep met PowerShell

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een resourcegroep.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Voor het voorbeeldscript van de preview is de Event Grid-module vereist. Voer `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` uit om deze te installeren

## <a name="sample-script---stable"></a>Voorbeeldscript - stabiel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="sample-script---preview-module"></a>Voorbeeldscript - preview-module

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.
