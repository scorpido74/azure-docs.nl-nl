---
title: Azure PowerShell-abonneren op resource groep
description: Azure Event Grid & Azure PowerShell-voorbeeld script-abonneren op resource groep en filteren op resource
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: 222bf6fb9297afda038b38d3fb528711ad03a3ca
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790736"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Abonneren op gebeurtenissen voor een resourcegroep en filteren op een resource met PowerShell

Met dit script maakt u een Event Grid-abonnement op de gebeurtenissen voor een resourcegroep. Er wordt een filter gebruikt om alleen gebeurtenissen op te halen voor een bepaalde resource in de resourcegroep.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Voorbeeldscript - stabiel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Voorbeeldscript - preview-module

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Voor het voorbeeldscript van de preview is de Event Grid-module vereist. Voer `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` uit om deze te installeren

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om het abonnement op de gebeurtenis te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Hiermee wordt een Event Grid-abonnement gemaakt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.
