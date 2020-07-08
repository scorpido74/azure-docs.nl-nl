---
title: Een door de gebruiker toegewezen beheerde identiteit maken, weer geven & verwijderen met behulp van Azure PowerShell-Azure AD
description: Stapsgewijze instructies voor het maken, weer geven en verwijderen van een door de gebruiker toegewezen beheerde identiteit met behulp van Azure PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 675c9a795a75f2a37e107ab9e8129a761581cde0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608446"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met behulp van Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt maken, weer geven en verwijderen met behulp van Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Installeer [de meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.
- Als u PowerShell lokaal uitvoert, moet u ook het volgende doen: 
    - Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.
    - Installeer de [nieuwste versie van PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Voer `Install-Module -Name PowerShellGet -AllowPrerelease` uit om de voorlopige versie van de `PowerShellGet`-module op te halen (mogelijk moet u met `Exit` de huidige PowerShell-sessie afsluiten nadat u met deze opdracht de `Az.ManagedServiceIdentity`-module hebt geïnstalleerd).
    - Voer uit `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` om de voorlopige versie van de `Az.ManagedServiceIdentity` module te installeren en de door de gebruiker toegewezen beheerde identiteits bewerkingen in dit artikel uit te voeren.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, gebruikt u de `New-AzUserAssignedIdentity` opdracht. Met de `ResourceGroupName` para meter wordt de resource groep opgegeven waarin de door de gebruiker toegewezen beheerde identiteit moet worden gemaakt `-Name` . de para meter geeft de naam op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` para meters en door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weer geven

Als u een door de gebruiker toegewezen beheerde identiteit wilt weer geven/lezen, moet uw account beschikken over de rol [Managed Identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) of [Managed id contributor](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u door de gebruiker toegewezen beheerde identiteiten wilt weer geven, gebruikt u de opdracht [Get-AzUserAssigned].  Met de `-ResourceGroupName` para meter wordt de resource groep opgegeven waarin de door de gebruiker toegewezen beheerde identiteit is gemaakt. Vervang de `<RESOURCE GROUP>` door uw eigen waarde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
In het antwoord hebben door de gebruiker toegewezen beheerde identiteiten een `"Microsoft.ManagedIdentity/userAssignedIdentities"` waarde geretourneerd voor de sleutel, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, gebruikt u de `Remove-AzUserAssignedIdentity` opdracht.  De `-ResourceGroupName` para meter geeft de resource groep op waarin de door de gebruiker toegewezen identiteit is gemaakt en de `-Name` para meter geeft de naam op. Vervang de `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` para meters en de parameter waarden door uw eigen waarden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan deze is toegewezen. Identiteits toewijzingen moeten afzonderlijk worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)voor een volledige lijst en meer informatie over de Azure PowerShell beheerde identiteiten voor Azure-resources-opdrachten.
