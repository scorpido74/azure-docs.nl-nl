---
title: 'Azure Active Directory Domain Services: Scoped synchronisatie | Microsoft Docs'
description: Scoped Synchronization from Azure AD configureren voor uw beheerde domeinen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7d3bd8c6c62c0b8a1be6203e426337fcee7d2126
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617118"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Scoped Synchronization from Azure AD configureren voor uw beheerde domein
In dit artikel wordt beschreven hoe u alleen specifieke gebruikers accounts kunt configureren voor synchronisatie vanuit uw Azure AD-Directory naar uw Azure AD Domain Services beheerde domein.


## <a name="group-based-scoped-synchronization"></a>Op groepen gebaseerde synchronisatie van scopes
Standaard worden alle gebruikers en groepen in uw Azure AD-adres lijst gesynchroniseerd met uw beheerde domein. Als slechts een paar gebruikers het beheerde domein gebruiken, kunt u alleen die gebruikers accounts synchroniseren. Met synchronisatie op basis van een groep met scopes kunt u dit doen. Wanneer deze zijn geconfigureerd, worden alleen gebruikers accounts die deel uitmaken van de groepen die u hebt opgegeven, gesynchroniseerd met het beheerde domein.

De volgende tabel helpt u bij het bepalen van het gebruik van scoped Synchronization:

| **Huidige status** | **Gewenste status** | **Vereiste configuratie** |
| --- | --- | --- |
| Uw bestaande beheerde domein is geconfigureerd voor het synchroniseren van alle gebruikers accounts en-groepen. | U wilt alleen gebruikers accounts die deel uitmaken van specifieke groepen, synchroniseren met uw beheerde domein. | [Verwijder het bestaande beheerde domein](delete-aadds.md). Volg vervolgens de instructies in dit artikel om de app opnieuw te maken met scoped Synchronization configured. |
| U hebt geen bestaand beheerd domein. | U wilt een nieuw beheerd domein maken en alleen gebruikers accounts synchroniseren die tot specifieke groepen behoren. | Volg de instructies in dit artikel voor het maken van een nieuw beheerd domein met scoped Synchronization geconfigureerd. |
| Uw bestaande beheerde domein is geconfigureerd voor het synchroniseren van alleen accounts die tot specifieke groepen behoren. | U wilt de lijst met groepen wijzigen waarvan gebruikers moeten worden gesynchroniseerd met het domein beheren. | Volg de instructies in dit artikel om de scoped Synchronization te wijzigen. |

> [!WARNING]
> **Als u het synchronisatie bereik wijzigt, wordt uw beheerde domein opnieuw gesynchroniseerd.**
> 
>  * Wanneer u het synchronisatie bereik voor een beheerd domein wijzigt, wordt een volledige hersynchronisatie uitgevoerd.
>  * Objecten die niet meer in het beheerde domein zijn vereist, worden verwijderd. Nieuwe objecten worden gemaakt in het beheerde domein.
>  * Het kan enige tijd duren voordat de synchronisatie is voltooid, afhankelijk van het aantal objecten (gebruikers, groepen en groepslid maatschappen) in uw beheerde domein en uw Azure AD-Directory. Voor grote mappen met veel honderd duizenden objecten kan het enkele dagen duren voordat de synchronisatie is uitgevoerd.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Een nieuw beheerd domein maken en op groepen gebaseerde synchronisatie inschakelen met behulp van Azure Portal

1. Volg de aan de slag- [hand leiding](tutorial-create-instance.md) voor het maken van een beheerd domein.
2. Kies **bereik** tijdens de selectie van de synchronisatie stijl in de wizard Azure AD Domain Services maken.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Een nieuw beheerd domein maken en op groepen gebaseerde synchronisatie inschakelen met behulp van Power shell
Gebruik Power shell om deze reeks stappen te volt ooien. Raadpleeg de instructies om Azure Active Directory Domain Services in te [scha kelen met behulp van Power shell](powershell-create-instance.md). Een paar stappen in dit artikel worden enigszins gewijzigd om de scoped synchronisatie te configureren.

Voer de volgende stappen uit om op groepen gebaseerde synchronisatie van het bereik te configureren voor uw beheerde domein:

1. Voer de volgende taken uit:
   * [Taak 1: Installeer de vereiste Power shell](powershell-create-instance.md#task-1-install-the-required-powershell-modules)-modules.
   * [Taak 2: Maak de vereiste service-principal in uw Azure AD](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)-Directory.
   * [Taak 3: Maak en configureer de groep AAD DC-Administrators] Power shell-Create-instance. MD # taak-3-Create-and-configure-the-Aad-DC-Administrators-group).
   * [Taak 4: Registreer de Azure AD Domain Services resource provider](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider).
   * [Taak 5: Maak een resource groep](powershell-create-instance.md#task-5-create-a-resource-group).
   * [Taak 6: Het virtuele netwerk](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)maken en configureren.

2. Selecteer de groepen die u wilt synchroniseren en geef de weergave naam op van de groepen die u wilt synchroniseren met uw beheerde domein.

3. Sla het [script in de volgende sectie op in](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) een bestand ```Select-GroupsToSync.ps1```met de naam. Voer het script uit zoals hieronder wordt beschreven:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Vergeet niet om de groep AAD DC-Administrators op te sluiten.**
   >
   > U moet de groep AAD DC-Administrators opnemen in de lijst met groepen die zijn geconfigureerd voor synchronisatie met een bereik. Als u deze groep niet opneemt, wordt het beheerde domein onbruikbaar.
   >

4. Maak nu het beheerde domein en schakel op groepen gebaseerde synchronisatie in voor het beheerde domein. Neem de eigenschap ```"filteredSync" = "Enabled"``` op in ```Properties``` de para meter. Zie bijvoorbeeld het volgende script fragment dat is gekopieerd uit [taak 7: Richt het Azure AD Domain Services beheerde domein](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)in.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Vergeet niet in te voegen ```"filteredSync" = "Enabled"``` in de ```-Properties``` para meter, dus synchronisatie met een bereik is ingeschakeld voor het beheerde domein.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Script om groepen te selecteren die moeten worden gesynchroniseerd met het beheerde domein (Select-GroupsToSync. ps1)
Sla het volgende script op in een bestand```Select-GroupsToSync.ps1```(). Met dit script configureert u Azure AD Domain Services om geselecteerde groepen te synchroniseren met het beheerde domein. Alle gebruikers accounts die deel uitmaken van de opgegeven groepen, worden gesynchroniseerd met het beheerde domein.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Synchronisatie op basis van een groeps scope wijzigen
Als u de lijst met groepen wilt wijzigen waarvan gebruikers moeten worden gesynchroniseerd met uw beheerde domein, voert u het [Power shell-script](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) opnieuw uit en geeft u de nieuwe lijst met groepen op. Geef altijd de groep ' AAD DC-Administrators ' op in deze lijst.

> [!WARNING]
> **Vergeet niet om de groep AAD DC-Administrators op te sluiten.**
>
> U moet de groep AAD DC-Administrators opnemen in de lijst met groepen die zijn geconfigureerd voor synchronisatie met een bereik. Als u deze groep niet opneemt, wordt het beheerde domein onbruikbaar.
>


## <a name="disable-group-based-scoped-synchronization"></a>Synchronisatie op basis van een groep uitschakelen
Gebruik het volgende Power shell-script om synchronisatie op basis van een groep uit te scha kelen voor uw beheerde domein:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Volgende stappen
* [Synchronisatie begrijpen in Azure AD Domain Services](synchronization.md)
* [Azure Active Directory Domain Services inschakelen met behulp van Power shell](powershell-create-instance.md)
