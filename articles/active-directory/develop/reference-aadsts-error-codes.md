---
title: Azure AD-verificatie & foutcodes voor autorisatie
description: Meer informatie over de AADSTS-foutcodes die worden geretourneerd van de Azure AD Security Token Service (STS).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/19/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 89240102837b65ed2a09d9f4865ad47ee5d5afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154556"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Foutcodes azure AD-verificatie en autorisatie

Op zoek naar informatie over de AADSTS-foutcodes die worden geretourneerd uit de Azure Active Directory (Azure AD) security token service (STS)? Lees dit document om AADSTS-foutbeschrijvingen, oplossingen en enkele voorgestelde tijdelijke oplossingen te vinden.

> [!NOTE]
> Deze informatie is voorlopig en kan worden gewijzigd. Heb je een vraag of kun je niet vinden wat je zoekt? Maak een GitHub-probleem of zie [ondersteunings- en helpopties voor ontwikkelaars](active-directory-develop-help-support.md) om meer te weten te komen over andere manieren waarop u hulp en ondersteuning krijgen.
>
> Deze documentatie is bedoeld voor richtlijnen voor ontwikkelaars en beheerders, maar mag nooit door de client zelf worden gebruikt. Foutcodes kunnen op elk moment worden gewijzigd om meer gedetailleerde foutmeldingen te kunnen geven die bedoeld zijn om de ontwikkelaar te helpen bij het bouwen van de toepassing. Apps die afhankelijk zijn van tekst- of foutcodenummers worden na verloop van tijd verbroken.

## <a name="lookup-current-error-code-information"></a>Huidige foutcode-informatie opzoeken
Foutcodes en berichten kunnen worden gewijzigd.  Bekijk de `https://login.microsoftonline.com/error` pagina voor de meest recente informatie om aadsts-foutbeschrijvingen, oplossingen en enkele voorgestelde tijdelijke oplossingen te vinden.  

Zoek op het numerieke deel van de geretourneerde foutcode.  Als u bijvoorbeeld de foutcode "AADSTS16000" hebt `https://login.microsoftonline.com/error` ontvangen, zoekt u naar '16000'.  U ook rechtstreeks naar een specifieke fout linken `https://login.microsoftonline.com/error?code=16000`door het foutcodenummer toe te voegen aan de URL:.

## <a name="aadsts-error-codes"></a>AADSTS-foutcodes

| Fout | Beschrijving |
|---|---|
| AADSTS16000 | SelectUserAccount - Dit is een onderbreking die wordt gegenereerd door Azure AD, wat resulteert in gebruikersinterface waarmee de gebruiker kan kiezen uit meerdere geldige SSO-sessies. Deze fout komt vrij vaak voor en `prompt=none` kan worden teruggestuurd naar de toepassing als dit is opgegeven. |
| AADSTS16001 | UserAccountSelectionInvalid - U ziet deze fout als de gebruiker klikt op een tegel die de logica voor sessieselectie heeft geweigerd. Wanneer deze fout wordt geactiveerd, kan de gebruiker herstellen door uit een bijgewerkte lijst met tegels/sessies te kiezen of door een ander account te kiezen. Deze fout kan optreden als gevolg van een codedefect of raceconditie. |
| AADSTS16002 | AppSessionSelectionInvalid - Aan de door de app opgegeven SID-vereiste is niet voldaan.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - Geeft aan dat de gebruiker niet expliciet aan de tenant is toegevoegd. |
| AADSTS17003 | CredentialKeyProvisioningFailed - Azure AD kan de gebruikerssleutel niet inrichten. |
| AADSTS20001 | WsFedSignInResponseError - Er is een probleem met uw federatieve identity provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS20012 | WsFedMessageInvalid - Er is een probleem met uw federatieve identiteitsprovider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS20033 | FedMetadataInvalidTenantName - Er is een probleem met uw federatieve identity provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40008 | OAuth2IdPUntryableServerError - Er is een probleem met uw federatieve Identity Provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - Er is een probleem met uw federatieve identiteitsprovider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40010 | OAuth2IdPRetryableServerError - Er is een probleem met uw federatieve Identity Provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Er is een probleem met uw federatieve Identity Provider. Neem contact op met uw IDP om dit probleem op te lossen. |
| AADSTS50000 | TokenIssuanceError - Er is een probleem met de aanmeldingsservice. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om dit probleem op te lossen. |
| AADSTS50001 | Ongeldige resource - De resource is uitgeschakeld of bestaat niet. Controleer de code van uw app om te controleren of u de exacte bron-URL hebt opgegeven voor de bron die u probeert te openen.  |
| AADSTS50002 | NotAllowedTenant - Aanmelden is mislukt vanwege een beperkte proxy-toegang op de tenant. Als het uw eigen tenantbeleid is, u uw beperkte tenantinstellingen wijzigen om dit probleem op te lossen. |
| AADSTS50003 | MissingSigningKey - Aanmelden is mislukt vanwege een ontbrekende ondertekeningssleutel of -certificaat. Dit kan zijn omdat er geen ondertekeningssleutel is geconfigureerd in de app. Bekijk de resoluties die [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured)worden beschreven op . Als u nog steeds problemen ziet, neemt u contact op met de eigenaar van de app of een app-beheerder. |
| AADSTS50005 | DevicePolicyError - De gebruiker heeft geprobeerd in te loggen op een apparaat vanaf een platform dat momenteel niet wordt ondersteund via het beleid voor voorwaardelijke toegang. |
| AADSTS50006 | Ongeldig handtekening - Verificatie van handtekeningen is mislukt vanwege een ongeldige handtekening. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - Het partnerversleutelingscertificaat is niet gevonden voor deze app. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met Microsoft om dit te laten repareren. |
| AADSTS50008 | InvalidSamlToken - SAML-bewering ontbreekt of is verkeerd geconfigureerd in het token. Neem contact op met uw federatieprovider. |
| AADSTS50010 | AudienceUriValidationFailed - Audience URI-validatie voor de app is mislukt omdat er geen tokendoelgroepen zijn geconfigureerd. |
| AADSTS50011 | Ongeldige antwoorden - Het antwoordadres ontbreekt, verkeerd geconfigureerd of komt niet overeen met antwoordadressen die zijn geconfigureerd voor de app.  Als oplossing moet u ervoor zorgen dat dit ontbrekende antwoordadres wordt toegevoegd aan de Azure Active Directory-toepassing of dat iemand met de machtigingen om uw toepassing in Active Directory te beheren, dit voor u doet.|
| AADSTS50012 | Verificatie mislukt - Verificatie is mislukt om een van de volgende redenen:<ul><li>De ondernaam van het ondertekeningscertificaat is niet toegestaan</li><li>Er is geen overeenkomend beleid voor vertrouwde autoriteit gevonden voor de geautoriseerde onderwerpnaam</li><li>De certificaatketen is niet geldig</li><li>Het ondertekeningscertificaat is niet geldig</li><li>Beleid is niet geconfigureerd op de tenant</li><li>Duimafdruk van het ondertekeningscertificaat is niet toegestaan</li><li>Clientbewering bevat een ongeldige handtekening</li></ul> |
| AADSTS50013 | Ongeldige bewering - Bewering is ongeldig vanwege verschillende redenen - De tokenuitgever komt niet overeen met de api-versie binnen het geldige tijdsbereik -verlopen -misvormd - Refresh token in de bewering is geen primaire refresh token. |
| AADSTS50014 | GuestUserInPendingState - De inwisseling van de gebruiker is in behandeling. Het gastgebruikersaccount is nog niet volledig gemaakt. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - De gebruiker vereist wettelijke toestemming van de leeftijdsgroep. |
| AADSTS50017 | CertificateValidationFailed - Certificeringvalidatie is mislukt, redenen om de volgende redenen:<ul><li>Kan uitgiftecertificaat niet vinden in de lijst met vertrouwde certificaten</li><li>Verwacht CrlSegment kan niet worden gevonden</li><li>Kan uitgiftecertificaat niet vinden in de lijst met vertrouwde certificaten</li><li>Delta CRL-distributiepunt is geconfigureerd zonder een bijbehorend CRL-distributiepunt</li><li>Kan geldige CRL-segmenten niet ophalen vanwege een time-outprobleem</li><li>Kan CRL niet downloaden</li></ul>Neem contact op met de beheerder van de tenant. |
| AADSTS50020 | UserUnauthorized - Gebruikers zijn onbevoegd om dit eindpunt te bellen. |
| AADSTS50027 | Ongeldig JwtToken - Ongeldig JWT-token om de volgende redenen:<ul><li>bevat geen nonceclaim, subclaim</li><li>onderwerp-id komt niet overeen</li><li>duplicaatclaim in idToken-claims</li><li>onverwachte certificaatverlener</li><li>onverwachte doelgroep</li><li>niet binnen geldig tijdsbereik </li><li>indeling van het token is niet juist</li><li>Handtekeningverificatie van extern ID-token van de verlener is mislukt.</li></ul> |
| AADSTS50029 | Ongeldige URI: domeinnaam bevat ongeldige tekens. Neem contact op met de beheerder van de tenant. |
| AADSTS50032 | WeakRsaKey - Geeft de foutieve gebruiker poging om een zwakke RSA-toets te gebruiken. |
| AADSTS50033 | RetryableError - Geeft een tijdelijke fout aan die niet gerelateerd is aan de databasebewerkingen. |
| AADSTS50034 | UserAccountNotFound - Als u zich wilt aanmelden bij deze toepassing, moet het account worden toegevoegd aan de directory. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - Het zout dat nodig is om een pairwise identifier te genereren ontbreekt in principe. Neem contact op met de beheerder van de tenant. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - Subject mismatches Issuer claim in the client claim. Neem contact op met de beheerder van de tenant. |
| AADSTS50049 | NoSuchInstanceForDiscovery - Onbekende of ongeldige instantie. |
| AADSTS50050 | MisvormdeDiscoveryRequest - De aanvraag is verkeerd vervormd. |
| AADSTS50053 | IdsLocked - Het account is vergrendeld omdat de gebruiker te vaak heeft geprobeerd in te loggen met een onjuiste gebruikersnaam of wachtwoord. |
| AADSTS50055 | InvalidPasswordExpiredPassword - Het wachtwoord is verlopen. |
| AADSTS50056 | Ongeldig of null wachtwoord -Wachtwoord bestaat niet in de winkel voor deze gebruiker. |
| AADSTS50057 | UserDisabled - Het gebruikersaccount is uitgeschakeld. Het account is uitgeschakeld door een beheerder. |
| AADSTS50058 | UserInformationNotProvided - Dit betekent dat een gebruiker niet is aangemeld. Dit is een veelvoorkomende fout die wordt verwacht wanneer een gebruiker niet is geverifieerd en nog niet is aangemeld.</br>Als deze fout wordt aangemoedigd in een SSO-context waarin de gebruiker zich eerder heeft aangemeld, betekent dit dat de SSO-sessie niet is gevonden of ongeldig is.</br>Deze fout kan worden geretourneerd naar de toepassing als prompt=none is opgegeven. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - Tenant-identificerende informatie werd niet gevonden in het verzoek of geïmpliceerd door een verstrekte referenties. De gebruiker kan contact opnemen met de tenantbeheerder om het probleem op te lossen. |
| AADSTS50061 | AfmeldingOngeldig verzoek - Het afmeldingsverzoek is ongeldig. |
| AADSTS50064 | CredentialAuthenticationError - Verificatievalidatie op gebruikersnaam of wachtwoord is mislukt. |
| AADSTS50068 | AanmeldingInitiatorNietDeelnemer - Afmelding is mislukt. De app die zich heeft aangemeld, is geen deelnemer aan de huidige sessie. |
| AADSTS50070 | AfmeldingUnknownSessionIdentifier - Afmelding is mislukt. In het aanmeldingsverzoek is een naam-id opgegeven die niet overeenkomt met de bestaande sessie(en). |
| AADSTS50071 | AfmeldingBerichtverlopen - De afmeldingsaanvraag is verlopen. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - Gebruiker moet zich inschrijven voor tweede factor authenticatie (interactief). |
| AADSTS50074 | UserStrongAuthAuthAuthNRequiredInterrupt - Sterke authenticatie is vereist en de gebruiker heeft de MFA-uitdaging niet doorstaan. |
| AADSTS50076 | UserStrongAuthAuthAuthNRequired - Vanwege een configuratiewijziging door de beheerder of omdat u naar een nieuwe locatie bent verhuisd, moet de gebruiker multi-factor authenticatie gebruiken om toegang te krijgen tot de bron. Probeer opnieuw met een nieuwe geautoriseerde aanvraag voor de resource. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - Vanwege een configuratiewijziging door de beheerder of omdat de gebruiker naar een nieuwe locatie is verhuisd, moet de gebruiker multifactorauthenticatie gebruiken. |
| AADSTS50085 | Vernieuwingstoken vereist een sociale IDP-aanmelding. Laat gebruikers proberen zich opnieuw aan te melden met gebruikersnaam en wachtwoord |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - De service is tijdelijk niet beschikbaar. &Opnieuw. |
| AADSTS50089 | Stroomtoken verlopen, verificatie is mislukt. Laat de gebruiker opnieuw proberen in te melden met gebruikersnaam -wachtwoord. |
| AADSTS50097 | DeviceAuthenticationRequired - Apparaatverificatie is vereist. |
| AADSTS50099 | PKeyAuthInvalidJwtOngeautoriseerd - De JWT-handtekening is ongeldig. |
| AADSTS50105 | EntitlementGrantsNotFound - De aangemelde gebruiker is niet toegewezen aan een rol voor de aangemelde app. Wijs de gebruiker toe aan de app. Voor meer[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)informatie: . |
| AADSTS50107 | InvalidRealmUri - Het gevraagde object van het federatiekoninkrijk bestaat niet. Neem contact op met de beheerder van de tenant. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - Probleem met JWT-header. Neem contact op met de beheerder van de tenant. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - Claimtransformatie bevat ongeldige invoerparameter. Neem contact op met de tenantbeheerder om het beleid bij te werken. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - Aanmelden is onderbroken vanwege een wachtwoordreset of wachtwoordregistratie. |
| AADSTS50126 | OngeldigUserNameOrPassword - Fout valideren van referenties als gevolg van ongeldige gebruikersnaam of wachtwoord. |
| AADSTS50127 | BrokerAppNotInstalled - Gebruiker moet een broker-app installeren om toegang te krijgen tot deze inhoud. |
| AADSTS50128 | Ongeldige domeinnaam - Geen tenant-identificerende informatie gevonden in het verzoek of geïmpliceerd door een verstrekte referenties. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - Workplace join is vereist om het apparaat te registreren. |
| AADSTS50131 | ConditionalAccessFailed - Geeft verschillende fouten in voorwaardelijke toegang aan, zoals de slechte status van het Windows-apparaat, de aanvraag geblokkeerd vanwege verdachte activiteiten, toegangsbeleid of beslissingen over beveiligingsbeleid. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - De sessie is niet geldig vanwege het verlopen van het wachtwoord of recente wachtwoordwijziging. |
| AADSTS50133 | SsoArtifactRevoked - De sessie is niet geldig vanwege het verlopen van het wachtwoord of recente wachtwoordwijziging. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - Verkeerde datacenter. Als u een aanvraag wilt autoriseren die is gestart door een app in de OAuth 2.0-apparaatstroom, moet de machtigingspartij zich in hetzelfde datacenter bevinden waar de oorspronkelijke aanvraag zich bevindt. |
| AADSTS50135 | PasswordChangeCompromisedPassword - Wachtwoordwijziging is vereist vanwege accountrisico's. |
| AADSTS50136 | RedirectMsaSessionToApp - Enkele MSA-sessie gedetecteerd. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - De sessie is ongeldig vanwege een ontbrekend extern vernieuwingstoken. |
| AADSTS50140 | KmsiInterrupt - Deze fout is opgetreden als gevolg van "Houd me aangemeld" onderbreken wanneer de gebruiker was aanmelden. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met correlatie-id, aanvraag-id en een foutcode voor meer informatie. |
| AADSTS50143 | Sessiemismatch - Sessie is ongeldig omdat de gebruikerstenant niet overeenkomt met de domeinhint vanwege verschillende bronnen.  [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met correlatie-id, aanvraag-id en foutcode voor meer informatie. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - Het Active Directory-wachtwoord van de gebruiker is verlopen. Genereer een nieuw wachtwoord voor de gebruiker of laat de gebruiker het selfservice resetprogramma gebruiken om zijn wachtwoord opnieuw in te stellen. |
| AADSTS50146 | MissingCustomSigningKey - Deze app moet worden geconfigureerd met een app-specifieke ondertekeningssleutel. De toepassing is niet met een dergelijke sleutel geconfigureerd, of de sleutel is verlopen of nog niet geldig. |
| AADSTS50147 | MissingCodeChallenge - De grootte van de parameter codechallenge is niet geldig. |
| AADSTS50155 | DeviceAuthenticationFailed - Apparaatverificatie is mislukt voor deze gebruiker. |
| AADSTS50158 | ExternalSecurityChallenge - Externe security uitdaging was niet tevreden. |
| AADSTS50161 | OngeldigExternalSecurityChallengeConfiguration - Claims verzonden door externe provider is niet genoeg of Ontbrekende claim gevraagd naar externe provider. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Kan het verzoek niet verzenden naar de claimprovider. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - De client is in staat om een SSO-token te verkrijgen via de Windows 10-accountextensie, maar het token is niet gevonden in het verzoek of het meegeleverde token is verlopen. |
| AADSTS50169 | InvalidRequestBadRealm - De realm is geen geconfigureerde realm van de huidige servicenaamruimte. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - De externe besturingselementen toewijzing ontbreekt. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - Externe uitdaging wordt niet ondersteund voor passthrough-gebruikers. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - Sessiebesturingselement wordt niet ondersteund voor passthrough-gebruikers. |
| AADSTS50180 | WindowsIntegratedAuthMissing - Geïntegreerde Windows-verificatie is nodig. Schakel de tenant in voor een naadloze eenmalige aanmelding (SSO). |
| AADSTS50187 | DeviceInformationNotProvided - De service kan apparaatverificatie niet uitvoeren. |
| AADSTS50196 | LoopDetected - Er is een clientlus gedetecteerd. Controleer de logica van de app om ervoor te zorgen dat tokencaching wordt geïmplementeerd en dat foutvoorwaarden correct worden verwerkt.  De app heeft te veel van hetzelfde verzoek in een te korte periode, wat aangeeft dat het in een defecte staat of is misbruik van het aanvragen van tokens. |
| AADSTS50199 | CmsiInterrupt - Om veiligheidsredenen is bevestiging van de gebruiker vereist voor dit verzoek.  Omdat dit een "interaction_required" fout is, moet de client interactieve auth doen.  Dit gebeurt omdat een systeemwebview is gebruikt om een token voor een native toepassing aan te vragen - de gebruiker moet worden gevraagd om te vragen of dit eigenlijk de app was waarop ze zich wilden aanmelden.|
| AADSTS51000 | RequiredFeatureNotEnabled - De functie is uitgeschakeld. |
| AADSTS51001 | DomainHintMustbePresent - Domeinhint moet aanwezig zijn met on-premises beveiligings-id of on-premises UPN. |
| AADSTS51004 | UserAccountNotInDirectory - Het gebruikersaccount bestaat niet in de directory. |
| AADSTS51005 | Tijdelijk omleiding - Gelijk aan HTTP-status 307, wat aangeeft dat de gevraagde informatie zich bevindt op de URI die is opgegeven in de locatiekoptekst. Wanneer u deze status ontvangt, volgt u de locatiekoptekst die aan het antwoord is gekoppeld. Wanneer de oorspronkelijke aanvraagmethode POST was, wordt de omgeleide aanvraag ook gebruikt voor de POST-methode. |
| AADSTS51006 | ForceReauthDueToInadequateAuth - Geïntegreerde Windows-verificatie is nodig. Gebruiker ingelogd met behulp van een sessietoken dat de geïntegreerde Windows-verificatieclaim mist. Vraag de gebruiker om opnieuw in te loggen. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - De gebruiker heeft geen toestemming gegeven voor toegang tot LinkedIn-bronnen. |
| AADSTS53000 | DeviceNotCompliant - Beleid voor voorwaardelijke toegang vereist een compatibel apparaat en het apparaat voldoet niet. De gebruiker moet zijn apparaat inschrijven bij een goedgekeurde MDM-provider zoals Intune. |
| AADSTS53001 | DeviceNotDomainJoined - Beleid voor voorwaardelijke toegang vereist een domeindat is samengevoegd en het apparaat is niet verbonden met het domein. Laat de gebruiker een domein lid maken van het apparaat. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - De gebruikte app is geen goedgekeurde app voor voorwaardelijke toegang. De gebruiker moet een van de apps uit de lijst met goedgekeurde apps gebruiken om toegang te krijgen. |
| AADSTS53003 | BlockedByConditionalAccess - Toegang is geblokkeerd door beleid voor voorwaardelijke toegang. Het toegangsbeleid staat geen tokenuitgifte toe. |
| AADSTS53004 | ProofUpBlockedDueToRisk - De gebruiker moet het registratieproces voor meervoudige verificatie voltooien voordat u toegang krijgt tot deze inhoud. Gebruiker moet zich registreren voor multi-factor authentication. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - De gebruiker of beheerder heeft geen toestemming gegeven om de toepassing te gebruiken met ID X. Stuur een interactieve autorisatieaanvraag voor deze gebruiker en bron. |
| AADSTS65004 | UserDeclinedConsent - Gebruiker weigerde toestemming om toegang te krijgen tot de app. Laat de gebruiker zich opnieuw aanmelden en toestemming geven voor de app|
| AADSTS65005 | Verkeerd geconfigureerde toepassing - De lijst met vereiste brontoegang voor apps bevat geen apps die door de bron kunnen worden ontdekt of de client-app heeft toegang gevraagd tot resource, die niet is opgegeven in de vereiste lijst met brontoegang of graph-service die slecht is geretourneerd aanvraag of resource niet gevonden. Als de app SAML ondersteunt, hebt u de app mogelijk geconfigureerd met de verkeerde id (entiteit). Probeer de resolutie die voor SAML wordt vermeld via de onderstaande link:[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | ActornotvalidserviceIdentity |
| AADSTS70000 | Ongeldige subsidie - Verificatie is mislukt. Het vernieuwingstoken is niet geldig. De fout kan te wijten zijn aan de volgende redenen:<ul><li>Tokenbindingskop is leeg</li><li>Tokenbindinghash komt niet overeen</li></ul> |
| AADSTS70001 | Onbevoegde Client - De toepassing is uitgeschakeld. |
| AADSTS70002 | Ongeldige client - Fout bij het valideren van de referenties. De opgegeven client_secret komt niet overeen met de verwachte waarde voor deze client. Corrigeer de client_secret en probeer het opnieuw. Zie [De autorisatiecode gebruiken om een toegangstoken aan te vragen](v2-oauth2-auth-code-flow.md#request-an-access-token)voor meer informatie. |
| AADSTS70003 | Niet-ondersteunde GrantType - De app heeft een niet-ondersteund subsidietype geretourneerd. |
| AADSTS70004 | InvalidRedirectUri - De app heeft een ongeldige omleiding URI geretourneerd. Het omleidingsadres dat is gespecificeerd door de client komt niet overeen met een geconfigureerd adres of een adres op de OIDC-goedkeuringslijst. |
| AADSTS70005 | Niet-ondersteunde ResponseType - De app heeft een niet-ondersteund antwoordtype geretourneerd vanwege de volgende redenen:<ul><li>responsetype 'token' is niet ingeschakeld voor de app</li><li>reactietype 'id_token' vereist 'OpenID'-bereik - bevat een niet-ondersteunde OAuth-parameterwaarde in de gecodeerde wctx</li></ul> |
| AADSTS70007 | Niet ondersteundResponseMode - De app heeft een `response_mode` niet-ondersteunde waarde geretourneerd van wanneer u een token aanvraagt.  |
| AADSTS70008 | ExpiredOrRevokedGrant - Het vernieuwingstoken is verlopen als gevolg van inactiviteit. Het token werd uitgegeven op XXX en was inactief voor een bepaalde tijd. |
| AADSTS70011 | Ongeldig : het door de app gevraagde bereik is ongeldig. |
| AADSTS70012 | MsaServerError - Er is een serverfout opgetreden tijdens het verifiëren van een MSA-gebruiker (consument). &Opnieuw. Als het blijft mislukken, [open dan een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AutorisatieAfhankelijk - OAuth 2.0-apparaatstroomfout. De autorisatie is in behandeling. Het apparaat zal opnieuw proberen het verzoek te peilen. |
| AADSTS70018 | BadVerificationCode - Ongeldige verificatiecode als gevolg van het typen van de gebruiker in verkeerde gebruikerscode voor de stroom van apparaatcode. De autorisatie is niet goedgekeurd. |
| AADSTS70019 | CodeVerlopen - Verificatiecode is verlopen. Laat de gebruiker de aanmelding opnieuw proberen. |
| AADSTS75001 | BindingSerializationError - Er is een fout opgetreden tijdens SAML-berichtbinding. |
| AADSTS75003 | Niet-ondersteunde BindingError - De app heeft een fout geretourneerd met betrekking tot niet-ondersteunde binding (SAML-protocolrespons kan niet worden verzonden via andere bindingen dan HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid - Azure AD biedt geen ondersteuning voor het SAML-verzoek dat door de app voor SSO is verzonden. |
| AADSTS75008 | RequestDeniedError - Het verzoek van de app is geweigerd omdat het SAML-verzoek een onverwachte bestemming had. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - De verificatiemethode waarmee de gebruiker die met de service is geverifieerd, komt niet overeen met de gevraagde verificatiemethode. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2-verificatieaanvraag heeft ongeldig nameidbeleid. |
| AADSTS80001 | OnPremiseStoreIsNotBeschikbaar - De verificatieagent kan geen verbinding maken met Active Directory. Zorg ervoor dat agentservers lid zijn van hetzelfde AD-forest als de gebruikers waarvan de wachtwoorden moeten worden gevalideerd en dat ze verbinding kunnen maken met Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - Wachtwoordvalidatieaanvraag time-out. Controleer of Active Directory beschikbaar is en reageer op verzoeken van de agents. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - Er is een onbekende fout opgetreden tijdens het verwerken van de reactie van de verificatieagent. Probeer het verzoek opnieuw. Als het mislukt, [opent u een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer details over de fout te krijgen. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - De verificatieagent kan het wachtwoord van de gebruiker niet valideren. Controleer de agentlogboeken voor meer informatie en controleer of Active Directory werkt zoals verwacht. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - De verificatieagent kan het wachtwoord niet decoderen. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - De gebruikers hebben geprobeerd in te loggen buiten de toegestane uren (dit is opgegeven in AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - De verificatiepoging kan niet worden voltooid vanwege tijdsverschil tussen de machine waarop de verificatieagent en AD worden uitgevoerd. Los problemen met tijdsynchronisatie op. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos-verificatiepoging is mislukt. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - Het verificatiepakket wordt niet ondersteund. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Er is geen autorisatiekop gevonden. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - De tenant is niet ingeschakeld voor Seamless SSO. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Kan het Kerberos-ticket van de gebruiker niet valideren. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Naadloze SSO is mislukt omdat het Kerberos-ticket van de gebruiker is verlopen of ongeldig is. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Kan het object van de gebruiker niet vinden op basis van informatie in het Kerberos-ticket van de gebruiker. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - De gebruiker die zich probeert aan te melden bij Azure AD is anders dan de gebruiker die op het apparaat is aangemeld. |
| AADSTS90002 | InvalidTenantName - De tenantnaam is niet gevonden in het gegevensarchief. Controleer of u over de juiste tenant-id beschikt. |
| AADSTS90004 | OngeldigerequestFormat - De aanvraag is niet correct opgemaakt. |
| AADSTS90005 | Ongeldige RequestWithMultipleRequirements - Kan de aanvraag niet voltooien. De aanvraag is niet geldig omdat de id en aanmeldingshint niet samen kunnen worden gebruikt.  |
| AADSTS90006 | ExternalServerRetryableError - De service is tijdelijk niet beschikbaar.|
| AADSTS90007 | InvalidSessionId - Slecht verzoek. De doorgegeven sessie-ID kan niet worden ontleed. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - De gebruiker of beheerder heeft geen toestemming gegeven om de toepassing te gebruiken. De toepassing heeft minimaal toegang tot Azure AD nodig door de aanmelding op te geven en de toestemming voor gebruikersprofielen te lezen. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - De toepassing vraagt een token voor zichzelf aan. Dit scenario wordt alleen ondersteund als de opgegeven resource de op GUID gebaseerde toepassings-id gebruikt. |
| AADSTS90010 | NotSupported - Kan het algoritme niet maken. |
| AADSTS90012 | RequestTimeout - De gevraagde heeft een time-out. |
| AADSTS90013 | Ongeldige UserInput - De invoer van de gebruiker is niet geldig. |
| AADSTS90014 | MissingRequiredField - Deze foutcode kan worden weergegeven in verschillende gevallen waarin een verwacht veld niet aanwezig is in de referentie. |
| AADSTS90015 | QueryStringTooLong - De querytekenreeks is te lang. |
| AADSTS90016 | MissingRequiredClaim - Het toegangstoken is niet geldig. De vereiste claim ontbreekt. |
| AADSTS90019 | MissingTenantRealm - Azure AD kan de tenant-id van de aanvraag niet bepalen. |
| AADSTS90022 | AuthenticdInvalidNamePrincipalNameFormat - De hoofdnaamnotatie is niet `name[/host][@realm]` geldig of voldoet niet aan de verwachte indeling. De hoofdnaam is vereist, host en realm zijn optioneel en kunnen worden ingesteld op null. |
| AADSTS90023 | Ongeldige aanvraag - De aanvraag voor verificatieservice is niet geldig. |
| AADSTS9002313 | Ongeldige aanvraag - Aanvraag is onjuist of ongeldig. - Het probleem hier is omdat er iets mis was met het verzoek om een bepaald eindpunt. De suggestie om dit probleem is om een fiddler spoor van de fout die zich voordoet en op zoek om te zien of het verzoek is eigenlijk goed geformatteerd of niet. |
| AADSTS90024 | RequestBudgetExceededError - Er is een tijdelijke fout opgetreden. &Opnieuw. |
| AADSTS90033 | MSODsServiceUnavailable - De Microsoft Online Directory Service (MSODS) is niet beschikbaar. |
| AADSTS90036 | MsodsServiceUntryableFailure - Er is een onverwachte, niet-probeerbare fout opgetreden van de WCF-service die wordt gehost door MSODS. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer informatie te krijgen over de fout. |
| AADSTS90038 | NationalCloudTenantRedirection - De opgegeven tenant 'Y' behoort tot de National Cloud 'X'. Huidige cloud instantie 'Z' niet federate met X. Er wordt een fout geretourneerd met een fout voor het omleiden van de cloud. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - De functie is uitgeschakeld. |
| AADSTS90051 | OngeldigNationalCloudId - De nationale cloud-id bevat een ongeldige cloud-id. |
| AADSTS90055 | TenantThrottlingError - Er zijn te veel binnenkomende aanvragen. Deze uitzondering wordt gegooid voor geblokkeerde huurders. |
| AADSTS90056 | BadResourceRequest - Om de code voor een toegangstoken in te `/token` wisselen, moet de app een post-verzoek naar het eindpunt verzenden. Ook moet u hiervoor een autorisatiecode verstrekken en deze in `/token` het POST-verzoek naar het eindpunt verzenden. Raadpleeg dit artikel voor een overzicht van de autorisatiecodestroom van OAuth 2.0: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Stuur de gebruiker `/authorize` naar het eindpunt, waardoor een authorization_code. Door een verzoek `/token` op het eindpunt te plaatsen, krijgt de gebruiker het toegangstoken. Meld u aan bij de Azure-portal en controleer **app-registraties > Eindpunten** om te controleren of de twee eindpunten correct zijn geconfigureerd. |
| AADSTS90072 | PassThroughUserMfaError - Het externe account waarmee de gebruiker zich aanmeldt, bestaat niet op de tenant waarop hij zich heeft aangemeld; zodat de gebruiker niet kan voldoen aan de MFA-vereisten voor de tenant. Het account moet eerst als externe gebruiker in de tenant worden toegevoegd. Meld u af en meld u aan met een ander Azure AD-gebruikersaccount. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Er is een fout opgetreden toen de service een WS-Federation-bericht probeerde te verwerken. Het bericht is niet geldig. |
| AADSTS90082 | OrgIdWsFederationNotSupported - Het geselecteerde verificatiebeleid voor de aanvraag wordt momenteel niet ondersteund. |
| AADSTS90084 | OrgIdWsFederationGuestNotAtoegestaan - Gastaccounts zijn niet toegestaan voor deze site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - De service kan geen token uitgeven omdat het object van het bedrijf nog niet is ingericht. |
| AADSTS90086 | OrgIdWsTrustDaTokenVerlopen - Het da-token van de gebruiker is verlopen. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Er is een fout opgetreden tijdens het maken van het WS-Federation-bericht van de URI. |
| AADSTS90090 | GraphRetryableError - De service is tijdelijk niet beschikbaar. |
| AADSTS90091 | GraphServiceOnbereikbaar |
| AADSTS90092 | GraphNonRetryableError GraphNonRetryableError GraphNonRetryableError GraphNon |
| AADSTS90093 | GraphUserUnauthorized - Grafiek geretourneerd met een verboden foutcode voor het verzoek. |
| AADSTS90094 | AdminConsentRequired - Toestemming van de beheerder is vereist. |
| AADSTS900382 | Vertrouwelijke client wordt niet ondersteund in Cross Cloud-aanvragen. |
| AADSTS90100 | OngeldigeGebeurtenisParameter - De parameter is leeg of niet geldig. |
| AADSTS901002 | AADSTS901002: De parameter 'resource' request wordt niet ondersteund. |
| AADSTS90101 | OngeldigEEMailadres - De meegeleverde gegevens zijn geen geldig e-mailadres. Het e-mailadres moet `someone@example.com`in de indeling staan. |
| AADSTS90102 | InvalidUriParameter - De waarde moet een geldige absolute URI zijn. |
| AADSTS90107 | InvalidXml - De aanvraag is niet geldig. Zorg ervoor dat uw gegevens geen ongeldige tekens bevatten.|
| AADSTS90114 | Ongeldig VerlopenDatum - De vervaldatum van het bulktoken zorgt ervoor dat een verlopen token wordt uitgegeven. |
| AADSTS90117 | Ongeldigeinvoer |
| AADSTS90119 | Ongeldige UserCode - De gebruikerscode is ongeldig of leeg.|
| AADSTS90120 | OngeldigDeviceFlowRequest - De aanvraag is al geautoriseerd of afgewezen. |
| AADSTS90121 | Ongeldig leegverzoek - Ongeldig leeg verzoek.|
| AADSTS90123 | IdentityProviderAccessDenied - Het token kan niet worden uitgegeven omdat de identiteits- of claimuitgifteprovider het verzoek heeft geweigerd. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - De resource wordt `/common` niet `/consumers` ondersteund via de of eindpunten. Gebruik `/organizations` in plaats daarvan het of tenantspecifieke eindpunt. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - De gebruiker is niet in het systeem. Zorg ervoor dat u de gebruikersnaam correct hebt ingevoerd. |
| AADSTS90126 | DebugModeEnrollTenantNotferred - Het gebruikerstype wordt niet ondersteund op dit eindpunt. Het systeem kan de tenant van de gebruiker niet afleiden van de gebruikersnaam. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - De toepassing wordt `/common` niet `/consumers` ondersteund via de of eindpunten. Gebruik `/organizations` in plaats daarvan het of tenantspecifieke eindpunt. |
| AADSTS120000 | PasswordchangeIncorrectCurrentPassword Password Password PasswordPassword PasswordPassword Password |
| AADSTS120002 | PasswordchangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordchangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity PasswordChangeOnPremComplexity PasswordChangeOnPremComplexity PasswordChange |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Er is een niet-opnieuw te proberen fout opgetreden.|
| AADSTS120011 | PasswordChangeAsyncUpnInference Mislukt |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem PasswordChange |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure PasswordChangeOnPremisesConnectivityFailure PasswordChangeOnPremisesConnectivityFailure PasswordChange |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionvereist |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr PasswordChangeUser |
| AADSTS120018 | PasswordChangePasswordVoldoet nietFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceFout |
| AADSTS130004 | NgcKeyNotFound - De gebruikersprincipal heeft de NGC ID-sleutel niet geconfigureerd. |
| AADSTS130005 | NgcInvalidSignature - NGC-sleutelhandtekening geverifieerd is mislukt.|
| AADSTS130006 | NgcTransportKeyNotFound - De NGC-transportsleutel is niet geconfigureerd op het apparaat. |
| AADSTS130007 | NgcDeviceIsDisabled - Het apparaat is uitgeschakeld. |
| AADSTS130008 | NgcDeviceIsNotFound - Het apparaat waarnaar wordt verwezen door de NGC-toets is niet gevonden. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | Ongeldige RequestNonce - Verzoek nonce wordt niet verstrekt. |
| AADSTS140001 | Ongeldige SessionKey - De sessiesleutel is ongeldig.|
| AADSTS165900 | Ongeldig apirequest - Ongeldig verzoek. |
| AADSTS220450 | Niet ondersteundAndroidWebViewVersion - De Chrome WebView-versie wordt niet ondersteund. |
| AADSTS220501 | OngeldigCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - De bron is niet geconfigureerd om alleen apparaten tokens te accepteren. |
| AADSTS240001 | BulkAADJTokenUnauthorized - De gebruiker is niet bevoegd om apparaten te registreren in Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - De id_token kan `urn:ietf:params:oauth:grant-type:jwt-bearer` niet worden gebruikt als subsidie.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy - De tenantbeheerder heeft een beveiligingsbeleid geconfigureerd dat deze aanvraag blokkeert. Controleer het beveiligingsbeleid dat op tenantniveau is gedefinieerd om te bepalen of uw aanvraag voldoet aan de beleidsvereisten. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest - De toepassing is niet gevonden in de directory/tenant. Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de tenant of toestemming heeft gegeven door een gebruiker in de tenant. Mogelijk hebt u de id-waarde voor de toepassing verkeerd geconfigureerd of uw verificatieverzoek naar de verkeerde tenant verzonden. |
| AADSTS700020 | InteractieVereist - De toegangssubsidie vereist interactie. |
| AADSTS700022 | OngeldigMultipleResourcesScope - De opgegeven waarde voor het bereik van de invoerparameter is niet geldig omdat deze meer dan één resource bevat. |
| AADSTS700023 | Ongeldige ResourcelessScope - De opgegeven waarde voor het bereik van de invoerparameter is niet geldig wanneer u een toegangstoken aanvraagt. |
| AADSTS1000000 | UserNotBoundError - De Binding API vereist dat de Azure AD-gebruiker ook verifieert met een externe IDP, wat nog niet is gebeurd. |
| AADSTS1000002 | BindCompleteInterruptError - De binding is voltooid, maar de gebruiker moet worden geïnformeerd. |
| AADSTS7000112 | Ongeautoriseerde ClientApplicationDisabled - De toepassing is uitgeschakeld. |
| AADSTS7500529 | De waarde 'SAMLId-Guid' is geen geldige SAML-id - Azure AD gebruikt dit kenmerk om het kenmerk InResponseTo van de geretourneerde reactie te vullen. ID mag niet beginnen met een getal, dus een gemeenschappelijke strategie is om een tekenreeks als "id" voor te bereiden op de tekenreeksweergave van een GUID. De id6c1c178c166d486687be4aaf5e482730 is bijvoorbeeld een geldig identiteitsbewijs. |

## <a name="next-steps"></a>Volgende stappen

* Heb je een vraag of kun je niet vinden wat je zoekt? Maak een GitHub-probleem of zie [ondersteunings- en helpopties voor ontwikkelaars](active-directory-develop-help-support.md) om meer te weten te komen over andere manieren waarop u hulp en ondersteuning krijgen.
