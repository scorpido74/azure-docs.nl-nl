---
title: Beheerde identiteiten configureren op een Azure VM met PowerShell - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure VM met PowerShell.
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
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547216"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure VM met PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben. 

In dit artikel leert u met PowerShell hoe u de volgende beheerde identiteiten uitvoert voor Azure-resourcesbewerkingen op een Azure VM.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de nieuwste versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit in- en uitschakelt met Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een Azure VM

Als u een Azure VM wilt maken met de door het systeem toegewezen beheerde identiteit ingeschakeld, heeft uw account de roltoewijzing [voor virtuele machineinzenderbijdrage](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Raadpleeg een van de volgende Azure VM Quickstarts, waarbij alleen de benodigde secties worden voltooid ('Aanmelden bij Azure', 'Resourcegroep maken', 'Netwerkgroep maken', 'De VM maken'.
    
    Wanneer u bij de sectie 'De VM maken' aankomt, brengt u een kleine wijziging aan in de syntaxis van de cmdlet [Nieuw-AzVMConfig.](/powershell/module/az.compute/new-azvm) Zorg ervoor dat `-AssignIdentity:$SystemAssigned` u een parameter toevoegt aan de inlevering van de VM met de systeemtoegewezen identiteit ingeschakeld, bijvoorbeeld:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Een Virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Beheerde identiteit met systeemtoegewezen op een bestaande Azure-vm inschakelen

Als u beheerde identiteit met systeemtoegewezen wilt inschakelen op een vm die oorspronkelijk zonder deze is ingericht, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de VM-eigenschappen op met de `Get-AzVM` cmdlet. Gebruik vervolgens de `-AssignIdentity` schakelaar op de cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) om een door het systeem toegewezen beheerde identiteit in te schakelen:

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Vm-systeem toegewezen identiteit toevoegen aan een groep

Nadat u de identiteit van het systeem op een virtuele machine hebt toegewezen, u deze toevoegen aan een groep.  In de volgende procedure wordt een vm-systeem toegevoegd dat aan een groep is toegewezen.

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

   ```powershell
   Connect-AzAccount
   ```

2. De serviceprincipal `ObjectID` van de `Id` VM ophalen en noteren (zoals opgegeven in het veld van de geretourneerde waarden):

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. De `ObjectID` groep ophalen en noteren `Id` (zoals opgegeven in het veld van de geretourneerde waarden):

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Voeg de serviceprincipal van de VM toe aan de groep:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Beheerde identiteit met systeemtoegewezen identiteit uitschakelen vanuit een Azure-vm

Als u de door het systeem toegewezen beheerde identiteit op een virtuele apparaat wilt uitschakelen, heeft uw account de roltoewijzing [voor virtuele machineinzender nodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

Als u een virtuele machine hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen beheerde identiteiten nodig heeft, gebruikt u de volgende cmdlet:

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal de VM-eigenschappen op met `Get-AzVM` `-IdentityType` de `UserAssigned`cmdlet en stel de parameter in op:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Als u een virtuele machine hebt die geen door het systeem toegewezen beheerde identiteit nodig heeft en geen door de gebruiker toegewezen beheerde identiteiten heeft, gebruikt u de volgende opdrachten:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit toevoegt en verwijdert van een VM met Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een vm tijdens het maken

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Raadpleeg een van de volgende Azure VM Quickstarts, waarbij alleen de benodigde secties worden voltooid ('Aanmelden bij Azure', 'Resourcegroep maken', 'Netwerkgroep maken', 'De VM maken'. 
  
    Wanneer u bij de sectie 'De vm maken' [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) aankomt, brengt u een kleine wijziging aan in de syntaxis van de cmdlet. Voeg `-IdentityType UserAssigned` de `-IdentityID` parameters en parameters toe aan het inrichten van de VM met een door de gebruiker toegewezen identiteit.  Vervang `<VM NAME>``<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` en met uw eigen waarden.  Bijvoorbeeld:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Een Virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Meld u aan `Connect-AzAccount`bij Azure met behulp van . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met de cmdlet [Nieuw-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Let `Id` op de in de uitvoer, want je moet dit in de volgende stap.

   > [!IMPORTANT]
   > Het maken van door de gebruiker toegewezen beheerde identiteiten ondersteunt alleen alfanumerieke, onderunderscore en koppelteken (0-9 of a-z of A-Z, \_ of -) tekens. Bovendien moet de naam worden beperkt van 3 tot 128 tekenlengte voor de toewijzing aan VM/VMSS om goed te werken. Zie [veelgestelde vragen en bekende problemen voor](known-issues.md) meer informatie

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Haal de VM-eigenschappen op met de `Get-AzVM` cmdlet. Als u vervolgens een door de gebruiker toegewezen `-IdentityType` beheerde identiteit wilt toewijzen aan de Azure VM, gebruikt u de cmdlet `-IdentityID` [Update-AzVM](/powershell/module/az.compute/update-azvm) en schakelt u deze in.  De waarde`-IdentityId` voor de `Id` parameter is de waarde die u in de vorige stap hebt opgemerkt.  Vervang `<VM NAME>` `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` en met uw eigen waarden.

   > [!WARNING]
   > Als u eerder aan de gebruiker toegewezen beheerde `Identity` identiteiten wilt behouden die `$vm.Identity`aan de VM zijn toegewezen, zoekt u de eigenschap van het VM-object op (bijvoorbeeld).  Als een gebruiker toegewezen beheerde identiteiten wordt geretourneerd, neemt u deze op in de volgende opdracht, samen met de nieuwe gebruiker toegewezen beheerde identiteit die u aan de VM wilt toewijzen.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig.

Als uw vm meerdere door de gebruiker toegewezen beheerde identiteiten heeft, u alle, behalve de laatste verwijderen met behulp van de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. Het `<USER ASSIGNED IDENTITY NAME>` is de door de gebruiker toegewezen beheerde identiteit eigenschap, die moet blijven op de VM. Deze informatie kan worden gevonden `Identity` door de eigenschap van het VM-object op te vragen.  Bijvoorbeeld: `$vm.Identity`

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Als uw VM geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Als uw VM zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door over te schakelen naar alleen door het systeem toegewezen beheerde identiteiten.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-bronnen](overview.md)
- Zie voor de volledige Quickstarts voor het maken van Azure VM:
  
  - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
