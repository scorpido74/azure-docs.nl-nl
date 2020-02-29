---
title: Id van lokalisatie reeks-Azure Active Directory B2C | Microsoft Docs
description: Geef de Id's op voor een inhouds definitie met de id API. signuporsignin in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3af62a75228959478a80c2628307fff2b47c3c4a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187488"
---
# <a name="localization-string-ids"></a>Id's van lokalisatiereeksen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het **lokalisatie** -element biedt ondersteuning voor meerdere land instellingen of talen in het beleid voor de gebruikers ritten. Dit artikel bevat de lijst met lokalisatie-Id's die u in uw beleid kunt gebruiken. Zie [lokalisatie](localization.md)om vertrouwd te raken met de lokalisatie van de gebruikers interface.

## <a name="sign-up-or-sign-in-page-elements"></a>Registratie-of aanmeldings pagina-elementen

De volgende Id's worden gebruikt voor een inhouds definitie met de ID `api.signuporsignin`.

| Id | Standaardwaarde |
| -- | ------------- |
| **local_intro_email** | Meld u aan met uw bestaande account |
| **logonIdentifier_email** | E-mailadres |
| **requiredField_email** | Voer uw e-mail adres in |
| **invalid_email** | Voer een geldig e-mail adres in |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' *+/=? ^ _\`{\|} ~-] + @ [a-zA-Z0-9-] + (?:\\. [ a-zA-Z0-9-] +)* $ |
| **local_intro_username** | Meld u aan met uw gebruikers naam |
| **logonIdentifier_username** | Gebruikersnaam |
| **requiredField_username** | Voer uw gebruikers naam in |
| **wacht woord** | Wachtwoord |
| **requiredField_password** | Voer uw wacht woord in |
| **invalid_password** | Het wacht woord dat u hebt opgegeven, heeft niet de verwachte indeling. |
| **forgotpassword_link** | Uw wacht woord verg eten? |
| **createaccount_intro** | Hebt u geen account? |
| **createaccount_link** | Nu registreren |
| **divider_title** | OF |
| **cancel_message** | De gebruiker is het wacht woord verg eten |
| **button_signin** | Aanmelden |
| **social_intro** | Meld u aan met uw sociale account |
  **remember_me** |Aangemeld blijven|
| **unknown_error** | U ondervindt problemen bij het aanmelden. Probeer het later opnieuw. |

In het volgende voor beeld ziet u het gebruik van een aantal van de elementen van de gebruikers interface op de registratie-of aanmeldings pagina:

![UX-elementen voor registratie of aanmeldings pagina](./media/localization-string-ids/localization-susi.png)

De id's van de ID-providers worden geconfigureerd in het **ClaimsExchange** -element van de gebruikers reis. Als u de naam van de ID-provider wilt lokaliseren, wordt het **element type** ingesteld op `ClaimsProvider`, terwijl de **StringId** is ingesteld op de id van de `ClaimsExchange`.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

In het volgende voor beeld wordt de Facebook-ID-provider naar Arabisch gelokaliseerd:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Fout berichten bij het registreren of aanmelden

| Id | Standaardwaarde |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Het wacht woord is onjuist. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Uw account is niet gevonden. |
| **UserMessageIfOldPasswordUsed** | Het lijkt erop dat u een oud wacht woord hebt gebruikt. |
| **DefaultMessage** | Ongeldige gebruikers naam of wacht woord. |
| **UserMessageIfUserAccountDisabled** | Uw account is vergrendeld. Neem contact op met de ondersteunings medewerker om deze te ontgrendelen en probeer het opnieuw. |
| **UserMessageIfUserAccountLocked** | Uw account is tijdelijk vergrendeld om onbevoegd gebruik te voor komen. Probeer het later opnieuw. |
| **AADRequestsThrottled** | Er zijn op dit moment te veel aanvragen. Wacht enige tijd en probeer het opnieuw. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Gebruikers interface-elementen voor registratie en zelf bevestigde pagina's

Hieronder ziet u de Id's voor een inhouds definitie met een ID van `api.localaccountsignup` of een inhouds definitie die begint met `api.selfasserted`, zoals `api.selfasserted.profileupdate` en `api.localaccountpasswordreset`.

| Id | Standaardwaarde |
| -- | ------------- |
| **ver_sent** | De verificatie code is verzonden naar: |
| **ver_but_default** | Standaard |
| **cancel_message** | De gebruiker heeft het invoeren van zelfbevestigende informatie geannuleerd |
| **preloader_alt** | Een ogenblik geduld |
| **ver_but_send** | Verificatie code verzenden |
| **alert_yes** | Ja |
| **error_fieldIncorrect** | Een of meer velden zijn onjuist ingevuld. Controleer uw vermeldingen en probeer het opnieuw. |
| **jaareinde** | Year |
| **verifying_blurb** | De gegevens worden verwerkt. een ogen blik geduld... |
| **button_cancel** | Annuleren |
| **ver_fail_no_retry** | U hebt te veel onjuiste pogingen gedaan. Probeer het later opnieuw. |
| **blijft** | Month |
| **ver_success_msg** | E-mail adres geverifieerd. U kunt nu door gaan. |
| **months** | Januari, februari, maart, april, mei, juni, juli, augustus, september, oktober, november, december |
| **ver_fail_server** | Er zijn problemen met het controleren van uw e-mail adres. Geef een geldig e-mail adres op en probeer het opnieuw. |
| **error_requiredFieldMissing** | Een vereist veld ontbreekt. Vul alle vereiste velden in en probeer het opnieuw. |
| **initial_intro** | Geef de volgende details op. |
| **ver_but_resend** | Nieuwe code verzenden |
| **button_continue** | Maken |
| **error_passwordEntryMismatch** | De invoer velden voor het wacht woord komen niet overeen. Voer hetzelfde wacht woord in beide velden in en probeer het opnieuw. |
| **ver_incorrect_format** | Onjuiste indeling. |
| **ver_but_edit** | E-mail wijzigen |
| **ver_but_verify** | Code verifiëren |
| **alert_no** | Nee |
| **ver_info_msg** | De verificatie code is verzonden naar het postvak in. Kopieer het naar het invoervak hieronder. |
| **profieldag** | Day |
| **ver_fail_throttled** | Er zijn te veel aanvragen om dit e-mail adres te controleren. Wacht even en probeer het opnieuw. |
| **helplink_text** | Wat is dit? |
| **ver_fail_retry** | Deze code is onjuist. Probeer het opnieuw. |
| **alert_title** | Het invoeren van uw gegevens annuleren |
| **required_field** | Deze gegevens zijn vereist. |
| **alert_message** | Weet u zeker dat u het invoeren van uw gegevens wilt annuleren? |
| **ver_intro_msg** | Verificatie is nood zakelijk. Klik op de knop verzenden. |
| **ver_input** | Verificatiecode |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Fout berichten over het registreren en automatisch bevestigen van pagina's

| Id | Standaardwaarde |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Er bestaat al een gebruiker met de opgegeven ID. Kies een andere naam. |
| **UserMessageIfClaimNotVerified** | Claim niet geverifieerd: {0} |
| **UserMessageIfIncorrectPattern** | Onjuist patroon voor: {0} |
| **UserMessageIfMissingRequiredElement** | Ontbrekend vereist element: {0} |
| **UserMessageIfValidationError** | Fout in validatie door: {0} |
| **UserMessageIfInvalidInput** | {0} heeft ongeldige invoer. |
| **ServiceThrottled** | Er zijn op dit moment te veel aanvragen. Wacht enige tijd en probeer het opnieuw. |

In het volgende voor beeld ziet u het gebruik van enkele van de elementen van de gebruikers interface op de registratie pagina:

![Registratie pagina met de namen van de gebruikers interface-elementen labelen](./media/localization-string-ids/localization-sign-up.png)

In het volgende voor beeld ziet u het gebruik van enkele van de elementen van de gebruikers interface op de registratie pagina, nadat de gebruiker op de knop verificatie code verzenden heeft geklikt:

![Aanmeldings pagina e-mail verificatie UX-elementen](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Gebruikers interface-elementen van de pagina telefoon Factor Authentication

Hier volgen de Id's voor een inhouds definitie met de ID `api.phonefactor`.

| Id | Standaardwaarde |
| -- | ------------- |
| **button_verify** | Bel me |
| **country_code_label** | Landnummer |
| **cancel_message** | De gebruiker heeft de multi-factor Authentication geannuleerd |
| **text_button_send_second_code** | een nieuwe code verzenden |
| **code_pattern** | \\d{6} |
| **intro_mixed** | We hebben het volgende nummer in de record voor u. We kunnen een code via SMS of telefoon verzenden om u te verifiëren. |
| **intro_mixed_p** | We hebben het volgende nummer in de record voor u. Kies een nummer dat wij telefonisch kunnen of verzend code via SMS om u te verifiëren. |
| **button_verify_code** | Code verifiëren |
| **requiredField_code** | Voer de verificatie code in die u hebt ontvangen |
| **invalid_code** | Voer de 6-cijferige code in die u hebt ontvangen |
| **button_cancel** | Annuleren |
| **local_number_input_placeholder_text** | Telefoonnummer |
| **button_retry** | Opnieuw proberen |
| **alternative_text** | Ik heb mijn telefoon niet |
| **intro_phone_p** | We hebben het volgende nummer in de record voor u. Kies een telefoon nummer dat kan worden geverifieerd. |
| **intro_phone** | We hebben het volgende nummer in de record voor u. We zullen u telefonisch verifiëren. |
| **enter_code_text_intro** | Voer uw verificatie code hieronder in of  |
| **intro_entry_phone** | Voer hieronder een nummer in waarop u kunt worden geverifieerd. |
| **intro_entry_sms** | Voer hieronder een nummer in dat we via SMS een code kunnen verzenden om u te verifiëren. |
| **button_send_code** | Code verzenden |
| **invalid_number** | Voer een geldig telefoon nummer in |
| **intro_sms** | We hebben het volgende nummer in de record voor u. Er wordt een code via SMS verzonden om u te verifiëren. |
| **intro_entry_mixed** | Voer hieronder een nummer in waarmee u een code via SMS of telefoon kunt verzenden om u te verifiëren. |
| **number_pattern** | ^\\+ (?: [0-9] [\\x20-]?){6,14}[0-9] $ |
| **intro_sms_p** |We hebben het volgende nummer in de record voor u. Kies een nummer dat via SMS een code kan verzenden om u te verifiëren. |
| **requiredField_countryCode** | Selecteer uw land code |
| **requiredField_number** | Voer uw telefoon nummer in |
| **country_code_input_placeholder_text** |Land of regio |
| **number_label** | Telefoonnummer |
| **error_tryagain** | Het telefoon nummer dat u hebt ingevoerd, is bezet of niet beschikbaar. Controleer het nummer en probeer het opnieuw. |
| **error_incorrect_code** | De ingevoerde verificatie code komt niet overeen met onze records. Probeer het opnieuw of vraag een nieuwe code aan. |
| **countryList** | {\"DEFAULT\":\"Country/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Islands\",\"AL\":\"Albania\",\"DZ\":\"Algeria\",\"AS\":\"American Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antarctica\",\"AG\":\"Antigua and Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenia\",\"AW\":\"Aruba\",\"AU\":\"Australia\",\"AT\":\"Austria\",\"AZ\":\"Azerbaijan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Belarus\",\"BE\":\"Belgium\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnia and Herzegovina\",\"BW\":\"Botswana\",\"BV\":\"Bouvet Island\",\"BR\":\"Brazil\",\"IO\":\"British Indian Ocean Territory\",\"VG\":\"British Virgin Islands\",\"BN\":\"Brunei\",\"BG\":\"Bulgaria\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Cambodia\",\"CM\":\"Cameroon\",\"CA\":\"Canada\",\"KY\":\"Cayman Islands\",\"CF\":\"Central African Republic\",\"TD\":\"Chad\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Christmas Island\",\"CC\":\"Cocos (Keeling) Islands\",\"CO\":\"Colombia\",\"KM\":\"Comoros\",\"CG\":\"Congo\",\"CD\":\"Congo (DRC)\",\"CK\":\"Cook Islands\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Croatia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Cyprus\",\"CZ\":\"Czech Republic\",\"DK\":\"Denmark\",\"DJ\":\"Djibouti\",\"DM\":\"Dominica\",\"DO\":\"Dominican Republic\",\"EC\":\"Ecuador\",\"EG\":\"Egypt\",\"SV\":\"El Salvador\",\"GQ\":\"Equatorial Guinea\",\"ER\":\"Eritrea\",\"EE\":\"Estonia\",\"ET\":\"Ethiopia\",\"FK\":\"Falkland Islands\",\"FO\":\"Faroe Islands\",\"FJ\":\"Fiji\",\"FI\":\"Finland\",\"FR\":\"France\",\"GF\":\"French Guiana\",\"PF\":\"French Polynesia\",\"TF\":\"French Southern Territories\",\"GA\":\"Gabon\",\"GM\":\"Gambia\",\"GE\":\"Georgia\",\"DE\":\"Germany\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Greece\",\"GL\":\"Greenland\",\"GD\":\"Grenada\",\"GP\":\"Guadeloupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Heard Island and McDonald Islands\",\"HN\":\"Honduras\",\"HK\":\"Hong Kong SAR\",\"HU\":\"Hungary\",\"IS\":\"Iceland\",\"IN\":\"India\",\"ID\":\"Indonesia\",\"IR\":\"Iran\",\"IQ\":\"Iraq\",\"IE\":\"Ireland\",\"IM\":\"Isle of Man\",\"IL\":\"Israel\",\"IT\":\"Italy\",\"JM\":\"Jamaica\",\"JP\":\"Japan\",\"JE\":\"Jersey\",\"JO\":\"Jordan\",\"KZ\":\"Kazakhstan\",\"KE\":\"Kenya\",\"KI\":\"Kiribati\",\"KR\":\"Korea\",\"KW\":\"Kuwait\",\"KG\":\"Kyrgyzstan\",\"LA\":\"Laos\",\"LV\":\"Latvia\",\"LB\":\"Lebanon\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libya\",\"LI\":\"Liechtenstein\",\"LT\":\"Lithuania\",\"LU\":\"Luxembourg\",\"MO\":\"Macao SAR\",\"MK\":\"North Macedonia\",\"MG\":\"Madagascar\",\"MW\":\"Malawi\",\"MY\":\"Malaysia\",\"MV\":\"Maldives\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Marshall Islands\",\"MQ\":\"Martinique\",\"MR\":\"Mauritania\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Mexico\",\"FM\":\"Micronesia\",\"MD\":\"Moldova\",\"MC\":\"Monaco\",\"MN\":\"Mongolia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Morocco\",\"MZ\":\"Mozambique\",\"MM\":\"Myanmar\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Netherlands\",\"NC\":\"New Caledonia\",\"NZ\":\"New Zealand\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Norfolk Island\",\"KP\":\"North Korea\",\"MP\":\"Northern Mariana Islands\",\"NO\":\"Norway\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Palestinian Authority\",\"PA\":\"Panama\",\"PG\":\"Papua New Guinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Philippines\",\"PN\":\"Pitcairn Islands\",\"PL\":\"Poland\",\"PT\":\"Portugal\",\"PR\":\"Puerto Rico\",\"QA\":\"Qatar\",\"RE\":\"Réunion\",\"RO\":\"Romania\",\"RU\":\"Russia\",\"RW\":\"Rwanda\",\"BL\":\"Saint Barthélemy\",\"KN\":\"Saint Kitts and Nevis\",\"LC\":\"Saint Lucia\",\"MF\":\"Saint Martin\",\"PM\":\"Saint Pierre and Miquelon\",\"VC\":\"Saint Vincent and the Grenadines\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé and Príncipe\",\"SA\":\"Saudi Arabia\",\"SN\":\"Senegal\",\"RS\":\"Serbia\",\"SC\":\"Seychelles\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapore\",\"SX\":\"Sint Maarten\",\"SK\":\"Slovakia\",\"SI\":\"Slovenia\",\"SB\":\"Solomon Islands\",\"SO\":\"Somalia\",\"ZA\":\"South Africa\",\"GS\":\"South Georgia and South Sandwich Islands\",\"SS\":\"South Sudan\",\"ES\":\"Spain\",\"LK\":\"Sri Lanka\",\"SH\":\"St Helena, Ascension, Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Swaziland\",\"SE\":\"Sweden\",\"CH\":\"Switzerland\",\"SY\":\"Syria\",\"TW\":\"Taiwan\",\"TJ\":\"Tajikistan\",\"TZ\":\"Tanzania\",\"TH\":\"Thailand\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad and Tobago\",\"TN\":\"Tunisia\",\"TR\":\"Turkey\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks and Caicos Islands\",\"TV\":\"Tuvalu\",\"UM\":\"U.S. Outlying Islands\",\"VI\":\"U.S. Virgin Islands\",\"UG\":\"Uganda\",\"UA\":\"Ukraine\",\"AE\":\"United Arab Emirates\",\"GB\":\"United Kingdom\",\"US\":\"United States\",\"UY\":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Vatican City\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis and Futuna\",\"YE\":\"Yemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Het telefoon nummer dat u hebt ingevoerd, is onbereikbaar. |
| **error_449** | De gebruiker heeft het aantal nieuwe pogingen overschreden. |
| **verification_code_input_placeholder_text** | Verificatiecode |

In het volgende voor beeld ziet u het gebruik van een aantal van de elementen van de gebruikers interface op de pagina MFA-inschrijving:

![Aanmeldings pagina e-mail verificatie UX-elementen](./media/localization-string-ids/localization-mfa1.png)

In het volgende voor beeld ziet u het gebruik van enkele van de elementen van de gebruikers interface op de pagina MFA-validatie:

![Aanmeldings pagina e-mail verificatie UX-elementen](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Gebruikers interface-elementen voor controle weergave

Hier volgen de Id's voor een [besturings element voor controle weergave](display-control-verification.md)

| Id | Standaardwaarde |
| -- | ------------- |
|verification_control_but_change_claims |Wijzigen |
|verification_control_fail_send_code |Kan de code niet verzenden. Probeer het later opnieuw. |
|verification_control_fail_verify_code |De code kan niet worden geverifieerd. Probeer het later opnieuw. |
|verification_control_but_send_code |Code verzenden |
|verification_control_but_send_new_code |Nieuwe code verzenden |
|verification_control_but_verify_code |Code verifiëren |

## <a name="one-time-password-error-messages"></a>Eenmalige fout berichten voor wacht woord
Hier volgen de Id's voor een eenmalig [wacht woord technische profiel](one-time-password-technical-profile.md) fout berichten

| Id | Standaardwaarde |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Eenmalige verificatie van het wacht woord heeft het maximum aantal pogingen overschreden |
|UserMessageIfSessionDoesNotExist |Eenmalige sessie voor wachtwoord verificatie is verlopen |
|UserMessageIfSessionConflict |Eenmalige sessie voor wachtwoord verificatie is een conflict |
|UserMessageIfInvalidCode |Er is een onjuist wacht woord opgegeven voor de verificatie |








