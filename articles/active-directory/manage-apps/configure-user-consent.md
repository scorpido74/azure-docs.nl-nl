---
title: Configureren hoe eindgebruikers instemmen met toepassingen met Azure AD
description: Meer informatie over hoe u beheren hoe en wanneer gebruikers kunnen instemmen met toepassingen die toegang hebben tot de gegevens van uw organisatie.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443396"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configureren hoe eindgebruikers toestemming geven voor toepassingen

Toepassingen kunnen worden geïntegreerd met het Microsoft Identity-platform, zodat gebruikers zich kunnen aanmelden met hun werk- of schoolaccount in Azure Active Directory (Azure AD) en toegang hebben tot de gegevens van uw organisatie om uitgebreide gegevensgestuurde ervaringen te bieden. Verschillende machtigingen geven de toepassing een ander niveau van toegang tot de gegevens van uw gebruikers en uw organisatie.

Standaard kunnen gebruikers toestemming geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie, hoewel alleen voor sommige machtigingen. Een gebruiker kan bijvoorbeeld standaard toestemming geven om een app toegang te geven tot zijn eigen postvak of de Teams-gesprekken voor een team waarvan de gebruiker eigenaar is, maar kan niet toestemming geven om een app zonder toezicht toegang te geven om alle SharePoint-sites in uw organisatie te lezen en te schrijven. Hoewel gebruikers zelf toestemming kunnen geven, kunnen gebruikers eenvoudig nuttige toepassingen aanschaffen die integreren met Microsoft 365, Azure en andere services, maar het kan een risico vormen als ze niet zorgvuldig worden gebruikt en gecontroleerd.

Microsoft raadt aan toekomstige toestemmingsbewerkingen voor gebruikers uit te schakelen om uw oppervlakte te verkleinen en dit risico te beperken. Als de toestemming van de gebruiker is uitgeschakeld, worden eerdere toestemmingssubsidies nog steeds gehonoreerd, maar moeten alle toekomstige toestemmingsbewerkingen worden uitgevoerd door een beheerder. Toestemming voor beheerders voor de hele tenant kan door gebruikers worden aangevraagd via een geïntegreerde [ondersteuningsaanvraagworkflow](configure-admin-consent-workflow.md) voor beheerders of via uw eigen ondersteuningsprocessen. Zie [Vijf stappen om uw identiteitsinfrastructuur te beveiligen](../../security/fundamentals/steps-secure-identity.md) voor meer informatie.

## <a name="configure-user-consent-to-applications"></a>Gebruikerstoestemming configureren voor toepassingen
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Gebruikerstoestemming uitschakelen of inschakelen vanuit de Azure-portal

U de Azure-portal gebruiken om de mogelijkheid van gebruikers uit te schakelen of in staat te stellen toestemming te geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selecteer **Azure Active Directory**en vervolgens **Enterprise-toepassingen**en **vervolgens Gebruikersinstellingen**.
3. Toestemming van gebruikers in- of uitschakelen met het label Besturingselement Gebruikers kunnen namens hen **toestemming geven voor apps die toegang krijgen tot bedrijfsgegevens.**
4. (Optioneel) Configureer [de werkstroom voor beheerderstoestemmingsverzoeken](configure-admin-consent-workflow.md) om ervoor te zorgen dat gebruikers die geen toestemming mogen geven voor een app, goedkeuring kunnen aanvragen.

> [!TIP]
> Gebruikers toestemming geven om een beoordeling van een toepassing van een beheerder aan te vragen waar de gebruiker geen toestemming voor mag geven (bijvoorbeeld omdat de toestemming van de gebruiker is uitgeschakeld of omdat de toepassing machtigingen aanvraagt die de gebruiker niet mag verlenen), u overwegen de werkstroom voor [beheerderstoestemming te configureren.](configure-admin-consent-workflow.md)

### <a name="disable-or-enable-user-consent-using-powershell"></a>Toestemming van de gebruiker uitschakelen of inschakelen met PowerShell

U de Azure AD PowerShell v1-module[(MSOnline)](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)gebruiken om de mogelijkheid van gebruikers om toestemming te geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie in- of uitschakelen.

1. Meld u aan bij uw organisatie door deze cmdlet uit te voeren:

    ```powershell
    Connect-MsolService
    ```

2. Controleer of toestemming van de gebruiker is ingeschakeld door deze cmdlet uit te voeren:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Toestemming van de gebruiker in- of uitschakelen. Voer bijvoorbeeld de toestemming van de gebruiker uit om toestemming van de gebruiker uit te schakelen:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Toestemming voor groepseigenaar configureren voor apps die toegang hebben tot groepsgegevens

> [!IMPORTANT]
> De volgende informatie is voor een aankomende functie waarmee groepseigenaren toegang kunnen verlenen tot de gegevens van hun groepen. Wanneer deze mogelijkheid wordt vrijgegeven, wordt deze standaard ingeschakeld. Hoewel deze functie nog niet op grote schaal wordt vrijgegeven, u deze instructies gebruiken om de mogelijkheid uit te schakelen voordat de release ervan wordt vrijgegeven.

Groepseigenaren kunnen toepassingen (bijvoorbeeld toepassingen die door externe leveranciers zijn gepubliceerd) autoriseren om toegang te krijgen tot de gegevens van uw organisatie die aan een groep zijn gekoppeld. Een teameigenaar (die eigenaar is van de Office 365-groep voor het team) kan bijvoorbeeld toestaan dat een app alle Teams-berichten in het team kan lezen of het basisprofiel van de leden van een groep weergeeft.

> [!NOTE]
> Onafhankelijk van deze instelling mag een groepseigenaar altijd andere gebruikers of apps rechtstreeks als groepseigenaar toevoegen.

### <a name="configure-group-owner-consent-using-powershell"></a>Toestemming voor groepseigenaar configureren met PowerShell

U de Azure AD PowerShell Preview-module[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)gebruiken om de mogelijkheid van groepseigenaren om toestemming te geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie in of uitschakelen voor de groepen die ze bezitten.

1. Zorg ervoor dat u de [AzureADPreview-module](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) gebruikt (deze stap is belangrijk als u zowel de [AzureAD-module](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) als de [AzureADPreview-module](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) hebt geïnstalleerd).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Maak verbinding met Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Haal de huidige waarde op voor de *mapinstellingen van het toestemmingsbeleid* in uw tenant. Dit vereist controle of de directory-instellingen voor deze functie zijn gemaakt en zo niet, met behulp van de waarden van de bijbehorende directory-instellingen sjabloon.

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

4. Begrijp de instellingswaarden. Er zijn twee instellingenwaarden die bepalen welke gebruikers een app toegang kunnen geven tot de gegevens van hun groep:

    | Instelling       | Type         | Beschrijving  |
    | ------------- | ------------ | ------------ |
    | _Groepsspecifieke toestemming inschakelen_   | Booleaans |  Vlag die aangeeft of eigenaren van groepen groepsspecifieke machtigingen mogen verlenen. |
    | _Groepsspecifieke toestemming beperkentot ledenvangroupid_ | GUID | Als _EnableGroupSpecificConsent_ is ingesteld op 'True' en deze waarde is ingesteld op de object-id van een groep, worden leden van de geïdentificeerde groep gemachtigd groepsspecifieke machtigingen te verlenen aan de groepen waarvan ze eigenaar zijn. |

5. Waarden voor het bijwerken van instellingen voor de gewenste configuratie:

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

[De werkstroom voor beheerderstoestemming configureren](configure-admin-consent-workflow.md)

[Toestemming voor een tenantbeheerder verlenen voor een toepassing](grant-admin-consent.md)

[Machtigingen en toestemming in het Microsoft-identiteitsplatform](../develop/active-directory-v2-scopes.md)

[Azure AD op StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
