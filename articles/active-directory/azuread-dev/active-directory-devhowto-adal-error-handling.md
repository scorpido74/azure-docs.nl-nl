---
title: Aanbevolen procedures voor foutafhandeling van ADAL-client-app | Azure
description: Biedt richtlijnen voor foutafhandeling en aanbevolen procedures voor ADAL-clienttoepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9fc45ead65a29f2e7567133b5af4667bdb7c79ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154981"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Aanbevolen procedures voor foutafhandeling voor ADAL-clients (Azure Active Directory Authentication Library)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

In dit artikel vindt u richtlijnen voor het type fouten dat ontwikkelaars kunnen tegenkomen wanneer ze ADAL gebruiken om gebruikers te verifiëren. Bij het gebruik van ADAL zijn er verschillende gevallen waarin een ontwikkelaar mogelijk moet instappen en fouten moet verwerken. Een goede foutafhandeling zorgt voor een geweldige gebruikerservaring en beperkt het aantal keren dat de eindgebruiker zich moet aanmelden.

In dit artikel verkennen we de specifieke cases voor elk platform dat door ADAL wordt ondersteund en hoe uw toepassing elk geval goed kan behandelen. De foutrichtlijnen zijn opgesplitst in twee bredere categorieën, op basis van de tokenacquisitiepatronen die door ADAL API's worden verstrekt:

- **AcquireTokenSilent**: Client probeert een token in stilte te krijgen (geen gebruikersinterface) en kan mislukken als ADAL niet succesvol is. 
- **AcquireToken**: Client kan proberen stille acquisitie, maar kan ook interactieve verzoeken die aanmelden vereisen uit te voeren.

> [!TIP]
> Het is een goed idee om alle fouten en uitzonderingen te registreren bij het gebruik van ADAL en Azure AD. Logboeken zijn niet alleen nuttig voor het begrijpen van de algehele status van uw toepassing, maar zijn ook belangrijk bij het debuggen van bredere problemen. Hoewel uw toepassing kan herstellen van bepaalde fouten, kunnen ze wijzen op bredere ontwerpproblemen waarvoor codewijzigingen nodig zijn om op te lossen. 
> 
> Wanneer u de foutvoorwaarden in dit document implementeert, moet u de foutcode en -beschrijving registreren om de eerder besproken redenen. Zie de [verwijzing Fout en logboekregistratie](#error-and-logging-reference) voor voorbeelden van logboekregistratiecode. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent probeert een token te krijgen met de garantie dat de eindgebruiker geen gebruikersinterface (UI) ziet. Er zijn verschillende gevallen waarin stille acquisitie kan mislukken en moet worden afgehandeld via interactieve verzoeken of door een standaardhandler. We duiken in de details van wanneer en hoe elk geval in te zetten in de secties die volgen.

Er is een reeks fouten gegenereerd door het besturingssysteem, waarvoor mogelijk een foutafhandeling vereist die specifiek is voor de toepassing. Zie de sectie Fouten in fouten in [Fout en logboekregistratie voor](#error-and-logging-reference)meer informatie . 

### <a name="application-scenarios"></a>Toepassingsscenario's

- [Native clienttoepassingen](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, .NET Desktop of Xamarin)
- [Webclienttoepassingen](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) die een [resource](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) aanroepen (.NET)

### <a name="error-cases-and-actionable-steps"></a>Foutgevallen en bruikbare stappen

Fundamenteel zijn er twee gevallen van AcquireTokenSilent-fouten:

| Case | Beschrijving |
|------|-------------|
| **Case 1**: Fout is oplosbaar met een interactieve aanmelding | Voor fouten veroorzaakt door een gebrek aan geldige tokens, is een interactieve aanvraag noodzakelijk. In het bijzonder vereisen cache-opzoeking en een ongeldig/verlopen vernieuwingstoken een AcquireToken-gesprek om op te lossen.<br><br>In deze gevallen moet de eindgebruiker worden gevraagd zich aan te melden. De toepassing kan ervoor kiezen om een interactieve aanvraag onmiddellijk te doen, na interactie tussen eindgebruikers (zoals het raken van een aanmeldingsknop) of later. De keuze is afhankelijk van het gewenste gedrag van de toepassing.<br><br>Zie de code in de volgende sectie voor dit specifieke geval en de fouten die het diagnosticeren.|
| **Case 2**: Fout is niet oplosbaar met een interactieve aanmelding | Voor netwerk- en tijdelijke fouten of andere fouten lost het uitvoeren van een interactieve AcquireToken-aanvraag het probleem niet op. Onnodige interactieve aanmeldingsprompts kunnen eindgebruikers ook frustreren. ADAL probeert automatisch een enkele poging voor de meeste fouten op AcquireTokenSilent-fouten.<br><br>De clientapplicatie kan ook op een later tijdstip een nieuwe poging doen, maar wanneer en hoe is afhankelijk van het toepassingsgedrag en de gewenste gebruikerservaring. De toepassing kan bijvoorbeeld een AcquireTokenSilent opnieuw proberen na een paar minuten, of als reactie op een actie van de eindgebruiker. Een onmiddellijke poging zal resulteren in de toepassing wordt beperkt, en mag niet worden geprobeerd.<br><br>Een volgende poging om niet met dezelfde fout te falen, betekent niet dat de client een interactieve aanvraag moet doen met AcquireToken, omdat de fout niet wordt opgelost.<br><br>Zie de code in de volgende sectie voor dit specifieke geval en de fouten die het diagnosticeren. |

### <a name="net"></a>.NET

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- overgenomenTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [afgeschaft] overnameTokenSilent(...)
- [afgeschaft] overnameTokenByRefreshToken(...) 

Uw code zou als volgt worden geïmplementeerd:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- acquireTokenSilentSync(...)
- overgenomenTokenSilentAsync(...)
- [afgeschaft] overnameTokenSilent(...)

Uw code zou als volgt worden geïmplementeerd:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- acquireTokenSilentWithResource(...)

Uw code zou als volgt worden geïmplementeerd:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken is de standaard ADAL-methode die wordt gebruikt om tokens te krijgen. In gevallen waarin gebruikersidentiteit vereist is, probeert AcquireToken eerst een token in stilte te krijgen en geeft u de gebruikersinterface indien nodig weer (tenzij PromptBehavior.Never wordt doorgegeven). In gevallen waarin toepassingsidentiteit vereist is, probeert AcquireToken een token te krijgen, maar geeft u geen gebruikersinterface weer omdat er geen eindgebruiker is. 

Bij het verwerken van AcquireToken-fouten is foutafhandeling afhankelijk van het platform en het scenario dat de toepassing probeert te bereiken. 

Het besturingssysteem kan ook een set fouten genereren, waarvoor foutafhandeling afhankelijk van de specifieke toepassing vereist is. Zie 'Fouten in het besturingssysteem' in [Fout en logboekregistratiereferentie](#error-and-logging-reference)voor meer informatie. 

### <a name="application-scenarios"></a>Toepassingsscenario's

- Native clienttoepassingen (iOS, Android, .NET Desktop of Xamarin)
- Webtoepassingen die een bron-API aanroepen (.NET)
- Toepassingen met één pagina (JavaScript)
- Service-to-Service-toepassingen (.NET, Java)
  - Alle scenario's, ook namens
  - Namens specifieke scenario's

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Foutgevallen en bruikbare stappen: native clienttoepassingen

Als u een native clienttoepassing bouwt, zijn er een paar foutafhandelingsaanvragen die betrekking hebben op netwerkproblemen, tijdelijke fouten en andere platformspecifieke fouten. In de meeste gevallen moet een toepassing geen onmiddellijke pogingen uitvoeren, maar wachten op interactie tussen eindgebruikers die een aanmelding vragen. 

Er zijn een paar speciale gevallen waarin een enkele poging kan het probleem op te lossen. Bijvoorbeeld wanneer een gebruiker gegevens op een apparaat moet inschakelen of de download van de Azure AD-broker moet voltooien na de eerste fout. 

In geval van uitval kan een toepassing gebruikersinterface presenteren zodat de eindgebruiker enige interactie kan uitvoeren die een nieuwe poging vraagt. Als het apparaat bijvoorbeeld is mislukt voor een offlinefout, probeert u de knop 'Opnieuw aanmelden' om een AcquireToken opnieuw te proberen in plaats van de fout onmiddellijk opnieuw te proberen. 

Foutafhandeling in native toepassingen kan worden gedefinieerd door twee gevallen:

|  |  |
|------|-------------|
| **Zaak 1**:<br>Niet-opnieuw probeerbare fout (de meeste gevallen) | 1. Probeer niet onmiddellijk opnieuw te proberen. Presenteer de gebruikersinterface van de eindgebruiker op basis van de specifieke fout die een nieuwe poging aanroept (bijvoorbeeld 'Probeer u opnieuw aan te melden' of 'Azure AD-broker-toepassing downloaden'). |
| **Zaak 2**:<br>Opnieuw probeerbare fout | 1. Voer een enkele poging uit, omdat de eindgebruiker mogelijk een status heeft ingevoerd die tot een succes leidt.<br><br>2. Als opnieuw proberen mislukt, presenteert u de gebruikersinterface van de eindgebruiker op basis van de specifieke fout die een nieuwe poging oproept ("Probeer u opnieuw aan te melden", 'Azure AD-broker-app downloaden', enz.). |

> [!IMPORTANT]
> Als een gebruikersaccount in een stille oproep wordt doorgegeven aan ADAL en mislukt, kan de eindgebruiker zich met het daaropvolgende interactieve verzoek aanmelden met een ander account. Nadat een succesvolle AcquireToken een gebruikersaccount heeft gebruikt, moet de toepassing controleren of de aangemelde gebruiker overeenkomt met het lokale gebruikersobject van de toepassing. Een mismatch genereert geen uitzondering (behalve in doelstelling C), maar moet worden overwogen in gevallen waarin een gebruiker lokaal bekend is vóór de verificatieverzoeken (zoals een mislukte stille oproep).
>

#### <a name="net"></a>.NET

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met alle niet-stille AcquireToken(...) ADAL-methoden, *met uitzondering van*: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync(..., UserAssertion,...)   

Uw code zou als volgt worden geïmplementeerd:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET heeft een extra overweging omdat het PromptBehavior ondersteunt.Never, dat gedrag heeft zoals AcquireTokenSilent.
>

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- acquireToken(..., PromptBehavior.Never)

Uw code zou als volgt worden geïmplementeerd:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met alle niet-stille AcquireToken(...) ADAL-methoden. 

Uw code zou als volgt worden geïmplementeerd:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met alle niet-stille AcquireToken(...) ADAL-methoden. 

Uw code zou als volgt worden geïmplementeerd:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Foutgevallen en bruikbare stappen: webtoepassingen die een bron-API aanroepen (.NET)

Als u een .NET-webapp bouwt die een token aanroept met behulp van een autorisatiecode voor een resource, is de enige vereiste code een standaardhandler voor de algemene aanvraag. 

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- AcquireTokenByAuthorizationCodeAsync(...)

Uw code zou als volgt worden geïmplementeerd:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Foutgevallen en bruikbare stappen: toepassingen met één pagina (adal.js)

Als u een toepassing met één pagina bouwt met Adal.js met AcquireToken, is de foutafhandelingscode vergelijkbaar met die van een typische stille oproep. Specifiek in adal.js toont AcquireToken nooit een gebruikersinterface. 

Een mislukte AcquireToken heeft de volgende gevallen:

|  |  |
|------|-------------|
| **Zaak 1**:<br>Oplosbaar met een interactief verzoek | 1. Als login() mislukt, niet onmiddellijk opnieuw proberen. Probeer het alleen opnieuw na de actie van de gebruiker vraagt om een nieuwe poging.|
| **Zaak 2**:<br>Niet oplosbaar met een interactief verzoek. Fout is opnieuw te proberen. | 1. Voer een enkele poging uit omdat de hoofdgroep van de eindgebruiker een status heeft ingevoerd die resulteert in een succes.<br><br>2. Als opnieuw proberen mislukt, stelt u de eindgebruiker een actie voor op basis van de specifieke fout die een nieuwe poging kan oproepen ('Probeer u opnieuw aan te melden'). |
| **Zaak 3**:<br>Niet oplosbaar met een interactief verzoek. Fout is niet opnieuw te proberen. | 1. Probeer niet onmiddellijk opnieuw te proberen. Presenteer de eindgebruiker met een actie op basis van de specifieke fout die een nieuwe poging kan oproepen ('Probeer je opnieuw aan te melden'). |

Uw code zou als volgt worden geïmplementeerd:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Foutgevallen en bruikbare stappen: service-to-service-toepassingen (alleen.NET)

Als u een service-to-service-toepassing bouwt die AcquireToken gebruikt, zijn er een paar belangrijke fouten die uw code moet verwerken. Het enige gebruik van falen is om de fout terug te sturen naar de bel-app (voor namens-namens-gevallen) of een strategie voor nieuwe try toe te passen. 

#### <a name="all-scenarios"></a>Alle scenario 's

Voor *alle* service-to-service toepassingsscenario's, ook namens:

- Probeer niet onmiddellijk opnieuw te proberen. ADAL probeert een enkele poging voor bepaalde mislukte aanvragen. 
- Alleen doorgaan met proberen nadat een gebruiker of app actie wordt gevraagd een nieuwe poging. Een daemon-toepassing die wel werkt op een bepaald ingestelde interval, moet bijvoorbeeld wachten tot het volgende interval opnieuw wordt geprobeerd.

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Uw code zou als volgt worden geïmplementeerd:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Namens scenario's

Voor *namens service-to-service* toepassingsscenario's.

De volgende richtlijnen geven voorbeelden voor foutafhandeling in combinatie met ADAL-methoden: 

- AcquireTokenAsync(..., UserAssertion, ...)

Uw code zou als volgt worden geïmplementeerd:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

We hebben een [compleet monster](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) gemaakt dat dit scenario aantoont.

## <a name="error-and-logging-reference"></a>Fout- en logboekregistratieverwijzing

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Registreren van persoonlijke identificeerbare informatie & identificeerbare informatie van de organisatie 
Standaard legt ADAL-logboekregistratie geen persoonlijke identificeerbare informatie of identificeerbare informatie vast. Met de bibliotheek kunnen app-ontwikkelaars dit inschakelen via een setter in de klasse Logger. Door het registreren van persoonlijke identificeerbare informatie of organisatorisch identificeerbare informatie, neemt de app de verantwoordelijkheid voor het veilig verwerken van zeer gevoelige gegevens en het voldoen aan eventuele wettelijke vereisten.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-bibliotheekfouten

Om specifieke ADAL-fouten te onderzoeken, is de broncode in de [azure-activedirectory-library-for-dotnet-repository](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) de beste foutverwijzing.

#### <a name="guidance-for-error-logging-code"></a>Richtlijnen voor foutlogboekregistratiecode

ADAL .NET logging verandert afhankelijk van het platform waar aan wordt gewerkt. Raadpleeg de [logboekregistratiewiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) voor code voor het inschakelen van logboekregistratie.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-bibliotheekfouten

Om specifieke ADAL-fouten te onderzoeken, is de broncode in de [azure-activedirectory-library-for-android-repository](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) de beste foutverwijzing.

#### <a name="operating-system-errors"></a>Fouten in het besturingssysteem

Android OS-fouten worden blootgesteld via AuthenticationException in ADAL, zijn identificeerbaar als "SERVER_INVALID_REQUEST", en kunnen verder worden korrelig door de foutbeschrijvingen. 

Raadpleeg de [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki)voor een volledige lijst met veelvoorkomende fouten en welke stappen u moet nemen wanneer uw app of eindgebruikers deze tegenkomen. 

#### <a name="guidance-for-error-logging-code"></a>Richtlijnen voor foutlogboekregistratiecode

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL-bibliotheekfouten

Om specifieke ADAL-fouten te onderzoeken, is de broncode in de [azure-activedirectory-library-for-objc-repository](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) de beste foutverwijzing.

#### <a name="operating-system-errors"></a>Fouten in het besturingssysteem

iOS-fouten kunnen ontstaan tijdens het aanmelden wanneer gebruikers webweergaven gebruiken en de aard van verificatie. Dit kan worden veroorzaakt door omstandigheden zoals SSL-fouten, time-outs of netwerkfouten:

- Voor Het delen van rechten zijn aanmeldingen niet blijvend en wordt de cache leeg weergegeven. U het oplossen door de volgende coderegel toe te voegen aan de sleutelhanger:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Voor de nsurldomeinset van fouten wordt de actie gewijzigd, afhankelijk van de app-logica. Zie de [referentiedocumentatie NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) voor specifieke exemplaren die kunnen worden verwerkt.
- Zie [Algemene problemen van ADAL Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) voor de lijst met veelvoorkomende fouten die worden bijgehouden door het ADAL Objective-C-team.

#### <a name="guidance-for-error-logging-code"></a>Richtlijnen voor foutlogboekregistratiecode

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Richtlijnen voor foutlogboekregistratiecode - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Gerelateerde inhoud

* [Azure AD-verificatiebibliotheken][AAD-Auth-Libraries]
* [Azure AD-verificatiescenario's][AAD-Auth-Scenarios]
* [Toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps]

Gebruik de volgende opmerkingensectie om feedback te geven en ons te helpen onze inhoud te verfijnen en vorm te geven.

[![Toont de knop Aanmelden met Microsoft][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

