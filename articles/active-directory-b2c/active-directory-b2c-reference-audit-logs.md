---
title: Voor beelden van controle logboeken en definities in Azure Active Directory B2C
description: Hand leiding en voor beelden voor het openen van de Azure AD B2C controle Logboeken.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b1070314767f280ec9d15390dc838fa80b5508e2
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643614"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Toegang tot Azure AD B2C controle logboeken

Azure Active Directory B2C (Azure AD B2C) verzendt audit logboeken met activiteit gegevens over B2C-resources, tokens die zijn uitgegeven en beheerders toegang. Dit artikel bevat een kort overzicht van de informatie die beschikbaar is in audit logs en instructies over het openen van deze gegevens voor uw Azure AD B2C-Tenant.

Audit logboek gebeurtenissen worden alleen **zeven dagen**bewaard. Plan uw logboeken te downloaden en op te slaan met een van de hieronder vermelde methoden als u een langere Bewaar periode nodig hebt.

> [!NOTE]
> U kunt geen gebruikers aanmeldingen zien voor afzonderlijke Azure AD B2C-toepassingen in het gedeelte **gebruikers** van de **Azure Active Directory** -of **Azure AD B2C** pagina's in de Azure Portal. De aanmeldings gebeurtenissen bevatten de gebruikers activiteit, maar kunnen niet worden gekoppeld aan de B2C-toepassing waarbij de gebruiker zich heeft aangemeld. U moet de audit logboeken voor gebruiken, zoals verderop in dit artikel wordt uitgelegd.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Overzicht van de activiteiten die beschikbaar zijn in de categorie B2C van audit logboeken

De categorie **B2C** in audit Logboeken bevat de volgende soorten activiteiten:

|Type activiteit |Beschrijving  |
|---------|---------|
|Autorisatie |Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-bronnen (bijvoorbeeld een beheerder die toegang heeft tot een lijst met B2C-beleid).         |
|Directory |Activiteiten gerelateerd aan adreslijst kenmerken die zijn opgehaald wanneer een beheerder zich aanmeldt met behulp van de Azure Portal. |
|Toepassing | Maak, lees-, update-en delete-bewerkingen (ruw) in B2C-toepassingen. |
|Sleutel |RUWE bewerkingen op sleutels die zijn opgeslagen in een B2C-sleutel container. |
|Resource |RUWE bewerkingen op B2C-resources. Bijvoorbeeld beleid en id-providers.
|Authentication |Validatie van gebruikers referenties en het uitgeven van tokens.|

Raadpleeg de categorie **basis directory** voor een ruwe activiteiten voor gebruikers objecten.

## <a name="example-activity"></a>Voorbeeld activiteit

In dit voor beeld van een afbeelding van de Azure Portal worden de gegevens weer gegeven die zijn vastgelegd wanneer een gebruiker zich aanmeldt met een externe ID-provider, in dit geval Facebook:

![Voor beeld van de pagina met details van de controle logboek activiteit in Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Het deel venster activity Details bevat de volgende relevante informatie:

|Sectie|Veld|Beschrijving|
|-------|-----|-----------|
| Activiteit | Naam | Welke activiteit heeft plaatsgevonden. U kunt bijvoorbeeld *een Id_token uitgeven aan de toepassing*, waardoor de werkelijke gebruikers aanmelding wordt beëindigd. |
| Gestart door (actor) | Id | De **object-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. Deze id is niet zichtbaar in de Azure Portal, maar is toegankelijk via de Microsoft Graph-API. |
| Gestart door (actor) | SPN | De **toepassings-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. |
| Doel (en) | Id | De **object-id** van de gebruiker die zich aanmeldt. |
| Aanvullende details | tenantId | De **Tenant-id** van de Azure AD B2C Tenant. |
| Aanvullende details | policyId | De **beleids-id** van de gebruikers stroom (het beleid) dat wordt gebruikt voor het ondertekenen van de gebruiker in. |
| Aanvullende details | applicationId | De **toepassings-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Audit logboeken weer geven in de Azure Portal

De Azure Portal biedt toegang tot de audit logboek gebeurtenissen in uw Azure AD B2C-Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Ga naar de map die uw Azure AD B2C-Tenant bevat en blader vervolgens naar **Azure AD B2C**.
1. Selecteer onder **activiteiten** in het linkermenu **audit logboeken**.

Er wordt een lijst weer gegeven met activiteiten gebeurtenissen die zijn vastgelegd in de afgelopen zeven dagen.

![Voorbeeld filter met twee activiteiten gebeurtenissen in Azure Portal](media/active-directory-b2c-reference-audit-logs/audit-logs-example-filter.png)

Er zijn verschillende filter opties beschikbaar, waaronder:

* **Resource type van activiteit** : filteren op de activiteitstypen die worden weer gegeven in de sectie [overzicht van activiteiten die beschikbaar zijn](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Datum** : filter het datum bereik van de weer gegeven activiteiten.

Als u een rij in de lijst selecteert, worden de gegevens van de activiteit voor de gebeurtenis weer gegeven.

Als u de lijst met activiteiten gebeurtenissen in een CSV-bestand (Comma-Separated Values) wilt downloaden, selecteert u **downloaden**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Audit logboeken ophalen met de rapportage-API van Azure AD

Audit logboeken worden gepubliceerd op dezelfde pijp lijn als andere activiteiten voor Azure Active Directory, zodat ze toegankelijk zijn via de [Azure Active Directory rapportage-API](https://docs.microsoft.com/graph/api/directoryaudit-list). Zie [aan de slag met de Azure Active Directory-rapportage-API](../active-directory/reports-monitoring/concept-reporting-api.md)voor meer informatie.

### <a name="enable-reporting-api-access"></a>Rapportage-API-toegang inschakelen

Als u toegang op basis van scripts of toepassingen wilt toestaan voor de rapportage-API van Azure AD, moet u een Azure Active Directory toepassing die in uw Azure AD B2C Tenant is geregistreerd met de volgende API-machtigingen:

* Microsoft Graph > toepassings machtigingen > AuditLog. Read. all

U kunt deze machtigingen inschakelen voor een bestaande Azure Active Directory van de toepassing in uw B2C-Tenant of een nieuw item maken dat specifiek is voor het gebruik van de functie voor het automatiseren van het controle logboek.

Voer de volgende stappen uit om een toepassing te registreren, de vereiste Microsoft Graph API-machtigingen te verlenen en vervolgens een client geheim te maken.

### <a name="register-application-in-azure-active-directory"></a>Toepassing registreren in Azure Active Directory

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API-toegangs machtigingen toewijzen

#### <a name="applicationstabapplications"></a>[Toepassingen](#tab/applications/)

1. Op de overzichts pagina van de **geregistreerde app** selecteert u **instellingen**.
1. Selecteer onder **API-toegang**de optie **vereiste machtigingen**.
1. Selecteer **toevoegen**en selecteer vervolgens **een API**.
1. Selecteer **Microsoft Graph**en **Selecteer**.
1. Selecteer **alle audit logboek gegevens lezen**onder **toepassings machtigingen**.
1. Selecteer de knop **selecteren** en selecteer vervolgens **gereed**.
1. Selecteer **machtigingen verlenen**en selecteer vervolgens **Ja**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[App-registraties (preview-versie)](#tab/app-reg-preview/)

1. Selecteer onder **beheren**de optie **API-machtigingen**.
1. Selecteer onder **geconfigureerde machtigingen** **de optie een machtiging toevoegen**.
1. Selecteer het tabblad **micro soft-api's** .
1. Selecteer **Microsoft Graph**.
1. Selecteer **Toepassingsmachtigingen**.
1. Vouw **AuditLog** uit en schakel vervolgens het selectie vakje **AuditLog. Read. all** in.
1. Selecteer **machtigingen toevoegen**. Wacht een paar minuten voordat u verdergaat met de volgende stap.
1. Selecteer **beheerder toestemming geven voor (uw Tenant naam)** .
1. Selecteer het account dat momenteel is aangemeld als de rol *globale beheerder* is toegewezen, of Meld u aan met een account in uw Azure AD B2C-Tenant waaraan de rol van *globale beheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **vernieuwen**en controleer vervolgens of ' verleend voor... ' wordt weer gegeven onder **status** voor de machtiging ' *AuditLog. Read. all* '. Het kan enkele minuten duren voordat de machtigingen zijn door gegeven.

* * *

### <a name="create-client-secret"></a>Client geheim maken

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

U hebt nu een toepassing met de vereiste API-toegang, een toepassings-ID en een sleutel die u kunt gebruiken in uw automatiserings scripts. Zie de sectie Power shell-script verderop in dit artikel voor een voor beeld van hoe u activiteiten gebeurtenissen kunt ophalen met een script.

### <a name="access-the-api"></a>Toegang tot de API

Als u Azure AD B2C controle logboek gebeurtenissen via de API wilt downloaden, filtert u de logboeken op de `B2C` categorie. Als u wilt filteren op categorie, gebruikt u de para meter `filter` query reeks wanneer u het Azure AD Reporting API-eind punt aanroept.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-script

In het volgende Power shell-script ziet u een voor beeld van het uitvoeren van een query op de Azure AD-rapportage-API. Na het uitvoeren van een query op de API, worden de vastgelegde gebeurtenissen in de standaard uitvoer afgedrukt en wordt de JSON-uitvoer naar een bestand geschreven.

U kunt dit script proberen in de [Azure Cloud shell](../cloud-shell/overview.md). Zorg ervoor dat u deze update bijwerkt met de toepassings-ID, het client geheim en de naam van uw Azure AD B2C-Tenant.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Hier volgt een voor beeld van de JSON-weer gave van de gebeurtenis activiteit die eerder in dit artikel is weer gegeven:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

U kunt andere beheer taken automatiseren, bijvoorbeeld [gebruikers beheren met .net](active-directory-b2c-devquickstarts-graph-dotnet.md).
