---
title: Beheerde identiteiten configureren op een virtuele Azure-machine met Power shell-Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure-VM met behulp van Power shell.
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
ms.openlocfilehash: 63ff6335cb17e1ec6c620d16f2d62597637b3e32
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693631"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Power shell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel wordt beschreven hoe u Power shell gebruikt om de volgende beheerde identiteiten uit te voeren voor Azure-bronnen bewerkingen op een Azure-VM.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie wordt beschreven hoe u de door het systeem toegewezen beheerde identiteit in-en uitschakelt met behulp van Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele Azure-machine

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Raadpleeg een van de volgende Quick starts voor Azure VM, waarbij u alleen de benodigde secties (' aanmelden bij Azure ', ' resource groep maken ', ' netwerk groep maken ', ' de virtuele machine maken ', hebt voltooid.
    
    Wanneer u de sectie ' de virtuele machine maken ' krijgt, brengt u een kleine wijziging aan in de syntaxis van de cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Zorg ervoor dat u een `-AssignIdentity:$SystemAssigned` para meter toevoegt voor het inrichten van de virtuele machine met de door het systeem toegewezen identiteit is ingeschakeld, bijvoorbeeld:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Een virtuele Linux-machine maken met behulp van Power shell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele machine van Azure

Als u door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele machine die oorspronkelijk is ingericht zonder deze, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de VM-eigenschappen op met behulp van de `Get-AzVM` cmdlet. Gebruik vervolgens de `-AssignIdentity` Schakel optie voor de cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) om een door het systeem toegewezen beheerde identiteit in te scha kelen:

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Aan een groep toegewezen VM-systeem-id toevoegen

Nadat u een door het systeem toegewezen identiteit op een virtuele machine hebt ingeschakeld, kunt u deze toevoegen aan een groep.  Met de volgende procedure voegt u de systeem-id van een virtuele machine toe aan een groep.

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal de `ObjectID` (zoals opgegeven in het `Id` veld van de geretourneerde waarden) van de service-principal van de VM op en noteer deze:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Haal de `ObjectID` (zoals opgegeven in het `Id` veld van de geretourneerde waarden) van de groep op en noteer deze:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. De service-principal van de virtuele machine toevoegen aan de groep:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit uitschakelen vanuit een Azure-VM

Als u een door het systeem toegewezen beheerde identiteit op een VM wilt uitschakelen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

Als u een virtuele machine hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen beheerde identiteiten nodig hebt, gebruikt u de volgende cmdlet:

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal de VM-eigenschappen op met behulp van de `Get-AzVM` cmdlet en stel de `-IdentityType` para meter in op `UserAssigned` :

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Als u een virtuele machine hebt die door het systeem toegewezen beheerde identiteit niet meer nodig heeft en geen door de gebruiker toegewezen beheerde identiteiten heeft, gebruikt u de volgende opdrachten:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een virtuele machine met behulp van Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een virtuele machine tijdens het maken

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Raadpleeg een van de volgende Quick starts voor Azure VM, waarbij u alleen de benodigde secties (' aanmelden bij Azure ', ' resource groep maken ', ' netwerk groep maken ', ' de virtuele machine maken ', hebt voltooid. 
  
    Wanneer u de sectie ' de virtuele machine maken ' krijgt, moet u een kleine wijziging aanbrengen in de syntaxis van de [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet. Voeg de `-IdentityType UserAssigned` `-IdentityID` para meters en toe om de virtuele machine in te richten met een door de gebruiker toegewezen identiteit.  Vervang `<VM NAME>` , `<SUBSCRIPTION ID>` , `<RESROURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden.  Bijvoorbeeld:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Een virtuele Linux-machine maken met behulp van Power shell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machine van Azure

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) .  Let op de `Id` in de uitvoer, omdat u dit in de volgende stap nodig hebt.

   > [!IMPORTANT]
   > Het maken van door de gebruiker toegewezen beheerde identiteiten ondersteunt alleen alfanumerieke, onderstrepings tekens en afbreek streepjes (0-9 of a-z of A-Z of \_ -). Bovendien moet de naam van 3 tot 128 tekens worden beperkt zodat de toewijzing aan VM-VMSS goed werkt. Zie [Veelgestelde vragen en bekende problemen](known-issues.md) voor meer informatie

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Haal de eigenschappen van de virtuele machine op met de `Get-AzVM` cmdlet. U kunt vervolgens een door de gebruiker toegewezen beheerde identiteit toewijzen aan de Azure VM met behulp van de- `-IdentityType` en `-IdentityID` -Switch in de cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) .  De waarde voor de `-IdentityId` para meter is die `Id` u in de vorige stap hebt genoteerd.  Vervang `<VM NAME>` , `<SUBSCRIPTION ID>` , `<RESROURCE GROUP>` en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden.

   > [!WARNING]
   > Als u eerder door de gebruiker toegewezen beheerde identiteiten wilt behouden die zijn toegewezen aan de virtuele machine, moet u een query uitvoeren op de `Identity` eigenschap van het VM-object (bijvoorbeeld `$vm.Identity` ).  Als een door de gebruiker toegewezen beheerde identiteit wordt geretourneerd, neemt u deze op in de volgende opdracht, samen met de nieuwe door de gebruiker toegewezen beheerde identiteit die u aan de virtuele machine wilt toewijzen.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-VM

Als u een door de gebruiker toegewezen identiteit wilt verwijderen naar een virtuele machine, moet uw account de roltoewijzing van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.

Als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle, behalve de laatste, verwijderen met de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de naam eigenschap van de door de gebruiker toegewezen beheerde identiteit, die op de virtuele machine moet blijven. Deze informatie kan worden gevonden door het opvragen `Identity` van de eigenschap van het VM-object.  Bijvoorbeeld `$vm.Identity` :

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Als uw virtuele machine geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Als uw virtuele machine zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te scha kelen naar alleen door het systeem toegewezen beheerde identiteiten te gebruiken.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie voor de volledige Snelstartgids voor het maken van virtuele Azure-machines:
  
  - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
