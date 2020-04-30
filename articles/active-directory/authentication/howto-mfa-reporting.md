---
title: Toegang en gebruiks rapporten voor Azure MFA-Azure Active Directory
description: Hierin wordt beschreven hoe u de functie Azure Multi-Factor Authentication-rapporten gebruikt.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df562d65ad064efb1be337e0b68cb8638536981
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112759"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporten in azure Multi-Factor Authentication

Azure Multi-Factor Authentication biedt verschillende rapporten die kunnen worden gebruikt door u en uw organisatie toegankelijk via de Azure Portal. De volgende tabel bevat de beschik bare rapporten:

| Rapport | Locatie | Beschrijving |
|:--- |:--- |:--- |
| Geschiedenis van geblokkeerde gebruikers | Azure AD > Security > MFA > blok keren/deblokkeren van gebruikers | Toont de geschiedenis van aanvragen om gebruikers te blok keren of deblokkeren. |
| Gebruiks-en fraude waarschuwingen | Aanmeldingen bij Azure AD-> | Bevat informatie over het algehele gebruik, de gebruikers samenvatting en de gebruikers gegevens. en een geschiedenis van fraude waarschuwingen die zijn ingediend tijdens het opgegeven datum bereik. |
| Gebruik voor on-premises onderdelen | Activiteiten rapport van Azure AD > Security > MFA > | Bevat informatie over het algehele gebruik voor MFA via de NPS-extensie, ADFS en MFA-server. |
| Geschiedenis van overgeslagen gebruikers | Azure AD > Security > MFA > eenmalige bypass | Biedt een geschiedenis van aanvragen om Multi-Factor Authentication voor een gebruiker over te slaan. |
| Server status | Azure AD > Security > MFA > server-status | Hier wordt de status weer gegeven van Multi-Factor Authentication-servers die aan uw account zijn gekoppeld. |

## <a name="view-mfa-reports"></a>MFA-rapporten weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** > **beveiliging** > **MFA**.
3. Selecteer het rapport dat u wilt weer geven.

   ![Status rapport van MFA-server server in de Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Rapport over Azure AD-aanmeldingen

Met het **activiteiten rapport voor aanmeldingen** in de [Azure Portal](https://portal.azure.com), kunt u de informatie ophalen die u nodig hebt om te bepalen hoe uw omgeving bezig is.

Het rapport met aanmeldingen kan u informatie geven over het gebruik van beheerde toepassingen en aanmeldings activiteiten van gebruikers, waaronder informatie over het gebruik van multi-factor Authentication (MFA). De MFA-gegevens geven u inzicht in hoe MFA werkt in uw organisatie. Hiermee kunt u vragen beantwoorden zoals:

- Is de aanmelding gelukt met MFA?
- Hoe heeft de gebruiker MFA voltooid?
- Waarom kan de gebruiker MFA niet voltooien?
- Hoeveel gebruikers gebruiken MFA?
- Hoeveel gebruikers kunnen MFA niet voltooien?
- Wat zijn de meest voorkomende MFA-problemen waarmee eindgebruikers te maken hebben?

Deze gegevens zijn beschikbaar via de [Azure Portal](https://portal.azure.com) en de [rapportage-API](../reports-monitoring/concept-reporting-api.md).

![Azure AD-aanmeld rapport in de Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Structuur van het registratie rapport

De rapporten voor aanmeldingsactiviteiten voor MFA bieden u toegang tot de volgende informatie:

**MFA is vereist:** of MFA is vereist voor aanmelding of niet. MFA kan worden vereist wegens MFA per gebruiker, voorwaardelijke toegang of andere redenen. Mogelijke waarden zijn **Ja** of **Nee**.

**MFA-resultaat:** meer informatie over of MFA is voldaan of geweigerd:

- Als MFA is voldaan, bevat deze kolom meer informatie over hoe dit is gebeurd.
   - Azure Multi-Factor Authentication
      - voltooid in de cloud
      - verlopen als gevolg van beleid dat is geconfigureerd in de tenant
      - registratie gevraagd
      - voldaan door de claim in het token
      - voldaan door de claim geleverd door de externe provider
      - voldaan door geavanceerde verificatie
      - overgeslagen omdat de uitgevoerde stroom een Windows broker-aanmeldingsstroom is
      - overgeslagen vanwege een app-wachtwoord
      - overgeslagen vanwege de locatie
      - overgeslagen vanwege een geregistreerd apparaat
      - overgeslagen vanwege een geregistreerd apparaat
      - voltooid
   - Omgeleid naar externe provider voor Multi-Factor Authentication

- Als MFA is geweigerd, bevat deze kolom de reden van weigering.
   - Azure Multi-Factor Authentication is geweigerd
      - verificatie wordt uitgevoerd
      - dubbele verificatiepoging
      - onjuiste code te vaak ingevoerd
      - ongeldige verificatie
      - ongeldig verificatiecode via mobiele app
      - onjuiste configuratie
      - telefonische oproep verzonden naar voicemail
      - telefoonnummer heeft een ongeldige indeling
      - servicefout
      - kan de telefoon van de gebruiker niet bereiken
      - kan de mobiele-app-melding niet verzenden naar het apparaat
      - kan de mobiele-app-melding niet verzenden
      - gebruiker heeft verificatie geweigerd
      - gebruiker heeft niet gereageerd op mobiele-app-melding
      - gebruiker heeft geen verificatiemethoden geregistreerd
      - gebruiker heeft onjuiste code ingevoerd
      - gebruiker heeft onjuiste pincode ingevoerd
      - gebruiker heeft opgehangen zonder te verifiëren
      - gebruiker is geblokkeerd
      - gebruiker heeft de verificatiecode niet ingevoerd
      - gebruiker is niet gevonden
      - verificatiecode is al gebruikt

**MFA-verificatiemethode:** de verificatiemethode die de gebruiker heeft gebruikt om MFA te voltooien. Mogelijke waarden zijn:

- Sms-bericht
- Meldingen via mobiele app
- Telefonische oproep (verificatie via telefoonnummer)
- Verificatiecode via mobiele app
- Telefonische oproep (telefoon op het werk)
- Telefonische oproep (verificatie via telefoonnummer)

**Detail van MFA-verificatie:** verwijderde versie van het telefoonnummer, bijvoorbeeld: + X XXXXXXXX64.

**Voorwaardelijke toegang** Informatie over beleid voor voorwaardelijke toegang dat is betrokken bij de aanmeldings poging, waaronder:

- Beleidsnaam
- Besturings elementen toewijzen
- Sessie besturings elementen
- Resultaat

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Power shell-rapportage voor gebruikers die zijn geregistreerd voor MFA

Controleer eerst of de [MSOnline v1 Power shell-module](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) is geïnstalleerd.

Identificeer gebruikers die zijn geregistreerd voor MFA met behulp van de volgende Power shell. Met deze reeks opdrachten worden uitgeschakelde gebruikers uitgesloten, omdat deze accounts niet kunnen worden geverifieerd bij Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identificeer gebruikers die zich niet hebben geregistreerd voor MFA met behulp van de Power shell die volgt. Met deze reeks opdrachten worden uitgeschakelde gebruikers uitgesloten, omdat deze accounts niet kunnen worden geverifieerd bij Azure AD.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Gebruikers en uitvoer methoden identificeren die zijn geregistreerd. 

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Mogelijke resultaten in activiteiten rapporten

De volgende tabel kan worden gebruikt om problemen met multi-factor Authentication op te lossen met behulp van de gedownloade versie van het multi-factor Authentication-activiteiten rapport. Ze worden niet direct weer gegeven in de Azure Portal.

| Aanroep resultaat | Beschrijving | Uitgebreide beschrijving |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PINCODE ingevoerd | De gebruiker heeft een pincode ingevoerd. Als de verificatie is geslaagd, wordt de juiste pincode ingevoerd. Als verificatie is geweigerd, wordt er een onjuiste pincode ingevoerd of is de gebruiker ingesteld op de standaard modus. |
| SUCCESS_NO_PIN | Alleen # ingevoerd | Als de gebruiker is ingesteld op pincode modus en de verificatie wordt geweigerd, betekent dit dat de gebruiker de pincode niet heeft ingevoerd en alleen #.  Als de gebruiker is ingesteld op de standaard modus en de verificatie slaagt, betekent dit dat de gebruiker alleen de waarde # heeft opgegeven als in de standaard modus. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Niet ingedrukt na invoer | De gebruiker heeft geen DTMF-cijfers verzonden omdat # niet is ingevoerd. Andere ingevoerde cijfers worden niet verzonden, tenzij # wordt ingevoerd om aan te geven dat de invoer is voltooid. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Geen telefoon invoer-time-out | De oproep is beantwoord, maar er is geen reactie. Dit geeft meestal aan dat de oproep is opgehaald via voice mail. |
| SUCCESS_PIN_EXPIRED | De pincode is verlopen en niet gewijzigd | De pincode van de gebruiker is verlopen en er wordt gevraagd om deze te wijzigen, maar de wijziging van de pincode is niet voltooid. |
| SUCCESS_USED_CACHE | Gebruikte cache | De authenticatie is geslaagd zonder Multi-Factor Authentication aanroep, omdat een eerdere geslaagde verificatie voor dezelfde gebruikers naam is opgetreden binnen de geconfigureerde tijds duur van de cache. |
| SUCCESS_BYPASSED_AUTH | Verificatie overs Laan | De verificatie is geslaagd met een eenmalige bypass die is geïnitieerd voor de gebruiker. Bekijk het rapport geschiedenis van overgeslagen gebruikers voor meer informatie over de bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Gebruikte IP-cache | De verificatie is geslaagd zonder een Multi-Factor Authentication aanroep sinds een eerdere geslaagde verificatie voor dezelfde gebruikers naam, hetzelfde verificatie type, toepassings naam en IP-adres binnen de geconfigureerde tijds duur van de cache. |
| SUCCESS_USED_APP_BASED_CACHE | Gebruikte cache op basis van apps | De verificatie is geslaagd zonder Multi-Factor Authentication aanroep sinds een eerdere geslaagde authenticatie voor dezelfde gebruikers naam, hetzelfde authenticatie type en toepassings naam binnen de geconfigureerde tijds duur van de cache. |
| SUCCESS_INVALID_INPUT | Ongeldige telefoon invoer | Het antwoord dat is verzonden vanaf de telefoon, is ongeldig. Dit kan afkomstig zijn van een faxapparaat of modem of de gebruiker heeft * opgegeven als onderdeel van de pincode. |
| SUCCESS_USER_BLOCKED | De gebruiker is geblokkeerd | Het telefoon nummer van de gebruiker is geblokkeerd. Een geblokkeerd nummer kan door de gebruiker worden geïnitieerd tijdens een authenticatie oproep of door een beheerder met behulp van de Azure Portal. <br> Opmerking: een geblokkeerd nummer is ook een byproduct van een fraude waarschuwing. |
| SUCCESS_SMS_AUTHENTICATED | Tekst bericht geverifieerd | Voor een test bericht in twee richtingen heeft de gebruiker goed gereageerd met de eenmalige wachtwoord code (OTP) of OTP + pincode. |
| SUCCESS_SMS_SENT | SMS-bericht verzonden | Voor tekst bericht is het SMS-bericht dat de eenmalige wachtwoord code (OTP) heeft verzonden. De gebruiker voert de OTP of OTP + pincode in de toepassing in om de verificatie te volt ooien. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Geverifieerde mobiele app | De gebruiker is geverifieerd via de mobiele app. |
| SUCCESS_OATH_CODE_PENDING | OATH-code in behandeling | De gebruiker is om hun OATH-code gevraagd, maar heeft niet gereageerd. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-code geverifieerd | De gebruiker heeft een geldige OATH-code ingevoerd wanneer daarom wordt gevraagd. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | OATH-code voor terugval gecontroleerd | De gebruiker heeft de verificatie geweigerd met behulp van de primaire Multi-Factor Authentication methode en vervolgens een geldige OATH-code voor terugval gegeven. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Terugval beveiligings vragen beantwoord | De gebruiker heeft de verificatie geweigerd met behulp van de primaire Multi-Factor Authentication methode en vervolgens zijn de beveiligings vragen op de juiste wijze beantwoord voor terugval. |
| FAILED_PHONE_BUSY | Verificatie wordt al uitgevoerd | Multi-Factor Authentication is al bezig met het verwerken van een verificatie voor deze gebruiker. Dit wordt vaak veroorzaakt door RADIUS-clients die meerdere verificatie aanvragen verzenden tijdens dezelfde aanmelding. |
| CONFIG_ISSUE | Telefoon onbereikbaar | Er is geprobeerd om een aanroep uit te voeren, maar deze kan niet worden geplaatst of niet worden beantwoord. Dit omvat het gespreks signaal, het Fast Busy-signaal (verbinding verbroken), Tri-tinten (getal niet meer in de service), er is een time-out opgetreden tijdens het belsignaal, enzovoort. |
| FAILED_INVALID_PHONENUMBER | Ongeldige indeling van telefoon nummer | Het telefoon nummer heeft een ongeldige indeling. Telefoon nummers moeten numeriek zijn en moeten 10 cijfers bevatten voor land nummer + 1 (Verenigde Staten & Canada). |
| FAILED_USER_HUNGUP_ON_US | Gebruiker heeft opgehangen | De gebruiker heeft de telefoon beantwoord, maar vervolgens opgehangen zonder op knoppen te drukken. |
| FAILED_INVALID_EXTENSION | Ongeldige extensie | De extensie bevat ongeldige tekens. Alleen cijfers, komma's, * en # zijn toegestaan. Er kan ook een @-voor voegsel worden gebruikt. |
| FAILED_FRAUD_CODE_ENTERED | Fraude code ingevoerd | De gebruiker heeft gekozen voor het melden van fraude tijdens de oproep, wat leidt tot een geweigerde authenticatie en een geblokkeerd telefoon nummer.| 
| FAILED_SERVER_ERROR | Kan de aanroep niet plaatsen | De oproep kan niet door de Multi-Factor Authentication-service worden geplaatst. |
| FAILED_SMS_NOT_SENT | SMS-bericht kan niet worden verzonden | Het SMS-bericht kan niet worden verzonden. De verificatie is geweigerd. |
| FAILED_SMS_OTP_INCORRECT | OTP van SMS-bericht onjuist | De gebruiker heeft een onjuiste eenmalige wachtwoord code (OTP) ingevoerd in het SMS-bericht dat ze hebben ontvangen. De verificatie is geweigerd. |
| FAILED_SMS_OTP_PIN_INCORRECT | SMS-bericht OTP + pincode onjuist | De gebruiker heeft een onjuiste eenmalige wachtwoord code (OTP) en/of een onjuiste gebruikers pincode ingevoerd. De verificatie is geweigerd. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Maximum aantal OTP-pogingen voor SMS-bericht overschreden | De gebruiker heeft het maximum aantal OTP-pogingen (one-time wachtwoord code) overschreden. |
| FAILED_PHONE_APP_DENIED | Mobiele app geweigerd | De gebruiker heeft de verificatie in de mobiele app geweigerd door op de knop weigeren te drukken. |
| FAILED_PHONE_APP_INVALID_PIN | Ongeldige pincode voor mobiele app | De gebruiker heeft een ongeldige pincode ingevoerd tijdens de verificatie in de mobiele app. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PINCODE voor mobiele app niet gewijzigd | De gebruiker heeft een vereiste pincode wijziging niet voltooid in de mobiele app. |
| FAILED_FRAUD_REPORTED | Fraude gerapporteerd | De door de gebruiker gemelde fraude in de mobiele app. |
| FAILED_PHONE_APP_NO_RESPONSE | Geen reactie van mobiele app | De gebruiker heeft niet gereageerd op de verificatie aanvraag voor mobiele apps. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Alle apparaten van mobiele app geblokkeerd | De mobiele app-apparaten voor deze gebruiker reageren niet meer op meldingen en zijn geblokkeerd. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Melding van mobiele app mislukt | Er is een fout opgetreden bij het verzenden van een melding naar de mobiele app op het apparaat van de gebruiker. |
| FAILED_PHONE_APP_INVALID_RESULT | Ongeldig resultaat mobiele app | De mobiele app heeft een ongeldig resultaat geretourneerd. |
| FAILED_OATH_CODE_INCORRECT | OATH-code onjuist | De gebruiker heeft een onjuiste OATH-code ingevoerd.  De verificatie is geweigerd. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-code + pincode onjuist | De gebruiker heeft een onjuiste OATH-code en/of een onjuiste gebruikers pincode ingevoerd.  De verificatie is geweigerd. |
| FAILED_OATH_CODE_DUPLICATE | Dubbele OATH-code | De gebruiker heeft een OATH-code ingevoerd die eerder is gebruikt.  De verificatie is geweigerd. |
| FAILED_OATH_CODE_OLD | OATH-code verouderd | De gebruiker heeft een OATH-code ingevoerd die voorafgaat aan een OATH-code die eerder is gebruikt.  De verificatie is geweigerd. |
| FAILED_OATH_TOKEN_TIMEOUT | Time-out voor resultaat van OATH-code | De gebruiker heeft te lang geduurd om de OATH-code in te voeren en er is al een time-out opgetreden voor de Multi-Factor Authentication poging. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Time-out resultaat van beveiligings vragen | De gebruiker heeft te lang geduurd om antwoord te geven op beveiligings vragen en er is al een time-out opgetreden voor de Multi-Factor Authentication poging. |
| FAILED_AUTH_RESULT_TIMEOUT | Time-out van auth-resultaat | De gebruiker heeft te lang geduurd om de Multi-Factor Authentication poging te volt ooien. |
| FAILED_AUTHENTICATION_THROTTLED | Verificatie beperkt | De poging van de Multi-Factor Authentication is beperkt door de service. |

## <a name="next-steps"></a>Volgende stappen

* [SSPR-en MFA-gebruik en inzichten rapportage](howto-authentication-methods-usage-insights.md)
* [Voor gebruikers](../user-help/multi-factor-authentication-end-user.md)
* [Implementatie van](concept-mfa-whichversion.md)
