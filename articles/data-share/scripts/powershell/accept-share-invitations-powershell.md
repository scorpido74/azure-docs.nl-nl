---
title: 'Power shell-script: Uitnodiging accepteren van een Azure-gegevens share | Microsoft Docs'
description: Dit Power shell-script accepteert uitnodigingen van een bestaande gegevens share.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9ef35e0865a7c5bbfb4accc058a8d63bb6f1e80b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243072"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Power shell gebruiken voor het accepteren van een uitnodiging voor een gegevens share

Dit Power shell-script accepteert de uitnodigingen die worden verzonden naar een consumer.

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
| [Get-AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | Verzonden uitnodigingen voor gegevens delen ophalen en weer geven. |
| [New-AzDataShareSubscription](/powershell/module/az.resources/get-azdatashareinvitation) | Een gegevens share-abonnement maken. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.

