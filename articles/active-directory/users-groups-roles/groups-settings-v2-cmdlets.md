---
title: PowerShell V2-voorbeelden voor het beheren van groepen - Azure AD | Microsoft Documenten
description: Op deze pagina vindt u PowerShell-voorbeelden waarmee u uw groepen beheren in Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, Groepen, Groepsbeheer
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a218e956c72f8005e533db7b8800e98ee72ce223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233117"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Azure Active Directory-versie 2-cmdlets voor groepsbeheer

> [!div class="op_single_selector"]
> - [Azure-portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [Powershell](groups-settings-v2-cmdlets.md)
>
>

Dit artikel bevat voorbeelden van het gebruik van PowerShell om uw groepen te beheren in Azure Active Directory (Azure AD).  Het vertelt u ook hoe u instellen met de Azure AD PowerShell-module. Eerst moet u [de Azure AD PowerShell-module downloaden.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="install-the-azure-ad-powershell-module"></a>De Azure AD PowerShell-module installeren

Als u de Azure AD PowerShell-module wilt installeren, gebruikt u de volgende opdrachten:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Als u wilt controleren of de module klaar is voor gebruik, gebruikt u de volgende opdracht:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Nu u beginnen met het gebruik van de cmdlets in de module. Raadpleeg de online referentiedocumentatie voor [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0)voor een volledige beschrijving van de cmdlets in de Azure AD-module.

## <a name="connect-to-the-directory"></a>Verbinding maken met de map

Voordat u groepen beheren met Azure AD PowerShell-cmdlets, moet u uw PowerShell-sessie verbinden met de map die u wilt beheren. Gebruik de volgende opdracht:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

De cmdlet vraagt u om de referenties die u wilt gebruiken om toegang te krijgen tot uw directory. In dit voorbeeld gebruiken karen@drumkit.onmicrosoft.com we om toegang te krijgen tot de demonstratiemap. De cmdlet retourneert een bevestiging om aan te geven dat de sessie is verbonden met uw map:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

U nu beginnen met het gebruik van de AzureAD-cmdlets om groepen in uw map te beheren.

## <a name="retrieve-groups"></a>Groepen ophalen

Als u bestaande groepen uit uw map wilt ophalen, gebruikt u de cmdlet Get-AzureADGroups. 

Als u alle groepen in de map wilt ophalen, gebruikt u de cmdlet zonder parameters:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

De cmdlet retourneert alle groepen in de verbonden map.

U de parameter -objectID gebruiken om een specifieke groep op te halen waarvoor u de objectID van de groep opgeeft:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

De cmdlet retourneert nu de groep waarvan de objectID overeenkomt met de waarde van de parameter die u hebt ingevoerd:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

U zoeken naar een specifieke groep met behulp van de parameter -filter. Deze parameter neemt een ODATA-filterclausule en retourneert alle groepen die overeenkomen met het filter, zoals in het volgende voorbeeld:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> De Azure AD PowerShell-cmdlets implementeren de OData-querystandaard. Zie **$filter** in [de queryopties van het OData-systeem voor](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)meer informatie met het OData-eindpunt .

## <a name="create-groups"></a>Groepen maken

Als u een nieuwe groep in uw map wilt maken, gebruikt u de cmdlet Nieuw-AzureADGroup. Deze cmdlet creëert een nieuwe beveiligingsgroep genaamd "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Groepen bijwerken

Als u een bestaande groep wilt bijwerken, gebruikt u de cmdlet Set-AzureADGroup. In dit voorbeeld wijzigen we de eigenschap DisplayName van de groep 'Intune Administrators'. Eerst zoeken we de groep met de cmdlet en het filter Get-AzureADGroup met het kenmerk DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Vervolgens wijzigen we de eigenschap Beschrijving in de nieuwe waarde 'Apparaatbeheerders inafstemmen':

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Als we de groep nu opnieuw vinden, zien we dat de eigenschap Beschrijving is bijgewerkt om de nieuwe waarde weer te geven:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Groepen verwijderen

Als u groepen uit uw map wilt verwijderen, gebruikt u de cmdlet Remove-AzureADGroup als volgt:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Groepslidmaatschap beheren

### <a name="add-members"></a>Leden toevoegen

Als u nieuwe leden aan een groep wilt toevoegen, gebruikt u de cmdlet Add-AzureADGroupMember. Met deze opdracht wordt een lid toegevoegd aan de groep Intune-beheerders die we in het vorige voorbeeld hebben gebruikt:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

De parameter -ObjectId is de ObjectID van de groep waaraan we een lid willen toevoegen, en de -RefObjectId is de ObjectID van de gebruiker die we als lid aan de groep willen toevoegen.

### <a name="get-members"></a>Leden krijgen

Als u de bestaande leden van een groep wilt opvragen, gebruikt u de cmdlet Get-AzureADGroupMember, zoals in dit voorbeeld:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Leden verwijderen

Als u het lid wilt verwijderen dat we eerder aan de groep hebben toegevoegd, gebruikt u de cmdlet Remove-AzureADGroupMember, zoals hier wordt weergegeven:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Leden verifiëren

Als u de groepslidmaatschappen van een gebruiker wilt verifiëren, gebruikt u de cmdlet Select-AzureADGroupIdsUserIsMemberOf. Deze cmdlet neemt als zijn parameters de ObjectId van de gebruiker waarvoor de groep lidmaatschappen te controleren, en een lijst van groepen waarvoor de lidmaatschappen te controleren. De lijst met groepen moet worden weergegeven in de vorm van een complexe variabele van het type "Microsoft.Open.AzureAD.Model.Model.GroupIdsForMembershipCheck", dus we moeten eerst een variabele met dat type maken:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Vervolgens geven we waarden voor de groupId's om het kenmerk "GroupIds" van deze complexe variabele te controleren:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Nu, als we willen de groepslidmaatschappen van een gebruiker te controleren met ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea tegen de groepen in $g, moeten we gebruiken:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

De geretourneerde waarde is een lijst met groepen waarvan deze gebruiker lid is. U deze methode ook toepassen om het lidmaatschap van contactpersonen, groepen of serviceprincipals te controleren op een bepaalde lijst met groepen, met Behulp van Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf of Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Groepscreatie door uw gebruikers uitschakelen

U voorkomen dat niet-beheerders beveiligingsgroepen maken. Het standaardgedrag in Microsoft Online Directory Services (MSODS) is om niet-beheerders in staat te stellen groepen te maken, ongeacht of ook selfservicegroepsbeheer (SSGM) is ingeschakeld. De SSGM-instelling regelt alleen het gedrag in het toegangspaneel Van Mijn apps.

Ga als u de aanmaak van groepen voor niet-beheerders uit:

1. Controleer of niet-beheerders groepen mogen maken:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Als het `UsersPermissionToCreateGroupsEnabled : True`terugkeert, kunnen niet-beheerdersgroepen maken. Ga als volgt te werk om deze functie uit te schakelen:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Eigenaren van groepen beheren

Als u eigenaren aan een groep wilt toevoegen, gebruikt u de cmdlet Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

De parameter -ObjectId is de ObjectID van de groep waaraan we een eigenaar willen toevoegen, en de -RefObjectId is de ObjectID van de gebruiker of serviceprincipal die we als eigenaar van de groep willen toevoegen.

Als u de eigenaren van een groep wilt ophalen, gebruikt u de cmdlet Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

De cmdlet retourneert de lijst met eigenaren (gebruikers en serviceprincipals) voor de opgegeven groep:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Als u een eigenaar uit een groep wilt verwijderen, gebruikt u de cmdlet Verwijderen-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Gereserveerde aliassen

Wanneer een groep wordt gemaakt, kunnen bepaalde eindpunten de eindgebruiker toestaan een e-mailnickname of alias op te geven die moet worden gebruikt als onderdeel van het e-mailadres van de groep.Groepen met de volgende zeer bevoorrechte e-mailaliassen kunnen alleen worden gemaakt door een globale Azure AD-beheerder. 
  
* Misbruik
* beheerder
* beheerder
* hostmaster
* majordomo
* Postmaster
* Root
* Veilige
* security
* ssl-beheerder
* Webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Schrijven naar on-premises groep (voorbeeld)

Tegenwoordig worden veel groepen nog steeds beheerd in on-premises Active Directory. Als u verzoeken wilt beantwoorden om cloudgroepen opnieuw te synchroniseren met on-premises, is de terugschrijffunctie voor Office 365-groepen voor Azure AD nu beschikbaar voor preview.

Office 365-groepen worden gemaakt en beheerd in de cloud. Met de schrijfmogelijkheid u Office 365-groepen als distributiegroepen terugschrijven naar een Active Directory-forest met Exchange geïnstalleerd. Gebruikers met on-premises Exchange-postvakken kunnen vervolgens e-mails van deze groepen verzenden en ontvangen. De groepterugschrijffunctie biedt geen ondersteuning voor Azure AD-beveiligingsgroepen of distributiegroepen.

Raadpleeg voor meer informatie documentatie voor de [Azure AD Connect-synchronisatieservice](../hybrid/how-to-connect-syncservice-features.md).

Office 365-groepsafschrijving is een openbare preview-functie van Azure Active Directory (Azure AD) en is beschikbaar met elk betaald Azure AD-licentieplan. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)bepaalde juridische informatie over voorvertoningen.

## <a name="next-steps"></a>Volgende stappen

U meer Azure Active Directory PowerShell-documentatie vinden in [Azure Active Directory Cmdlets.](/powershell/azure/install-adv2?view=azureadps-2.0)

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context) (Toegang tot resources beheren met Azure Active Directory-groepen)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
