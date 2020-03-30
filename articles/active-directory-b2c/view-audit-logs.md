---
title: Controlelogboeken openen en controleren
titleSuffix: Azure AD B2C
description: Toegang krijgen tot Azure AD B2C-auditlogboeken programmatisch en in de Azure-portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264217"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Toegang tot Azure AD B2C-controlelogboeken

Azure Active Directory B2C (Azure AD B2C) zendt controlelogboeken uit met activiteitsgegevens over B2C-resources, uitgegeven tokens en beheerderstoegang. In dit artikel vindt u een kort overzicht van de beschikbare informatie in controlelogboeken en instructies over hoe u toegang krijgt tot deze gegevens voor uw Azure AD B2C-tenant.

Auditloggebeurtenissen worden slechts **zeven dagen**bewaard. Plan om uw logboeken te downloaden en op te slaan met behulp van een van de onderstaande methoden als u een langere bewaartermijn nodig hebt.

> [!NOTE]
> U geen aanmeldingen van gebruikers zien voor afzonderlijke Azure AD B2C-toepassingen onder de sectie **Gebruikers** van de **Azure Active Directory-** of Azure AD **B2C-pagina's** in de Azure-portal. De aanmeldingsgebeurtenissen daar tonen gebruikersactiviteit, maar kunnen niet worden gecorreleerd naar de B2C-toepassing waarop de gebruiker zich heeft aangemeld. U moet de controlelogboeken daarvoor gebruiken, zoals verder in dit artikel wordt uitgelegd.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Overzicht van activiteiten die beschikbaar zijn in de B2C-categorie van auditlogs

De **B2C-categorie** in auditlogs bevat de volgende soorten activiteiten:

|Type activiteit |Beschrijving  |
|---------|---------|
|Autorisatie |Activiteiten met betrekking tot de autorisatie van een gebruiker om toegang te krijgen tot B2C-bronnen (bijvoorbeeld een beheerder die toegang heeft tot een lijst met B2C-beleidsregels).         |
|Directory |Activiteiten met betrekking tot directorykenmerken die zijn opgehaald wanneer een beheerder zich aanmeldt met de Azure-portal. |
|Toepassing | Crud-bewerkingen maken, lezen, bijwerken en verwijderen op B2C-toepassingen. |
|Sleutel |CRUD-bewerkingen op sleutels die zijn opgeslagen in een B2C-sleutelcontainer. |
|Resource |CRUD-bewerkingen op B2C-resources. Bijvoorbeeld beleid en identiteitsproviders.
|Authentication |Validatie van gebruikersreferenties en tokenuitgifte.|

Raadpleeg de categorie **Core Directory** voor CRUD-activiteiten van gebruikersobject.

## <a name="example-activity"></a>Voorbeeldactiviteit

In deze voorbeeldafbeelding van de Azure-portal worden de gegevens weergegeven die zijn vastgelegd wanneer een gebruiker zich aanmeldt bij een externe identiteitsprovider, in dit geval Facebook:

![Voorbeeld van de pagina Activiteitsdetails van controlelogboeken in Azure-portal](./media/view-audit-logs/audit-logs-example.png)

Het deelvenster activiteitsdetails bevat de volgende relevante informatie:

|Sectie|Veld|Beschrijving|
|-------|-----|-----------|
| Activiteit | Name | Welke activiteit vond plaats. Geef bijvoorbeeld *een id_token aan de toepassing*, waarin de daadwerkelijke aanmelding van de gebruiker wordt afgesloten. |
| Geïnitieerd door (Acteur) | ObjectId | De **object-id** van de B2C-toepassing waarop de gebruiker zich aanmeldt. Deze id is niet zichtbaar in de Azure-portal, maar is toegankelijk via de Microsoft Graph API. |
| Geïnitieerd door (Acteur) | Spn | De **toepassings-id** van de B2C-toepassing waarop de gebruiker zich aanmeldt. |
| Doel(en) | ObjectId | De **object-id** van de gebruiker die zich aanmeldt. |
| Aanvullende details | TenantId | De **tenant-id** van de Azure AD B2C-tenant. |
| Aanvullende details | PolicyId | De **beleids-id** van de gebruikersstroom (beleid) die wordt gebruikt om de gebruiker aan te melden. |
| Aanvullende details | ApplicationId | De **toepassings-id** van de B2C-toepassing waarop de gebruiker zich aanmeldt. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Controlelogboeken weergeven in de Azure-portal

De Azure-portal biedt toegang tot de gebeurtenissen in het controlelogboek in uw Azure AD B2C-tenant.

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Ga over naar de map met uw Azure AD B2C-tenant en blader vervolgens naar **Azure AD B2C**.
1. Selecteer **Controlelogboeken**onder **Activiteiten** in het linkermenu .

Er wordt een lijst weergegeven met activiteitsgebeurtenissen die in de afgelopen zeven dagen zijn geregistreerd.

![Voorbeeldfilter met twee activiteitsgebeurtenissen in Azure-portal](./media/view-audit-logs/audit-logs-example-filter.png)

Er zijn verschillende filteropties beschikbaar, waaronder:

* **Type activiteitsbron** - Filter op de activiteitstypen in de tabel in het [gedeelte Overzicht van beschikbare activiteiten.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Datum** - Filter het datumbereik van de getoonde activiteiten.

Als u een rij in de lijst selecteert, worden de activiteitsgegevens voor de gebeurtenis weergegeven.

Als u de lijst met activiteitsgebeurtenissen in een CSV-bestand (comma-separated values) wilt downloaden, selecteert u **Downloaden**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Controlelogboeken verzamelen met de Azure AD-rapportage-API

Controlelogboeken worden gepubliceerd in dezelfde pijplijn als andere activiteiten voor Azure Active Directory, zodat ze toegankelijk zijn via de [Azure Active Directory-rapportage-API.](https://docs.microsoft.com/graph/api/directoryaudit-list) Zie [Aan de slag met de Azure Active Directory reporting API](../active-directory/reports-monitoring/concept-reporting-api.md)voor meer informatie.

### <a name="enable-reporting-api-access"></a>API-toegang voor rapportage inschakelen

Als u script- of toepassingsgebaseerde toegang wilt verlenen tot de Azure AD-rapportage-API, hebt u een toepassing nodig die is geregistreerd in uw Azure AD B2C-tenant met de volgende API-machtigingen. U deze machtigingen inschakelen voor een bestaande toepassingsregistratie binnen uw B2C-tenant, of een nieuwe inschakelen die specifiek is bedoeld voor gebruik met auditlogautomatisering.

* Microsoft Graph > toepassingsmachtigingen > AuditLog > AuditLog.Read.All

Volg de stappen in het volgende artikel om een toepassing te registreren met de vereiste machtigingen:

[Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)

Nadat u een toepassing hebt geregistreerd met de juiste machtigingen, raadpleegt u de sectie PowerShell-script later in dit artikel voor een voorbeeld van hoe u activiteitsgebeurtenissen met een script krijgen.

### <a name="access-the-api"></a>Toegang tot de API

Als u Azure AD B2C-controlelogboekgebeurtenissen wilt downloaden `B2C` via de API, filtert u de logboeken in de categorie. Als u wilt filteren `filter` op categorie, gebruikt u de parameter querytekenreeks wanneer u het eindpunt van de Azure AD-rapportage-API aanroept.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-script

In het volgende PowerShell-script ziet u een voorbeeld van het opvragen van de Azure AD-rapportage-API. Nadat u de API hebt opgevraagd, worden de aangemelde gebeurtenissen afgedrukt op standaarduitvoer en wordt de JSON-uitvoer naar een bestand geschreven.

U dit script uitproberen in de [Azure Cloud Shell.](overview.md) Zorg ervoor dat u deze bijwerkt met uw toepassings-id, clientgeheim en de naam van uw Azure AD B2C-tenant.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
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

Hier is de JSON-weergave van de voorbeeldactiviteitsgebeurtenis die eerder in het artikel wordt weergegeven:

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

U andere beheertaken automatiseren, bijvoorbeeld [Azure AD B2C-gebruikersaccounts beheren met Microsoft Graph.](manage-user-accounts-graph-api.md)
