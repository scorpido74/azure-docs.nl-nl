---
title: Groeps instellingen configureren met behulp van Power shell-Azure Active Directory | Microsoft Docs
description: De instellingen voor groepen beheren met Azure Active Directory-cmdlets
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/10/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f2d3f8d8d2298ec00532205e359ed6f8dbc87a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315682"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory cmdlets voor het configureren van groepsinstellingen
Dit artikel bevat instructies voor het gebruik van Azure Active Directory (Azure AD) Power shell-cmdlets om groepen te maken en bij te werken. Deze inhoud is alleen van toepassing op Office 365-groepen (ook wel Unified groups genoemd). 

> [!IMPORTANT]
> Voor sommige instellingen is een Azure Active Directory Premium P1-licentie vereist. Zie de tabel met [sjabloon instellingen](#template-settings) voor meer informatie.

Voor meer informatie over het voor komen van niet-beheerders gebruikers om beveiligings groepen te maken `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` , stelt u in zoals beschreven in [set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Instellingen voor Office 365-groepen worden geconfigureerd met behulp van een instellingen object en een SettingsTemplate-object. In eerste instantie ziet u geen instellingen objecten in uw directory, omdat uw directory is geconfigureerd met de standaard instellingen. Als u de standaard instellingen wilt wijzigen, moet u een nieuw instellingen object maken met behulp van een instellingen sjabloon. Instellingen sjablonen worden gedefinieerd door micro soft. Er zijn verschillende instellingen sjablonen. Als u de instellingen voor de Office 365-groep voor uw Directory wilt configureren, gebruikt u de sjabloon groep. Unified. Als u de instellingen voor de Office 365-groep wilt configureren voor één groep, gebruikt u de sjabloon met de naam groep. Unified. Guest. Deze sjabloon wordt gebruikt voor het beheren van gast toegang tot een Office 365-groep. 

De cmdlets maken deel uit van de Azure Active Directory Power shell V2-module. Zie het artikel [Azure Active Directory Power shell versie 2](https://docs.microsoft.com/powershell/azuread/)voor instructies voor het downloaden en installeren van de module op uw computer. U kunt de versie 2-versie van de module installeren vanuit [de Power shell-galerie](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>PowerShell-cmdlets installeren

Verwijder een oudere versie van Azure Active Directory PowerShell voor Graph Module voor Windows PowerShell en installeer [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell voor Graph - Release 2.0.0.137 voor openbare preview) voordat u de PowerShell-opdrachten uitvoert.

1. Open de Windows PowerShell-app als beheerder.
2. Verwijder eventuele oudere versies van AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Installeer de nieuwste versie van AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview

## Create settings at the directory level
These steps create settings at directory level, which apply to all Office 365 groups in the directory. The Get-AzureADDirectorySettingTemplate cmdlet is available only in the [Azure AD PowerShell Preview module for Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. In the DirectorySettings cmdlets, you must specify the ID of the SettingsTemplate you want to use. If you do not know this ID, this cmdlet returns the list of all settings templates:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Met deze cmdlet-aanroep worden alle beschik bare sjablonen geretourneerd:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Als u een URL voor gebruiks richtlijn wilt toevoegen, moet u eerst het SettingsTemplate-object ophalen dat de URL-waarde voor de gebruiks richtlijn definieert. dat wil zeggen, de sjabloon Group. Unified:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Maak vervolgens een nieuw instellingen object op basis van deze sjabloon:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Werk vervolgens de waarde voor het gebruik van de richt lijn bij:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Pas vervolgens de instelling toe:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. U kunt de waarden lezen met behulp van:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Instellingen bijwerken op mapniveau
Als u de waarde voor UsageGuideLinesUrl in de instellings sjabloon wilt bijwerken, bewerkt u de URL gewoon met stap 4 hierboven en voert u vervolgens stap 5 uit om de nieuwe waarde in te stellen.

Als u de waarde van UsageGuideLinesUrl wilt verwijderen, bewerkt u de URL in een lege teken reeks met behulp van stap 4 hierboven:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Voer vervolgens stap 5 uit om de nieuwe waarde in te stellen.

## <a name="template-settings"></a>Sjabloon instellingen
Dit zijn de instellingen die zijn gedefinieerd in de groep. Unified SettingsTemplate. Tenzij anders aangegeven, is voor deze functies een Azure Active Directory Premium P1-licentie vereist. 

| **Instelling** | **Beschrijving** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Type: Boolean-waarde<li>Prijs Waar |De vlag die aangeeft of het maken van een Office 365-groep is toegestaan in de map door niet-beheerders gebruikers. Voor deze instelling is geen Azure Active Directory Premium P1-licentie vereist.|
|  <ul><li>GroupCreationAllowedGroupId<li>Type: Tekenreeks<li>Standaard: |GUID van de beveiligings groep waarvoor de leden Office 365-groepen mogen maken, zelfs wanneer EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Type: Tekenreeks<li>Standaard: |Een koppeling naar de richt lijnen voor groeps gebruik. |
|  <ul><li>ClassificationDescriptions<li>Type: Tekenreeks<li>Standaard: | Een door komma's gescheiden lijst met classificatie beschrijvingen. De waarde van ClassificationDescriptions is alleen geldig in deze indeling:<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>waarbij classificatie overeenkomt met de teken reeksen in de ClassificationList.|
|  <ul><li>DefaultClassification<li>Type: Tekenreeks<li>Standaard: | De classificatie die moet worden gebruikt als de standaard classificatie voor een groep als er geen is opgegeven.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Type: Tekenreeks<li>Standaard: | Een teken reeks met een maximale lengte van 64 tekens die de naamgevings Conventie definieert die voor Office 365-groepen is geconfigureerd. Zie [een naamgevings beleid afdwingen voor Office 365-groepen](groups-naming-policy.md)voor meer informatie. |
| <ul><li>CustomBlockedWordsList<li>Type: Tekenreeks<li>Standaard: | Een door komma's gescheiden teken reeks van zinsdelen die gebruikers niet mogen gebruiken in groeps namen of aliassen. Zie [een naamgevings beleid afdwingen voor Office 365-groepen](groups-naming-policy.md)voor meer informatie. |
| <ul><li>EnableMSStandardBlockedWords<li>Type: Boolean-waarde<li>Prijs Terecht | Niet gebruiken
|  <ul><li>AllowGuestsToBeGroupOwner<li>Type: Boolean-waarde<li>Prijs False | Een Booleaanse waarde die aangeeft of een gast gebruiker een eigenaar van groepen kan zijn. |
|  <ul><li>AllowGuestsToAccessGroups<li>Type: Boolean-waarde<li>Prijs Waar | Een Booleaanse waarde die aangeeft of een gast gebruiker toegang kan hebben tot de inhoud van de Office 365-groep.  Voor deze instelling is geen Azure Active Directory Premium P1-licentie vereist.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Type: Tekenreeks<li>Standaard: | De URL van een koppeling naar de richt lijnen voor gast gebruik. |
|  <ul><li>AllowAddGuests<li>Type: Boolean-waarde<li>Prijs Waar | Een Booleaanse waarde die aangeeft of gasten aan deze map mogen worden toegevoegd.|
|  <ul><li>ClassificationList<li>Type: Tekenreeks<li>Standaard: |Een door komma's gescheiden lijst met geldige classificatie waarden die kunnen worden toegepast op Office 365-groepen. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Voorbeeld: Gast beleid voor groepen op mapniveau configureren
1. Alle instellings sjablonen ophalen:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Als u gast beleid wilt instellen voor groepen op het mapniveau, hebt u Group. Unified Temp late nodig
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Maak vervolgens een nieuw instellingen object op basis van deze sjabloon:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Werk de instelling AllowAddGuests vervolgens bij
   ```powershell
   $Setting["AllowAddGuests"] = $False
   ```  
5. Pas vervolgens de instelling toe:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. U kunt de waarden lezen met behulp van:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Instellingen lezen op mapniveau

Als u de naam weet van de instelling die u wilt ophalen, kunt u de onderstaande cmdlet gebruiken om de huidige instellingen waarde op te halen. In dit voor beeld wordt de waarde opgehaald voor een instelling met de naam ' UsageGuidelinesUrl '. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Met deze stappen leest u de instellingen op mapniveau, die van toepassing zijn op alle Office-groepen in de Directory.

1. Alle bestaande Directory-instellingen lezen:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Met deze cmdlet wordt een lijst met alle Directory-instellingen geretourneerd:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Alle instellingen voor een specifieke groep lezen:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. De waarden van alle Directory-instellingen van een specifiek Directory-instellingen object lezen met behulp van de instellingen-ID GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Deze cmdlet retourneert de namen en waarden in dit instellingen object voor deze specifieke groep:
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
   AllowAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Instellingen op mapniveau verwijderen
Met deze stap worden de instellingen op mapniveau verwijderd, die van toepassing zijn op alle Office-groepen in de Directory.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Instellingen voor een specifieke groep maken

1. Zoek naar de sjabloon instellingen met de naam ' groups. Unified. Guest '
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
2. Ophalen van het sjabloon object voor de groepen. Unified. Guest-sjabloon:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Maak een nieuw instellingen object op basis van de sjabloon:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Stel de instelling in op de vereiste waarde:
   ```powershell
   $SettingCopy["AllowAddGuests"]=$False
   ```
5. Haal de ID op van de groep waarop u deze instelling wilt Toep assen:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Maak de nieuwe instelling voor de vereiste groep in de map:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Voer de volgende opdracht uit om de instellingen te controleren:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Instellingen voor een specifieke groep bijwerken
1. De ID ophalen van de groep waarvan u de instellingen wilt bijwerken:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. De instelling van de groep ophalen:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Werk de instelling van de groep zo nodig bij, bijvoorbeeld
   ```powershell
   $Setting["AllowAddGuests"] = $True
   ```
4. Vervolgens krijgt u de ID van de instelling voor deze specifieke groep:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   U ontvangt een antwoord dat er ongeveer als volgt uitziet:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Vervolgens kunt u de nieuwe waarde voor deze instelling instellen:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. U kunt de waarde van de instelling lezen om er zeker van te zijn dat deze correct is bijgewerkt:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Naslag informatie over de cmdlet-syntaxis
U kunt meer Azure Active Directory Power shell-documentatie vinden op [Azure Active Directory-cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Meer lezen

* [Managing access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Toegang tot resources beheren met Azure Active Directory-groepen)
* [Uw on-premises identiteiten integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
