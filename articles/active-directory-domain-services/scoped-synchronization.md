---
title: Scoped-synchronisatie voor Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over het configureren van scoped-synchronisatie van Azure AD naar een beheerd Azure Active Directory Domain Services-domein
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
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613044"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Scoped synchronisatie configureren van Azure AD naar Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) synchroniseert gebruikers en groepen vanuit Azure AD om verificatieservices te bieden. In een hybride omgeving kunnen gebruikers en groepen uit een on-premises AD DS-omgeving (Active Directory Domain Services) eerst worden gesynchroniseerd met Azure AD met Azure AD Connect en vervolgens worden gesynchroniseerd met Azure AD DS.

Standaard worden alle gebruikers en groepen uit een Azure AD-map gesynchroniseerd met een door Azure AD DS beheerd domein. Als u specifieke behoeften hebt, u er in plaats daarvan voor kiezen om alleen een gedefinieerde set gebruikers te synchroniseren.

In dit artikel ziet u hoe u een door Azure AD DS beheerd domein maakt dat scoped-synchronisatie gebruikt en vervolgens de set scoped-gebruikers wijzigt of uitschakelt.

## <a name="scoped-synchronization-overview"></a>Overzicht van scoped-synchronisatie

Standaard worden alle gebruikers en groepen uit een Azure AD-map gesynchroniseerd met een door Azure AD DS beheerd domein. Als slechts een paar gebruikers toegang nodig hebben tot het beheerde domein, u alleen die gebruikersaccounts synchroniseren. Deze scoped synchronisatie is groepsgebaseerd. Wanneer u groepssynchronisatie op basis van scope configureert, worden alleen de gebruikersaccounts die behoren tot de groepen die u opgeeft, gesynchroniseerd met het door Azure AD DS beheerde domein.

In de volgende tabel wordt beschreven hoe u scoped-synchronisatie gebruiken:

| Huidige status | Gewenste staat | Vereiste configuratie |
| --- | --- | --- |
| Een bestaand beheerd domein is geconfigureerd om alle gebruikersaccounts en -groepen te synchroniseren. | U wilt alleen gebruikersaccounts synchroniseren die deel uitmaken van specifieke groepen. | U niet veranderen van het synchroniseren van alle gebruikers naar het gebruik van scoped synchronization. [Verwijder het bestaande beheerde domein](delete-aadds.md)en volg vervolgens de stappen in dit artikel om opnieuw een door Azure AD DS beheerd domein te maken met scoped synchronisatie geconfigureerd. |
| Geen bestaand beheerd domein. | U wilt een nieuw beheerd domein maken en alleen gebruikersaccounts van specifieke groepen synchroniseren. | Volg de stappen in dit artikel om een door Azure AD DS beheerd domein te maken met scoped synchronisatie geconfigureerd. |
| Een bestaand beheerd domein is geconfigureerd om alleen accounts te synchroniseren die deel uitmaken van specifieke groepen. | U wilt de lijst met groepen wijzigen waarvan de gebruikers moeten worden gesynchroniseerd met het beheerde Azure AD DS-domein. | Volg de stappen in dit artikel om scoped synchronisatie te wijzigen. |

U gebruikt de Azure-portal of PowerShell om de synchronisatie-instellingen voor scopen te configureren:

| Actie | | |
|--|--|--|
| Een door Azure AD DS beheerd domein maken en scoped synchronisatie configureren | [Azure-portal](#enable-scoped-synchronization-using-the-azure-portal) | [Powershell](#enable-scoped-synchronization-using-powershell) |
| Scoped-synchronisatie wijzigen | [Azure-portal](#modify-scoped-synchronization-using-the-azure-portal) | [Powershell](#modify-scoped-synchronization-using-powershell) |
| Scoped-synchronisatie uitschakelen | [Azure-portal](#disable-scoped-synchronization-using-the-azure-portal) | [Powershell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Als u het synchronisatiebereik wijzigt, wordt het beheerde Azure AD DS-domein opnieuw gesynchroniseerd.
> 
>  * Wanneer u het synchronisatiebereik voor een door Azure AD DS beheerd domein wijzigt, vindt een volledige resynchronisatie plaats.
>  * Objecten die niet langer nodig zijn in het beheerde Azure AD DS-domein, worden verwijderd. Er worden nieuwe objecten gemaakt in het beheerde domein.
>  * Opnieuw synchroniseren kan lang duren. De synchronisatietijd is afhankelijk van het aantal objecten, zoals gebruikers, groepen en groepslidmaatschappen in het azure AD DS-beheerde domein en de Azure AD-map. Voor grote mappen met vele honderdduizenden objecten kan opnieuw worden gesanerd, een paar dagen opnieuw worden gesynchroniseerd.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Scoped-synchronisatie inschakelen met de Azure-portal

1. Volg de [zelfstudie om een Azure AD DS-exemplaar te maken en te configureren.](tutorial-create-instance-advanced.md) Voer alle vereisten en implementatiestappen uit, behalve voor synchronisatiebereik.
1. Kies **Scoped** bij de synchronisatiestap en selecteer vervolgens de Azure AD-groepen die u wilt synchroniseren met het Azure AD DS-exemplaar.

Het beheerde Azure AD DS-domein kan tot een uur duren voordat de implementatie is voltooid. In de Azure-portal wordt op de **pagina Overzicht** voor uw door Azure AD DS beheerde domein de huidige status in deze implementatiefase weergegeven.

Wanneer de Azure-portal aangeeft dat het door Azure AD DS beheerde domein is voltooid, moeten de volgende taken worden voltooid:

* Dns-instellingen voor het virtuele netwerk bijwerken, zodat virtuele machines het beheerde domein kunnen vinden voor domeinjoin of verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **venster Overzicht** wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* [Wachtwoordsynchronisatie inschakelen voor Azure AD Domain Services,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfsreferenties.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Scoped synchronisatie wijzigen met de Azure-portal

Voer de volgende stappen uit om de lijst met groepen te wijzigen waarvan de gebruikers moeten worden gesynchroniseerd met het beheerde Azure AD DS-domein:

1. Zoek en selecteer Azure AD Domain Services in de **Azure-portal.** Kies uw instantie, zoals *aaddscontoso.com*.
1. Selecteer **Synchronisatie in** het menu aan de linkerkant.
1. Als u een groep wilt toevoegen, kiest u **+ Groepen** bovenaan selecteren en kiest u de groepen die u wilt toevoegen.
1. Als u een groep uit het synchronisatiebereik wilt verwijderen, selecteert u deze in de lijst met momenteel gesynchroniseerde groepen en kiest u **Groepen verwijderen**.
1. Wanneer alle wijzigingen zijn aangebracht, selecteert u **Synchronisatiebereik opslaan**.

Als u het synchronisatiebereik wijzigt, wordt het beheerde Azure AD DS-domein opnieuw gesynchroniseerd. Objecten die niet langer nodig zijn in het beheerde Azure AD DS-beheerde domein worden verwijderd en opnieuw synchronisatie kan lang duren.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Scoped-synchronisatie uitschakelen met de Azure-portal

Voer de volgende stappen uit om op groepen gebaseerde scopen voor een door Azure AD DS beheerd domein uit te schakelen:

1. Zoek en selecteer Azure AD Domain Services in de **Azure-portal.** Kies uw instantie, zoals *aaddscontoso.com*.
1. Selecteer **Synchronisatie in** het menu aan de linkerkant.
1. Stel het synchronisatiebereik in van **Scoped** naar **Alles**en selecteer **synchronisatiebereik opslaan**.

Als u het synchronisatiebereik wijzigt, wordt het beheerde Azure AD DS-domein opnieuw gesynchroniseerd. Objecten die niet langer nodig zijn in het beheerde Azure AD DS-beheerde domein worden verwijderd en opnieuw synchronisatie kan lang duren.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-script voor scoped synchronisatie

Als u scoped synchronisatie wilt configureren met PowerShell, slaat `Select-GroupsToSync.ps1`u eerst het volgende script op in een bestand met de naam . Met dit script configureert Azure AD DS om geselecteerde groepen uit Azure AD te synchroniseren. Alle gebruikersaccounts die deel uitmaken van de opgegeven groepen worden gesynchroniseerd met het beheerde Azure AD DS-domein.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Scoped-synchronisatie inschakelen met PowerShell

Gebruik PowerShell om deze set stappen uit te voeren. Raadpleeg de instructies om Azure Active Directory Domain Services in te [schakelen met PowerShell](powershell-create-instance.md). Een paar stappen in dit artikel worden enigszins gewijzigd om scoped synchronization te configureren.

1. Voltooi de volgende taken uit het artikel om Azure AD DS in te schakelen met PowerShell. Stop bij de stap om het beheerde domein daadwerkelijk te maken. U configureert de scoped synchronisatie die u maakt van het door Azure AD DS beheerde domein.

   * [Installeer de vereiste PowerShell-modules.](powershell-create-instance.md#prerequisites)
   * [Maak de vereiste serviceprincipal en Azure AD-groep voor beheerderstoegang](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Maak ondersteunende Azure-bronnen zoals een virtueel netwerk en subnetten.](powershell-create-instance.md#create-supporting-azure-resources)

1. Bepaal de groepen en gebruikers die ze bevatten die u vanuit Azure AD wilt synchroniseren. Maak een lijst met de weergavenamen van de groepen die moeten worden gesynchroniseerd met Azure AD DS.

1. Voer het [script uit de vorige sectie uit](#powershell-script-for-scoped-synchronization) en gebruik de parameter *-groupsToAdd* om de lijst met groepen door te geven om te synchroniseren.

   > [!WARNING]
   > U moet de groep *AAD DC-beheerders* opnemen in de lijst met groepen voor synchronisatie met bereik. Als u deze groep niet opneemt, is het beheerde Azure AD DS-domein onbruikbaar.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Maak nu het door Azure AD DS beheerde domein en schakel op groepsgerichte scopesynchronisatie in. Voeg *'filteredSync' = 'Ingeschakeld' toe* in de parameter *Eigenschappen.*

    Stel uw Azure-abonnements-id in en geef vervolgens een naam op voor het beheerde domein, zoals *aaddscontoso.com*. U uw abonnements-ID krijgen met de [cmdlet Get-AzSubscription.][Get-AzSubscription] Stel de naam van de brongroep, de naam van het virtuele netwerk en het gebied in op de waarden die in de vorige stappen zijn gebruikt om de ondersteunende Azure-bronnen te maken:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
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

Het duurt een paar minuten om de resource te maken en het besturingselement terug te geven aan de PowerShell-prompt. Het beheerde Azure AD DS-domein blijft op de achtergrond worden ingericht en kan tot een uur duren voordat de implementatie is voltooid. In de Azure-portal wordt op de **pagina Overzicht** voor uw door Azure AD DS beheerde domein de huidige status in deze implementatiefase weergegeven.

Wanneer de Azure-portal aangeeft dat het door Azure AD DS beheerde domein is voltooid, moeten de volgende taken worden voltooid:

* Dns-instellingen voor het virtuele netwerk bijwerken, zodat virtuele machines het beheerde domein kunnen vinden voor domeinjoin of verificatie.
    * Als u DNS wilt configureren, selecteert u uw door Azure AD DS beheerde domein in de portal. In het **venster Overzicht** wordt u gevraagd deze DNS-instellingen automatisch te configureren.
* Als u een door Azure AD DS beheerd domein hebt gemaakt in een regio die beschikbaarheidszones ondersteunt, maakt u een netwerkbeveiligingsgroep om het verkeer in het virtuele netwerk voor het beheerde Azure AD DS-beheerde domein te beperken. Er wordt een Azure-standaardloadbalancer gemaakt waarvoor deze regels moeten worden geïnstalleerd. Deze netwerkbeveiligingsgroep beveiligt Azure AD DS en is vereist dat het beheerde domein correct werkt.
    * Als u de netwerkbeveiligingsgroep en vereiste regels wilt maken, selecteert u uw door Azure AD DS beheerde domein in de portal. In het venster **Overzicht** wordt u gevraagd om de netwerkbeveiligingsgroep automatisch te maken en te configureren.
* [Wachtwoordsynchronisatie inschakelen voor Azure AD Domain Services,](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) zodat eindgebruikers zich kunnen aanmelden bij het beheerde domein met hun bedrijfsreferenties.

## <a name="modify-scoped-synchronization-using-powershell"></a>Scoped synchronisatie wijzigen met PowerShell

Als u de lijst met groepen wilt wijzigen waarvan de gebruikers moeten worden gesynchroniseerd met het door Azure AD DS beheerde domein, voert u het [PowerShell-script](#powershell-script-for-scoped-synchronization) opnieuw uit en geeft u de nieuwe lijst met groepen op. In het volgende voorbeeld bevatten de groepen die moeten worden gesynchroniseerd *GroupName2*niet meer en bevat nu *GroupName3*.

> [!WARNING]
> U moet de groep *AAD DC-beheerders* opnemen in de lijst met groepen voor synchronisatie met bereik. Als u deze groep niet opneemt, is het beheerde Azure AD DS-domein onbruikbaar.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Als u het synchronisatiebereik wijzigt, wordt het beheerde Azure AD DS-domein opnieuw gesynchroniseerd. Objecten die niet langer nodig zijn in het beheerde Azure AD DS-beheerde domein worden verwijderd en opnieuw synchronisatie kan lang duren.

## <a name="disable-scoped-synchronization-using-powershell"></a>Scoped-synchronisatie uitschakelen met PowerShell

Als u groepssynchronisatie voor een door Azure AD DS beheerd domein wilt uitschakelen, stelt u *'filteredSync' = 'Uitgeschakeld'* in op de Azure AD DS-bron en werkt u het beheerde domein bij. Als deze is voltooid, worden alle gebruikers en groepen ingesteld op synchronisatie vanuit Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Als u het synchronisatiebereik wijzigt, wordt het beheerde Azure AD DS-domein opnieuw gesynchroniseerd. Objecten die niet langer nodig zijn in het beheerde Azure AD DS-beheerde domein worden verwijderd en opnieuw synchronisatie kan lang duren.

## <a name="next-steps"></a>Volgende stappen

Zie Synchronisatie [begrijpen in Azure AD Domain Services](synchronization.md)voor meer informatie over het synchronisatieproces.

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
