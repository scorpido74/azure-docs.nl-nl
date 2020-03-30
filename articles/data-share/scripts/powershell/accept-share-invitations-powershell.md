---
title: 'PowerShell-script: uitnodiging accepteren van een Azure-gegevensaandeel | Microsoft Documenten'
description: Dit PowerShell-script accepteert uitnodigingen van een bestaand gegevensaandeel.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307331"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>PowerShell gebruiken om een uitnodiging voor gegevensshare te accepteren

Dit PowerShell-script accepteert uitnodigingen die naar een consument worden verzonden.

## <a name="sample-script"></a>Voorbeeldscript
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDataShareUitnodiging](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Uitnodigingen voor het delen van gegevens in kaart en vermelden deze. |
| [Nieuw-AzDataShareAbonnement](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | Maak een abonnement voor gegevensshare. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Data Share PowerShell-scriptvoorbeelden zijn te vinden in de [PowerShell-voorbeelden voor Azure Data Share.](../../samples-powershell.md)

