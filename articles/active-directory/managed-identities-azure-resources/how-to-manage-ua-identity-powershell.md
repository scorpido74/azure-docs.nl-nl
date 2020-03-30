---
title: Maak, lijst & verwijderen door de gebruiker toegewezen beheerde identiteit met Azure PowerShell - Azure AD
description: Stap voor stap instructies voor het maken, aanbieden en verwijderen van door de gebruiker toegewezen beheerde identiteit met Azure PowerShell.
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547407"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Een door de gebruiker toegewezen beheerde identiteit maken, aanbieden of verwijderen met Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor services die Azure AD-verificatie ondersteunen, zonder dat u referenties in uw code nodig hebt. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit maakt, vermeldt en verwijdert met Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de nieuwste versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.
- Als u PowerShell lokaal uitvoert, moet u ook het volgende doen: 
    - Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.
    - Installeer de [nieuwste versie van PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Voer `Install-Module -Name PowerShellGet -AllowPrerelease` uit om de voorlopige versie van de `PowerShellGet`-module op te halen (mogelijk moet u met `Exit` de huidige PowerShell-sessie afsluiten nadat u met deze opdracht de `Az.ManagedServiceIdentity`-module hebt geïnstalleerd).
    - Voer `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` deze versie van de `Az.ManagedServiceIdentity` module uit om de door de gebruiker toegewezen beheerde identiteitsbewerkingen in dit artikel uit te voeren.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u een door de gebruiker `New-AzUserAssignedIdentity` toegewezen beheerde identiteit wilt maken, gebruikt u de opdracht. De `ResourceGroupName` parameter geeft de resourcegroep op waar de door `-Name` de gebruiker toegewezen beheerde identiteit moet worden gemaakt en de parameter geeft de naam aan. Vervang `<RESOURCE GROUP>` de `<USER ASSIGNED IDENTITY NAME>` parameterwaarden en parameterwaarden door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weergeven

Als u een door de gebruiker toegewezen beheerde identiteit wilt aanbieden/lezen, heeft uw account de taaktoewijzing [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) of Managed Identity [Contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u door de gebruiker toegewezen beheerde identiteiten wilt weergeven, gebruikt u de opdracht [Get-AzUserAssigned] als u beheerde identiteiten wilt weergeven.  De `-ResourceGroupName` parameter geeft de resourcegroep op waar de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang `<RESOURCE GROUP>` de met uw eigen waarde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
In het antwoord hebben `"Microsoft.ManagedIdentity/userAssignedIdentities"` door de gebruiker toegewezen `Type`beheerde identiteiten waarde geretourneerd voor sleutel, .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u een door de gebruiker `Remove-AzUserAssignedIdentity` toegewezen beheerde identiteit wilt verwijderen, gebruikt u de opdracht.  De `-ResourceGroupName` parameter geeft de resourcegroep op waar de `-Name` door de gebruiker toegewezen identiteit is gemaakt en de parameter de naam opgeeft. Vervang `<RESOURCE GROUP>` de `<USER ASSIGNED IDENTITY NAME>` parameterswaarden en de parameters door uw eigen waarden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan de verwijzing is toegewezen. Identiteitstoewijzingen moeten afzonderlijk worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)voor een volledige lijst en meer details van de azure PowerShell beheerde identiteiten voor Azure-resources.
