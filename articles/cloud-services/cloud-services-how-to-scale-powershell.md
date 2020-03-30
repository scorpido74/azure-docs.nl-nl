---
title: Een Azure-cloudservice schalen in Windows PowerShell | Microsoft Documenten
description: (klassiek) Meer informatie over het gebruik van PowerShell om een webrol of werknemersrol in of uit te schalen in Azure.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359039"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Een cloudservice schalen in PowerShell

U Windows PowerShell gebruiken om een webrol of werknemersrol in of uit te schalen door instanties toe te voegen of te verwijderen.  

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Voordat u bewerkingen op uw abonnement uitvoeren via PowerShell, moet u zich aanmelden:

```powershell
Add-AzureAccount
```

Als u meerdere abonnementen aan uw account hebt gekoppeld, moet u mogelijk het huidige abonnement wijzigen, afhankelijk van waar uw cloudservice zich bevindt. Voer het huidige abonnement uit om het huidige abonnement te controleren:

```powershell
Get-AzureSubscription -Current
```

Als u het huidige abonnement moet wijzigen, voert u het als:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Het huidige aantal instantievoor uw rol controleren

Voer het als nodig om de huidige status van uw rol te controleren:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

U moet informatie over de rol terugkrijgen, inclusief de huidige os-versie en het aantal instance's. In dit geval heeft de rol één exemplaar.

![Informatie over de rol](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>De rol uitschalen door meer exemplaren toe te voegen

Als u uw rol wilt uitschalen, geeft u het gewenste aantal exemplaren door als parameter **Aantal** aan de cmdlet **Set-AzureRole:**

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

De cmdlet blokkeert tijdelijk terwijl de nieuwe exemplaren zijn ingericht en gestart. Als u gedurende deze periode een nieuw PowerShell-venster opent en **Get-AzureRole** belt zoals eerder is weergegeven, ziet u het aantal nieuwe doelinstanties. En als u de rolstatus in de portal inspecteert, moet u het nieuwe exemplaar zien opstarten:

![VM-exemplaar vanaf portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Zodra de nieuwe exemplaren zijn gestart, wordt de cmdlet met succes retournerd:

![Rolinstantie verhoogt succes](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Schalen in de rol door instanties te verwijderen

U in een rol schalen door instanties op dezelfde manier te verwijderen. Stel de parameter **Aantal** in **op Set-AzureRole** op het aantal exemplaren dat u wilt hebben nadat de schaal in bedrijf is voltooid.

## <a name="next-steps"></a>Volgende stappen

Het is niet mogelijk om automatische schaal te configureren voor cloudservices van PowerShell. Zie Een [cloudservice automatisch schalen om dit te](cloud-services-how-to-scale-portal.md)doen.
