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
ms.openlocfilehash: 6e17b4a3f71e67b99bfbd4c52edc00f98d549ef2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183697"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Power shell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code. 

In dit artikel wordt beschreven hoe u Power shell gebruikt om de volgende beheerde identiteiten uit te voeren voor Azure-bronnen bewerkingen op een Azure-VM.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend met beheerde identiteiten voor Azure-resources bent, lees de [overzichtssectie](overview.md). **Lees de [verschil tussen een beheerde identiteit door het systeem is toegewezen en de gebruiker toegewezen](overview.md#how-does-it-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet gedaan hebt.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie wordt beschreven hoe u de door het systeem toegewezen beheerde identiteit in-en uitschakelt met behulp van Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele Azure-machine

Als u een Azure-VM wilt maken met de door het systeem toegewezen beheerde identiteit, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Raadpleeg een van de volgende Quick starts voor Azure VM, waarbij u alleen de benodigde secties (' aanmelden bij Azure ', ' resource groep maken ', ' netwerk groep maken ', ' de virtuele machine maken ', hebt voltooid.
    
    Wanneer u de sectie ' de virtuele machine maken ' krijgt, brengt u een kleine wijziging aan in de syntaxis van de cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm) . Zorg ervoor dat u een `-AssignIdentity:$SystemAssigned` para meter toevoegt voor het inrichten van de virtuele machine met de door het systeem toegewezen identiteit is ingeschakeld, bijvoorbeeld:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Een virtuele Windows-machine maken met behulp van Power shell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Een virtuele Linux-machine maken met behulp van Power shell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele machine van Azure

Als u door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele machine die oorspronkelijk is ingericht zonder deze, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de VM-eigenschappen op met behulp van de cmdlet `Get-AzVM`. Als u vervolgens een door het systeem toegewezen beheerde identiteit wilt inschakelen, gebruikt u de `-AssignIdentity` switch op de cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) :

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Aan een groep toegewezen VM-systeem-id toevoegen

Nadat u een door het systeem toegewezen identiteit op een virtuele machine hebt ingeschakeld, kunt u deze toevoegen aan een groep.  Met de volgende procedure voegt u de systeem-id van een virtuele machine toe aan een groep.

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal de `ObjectID` (zoals opgegeven in het veld `Id` van de geretourneerde waarden) van de service-principal van de VM op en noteer deze.

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. De `ObjectID` (zoals opgegeven in het veld `Id` van de geretourneerde waarden) van de groep ophalen en noteren:

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

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Haal de eigenschappen van de virtuele machine op met behulp van de cmdlet `Get-AzVM` en stel de para meter `-IdentityType` in op `UserAssigned`:

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
  
    Wanneer u de sectie ' de virtuele machine maken ' krijgt, brengt u een kleine wijziging aan in de syntaxis van de [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) -cmdlet. Voeg de para meters `-IdentityType UserAssigned` en `-IdentityID` toe om de virtuele machine in te richten met een door de gebruiker toegewezen identiteit.  Vervang `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden.  Bijvoorbeeld:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Een virtuele Windows-machine maken met behulp van Power shell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Een virtuele Linux-machine maken met behulp van Power shell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele machine van Azure

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

   ```powershell
   Connect-AzAccount
   ```

2. Maak een door de gebruiker toegewezen beheerde identiteit met behulp van de cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) .  Let op de `Id` in de uitvoer, omdat u deze in de volgende stap nodig hebt.

   > [!IMPORTANT]
   > Het maken van door de gebruiker toegewezen beheerde identiteiten ondersteunt alleen alfanumerieke, onderstrepings tekens en afbreek streepjes (0-9 of a-z of A-Z, \_ of-). Bovendien moet de naam van 3 tot 128 tekens worden beperkt zodat de toewijzing aan VM-VMSS goed werkt. Zie [Veelgestelde vragen en bekende problemen](known-issues.md) voor meer informatie

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Haal de eigenschappen van de virtuele machine op met behulp van de cmdlet `Get-AzVM`. Als u vervolgens een door de gebruiker toegewezen beheerde identiteit aan de Azure-VM wilt toewijzen, gebruikt u de schakel optie `-IdentityType` en `-IdentityID` van de cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) .  De waarde voor de para meter`-IdentityId` is de `Id` die u in de vorige stap hebt genoteerd.  Vervang `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`en `<USER ASSIGNED IDENTITY NAME>` door uw eigen waarden.

   > [!WARNING]
   > Als u eerder door de gebruiker toegewezen beheerde identiteiten wilt behouden die zijn toegewezen aan de virtuele machine, moet u een query uitvoeren op de eigenschap `Identity` van het VM-object (bijvoorbeeld `$vm.Identity`).  Als een door de gebruiker toegewezen beheerde identiteit wordt geretourneerd, neemt u deze op in de volgende opdracht, samen met de nieuwe door de gebruiker toegewezen beheerde identiteit die u aan de virtuele machine wilt toewijzen.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-VM

Als u een door de gebruiker toegewezen identiteit wilt verwijderen naar een virtuele machine, moet uw account de roltoewijzing van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.

Als uw VM meerdere door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle, behalve de laatste, verwijderen met de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VM NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de naam eigenschap van de door de gebruiker toegewezen beheerde identiteit, die op de virtuele machine moet blijven. Deze informatie kan worden gevonden door de eigenschap `Identity` van het VM-object op te vragen.  Bijvoorbeeld `$vm.Identity`:

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

- [Beheerde identiteiten voor een overzicht van Azure-resources](overview.md)
- Zie voor de volledige Snelstartgids voor het maken van virtuele Azure-machines:
  
  - [Een virtuele Windows-machine maken met Power shell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Een virtuele Linux-machine maken met Power shell](../../virtual-machines/linux/quick-create-powershell.md) 
