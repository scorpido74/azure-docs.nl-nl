---
title: ADAL naar MSAL migratiegids voor Android | Azure
description: Meer informatie over het migreren van uw Azure Active Directory Authentication Library (ADAL) Android-app naar de Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084051"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>ADAL naar MSAL migratiegids voor Android

In dit artikel worden wijzigingen beschreven die u moet aanbrengen om een app te migreren die de Azure Active Directory Authentication Library (ADAL) gebruikt om de MsAL (Microsoft Authentication Library) te gebruiken.

## <a name="difference-highlights"></a>Verschil hoogtepunten

ADAL werkt met het Azure Active Directory v1.0-eindpunt. De Microsoft Authentication Library (MSAL) werkt met het Microsoft-identiteitsplatform, voorheen bekend als het Azure Active Directory v2.0-eindpunt. Het Microsoft-identiteitsplatform verschilt van Azure Active Directory v1.0 in die zin dat het:

Ondersteunt:
  - Organisatie-identiteit (Azure Active Directory)
  - Niet-organisatorische identiteiten zoals Outlook.com, Xbox Live, enzovoort
  - (Alleen B2C) Federatieve login met Google, Facebook, Twitter en Amazon

- Is normen compatibel met:
  - OAuth v2.0
  - OpenID Connect (OIDC)

De MSAL public API introduceert belangrijke veranderingen, waaronder:

- Een nieuw model voor toegang tot tokens:
  - ADAL biedt toegang tot `AuthenticationContext`tokens via de , die de server vertegenwoordigt. MSAL biedt toegang tot `PublicClientApplication`tokens via de , die de client vertegenwoordigt. Clientontwikkelaars hoeven geen nieuw `PublicClientApplication` exemplaar te maken voor elke autoriteit waarmee ze moeten communiceren. Er `PublicClientApplication` is slechts één configuratie vereist.
  - Ondersteuning voor het aanvragen van toegangstokens met behulp van scopes naast resource-id's.
  - Ondersteuning voor incrementele toestemming. Ontwikkelaars kunnen scopes aanvragen omdat de gebruiker steeds meer functionaliteit in de app heeft, inclusief die welke niet zijn opgenomen tijdens de registratie van apps.
  - Autoriteiten worden niet meer gevalideerd op run-time. In plaats daarvan verklaart de ontwikkelaar een lijst van 'bekende autoriteiten' tijdens de ontwikkeling.
- Token API-wijzigingen:
  - In ADAL `AcquireToken()` doet u eerst een zwijgverzoek. Lukt dat niet, dan doet het een interactief verzoek. Dit gedrag resulteerde in sommige ontwikkelaars `AcquireToken`vertrouwen alleen op , wat resulteerde in de gebruiker onverwacht gevraagd voor referenties op keer. MSAL vereist dat ontwikkelaars opzettelijk zijn wanneer de gebruiker een ui-prompt ontvangt.
    - `AcquireTokenSilent`altijd resulteert in een stille verzoek dat ofwel slaagt of mislukt.
    - `AcquireToken`resulteert altijd in een verzoek dat de gebruiker via de gebruikersinterface vraagt.
- MSAL ondersteunt aanmelden vanuit een standaardbrowser of een ingesloten webweergave:
  - Standaard wordt de standaardbrowser op het apparaat gebruikt. Hierdoor kan MSAL verificatiestatus (cookies) gebruiken die mogelijk al aanwezig zijn voor een of meer aangemelde accounts. Als er geen verificatiestatus aanwezig is, resulteert het verifiëren tijdens autorisatie via MSAL in dat de verificatiestatus (cookies) wordt gemaakt ten behoeve van andere webtoepassingen die in dezelfde browser worden gebruikt.
- Nieuw uitzonderingsmodel:
  - Uitzonderingen definiëren duidelijker het type fout dat is opgetreden en wat de ontwikkelaar moet doen om deze op te lossen.
- MSAL ondersteunt parameterobjecten voor `AcquireToken` en `AcquireTokenSilent` aanroepen.
- MSAL ondersteunt declaratieve configuratie voor:
  - Client-ID, Redirect URI.
  - Ingesloten versus standaardbrowser
  - Autoriteiten
  - HTTP-instellingen zoals een time-out voor lezen en verbinding

## <a name="your-app-registration-and-migration-to-msal"></a>Uw app-registratie en migratie naar MSAL

U hoeft uw bestaande app-registratie niet te wijzigen om MSAL te gebruiken. Als u wilt profiteren van incrementele/progressieve toestemming, moet u mogelijk de registratie controleren om de specifieke scopes te identificeren die u stapsgewijs wilt aanvragen. Meer informatie over scopes en incrementele toestemming volgt.

In uw app-registratie in de portal ziet u een tabblad **API-machtigingen.** Dit bevat een lijst met de API's en machtigingen (scopes) waarvan uw app momenteel is geconfigureerd om toegang tot te vragen. Het toont ook een lijst met de scopenamen die aan elke API-machtiging zijn gekoppeld.

### <a name="user-consent"></a>Toestemming van de gebruiker

Met ADAL en het AAD v1-eindpunt werd toestemming van de gebruiker verleend voor resources die ze bezitten bij het eerste gebruik. Met MSAL en het Microsoft-identiteitsplatform kan stapsgewijs toestemming worden gevraagd. Incrementele toestemming is handig voor machtigingen die een gebruiker kan overwegen hoge bevoegdheden, of kan anderszins vraag als niet voorzien van een duidelijke uitleg waarom de toestemming nodig is. In ADAL kunnen deze machtigingen ertoe hebben geleid dat de gebruiker zich niet heeft aanmelden bij uw app.

> [!TIP]
> We raden u aan incrementele toestemming te gebruiken in scenario's waarin u uw gebruiker extra context moet geven over waarom uw app een toestemming nodig heeft.

### <a name="admin-consent"></a>toestemming van de beheerder

Organisatiebeheerders kunnen instemmen met machtigingen die uw toepassing nodig heeft namens alle leden van hun organisatie. Sommige organisaties staan alleen beheerders toe om toestemming te geven voor toepassingen. Beheerderstoestemming vereist dat u alle API-machtigingen en -scopes die door uw toepassing worden gebruikt, opneemt in uw app-registratie.

> [!TIP]
> Hoewel u een scope aanvragen met MSAL voor iets dat niet in uw app-registratie is opgenomen, raden we u aan uw app-registratie bij te werken om alle bronnen en scopes op te nemen waaraan een gebruiker ooit toestemming zou kunnen verlenen.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migreren van resource---geïdentificeerde gegevens naar scopes

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autorisatie verifiëren en aanvragen voor alle machtigingen bij het eerste gebruik

Als u ADAL momenteel gebruikt en geen incrementele toestemming hoeft te gebruiken, u `acquireToken` msal `AcquireTokenParameter` de eenvoudigste manier gebruiken door een aanvraag in te dienen met behulp van het nieuwe object en de resource-id-waarde in te stellen.

> [!CAUTION]
> Het is niet mogelijk om zowel scopes als een resource id in te stellen. Een poging om beide `IllegalArgumentException`in te stellen zal resulteren in een .

 Dit zal resulteren in hetzelfde v1-gedrag dat u gebruikt. Alle machtigingen die in uw app-registratie worden aangevraagd, worden tijdens de eerste interactie van de gebruiker gevraagd.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Alleen machtigingen verifiëren en aanvragen als dat nodig is

Als u wilt profiteren van incrementele toestemming, maakt u een lijst met machtigingen (scopes) die uw app gebruikt vanuit uw app-registratie en organiseert u deze in twee lijsten op basis van:

- Welke scopes u wilt aanvragen tijdens de eerste interactie van de gebruiker met uw app tijdens het aanmelden.
- De machtigingen die zijn gekoppeld aan een belangrijke functie van uw app die u ook aan de gebruiker moet uitleggen.

Zodra u de scopes hebt georganiseerd, organiseert u elke lijst op basis van welke bron (API) u een token wilt aanvragen. Evenals alle andere scopes die u wilt dat de gebruiker te machtigen op hetzelfde moment.

Het parametersobject dat wordt gebruikt om uw aanvraag te doen bij MSAL-ondersteuning:

- `Scope`: De lijst met scopes waarvoor u een autorisatie wilt aanvragen en die een toegangstoken wilt ontvangen.
- `ExtraScopesToConsent`: Een extra lijst met scopes waarvoor u autorisatie wilt aanvragen terwijl u een toegangstoken aanvraagt voor een andere bron. Met deze lijst met scopes u het aantal keren minimaliseren dat u gebruikersautorisatie moet aanvragen. Dat betekent minder toestemming of toestemmingsprompts.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migreren van AuthenticationContext naar PublicClientApplications

### <a name="constructing-publicclientapplication"></a>PublicClientApplication bouwen

Wanneer u MSAL gebruikt, instantiate u een `PublicClientApplication`. Dit object modelleert uw app-identiteit en wordt gebruikt om aanvragen te doen bij een of meer autoriteiten. Met dit object configureert u uw clientidentiteit, leidt u URI, standaardautoriteit om of u de apparaatbrowser wilt gebruiken versus ingesloten webweergave, het logboekniveau en meer.

U dit object declaratief configureren met JSON, dat u als bestand opgeeft of als bron opslaat in uw APK.

Hoewel dit object geen singleton is, `Executors` gebruikt het intern gedeeld voor zowel interactieve als stille aanvragen.

### <a name="business-to-business"></a>Business to Business

In ADAL vereist elke organisatie waarvan u toegangstokens `AuthenticationContext`aanvraagt een apart exemplaar van de . Bij MSAL is dit niet langer een vereiste. U de autoriteit opgeven van waaruit u een token wilt aanvragen als onderdeel van uw stille of interactieve aanvraag.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migreren van autoriteitsvalidatie naar bekende autoriteiten

MSAL heeft geen vlag om authority validatie in te schakelen of uit te schakelen. Authority validatie is een functie in ADAL, en in de vroege releases van MSAL, dat voorkomt dat uw code tokens aanvragen bij een mogelijk kwaadaardige autoriteit. MSAL haalt nu een lijst op met autoriteiten die bekend zijn bij Microsoft en voegt die lijst samen met de autoriteiten die u in uw configuratie hebt opgegeven.

> [!TIP]
> Als u een B2C-gebruiker (Azure Business to Consumer) bent, hoeft u de validatie van de autoriteit niet langer uit te schakelen. Voeg in plaats daarvan elk van de door u ondersteunde Azure AD B2C-beleidsregels op als autoriteiten in uw MSAL-configuratie.

Als u een autoriteit probeert te gebruiken die niet bekend is bij Microsoft en `UnknownAuthorityException`niet in uw configuratie is opgenomen, krijgt u een .

### <a name="logging"></a>Logboekregistratie
U logboekregistratie nu declaratief configureren als onderdeel van uw configuratie, zoals dit:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migreren van UserInfo naar Account

In ADAL `AuthenticationResult` biedt `UserInfo` het object een object dat wordt gebruikt om informatie over het geverifieerde account op te halen. De term "gebruiker", wat een mens of software agent betekende, werd toegepast op een manier die het moeilijk maakte om te communiceren dat sommige apps een enkele gebruiker (of een menselijke of software agent) die meerdere accounts heeft ondersteunt.

Overweeg een bankrekening. U hebt mogelijk meer dan één account bij meer dan één financiële instelling. Wanneer u een account opent, krijgt u (de gebruiker) referenties, zoals een pincode & ATM-kaart, die worden gebruikt om toegang te krijgen tot uw saldo, factuurbetalingen, enzovoort, voor elke account. Deze referenties kunnen alleen worden gebruikt bij de financiële instelling die ze heeft uitgegeven.

Naar analogie, zoals accounts bij een financiële instelling, accounts in het Microsoft-identiteitsplatform worden benaderd met behulp van referenties. Deze referenties zijn geregistreerd bij of uitgegeven door Microsoft. Of door Microsoft namens een organisatie.

Wanneer het Microsoft-identiteitsplatform verschilt van een financiële instelling, in deze analogie, is dat het Microsoft-identiteitsplatform een framework biedt waarmee een gebruiker één account en de bijbehorende referenties kan gebruiken om toegang te krijgen tot bronnen die behoren tot meerdere personen en organisaties. Dit is als de mogelijkheid om een kaart uitgegeven door een bank, bij nog een andere financiële instelling te gebruiken. Dit werkt omdat alle betrokken organisaties het Microsoft-identiteitsplatform gebruiken, waardoor één account kan worden gebruikt voor meerdere organisaties. Hier volgt een voorbeeld:

Sam werkt voor Contoso.com maar beheert virtuele Azure-machines die tot Fabrikam.com behoren. Om fabrikam's virtuele machines te beheren, moet hij er toegang toe hebben. Deze toegang kan worden verleend door sam's account toe te voegen aan Fabrikam.com en zijn account een rol te geven die hem in staat stelt om met de virtuele machines te werken. Dit zou worden gedaan met de Azure-portal.

Het toevoegen van Sam's Contoso.com-account als lid van Fabrikam.com zou resulteren in het maken van een nieuwe record in Fabrikam.com's Azure Active Directory voor Sam. Sam's record in Azure Active Directory staat bekend als een gebruikersobject. In dit geval wijst dat object van de gebruiker terug naar het gebruikersobject van Sam in Contoso.com. Sam's Fabrikam user object is de lokale vertegenwoordiging van Sam, en zou worden gebruikt om informatie over het account gekoppeld aan Sam op te slaan in de context van Fabrikam.com. In Contoso.com, Sam's titel is Senior DevOps Consultant. In Fabrikam, Sam's titel is Contractor-Virtual Machines. In Contoso.com is Sam niet verantwoordelijk, noch geautoriseerd, om virtuele machines te beheren. In Fabrikam.com is dat zijn enige baan. Toch sam heeft nog steeds slechts een set van referenties bij te houden, die de referenties uitgegeven door Contoso.com.

Zodra een `acquireToken` geslaagde oproep is gemaakt, `IAccount` ziet u een verwijzing `acquireTokenSilent` naar een object dat in latere aanvragen kan worden gebruikt.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Als u een app hebt die toegang heeft tot claims over een account van `IAccount` elk `IMultiTenantAccount`van de tenants waarin het account is vertegenwoordigd, u objecten casten naar. Deze interface biedt `ITenantProfiles`een kaart van , ingetoetst door tenant-ID, waarmee u toegang hebt tot de claims die behoren tot het account in elk van de tenants waar u een token hebt aangevraagd, ten opzichte van de lopende rekening.

De vorderingen aan de `IAccount` `IMultiTenantAccount` basis van de en bevatten altijd de vorderingen van de huurder van de woning. Als u nog geen aanvraag voor een token binnen de woninghuurder hebt ingediend, is deze collectie leeg.

## <a name="other-changes"></a>Andere wijzigingen

### <a name="use-the-new-authenticationcallback"></a>De nieuwe AuthenticationCallback gebruiken

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Migreren naar de nieuwe uitzonderingen

In ADAL is er één type `AuthenticationException`uitzondering, dat een methode `ADALError` bevat voor het ophalen van de enumwaarde.
In MSAL is er een hiërarchie van uitzonderingen en elk heeft zijn eigen set bijbehorende specifieke foutcodes.

Lijst van MSAL-uitzonderingen

|Uitzondering  | Beschrijving  |
|---------|---------|
| `MsalException`     | Standaard gecontroleerde uitzondering die door MSAL wordt gegooid.  |
| `MsalClientException`     | Gegooid als de fout clientkant is. |
| `MsalArgumentException`     | Als een of meer invoerargumenten ongeldig zijn, wordt deze gegooide als een of meer invoerargumenten ongeldig zijn. |
| `MsalClientException`     | Gegooid als de fout clientkant is. |
| `MsalServiceException`     | Gegooid als de fout serverzijde is. |
| `MsalUserCancelException`     | Gegooid als de gebruiker de verificatiestroom heeft geannuleerd.  |
| `MsalUiRequiredException`     | Gegooid als het token niet in stilte kan worden vernieuwd.  |
| `MsalDeclinedScopeException`     | Thrown if a of more requested scopes were declined by the server.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Thrown if the resource has MAMCA protection policy enabled. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError to MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>ADAL-logboekregistratie voor MSAL-logboekregistratie

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
