---
title: Beheerde identiteiten configureren op een virtuele-machineschaalset met PowerShell - Azure AD
description: Stapsgewijze instructies voor het configureren van door het systeem en de gebruiker toegewezen beheerde identiteiten op een virtuele-machineschaalset met behulp van PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70296dce5b9dcac738c17a4f2388a7eb37abd66f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269332"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Beheerde identiteiten configureren voor Azure-resources op virtuele-machineschaalsets met PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u om met behulp van PowerShell de beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een virtuele-machineschaalset:
- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machineschaalset
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen van een virtuele-machineschaalset

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker beheerde toegewezen identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Voor het uitvoeren van de beheerbewerkingen in dit artikel zijn voor uw account de volgende toewijzingen van op rollen gebaseerd toegangsbeheer van Azure nodig:

    > [!NOTE]
    > Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-map.

    - [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) voor het maken van een virtuele-machineschaalset en het inschakelen en verwijderen van een door het systeem toegewezen beheerde en/of door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset.
    - De rol van [inzender voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) voor het maken van een door de gebruiker toegewezen beheerde identiteit.
    - De rol van [operator voor beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator) voor het toewijzen/verwijderen van een door de gebruiker toegewezen beheerde identiteit aan/uit een virtuele-machine schaalset.
- Installeer de [meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan. 

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u een door een systeem toegewezen beheerde identiteit kunt inschakelen en verwijderen met behulp van Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset van Azure

Doe het volgende om een virtuele-machineschaalset te maken met de door het systeem toegewezen beheerde identiteit ingeschakeld:

1. Raadpleeg *Voorbeeld 1* in het cmdlet-referentieartikel [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) om een virtuele-machineschaalset te maken met een door het systeem toegewezen beheerde identiteit.  Voeg de parameter `-IdentityType SystemAssigned` toe aan de cmdlet `New-AzVmssConfig`:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele-machineschaalset van Azure

Als u een door het systeem toegewezen beheerde identiteit moet inschakelen op een bestaande virtuele-machineschaalset van Azure:

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol waarmee u schrijfmachtigingen hebt voor de virtuele-machineschaalset, zoals 'Inzender voor virtuele machines':

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de eigenschappen van de virtuele-machineschaalset op met behulp van de cmdlet [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss). Gebruik vervolgens de schakeloptie `-IdentityType` in de cmdlet [Update-AzVMss](/powershell/module/az.compute/update-azvmss) om een door het systeem toegewezen beheerde identiteit in te schakelen:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een virtuele-machineschaalset van Azure

Gebruik de volgende cmdlet als u een virtuele-machineschaalset hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar nog wel de door de gebruiker toegewezen beheerde identiteiten nodig heeft:

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account die is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol waarmee u schrijfmachtigingen hebt voor de virtuele-machineschaalset, zoals 'Inzender voor virtuele machines':

2. Voer de volgende cmdlet uit:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Gebruik de volgende opdrachten als u een virtuele-machineschaalset hebt die geen door het systeem toegewezen beheerde identiteit meer nodig heeft en die geen door de gebruiker toegewezen beheerde identiteiten heeft:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen van een virtuele-machineschaalset met behulp van Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een virtuele-machineschaalset van Azure

Het maken van een nieuwe virtuele-machineschaalset met een door de gebruiker toegewezen beheerde identiteit wordt momenteel niet ondersteund via PowerShell. Raadpleeg de volgende sectie over hoe u een door de gebruiker toegewezen beheerde identiteit toevoegt aan een bestaande virtuele-machineschaalset. Controleer later op updates.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele-machineschaalset van Azure

Doe het volgende om een door de gebruiker toegewezen beheerde identiteit toe te wijzen aan een bestaande virtuele-machineschaalset van Azure:

1. Meld u aan bij Azure met `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol waarmee u schrijfmachtigingen hebt voor de virtuele-machineschaalset, zoals 'Inzender voor virtuele machines':

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de eigenschappen van de virtuele-machineschaalset op met behulp van de cmdlet `Get-AzVM`. Wijs vervolgens een door de gebruiker toegewezen beheerde identiteit toe aan de virtuele-machineschaalset, gebruik de schakeloptie `-IdentityType` en `-IdentityID` in de cmdlet [Update-AzVMss](/powershell/module/az.compute/update-azvmss). Vervang `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` en `USER ASSIGNED ID2` door uw eigen waarden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset van Azure verwijderen

Als uw virtuele-machineschaalset meerdere door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle, behalve de laatste, verwijderen met de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de naameigenschap van de door de gebruiker toegewezen beheerde identiteit; deze moet op de virtuele-machineschaalset blijven. Deze informatie vindt u in de sectie Identiteit van de virtuele-machineschaalset met behulp van `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Gebruik de volgende opdracht als uw virtuele-machineschaalset geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Als uw virtuele-machineschaalset zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten bevat, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door over te schakelen naar het gebruik van alleen de door het systeem toegewezen beheerde identiteit.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie voor de volledige quickstarts voor het maken van virtuele Azure-machines:
  
  - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md)