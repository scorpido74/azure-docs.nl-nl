---
title: Een Azure-meldingshub maken met PowerShell | Microsoft Documenten
description: Meer informatie over het gebruik van een PowerShell-script om een Azure-meldingshub te maken.
author: dimazaid
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: dimazaid
ms.openlocfilehash: 01749e1ae5c29a7e77b2143939e25cf1ccd1aec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263967"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>PowerShell gebruiken om een Azure-meldingshub te maken

Met dit voorbeeld maakt PowerShell-script een voorbeeld van Azure-meldingshub. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Nadat u het voorbeeldscript hebt uitgevoerd, u de volgende opdracht gebruiken om de resourcegroep en alle bijbehorende resources te verwijderen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [Nieuwe AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | Hiermee maakt u een naamruimte voor de meldingshub. |
| [Nieuwe AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | Hiermee maakt u een meldingshub. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.
