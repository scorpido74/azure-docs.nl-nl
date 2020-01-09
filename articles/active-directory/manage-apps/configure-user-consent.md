---
title: Configureren hoe eind gebruikers toestemming geven voor toepassingen met Azure AD
description: Meer informatie over hoe u kunt beheren hoe en wanneer gebruikers toestemming kunnen geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443396"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configureren hoe eind gebruikers toestemming geven voor toepassingen

Toepassingen kunnen worden geïntegreerd met het micro soft Identity-platform zodat gebruikers zich kunnen aanmelden met hun werk-of school account in Azure Active Directory (Azure AD) en om toegang te krijgen tot de gegevens van uw organisatie om uitgebreide gegevensgestuurde ervaringen te leveren. Met verschillende machtigingen kan de toepassing op verschillende manieren toegang krijgen tot de gegevens van uw gebruikers en uw organisatie.

Gebruikers kunnen standaard toestemming geven aan toepassingen die toegang hebben tot de gegevens van uw organisatie, maar alleen voor bepaalde machtigingen. Een gebruiker kan bijvoorbeeld standaard toestemming verlenen om een app toegang te geven tot hun eigen postvak of met de team gesprekken voor een team dat eigenaar is van de gebruiker, maar kan niet toestemming geven om een app zonder toezicht toe te staan om alle share point-sites in uw organisatie te lezen en te schrijven. Hoewel gebruikers in staat stellen om op zichzelf toestemming te geven, kunnen ze eenvoudig nuttige toepassingen verwerven die worden geïntegreerd met Microsoft 365, Azure en andere services. het kan een risico vormen wanneer het niet wordt gebruikt en zorgvuldig wordt gecontroleerd.

Micro soft adviseert toekomstige acties voor de gebruikers toestemming uit te scha kelen om uw surface area te verminderen en dit risico te beperken. Als de toestemming van de gebruiker is uitgeschakeld, worden er nog steeds toestemming gegeven voor eerdere toestemmingen, maar moeten alle toekomstige toestemmings bewerkingen worden uitgevoerd door een beheerder. Toestemming van de beheerder voor de hele Tenant kan worden aangevraagd door gebruikers via een geïntegreerde [aanvraag werk stroom](configure-admin-consent-workflow.md) voor het beheer van toestemming of via uw eigen ondersteunings processen. Zie [vijf stappen voor het beveiligen van uw identiteits infrastructuur](../../security/fundamentals/steps-secure-identity.md) voor meer informatie.

## <a name="configure-user-consent-to-applications"></a>Gebruikers toestemming voor toepassingen configureren
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Toestemming van de gebruiker voor de Azure Portal in-of uitschakelen

U kunt de Azure Portal gebruiken om gebruikers in staat te stellen om toestemming te geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selecteer **Azure Active Directory**en vervolgens **bedrijfs toepassingen**en vervolgens **gebruikers instellingen**.
3. Toestemming van de gebruiker in-of uitschakelen met het besturings element met het label **gebruikers kan toestemming geven voor apps die namens hen toegang hebben tot Bedrijfs gegevens**.
4. Beschrijving Configureer de [aanvraag werk stroom beheerder toestemming](configure-admin-consent-workflow.md) om ervoor te zorgen dat gebruikers die geen toestemming voor een app kunnen geven, goed keuring aanvragen.

> [!TIP]
> Om gebruikers in staat te stellen om de beoordeling van een toepassing te vragen dat de gebruiker niet toestemming mag geven (bijvoorbeeld omdat de gebruiker toestemming is gegeven, of omdat de toepassing machtigingen aanvraagt die de gebruiker niet mag verlenen), kunt u overwegen [de beheerder toestemming werk stroom te configureren](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Toestemming van de gebruiker in-of uitschakelen met Power shell

U kunt de Azure AD Power shell v1-module ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) gebruiken voor het in-of uitschakelen van de mogelijkheid van gebruikers om toestemming te geven aan toepassingen die toegang hebben tot de gegevens van uw organisatie.

1. Meld u aan bij uw organisatie door deze cmdlet uit te voeren:

    ```powershell
    Connect-MsolService
    ```

2. Controleer of de toestemming van de gebruiker is ingeschakeld door deze cmdlet uit te voeren:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Toestemming van de gebruiker in-of uitschakelen. Als u bijvoorbeeld toestemming van de gebruiker wilt uitschakelen, voert u deze cmdlet uit:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Toestemming van groeps eigenaar configureren voor apps die toegang hebben tot groeps gegevens

> [!IMPORTANT]
> De volgende informatie is voor een aanstaande functie waarmee groeps eigenaren toepassingen toegang kunnen verlenen tot hun groeps gegevens. Wanneer deze mogelijkheid is uitgebracht, wordt deze standaard ingeschakeld. Hoewel deze functie nog niet algemeen is vrijgegeven, kunt u deze instructies gebruiken om de mogelijkheden van de release van tevoren uit te scha kelen.

Groeps eigenaren kunnen toepassingen (bijvoorbeeld toepassingen die door leveranciers van derden worden gepubliceerd) toestemming geven om toegang te krijgen tot de gegevens van uw organisatie die aan een groep zijn gekoppeld. Een team eigenaar (die de eigenaar is van de Office 365-groep voor het team) kan bijvoorbeeld toestaan dat een app alle teams berichten in het team leest of het basis profiel van de leden van een groep vermeldt.

> [!NOTE]
> Onafhankelijk van deze instelling mag een groeps eigenaar altijd toestaan om andere gebruikers of apps rechtstreeks als groeps eigenaren toe te voegen.

### <a name="configure-group-owner-consent-using-powershell"></a>Toestemming van groeps eigenaar configureren met behulp van Power shell

U kunt de Azure AD Power shell preview-module ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) gebruiken om de mogelijkheid van groeps eigenaren in of uit te scha kelen, zodat toepassingen toegang krijgen tot de gegevens van uw organisatie voor de groepen waarvan ze eigenaar zijn.

1. Zorg ervoor dat u de [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) -module gebruikt (deze stap is belang rijk als u zowel de module [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) als de module [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) hebt geïnstalleerd).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Verbinding maken met Azure AD Power shell.

   ```powershell
   Connect-AzureAD
   ```

3. Haal de huidige waarde op voor de instellingen van de map met instellingen voor het *toestemming beleid* in uw Tenant. Hiervoor moet worden gecontroleerd of de Directory-instellingen voor deze functie zijn gemaakt en als dat niet het geval is, gebruikt u de waarden uit de bijbehorende sjabloon voor Directory-instellingen.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Meer informatie over de instellings waarden. Er zijn twee instellingen waarden die bepalen welke gebruikers een app kunnen gebruiken om toegang te krijgen tot de gegevens van de groep:

    | Instelling       | Type         | Beschrijving  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleaans |  Vlag waarmee wordt aangegeven of groeps eigenaren specifieke machtigingen mogen verlenen. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Als _EnableGroupSpecificConsent_ is ingesteld op ' True ' en deze waarde is ingesteld op de object-id van een groep, worden leden van de geïdentificeerde groep gemachtigd om groeps-specifieke machtigingen te verlenen aan de groepen waarvan ze eigenaar zijn. |

5. Update-instellingen waarden voor de gewenste configuratie:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Instellingen opslaan.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Volgende stappen

[De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)

[Toestemming van de beheerder voor de hele Tenant verlenen aan een toepassing](grant-admin-consent.md)

[Machtigingen en toestemming in het micro soft Identity-platform](../develop/active-directory-v2-scopes.md)

[Azure AD op stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
