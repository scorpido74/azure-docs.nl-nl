---
title: Microsoft Graph-API voor Azure Active Directory Identity Protection
description: Meer informatie over het opvragen van Microsoft Graph risico detecties en bijbehorende informatie uit Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15b9bae1bd901325efdefeaa4db53df2d6b42b44
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275890"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph

Microsoft Graph is het micro soft Unified API-eind punt en de start van [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) -api's. Er zijn vier Api's die informatie over Risk ante gebruikers en aanmeldingen beschikbaar maken. Met de eerste API, **riskDetection**, kunt u een query uitvoeren op Microsoft Graph voor een lijst van zowel de gebruiker als de aanmelding en de bijbehorende informatie over de detectie. De tweede API, **riskyUsers**, stelt u in staat om Microsoft Graph te zoeken naar informatie over gebruikers identiteits beveiliging die als risico is gedetecteerd. De derde API, **signIn**, stelt u in staat om Microsoft Graph te zoeken naar informatie over Azure AD-aanmeldingen met specifieke eigenschappen die betrekking hebben op risico status, Details en niveau. Met de vierde API, **identityRiskEvents**, kunt u een query uitvoeren op Microsoft Graph voor een lijst met [risico detecties](../reports-monitoring/concept-risk-events.md) en gerelateerde informatie. De API identityRiskEvents wordt afgeschaft op 10 januari 2020; u wordt aangeraden om in plaats daarvan de **riskDetections** API te gebruiken. In dit artikel wordt u aan de slag met het maken van verbinding met de Microsoft Graph en het uitvoeren van query's op deze Api's. Zie de [Microsoft Graph-site](https://graph.microsoft.io/) of de specifieke referentie documentatie voor deze api's voor een uitgebreide inleiding, volledige documentatie en toegang tot de Graph Explorer:

* [riskDetection-API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers-API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [Aanmeldings-API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [IDENTITYRISKEVENTS API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) *wordt afgeschaft op 10 januari 2020*

## <a name="connect-to-microsoft-graph"></a>Verbinding maken met micro soft Graph

Er zijn vier stappen voor het verkrijgen van toegang tot identiteits beveiligings gegevens via Microsoft Graph:

1. Haal uw domein naam op.
2. Maak een nieuwe app-registratie. 
3. Gebruik dit geheim en enkele andere informatie over de verificatie bij Microsoft Graph, waar u een verificatie token ontvangt. 
4. Gebruik dit token om aanvragen voor het API-eind punt te maken en gegevens van identiteits beveiliging terug te halen.

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Beheerders bevoegdheden voor het maken van de toepassing in azure AD
* De naam van het domein van de Tenant (bijvoorbeeld contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Domein naam ophalen 

1. [Meld](https://portal.azure.com) u aan bij uw Azure portal als beheerder. 
1. Klik op **Active Directory**in het navigatie deel venster aan de linkerkant. 

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/41.png)

1. Klik in de sectie **beheren** op **Eigenschappen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/42.png)

1. Kopieer uw domein naam.

## <a name="create-a-new-app-registration"></a>Maak een nieuwe app-registratie

1. Klik op de pagina **Active Directory** in de sectie **beheren** op **app-registraties**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/42.png)

1. Klik in het menu aan de bovenkant op **nieuwe toepassing registreren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/43.png)

1. Voer de volgende stappen uit op de pagina **maken** :

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/44.png)

   1. Typ in het tekstvak **naam** een naam voor de toepassing (bijvoorbeeld: Azure AD Risk Detection API-toepassing).

   1. Als **type**, selecteer **Webtoepassing en/of Web-API**.

   1. Typ `http://localhost`in het tekstvak **URL voor aanmelden** .

   1. Klik op **Create**.
1. Als u de pagina **instellingen** wilt openen, klikt u in de lijst toepassingen op uw zojuist gemaakte app-registratie. 
1. Kopieer de **toepassings-id**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Uw toepassing machtigen om de API te gebruiken

1. Klik op de pagina **instellingen** op **vereiste machtigingen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/15.png)

1. Klik op de pagina **vereiste machtigingen** in de werk balk aan de bovenkant op **toevoegen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/16.png)

1. Klik op de pagina **API-toegang toevoegen** op **een API selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/17.png)

1. Selecteer **Microsoft Graph**op de pagina **een API selecteren** en klik vervolgens op **selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/18.png)

1. Klik op de pagina **API-toegang toevoegen** op **machtigingen selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/19.png)

1. Klik op de pagina **toegang inschakelen** op **alle informatie over identiteits risico lezen**en klik vervolgens op **selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/20.png)

1. Klik op de pagina **API-toegang toevoegen** op **gereed**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/21.png)

1. Klik op de pagina **vereiste machtigingen** op **machtigingen verlenen**en klik vervolgens op **Ja**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Een toegangssleutel opvragen

1. Klik op de pagina **instellingen** op **sleutels**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/23.png)

1. Voer de volgende stappen uit op de pagina **sleutels** :

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/24.png)

   1. Typ een beschrijving in het tekstvak **sleutel beschrijving** (bijvoorbeeld *Azure AD-risico detectie*).
   1. Als **duur**, selecteert u **in 1 jaar**.
   1. Klik op **Opslaan**.
   1. Kopieer de sleutel waarde en plak deze in een veilige locatie.   
   
   > [!NOTE]
   > Als u deze sleutel kwijtraakt, gaat u terug naar deze sectie en maakt u een nieuwe sleutel. Bewaar deze sleutel een geheim: iedereen met toegang tot uw gegevens kan.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Verificatie bij Microsoft Graph en query uitvoeren op de API voor detectie van identiteits Risico's

Op dit moment hebt u het volgende nodig:

- De naam van het domein van de Tenant
- De client-ID 
- De sleutel 

Als u zich wilt verifiëren, verzendt u een post-aanvraag naar `https://login.microsoft.com` met de volgende para meters in de hoofd tekst:

- grant_type:**client_credentials**
- resource: `https://graph.microsoft.com`
- client_id: \<uw client-ID\>
- client_secret: \<uw sleutel\>

Als dit is gelukt, wordt er een verificatie token geretourneerd.  
Maak een header met de volgende para meter om de API aan te roepen:

```
`Authorization`="<token_type> <access_token>"
```

Bij het verifiëren vindt u het token type en toegangs token in het geretourneerde token.

Deze header verzenden als een aanvraag naar de volgende API-URL: `https://graph.microsoft.com/beta/identityRiskEvents`

Het antwoord, indien geslaagd, is een verzameling identiteits risico detecties en gekoppelde gegevens in de OData-JSON-indeling, die kan worden geparseerd en verwerkt zoals u dat wilt.

Hier volgt een voorbeeld code voor het verifiëren en aanroepen van de API met behulp van Power shell.  
Voeg gewoon uw client-ID, de geheime sleutel en het Tenant domein toe.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Query's uitvoeren op de Api's

Deze drie Api's bieden een groot aantal mogelijkheden om informatie op te halen over Risk ante gebruikers en aanmeldingen in uw organisatie. Hieronder vindt u enkele veelvoorkomende use cases voor deze Api's en de bijbehorende voorbeeld aanvragen. U kunt deze query's uitvoeren met behulp van de voorbeeld code hierboven of met [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Alle offline-risico detecties ophalen (riskDetection API)

Met het beleid voor aanmeldings Risico's voor identiteits beveiliging kunt u voor waarden Toep assen wanneer er in realtime een risico wordt gedetecteerd. Maar hoe zit het met detecties die offline zijn gedetecteerd? Als u wilt weten welke detecties offline zijn, en dus het beleid voor aanmeldings Risico's niet hebben geactiveerd, kunt u een query uitvoeren op de riskDetection-API.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Alle gebruikers ophalen die een MFA-Challenge hebben door gegeven die zijn geactiveerd door het beleid voor Risk ante aanmeldingen (riskyUsers-API)

Om inzicht te krijgen in het op risico gebaseerd beleid voor identiteits beveiliging van uw organisatie, kunt u een query uitvoeren voor alle gebruikers die een MFA-Challenge hebben door gegeven die is geactiveerd door een beleid voor Risk ante aanmeldingen. Aan de hand van deze informatie kunt u begrijpen welke gebruikers identiteits beveiliging mogelijk onterecht is gedetecteerd op als risico en welke van uw rechtmatige gebruikers acties uitvoeren die het AI-risico in beslag brengt.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Alle Risk ante aanmeldingen ophalen voor een specifieke gebruiker (aanmeldings-API)

Wanneer u denkt dat een gebruiker mogelijk is aangetast, kunt u beter inzicht krijgen in de status van het risico door al hun Risk ante aanmeldingen op te halen. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, u hebt uw eerste oproep doorgevoerd naar Microsoft Graph!  
Nu kunt u de detectie van identiteits Risico's opvragen en de gegevens gebruiken.

Als u meer wilt weten over Microsoft Graph en hoe u toepassingen bouwt met behulp van de Graph API, raadpleegt u de [documentatie](https://docs.microsoft.com/graph/overview) en nog veel meer op de [Microsoft Graph-site](https://developer.microsoft.com/graph). 

Zie voor verwante informatie:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Typen risico detecties gedetecteerd door Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overzicht van Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Hoofdmap van Azure AD Identity Protection-Service](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
