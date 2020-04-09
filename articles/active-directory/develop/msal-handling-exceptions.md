---
title: Fouten en uitzonderingen (MSAL)
titleSuffix: Microsoft identity platform
description: Meer informatie over het omgaan met fouten en uitzonderingen, voorwaardelijke toegang en claimsproblemen in MSAL-toepassingen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 93d07ab1740da68298478ae2dcc2ab46d8d8362e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884015"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL-uitzonderingen en -fouten verwerken

In dit artikel vindt u een overzicht van de verschillende soorten fouten en aanbevelingen voor het afhandelen van veelvoorkomende aanmeldingsfouten.

## <a name="msal-error-handling-basics"></a>Basisprincipes van MSAL-foutafhandeling

Uitzonderingen in de Microsoft Authentication Library (MSAL) zijn bedoeld voor app-ontwikkelaars om problemen op te lossen, niet voor weergave aan eindgebruikers. Uitzonderingsberichten zijn niet gelokaliseerd.

Wanneer u uitzonderingen en fouten verwerkt, u het uitzonderingstype zelf en de foutcode gebruiken om onderscheid te maken tussen uitzonderingen.  Zie [Foutcodes voor verificatie en autorisatie](reference-aadsts-error-codes.md)voor een lijst met foutcodes .

Tijdens de aanmeldingservaring u fouten tegenkomen over toestemmingen, voorwaardelijke toegang (MFA, Apparaatbeheer, Locatiebeperkingen), uitgifte en inwisseling van token en gebruikerseigenschappen.

Zie de volgende sectie die overeenkomt met de taal die u gebruikt voor meer informatie over foutafhandeling voor uw app.

## <a name="net"></a>[.NET](#tab/dotnet)

Bij het verwerken van .NET-uitzonderingen `ErrorCode` u het uitzonderingstype zelf en het lid gebruiken om onderscheid te maken tussen uitzonderingen. `ErrorCode`waarden zijn constanten van het type [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

U ook een kijkje nemen op de velden van [MsalClientException,](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet) [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)en [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Als [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) wordt gegooid, probeert [u verificatie- en autorisatiefoutcodes](reference-aadsts-error-codes.md) om te zien of de code daar wordt vermeld.

### <a name="common-net-exceptions"></a>Algemene .NET-uitzonderingen

Hier zijn de gemeenschappelijke uitzonderingen die kunnen worden gegooid en een aantal mogelijke oplossingen:  

| Uitzondering | Foutcode | Oplossing|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: De gebruiker of beheerder heeft niet ingestemd met het gebruik van de toepassing met ID '{appId}' met de naam '{appName}'. Stuur een interactieve autorisatieaanvraag voor deze gebruiker en bron.| Je moet eerst toestemming van de gebruiker te krijgen. Als u geen .NET-kern gebruikt (die geen webgebruikersinterface heeft), `AcquireTokeninteractive`belt u (slechts één keer). Als u .NET-kern gebruikt of geen `AcquireTokenInteractive`, kan de gebruiker naar een `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`URL navigeren om toestemming te geven: . te `AcquireTokenInteractive`bellen:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: De gebruiker is verplicht om multi-factor authenticatie (MFA) te gebruiken.| Er is geen mitigatie. Als MFA is geconfigureerd voor uw tenant en Azure Active Directory (AAD) besluit deze af `AcquireTokenInteractive` te `AcquireTokenByDeviceCode`dwingen, moet u terugvallen op een interactieve stroom zoals of .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Het subsidietype wordt niet ondersteund via de */common* of */consumers-eindpunten.* Gebruik het */-organisatie-* of tenantspecifieke eindpunt. Je gebruikte */common*.| Zoals uitgelegd in het bericht van Azure AD, moet de autoriteit een tenant of anderszins */organisaties*hebben.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: De aanvraaginstantie moet de `client_secret or client_assertion`volgende parameter bevatten: .| Deze uitzondering kan worden gegenereerd als uw toepassing niet is geregistreerd als een openbare clienttoepassing in Azure AD. Bewerk in de Azure-portal het manifest `allowPublicClient` `true`voor uw toepassing en stel in op . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Kan ingelogde gebruiker niet identificeren| De bibliotheek kan geen query's opvragen op de huidige Windows-aangemelde gebruiker of deze gebruiker is geen AD of AAD lid (werkplaats samengevoegde gebruikers worden niet ondersteund). Mitigatie 1: controleer op UWP of de toepassing de volgende mogelijkheden heeft: Enterprise Authentication, Private Networks (Client and Server), User Account Information. Mitigatie 2: Implementeer uw eigen logica om john@contoso.comde gebruikersnaam `AcquireTokenByIntegratedWindowsAuth` (bijvoorbeeld ) op te halen en het formulier te gebruiken dat in de gebruikersnaam wordt aangenomen.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Deze methode is gebaseerd op een protocol dat wordt blootgesteld door Active Directory (AD). Als een gebruiker is gemaakt in Azure Active Directory zonder AD-ondersteuning ('beheerde' gebruiker), mislukt deze methode. Gebruikers die zijn gemaakt in AD en worden ondersteund door AAD ("federatieve" gebruikers) kunnen profiteren van deze niet-interactieve verificatiemethode. Mitigatie: gebruik interactieve verificatie.|

### `MsalUiRequiredException`

Een van de gemeenschappelijke statuscodes die zijn geretourneerd van MSAL.NET wanneer bellen `AcquireTokenSilent()` is `MsalError.InvalidGrantError`. Deze statuscode betekent dat de toepassing de verificatiebibliotheek opnieuw moet aanroepen, maar in de interactieve modus (AcquireTokenInteractive of AcquireTokenByDeviceCodeFlow voor openbare clienttoepassingen en een uitdaging in web-apps). Dit komt omdat extra gebruikersinteractie vereist is voordat verificatietoken kan worden uitgegeven.

Meestal wanneer `AcquireTokenSilent` het mislukt, is dit omdat de tokencache geen tokens heeft die overeenkomen met uw aanvraag. Toegangstokens verlopen binnen 1 `AcquireTokenSilent` uur en proberen een nieuwe te halen op basis van een vernieuwingstoken (in OAuth2-termen is dit de 'Refresh Token'-stroom). Deze stroom kan ook om verschillende redenen mislukken, bijvoorbeeld als een tenantbeheerder een strenger inlogbeleid configureert. 

De interactie is bedoeld om de gebruiker een actie te laten doen. Sommige van deze voorwaarden zijn eenvoudig op te lossen voor gebruikers (accepteren bijvoorbeeld gebruiksvoorwaarden met één klik) en sommige kunnen niet worden opgelost met de huidige configuratie (de machine in kwestie moet bijvoorbeeld verbinding maken met een specifiek bedrijfsnetwerk). Sommige helpen de gebruiker bij het instellen van Multi-factor authenticatie of microsoft authenticator installeren op hun apparaat.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`indelingsopsomming

MSAL onthult `Classification` een veld, dat u lezen om een betere gebruikerservaring te bieden, bijvoorbeeld om de gebruiker te vertellen dat hun wachtwoord is verlopen of dat ze toestemming moeten geven om bepaalde bronnen te gebruiken. De ondersteunde waarden maken `UiRequiredExceptionClassification` deel uit van het enum:

| Classificatie    | Betekenis           | Aanbevolen afhandeling |
|-------------------|-------------------|----------------------|
| Basisactie | Voorwaarde kan worden opgelost door interactie van de gebruiker tijdens de interactieve verificatiestroom. | Call AcquireTokenInteractively(). |
| Aanvullende actie | Voorwaarde kan worden opgelost door extra corrigerende interactie met het systeem, buiten de interactieve authenticatie stroom. | Call AcquireTokenInteractively() om een bericht weer te geven dat de corrigerende actie verklaart. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen die additional_action vereisen als het onwaarschijnlijk is dat de gebruiker de corrigerende actie voltooit. |
| Alleen voor berichten      | Voorwaarde kan niet worden opgelost op dit moment. Als u interactieve verificatiestroom start, wordt een bericht weergegeven waarin de voorwaarde wordt uitgelegd. | Call AcquireTokenInteractively() om een bericht weer te geven dat de voorwaarde uitlegt. AcquireTokenInteractively() retourneert userCanceled error nadat de gebruiker het bericht heeft gelezen en het venster sluit. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen die resulteren in message_only als de gebruiker waarschijnlijk niet profiteert van het bericht.|
| Toestemmingvereist  | De toestemming van de gebruiker ontbreekt of is ingetrokken. | Call AcquireTokenInteractively() voor de gebruiker om toestemming te geven. |
| UserPasswordExpired | Het wachtwoord van de gebruiker is verlopen. | Call AcquireTokenInteractively() zodat de gebruiker zijn wachtwoord opnieuw kan instellen. |
| PromptNeverFailed| Interactieve verificatie werd aangeroepen met de parameter prompt=never, waardoor MSAL gedwongen werd om te vertrouwen op browsercookies en de browser niet weer te geven. Dit is mislukt. | Call AcquireTokenInteractively() zonder Prompt.None |
| AcquireTokenSilentFailed | MSAL SDK heeft niet genoeg informatie om een token uit de cache te halen. Dit kan zijn omdat er geen tokens in de cache zijn of omdat er geen account is gevonden. Het foutbericht bevat meer details.  | Call AcquireTokenInteractively(). |
| Geen    | Verdere details worden niet verstrekt. Voorwaarde kan worden opgelost door interactie van de gebruiker tijdens de interactieve verificatiestroom. | Call AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Voorbeeld van .NET-code

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
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
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

## <a name="javascript"></a>[Javascript](#tab/javascript)

MSAL.js biedt foutobjecten die de verschillende soorten veelvoorkomende fouten abstraheren en classificeren. Het biedt ook interface om toegang te krijgen tot specifieke details van de fouten, zoals foutmeldingen om ze op de juiste manier te behandelen.

### <a name="error-object"></a>Foutobject

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

Door de foutklasse uit te breiden, hebt u toegang tot de volgende eigenschappen:
- `AuthError.message`: Hetzelfde `errorMessage`als de .
- `AuthError.stack`: Stack trace voor gegooide fouten.

### <a name="error-types"></a>Fouttypen

De volgende fouttypen zijn beschikbaar:

- `AuthError`: Basisfoutklasse voor de MSAL.js-bibliotheek, ook gebruikt voor onverwachte fouten.

- `ClientAuthError`: Foutklasse, die een probleem met clientverificatie aangeeft. De meeste fouten die afkomstig zijn uit de bibliotheek zullen ClientAuthErrors zijn. Deze fouten zijn het gevolg van zaken als het aanroepen van een inlogmethode wanneer het inloggen al aan de gang is, de gebruiker annuleert de login, enzovoort.

- `ClientConfigurationError`: Foutklasse, `ClientAuthError` breidt gegooid voordat aanvragen worden gedaan wanneer de gegeven gebruiker config parameters zijn misvormd of ontbreekt.

- `ServerError`: Foutklasse, vertegenwoordigt de fouttekenreeksen die door de verificatieserver worden verzonden. Dit kunnen fouten zijn, zoals ongeldige aanvraagnotaties of parameters, of andere fouten die voorkomen dat de server de gebruiker authenticeert of autorisert.

- `InteractionRequiredAuthError`: Foutklasse, `ServerError` strekt zich uit tot serverfouten, waarvoor een interactief gesprek vereist is. Deze fout wordt `acquireTokenSilent` gegenereerd door als de gebruiker moet communiceren met de server om referenties of toestemming voor authenticatie / autorisatie te verstrekken. Foutcodes `"interaction_required"`zijn `"login_required"`onder `"consent_required"`andere , en .

Voor foutafhandeling in verificatiestromen met`loginRedirect` `acquireTokenRedirect`omleidingsmethoden ( , ) moet u de callback registreren, `handleRedirectCallback()` die met succes of fout wordt aangeroepen na de omleidingsmethode als volgt:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

De methoden voor pop-up`loginPopup` `acquireTokenPopup`ervaring (, ) terugkeer beloften, zodat u het belofte patroon (.then en .catch) gebruiken om ze te behandelen zoals getoond:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Fouten die interactie vereisen

Een fout wordt geretourneerd wanneer u een niet-interactieve methode probeert `acquireTokenSilent`te gebruiken om een token te verkrijgen, zoals , maar MSAL kon het niet stil doen.

Mogelijke redenen zijn:

- je moet inloggen
- je moet toestemming geven
- je moet gaan door middel van een multi-factor authenticatie ervaring.

De sanering is het aanroepen `acquireTokenPopup` van `acquireTokenRedirect`een interactieve methode zoals:

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

## <a name="python"></a>[Python](#tab/python)

In MSAL voor Python worden de meeste fouten overgebracht als een retourwaarde uit de API-aanroep. De fout wordt weergegeven als een woordenboek met de JSON-reactie van het Microsoft-identiteitsplatform.

* Een succesvol antwoord `"access_token"` bevat de sleutel. De notatie van het antwoord wordt gedefinieerd door het OAuth2-protocol. Zie [5.1 Succesvolle reactie](https://tools.ietf.org/html/rfc6749#section-5.1) voor meer informatie
* Een foutreactie `"error"` bevat `"error_description"`en meestal . De notatie van het antwoord wordt gedefinieerd door het OAuth2-protocol. Zie [5.2 Foutreactie](https://tools.ietf.org/html/rfc6749#section-5.2) voor meer informatie

Wanneer een fout wordt `"error_description"` geretourneerd, bevat de sleutel een door de mens leesbaar bericht; die op zijn beurt meestal een microsoft-identiteitsplatformfoutcode bevat. Zie [Foutcodes voor verificatie en autorisatie](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)voor meer informatie over de verschillende foutcodes.

In MSAL voor Python zijn uitzonderingen zeldzaam omdat de meeste fouten worden verwerkt door een foutwaarde terug te sturen. De `ValueError` uitzondering wordt alleen gegenereerd wanneer er een probleem is met de manier waarop u de bibliotheek probeert te gebruiken, zoals wanneer API-parameter(s) verkeerd zijn vervormd.

## <a name="java"></a>[Java](#tab/java)

In MSAL voor Java zijn er `MsalClientException`drie `MsalServiceException`soorten `MsalInteractionRequiredException`uitzonderingen: , , en ; allen die `MsalException`erven van .

- `MsalClientException`wordt gegooid wanneer er een fout optreedt die lokaal is voor de bibliotheek of het apparaat.
- `MsalServiceException`wordt gegooid wanneer de secure token service (STS) een foutreactie retourneert of een andere netwerkfout optreedt.
- `MsalInteractionRequiredException`wordt gegenereerd wanneer ui-interactie vereist is om verificatie te laten slagen.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`stelt HTTP-headers die in de aanvragen worden geretourneerd, bloot aan de STS. Toegang tot hen via`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Een van de gemeenschappelijke statuscodes die `AcquireTokenSilently()` `InvalidGrantError`zijn geretourneerd van MSAL voor Java bij het bellen is . Dit betekent dat extra gebruikersinteractie vereist is voordat een verificatietoken kan worden uitgegeven. Uw toepassing moet de verificatiebibliotheek opnieuw aanroepen, maar in de interactieve modus door het verzenden `AuthorizationCodeParameters` of `DeviceCodeParameters` voor openbare clienttoepassingen.

Meestal wanneer `AcquireTokenSilently` het mislukt, komt dat omdat de tokencache geen token heeft die overeenkomt met uw aanvraag. Toegangstokens verlopen binnen een `AcquireTokenSilently` uur en proberen een nieuwe te krijgen op basis van een vernieuwingstoken. In OAuth2-termen is dit de vernieuwingstokenstroom. Deze stroom kan ook mislukken om verschillende redenen, zoals wanneer een tenantbeheerder een strenger inlogbeleid configureert.

Sommige voorwaarden die resulteren in deze fout zijn gemakkelijk voor gebruikers op te lossen. Het kan bijvoorbeeld nodig zijn dat ze gebruiksvoorwaarden accepteren. Of misschien kan het verzoek niet worden voldaan met de huidige configuratie, omdat de machine moet verbinding maken met een specifiek bedrijfsnetwerk.

MSAL legt `reason` een veld bloot, dat u gebruiken om een betere gebruikerservaring te bieden. Het `reason` veld kan er bijvoorbeeld toe leiden dat u de gebruiker vertelt dat zijn wachtwoord is verlopen of dat hij/zij toestemming moet geven om bepaalde bronnen te gebruiken. De ondersteunde waarden maken `InteractionRequiredExceptionReason` deel uit van het enum:

| Reden | Betekenis | Aanbevolen afhandeling |
|---------|-----------|-----------------------------|
| `BasicAction` | Voorwaarde kan worden opgelost door interactie van de gebruiker tijdens de interactieve verificatiestroom | Bellen `acquireToken` met interactieve parameters |
| `AdditionalAction` | Voorwaarde kan worden opgelost door extra corrigerende interactie met het systeem buiten de interactieve verificatiestroom. | Bel `acquireToken` met interactieve parameters om een bericht weer te geven dat de corrigerende actie verklaart die moet worden ondernomen. De aanroepende app kan ervoor kiezen om stromen te verbergen die extra actie vereisen als het onwaarschijnlijk is dat de gebruiker de corrigerende actie voltooit. |
| `MessageOnly` | Voorwaarde kan niet worden opgelost op dit moment. Start interactieve verificatiestroom om een bericht weer te geven waarin de voorwaarde wordt uitgelegd. | Bel `acquireToken` met interactieve parameters om een bericht weer te geven dat de voorwaarde uitlegt. `acquireToken`de `UserCanceled` fout wordt weergegeven nadat de gebruiker het bericht heeft gelezen en het venster heeft gesloten. De app kan ervoor kiezen om stromen te verbergen die resulteren in een bericht als de gebruiker waarschijnlijk niet profiteert van het bericht. |
| `ConsentRequired`| De toestemming van de gebruiker ontbreekt of is ingetrokken. |Bel `acquireToken` met interactieve parameters, zodat de gebruiker toestemming kan geven. |
| `UserPasswordExpired` | Het wachtwoord van de gebruiker is verlopen. | Bellen `acquireToken` met interactieve parameter, zodat de gebruiker zijn wachtwoord opnieuw kan instellen |
| `None` |  Verdere details worden verstrekt. De voorwaarde kan worden opgelost door interactie van de gebruiker tijdens de interactieve verificatiestroom. | Bellen `acquireToken` met interactieve parameters |

### <a name="code-example"></a>Codevoorbeeld

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

De volledige lijst van MSAL voor iOS- en macOS-fouten wordt vermeld in [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Alle msal geproduceerde fouten `MSALErrorDomain` worden geretourneerd met domein.

Voor systeemfouten retourneert `NSError` MSAL het origineel uit de systeem-API. Als tokenacquisitie bijvoorbeeld mislukt vanwege een gebrek aan netwerkconnectiviteit, retourneert `NSURLErrorNotConnectedToInternet` MSAL een fout met het domein en de `NSURLErrorDomain` code.

Wij raden u aan ten minste de volgende twee MSAL-fouten aan de clientzijde te verwerken:

- `MSALErrorInteractionRequired`: De gebruiker moet een interactief verzoek doen. Er zijn veel voorwaarden die tot deze fout kunnen leiden, zoals een verlopen verificatiesessie of de noodzaak van aanvullende verificatievereisten. Bel de MSAL interactive token acquisition API om te herstellen. 

- `MSALErrorServerDeclinedScopes`: Sommige of alle scopes zijn geweigerd. Bepaal of u alleen doorwilt gaan met de toegekende scopes of stop het aanmeldingsproces.

> [!NOTE]
> Het `MSALInternalError` enum mag alleen worden gebruikt voor referentie en debugging. Probeer deze fouten niet automatisch te verwerken tijdens runtime. Als uw app een van de `MSALInternalError`fouten tegenkomt die onder vallen, u een algemene gebruiker die wordt geconfronteerd met een bericht laten zien waarin wordt uitgelegd wat er is gebeurd.

Dit betekent `MSALInternalErrorBrokerResponseNotReceived` bijvoorbeeld dat de gebruiker de verificatie niet heeft voltooid en handmatig is teruggestuurd naar de app. In dit geval moet uw app een algemeen foutbericht weergeven waarin wordt uitgelegd dat de verificatie niet is voltooid en moet worden gesuggereerd dat deze opnieuw probeert te verifiëren.

De volgende objectieve c-voorbeeldcode toont best practices aan voor het omgaan met enkele veelvoorkomende foutvoorwaarden:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Uitdagingen voor voorwaardelijke toegang en claims

Wanneer u tokens in stilte ontvangt, kan uw toepassing fouten ontvangen wanneer een [uitdaging voor voorwaardelijke toegangclaims](../azuread-dev/conditional-access-dev-guide.md) zoals mfa-beleid vereist is door een API die u probeert te openen.

Het patroon voor het verwerken van deze fout is om interactief een token te verwerven met MSAL. Het interactief verwerven van een token vraagt de gebruiker en geeft hen de mogelijkheid om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

In bepaalde gevallen wanneer u een API aanroept waarvoor voorwaardelijke toegang vereist is, u een claimuitdaging ontvangen in de fout van de API. Als het beleid voor voorwaardelijke toegang bijvoorbeeld een beheerd apparaat (Intune) moet hebben, is de fout iets als [AADSTS53000: uw apparaat moet worden beheerd om toegang te krijgen tot deze bron](reference-aadsts-error-codes.md) of iets dergelijks. In dit geval u de claims doorgeven in het aanwerven van tokenoproepen, zodat de gebruiker wordt gevraagd om aan het juiste beleid te voldoen.

### <a name="net"></a>.NET

Wanneer u een API aanroept waarvoor voorwaardelijke toegang vereist is van MSAL.NET, moet uw toepassing uitzonderingen op claimaanvragen afhandelen. Dit wordt weergegeven als een [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) waarbij de eigenschap [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) niet leeg is.

Om de claimuitdaging aan te kunnen, `.WithClaim()` moet `PublicClientApplicationBuilder` je de methode van de klasse gebruiken.

### <a name="javascript"></a>Javascript

Wanneer tokens in stilte `acquireTokenSilent`(met behulp van) msal.js worden gebruikt, kan uw toepassing fouten ontvangen wanneer een [conditional access-claimuitdaging](../azuread-dev/conditional-access-dev-guide.md) zoals MFA-beleid vereist is door een API die u probeert te openen.

Het patroon om deze fout te verwerken is om een interactieve `acquireTokenPopup` oproep `acquireTokenRedirect` te doen om token te verwerven in MSAL.js, zoals of zoals in het volgende voorbeeld:

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

Het interactief verwerven van het token vraagt de gebruiker en geeft hen de mogelijkheid om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

Wanneer u een API aanroept waarvoor voorwaardelijke toegang vereist is, u een claimuitdaging ontvangen in de fout van de API. In dit geval u de claims die `claimsRequest` in `AuthenticationParameters.ts` de fout zijn geretourneerd, doorgeven aan het veld van de klasse om aan het juiste beleid te voldoen. 

Zie [Aanvullende claims aanvragen](active-directory-optional-claims.md) voor meer details.

### <a name="msal-for-ios-and-macos"></a>MSAL voor iOS en macOS

Met MSAL voor iOS en macOS u specifieke claims aanvragen in zowel interactieve als stille tokenacquisitiescenario's.

Als u aangepaste `claimsRequest` claims `MSALSilentTokenParameters` `MSALInteractiveTokenParameters`wilt aanvragen, geeft u op in of .

Zie [Aangepaste claims aanvragen met MSAL voor iOS en macOS](request-custom-claims.md) voor meer informatie.

## <a name="retrying-after-errors-and-exceptions"></a>Opnieuw proberen na fouten en uitzonderingen

Er wordt van u verwacht dat u een eigen beleid voor nieuwe try implementeert wanneer u MSAL belt. MSAL voert HTTP-oproepen naar de AAD-service en er kunnen incidentele fouten optreden, bijvoorbeeld het netwerk kan uitvallen of de server is overbelast.  

### <a name="http-error-codes-500-600"></a>HTTP-foutcodes 500-600

MSAL.NET implementeert een eenvoudig opnieuw proberen-eenmechanisme voor fouten met HTTP-foutcodes 500-600.

### <a name="http-429"></a>HTTP 429

Wanneer de Service Token Server (STS) overbelast is met te veel aanvragen, wordt HTTP-fout 429 `Retry-After` geretourneerd met een hint over hoe lang het duurt voordat u het opnieuw proberen in het antwoordveld.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) duikt `System.Net.Http.Headers.HttpResponseHeaders` op `namedHeaders`als eigenschap . U aanvullende informatie uit de foutcode gebruiken om de betrouwbaarheid van uw toepassingen te verbeteren. In het beschreven geval kunt `RetryAfterproperty` u `RetryConditionHeaderValue`het (van type) gebruiken en berekenen wanneer u het opnieuw moet proberen.

Hier is een voorbeeld voor een daemon-toepassing met behulp van de clientreferentiesstroom. U dit aanpassen aan een van de methoden voor het verkrijgen van een token.

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
