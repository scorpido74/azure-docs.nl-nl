---
title: Een beheerde identiteits toegang toewijzen aan een resource met behulp van Power shell-Azure AD
description: Stapsgewijze toegang instructies voor het toewijzen van een beheerde identiteit op een resource, tot een andere bron, met behulp van PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2283ac076ef761fd098d75e7120e6557a959574
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547250"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Een beheerde identiteit toegang toewijzen aan een resource met behulp van PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Wanneer u een Azure-resource hebt geconfigureerd met een beheerde identiteit, kunt u de toegang tot de beheerde identiteit geven naar een andere resource, net als bij elke beveiligings-principal. In dit voorbeeld ziet u hoe u een virtuele machine van Azure van beheerde identiteit toegang geven tot Azure storage-account met behulp van PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken voor het toewijzen van een beheerde identiteit toegang naar een andere resource

Nadat u de beheerde identiteit hebt ingeschakeld voor een Azure-resource, [zoals een Azure-VM](qs-configure-powershell-windows-vm.md):

1. Meld u aan bij Azure met behulp van de cmdlet `Connect-AzAccount`. Gebruik een account dat is gekoppeld aan het Azure-abonnement waarin u de beheerde identiteit hebt geconfigureerd:

   ```powershell
   Connect-AzAccount
   ```
2. In dit voorbeeld geeft we een Azure VM-toegang om een storage-account. Eerst gebruiken we [Get-AzVM](/powershell/module/az.compute/get-azvm) om de Service-Principal op te halen voor de virtuele machine met de naam `myVM`, die is gemaakt toen de beheerde identiteit werd ingeschakeld. Gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om de VM- **lezer** toegang te geven tot een opslag account met de naam `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met Power shell](qs-configure-powershell-windows-vm.md)voor meer informatie over het inschakelen van beheerde identiteiten op een virtuele Azure-machine.
