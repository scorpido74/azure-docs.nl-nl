---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67175993"
---
## <a name="start-your-powershell-session"></a>Een PowerShell-sessie starten
 

Voer de cmdlet [**Connect-AZ account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) uit en er wordt een aanmeldings scherm weer gegeven waarin u uw referenties kunt invoeren. Gebruik de referenties waarmee u zich aanmeldt bij Azure Portal.

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt, gebruikt u de cmdlet [**set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) om te selecteren welk abonnement uw Power shell-sessie moet gebruiken. Als u wilt zien welk abonnement door de huidige Power shell-sessie wordt gebruikt, voert u [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext)uit. Voer [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription)uit om al uw abonnementen weer te geven.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

