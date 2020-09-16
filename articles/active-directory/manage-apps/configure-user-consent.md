---
title: Configureren hoe eind gebruikers toestemming geven voor toepassingen met Azure AD
description: Meer informatie over hoe u kunt beheren hoe en wanneer gebruikers toestemming kunnen geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 433ff5498baeb4c31473e43fc4a5d24f4ba9fd1c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605155"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configureren hoe eindgebruikers toestemming geven voor toepassingen

U kunt uw toepassingen integreren met het micro soft Identity-platform zodat gebruikers zich kunnen aanmelden met hun werk-of school account en toegang hebben tot de gegevens van uw organisatie om uitgebreide gegevensgestuurde ervaringen te leveren.

Voordat een toepassing toegang kan krijgen tot de gegevens van uw organisatie, moet een gebruiker de toepassings machtigingen hiervoor verlenen. Met verschillende machtigingen kunnen verschillende toegangs niveaus worden toegestaan. Standaard kunnen alle gebruikers toestemming geven voor machtigingen waarvoor geen toestemming van de beheerder nodig is. Een gebruiker kan bijvoorbeeld standaard toestemming geven om toegang te krijgen tot het postvak van een app, maar kan niet toestemming geven om de toegang tot alle bestanden in uw organisatie te lezen en te schrijven voor een app onbelemmerde.

Door gebruikers toe te staan apps toegang te verlenen tot gegevens, kunnen gebruikers eenvoudig nuttige toepassingen verkrijgen en productief zijn. In sommige gevallen kan deze configuratie echter een risico vormen als deze niet zorgvuldig wordt bewaakt en beheerd.

## <a name="user-consent-settings"></a>Instellingen voor gebruikers toestemming

Kies het toestemmings beleid dat wordt toegepast op alle gebruikers om te bepalen welke cases gebruikers toestemming kunnen geven voor toepassingen. Hier volgen de drie opties voor het toestemming beleid:

* **Gebruikers toestemming uitschakelen** : gebruikers kunnen geen machtigingen toekennen aan toepassingen. Gebruikers kunnen zich blijven aanmelden bij apps die voorheen hebben gecommuniceerd naar of die zijn gemachtigd door beheerders in hun naam, maar ze mogen niet toestemming geven voor nieuwe machtigingen of aan nieuwe apps. Alleen gebruikers die een directory-rol met de machtiging voor het verlenen van toestemming hebben verleend, kunnen toestemming geven voor nieuwe machtigingen of nieuwe apps.

* **Gebruikers kunnen toestemming geven voor apps van geverifieerde uitgevers, maar alleen voor door u geselecteerde machtigingen (preview)** : alle gebruikers kunnen alleen toestemming verlenen voor apps die zijn gepubliceerd door een [geverifieerde Uitgever](../develop/publisher-verification-overview.md) en apps die zijn geregistreerd in uw Tenant. Gebruikers kunnen alleen toestemming geven voor de machtigingen die u hebt geclassificeerd als ' lage impact ', ook wel ' laag risico ' genoemd. Wat wordt als laag risico beschouwd voor één organisatie, zoals een app die een gebruikers-e-mail adres bekijkt, kan worden beschouwd als een hoog risico voor een andere organisatie. Daarom worden de machtigingen voor ' laag risico ' ingesteld door de beheerder voor de Tenant.

  Zorg ervoor dat u de [machtigingen classificeert](#configure-permission-classifications-preview) om te selecteren welke machtigingen gebruikers toestemming mogen geven.

* **Gebruikers kunnen toestemming geven voor alle apps** : met deze optie kunnen alle gebruikers toestemming geven voor elke machtiging waarvoor geen beheerders toestemming is vereist voor elke toepassing. 

   Om het risico te verkleinen dat kwaadwillende toepassingen gebruikers toegang geven tot de gegevens van uw organisatie, wordt u aangeraden alleen toestemming van de gebruiker toe te staan voor toepassingen die zijn gepubliceerd door een [gecontroleerde uitgever](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Instellingen voor gebruikers toestemming configureren van de Azure Portal

Instellingen voor gebruikers toestemming configureren via de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selecteer **Azure Active Directory**  >  toestemming van**bedrijfs toepassingen**  >  **en machtigingen**  >  **instellingen voor gebruikers toestemming**.
1. Selecteer onder **toestemming van de gebruiker voor toepassingen**de instelling van de toestemming die u wilt configureren voor alle gebruikers.
1. Selecteer **Opslaan** om uw instellingen op te slaan.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Instellingen voor gebruikers toestemming":::

> [!TIP]
> U kunt [de beheer goedkeurings werk stroom inschakelen](configure-admin-consent-workflow.md) om gebruikers toe te staan de controle en goed keuring van een beheerder aan te vragen van een toepassing waarbij de gebruiker niet toestemming mag geven, bijvoorbeeld wanneer toestemming van de gebruiker is uitgeschakeld of wanneer een toepassing machtigingen aanvraagt die de gebruiker niet mag verlenen.

### <a name="configure-user-consent-settings-using-powershell"></a>Instellingen voor gebruikers toestemming configureren met behulp van Power shell

U kunt de nieuwste Azure AD Power shell preview-module, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), gebruiken om te kiezen welk toestemmings beleid de gebruikers instemming voor toepassingen bepaalt.

* **Toestemming van de gebruiker uitschakelen** : als u de toestemming van de gebruiker wilt uitschakelen, stelt u het beleid voor toestemming in waarmee de toestemming van de gebruiker wordt geledigd

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Toestemming van de gebruiker toestaan voor apps van geverifieerde uitgevers, voor geselecteerde machtigingen (preview)** : om beperkte gebruikers toestemming alleen toe te staan voor apps van geverifieerde uitgevers en apps die zijn geregistreerd in uw Tenant, en alleen voor machtigingen die u als ' weinig impact ' hebt geclassificeerd, configureert u het ingebouwde toestemming beleid met de naam `microsoft-user-default-low` :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Vergeet niet om [machtigingen te classificeren](#configure-permission-classifications-preview) om te selecteren welke machtigingen gebruikers toestemming mogen geven.

* **Toestemming van de gebruiker voor alle apps toestaan** , zodat toestemming van de gebruiker voor alle apps is toegestaan:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Met deze optie kunnen alle gebruikers toestemming geven voor machtigingen waarvoor geen beheerders toestemming is vereist voor elke toepassing. We raden u aan om alleen toestemming van de gebruiker toe te staan voor apps van geverifieerde uitgevers.

## <a name="configure-permission-classifications-preview"></a>Machtigings classificaties configureren (preview-versie)

Met machtigings classificaties kunt u de invloed identificeren die verschillende machtigingen hebben volgens het beleid van uw organisatie en risico-evaluaties. U kunt bijvoorbeeld machtigings classificaties in toestemming beleid gebruiken om de set machtigingen te identificeren die gebruikers mogen toestemming geven.

> [!NOTE]
> Op dit moment wordt alleen de machtiging classificatie ' lage impact ' ondersteund. Alleen gedelegeerde machtigingen waarvoor geen beheerders toestemming is vereist, kunnen worden geclassificeerd als ' lage impact '.

### <a name="classify-permissions-using-the-azure-portal"></a>Machtigingen classificeren met behulp van de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Selecteer **Azure Active Directory**  >  toestemming voor**bedrijfs toepassingen**  >  **en**  >  **classificaties**voor machtigingen.
1. Kies **machtigingen toevoegen** om een andere machtiging als ' lage impact ' te classificeren. 
1. Selecteer de API en selecteer vervolgens de gedelegeerde machtiging (en).

In dit voor beeld hebben we de minimale set vereiste machtiging voor eenmalige aanmelding geclassificeerd:

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="Machtigings classificaties":::

> [!TIP]
> Voor de Microsoft Graph-API zijn de minimale machtigingen die nodig zijn voor eenvoudige enkelvoudige aanmelding `openid` ,, `profile` `User.Read` en `offline_access` . Met deze machtigingen kan een app de profiel gegevens van de aangemelde gebruiker lezen en kan deze toegang behouden, zelfs wanneer de gebruiker de app niet meer gebruikt.

### <a name="classify-permissions-using-powershell"></a>Machtigingen classificeren met Power shell

U kunt de nieuwste Azure AD Power shell preview-module, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), gebruiken om machtigingen te classificeren. Machtigings classificaties worden geconfigureerd op het **ServicePrincipal** -object van de API die de machtigingen publiceert.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>De huidige machtigings classificaties voor een API lezen:

1. Het **ServicePrincipal** -object voor de API ophalen. Hier ophalen we het ServicePrincipal-object voor de Microsoft Graph-API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Lees de gedelegeerde machtigings classificaties voor de API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Een machtiging als ' lage impact ' classificeren:

1. Het **ServicePrincipal** -object voor de API ophalen. Hier ophalen we het ServicePrincipal-object voor de Microsoft Graph-API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Zoek de gedelegeerde machtiging die u wilt classificeren:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Stel de machtigings classificatie in met behulp van de naam en ID van de machtiging:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Een gedelegeerde machtigings classificatie verwijderen:

1. Het **ServicePrincipal** -object voor de API ophalen. Hier ophalen we het ServicePrincipal-object voor de Microsoft Graph-API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Zoek de gedelegeerde machtigings classificatie die u wilt verwijderen:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. De machtigings classificatie verwijderen:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Toestemming van groeps eigenaar configureren voor apps die toegang hebben tot groeps gegevens

Groeps eigenaren kunnen toepassingen, zoals toepassingen die door leveranciers van derden worden gepubliceerd, machtigen om toegang te krijgen tot de gegevens van uw organisatie die aan een groep zijn gekoppeld. Een team eigenaar in micro soft teams kan bijvoorbeeld toestaan dat een app alle teams berichten in het team leest of het basis profiel van de leden van een groep vermeldt.

U kunt configureren welke gebruikers toestemming mogen geven om toegang te krijgen tot de gegevens van hun groepen, of u kunt deze functie uitschakelen.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Toestemming van de groeps eigenaar configureren met behulp van de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Selecteer **Azure Active Directory**  >  toestemming van**bedrijfs toepassingen**  >  **en machtigingen**  >  **instellingen voor gebruikers toestemming**.
3. Selecteer onder **toestemming van groeps eigenaar voor apps die toegang krijgen tot gegevens** de optie die u wilt inschakelen.
4. Selecteer **Opslaan** om uw instellingen op te slaan.

In dit voor beeld mogen alle groeps eigenaren toestemming geven voor apps die toegang hebben tot de gegevens van hun groepen:

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="Instellingen voor toestemming van groeps eigenaar":::

### <a name="configure-group-owner-consent-using-powershell"></a>Toestemming van groeps eigenaar configureren met behulp van Power shell

U kunt de Azure AD Power shell preview-module, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), gebruiken om de mogelijkheid van de groeps eigenaren in of uit te scha kelen, zodat toepassingen toegang krijgen tot de gegevens van uw organisatie voor de groepen waarvan ze eigenaar zijn.

1. Zorg ervoor dat u de [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) -module gebruikt. Deze stap is belang rijk als u de [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) -module en de [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) -module hebt geïnstalleerd.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Verbinding maken met Azure AD Power shell.

   ```powershell
   Connect-AzureAD
   ```

1. Haal de huidige waarde op voor de instellingen van de map met instellingen voor het **toestemming beleid** in uw Tenant. Hiervoor moet worden gecontroleerd of de Directory-instellingen voor deze functie zijn gemaakt en als dat niet het geval is, gebruikt u de waarden uit de bijbehorende sjabloon voor Directory-instellingen.

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

1. Meer informatie over de instellings waarden. Er zijn twee instellingen waarden die bepalen welke gebruikers een app kunnen gebruiken om toegang te krijgen tot de gegevens van de groep:

    | Instelling       | Type         | Beschrijving  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleaans | Vlag waarmee wordt aangegeven of groeps eigenaren specifieke machtigingen mogen verlenen. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Als _EnableGroupSpecificConsent_ is ingesteld op ' True ' en deze waarde is ingesteld op de object-id van een groep, worden leden van de geïdentificeerde groep gemachtigd om groeps-specifieke machtigingen te verlenen aan de groepen waarvan ze eigenaar zijn. |

1. Update-instellingen waarden voor de gewenste configuratie:

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

1. Sla uw instellingen op.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Toestemming op basis van risico configuratie configureren

Met behulp van op risico gebaseerde stapsgewijze toestemming kan de gebruikers bloot stellen aan schadelijke apps die [illegale toestemming aanvragen](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)doen. Als micro soft een aanvraag voor een Risk ante toestemming van de eind gebruiker detecteert, heeft de aanvraag een ' stapsgewijs ' door gegeven aan de beheerder. Deze mogelijkheid is standaard ingeschakeld, maar dit resulteert alleen in een wijziging in het gedrag wanneer toestemming van de eind gebruiker is ingeschakeld.

Wanneer een Risk ante toestemming aanvraag wordt gedetecteerd, wordt in de toestemming prompt een bericht weer gegeven waarin wordt aangegeven dat de goed keuring van de beheerder nodig is. Als de [aanvraag werk stroom beheerder toestemming](configure-admin-consent-workflow.md) is ingeschakeld, kan de gebruiker de aanvraag naar een beheerder verzenden voor verdere controle, rechtstreeks vanuit de toestemming prompt. Als de functie niet is ingeschakeld, wordt het volgende bericht weer gegeven:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; heeft toestemming nodig om toegang te krijgen tot bronnen in uw organisatie die alleen door een beheerder kunnen worden verleend. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.

In dit geval wordt er ook een controle gebeurtenis vastgelegd met de categorie ' ApplicationManagement ', het activiteitstype ' instemming met de toepassing ' en de status van ' Risk ante toepassing gedetecteerd '.

> [!IMPORTANT]
> Beheerders moeten [alle toestemming aanvragen zorgvuldig evalueren](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) voordat ze een aanvraag goed keuren, met name wanneer micro soft risico heeft gedetecteerd.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Op risico gebaseerde Step-up van de stap uitschakelen of opnieuw inschakelen met behulp van Power shell

U kunt de Azure AD Power shell preview-module, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true), gebruiken om de stap-tot-toestemming van de beheerder uit te scha kelen in gevallen waarin micro soft risico detecteert of het opnieuw inschakelt als dit eerder was uitgeschakeld.

U kunt dit doen met behulp van dezelfde stappen als hierboven voor het configureren van de toestemming van de [groeps eigenaar met behulp van Power shell](#configure-group-owner-consent-using-powershell), maar door een andere waarde voor de instellingen te vervangen. Er zijn drie verschillen in stappen: 

1. Meer informatie over de instellings waarden voor de stap op basis van risico gerichte toestemming:

    | Instelling       | Type         | Beschrijving  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Booleaans |  Markering die aangeeft of de gebruikers toestemming wordt geblokkeerd wanneer een Risk ante aanvraag wordt gedetecteerd. |

1. Vervang de volgende waarde in stap 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Vervang een van de volgende opties in stap 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemming aanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](../develop/active-directory-v2-scopes.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD op stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
