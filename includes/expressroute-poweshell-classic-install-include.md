---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926223"
---
Installeer de nieuwste versies van de Azure Service Management (SM) PowerShell-modules en de ExpressRoute-module. U de Azure CloudShell-omgeving niet gebruiken om SM-modules uit te voeren.

1. Gebruik de instructies in het artikel [Van de module Servicebeheer](/powershell/azure/servicemanagement/install-azure-ps) om de Azure Service Management Module te installeren. Als u de Az- of RM-module al hebt geïnstalleerd, moet u '-AllowClobber' gebruiken.
2. Importeer de geïnstalleerde modules. Pas bij het gebruik van het volgende voorbeeld het pad aan de locatie en versie van uw geïnstalleerde PowerShell-modules weer.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Als u zich wilt aanmelden bij uw Azure-account, opent u uw PowerShell-console met verhoogde rechten en maakt u verbinding met uw account. Gebruik het volgende voorbeeld om verbinding te maken via de module Servicebeheer:

   ```powershell
   Add-AzureAccount
   ```