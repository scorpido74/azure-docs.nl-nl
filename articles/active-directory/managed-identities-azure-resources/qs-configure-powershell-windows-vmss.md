---
title: Beheerde identiteiten configureren op virtuele machineschaalsets met PowerShell - Azure AD
description: Stapsgewijze instructies voor het configureren van een systeem en door de gebruiker toegewezen beheerde identiteiten op een virtuele machineschaalset met PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547270"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Beheerde identiteiten configureren voor Azure-resources op virtuele machineschaalsets met PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u met PowerShell hoe u de beheerde identiteiten voor Azure-resources-bewerkingen uitvoert op een set virtuele machineschaal:
- De door het systeem toegewezen beheerde identiteit in- en uitschakelen op een virtuele machineschaalset
- Een door de gebruiker toegewezen beheerde identiteit toevoegen en verwijderen op een virtuele machineschaalset

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker beheerde toegewezen identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheerbewerkingen in dit artikel wilt uitvoeren, heeft uw account de volgende op Azure-rollen gebaseerde toegangsbeheertoewijzingen nodig:

    > [!NOTE]
    > Er zijn geen extra Azure AD-maproltoewijzingen vereist.

    - [Virtuele machinebijdrager](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) om een virtuele machineschaalset te maken en beheerde en/of door de gebruiker toegewezen beheerde identiteit in te schakelen en te verwijderen uit een virtuele machineschaalset.
    - [Functie Beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - [Functie Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) om een door de gebruiker toegewezen beheerde identiteit toe te wijzen en te verwijderen van en naar een virtuele machineschaalset.
- Installeer [de nieuwste versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan. 

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u een door het systeem toegewezen beheerde identiteit in- en verwijdert met Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een Azure-schaalset voor virtuele machines

Ga als lid van het nieuwe bedrijf naar een virtuele machineschaalset met de door het systeem toegewezen beheerde identiteit:

1. Raadpleeg *voorbeeld 1* in het artikel [Nieuw-AzVmssConfig-cmdlet](/powershell/module/az.compute/new-azvmssconfig) om een virtuele machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.  Voeg de `-IdentityType SystemAssigned` parameter `New-AzVmssConfig` toe aan de cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Beheerde identiteit met systeem toegewezen op een bestaande Azure-schaalset voor virtuele machines inschakelen

Als u een door het systeem toegewezen beheerde identiteit moet inschakelen op een bestaande Azure-schaalset voor virtuele machines:

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machineschaalset bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijfmachtigingen geeft op de virtuele machineschaalset, zoals 'Virtuele machineinzender':

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de eigenschappen van [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) de virtuele machineschaal op met de cmdlet. Gebruik vervolgens de schakelaar op de `-IdentityType` cmdlet [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>De door het systeem toegewezen beheerde identiteit uitschakelen vanuit een Azure-schaalset voor virtuele machines

Als u een virtuele machineschaalset hebt die niet langer de door het systeem toegewezen beheerde identiteit nodig heeft, maar nog steeds door de gebruiker toegewezen beheerde identiteiten nodig heeft, gebruikt u de volgende cmdlet:

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijfmachtigingen geeft op de virtuele machineschaalset, zoals 'Virtuele machineinzender':

2. Voer de volgende cmdlet uit:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Als u een virtuele machineschaalset hebt die geen door het systeem toegewezen beheerde identiteit nodig heeft en geen door de gebruiker toegewezen beheerde identiteiten heeft, gebruikt u de volgende opdrachten:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit toevoegt en verwijdert uit een schaalset voor virtuele machines met Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een Azure-schaalset voor virtuele machines

Het maken van een nieuwe virtuele machineschaalset met een door de gebruiker toegewezen beheerde identiteit wordt momenteel niet ondersteund via PowerShell. Zie de volgende sectie over het toevoegen van een door de gebruiker toegewezen beheerde identiteit aan een bestaande virtuele machineschaalset. Controleer later op updates.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande Azure-schaalset voor virtuele machines

Ga als u een door de gebruiker toegewezen beheerde identiteit toewijst aan een bestaande Azure-schaalset voor virtuele machines:

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machineschaalset bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijfmachtigingen geeft op de virtuele machineschaalset, zoals 'Virtuele machineinzender':

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de eigenschappen van `Get-AzVM` de virtuele machineschaal op met de cmdlet. Als u vervolgens een door de gebruiker toegewezen beheerde `-IdentityType` `-IdentityID` identiteit wilt toewijzen aan de virtuele machineschaalset, gebruikt u de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) en schakelt u deze in. Vervang `<VM NAME>` `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` , met uw eigen waarden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-schaalset voor virtuele machines

Als uw virtuele machineschaalset meerdere door de gebruiker toegewezen beheerde identiteiten heeft, u alle, behalve de laatste verwijderen met behulp van de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. Het `<USER ASSIGNED IDENTITY NAME>` is de door de gebruiker toegewezen beheerde identiteit eigenschap, die moet blijven op de virtuele machine schaal set. Deze informatie is te vinden in het identiteitsgedeelte `az vmss show`van de virtuele machineschaalset met behulp van:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Als uw virtuele machineschaalset geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Als uw virtuele machineschaalset zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door over te schakelen naar alleen door het systeem toegewezen beheerde identiteit.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-bronnen](overview.md)
- Zie voor de volledige Quickstarts voor het maken van Azure VM:
  
  - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















