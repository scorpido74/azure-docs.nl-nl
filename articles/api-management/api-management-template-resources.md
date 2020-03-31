---
title: Azure API Management-sjabloonbronnen | Microsoft Documenten
description: Meer informatie over de typen resources die beschikbaar zijn voor gebruik in ontwikkelaarsportalsjablonen in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249501"
---
# <a name="azure-api-management-template-resources"></a>Azure API Management-sjabloonbronnen
Azure API Management biedt de volgende typen resources voor gebruik in de ontwikkelaarsportalsjablonen.  
  
-   [Tekenreeksbronnen](#strings)  
  
-   [Glyph-bronnen](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a>Tekenreeksbronnen  
 API-beheer biedt een uitgebreide set tekenreeksbronnen voor gebruik in de ontwikkelaarsportal. Deze bronnen zijn gelokaliseerd in alle talen die worden ondersteund door API-beheer. De standaardset sjablonen gebruikt deze bronnen voor paginakoppen, labels en constante tekenreeksen die worden weergegeven in de ontwikkelaarsportal. Als u een tekenreeksbron in uw sjablonen wilt gebruiken, geeft u het voorvoegsel resourcetekenreeks op, gevolgd door de tekenreeksnaam, zoals in het volgende voorbeeld wordt weergegeven.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Het volgende voorbeeld is van de sjabloon Productlijst en geeft **producten** boven aan de pagina weer.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
De volgende lokalisatieopties worden ondersteund:

| Landinstelling    | Taal               |
|-----------|------------------------|
| en      | "Engels"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "het"      | "Italiano"             |
| "ja-JP"   | "???"                |
| "ko"      | "ーسس"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brazilië)"   |
| "pt-pt"   | "Português (Portugal)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "??????           |
| "zh-hant" | "??????           |

 Raadpleeg de volgende tabellen voor de tekenreeksbronnen die beschikbaar zijn voor gebruik in uw ontwikkelaarsportalsjablonen. Gebruik de tabelnaam als voorvoegsel voor de tekenreeksbronnen in die tabel.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppTekenreeksen](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [Algemene tekenreeksen](#CommonStrings)  
  
-   [Documentatie](#Documentation)  
  
-   [Foutpaginatekenreeksen](#ErrorPageStrings)  
  
-   [ProblemenStrings](#IssuesStrings)  
  
-   [Notfoundstrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductenTekenreeksen](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [Aanmeldingen](#SigninStrings)  
  
-   [Aanmeldingstekenreeksen](#SignupStrings)  
  
-   [AbonnementlistTekenreeksen](#SubscriptionListStrings)  
  
-   [Abonnementen](#SubscriptionStrings)  
  
-   [Profieltekenreeksen bijwerken](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a>ApisStrings  
  
|Name|Tekst|  
|----------|----------|  
|PageTitleApis PageTitleApis|API's|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebApplicationsDetailsTitle|Toepassingsvoorbeeld|  
|WebApplicationsRequirementsHeader|Vereisten|  
|WebApplicationsScreenshotAlt|Schermopname|  
|WebApplicationsScreenshotsHeader|Schermopnamen|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebDevelopersAppDeleteBevestiging|Weet u zeker dat u de toepassing wilt verwijderen?|  
|WebDevelopersAppNotGepubliceerd|Niet gepubliceerd|  
|WebDevelopersAppNotIngediend|Niet ingediend|  
|WebDevelopersAppTableCategoryHeader|Categorie|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|Status|  
|WebDevelopersEditLink|Bewerken|  
|WebDevelopersRegisterAppLink|Toepassing registreren|  
|WebOntwikkelaarsRemoveLink|Verwijderen|  
|WebDevelopersSubmitLink|Verzenden|  
|WebDevelopersYourApplicationsHeader|Uw toepassingen|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a>AppTekenreeksen  
  
|Name|Tekst|  
|----------|----------|  
|WebApplicationsHeader|Toepassingen|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a>CommonResources  
  
|Name|Tekst|  
|----------|----------|  
|NoItemsToDisplay|Geen resultaten gevonden.|  
|Algemeen Uitzonderingsbericht|Er klopt iets niet. Het kan een tijdelijke storing of een bug. Probeer het nog eens.|  
|GeneralJsonExceptionMessage GeneralJsonExceptionMessage GeneralJsonExceptionMessage GeneralJs|Er klopt iets niet. Het kan een tijdelijke storing of een bug. Gelieve, herlaad de pagina en probeer het opnieuw.|  
|ConfirmationMessageUnsavedChanges|Er zijn enkele niet-opgeslagen wijzigingen. Weet u zeker dat u de wijzigingen wilt annuleren en verwijderen?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttplargerequestMessage HttplargerequestMessage|Http Request Body te groot.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a>Algemene tekenreeksen  
  
|Name|Tekst|  
|----------|----------|  
|ButtonLabelAnnuleren|Annuleren|  
|ButtonLabelOpslaan|Opslaan|  
|Algemeen Uitzonderingsbericht|Er klopt iets niet. Het kan een tijdelijke storing of een bug. Probeer het nog eens.|  
|NoItemsToDisplay|Er zijn geen objecten om weer te geven.|  
|PagerButtonLabelFirst PagerButtonLabelFirst|Eerste|  
|PagerButtonLabelLast|Laatste|  
|PagerButtonLabelNext PagerButtonLabelNext|Volgende|  
|PagerButtonLabelPrevious|Vorige|  
|PagerLabelPageNOfM PagerLabel|Pagina {0} van {1}|  
|PasswordtooShort PasswordTooShort|Het wachtwoord is te kort|  
|EmailAsPassword|Gebruik uw e-mail niet als wachtwoord|  
|PasswordSameasUserName|Uw wachtwoord kan uw gebruikersnaam niet bevatten|  
|PasswordTwoCharacterClasses PasswordTwoCharacterClasses PasswordTwoCharacterClasses PasswordTwo|Verschillende tekenklassen gebruiken|  
|PasswordTooManyHerhalings|Te veel herhalingen|  
|PasswordSequenceFound PasswordSequenceFound|Uw wachtwoord bevat reeksen|  
|PagerLabelPageSize PageSize PagePageSize PagePageSize PagePageSize Page|Paginagrootte|  
|Gordijnlabelladen|Laden...|  
|TablePlaceholderNothingToDisplay|Er zijn geen gegevens voor de geselecteerde periode en het bereik|  
|ButtonLabelClose ButtonLabelClose|Sluiten|  
  
###  <a name="documentation"></a><a name="Documentation"></a>Documentatie  
  
|Name|Tekst|  
|----------|----------|  
|WebdocumentationinvalidheaderErrorMessage|Ongeldige{0}koptekst ' '|  
|WebdocumentationinvalidrequestErrorMessage|URL van ongeldige aanvraag|  
|TextboxLabelAccessToken|Toegangstoken *|  
|VervolgkeuzelijstPrimaryKeyFormat|Primair-{0}|  
|VervolgkeuzeoptieSecondaryKeyFormat|Secundair-{0}|  
|WebdocumentationSubscriptionKeyText|Uw abonnementssleutel|  
|WebDocumentationTemplatesAddHeaders|Vereiste HTTP-headers toevoegen|  
|WebdocumentationTemplatesBasicAuthSample|Voorbeeld van basisautorisatie|  
|WebdocumentationTemplatesCurlForBasicAuth|voor basisautorisatiegebruik: --gebruiker {gebruikersnaam}:{password}|  
|WebdocumentationTemplatesCurlValuesForPath|Waarden opgeven voor padparameters (weergegeven als {...}), uw abonnementssleutel en waarden voor queryparameters|  
|WebdocumentationTemplatesDeveloperKey|Uw abonnementssleutel opgeven|  
|WebdocumentationTemplatesJavaApache|In dit voorbeeld wordt de Apache HTTP-client van HTTP-componenten (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Waarden opgeven voor optionele parameters, indien nodig|  
|WebDocumentationTemplatesPhpPakket|In dit voorbeeld wordt het HTTP_Request2 pakket gebruikt. (voor meer informatie:https://pear.php.net/package/HTTP_Request2)|  
|WebdocumentationTemplatesPythonWaardenForPath|Waarden opgeven voor padparameters (weergegeven als {...}) en indien nodig een aanvraaginstantie|  
|WebdocumentationTemplatesRequestBody|Aanvraaginstantie opgeven|  
|WebDocumentationTemplatesRequiredParams|Waarden opgeven voor de volgende vereiste parameters|  
|WebdocumentationTemplatesValuesForPath|Waarden opgeven voor padparameters (weergegeven als {...})|  
|Beschrijving van OAuth2AuthorizationEndpoint|Het autorisatieeindpunt wordt gebruikt om te communiceren met de eigenaar van de resource en een autorisatiesubsidie te verkrijgen.|  
|OAuth2AuthorizationEndpointName|Eindpunt voor autorisatie|  
|Beschrijving van OAuth2TokenEndpoint|Het tokeneindpunt wordt door de client gebruikt om een toegangstoken te verkrijgen door het autorisatie-token of het vernieuwen van het token te presenteren.|  
|OAuth2TokenEndpointName|Tokeneindpunt|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<\> p De client initieert de stroom door de gebruikersagent van de broneigenaar naar het eindpunt van de autorisatie te leiden.  De client bevat zijn client-id, gevraagde scope, lokale status en een omleidings-URI waarnaar de autorisatieserver de user-agent terugstuurt zodra de toegang is verleend (of geweigerd).     </p\> <\> p De autorisatieserver verifieert de eigenaar van de bron (via de user-agent) en bepaalt of de eigenaar van de resource de toegangsaanvraag van de klant verleent of weigert.     </p\> <\> p Ervan uitgaande dat de eigenaar van de bron toegang verleent, stuurt de autorisatieserver de gebruiker-agent terug naar de client met behulp van de eerder geleverde omleidings-URI (in de aanvraag of tijdens de clientregistratie).  De omleiding URI bevat een autorisatiecode en een lokale staat die door de client eerder.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Als de gebruiker de toegangsaanvraag weigert als het verzoek ongeldig is, wordt de client op de hoogte gebracht met behulp van de volgende parameters die zijn toegevoegd aan de omleiding: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Vergunningsaanvraag|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<\> p De client-app moet de gebruiker naar het autorisatieeindpunt sturen om het OAuth-proces te starten.          Op het eindpunt van de autorisatie verifieert de gebruiker en verleent of weigert hij toegang tot de app.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Ervan uitgaande dat de eigenaar van de bron toegang verleent, leidt autorisatieserver de gebruiker-agent terug naar de client met behulp van de eerder geleverde omleidings-URI (in de aanvraag of tijdens de clientregistratie).  De omleiding URI bevat een autorisatiecode en een lokale staat die door de client eerder. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<\> p De client vraagt een toegangstoken aan van het tokeneindpunt van de autorisatieserver door de autorisatiecode op te die in de vorige stap is ontvangen.  Bij het indienen van de aanvraag verifieert de client met de autorisatieserver.  De client omvat de omleiding URI gebruikt om de autorisatie code te verkrijgen voor verificatie. </p\> <\> p De autorisatieserver verifieert de client, valideert de autorisatiecode en zorgt ervoor dat de omleidings-URI-ontvangen functie overeenkomt met de URI die wordt gebruikt om de client in stap (C) om te leiden.  Indien geldig, reageert de autorisatieserver terug met een toegangstoken en, optioneel, een vernieuwingstoken. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<\> p Als de verificatie van de aanvraagclient is mislukt of ongeldig is, reageert de autorisatieserver met een HTTP 400 -statuscode (slecht verzoek) (tenzij anders opgegeven) en bevat hij de volgende parameters bij het antwoord. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<\> p De client doet een verzoek aan het tokeneindpunt door de volgende parameters te verzenden met behulp van de "application/x-www-form-urlencoded" indeling met een tekencodering van UTF-8 in de HTTP-aanvraagentiteit-body. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<\> p De autorisatieserver geeft een access token en optioneel vernieuwingstoken uit en construeert het antwoord door de volgende parameters toe te voegen aan de entiteitshoofd van de HTTP-respons met een statuscode van 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<\> p De client verifieert met de autorisatieserver en vraagt een toegangstoken aan vanaf het token-eindpunt. </p\> <\> p De autorisatieserver verifieert de client en geeft, indien geldig, een toegangstoken uit. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Als de clientverificatie van de aanvraag is mislukt of ongeldig is, reageert de autorisatieserver met een HTTP 400 -status (Bad Request) (tenzij anders opgegeven) en bevat de volgende parameters bij het antwoord. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p De client doet een verzoek aan het tokeneindpunt door de volgende parameters toe te voegen met de indeling "toepassing/x-www-formulier-urlencoded" met een tekencodering van UTF-8 in de http-aanvraagentiteit.< </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Als de aanvraag voor toegangstoken geldig en geautoriseerd is, geeft de autorisatieserver een toegangstoken en een optioneel vernieuwingstoken uit en construeert het antwoord door de volgende parameters toe te voegen aan de entiteitshoofd van het HTTP-antwoord met een statuscode van 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<\> p De client initieert de stroom door de gebruikersagent van de broneigenaar naar het eindpunt van de autorisatie te leiden.  De client bevat zijn client-id, gevraagde scope, lokale status en een omleidings-URI waarnaar de autorisatieserver de user-agent terugstuurt zodra de toegang is verleend (of geweigerd). </p\> <\> p De autorisatieserver verifieert de eigenaar van de bron (via de user-agent) en bepaalt of de eigenaar van de bron de toegangsaanvraag van de klant verleent of ontkent. </p\> <\> p Ervan uitgaande dat de eigenaar van de bron toegang verleent, stuurt de autorisatieserver de gebruikersagent terug naar de client met behulp van de eerder verstrekte omleidings-URI.  De omleidings-URI bevat het toegangstoken in het URI-fragment. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Als de eigenaar van de bron het toegangsverzoek weigert of als het verzoek mislukt om andere redenen dan een ontbrekende of ongeldige omleidings-URI, informeert de autorisatieserver de client door de volgende parameters toe te voegen aan de fragmentcomponent van de omleidings-URI met behulp van de indeling "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<\> p De client-app moet de gebruiker naar het autorisatieeindpunt sturen om het OAuth-proces te starten.      Op het eindpunt van de autorisatie verifieert de gebruiker en verleent of weigert hij toegang tot de app. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Als de eigenaar van de bron de toegangsaanvraag verleent, geeft de autorisatieserver een toegangstoken uit en levert het aan de client door de volgende parameters toe te voegen aan de fragmentcomponent van de omleidings-URI met behulp van de indeling "toepassing/x-www-formulier-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Autorisatiecodestroom is geoptimaliseerd voor clients die in staat zijn de vertrouwelijkheid van hun referenties te behouden (bijvoorbeeld webservertoepassingen die zijn geïmplementeerd met PHP, Java, Python, Ruby, ASP.NET, enz.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Vergunningscode verlening|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Clientreferenties stroom is geschikt in gevallen waarin de client (uw toepassing) vraagt om toegang tot de beveiligde bronnen onder haar controle. De client wordt beschouwd als een broneigenaar, dus er is geen interactie van de eindgebruiker vereist.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Clientreferenties verlenen|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Impliciete stroom is geoptimaliseerd voor clients die niet in staat zijn om de vertrouwelijkheid van hun referenties te behouden waarvan bekend is dat ze een bepaalde omleiding uri bedienen. Deze clients worden meestal geïmplementeerd in een browser met behulp van een scripttaal zoals JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Impliciete subsidie|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|De stroom van wachtwoordreferenties voor resourceeigenaren is geschikt in gevallen waarin de eigenaar van de resource een vertrouwensrelatie heeft met de client (uw toepassing), zoals het besturingssysteem voor apparaten of een zeer bevoorrechte toepassing. Deze stroom is geschikt voor clients die in staat zijn om de referenties van de broneigenaar te verkrijgen (gebruikersnaam en wachtwoord, meestal met behulp van een interactief formulier).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Toekenning van wachtwoordreferenties voor resource-eigenaar|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<\> p De eigenaar van de bron voorziet de client van zijn gebruikersnaam en wachtwoord. </p\> <\> p De client vraagt een toegangstoken aan van het tokeneindpunt van de autorisatieserver door de referenties op te geven die van de eigenaar van de bron zijn ontvangen.  Bij het indienen van de aanvraag verifieert de client met de autorisatieserver. </p\> <\> p De autorisatieserver verifieert de client en valideert de referenties van de broneigenaar en geeft, indien geldig, een toegangstoken uit. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Als de clientverificatie van de aanvraag is mislukt of ongeldig is, reageert de autorisatieserver met een HTTP 400 -status (Bad Request) (tenzij anders opgegeven) en bevat de volgende parameters bij het antwoord. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p De client doet een verzoek aan het tokeneindpunt door de volgende parameters toe te voegen met de indeling "toepassing/x-www-formulier-urlencoded" met een tekencodering van UTF-8 in de http-aanvraagentiteit.< </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Als de aanvraag voor toegangstoken geldig en geautoriseerd is, geeft de autorisatieserver een toegangstoken en een optioneel vernieuwingstoken uit en construeert het antwoord door de volgende parameters toe te voegen aan de entiteitshoofd van het HTTP-antwoord met een statuscode van 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Toegangstokenaanvraag|  
|OAuth2Step_AuthorizationRequest_Name|Vergunningsaanvraag|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Vereist. Het toegangstoken dat is uitgegeven door de autorisatieserver.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Vereist. Het toegangstoken dat is uitgegeven door de autorisatieserver.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Vereist. Het toegangstoken dat is uitgegeven door de autorisatieserver.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Vereist. Het toegangstoken dat is uitgegeven door de autorisatieserver.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Vereist. Client-id.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|Vereist als de client niet authenticeren met de autorisatieserver.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Vereist. De client-id.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Vereist. De autorisatiecode die door de autorisatieserver wordt gegenereerd.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Vereist. De autorisatiecode die is ontvangen van de autorisatieserver.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Optionele. Door de mens leesbare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Optionele. Door de mens leesbare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Optionele. Door de mens leesbare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Optionele. Door de mens leesbare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Optionele. Door de mens leesbare ASCII-tekst met aanvullende informatie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Optionele. Een URI die een door de mens leesbare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Optionele. Een URI die een door de mens leesbare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Optionele. Een URI die een door de mens leesbare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Optionele. Een URI die een door de mens leesbare webpagina identificeert met informatie over de fout.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Optionele. Een URI die een door de mens leesbare webpagina identificeert met informatie over de fout.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Vereist. Een enkele ASCII-foutcode van de volgende: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Vereist. Een enkele ASCII-foutcode van het volgende: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Vereist. Een enkele ASCII-foutcode van het volgende: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Vereist. Een enkele ASCII-foutcode van de volgende: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Vereist. Een enkele ASCII-foutcode van het volgende: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Aanbevolen. De levensduur in seconden van het toegangstoken.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Aanbevolen. De levensduur in seconden van het toegangstoken.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Aanbevolen. De levensduur in seconden van het toegangstoken.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Aanbevolen. De levensduur in seconden van het toegangstoken.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Vereist. Waarde MOET worden ingesteld op "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Vereist. Waarde MOET worden ingesteld op "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Vereist. Waarde MOET worden ingesteld op "wachtwoord".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Vereist. Het wachtwoord van de broneigenaar.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Optionele. Het omleidingseindpunt URI moet een absolute URI zijn.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|Vereist als de parameter "redirect_uri" is opgenomen in de autorisatieaanvraag en de waarden ervan identiek moeten zijn.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Optionele. Het omleidingseindpunt URI moet een absolute URI zijn.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Optionele. Het vernieuwingstoken, dat kan worden gebruikt om nieuwe toegangstokens te verkrijgen.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Optionele. Het vernieuwingstoken, dat kan worden gebruikt om nieuwe toegangstokens te verkrijgen.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Optionele. Het vernieuwingstoken, dat kan worden gebruikt om nieuwe toegangstokens te verkrijgen.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Vereist. Waarde MOET worden ingesteld op "code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Vereist. Waarde MOET worden ingesteld op "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Optionele. De reikwijdte van het toegangsverzoek.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|FACULTATIEf indien identiek aan het door de klant gevraagde bereik; anders, vereist.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Optionele. De reikwijdte van het toegangsverzoek.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|FACULTATIEF, indien identiek aan het door de cliënt gevraagde bereik; anders, vereist.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Optionele. De reikwijdte van het toegangsverzoek.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|FACULTATIEf indien identiek aan het door de klant gevraagde bereik; anders, vereist.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Optionele. De reikwijdte van het toegangsverzoek.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|FACULTATIEF, indien identiek aan het door de cliënt gevraagde bereik; anders, vereist.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|Vereist als de parameter "status" aanwezig was in de aanvraag voor clientautorisatie.  De exacte waarde van de klant.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Aanbevolen. Een ondoorzichtige waarde die door de client wordt gebruikt om de status tussen de aanvraag en de callback te behouden.  De autorisatieserver bevat deze waarde bij het doorsturen van de user-agent naar de client.  De parameter MOET worden gebruikt voor het voorkomen van cross-site aanvraag vervalsing.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|Vereist als de parameter "status" aanwezig was in de aanvraag voor clientautorisatie.  De exacte waarde van de klant.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|Vereist als de parameter "status" aanwezig was in de aanvraag voor clientautorisatie.  De exacte waarde van de klant.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Aanbevolen. Een ondoorzichtige waarde die door de client wordt gebruikt om de status tussen de aanvraag en de callback te behouden.  De autorisatieserver bevat deze waarde bij het doorsturen van de user-agent naar de client.  De parameter MOET worden gebruikt voor het voorkomen van cross-site aanvraag vervalsing.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|Vereist als de parameter "status" aanwezig was in de aanvraag voor clientautorisatie.  De exacte waarde van de klant.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Vereist. Het type token dat is uitgegeven.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Vereist. Het type token dat is uitgegeven.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Vereist. Het type token dat is uitgegeven.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Vereist. Het type token dat is uitgegeven.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Vereist. De gebruikersnaam van de broneigenaar.|  
|OAuth2UnSupportedTokenType|Tokentype{0}' wordt niet ondersteund.|  
|OAuth2InvalidState|Ongeldig antwoord van autorisatieserver|  
|OAuth2GrantType_AuthorizationCode|Autorisatiecode|  
|OAuth2GrantType_Implicit|Impliciete|  
|OAuth2GrantType_ClientCredentials|Clientreferenties|  
|OAuth2GrantType_ResourceOwnerPassword|Wachtwoord voor broneigenaar|  
|WebDocumentatie302Code|302 Gevonden|  
|WebDocumentatie400Code|400 (Slecht verzoek)|  
|OAuth2SendingMethod_AuthHeader|Autorisatiekoptekst|  
|OAuth2SendingMethod_QueryParam|Queryparameter|  
|OAuth2AuthorizationServerGeneralException|Er is een fout opgetreden tijdens het toestaan van toegang via{0}|  
|OAuth2AuthorizationServerCommunicationException|Er kan geen HTTP-verbinding met autorisatieserver worden ingesteld of deze is onverwachtgesloten.|  
|Webdocumentationoauth2GeneralErrorMessage|Er is onverwachte fout opgetreden.|  
|AuthorizationServerCommunicationException|Er is een uitzondering op de communicatievan autorisatieserver. Neem contact op met de beheerder.|  
|TextblockSubscriptionKeyHeaderDescription|Abonnementssleutel die toegang biedt tot deze API. Gevonden in uw <een href='/developer'\>Profiel</a\>.|  
|TextblockOAuthHeaderBeschrijving|OAuth 2.0-toegangstoken verkregen van <i\> {0}</i\>. Ondersteunde subsidietypen: <\> {1} i\></i .|  
|TextblockContentTypeHeaderDescription|Mediatype van de body die naar de API wordt verzonden.|  
|ErrorMessageApiNotToegankelijk|De API die u probeert aan te roepen is op dit moment niet toegankelijk. Neem hier contact op met de API-uitgever <een href="/issues"\></a\>.|  
|ErrorMessageApiTimedout|De API die u probeert aan te roepen duurt langer dan normaal om de reactie terug te krijgen. Neem hier contact op met de API-uitgever <een href="/issues"\></a\>.|  
|BadRequestParameterVerwacht|"'{0}' parameter wordt verwacht"|  
|TooltipTextDoubleClickToSelectAll|Dubbelklik om alles te selecteren.|  
|TooltipTextHideRevealSecret|Weergeven/verbergen|  
|ButtonLinkOpenConsole|Probeer het nu|  
|SectionheadingRequestBody SectionheadingRequestBody|Aanvraagbody|  
|SectionheadingRequestParameters|Parameters aanvragen|  
|SectionheadingRequestUrl SectionHeadingRequestUrl SectionHeading|Aanvraag-URL|  
|SectionHeadingResponse|Antwoord|  
|SectionHeadingRequestHeaders|Aanvraagheaders|  
|FormLabelSubtextOptioneel|optioneel|  
|SectionheadingCodeSamples|Codevoorbeelden|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect ID-token\> {0} verkregen\>van <i</i . Ondersteunde subsidietypen: <\> {1} i\></i .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a>Foutpaginatekenreeksen  
  
|Name|Tekst|  
|----------|----------|  
|LinkLabelBack|terug|  
|LinkLabelHomePage|startpagina|  
|LinkLabelSendusE-e-mail|Stuur ons een e-mail|  
|PageTitleError PageTitle|Sorry, er was een probleem met het serveren van de opgevraagde pagina|  
|TextblockPotentialCauseIntermittentIssue|Dit kan een incidenteel probleem met gegevenstoegang zijn dat al is verdwenen.|  
|TextblockPotentialCauseOldLink|De link waarop u hebt geklikt kan oud zijn en niet meer naar de juiste locatie wijzen.|  
|TextblockPotentialCauseTechnicalProblem|Er kan een technisch probleem aan onze kant zijn.|  
|TextblockPotentialSolutionRefresh|Vernieuw de pagina.|  
|TextblockPotentialSolutionStartOver|Begin opnieuw {0}vanaf onze.|  
|TextblockPotentialSolutionTryAgain|Ga {0} en probeer de actie die u hebt uitgevoerd opnieuw.|  
|Tekstrapportprobleem|{0}beschrijven wat er mis ging en we zullen kijken naar het zo snel als we kunnen.|  
|TitlePotentialCause|Mogelijke oorzaak|  
|TitlePotentialSolution|Het is misschien slechts een tijdelijk probleem, een paar dingen om te proberen|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a>ProblemenStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebIssuesIndexTitel|Problemen|  
|WebIssuesNoActiveAbonnementen|Je hebt geen actieve abonnementen. U moet zich abonneren op een product om een probleem te melden.|  
|WebIssuesNotSignin|Je bent niet ingelogd. Meld {0} een probleem of plaats een reactie.|  
|WebIssuesReportIssueButton|Rapportprobleem|  
|WebIssuesSignIn|aanmelden|  
|WebIssuesStatusgerapporteerddoor|Status: {0} &#124; Gerapporteerd door{1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a>Notfoundstrings  
  
|Name|Tekst|  
|----------|----------|  
|LinkLabelHomePage|startpagina|  
|LinkLabelSendusE-e-mail|stuur ons een e-mail|  
|PageTitleNotFound PageTitleNotFound PageTitleNotFound PageTitle|Sorry, we kunnen de pagina die u zoekt niet vinden|  
|TextblockPotentialCauseMisspelledUrl|Het kan zijn dat u de URL verkeerd hebt gespeld als u deze hebt ingevoerd.|  
|TextblockPotentialCauseOldLink|De link waarop u hebt geklikt kan oud zijn en niet meer naar de juiste locatie wijzen.|  
|TextblockPotentialSolutionRetype|Probeer de URL opnieuw te typen.|  
|TextblockPotentialSolutionStartOver|Begin opnieuw {0}vanaf onze.|  
|Tekstrapportprobleem|{0}beschrijven wat er mis ging en we zullen kijken naar het zo snel als we kunnen.|  
|TitlePotentialCause|Mogelijke oorzaak|  
|TitlePotentialSolution|Mogelijke oplossing|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Name|Tekst|  
|----------|----------|  
|WebProductenOvereenkomst|Door me {0} te abonneren op `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`product, ga ik akkoord met de .|  
|WebProductsLegalTermsLink|Gebruiksrechtovereenkomst|  
|WebProductsSubscribeButton|Abonneren|  
|WebProductsUsageLimitsheader|Gebruikslimieten|  
|WebProductenYouAreNotSubscribed|U bent geabonneerd op dit product.|  
|WebProductsYouRequestedAbonnement|Je hebt een abonnement op dit product aangevraagd.|  
|ErrorYouNeedToAgreeLegalTerms|U moet akkoord gaan met de gebruiksvoorwaarden voordat u verder gaan.|  
|ButtonLabelAddSubscription|Abonnement toevoegen|  
|LinkLabelChangeSubscriptionName|wijzigen|  
|ButtonLabelConfirm ButtonLabelConfirm ButtonLabelConfirm ButtonLabel|Bevestigen|  
|Aantal textblockveelvoudenabonnementen|U {0} heeft abonnementen op dit product:|  
|TextblockSingleSubscriptionsCount TextblockSingleSubscriptionsCount TextblockSingleSubscriptionsCount Textblock|U {0} heeft een abonnement op dit product:|  
|TextblockSingleApisCount|Dit product {0} bevat API:|  
|TextblockMultipleApisCount|Dit product {0} bevat API's:|  
|TextblockHeaderSubscribe|Abonneer u op product|  
|Beschrijving van TextblockSubscription|Er wordt als volgt een nieuw abonnement gemaakt:|  
|TextblockSubscriptionLimitBereikt|Abonnementslimiet bereikt.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a>ProductenTekenreeksen  
  
|Name|Tekst|  
|----------|----------|  
|PageTitleProducten|Producten|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Name|Tekst|  
|----------|----------|  
|TekstvakExternalIdentitiesDisabled|Aanmelden is momenteel uitgeschakeld door de beheerders.|  
|TekstvakExterne identiteitenMelduitnodiging|U zich ook aanmelden met|  
|TekstvakExterne identiteitenMeldnaamPrimair|Log hier in|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a>SigninResources  
  
|Name|Tekst|  
|----------|----------|  
|PrincipalNotFound|Opdrachtgever wordt niet gevonden of handtekening is ongeldig|  
|ErrorSsoAuthentication Failed|SSO-verificatie is mislukt|  
|ErrorSsoAuthenticationFailedDetailed|Ongeldige token of handtekening kan niet worden geverifieerd.|  
|ErrorSsoTokenOngeldig|SSO-token is ongeldig|  
|ValidatieErrorSpecificEmailBestaat al|E-mail '{0}' al geregistreerd|  
|ValidatieErrorSpecificEmailOngeldig|E-mail '{0}' is ongeldig|  
|ValidatieErrorPasswordOngeldig|Het wachtwoord is ongeldig. Corrigeer de fouten en probeer het opnieuw.|  
|PropertytooShort|{0}is te kort|  
|WebAuthenticationAddresserE-mailOngeldigFoutbericht|Het e-mailadres is ongeldig.|  
|ValidatieMessageNewPasswordBevestiging vereist|Nieuw wachtwoord bevestigen|  
|ValidatieErrorPasswordConfirmationvereist|Bevestigen dat het wachtwoord leeg is|  
|WebAuthenticationEmailChangeNotice|Bevestigingsmail wijzigen is onderweg {0}naar . Volg de instructies om je nieuwe e-mailadres te bevestigen. Als de e-mail niet binnen enkele minuten in uw postvak IN wordt weergegeven, raadpleegt u uw map met ongewenste e-mail.|  
|WebAuthenticationEmailChangeNoticeHeader|Uw e-mailwijzigingsverzoek is verwerkt|  
|WebAuthenticationEmailChangeNoticeTitle|E-mailwijziging gevraagd|  
|WebAuthenticationEmailHasBeenRevertedNotice|Je e-mail bestaat al. Aanvraag is teruggedraaid|  
|ValidatieErrorEmailBestaat al|E-mail bestaat al|  
|ValidatieErrorEmailOngeldig|Ongeldig e-mailadres|  
|TextboxLabelEmail|Email|  
|ValidatieErrorEmailvereist|Het e-mailadres is vereist.|  
|WebAuthenticationErrorNoticeHeader|Fout|  
|WebAuthenticationFieldLengthErrorMessage|{0}moet een maximale lengte van{1}|  
|TextboxLabelEmailFirstName|Voornaam|  
|ValidatieErrorFirstNamevereist|Voornaam is vereist.|  
|ValidatieErrorFirstNameOngeldig|Ongeldige voornaam|  
|NoticeInvalidInvitationToken|Houd er rekening mee dat bevestigingslinks slechts 48 uur geldig zijn. Als u zich nog steeds binnen deze termijn bevindt, moet u ervoor zorgen dat uw koppeling correct is. Als je koppeling is verlopen, herhaal je de actie die je probeert te bevestigen.|  
|NoticeheaderInvalidInvitationToken|Ongeldig uitnodigingstoken|  
|NoticeTitleInvalidInvitationToken|Bevestigingsfout|  
|WebAuthenticationLastNameOngeldigFoutbericht|Ongeldige achternaam|  
|TextboxLabelENaam|Achternaam|  
|ValidatieErrorLastNamevereist|Achternaam is vereist.|  
|WebAuthenticationLinkVerlopenNotice|De bevestigingskoppeling die naar u is verzonden, is verlopen. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidorverlopen|De koppeling voor het opnieuw instellen van uw wachtwoord is ongeldig of verlopen.|  
|WebAuthenticationLinkVerlopenNoticeTitle|Gekoppeld verzonden|  
|WebAuthenticationNewPasswordLabel|Nieuw wachtwoord|  
|ValidatieMessageNewPasswordvereist|Nieuw wachtwoord is vereist.|  
|TextboxLabelNotificationsSenderEmail|E-mail met afzendermeldingen|  
|TextboxLabelOrganizationName|Organisatienaam|  
|WebAuthenticationOrganisatieRequiredErrorMessage|De naam van de organisatie is leeg|  
|WebAuthenticationPasswordChangedNotice|Uw wachtwoord is bijgewerkt|  
|WebAuthenticationPasswordChangedNoticeTitle|Wachtwoord bijgewerkt|  
|WebAuthenticationPasswordCompareErrorMessage|Wachtwoorden komen niet overeen|  
|WebAuthenticationPasswordConfirmLabel|Wachtwoord bevestigen|  
|ValidatieErrorPasswordInvalidDetailed|Wachtwoord is te zwak.|  
|WebAuthenticationPasswordLabel|Wachtwoord|  
|ValidatieErrorPasswordVereist|Het wachtwoord is vereist.|  
|WebAuthenticationPasswordResetSendNotice|Wachtwoordbevestiging wijzigen is onderweg {0}naar . Volg de instructies in de e-mail om uw proces voor wachtwoordwijziging voort te zetten.|  
|WebAuthenticationPasswordResetSendNoticeheader|Uw verzoek tot het opnieuw instellen van uw wachtwoord is verwerkt|  
|WebAuthenticationPasswordResetSendNoticeTitle|Wachtwoordreset gevraagd|  
|WebAuthenticationRequestNotFoundNotice|Verzoek niet gevonden|  
|WebAuthenticationSenderEmailRequiredErrorMessage|E-mail van de afzender van meldingen is leeg|  
|WebAuthenticationSigninPasswordLabel|Bevestig de wijziging door een wachtwoord in te voeren|  
|WebAuthenticationMeldbevestiging|Registratiebevestiging e-mail is {0}onderweg naar\> .<br / Volg instructies in\> de e-mail om uw account te activeren.<br / Als de e-mail niet binnen enkele minuten in uw inbox binnen enkele minuten binnenkomt, controleer dan uw map met ongewenste e-mail.|  
|WebAuthenticationSignupConfirmNoticeHeader|Uw account is gemaakt|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Er is opnieuw een e-mail met registratiebevestiging verzonden|  
|WebAuthenticationSignupConfirmNoticeTitle|Account gemaakt|  
|WebAuthenticationTokenRequiredErrorMessage|Token is leeg|  
|WebAuthenticationUserAlRegisteredNotice|Het lijkt erop dat een gebruiker met deze e-mail is al geregistreerd in het systeem. Als je je wachtwoord bent vergeten, probeer het dan te herstellen of neem contact op met ons ondersteuningsteam.|  
|WebAuthenticationUserAlRegisteredNoticeHeader|Gebruiker al geregistreerd|  
|WebAuthenticationUserReedsGeregistreerdeopmerktitel|Reeds geregistreerd|  
|ButtonLabelChangePassword ButtonLabelChangePassword ButtonLabelChangePassword|Wachtwoord wijzigen|  
|ButtonLabelChangeAccountInfo|Accountgegevens wijzigen|  
|ButtonLabelCloseAccount|Account sluiten|  
|WebAuthenticationInvalidCaptchaErrorMessage|De ingevoerde tekst komt niet overeen met de tekst op de afbeelding. Probeer het opnieuw.|  
|ValidatieErrorCredentialsOngeldig|E-mail of wachtwoord is ongeldig. Corrigeer de fouten en probeer het opnieuw.|  
|WebAuthenticationRequestIsNotValid|Aanvraag is niet geldig|  
|WebAuthenticationUserIsNotConfirm|Bevestig uw inschrijving voordat u probeert in te loggen.|  
|WebAuthenticationInvalidEmailFormated|E-mail is ongeldig:{0}|  
|WebAuthenticationUserNotFound|Gebruiker is niet gevonden|  
|WebAuthenticationTenantNiet geregistreerd|Uw account behoort tot een Azure Active Directory-tenant die geen toegang heeft tot deze portal.|  
|WebAuthenticationAuthentication Mislukt|Verificatie is mislukt.|  
|WebAuthenticationGooglePlusNotIngeschakeld|Verificatie is mislukt. Als u de toepassing hebt geautoriseerd, neem dan contact op met de beheerder om ervoor te zorgen dat google-verificatie correct is geconfigureerd.|  
|ValidatieErrorAllowedTenantIsVereist|Toegestane huurder is vereist|  
|ValidatieErrorTenantisniet geldig|De Azure Active{0}Directory-tenant ' ' is ongeldig.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Log hier {0} in|  
|WebAuthenticationUserLimitNotice|Deze service heeft het maximum aantal toegestane gebruikers bereikt. Gelieve `<a href="mailto:{0}"\>contact the administrator</a\>` hun service te upgraden en gebruikersregistratie opnieuw in te schakelen.|  
|WebAuthenticationUserLimitNoticeHeader|Gebruikersregistratie uitgeschakeld|  
|WebAuthenticationUserLimitNoticeTitle|Gebruikersregistratie uitgeschakeld|  
|WebAuthenticationUserRegistrationDisabledNotice|Registratie van gebruikers is uitgeschakeld door de beheerder. Log dan in bij de externe identiteitsprovider.|  
|WebAuthenticationUserRegistrationDisabledNoticeheader|Gebruikersregistratie uitgeschakeld|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Gebruikersregistratie uitgeschakeld|  
|WebAuthenticationSignupPendingConfirmationNotice|Voordat we het aanmaken van uw account kunnen voltooien, moeten we uw e-mailadres verifiëren. We hebben een e-mail {0}gestuurd naar. Volg de instructies in de e-mail om uw account te activeren. Als de e-mail niet binnen enkele minuten binnen de komende minuten binnenkomt, controleer dan uw map met ongewenste e-mail.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|We vonden een onbevestigde account voor {0}het e-mailadres. Om het aanmaken van je account te voltooien, moeten we je e-mailadres verifiëren. We hebben een e-mail {0}gestuurd naar. Volg de instructies in de e-mail om uw account te activeren. Als de e-mail niet binnen enkele minuten binnen de komende minuten binnenkomt, controleer dan uw map met ongewenste e-mail|  
|WebAuthenticationSignupBevestigingBijnaDone|Bijna klaar|  
|WebAuthenticationSignupConfirmationEmailSent|We hebben een e-mail {0}gestuurd naar. Volg de instructies in de e-mail om uw account te activeren. Als de e-mail niet binnen enkele minuten binnen de komende minuten binnenkomt, controleer dan uw map met ongewenste e-mail.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail verzonden naar{0}|  
|WebAuthenticationNoAadTenantGeconfigureerd|Er is geen Azure Active Directory-tenant geconfigureerd voor de service.|  
|SelectievakjeGebruikersregistratievoorwaardenvereist|Ik ga `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`akkoord met de .|  
|Voorwaarden voor textblockuserregistratieverstrekt|Bekijk het eens`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogheadingTermsOfUse|Gebruiksrechtovereenkomst|  
|ValidatieBerichtToestemmingniet geaccepteerd|U moet akkoord gaan met de gebruiksvoorwaarden voordat u verder gaan.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a>Aanmeldingen  
  
|Name|Tekst|  
|----------|----------|  
|WebAuthenticationForgotPassword|Bent u uw wachtwoord vergeten?|  
|WebAuthenticationIfAdministrator|Als u beheerder bent, `<a href="{0}"\>here</a\>`moet u zich aanmelden.|  
|WebAuthenticationNotALid|Nog geen lid? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Denk aan mij op deze computer|  
|WebAuthenticationSigininMetPassword|Inloggen met uw gebruikersnaam en wachtwoord|  
|WebAuthenticationSigninTitle|Aanmelden|  
|WebAuthenticationSignUpNow|Nu registreren|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a>Aanmeldingstekenreeksen  
  
|Name|Tekst|  
|----------|----------|  
|PageTitleSignup PageTitleSignup PageTitle|Aanmelden|  
|WebAuthenticationAlALid|Ben je al lid?|  
|WebAuthenticationCreateNewAccount|Een nieuw API Management-account maken|  
|WebAuthenticationSigninNow|Log hier in|  
|ButtonLabelSignup|Aanmelden|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a>AbonnementlistTekenreeksen  
  
|Name|Tekst|  
|----------|----------|  
|AbonnementOpzeggenBevestiging|Weet u zeker dat u dit abonnement wilt opzeggen?|  
|AbonnementvernieuwdeBevestiging|Weet u zeker dat u dit abonnement wilt verlengen?|  
|WebDevelopersManageSubscriptions|Abonnementen beheren|  
|WebDevelopersPrimaryKey|Primaire sleutel|  
|WebDevelopersRegenerateLink WebDevelopersRegenerateLink|Opnieuw genereren|  
|WebDevelopersSecondaryKey|Secundaire sleutel|  
|ButtonLabelShowKey ButtonLabelShowKey|Weergeven|  
|ButtonLabelRenewAbonnement|Verlengen|  
|WebDevelopersSubscriptionGevraagd|Gevraagd op{0}|  
|WebDevelopersSubscriptionRequestedState|Aangevraagd|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Status|  
|WebDevelopersUsageStatisticsLink WebDevelopersUsageStatisticsLink WebDevelopersUsageStatisticsLink WebDeveloper|Analyserapporten|  
|WebDevelopersYourAbonnementen|Uw abonnementen|  
|AbonnementPropertyLabelRequestedDate|Gevraagd op|  
|AbonnementPropertyLabelStartDate|Gestart op|  
|PageTitleRenameSubscription|Naamname wijzigen|  
|AbonnementPropertyLabelName|Abonnementsnaam|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a>Abonnementen  
  
|Name|Tekst|  
|----------|----------|  
|SectionheadingCloseAccount|Wilt u uw account sluiten?|  
|PageTitleDeveloperProfiel PageTitleDeveloperProfile PageTitleDeveloperProfile PageTitle|Profiel|  
|ButtonLabelHideKey|Verbergen|  
|ButtonLabelRegenerateKey|Opnieuw genereren|  
|InformatieMessageKeyWasRegenerated|Weet je zeker dat je deze sleutel wilt regenereren?|  
|ButtonLabelShowKey ButtonLabelShowKey|Weergeven|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a>Profieltekenreeksen bijwerken  
  
|Name|Tekst|  
|----------|----------|  
|ButtonLabelUpdateProfiel|Profiel bijwerken|  
|PageTitleUpdateProfiel PageTitleUpdateProfile PageTitleUpdateProfile PageTitle|Accountgegevens bijwerken|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a>Userprofile  
  
|Name|Tekst|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Accountgegevens wijzigen|  
|ButtonLabelChangePassword ButtonLabelChangePassword ButtonLabelChangePassword|Wachtwoord wijzigen|  
|ButtonLabelCloseAccount|Account sluiten|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Voornaam|  
|TextboxLabelENaam|Achternaam|  
|TextboxLabelNotificationsSenderEmail|E-mail met afzendermeldingen|  
|TextboxLabelOrganizationName|Organisatienaam|  
|AbonnementStateActive|Actief|  
|Abonnementstategeannuleerd|Geannuleerd|  
|AbonnementStateVerlopen|Verlopen|  
|AbonnementStateAfgewezen|Geweigerd|  
|AbonnementStateRequested|Aangevraagd|  
|AbonnementStateopgeschort|Onderbroken|  
|StandaardAbonnementNameTemplate|{0}(standaard)|  
|AbonnementNameTemplate|Toegang tot ontwikkelaars #{0}|  
|TextboxLabelSubscriptionName|Abonnementsnaam|  
|ValidatieMessageAbonnementnaamvereist|De naam van het abonnement kan niet leeg zijn.|  
|ApiManagementUserLimitBereikt|Deze service heeft het maximum aantal toegestane gebruikers bereikt. Upgrade naar een hogere prijscategorie.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a>Glyph-bronnen  
 API Management developer portal templates can use the glyphs from [Glyphicons from Bootstrap.](https://getbootstrap.com/components/#glyphicons) Deze set glyphs bevat meer dan 250 glyphs in lettertype-indeling van de [set Glyphicon](https://glyphicons.com/) Halflings. Als u een glyph uit deze set wilt gebruiken, gebruikt u de volgende syntaxis.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Zie [Glyphicons van Bootstrap](https://getbootstrap.com/components/#glyphicons)voor de volledige lijst met glyphs.

## <a name="next-steps"></a>Volgende stappen
Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.
