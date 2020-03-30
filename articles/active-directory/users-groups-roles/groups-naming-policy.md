---
title: Groepsnaamgevingsbeleid afdwingen in Azure Active Directory | Microsoft Documenten
description: Naamgevingsbeleid instellen voor Office 365-groepen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141e83e21db18f21468113fd9927c2bdd2ed176d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497872"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Een naamgevingsbeleid afdwingen voor Office 365-groepen in Azure Active Directory

Als u consistente naamgevingsconventies wilt afdwingen voor Office 365-groepen die door uw gebruikers zijn gemaakt of bewerkt, stelt u een groepsnaamgevingsbeleid in voor uw tenants in Azure Active Directory (Azure AD). U bijvoorbeeld het naamgevingsbeleid gebruiken om de functie van een groep, lidmaatschap, geografische regio of de groep te communiceren. U het naamgevingsbeleid ook gebruiken om groepen in het adresboek te categoriseren. U het beleid gebruiken om te voorkomen dat specifieke woorden worden gebruikt in groepsnamen en aliassen.

> [!IMPORTANT]
> Voor het gebruik van Azure AD-naamgevingsbeleid voor Office 365-groepen moet u een Azure Active Directory Premium P1-licentie of Azure AD Basic EDU-licentie toewijzen voor elke unieke gebruiker die lid is van een of meer Office 365-groepen.

Het naamgevingsbeleid wordt toegepast op het maken of bewerken van groepen die zijn gemaakt voor workloads (bijvoorbeeld Outlook, Microsoft Teams, SharePoint, Exchange of Planner). Het wordt toegepast op zowel de groepsnaam als de groepsalias. Als u uw naamgevingsbeleid inAzure AD instelt en u een bestaand beleid voor de naamgeving van Exchange-groepen hebt, wordt het Azure AD-naamgevingsbeleid afgedwongen in uw organisatie.

Wanneer groepsnaamgevingsbeleid is geconfigureerd, wordt het beleid toegepast op nieuwe Office 365-groepen die door eindgebruikers zijn gemaakt. Naamgevingsbeleid is niet van toepassing op bepaalde directoryrollen, zoals globale beheerder of gebruikersbeheerder (zie hieronder voor de volledige lijst met rollen die zijn vrijgesteld van groepsnaamgevingsbeleid). Voor bestaande Office 365-groepen is het beleid niet onmiddellijk van toepassing op het moment van configuratie. Zodra de groepseigenaar de groepsnaam voor deze groepen heeft bewerkt, wordt het naamgevingsbeleid afgedwongen.

## <a name="naming-policy-features"></a>Naamgevingskenmerken

U het naamgevingsbeleid voor groepen op twee verschillende manieren afdwingen:

- **Naamgevingsbeleid voor voorvoegsel** U voorvoegsels of achtervoegsels definiëren die vervolgens automatisch worden toegevoegd om een naamgevingsconventie op\_\_uw\_groepen af\_te\_ dwingen (bijvoorbeeld \_in de groepsnaam GRP JAPAN My Group Engineering, GRP JAPAN is het voorvoegsel en Engineering is het achtervoegsel). 

- **Aangepaste geblokkeerde woorden** U een reeks geblokkeerde woorden uploaden die specifiek zijn voor uw organisatie en die moeten worden geblokkeerd in groepen die door gebruikers zijn gemaakt (bijvoorbeeld 'CEO, Salarisadministratie, HR').

### <a name="prefix-suffix-naming-policy"></a>Naamgevingsbeleid voor voorvoegsel

De algemene structuur van de naamgevingsconventie is 'Prefix[GroupName]Suffix'. Hoewel u meerdere voorvoegsels en achtervoegsels definiëren, u slechts één instantie van de [GroupName] in de instelling hebben. De voorvoegsels of achtervoegsels kunnen vaste tekenreeksen \[of\] gebruikerskenmerken zijn, zoals afdeling, die worden vervangen op basis van de gebruiker die de groep maakt. Het totale toegestane aantal tekens voor uw voorvoegsel en achtervoegseltekenreeksen samen is 53 tekens. 

Voorvoegsels en achtervoegsels kunnen speciale tekens bevatten die worden ondersteund in groepsnaam en groepsalias. Tekens in het voorvoegsel of achtervoegsel die niet worden ondersteund in de groepsalias, worden nog steeds toegepast in de groepsnaam, maar verwijderd uit de groepsalias. Vanwege deze beperking kunnen de voorvoegsels en achtervoegsels die op de groepsnaam worden toegepast, afwijken van de voorvoegsels die op de groepsalias worden toegepast. 

#### <a name="fixed-strings"></a>Vaste tekenreeksen

U tekenreeksen gebruiken om het gemakkelijker te maken om groepen te scannen en te differentiëren in de algemene adreslijst en in de linkernavigatiekoppelingen van groepsworkloads. Enkele van de veelvoorkomende voorvoegsels zijn\_trefwoorden\#zoals 'Grp Name', 'Name', 'Name'\_

#### <a name="user-attributes"></a>Gebruikerskenmerken

U kenmerken gebruiken waarmee u en uw gebruikers kunnen bepalen voor welke afdeling, kantoor of geografische regio waarvoor de groep is gemaakt. Als u bijvoorbeeld uw naamgevingsbeleid definieert als `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`, en `User’s department = Engineering`, dan kan een afgedwongen groepsnaam 'GRP My Group Engineering' zijn. Ondersteunde Azure AD-kenmerken \[\]zijn \[\]Afdeling \[\], \[Bedrijf\], \[Office\], \[\]StateOrProvince , CountryOrRegion , Title . Niet-ondersteunde gebruikerskenmerken worden behandeld als vaste tekenreeksen; bijvoorbeeld "\[postcode\]". Extensiekenmerken en aangepaste kenmerken worden niet ondersteund.

We raden u aan kenmerken te gebruiken waarop waarden zijn ingevuld voor alle gebruikers in uw organisatie en die geen kenmerken met lange waarden gebruiken.

### <a name="custom-blocked-words"></a>Aangepaste geblokkeerde woorden

Een geblokkeerde woordenlijst is een door komma's gescheiden lijst met zinnen die moeten worden geblokkeerd in groepsnamen en aliassen. Er worden geen zoekopdrachten met subtekenreeksen uitgevoerd. Een exacte overeenkomst tussen de groepsnaam en een of meer van de aangepaste geblokkeerde woorden is vereist om een fout te veroorzaken. Subtekenreekszoekopdrachten worden niet uitgevoerd, zodat gebruikers veelvoorkomende woorden als 'Klasse' kunnen gebruiken, zelfs als 'lass' een geblokkeerd woord is.

Regels voor geblokkeerde woordenlijst:

- Geblokkeerde woorden zijn niet hoofdlettergevoelig.
- Wanneer een gebruiker een geblokkeerd woord invoert als onderdeel van een groepsnaam, ziet hij of zij een foutbericht met het geblokkeerde woord.
- Er zijn geen tekenbeperkingen voor geblokkeerde woorden.
- Er is een bovengrens van 5000 zinnen die kunnen worden geconfigureerd in de lijst met geblokkeerde woorden. 

### <a name="roles-and-permissions"></a>Rollen en machtigingen

Om het naamgevingsbeleid te configureren, is een van de folowingrollen vereist:
- Globale beheerder
- Groepsbeheerder
- Gebruikersbeheerder

Geselecteerde beheerders kunnen worden vrijgesteld van deze beleidsregels voor alle groepsworkloads en eindpunten, zodat ze groepen kunnen maken met geblokkeerde woorden en met hun eigen naamgevingsconventies. Hieronder volgt de lijst met beheerdersrollen die zijn vrijgesteld van het groepsnaamgevingsbeleid.

- Globale beheerder
- Ondersteuning voor Partner Tier 1
- Ondersteuning voor Partner Tier 2
- Gebruikersbeheerder
- Directory schrijvers

## <a name="configure-naming-policy-in-azure-portal"></a>Naamgevingsbeleid configureren in Azure-portal

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een groepsbeheerdersaccount.
1. Selecteer **Groepen**en selecteer **vervolgens Naamgevingsbeleid** om de pagina Naamgevingsbeleid te openen.

    ![de pagina Naamgevingsbeleid openen in het beheercentrum](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Het naamgevingsbeleid voor voorvoegsel weergeven of bewerken

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Groepsnaamgevingsbeleid**.
1. U het huidige voorvoegsel- of achtervoegselnaamgevingsbeleid afzonderlijk weergeven of bewerken door de kenmerken of tekenreeksen te selecteren die u wilt afdwingen als onderdeel van het naamgevingsbeleid.
1. Als u een voorvoegsel of achtervoegsel uit de lijst wilt verwijderen, selecteert u het voorvoegsel of achtervoegsel en selecteert u **Verwijderen**. Meerdere items kunnen tegelijkertijd worden verwijderd.
1. Sla uw wijzigingen op voor het nieuwe beleid dat van kracht wordt door **Opslaan te selecteren.**

### <a name="edit-custom-blocked-words"></a>Aangepaste geblokkeerde woorden bewerken

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Geblokkeerde woorden**.

    ![lijst met geblokkeerde woorden bewerken en uploaden voor naamgevingsbeleid](./media/groups-naming-policy/blockedwords.png)

1. De huidige lijst met aangepaste geblokkeerde woorden weergeven of bewerken door **Downloaden te**selecteren.
1. Upload de nieuwe lijst met aangepaste geblokkeerde woorden door het bestandspictogram te selecteren.
1. Sla uw wijzigingen op voor het nieuwe beleid dat van kracht wordt door **Opslaan te selecteren.**

## <a name="install-powershell-cmdlets"></a>PowerShell-cmdlets installeren

Verwijder een oudere versie van Azure Active Directory PowerShell voor Graph Module voor Windows PowerShell en installeer [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell voor Graph - Release 2.0.0.137 voor openbare preview) voordat u de PowerShell-opdrachten uitvoert.

1. Open de Windows PowerShell-app als beheerder.
2. Verwijder eventuele oudere versies van AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Installeer de nieuwste versie van AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Als u wordt gevraagd om toegang te krijgen tot een niet-vertrouwde opslagplaats, voert u **Y**in. Het kan enkele minuten duren voordat de nieuwe module is geïnstalleerd.

## <a name="configure-naming-policy-in-powershell"></a>Naamgevingsbeleid configureren in PowerShell

1. Open een Windows PowerShell-venster op uw computer. Je het openen zonder verhoogde privileges.

1. Voer de volgende opdrachten uit als voorbereiding op het uitvoeren van de cmdlets.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   In het scherm **Sign in to your Account** dat verschijnt, voert u uw beheerdersaccount en wachtwoord in om verbinding te maken met uw service. Selecteer vervolgens **Aanmelden**.

1. Volg de stappen in [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](groups-settings-cmdlets.md) om groepsinstellingen voor deze tenant te maken.

### <a name="view-the-current-settings"></a>Huidige instellingen weergeven

1. Haal het huidige naamgevingsbeleid om de huidige instellingen weer te geven.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Geef de instellingen voor de huidige groep weer.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Het naamgevingsbeleid en aangepaste geblokkeerde woorden instellen

1. Stel de voor- en achtervoegsels van de groepsnaam in in Azure AD PowerShell. [GroupName] moet in de instelling worden opgenomen om de functie goed te laten werken.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Stel de aangepaste, geblokkeerde woorden in die u wilt verbieden. In het volgende voorbeeld wordt getoond hoe u uw eigen aangepaste woorden kunt toevoegen.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Sla de instellingen op voor het nieuwe beleid dat in werking treedt, zoals in het volgende voorbeeld.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Dat is alles. U hebt uw naamgevingsbeleid ingesteld en uw geblokkeerde woorden toegevoegd.

## <a name="export-or-import-custom-blocked-words"></a>Aangepaste geblokkeerde woorden exporteren of importeren

Zie het artikel [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen voor](groups-settings-cmdlets.md)meer informatie.

Hier is een voorbeeld van een PowerShell-script om meerdere geblokkeerde woorden te exporteren:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Hier is een voorbeeld PowerShell-script om meerdere geblokkeerde woorden te importeren:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Het naamgevingsbeleid verwijderen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Het naamgevingsbeleid verwijderen met Azure-portal

1. Selecteer op de pagina **Naamgevingsbeleid** de optie **Beleid verwijderen**.
1. Nadat u de verwijdering hebt bevestigd, wordt het naamgevingsbeleid verwijderd, inclusief alle naamgevingsbeleid voor voorvoegsel en aangepaste geblokkeerde woorden.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Het naamgevingsbeleid verwijderen met Azure AD PowerShell

1. Wis de voor- en achtervoegsels van de groepsnaam in Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Maak de aangepaste lijst met geblokkeerde woorden leeg.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Sla de instellingen op.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Ervaring in Office 365-apps

Nadat u een groepsnaamgevingsbeleid hebt ingesteld in Azure AD, ziet een gebruiker die een groep maakt in een Office 365-app:

- Een voorbeeld van de naam volgens uw naamgevingsbeleid (met voorvoegsels en achtervoegsels) zodra de gebruiker de groepsnaam intypt
- Als de gebruiker geblokkeerde woorden invoert, ziet hij een foutbericht zodat hij de geblokkeerde woorden kan verwijderen.

Workload | Naleving
----------- | -------------------------------
Azure Active Directory-portalen | De Azure AD-portal en de portal voor het toegangspaneel geven de naam naamgevingsbeleid weer wanneer de gebruiker een groepsnaam intypt bij het maken of bewerken van een groep. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een foutbericht met het geblokkeerde woord weergegeven, zodat de gebruiker het kan verwijderen.
Outlook Web Access (OWA) | Outlook Web Access toont de afgedwongen naam van het naamgevingsbeleid wanneer de gebruiker een groepsnaam of groepsalias typt. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt er een foutbericht weergegeven in de gebruikersinterface, samen met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
Outlook-bureaublad | Groepen die zijn gemaakt in Outlook-bureaublad, voldoen aan de instellingen voor naamgevingsbeleid. Outlook-bureaublad-app toont nog niet het voorbeeld van de afgedwongen groepsnaam en retourneert de aangepaste geblokkeerde woordfouten niet wanneer de gebruiker de groepsnaam invoert. Het naamgevingsbeleid wordt echter automatisch toegepast bij het maken of bewerken van een groep en gebruikers zien foutmeldingen als er aangepaste geblokkeerde woorden in de groepsnaam of alias staan.
Microsoft Teams | Microsoft Teams toont de groepsnaamgevingsnaam die is afgedwongen wanneer de gebruiker een teamnaam invoert. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een foutbericht weergegeven samen met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
SharePoint  |  SharePoint toont de afgedwongen naam van het naamgevingsbeleid wanneer de gebruiker een sitenaam of groepse-mailadres intypt. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een foutbericht weergegeven, samen met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
Microsoft Stream | Microsoft Stream toont de groepsnaamgevingsnaam die wordt afgedwongen wanneer de gebruiker een groepsnaam of groepse-mailalias typt. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een foutbericht weergegeven met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
Outlook iOS en Android App | Groepen die zijn gemaakt in Outlook-apps voldoen aan het geconfigureerde naamgevingsbeleid. De mobiele outlook-app in Outlook wordt nog niet weergegeven in de voorvertoning van de door het naamgevingsbeleid afgedwongen naam en geeft geen aangepaste geblokkeerde woordfouten terug wanneer de gebruiker de groepsnaam invoert. Het naamgevingsbeleid wordt echter automatisch toegepast bij het klikken op maken/bewerken en gebruikers zien foutmeldingen als er aangepaste geblokkeerde woorden in de groepsnaam of alias staan.
Mobiele app groepen | Groepen die zijn gemaakt in de mobiele app Groepen voldoen aan het naamgevingsbeleid. De mobiele app Groepen toont niet het voorbeeld van het naamgevingsbeleid en retourneert geen aangepaste geblokkeerde woordfouten wanneer de gebruiker de groepsnaam invoert. Maar het naamgevingsbeleid wordt automatisch toegepast bij het maken of bewerken van een groep en gebruikers krijgen de juiste fouten te zien als er aangepaste geblokkeerde woorden in de groepsnaam of alias staan.
Planner | Planner voldoet aan het naamgevingsbeleid. Planner toont het voorbeeld van het naamgevingsbeleid bij het invoeren van de naam van het plan. Wanneer een gebruiker een aangepast geblokkeerd woord invoert, wordt een foutbericht weergegeven bij het maken van het plan.
Dynamics 365 for Customer Engagement | Dynamics 365 voor Klantbetrokkenheid voldoet aan het naamgevingsbeleid. Dynamics 365 toont de afgedwongen naam van het naamgevingsbeleid wanneer de gebruiker een groepsnaam of groepse-mailalias typt. Wanneer de gebruiker een aangepast geblokkeerd woord invoert, wordt een foutbericht weergegeven met het geblokkeerde woord, zodat de gebruiker het kan verwijderen.
Schooldata synchronisatie (SDS) | Groepen die via SDS zijn gemaakt, voldoen aan het naamgevingsbeleid, maar het naamgevingsbeleid wordt niet automatisch toegepast. SDS-beheerders moeten de voorvoegsels en achtervoegsels toevoegen aan klassenamen waarvoor groepen moeten worden gemaakt en vervolgens moeten worden geüpload naar SDS. Het maken of bewerken van groepen zou anders mislukken.
Outlook Customer Manager (OCM) | Outlook Customer Manager voldoet aan het naamgevingsbeleid, dat automatisch wordt toegepast op de groep die is gemaakt in Outlook Customer Manager. Als een aangepast geblokkeerd woord wordt gedetecteerd, wordt het maken van groepen in OCM geblokkeerd en wordt de gebruiker geblokkeerd voor het gebruik van de OCM-app.
Classroom-app | Groepen die zijn gemaakt in de Classroom-app voldoen aan het naamgevingsbeleid, maar het naamgevingsbeleid wordt niet automatisch toegepast en het voorbeeld van het naamgevingsbeleid wordt niet weergegeven aan de gebruikers terwijl ze een groepsnaam in de klas invoeren. Gebruikers moeten de afgedwongen groepsnaam van het klaslokaal invoeren met voorvoegsels en achtervoegsels. Als dit niet het zelfde is, mislukt de bewerking voor het maken of bewerken van de klasgroep met fouten.
Power BI | Power BI-werkruimten voldoen aan het naamgevingsbeleid.    
Yammer | Wanneer een gebruiker zich met zijn Azure Active Directory-account bij Yammer heeft aangemeld of een groepsnaam bewerkt, voldoet de groepsnaam aan het naamgevingsbeleid. Dit geldt zowel voor met Office 365 verbonden groepen als voor alle andere Yammer-groepen.<br>Als er een met Office 365 verbonden groep is gemaakt voordat het naamgevingsbeleid van kracht is, volgt de groepsnaam niet automatisch het naamgevingsbeleid. Wanneer een gebruiker de groepsnaam bewerkt, wordt deze gevraagd het voorvoegsel en het achtervoegsel toe te voegen.
StaffHub (StaffHub)  | StaffHub-teams volgen het naamgevingsbeleid niet, maar de onderliggende Office 365-groep wel. De teamnaam van StaffHub past de voorvoegsels en achtervoegsels niet toe en controleert niet op aangepaste geblokkeerde woorden. Maar StaffHub past de voorvoegsels en achtervoegsels toe en verwijdert geblokkeerde woorden uit de onderliggende Office 365-groep.
Exchange PowerShell | Exchange PowerShell-cmdlets voldoen aan het naamgevingsbeleid. Gebruikers ontvangen de juiste foutmeldingen met voorgestelde voorvoegsels en achtervoegsels en voor aangepaste geblokkeerde woorden als ze het naamgevingsbeleid in de groepsnaam en groepsalias (mailNickname) niet volgen.
Azure Active Directory PowerShell-cmdlets | Azure Active Directory PowerShell-cmdlets voldoen aan het naamgevingsbeleid. Gebruikers ontvangen de juiste foutmeldingen met voorgestelde voorvoegsels en achtervoegsels en voor aangepaste geblokkeerde woorden als ze de naamgevingsconventie niet volgen in groepsnamen en groepsalias.
Exchange-beheercentrum | Exchange-beheercentrum voldoet aan het naamgevingsbeleid. Gebruikers ontvangen de juiste foutmeldingen met voorgestelde voorvoegsels en achtervoegsels en voor aangepaste geblokkeerde woorden als ze de naamgevingsconventie in de groepsnaam en groepsalias niet volgen.
Microsoft 365-beheercentrum | Microsoft 365-beheercentrum voldoet aan het naamgevingsbeleid. Wanneer een gebruiker groepsnamen maakt of bewerkt, wordt het naamgevingsbeleid automatisch toegepast en ontvangen gebruikers de juiste fouten wanneer ze aangepaste geblokkeerde woorden invoeren. Het Microsoft 365-beheercentrum toont nog geen voorbeeld van het naamgevingsbeleid en retourneert geen aangepaste geblokkeerde woordfouten wanneer de gebruiker de groepsnaam invoert.

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure AD-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Verloopbeleid voor Office 365-groepen](groups-lifecycle.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Leden van een groep beheren](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
