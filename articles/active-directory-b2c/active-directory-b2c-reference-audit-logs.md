---
title: Voor beelden van controle logboeken en definities in Azure Active Directory B2C | Microsoft Docs
description: Hand leiding en voor beelden voor het openen van de Azure AD B2C controle Logboeken.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969631"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Toegang tot Azure AD B2C controle logboeken

Azure Active Directory B2C (Azure AD B2C) verzendt audit logboeken met activiteit gegevens over B2C-resources, uitgegeven tokens en beheerders toegang. Dit artikel bevat een kort overzicht van de informatie die beschikbaar is via audit logboeken en instructies voor het openen van deze gegevens voor uw Azure AD B2C-Tenant.

> [!IMPORTANT]
> Audit logboeken worden alleen zeven dagen bewaard. Plan uw logboeken te downloaden en op te slaan met een van de hieronder vermelde methoden als u een langere Bewaar periode nodig hebt.

> [!NOTE]
> U kunt geen gebruikers aanmeldingen zien voor afzonderlijke Azure AD B2C-toepassingen in het gedeelte **gebruikers** van de **Azure Active Directory** -of **Azure AD B2C** -Blades. Bij de aanmeldingen wordt de activiteit van de gebruiker weer gegeven, maar kan niet worden gekoppeld aan de B2C-toepassing waarbij de gebruiker zich heeft aangemeld. U moet de audit logboeken voor gebruiken, zoals verderop in dit artikel wordt uitgelegd.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Overzicht van de activiteiten die beschikbaar zijn in de categorie B2C van audit logboeken
De categorie **B2C** in audit Logboeken bevat de volgende soorten activiteiten:

|Type activiteit |Description  |
|---------|---------|
|Authorization |Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-bronnen (bijvoorbeeld een beheerder die toegang heeft tot een lijst met B2C-beleid)         |
|Directory |Activiteiten met betrekking tot adreslijst kenmerken die zijn opgehaald wanneer een beheerder zich aanmeldt met de Azure Portal |
|Toepassing | RUWE bewerkingen op B2C-toepassingen |
|Sleutel |RUWE bewerkingen op sleutels die zijn opgeslagen in de sleutel container B2C |
|Resource |RUWE bewerkingen op B2C-bronnen (bijvoorbeeld beleids regels en identiteits providers)
|Authentication |Validatie van gebruikers referenties en het uitgeven van tokens|

> [!NOTE]
> Raadpleeg de categorie **basis directory** voor een ruwe activiteiten voor gebruikers objecten.

## <a name="example-activity"></a>Voorbeeld activiteit
In het onderstaande voor beeld ziet u de gegevens die zijn vastgelegd wanneer een gebruiker zich aanmeldt met een externe ID-provider:  
    ![Voor beeld van de pagina met details van de controle logboek activiteit in Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Het deel venster activity Details bevat de volgende relevante informatie:

|Section|Veld|Description|
|-------|-----|-----------|
| Activiteit | Name | Welke activiteit heeft plaatsgevonden. Bijvoorbeeld ' Geef een id_token op voor de toepassing ' (waarmee de werkelijke gebruikers aanmelding wordt beëindigd). |
| Gestart door (actor) | ObjectId | De **object-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt (deze id is niet zichtbaar in de Azure Portal, maar is wel toegankelijk via de Graph API bijvoorbeeld). |
| Gestart door (actor) | SPN-naam | De **toepassings-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. |
| Doel (en) | ObjectId | De **object-id** van de gebruiker die zich aanmeldt. |
| Aanvullende details | TenantId | De **Tenant-id** van de Azure AD B2C Tenant. |
| Aanvullende details | `PolicyId` | De **beleids-id** van de gebruikers stroom (het beleid) dat wordt gebruikt voor het ondertekenen van de gebruiker in. |
| Aanvullende details | ApplicationId | De **toepassings-id** van de B2C-toepassing waarbij de gebruiker zich aanmeldt. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Controle Logboeken openen via de Azure Portal
1. Ga naar de [Azure Portal](https://portal.azure.com). Zorg ervoor dat u zich in de B2C-adres lijst bevindt.
2. Klik op **Azure Active Directory** in de werk balk Favorieten aan de linkerkant

    ![Azure Active Directory knop gemarkeerd in het menu links in de portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Klik onder **activiteit**op **audit logboeken**

    ![De knop controle logboeken gemarkeerd in het gedeelte activiteit van het menu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. Selecteer **B2C** in de **categorie** Dropbox
3. Klik op **Toep assen**

    ![De categorie en de knop Toep assen gemarkeerd in het controle logboek filter](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

U ziet een lijst met activiteiten die zijn vastgelegd in de afgelopen zeven dagen.
- Gebruik de vervolg keuzelijst **activiteit resource type** om te filteren op de hierboven beschreven typen activiteiten
- Gebruik de vervolg keuzelijst **datum bereik** om het datum bereik van de weer gegeven activiteiten te filteren
- Als u op een specifieke rij in de lijst klikt, worden in een context afhankelijk vak aan de rechter kant aanvullende kenmerken weer gegeven die zijn gekoppeld aan de activiteit
- Klik op **downloaden** om de activiteiten te downloaden als een CSV-bestand

> [!NOTE]
> U kunt de audit logboeken ook weer geven door te navigeren naar **Azure AD B2C** in plaats van **Azure Active Directory** in de werk balk Favorieten aan de linkerkant. Klik onder **activiteiten**op **controle logboeken**, waarin u dezelfde Logboeken vindt als vergelijk bare filter mogelijkheden.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Controle Logboeken openen met de rapportage-API van Azure AD
Audit logboeken worden gepubliceerd op dezelfde pijp lijn als andere activiteiten voor Azure Active Directory, zodat ze toegankelijk zijn via de [Azure Active Directory rapportage-API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Vereisten
Als u de Azure AD Reporting API wilt verifiëren, moet u eerst een toepassing registreren. Zorg ervoor dat u de stappen in [vereisten voor toegang tot de Azure AD Reporting api's](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)volgt.

### <a name="accessing-the-api"></a>Toegang tot de API
Als u de Azure AD B2C controle logboeken wilt downloaden via de API, kunt u de logboeken filteren in de categorie **B2C** . Als u wilt filteren op categorie, gebruikt u de query teken reeks parameter bij het aanroepen van het Azure AD Reporting API-eind punt, zoals hieronder wordt weer gegeven:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell-script
Het volgende script geeft een voor beeld van het gebruik van Power shell om de rapportage-API van Azure AD op te vragen en de resultaten op te slaan als een JSON-bestand:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

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
