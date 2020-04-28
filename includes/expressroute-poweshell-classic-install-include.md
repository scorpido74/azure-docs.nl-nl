---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74926223"
---
Installeer de nieuwste versies van de Azure Service Management (SM) Power shell-modules en de ExpressRoute-module. U kunt de Azure Cloud shell-omgeving niet gebruiken om SM-modules uit te voeren.

1. Volg de instructies in het artikel [service management-module installeren](/powershell/azure/servicemanagement/install-azure-ps) om de Azure service management-module te installeren. Als u de module AZ of RM al hebt geïnstalleerd, zorg er dan voor dat u '-AllowClobber ' gebruikt.
2. Importeer de geïnstalleerde modules. Wanneer u het volgende voor beeld gebruikt, past u het pad aan om de locatie en versie van de geïnstalleerde Power shell-modules weer te geven.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Open uw Power shell-console met verhoogde rechten en maak verbinding met uw account om u aan te melden bij uw Azure-account. Gebruik het volgende voor beeld om verbinding te maken met behulp van de module Service Management:

   ```powershell
   Add-AzureAccount
   ```