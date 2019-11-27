---
title: Scoped Synchronization for Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het configureren van een scoped synchronisatie vanuit Azure AD naar een door Azure Active Directory Domain Services beheerd domein
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 525ea421eb0fa0131fa91078b0619b8463f6fbb0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546251"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Scoped Synchronization from Azure AD to Azure Active Directory Domain Services configureren

Azure Active Directory Domain Services (Azure AD DS) om verificatie services te bieden, synchroniseert gebruikers en groepen van Azure AD. In een hybride omgeving kunnen gebruikers en groepen van een on-premises Active Directory Domain Services-omgeving (AD DS) eerst worden gesynchroniseerd met Azure AD met behulp van Azure AD Connect en vervolgens worden gesynchroniseerd met Azure AD DS.

Standaard worden alle gebruikers en groepen van een Azure AD-Directory gesynchroniseerd met een door Azure AD DS beheerd domein. Als u specifieke vereisten hebt, kunt u in plaats daarvan ervoor kiezen om alleen een gedefinieerde set gebruikers te synchroniseren.

In dit artikel wordt beschreven hoe u een door Azure AD DS beheerd domein maakt dat gebruikmaakt van een synchronisatie met een scope en vervolgens de set gebruikers met een bereik wijzigt of uitschakelt.

## <a name="scoped-synchronization-overview"></a>Overzicht van scoped Synchronization

Standaard worden alle gebruikers en groepen van een Azure AD-Directory gesynchroniseerd met een door Azure AD DS beheerd domein. Als slechts een paar gebruikers toegang nodig hebben tot het beheerde domein, kunt u alleen die gebruikers accounts synchroniseren. Deze synchronisatie met een bereik is gebaseerd op een groep. Wanneer u synchronisatie op basis van een groeps bereik configureert, worden alleen de gebruikers accounts die deel uitmaken van de groepen die u opgeeft gesynchroniseerd met het door Azure AD DS beheerde domein.

De volgende tabel bevat een overzicht van het gebruik van scoped Synchronization:

| Huidige status | Gewenste status | Vereiste configuratie |
| --- | --- | --- |
| Een bestaand beheerd domein is geconfigureerd voor het synchroniseren van alle gebruikers accounts en-groepen. | U wilt alleen gebruikers accounts synchroniseren die deel uitmaken van specifieke groepen. | U kunt niet wijzigen van het synchroniseren van alle gebruikers naar het gebruik van scoped synchronisatie. [Verwijder het bestaande beheerde domein](delete-aadds.md)en volg de stappen in dit artikel om een Azure AD DS beheerd domein met scoped Synchronization opnieuw te maken. |
| Geen bestaand beheerd domein. | U wilt een nieuw beheerd domein maken en alleen gebruikers accounts synchroniseren die tot specifieke groepen behoren. | Volg de stappen in dit artikel om een door Azure AD DS beheerd domein te maken waarin de synchronisatie met scoped is geconfigureerd. |
| Een bestaand beheerd domein is geconfigureerd voor het synchroniseren van alleen accounts die deel uitmaken van specifieke groepen. | U wilt de lijst met groepen wijzigen waarvan gebruikers moeten worden gesynchroniseerd met het door Azure AD DS beheerde domein. | Volg de stappen in dit artikel om scoped Synchronization te wijzigen. |

Met de Azure Portal of Power shell kunt u de scoped synchronisatie-instellingen configureren:

| Actie | | |
|--|--|--|
| Een door Azure AD DS beheerd domein maken en een synchronisatie met een scope configureren | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Scoped synchronisatie wijzigen | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Scoped synchronisatie uitschakelen | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Als u het synchronisatie bereik wijzigt, wordt het door Azure AD DS beheerde domein om alle gegevens opnieuw te synchroniseren.
> 
>  * Wanneer u het synchronisatie bereik voor een door Azure AD DS beheerd domein wijzigt, wordt een volledige hersynchronisatie uitgevoerd.
>  * Objecten die niet meer nodig zijn in het door Azure AD DS beheerde domein worden verwijderd. Nieuwe objecten worden gemaakt in het beheerde domein.
>  * Het kan enige tijd duren voordat de synchronisatie is voltooid. De synchronisatie tijd is afhankelijk van het aantal objecten, zoals gebruikers, groepen en groepslid maatschappen in de Azure AD DS beheerde domein en Azure AD-adres lijst. Voor grote mappen met veel honderd duizenden objecten kan het enkele dagen duren voordat de synchronisatie is uitgevoerd.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Scoped synchronisatie inschakelen met behulp van de Azure Portal

1. Volg de [zelf studie voor het maken en configureren van een Azure AD DS-exemplaar](tutorial-create-instance-advanced.md). Voer alle vereisten en implementatie stappen uit, anders dan voor synchronisatie bereik.
1. Kies **scoped** bij de synchronisatie stap en selecteer vervolgens de Azure ad-groepen die u wilt synchroniseren met het Azure AD DS-exemplaar.

De Azure AD DS Managed Domain kan tot een uur duren om de implementatie te volt ooien. In de Azure Portal wordt op de pagina **overzicht** voor uw door Azure AD DS beheerde domein de huidige status weer gegeven in de gehele implementatie fase.

Wanneer de Azure Portal laat zien dat de inrichting van het beheerde Azure AD DS-domein is voltooid, moeten de volgende taken worden uitgevoerd:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* [Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Scoped Synchronization wijzigen met behulp van de Azure Portal

Voer de volgende stappen uit om de lijst met groepen te wijzigen waarvan gebruikers moeten worden gesynchroniseerd met het beheerde Azure AD DS-domein:

1. Zoek in het Azure Portal naar en selecteer **Azure AD Domain Services**. Kies uw instantie, bijvoorbeeld *contoso.com*.
1. Selecteer **synchronisatie** in het menu aan de linkerkant.
1. Als u een groep wilt toevoegen, **selecteert u groepen bovenaan selecteren** en kiest u vervolgens de groepen die u wilt toevoegen.
1. Als u een groep uit het synchronisatie bereik wilt verwijderen, selecteert u deze in de lijst met momenteel gesynchroniseerde groepen en kiest u **groepen verwijderen**.
1. Wanneer alle wijzigingen zijn aangebracht, selecteert u **synchronisatie bereik opslaan**.

Als u het synchronisatie bereik wijzigt, wordt het door Azure AD DS beheerde domein om alle gegevens opnieuw te synchroniseren. Objecten die niet meer nodig zijn in het door Azure AD DS beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Scoped synchronisatie uitschakelen met behulp van de Azure Portal

Voer de volgende stappen uit als u de synchronisatie op basis van een groep wilt uitschakelen voor een beheerd domein in azure AD DS:

1. Zoek in het Azure Portal naar en selecteer **Azure AD Domain Services**. Kies uw instantie, bijvoorbeeld *contoso.com*.
1. Selecteer **synchronisatie** in het menu aan de linkerkant.
1. Stel het synchronisatie **bereik in op** **alle**en selecteer vervolgens **synchronisatie bereik opslaan**.

Als u het synchronisatie bereik wijzigt, wordt het door Azure AD DS beheerde domein om alle gegevens opnieuw te synchroniseren. Objecten die niet meer nodig zijn in het door Azure AD DS beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="powershell-script-for-scoped-synchronization"></a>Power shell-script voor synchronisatie met scopes

Als u synchronisatie met een scope wilt configureren met behulp van Power shell, moet u eerst het volgende script opslaan in een bestand met de naam `Select-GroupsToSync.ps1`. Met dit script wordt Azure AD DS zodanig geconfigureerd dat geselecteerde groepen vanuit Azure AD worden gesynchroniseerd. Alle gebruikers accounts die deel uitmaken van de opgegeven groepen, worden gesynchroniseerd met het door Azure AD DS beheerde domein.

Dit script wordt gebruikt in de extra stappen in dit artikel.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Scoped synchronisatie inschakelen met Power shell

Gebruik Power shell om deze reeks stappen te volt ooien. Raadpleeg de instructies om Azure Active Directory Domain Services in te [scha kelen met behulp van Power shell](powershell-create-instance.md). Een paar stappen in dit artikel worden enigszins gewijzigd om de scoped synchronisatie te configureren.

1. Voer de volgende taken uit in het artikel om Azure AD DS in te scha kelen met behulp van Power shell. Stop bij de stap om het beheerde domein daad werkelijk te maken. U configureert de scoped synchronisatie u maakt het door Azure AD DS beheerde domein.

   * [Installeer de vereiste Power shell-modules](powershell-create-instance.md#prerequisites).
   * [Maak de vereiste service-principal en Azure AD-groep voor beheerders toegang](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Ondersteunende Azure-resources maken, zoals een virtueel netwerk en subnetten](powershell-create-instance.md#create-supporting-azure-resources).

1. Bepaal welke groepen en gebruikers ze bevatten die u wilt synchroniseren vanuit Azure AD. Maak een lijst met de weergave namen van de groepen die moeten worden gesynchroniseerd met Azure AD DS.

1. Voer het [script uit in de vorige sectie](#powershell-script-for-scoped-synchronization) en gebruik de para meter *-groupsToAdd* om de lijst met groepen die moeten worden gesynchroniseerd door te geven.

   > [!WARNING]
   > U moet de groep *Aad DC-Administrators* opnemen in de lijst met groepen voor synchronisatie met een bereik. Als u deze groep niet opneemt, is het door Azure AD DS beheerde domein onbruikbaar.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Maak nu de door Azure AD DS beheerde domein en schakel op groepen gebaseerde synchronisatie van het bereik in. Neem *' filteredSync ' = ' enabled* ' op in de para meter *-Properties* .

    Stel de ID van uw Azure-abonnement in en geef een naam op voor het beheerde domein, zoals *contoso.com*. U kunt uw abonnements-ID ophalen met behulp van de cmdlet [Get-AzSubscription][Get-AzSubscription] . Stel de naam van de resource groep, de naam van het virtuele netwerk en de regio in op de waarden die worden gebruikt in de vorige stappen voor het maken van de ondersteunende Azure-resources:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Het duurt enkele minuten om de resource te maken en het besturings element terug te sturen naar de Power shell-prompt. Het beheerde domein van Azure AD DS blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure Portal wordt op de pagina **overzicht** voor uw door Azure AD DS beheerde domein de huidige status weer gegeven in de gehele implementatie fase.

Wanneer de Azure Portal laat zien dat de inrichting van het beheerde Azure AD DS-domein is voltooid, moeten de volgende taken worden uitgevoerd:

* Werk de DNS-instellingen voor het virtuele netwerk bij, zodat de virtuele machines het beheerde domein kunnen vinden voor het lid worden van het domein of de verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* Als u een beheerd domein van Azure AD DS hebt gemaakt in een regio die Beschikbaarheidszones ondersteunt, maakt u een netwerk beveiligings groep om het verkeer in het virtuele netwerk voor het door Azure AD DS beheerde domein te beperken. Er wordt een Azure Standard-load balancer gemaakt waarvoor deze regels moeten worden uitgevoerd. Deze netwerk beveiligings groep beveiligt Azure AD DS en is vereist voor een juiste werking van het beheerde domein.
    * Als u de netwerk beveiligings groep en de vereiste regels wilt maken, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **overzichts** venster wordt u gevraagd om de netwerk beveiligings groep automatisch te maken en te configureren.
* [Wachtwoord synchronisatie inschakelen voor Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) zodat eind gebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfs referenties.

## <a name="modify-scoped-synchronization-using-powershell"></a>Scoped Synchronization wijzigen met Power shell

Als u de lijst met groepen wilt wijzigen waarvan gebruikers moeten worden gesynchroniseerd met de Azure AD DS beheerde domein, voert u het [Power shell-script](#powershell-script-for-scoped-synchronization) opnieuw uit en geeft u de nieuwe lijst met groepen op. In het volgende voor beeld bevat de groepen die worden gesynchroniseerd niet langer *GroupName2*en bevat nu *GroupName3*.

> [!WARNING]
> U moet de groep *Aad DC-Administrators* opnemen in de lijst met groepen voor synchronisatie met een bereik. Als u deze groep niet opneemt, is het door Azure AD DS beheerde domein onbruikbaar.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Als u het synchronisatie bereik wijzigt, wordt het door Azure AD DS beheerde domein om alle gegevens opnieuw te synchroniseren. Objecten die niet meer nodig zijn in het door Azure AD DS beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="disable-scoped-synchronization-using-powershell"></a>Scoped synchronisatie uitschakelen met Power shell

Als u synchronisatie op basis van een groep wilt uitschakelen voor een beheerd domein in azure AD DS, stelt u *"filteredSync" = "uitgeschakeld"* in op de Azure AD DS-resource en werkt u het beheerde domein bij. Wanneer dit is voltooid, worden alle gebruikers en groepen ingesteld op synchroniseren vanuit Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Als u het synchronisatie bereik wijzigt, wordt het door Azure AD DS beheerde domein om alle gegevens opnieuw te synchroniseren. Objecten die niet meer nodig zijn in het door Azure AD DS beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het synchronisatie proces [begrijpen synchronisatie in azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
