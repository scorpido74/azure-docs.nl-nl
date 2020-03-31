---
title: Een beheerde identiteitstoegang toewijzen aan een bron met PowerShell - Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit op de ene bron, toegang tot een andere bron, met behulp van PowerShell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547250"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Een beheerde identiteitstoegang toewijzen aan een bron met PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zodra u een Azure-bron met een beheerde identiteit hebt geconfigureerd, u de beheerde identiteit toegang geven tot een andere bron, net als elke beveiligingsprincipal. In dit voorbeeld ziet u hoe u de beheerde identiteit van een Azure-virtuele machine toegang geeft tot een Azure-opslagaccount met PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de nieuwste versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>RBAC gebruiken om een beheerde identiteitstoegang toe te wijzen aan een andere bron

Nadat u beheerde identiteit hebt ingeschakeld op een Azure-bron, [zoals een Azure VM:](qs-configure-powershell-windows-vm.md)

1. Meld u aan `Connect-AzAccount` bij Azure met de cmdlet. Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de beheerde identiteit hebt geconfigureerd:

   ```powershell
   Connect-AzAccount
   ```
2. In dit voorbeeld geven we een Azure VM toegang tot een opslagaccount. Eerst gebruiken we [Get-AzVM](/powershell/module/az.compute/get-azvm) om de serviceprincipal voor de VM met de naam `myVM`VM te krijgen, die is gemaakt toen we beheerde identiteit inschakelen. Gebruik vervolgens [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) om de VM **Reader** toegang `myStorageAcct`te geven tot een opslagaccount genaamd:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht beheerde identiteit voor Azure-bronnen](overview.md)
- Zie [Beheerde identiteit configureren voor Azure-resources op een Azure VM configureren met PowerShell](qs-configure-powershell-windows-vm.md)als u beheerde identiteit op een Azure VM wilt inschakelen.
