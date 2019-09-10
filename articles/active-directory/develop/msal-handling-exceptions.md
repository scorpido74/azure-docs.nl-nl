---
title: Fouten en uitzonde ringen (MSAL) | Azure
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, voorwaardelijke toegang en claim Challenge in MSAL-toepassingen.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 280746281fd45b3286cc76be5d3483f0cc65f90f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872801"
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

### `MsalUiRequiredException`

Een van de algemene status codes die door MSAL.net worden `AcquireTokenSilent()` geretourneerd `MsalError.InvalidGrantError`bij het aanroepen is. Deze status code betekent dat de toepassing de verificatie bibliotheek opnieuw moet aanroepen, maar in de interactieve modus (AcquireTokenInteractive of AcquireTokenByDeviceCodeFlow voor open bare client toepassingen en een uitdaging in web-apps). Dit komt doordat er aanvullende gebruikers interactie is vereist voordat het verificatie token kan worden uitgegeven.

De meeste tijd wanneer `AcquireTokenSilent` dit mislukt, komt doordat de token cache geen tokens bevat die overeenkomen met uw aanvraag. Toegangs tokens verlopen in één uur en `AcquireTokenSilent` er wordt geprobeerd een nieuwe op te halen op basis van een vernieuwings token (in OAuth2-voor waarden is dit de stroom voor het vernieuwen van het token). Deze stroom kan om verschillende redenen ook mislukken, bijvoorbeeld als een Tenant beheerder strengere aanmeldings beleidsregels configureert. 

De interactie is gericht op het uitvoeren van een actie door de gebruiker. Sommige van deze voor waarden zijn gemakkelijk te oplossen (bijvoorbeeld het accepteren van gebruiks voorwaarden met één klik), en sommige kunnen niet worden omgezet met de huidige configuratie (de computer in kwestie moet bijvoorbeeld verbinding maken met een specifiek bedrijfs netwerk). Sommige gebruikers helpen bij het instellen van multi-factor Authentication of het installeren van Microsoft Authenticator op het apparaat.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`inventarisatie van classificatie

In MSAL wordt een `Classification` veld weer gegeven, dat u kunt lezen om een betere gebruikers ervaring te bieden, bijvoorbeeld om de gebruiker te laten weten dat het wacht woord is verlopen of dat ze toestemming moeten geven om bepaalde resources te gebruiken. De ondersteunde waarden maken deel uit van `UiRequiredExceptionClassification` de Enum:

| Classificatie    | Betekenis           | Aanbevolen verwerking |
|-------------------|-------------------|----------------------|
| BasicAction | De voor waarde kan worden omgezet door de interactie van de gebruiker tijdens de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan. |
| AdditionalAction | Voor waarde kan worden opgelost door extra Remedia-interactie met het systeem, buiten de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan om een bericht weer te geven waarin de herstel actie wordt uitgelegd. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen waarvoor additional_action is vereist als de gebruiker de herstel actie niet waarschijnlijk kan volt ooien. |
| MessageOnly      | De voor waarde kan op dit moment niet worden opgelost. Bij het starten van een interactieve verificatie stroom wordt een bericht weer gegeven waarin de voor waarde wordt uitgelegd. | Roep AcquireTokenInteractively () aan om een bericht weer te geven waarin de voor waarde wordt uitgelegd. AcquireTokenInteractively () retourneert een UserCanceled-fout nadat de gebruiker het bericht heeft gelezen en het venster sluit. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen die resulteren in message_only als de gebruiker waarschijnlijk niet in aanmerking komt voor het bericht.|
| ConsentRequired  | Toestemming van de gebruiker ontbreekt of is ingetrokken. | Roep AcquireTokenInteractively () aan om de gebruiker toestemming te geven. |
| UserPasswordExpired | Het wacht woord van de gebruiker is verlopen. | Roep AcquireTokenInteractively () aan zodat gebruikers hun wacht woord opnieuw kunnen instellen. |
| PromptNeverFailed| Interactieve verificatie is aangeroepen met de parameter prompt = Never, het afdwingen van MSAL om te vertrouwen op browser cookies en niet om de browser weer te geven. Dit is mislukt. | Roep AcquireTokenInteractively () aan zonder prompt. none |
| AcquireTokenSilentFailed | De SDK voor MSAL heeft niet genoeg informatie om een token uit de cache op te halen. Dit kan zijn omdat er geen tokens in de cache staan of omdat er geen account is gevonden. Het fout bericht bevat meer details.  | Roep AcquireTokenInteractively () aan. |
| Geen    | Er worden geen verdere details gegeven. De voor waarde kan worden opgelost door de gebruikers interactie tijdens de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan. |

## <a name="code-example"></a>Voorbeeld van code

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```


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

Er wordt een fout geretourneerd wanneer u een niet-interactieve methode voor het verkrijgen van een token wilt gebruiken (bijvoorbeeld `acquireTokenSilent`) en MSAL niet op de achtergrond kan worden uitgevoerd.

Mogelijke oorzaken zijn:

* de gebruiker moet zich aanmelden
* de gebruiker moet toestemming geven
* de gebruiker moet een multi-factor Authentication-ervaring door lopen.

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

Zie [aanvullende claims aanvragen](active-directory-optional-claims.md) voor meer informatie.

## <a name="retrying-after-errors-and-exceptions"></a>Opnieuw proberen na fouten en uitzonde ringen

U wordt verwacht dat u het beleid voor opnieuw proberen implementeert bij het aanroepen van MSAL. MSAL maakt HTTP-aanroepen van de AAD-service mogelijk en er kunnen incidentele fouten optreden, bijvoorbeeld het netwerk kan uitvallen of de server overbelast is.  

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
