---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175993"
---
## <a name="start-your-powershell-session"></a>Een PowerShell-sessie starten
 

Voer de cmdlet van het [**Connect-Az-account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) uit en u krijgt een aanmeldingsscherm om uw referenties in te voeren. Gebruik de referenties waarmee u zich aanmeldt bij Azure Portal.

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt, u de cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) gebruiken om te selecteren welk abonnement uw PowerShell-sessie moet gebruiken. Voer [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext)uit om te zien welk abonnement de huidige PowerShell-sessie gebruikt. Als u al uw abonnementen wilt bekijken, voert u [**Get-AzSubscription uit.**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

