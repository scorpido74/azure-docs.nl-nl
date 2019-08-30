---
title: Fouten en uitzonde ringen (MSAL) | Azure
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, voorwaardelijke toegang en claim Challenge in MSAL-toepassingen.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 77a2e571b76044ff9114f6671b187118cf03c0ba
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135814"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Uitzonde ringen en fouten afhandelen met MSAL
Uitzonde ringen in micro soft Authentication Library (MSAL) zijn bedoeld voor app-ontwikkel aars om problemen op te lossen en niet voor het weer geven van eind gebruikers. Uitzonderings berichten zijn niet gelokaliseerd.

Bij het verwerken van uitzonde ringen en fouten kunt u het uitzonderings type zelf en de fout code gebruiken om onderscheid te maken tussen uitzonde ringen.  Zie [verificatie-en autorisatie fout codes](reference-aadsts-error-codes.md)voor een lijst met fout codes.

## <a name="net-exceptions"></a>.NET-uitzonde ringen
Bij het verwerken van uitzonde ringen kunt u het uitzonderings `ErrorCode` type zelf en het lid gebruiken om onderscheid te maken tussen uitzonde ringen. De waarden van `ErrorCode` zijn constanten van het type [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

U kunt ook een kijkje geven in de velden [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Als [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) wordt gegenereerd, kan de fout code een code bevatten die kan worden gevonden in de [fout codes voor verificatie en autorisatie](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Algemene uitzonderingen
Hier volgen de veelvoorkomende uitzonde ringen die mogelijk worden gegenereerd en enkele mogelijke oplossingen.

| Uitzondering | Foutcode | Oplossing|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: De gebruiker of beheerder heeft niet ingestemd met het gebruik van de toepassing met de ID {appId} met de naam {appName}. Verzend een interactieve autorisatie aanvraag voor deze gebruiker en resource.| U moet eerst de toestemming van de gebruiker ophalen. Als u geen .NET Core gebruikt (die geen Web-UI heeft), roept u (eenmaal) `AcquireTokeninteractive`aan. Als u .net Core gebruikt of als u geen gebruik wilt maken `AcquireTokenInteractive`van een, kan de gebruiker naar een URL navigeren om toestemming https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read te geven:. Om aan `AcquireTokenInteractive`te roepen:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: De gebruiker is verplicht multi-factor Authentication te gebruiken.| Er is geen risico beperking: als MFA is geconfigureerd voor uw Tenant en Aad besluit dit af te dwingen, moet u een interactieve stroom, zoals `AcquireTokenInteractive` of `AcquireTokenByDeviceCode`, terugvallen op een andere.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Het toekennings type wordt niet ondersteund via de */veelvoorkomende* -of */consumers* -eind punten. Gebruik het */organizations* -of Tenant-specifieke eind punt. U hebt */veelvoorkomende*gebruikt.| Zoals uitgelegd in het bericht van Azure AD, moet de instantie een Tenant of anderszins */organizations*hebben.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: De aanvraag tekst moet de volgende para meter bevatten: client_secret of client_assertion.| Deze uitzonde ring kan zich voordoen als uw toepassing niet is geregistreerd als een open bare client toepassing in azure AD. Bewerk in het Azure Portal het manifest voor uw toepassing en stel de `allowPublicClient` in op. `true` |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user bericht: Kan de aangemelde gebruiker niet identificeren| De bibliotheek kan geen query uitvoeren op de huidige aangemelde gebruiker van Windows of deze gebruiker is niet opgenomen in AD of AAD. dit wordt niet ondersteund door gebruikers die lid zijn van een domein. Beperking 1: Controleer op UWP of de toepassing de volgende mogelijkheden heeft: Ondernemings verificatie, particuliere netwerken (client en server), informatie over gebruikers accounts. Beperking 2: Implementeer uw eigen logica voor het ophalen van de gebruikers naam ( john@contoso.combijvoorbeeld) en gebruik `AcquireTokenByIntegratedWindowsAuth` het formulier dat de gebruikers naam gebruikt.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Deze methode is afhankelijk van een protocol dat wordt weer gegeven door Active Directory (AD). Als een gebruiker is gemaakt in Azure Active Directory zonder een AD-back-up (' beheerde ' gebruiker), mislukt deze methode. Gebruikers die zijn gemaakt in AD en die worden ondersteund door AAD (' federatieve ' gebruikers), kunnen profiteren van deze niet-interactieve verificatie methode. Risico beperking Interactieve verificatie gebruiken.|

## <a name="javascript-errors"></a>Java script-fouten

MSAL. js bevat fout objecten die de verschillende soorten veelvoorkomende fouten abstract en geclassificeerd. Het biedt ook een interface voor toegang tot specifieke details van de fouten, zoals fout berichten om deze op de juiste wijze af te handelen.

**Fout object**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
Door de fout klasse uit te breiden, hebt u toegang tot de volgende eigenschappen:
* **AuthError. bericht:**  Hetzelfde als de errorMessage.
* **AuthError. stack:** Stack tracering voor fouten die zijn opgetreden. Hiermee kan het bron punt van de fout traceren.

**Fout typen**

De volgende fout typen zijn beschikbaar:

* *AuthError:* De basis fout klasse voor de MSAL. JS-bibliotheek, die ook wordt gebruikt voor onverwachte fouten.

* *ClientAuthError:* Fout klasse, die een probleem met client verificatie aanduidt. De meeste fouten die afkomstig zijn uit de-bibliotheek, zijn ClientAuthErrors. Deze fouten zijn bijvoorbeeld het aanroepen van een aanmeldings methode wanneer de aanmelding wordt uitgevoerd, gebruikers die het aanmelden annuleren. 

* *ClientConfigurationError:* Fout klasse waardoor ClientAuthError wordt gegenereerd voordat aanvragen worden gedaan wanneer de opgegeven gebruikers configuratie parameters onjuist zijn of ontbreken.

* *ServerError:* Fout klasse om de fout teken reeksen weer te geven die door de verificatie server worden verzonden. Dit kunnen fouten zijn, zoals ongeldige aanvraag indelingen of para meters, of andere fouten die voor komen dat de server de gebruiker verifieert of autoriseert.

* *InteractionRequiredAuthError:* Fout klasse die server error uitbreidt om Server fouten aan te duiden, waarvoor een interactieve aanroep is vereist. Deze fout treedt op `acquireTokenSilent` als de gebruiker moet communiceren met de server om referenties of toestemming te geven voor verificatie/autorisatie. Fout codes zijn onder andere "interaction_required", "login_required", "consent_required".

Voor fout afhandeling in verificatie stromen met omleidings`loginRedirect`methoden `acquireTokenRedirect`(,) moet u de retour aanroep registreren, die wordt aangeroepen met geslaagd of mislukt na de omleiding `handleRedirectCallback()` met de methode:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

De methoden voor de pop-upervaring`loginPopup`( `acquireTokenPopup`,) retour nering, zodat u het Promise-patroon (. then en. Catch) kunt gebruiken om ze te verwerken zoals wordt weer gegeven:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Voor de interactie vereiste fouten

Er wordt een fout geretourneerd wanneer u een niet-interactieve methode voor het verkrijgen van een token wilt gebruiken (bijvoorbeeld `acquireTokenSilent`) en MSAL kan niet op de achtergrond worden uitgevoerd. 

Mogelijke oorzaken zijn:

* u moet zich aanmelden
* u moet toestemming geven
* u moet een multi-factor Authentication-ervaring door lopen.

Het herstel is om een interactieve methode aan te roepen, `acquireTokenPopup` zoals `acquireTokenRedirect`of:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Voorwaardelijke toegang en claim uitdagingen
Wanneer tokens op de achtergrond worden opgehaald, kan uw toepassing fouten ontvangen wanneer een [Challenge voor voorwaardelijke toegang](conditional-access-dev-guide.md) , zoals MFA-beleid, is vereist voor een API die u probeert te openen.

Het patroon voor het afhandelen van deze fout is het interactief verkrijgen van een token met behulp van MSAL. Bij het interactief ophalen van een token wordt de gebruiker gevraagd om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

In bepaalde gevallen bij het aanroepen van een API waarvoor voorwaardelijke toegang is vereist, kunt u een claim Challenge ontvangen in de fout van de API. Als het beleid voor voorwaardelijke toegang bijvoorbeeld een beheerd apparaat (intune) heeft, is de fout [als AADSTS53000: Het apparaat moet worden beheerd om toegang te krijgen tot deze](reference-aadsts-error-codes.md) bron of iets anders. In dit geval kunt u de claims door geven in de aanroep van het Acquire-token, zodat de gebruiker wordt gevraagd om te voldoen aan het juiste beleid.

### <a name="net"></a>.NET
Bij het aanroepen van een API waarvoor voorwaardelijke toegang is vereist vanuit MSAL.NET, moet uw toepassing de uitzonde ringen voor claim controle afhandelen. Dit wordt weer gegeven als een [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) waarbij de eigenschap [claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) niet leeg is.

Als u de claim Challenge wilt afhandelen, moet u `.WithClaim()` de methode van `PublicClientApplicationBuilder` de-klasse gebruiken.

### <a name="javascript"></a>JavaScript
Wanneer tokens op de achtergrond worden opgehaald `acquireTokenSilent`(met behulp van MSAL. js), kan uw toepassing fouten ontvangen wanneer een [Challenge voor voorwaardelijke toegang](conditional-access-dev-guide.md) , zoals MFA-beleid, is vereist voor een API die u probeert te openen.

Het patroon voor het afhandelen van deze fout is het maken van een interactieve aanroep om token te verkrijgen `acquireTokenPopup` in `acquireTokenRedirect` MSAL. js, zoals of zoals in het volgende voor beeld:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Als u het token interactief aanschaft, wordt de gebruiker gevraagd om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

Wanneer u een API aanroept waarvoor voorwaardelijke toegang is vereist, kunt u een claim Challenge ontvangen in de fout van de API. In dit geval kunt u de geretourneerde claims in de fout `claimsRequest` door geven aan het veld van de `AuthenticationParameters.ts` klasse om aan het juiste beleid te voldoen. 

Zie [aanvullende claims aanvragen]() voor meer informatie.

## <a name="retrying-after-errors-and-exceptions"></a>Opnieuw proberen na fouten en uitzonde ringen

### <a name="http-error-codes-500-600"></a>HTTP-fout codes 500-600
MSAL.NET implementeert een eenvoudig mechanisme voor opnieuw proberen voor fouten met HTTP-fout codes 500-600.

### <a name="http-429"></a>HTTP 429
Wanneer de service token server (STS) is overbelast met te veel aanvragen, retourneert deze een HTTP-fout 429 met een hint bij het tijdstip waarop u het opnieuw kunt proberen. De fout kan worden gelezen uit `Retry-After` het antwoord veld.

#### <a name="net"></a>.NET
De [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) -uitzonderings oppervlakken `System.Net.Http.Headers.HttpResponseHeaders` als eigenschap `namedHeaders`. Daarom kunt u aanvullende informatie uit de fout code gebruiken om de betrouw baarheid van uw toepassingen te verbeteren. In het geval dat we zojuist hebben beschreven, kunt u `RetryAfterproperty` het (type `RetryConditionHeaderValue`) gebruiken en berekenen wanneer u het opnieuw wilt proberen.

Hier volgt een voor beeld van een daemon-toepassing (met behulp van de client referenties stroom), maar u kunt deze aanpassen aan een van de methoden voor het verkrijgen van een token.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
