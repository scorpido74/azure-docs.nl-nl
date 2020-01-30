---
title: Gebruiks rapportage-API-voor beelden en-definities
titleSuffix: Azure AD B2C
description: Hand leiding en voor beelden over het verkrijgen van rapporten over Azure AD B2C Tenant gebruikers, authenticaties en multi-factor Authentication.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b4486c1459024d60b53292f070dd565d20d10c52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847730"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Toegang tot gebruiks rapporten in Azure AD B2C via de rapportage-API

Azure Active Directory B2C (Azure AD B2C) biedt verificatie op basis van de gebruiker die zich aanmeldt en Azure Multi-Factor Authentication. Er wordt verificatie gegeven voor eind gebruikers van uw toepassings familie over id-providers. Wanneer u weet hoeveel gebruikers zijn geregistreerd in de Tenant, welke providers ze hebben gebruikt om zich te registreren, en het aantal verificaties per type, kunt u vragen beantwoorden zoals:
* Hoeveel gebruikers van elk type ID-provider (bijvoorbeeld een micro soft-of LinkedIn-account) zijn in de afgelopen tien dagen geregistreerd?
* Hoeveel verificaties met behulp van Multi-Factor Authentication zijn in de afgelopen maand voltooid?
* Hoeveel aanmeldings verificaties op basis van aanmelding zijn deze maand voltooid? Per dag? Per toepassing?
* Hoe kan ik de verwachte maandelijkse kosten van mijn Azure AD B2C-Tenant activiteit schatten?

Dit artikel richt zich op rapporten die zijn gekoppeld aan facturerings activiteiten, die zijn gebaseerd op het aantal gebruikers, factureer bare aanmeldings verificaties en multi-factor Authentication.


## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u de stappen in [vereisten voor toegang tot de Azure AD Reporting-api's](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)volt ooien. Maak een toepassing, vraag een geheim aan en verleen deze toegang tot de rapporten van uw Azure AD B2C-Tenant. Hier vindt u ook voor beelden van *bash-script* -en *python-scripts* .

## <a name="powershell-script"></a>PowerShell-script
Met dit script wordt het maken van vier gebruiks rapporten gedemonstreerd met behulp van de para meter `TimeStamp` en het `ApplicationId` filter.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definities van gebruiks rapporten
* **tenantUserCount**: het aantal gebruikers in de Tenant per type ID-provider, per dag in de afgelopen 30 dagen. (Optioneel, een `TimeStamp` filter geeft gebruikers aantallen van een opgegeven datum tot de huidige datum). Het rapport bevat het volgende:
  * **TotalUserCount**: het aantal gebruikers objecten.
  * **OtherUserCount**: het aantal Azure Active Directory gebruikers (niet Azure AD B2C gebruikers).
  * **LocalUserCount**: het aantal Azure AD B2C gebruikers accounts die zijn gemaakt met referenties die lokaal zijn voor de Azure AD B2C-Tenant.

* **AlternateIdUserCount**: het aantal Azure AD B2C-gebruikers dat is geregistreerd bij externe ID-providers (bijvoorbeeld Facebook, een Microsoft-account of een andere Azure Active Directory Tenant, ook wel een `OrgId`genoemd).

* **b2cAuthenticationCountSummary**: overzicht van het dagelijkse aantal factureer bare authenticaties in de afgelopen 30 dagen, per dag en type verificatie stroom.

* **b2cAuthenticationCount**: het aantal authenticaties binnen een bepaalde periode. De standaard waarde is de laatste 30 dagen.  (Optioneel: de begin-en eind `TimeStamp` para meters definiëren een specifieke tijds periode.) De uitvoer bevat `StartTimeStamp` (vroegste datum van de activiteit voor deze Tenant) en `EndTimeStamp` (laatste update).

* **b2cMfaRequestCountSummary**: een samen vatting van het dagelijkse aantal multi-factor Authentication, per dag en type (SMS of voice).


## <a name="limitations"></a>Beperkingen
De gegevens van het aantal gebruikers worden elke 24 tot 48 uur vernieuwd. Verificaties worden meerdere keren per dag bijgewerkt. Wanneer u het `ApplicationId`-filter gebruikt, kan een leeg rapport antwoord een van de volgende oorzaken hebben:
  * De toepassings-ID bestaat niet in de Tenant. Controleer of het juist is.
  * De toepassings-ID bestaat, maar er zijn geen gegevens gevonden in de rapportage periode. Controleer de datum/tijd-para meters.


## <a name="next-steps"></a>Volgende stappen
### <a name="monthly-bill-estimates-for-azure-ad"></a>Schattingen maandelijks gefactureerd voor Azure AD
In combi natie met [de meest actuele prijzen voor Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/), kunt u dagelijks, wekelijks en maandelijks Azure-verbruik schatten.  Een schatting is vooral nuttig wanneer u wijzigingen aanbrengt in het gedrag van de Tenant die van invloed kunnen zijn op de totale kosten. U kunt de werkelijke kosten bekijken in uw [gekoppelde Azure-abonnement](billing.md).

### <a name="options-for-other-output-formats"></a>Opties voor andere uitvoer indelingen
In de volgende code ziet u voor beelden van het verzenden van uitvoer naar JSON, een lijst met naam waarden en XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
