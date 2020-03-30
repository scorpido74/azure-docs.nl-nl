---
title: Groepsinstellingen configureren met PowerShell - Azure AD | Microsoft Documenten
description: Hoe de instellingen voor groepen beheren met Azure Active Directory-cmdlets
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/20/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d56bb7c30a8289fe7f261979dca6a4ffe2bfe99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048141"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory cmdlets voor het configureren van groepsinstellingen

Dit artikel bevat instructies voor het gebruik van Azure Active Directory (Azure AD) PowerShell-cmdlets om groepen te maken en bij te werken. Deze inhoud is alleen van toepassing op Office 365-groepen (soms uniforme groepen genoemd).

> [!IMPORTANT]
> Voor sommige instellingen is een Azure Active Directory Premium P1-licentie vereist. Zie de tabel [Sjablooninstellingen](#template-settings) voor meer informatie.

Voor meer informatie over hoe u voorkomen dat `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` niet-beheerders beveiligingsgroepen maken, stel u in zoals beschreven in [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Instellingen voor Office 365-groepen worden geconfigureerd met een object Instellingen en een object SettingsTemplate. In eerste instantie ziet u geen objecten instellingen in uw map, omdat uw map is geconfigureerd met de standaardinstellingen. Als u de standaardinstellingen wilt wijzigen, moet u een nieuw object met instellingen maken met behulp van een instellingensjabloon. Instellingensjablonen worden gedefinieerd door Microsoft. Er zijn verschillende instellingen sjablonen. Als u de groepsinstellingen van Office 365 voor uw map wilt configureren, gebruikt u de sjabloon 'Group.Unified'. Als u de groepsinstellingen van Office 365 in één groep wilt configureren, gebruikt u de sjabloon 'Group.Unified.Guest'. Deze sjabloon wordt gebruikt om gasttoegang tot een Office 365-groep te beheren. 

De cmdlets maken deel uit van de Azure Active Directory PowerShell V2-module. Zie het artikel [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/)voor instructies voor het downloaden en installeren van de module op uw computer. U de versie van versie 2 van de module installeren vanuit [de PowerShell-galerie.](https://www.powershellgallery.com/packages/AzureAD/)

## <a name="install-powershell-cmdlets"></a>PowerShell-cmdlets installeren

Verwijder elke oudere versie van de Azure Active Directory PowerShell voor Graph-module voor Windows PowerShell en installeer [Azure Active Directory PowerShell voor Graph - Public Preview Release (later dan 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) voordat u de PowerShell-opdrachten uitvoert.

1. Open de Windows PowerShell-app als beheerder.
2. Verwijder eventuele oudere versies van AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Installeer de nieuwste versie van AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Instellingen maken op directoryniveau
Met deze stappen worden instellingen gemaakt op directoryniveau, die van toepassing zijn op alle Office 365-groepen in de map. De cmdlet Get-AzureADDirectorySettingTemplate is alleen beschikbaar in de [Azure AD PowerShell Preview-module voor Grafiek.](https://www.powershellgallery.com/packages/AzureADPreview)

1. In de cmdlets DirectorySettings moet u de id opgeven van de InstellingenSjabloon die u wilt gebruiken. Als u deze id niet kent, retourneert deze cmdlet de lijst met alle instellingensjablonen:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Met deze cmdlet-aanroep worden alle beschikbare sjablonen geretourneerd:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Als u een URL van de gebruiksrichtlijn wilt toevoegen, moet u eerst het object SettingsTemplate ophalen dat de URL-waarde van de gebruiksrichtlijn definieert. dat wil zeggen, de sjabloon Group.Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Maak vervolgens een nieuw object met instellingen op basis van die sjabloon:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Werk vervolgens de waarde van de gebruiksrichtlijn bij:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Pas vervolgens de instelling toe:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. U de waarden lezen met:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Instellingen bijwerken op directoryniveau
Als u de waarde voor UsageGuideLinesUrl in de instellingssjabloon wilt bijwerken, leest u de huidige instellingen van Azure AD, anders kunnen we bestaande instellingen overschrijven dan de UsageGuideLinesUrl.

1. Download de huidige instellingen van de Group.Unified SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Controleer de huidige instellingen:
   
   ```powershell
   $Setting.Values
   ```
   
   Uitvoer:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Als u de waarde van UsageGuideLinesUrl wilt verwijderen, bewerkt u de URL als een lege tekenreeks:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Update opslaan in de map:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Sjablooninstellingen
Dit zijn de instellingen die zijn gedefinieerd in de Group.Unified SettingsTemplate. Tenzij anders aangegeven, vereisen deze functies een Azure Active Directory Premium P1-licentie. 

| **Instelling** | **Beschrijving** |
| --- | --- |
|  <ul><li>Groepscreatie inschakelen<li>Type: Boolean<li>Standaard: True |De vlag die aangeeft of Office 365-groepscreatie is toegestaan in de map door niet-beheerders. Voor deze instelling is geen Azure Active Directory Premium P1-licentie vereist.|
|  <ul><li>GroupCreationAllowedGroupId<li>Type: String<li>Standaard: "" |GUID van de beveiligingsgroep waarvoor de leden Office 365-groepen mogen maken, zelfs wanneer EnableGroupCreation == false. |
|  <ul><li>GebruiksrichtlijnenUrl<li>Type: String<li>Standaard: "" |Een koppeling naar de richtlijnen voor groepsgebruik. |
|  <ul><li>Classificatiebeschrijvingen<li>Type: String<li>Standaard: "" | Een door komma's afgebakende lijst met classificatiebeschrijvingen. De waarde van Classificatiebeschrijvingen is alleen geldig in deze indeling:<br>$setting["Classificatiebeschrijvingen"] ="Classificatie:beschrijving,classificatie:beschrijving".<br>waarbij classificatie overeenkomt met een vermelding op de classificatielijst.<br>Deze instelling is niet van toepassing wanneer EnableMIPLabels == True.|
|  <ul><li>Standaardclassificatie<li>Type: String<li>Standaard: "" | De classificatie die moet worden gebruikt als de standaardclassificatie voor een groep als deze is opgegeven.<br>Deze instelling is niet van toepassing wanneer EnableMIPLabels == True.|
|  <ul><li>Vereiste voorfixnaamnaam<li>Type: String<li>Standaard: "" | Tekenreeks met een maximale lengte van 64 tekens die de naamgevingsconventie definieert die is geconfigureerd voor Office 365-groepen. Zie [Een naamgevingsbeleid voor Office 365-groepen afdwingen voor](groups-naming-policy.md)meer informatie. |
| <ul><li>Aangepaste geblokkeerdewoordenlijst<li>Type: String<li>Standaard: "" | Door komma's gescheiden reeks zinnen die gebruikers niet mogen gebruiken in groepsnamen of aliassen. Zie [Een naamgevingsbeleid voor Office 365-groepen afdwingen voor](groups-naming-policy.md)meer informatie. |
| <ul><li>MSStandardBlockedWords inschakelen<li>Type: Boolean<li>Standaard: "False" | Niet gebruiken
|  <ul><li>Gastentoestaan<li>Type: Boolean<li>Standaard: False | Booleaandie aangeeft of een gastgebruiker eigenaar kan zijn van groepen. |
|  <ul><li>Gastentoegang toestaan<li>Type: Boolean<li>Standaard: True | Booleaandie aangeeft of een gastgebruiker toegang heeft tot inhoud van Office 365-groepen.  Voor deze instelling is geen Azure Active Directory Premium P1-licentie vereist.|
|  <ul><li>GuestUsageGuidelinesUrl GuestUsageGuidelinesUrl GuestUsageGuidelinesUrl GuestUsage<li>Type: String<li>Standaard: "" | De url van een link naar de richtlijnen voor gastgebruik. |
|  <ul><li>AllowtoAddGuests<li>Type: Boolean<li>Standaard: True | Een booleaan die aangeeft of gasten al dan niet aan deze directory mogen worden toegevoegd. <br>Deze instelling kan worden overschreven en alleen-lezen worden als *EnableMIPLabels* is ingesteld op *True* en een gastbeleid is gekoppeld aan het gevoeligheidslabel dat aan de groep is toegewezen.<br>Als de instelling AllowToAddGuests is ingesteld op False op tenantniveau, wordt elke instelling allowtoaddguests op groepsniveau genegeerd. Als u gasttoegang voor slechts een paar groepen wilt inschakelen, moet u AllowToAddGuests instellen om waar te zijn op tenantniveau en deze vervolgens selectief uitschakelen voor specifieke groepen. |
|  <ul><li>Classificatielijst<li>Type: String<li>Standaard: "" | Een door komma's afgebakende lijst met geldige classificatiewaarden die kunnen worden toegepast op Office 365-groepen. <br>Deze instelling is niet van toepassing wanneer EnableMIPLabels == True.|
|  <ul><li>EnableMIPLabels<li>Type: Boolean<li>Standaard: "False" |De vlag die aangeeft of gevoeligheidslabels die zijn gepubliceerd in Microsoft 365 Compliance Center, kan worden toegepast op Office 365-groepen. Zie [Gevoeligheidslabels toewijzen voor Office 365-groepen voor](groups-assign-sensitivity-labels.md)meer informatie . |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Voorbeeld: Gastbeleid configureren voor groepen op directoryniveau
1. Download alle instellingssjablonen:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Als u gastbeleid wilt instellen voor groepen op directoryniveau, hebt u de sjabloon Group.Unified nodig
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Maak vervolgens een nieuw object met instellingen op basis van die sjabloon:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Update vervolgens de instelling AllowToAddGuests bij
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Pas vervolgens de instelling toe:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. U de waarden lezen met:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Leesinstellingen op directoryniveau

Als u de naam weet van de instelling die u wilt ophalen, u de onderstaande cmdlet gebruiken om de huidige instellingenwaarde op te halen. In dit voorbeeld halen we de waarde op voor een instelling met de naam 'UsageGuidelinesUrl'. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
In deze stappen worden instellingen op directoryniveau gelezen die van toepassing zijn op alle Office-groepen in de map.

1. Lees alle bestaande directory-instellingen:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Met deze cmdlet wordt een lijst met alle directory-instellingen geretourneerd:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Lees alle instellingen voor een specifieke groep:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Lees alle directory-instellingen waarden van een specifiek directory-instellingenobject met behulp van Instellingen ID GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Met deze cmdlet worden de namen en waarden in dit instellingenobject voor deze specifieke groep geretourneerd:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Instellingen op mapniveau verwijderen
Met deze stap worden instellingen op directoryniveau verwijderd die van toepassing zijn op alle Office-groepen in de map.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Instellingen voor een specifieke groep maken

1. Zoeken naar de instellingensjabloon met de naam 'Groups.Unified.Guest'
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Het sjabloonobject voor de sjabloon Groups.Unified.Guest ophalen:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Een nieuw object met instellingen maken op basis van de sjabloon:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Stel de instelling in op de vereiste waarde:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Haal de id op van de groep waarop u deze instelling wilt toepassen:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Maak de nieuwe instelling voor de vereiste groep in de map:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Voer de opdracht uit om de instellingen te verifiëren:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Update-instellingen voor een specifieke groep
1. De id van de groep opvragen waarvan u de instelling wilt bijwerken:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Haal de instelling van de groep op:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Werk de instelling van de groep bij als u dat nodig hebt, bijv.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Download vervolgens de id van de instelling voor deze specifieke groep:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   U krijgt een antwoord vergelijkbaar met deze:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Vervolgens u de nieuwe waarde voor deze instelling instellen:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. U de waarde van de instelling lezen om er zeker van te zijn dat deze correct is bijgewerkt:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Verwijzing naar syntaxis van cmdlet
U meer Azure Active Directory PowerShell-documentatie vinden in [Azure Active Directory Cmdlets.](/powershell/azure/install-adv2?view=azureadps-2.0)

## <a name="additional-reading"></a>Aanvullende lezing

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)
