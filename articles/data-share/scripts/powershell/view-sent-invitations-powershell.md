---
title: 'PowerShell-script: Azure Data Share-uitnodigingen aanbieden die naar een consument zijn verzonden | Microsoft Documenten'
description: Dit PowerShell-script accepteert uitnodigingen van een bestaand gegevensaandeel.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 157b844afd9d59b6c03c7ae8585bc2182370d3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307155"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>PowerShell gebruiken om een uitnodiging voor gegevensshare te ontvangen

Dit PowerShell-script krijgt uitnodigingen naar een consument.

## <a name="sample-script"></a>Voorbeeldscript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDataShareUitnodiging](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Uitnodigingen voor het delen van gegevens in kaart en vermelden deze. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Share PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden voor Azure Data Share.](../../samples-powershell.md)
