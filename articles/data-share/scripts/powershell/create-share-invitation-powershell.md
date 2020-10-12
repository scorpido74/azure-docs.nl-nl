---
title: 'Power shell-script: een uitnodiging voor een Azure-gegevens share maken | Microsoft Docs'
description: Met dit Power shell-script wordt een uitnodiging voor gegevens delen verzonden.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 220677df3bd0c221f61efd28a80b05cfd76e2734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086639"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>Een Power shell-script gebruiken om het gebruik van een verzonden gegevens share te bewaken

Met dit Power shell-script maakt u een uitnodiging voor een gegevens share.

## <a name="sample-script"></a>Voorbeeldscript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Maak een uitnodiging voor een gegevens share. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
