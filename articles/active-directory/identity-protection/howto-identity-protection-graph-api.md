---
title: Microsoft Graph API voor Azure Active Directory Identity Protection
description: Informatie over het opvragen van Microsoft Graph-risicodetecties en bijbehorende informatie uit Azure Active Directory
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
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671624"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph

Microsoft Graph is het microsoft unified API-eindpunt en de thuisbasis van [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API's. Er zijn vier API's die informatie over riskante gebruikers en aanmeldingen blootleggen. Met de eerste API, **riskDetection,** u Microsoft Graph opvragen voor een lijst met zowel gebruikers- als aanmeldingsgekoppelde risicodetecties en bijbehorende informatie over de detectie. Met de tweede API, **riskyUsers,** u Microsoft Graph opvragen naar informatie over gebruikers Identiteitsbescherming gedetecteerd als risico. Met de derde API, **signIn,** u Microsoft Graph opvragen voor informatie over Azure AD-aanmeldingen met specifieke eigenschappen met betrekking tot risicostatus, detail en niveau. Met de vierde API, **identityRiskEvents,** u Microsoft Graph opvragen voor een lijst met [risicodetecties](../reports-monitoring/concept-risk-events.md) en bijbehorende informatie. De identityRiskEvents API wordt op 10 januari 2020 afgeschaft; we raden u aan de **riskDetections** API te gebruiken. In dit artikel u aan de slag met het maken van verbinding met de Microsoft Graph en het opvragen van deze API's. Zie de [Microsoft Graph-site](https://graph.microsoft.io/) of de specifieke referentiedocumentatie voor deze API's voor een uitgebreide inleiding, volledige documentatie en toegang tot de Graph Explorer:

* [risicoDetectie-API](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *Wordt afgeschaft op 10 januari 2020*

## <a name="connect-to-microsoft-graph"></a>Verbinding maken met Microsoft-grafiek

Er zijn vier stappen om toegang te krijgen tot gegevens over identiteitsbescherming via Microsoft Graph:

1. Haal uw domeinnaam op.
2. Maak een nieuwe app-registratie. 
3. Gebruik dit geheim en een paar andere gegevens om te verifiëren bij Microsoft Graph, waar u een verificatietoken ontvangt. 
4. Gebruik dit token om aanvragen te doen voor het API-eindpunt en gegevens over identiteitsbescherming terug te krijgen.

Voordat u aan de slag gaat, hebt u het:

* Beheerdersrechten voor het maken van de toepassing in Azure AD
* De naam van het domein van uw tenant (bijvoorbeeld contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Uw domeinnaam ophalen 

1. [Meld u](https://portal.azure.com) aan bij uw Azure-portal als beheerder. 
1. Klik in het linkernavigatiedeelvenster op **Active Directory**. 

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/41.png)

1. Klik **in** de sectie Beheren op **Eigenschappen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/42.png)

1. Kopieer uw domeinnaam.

## <a name="create-a-new-app-registration"></a>Een nieuwe app-registratie maken

1. Klik op de pagina **Active Directory** in de sectie **Beheren** op **App-registraties**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/42.png)

1. Klik in het menu bovenaan op **Nieuwe toepassingsregistratie**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/43.png)

1. Voer op de pagina **Maken** de volgende stappen uit:

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/44.png)

   1. Typ in het tekstvak **Naam** een naam voor uw toepassing (bijvoorbeeld: Azure AD Risk Detection API Application).

   1. Selecteer **Webtoepassing en/of Web-API**als **type**.

   1. Typ in het tekstvak **AANmeldings-URL** . `http://localhost`

   1. Klik **op Maken**.
1. Als u de pagina **Instellingen wilt** openen, klikt u in de lijst met toepassingen op uw nieuw gemaakte app-registratie. 
1. Kopieer de **toepassings-id**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Uw aanvraag toestemming geven om de API te gebruiken

1. Klik **op** de pagina Instellingen op **Vereiste machtigingen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/15.png)

1. Klik **op** de pagina Vereiste machtigingen op de werkbalk bovenaan op **Toevoegen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/16.png)

1. Klik op de pagina **API-toegangs toevoegen** op **Een API selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/17.png)

1. Selecteer **op** de pagina Een API selecteren de optie Microsoft **Graph**en klik op **Selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/18.png)

1. Klik op de pagina **API-toegangs toevoegen** op **Machtigingen selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/19.png)

1. Klik **op** de pagina Toegang inschakelen op **Alle identiteitsrisicogegevens lezen**en klik vervolgens op **Selecteren**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/20.png)

1. Klik op de pagina **API-toegangs toevoegen** op **Gereed**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/21.png)

1. Klik **op** de pagina Vereiste machtigingen op **Machtigingen verlenen**en klik vervolgens op **Ja**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Een toegangssleutel opvragen

1. Klik **op** de pagina Instellingen op **Toetsen**.

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/23.png)

1. Voer **op** de pagina Sleutels de volgende stappen uit:

   ![Een toepassing maken](./media/howto-identity-protection-graph-api/24.png)

   1. Typ in het tekstvak **Sleutelbeschrijving** een beschrijving (bijvoorbeeld *Azure AD-risicodetectie).*
   1. Als **duur**selecteert u **In 1 jaar**.
   1. Klik op **Opslaan**.
   1. Kopieer de sleutelwaarde en plak deze op een veilige locatie.   
   
   > [!NOTE]
   > Als u deze sleutel verliest, moet u terugkeren naar deze sectie en een nieuwe sleutel maken. Houd deze sleutel geheim: iedereen die de sleutel heeft, heeft toegang tot uw gegevens.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Verifiëren naar Microsoft Graph en de API voor identiteitsrisicodetecties opvragen

Op dit punt moet je:

- De naam van het domein van uw tenant
- De client-id 
- De sleutel 

Als u wilt verifiëren, `https://login.microsoft.com` stuurt u een berichtverzoek naar met de volgende parameters in de body:

- grant_type: "**client_credentials**"
- Resource:`https://graph.microsoft.com`
- client_id: \<uw client-id\>
- client_secret: \<uw sleutel\>

Als dit lukt, retourneert dit een verificatietoken.  
Als u de API wilt aanroepen, maakt u een koptekst met de volgende parameter:

```
`Authorization`="<token_type> <access_token>"
```

Bij het verifiëren u het tokentype en het toegangstoken vinden in het geretourneerde token.

Stuur deze koptekst als een verzoek naar de volgende API-URL:`https://graph.microsoft.com/beta/identityRiskEvents`

Het antwoord, indien succesvol, is een verzameling van identiteitsrisicodetecties en bijbehorende gegevens in de OData JSON-indeling, die naar eigen inzicht kunnen worden ontleed en behandeld.

Hier vindt u voorbeeldcode voor het authenticeren en aanroepen van de API met PowerShell.  
Voeg gewoon uw client-ID, de geheime sleutel en het tenantdomein toe.

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

## <a name="query-the-apis"></a>De API's opvragen

Deze drie API's bieden een veelheid aan mogelijkheden om informatie op te halen over risicovolle gebruikers en aanmeldingen in uw organisatie. Hieronder vindt u enkele veelvoorkomende gebruiksvoorbeelden voor deze API's en de bijbehorende voorbeeldaanvragen. U deze query's uitvoeren met de voorbeeldcode hierboven of met [Grafiekverkenner](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Alle offline risicodetecties (riskDetection API)

Met aanmeldingsrisicobeleid voor identiteitsbescherming u voorwaarden toepassen wanneer risico's in realtime worden gedetecteerd. Maar hoe zit het met detecties die offline worden ontdekt? Als u wilt weten welke detecties offline zijn opgetreden en dus het aanmeldingsrisicobeleid niet zou hebben geactiveerd, u de API risicodetectie opvragen.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Krijg alle gebruikers die met succes geslaagd voor een MFA-uitdaging die wordt geactiveerd door riskantaanmeldingsbeleid (riskyUsers API)

Als u wilt inzicht krijgen in de impact die op risico's op basis van identiteitsbeveiliging op uw organisatie is gebaseerd, u alle gebruikers opvragen die een MFA-uitdaging hebben doorstaan die is geactiveerd door een riskant aanmeldingsbeleid. Deze informatie kan u helpen te begrijpen welke gebruikers Identiteitsbescherming mogelijk ten onrechte als risico hebben gedetecteerd en welke van uw legitieme gebruikers acties uitvoeren die de AI riskant acht.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Alle riskante aanmeldingen voor een specifieke gebruiker (signIn API)

Wanneer u denkt dat een gebruiker mogelijk is gecompromitteerd, u de toestand van hun risico beter begrijpen door al hun riskante aanmeldingen op te halen. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd, je hebt net je eerste oproep naar Microsoft Graph!  
Nu u identiteitsrisicodetecties opvragen en de gegevens gebruiken zoals u dat wilt.

Voor meer informatie over Microsoft Graph en het bouwen van toepassingen met behulp van de Graph API, bekijk de [documentatie](/graph/overview) en nog veel meer op de [Microsoft Graph-site.](https://developer.microsoft.com/graph) 

Zie voor gerelateerde informatie:

- [Azure Active Directory-identiteitsbeveiliging](../active-directory-identityprotection.md)
- [Typen risicodetecties gedetecteerd door Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overzicht van Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Root van azure AD-service voor identiteitsbescherming](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
