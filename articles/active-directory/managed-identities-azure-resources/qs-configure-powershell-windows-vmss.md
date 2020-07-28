---
title: Beheerde identiteiten op virtuele-machine schaal sets configureren met Power shell-Azure AD
description: Stapsgewijze instructies voor het configureren van een door een systeem en de gebruiker toegewezen beheerde identiteiten op een schaalset voor virtuele machines met behulp van Power shell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d549d3b59eabbeab6b8a892cb6800f0088ece2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609060"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Beheerde identiteiten configureren voor Azure-resources op schaal sets voor virtuele machines met behulp van Power shell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel, met behulp van Power shell, leert u hoe u de beheerde identiteiten voor Azure-bronnen bewerkingen kunt uitvoeren op een schaalset voor virtuele machines:
- De door het systeem toegewezen beheerde identiteit op een schaalset voor virtuele machines in-en uitschakelen
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een schaalset voor virtuele machines

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een aan het systeem toegewezen en door de gebruiker beheerde toegewezen identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheer bewerkingen in dit artikel wilt uitvoeren, moet uw account beschikken over de volgende toegangs beheer toewijzingen van Azure op basis van rollen:

    > [!NOTE]
    > Er zijn geen aanvullende Azure AD Directory-roltoewijzingen vereist.

    - [Inzender voor virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) voor het maken van een schaalset voor virtuele machines en het inschakelen en verwijderen van door het systeem toegewezen beheerde en/of door de gebruiker toegewezen beheerde identiteit uit een schaalset met virtuele machines.
    - Rol van [beheerde identiteit Inzender](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - De rol [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) voor het toewijzen en verwijderen van een door de gebruiker toegewezen beheerde identiteit van en aan een virtuele-machine schaalset.
- Installeer [de meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan. 

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u een door het systeem toegewezen beheerde identiteit kunt inschakelen en verwijderen met behulp van Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een schaalset voor virtuele Azure-machines

Een schaalset voor virtuele machines maken waarbij de door het systeem toegewezen beheerde identiteit is ingeschakeld:

1. Raadpleeg *voor beeld 1* in het naslag artikel [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet voor meer informatie over het maken van een schaalset voor virtuele machines met een door het systeem toegewezen beheerde identiteit.  Voeg de para meter toe `-IdentityType SystemAssigned` aan de `New-AzVmssConfig` cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande schaalset voor virtuele Azure-machines

Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een bestaande schaalset voor virtuele Azure-machines:

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijf machtigingen biedt voor de schaalset voor virtuele machines, zoals ' Inzender voor virtuele machines ':

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de eigenschappen van de schaalset voor virtuele machines op met behulp van de- [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) cmdlet. Gebruik vervolgens de `-IdentityType` Schakel optie voor de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om een door het systeem toegewezen beheerde identiteit in te scha kelen:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>De door het systeem toegewezen beheerde identiteit uitschakelen vanuit een schaalset voor virtuele Azure-machines

Als u een schaalset voor virtuele machines hebt die de door het systeem toegewezen beheerde identiteit niet meer nodig heeft, maar nog steeds door de gebruiker toegewezen beheerde identiteiten nodig hebt, gebruikt u de volgende cmdlet:

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijf machtigingen biedt voor de schaalset voor virtuele machines, zoals ' Inzender voor virtuele machines ':

2. Voer de volgende cmdlet uit:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Als u een schaalset voor virtuele machines hebt waaraan een door het systeem toegewezen beheerde identiteit niet meer nodig is en er geen door de gebruiker toegewezen beheerde identiteiten zijn, gebruikt u de volgende opdrachten:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt toevoegen aan en verwijderen uit een schaalset voor virtuele machines met behulp van Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen tijdens het maken van een schaalset voor virtuele Azure-machines

Het maken van een nieuwe schaalset voor virtuele machines met een door de gebruiker toegewezen beheerde identiteit wordt momenteel niet ondersteund via Power shell. Zie de volgende sectie voor informatie over het toevoegen van een door de gebruiker toegewezen beheerde identiteit aan een bestaande schaalset voor virtuele machines. Controleer later op updates.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande schaalset voor virtuele Azure-machines

Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande schaalset voor virtuele Azure-machines:

1. Meld u aan bij Azure met `Connect-AzAccount` . Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol die u schrijf machtigingen biedt voor de schaalset voor virtuele machines, zoals ' Inzender voor virtuele machines ':

   ```powershell
   Connect-AzAccount
   ```

2. Haal eerst de eigenschappen van de schaalset voor virtuele machines op met behulp van de- `Get-AzVM` cmdlet. Vervolgens kunt u een door de gebruiker toegewezen beheerde identiteit toewijzen aan de schaalset voor virtuele machines met behulp `-IdentityType` `-IdentityID` van de-en-switch in de cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) . Vervang `<VM NAME>` , `<SUBSCRIPTION ID>` , `<RESROURCE GROUP>` , `<USER ASSIGNED ID1>` door `USER ASSIGNED ID2` uw eigen waarden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset voor virtuele Azure-machines

Als uw schaalset voor virtuele machines meerdere door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle, behalve de laatste, verwijderen met de volgende opdrachten. Vervang de parameterwaarden `<RESOURCE GROUP>` en `<VIRTUAL MACHINE SCALE SET NAME>` door uw eigen waarden. De `<USER ASSIGNED IDENTITY NAME>` is de naam eigenschap van de door de gebruiker toegewezen beheerde identiteit, die op de virtuele-machine schaalset moet blijven. Deze informatie vindt u in het gedeelte identiteit van de virtuele-machine schaalset met behulp van `az vmss show` :

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Als uw schaalset voor virtuele machines geen door het systeem toegewezen beheerde identiteit heeft en u alle door de gebruiker toegewezen beheerde identiteiten wilt verwijderen, gebruikt u de volgende opdracht:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Als uw schaalset voor virtuele machines zowel door het systeem toegewezen als door de gebruiker toegewezen beheerde identiteiten heeft, kunt u alle door de gebruiker toegewezen beheerde identiteiten verwijderen door te scha kelen naar alleen door het systeem toegewezen beheerde identiteit te gebruiken.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie voor de volledige Snelstartgids voor het maken van virtuele Azure-machines:
  
  - [Een virtuele Windows-machine maken met PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Een virtuele Linux-machine maken met PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















