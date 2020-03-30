---
title: Toegangs- en gebruiksrapporten voor Azure MFA - Azure Active Directory
description: Hierin wordt beschreven hoe u de functie Azure Multi-Factor Authentication - rapporten - gebruikt.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47e6bac031a5f2cffc8734fee976fbf8dadc666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129088"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapporten in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication biedt verschillende rapporten die door u en uw organisatie toegankelijk kunnen worden via de Azure-portal. In de volgende tabel worden de beschikbare rapporten weergegeven:

| Rapport | Locatie | Beschrijving |
|:--- |:--- |:--- |
| Geblokkeerde gebruikersgeschiedenis | Azure AD > Beveiliging > MFA > Gebruikers blokkeren/deblokkeren | Toont de geschiedenis van aanvragen om gebruikers te blokkeren of te deblokkeren. |
| Meldingen over gebruik en fraude | Azure AD-> aanmeldingen | Biedt informatie over het algemene gebruik, het gebruikersoverzicht en de gebruikersgegevens; evenals een geschiedenis van fraudewaarschuwingen die tijdens het opgegeven datumbereik zijn ingediend. |
| Gebruik voor on-premises componenten | Azure AD >-beveiligingsrapport > >-> | Biedt informatie over het algehele gebruik van MFA via de NPS-extensie, ADFS en MFA-server. |
| Gebruikersgeschiedenis omzeild | Azure AD > Beveiliging > MFA > eenmalige bypass | Biedt een geschiedenis van aanvragen om multi-factorauthenticatie voor een gebruiker te omzeilen. |
| Serverstatus | Azure AD >-status > MFA > Server | Hiermee geeft u de status weer van multifactorauthenticatieservers die aan uw account zijn gekoppeld. |

## <a name="view-mfa-reports"></a>MFA-rapporten bekijken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer aan de linkerkant **Azure Active Directory** > **Security** > **MFA**.
3. Selecteer het rapport dat u wilt weergeven.

   ![Statusrapport MFA Server-server in de Azure-portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD-aanmeldingsrapport

Met het **activiteitsrapport aanmeldingen** in de [Azure-portal](https://portal.azure.com)u de informatie krijgen die u nodig hebt om te bepalen hoe het met uw omgeving gaat.

Het aanmeldingsrapport kan u informatie geven over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten voor gebruikers, waaronder informatie over mfa-gebruik (multi-factor authentication). De MFA-gegevens geven u inzicht in hoe MFA werkt in uw organisatie. Hiermee kunt u vragen beantwoorden zoals:

- Is de aanmelding gelukt met MFA?
- Hoe heeft de gebruiker MFA voltooid?
- Waarom kan de gebruiker MFA niet voltooien?
- Hoeveel gebruikers gebruiken MFA?
- Hoeveel gebruikers kunnen MFA niet voltooien?
- Wat zijn de meest voorkomende MFA-problemen waarmee eindgebruikers te maken hebben?

Deze gegevens zijn beschikbaar via de [Azure-portal](https://portal.azure.com) en de [rapportage-API.](../reports-monitoring/concept-reporting-api.md)

![Azure AD-aanmeldingsrapport in de Azure-portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Rapportstructuur aanmeldingen

De rapporten voor aanmeldingsactiviteiten voor MFA bieden u toegang tot de volgende informatie:

**MFA is vereist:** of MFA is vereist voor aanmelding of niet. MFA kan worden vereist vanwege MFA per gebruiker, voorwaardelijke toegang of andere redenen. Mogelijke waarden zijn **Ja** of **Nee.**

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
      - kan telefoonnummer van gebruiker niet bereiken
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

**Voorwaardelijke toegang** Informatie vinden over beleid voor voorwaardelijke toegang dat van invloed was op de aanmeldingspoging, waaronder:

- Beleidsnaam
- Subsidiebesturingselementen
- Sessiebesturingselementen
- Resultaat

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-rapportage over gebruikers die zijn geregistreerd voor MFA

Zorg er eerst voor dat u de [MSOnline V1 PowerShell-module hebt](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) geïnstalleerd.

Identificeer gebruikers die zich hebben geregistreerd voor MFA met behulp van de PowerShell die volgt. Met deze set opdrachten worden uitgeschakelde gebruikers uitgesloten, omdat deze accounts niet kunnen worden geverifieerd tegen Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identificeer gebruikers die zich niet hebben geregistreerd voor MFA met behulp van de PowerShell die volgt. Met deze set opdrachten worden uitgeschakelde gebruikers uitgesloten, omdat deze accounts niet kunnen worden geverifieerd tegen Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identificeer gebruikers en geregistreerde uitvoermethoden. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Mogelijke resultaten in activiteitenrapporten

De volgende tabel kan worden gebruikt om multi-factor authenticatie op te lossen met behulp van de gedownloade versie van het multi-factor authenticatie activiteitsrapport. Ze worden niet rechtstreeks in de Azure-portal weergegeven.

| Gespreksresultaat | Beschrijving | Brede beschrijving |
| --- | --- | --- |
| SUCCESS_WITH_PIN | INGEVOERDE pincode | De gebruiker heeft een pincode ingevoerd. Als de verificatie is geslaagd, hebben ze de juiste pincode ingevoerd. Als verificatie wordt geweigerd, hebben ze een onjuiste pincode ingevoerd of is de gebruiker ingesteld op de standaardmodus. |
| SUCCESS_NO_PIN | Alleen # ingevoerd | Als de gebruiker is ingesteld op de PIN-modus en de verificatie wordt geweigerd, betekent dit dat de gebruiker zijn pincode niet heeft ingevoerd en alleen #heeft ingevoerd.  Als de gebruiker is ingesteld op de standaardmodus en de verificatie slaagt, betekent dit dat de gebruiker alleen # heeft ingevoerd, wat het juiste is om te doen in de standaardmodus. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Niet ingedrukt na binnenkomst | De gebruiker heeft geen DTMF-cijfers verzonden omdat # niet is ingevoerd. Andere ingevoerde cijfers worden niet verzonden, tenzij # wordt ingevoerd met vermelding van de voltooiing van de vermelding. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Geen telefooninvoer - Time-out | De oproep werd beantwoord, maar er was geen antwoord. Dit geeft meestal aan dat het gesprek is opgenomen via voicemail. |
| SUCCESS_PIN_EXPIRED | Pincode verlopen en niet gewijzigd | De pincode van de gebruiker is verlopen en deze is gevraagd deze te wijzigen, maar de pincodewijziging is niet voltooid. |
| SUCCESS_USED_CACHE | Gebruikte cache | Verificatie is geslaagd zonder een multi-factor verificatiegesprek, omdat een eerdere succesvolle verificatie voor dezelfde gebruikersnaam plaatsvond binnen de geconfigureerde cacheperiode. |
| SUCCESS_BYPASSED_AUTH | Omzeild Auth | Verificatie is geslaagd met behulp van een eenmalige bypass die voor de gebruiker is gestart. Zie het rapport Omzeild gebruikersgeschiedenis voor meer informatie over de bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Gebruikte IP-gebaseerde cache | Verificatie is geslaagd zonder een multi-factor verificatiegesprek, omdat een eerdere succesvolle verificatie voor dezelfde gebruikersnaam, verificatietype, toepassingsnaam en IP plaatsvond binnen de geconfigureerde cacheperiode. |
| SUCCESS_USED_APP_BASED_CACHE | Gebruikte cache op basis van apps | Verificatie is zonder multifactorverificatie-aanroep geslaagd sinds een eerdere succesvolle verificatie voor dezelfde gebruikersnaam, verificatietype en toepassingsnaam binnen de geconfigureerde cacheperiode. |
| SUCCESS_INVALID_INPUT | Ongeldige telefooninvoer | Het antwoord dat vanaf de telefoon wordt verzonden, is niet geldig. Dit kan afkomstig zijn van een faxapparaat of modem of de gebruiker kan * als onderdeel van zijn pincode hebben ingevoerd. |
| SUCCESS_USER_BLOCKED | Gebruiker is geblokkeerd | Het telefoonnummer van de gebruiker is geblokkeerd. Een geblokkeerd nummer kan door de gebruiker worden gestart tijdens een verificatiegesprek of door een beheerder die de Azure-portal gebruikt. <br> OPMERKING: Een geblokkeerd nummer is ook een bijproduct van een fraudewaarschuwing. |
| SUCCESS_SMS_AUTHENTICATED | Sms-bericht geverifieerd | Voor een testbericht in twee richtingen antwoordde de gebruiker correct met zijn eenmalige toegangscode (OTP) of OTP + PINCODE. |
| SUCCESS_SMS_SENT | Verzonden sms-bericht | Voor sms'jes is het sms-bericht met de eenmalige toegangscode (OTP) verzonden. De gebruiker voert de OTP of OTP + PIN in de toepassing in om de verificatie te voltooien. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobiele app geverifieerd | De gebruiker is geverifieerd via de mobiele app. |
| SUCCESS_OATH_CODE_PENDING | OATH-code in behandeling | De gebruiker werd gevraagd om hun OATH-code, maar reageerde niet. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-code geverifieerd | De gebruiker heeft een geldige OATH-code ingevoerd wanneer daarom wordt gevraagd. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Fallback OATH Code geverifieerd | De gebruiker werd geweigerd authenticatie met behulp van hun primaire Multi-Factor Authenticatie methode en vervolgens een geldige OATH-code voor terugval. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Fallback Security Vragen beantwoord | De gebruiker werd geweigerd authenticatie met behulp van hun primaire Multi-Factor Authenticatie methode en vervolgens beantwoord de beveiligingsvragen correct voor fallback. |
| FAILED_PHONE_BUSY | Auth al in uitvoering | Multi-Factor Authentication is al bezig met het verwerken van een verificatie voor deze gebruiker. Dit wordt vaak veroorzaakt door RADIUS-clients die meerdere verificatieverzoeken verzenden tijdens dezelfde aanmelding. |
| CONFIG_ISSUE | Telefoon onbereikbaar | Oproep werd geprobeerd, maar ofwel kon niet worden geplaatst of werd niet beantwoord. Dit omvat bezet signaal, snel bezet signaal (losgekoppeld), tri-tonen (nummer niet meer in dienst), time-out tijdens het rinkelen, enz. |
| FAILED_INVALID_PHONENUMBER | Ongeldige notatie van telefoonnummers | Het telefoonnummer heeft een ongeldige indeling. Telefoonnummers moeten numeriek zijn en moeten 10 cijfers zijn voor landcode +1 (Verenigde Staten & Canada). |
| FAILED_USER_HUNGUP_ON_US | Gebruiker hing de telefoon op | De gebruiker nam de telefoon op, maar hing vervolgens op zonder op knoppen te drukken. |
| FAILED_INVALID_EXTENSION | Ongeldige extensie | De extensie bevat ongeldige tekens. Alleen cijfers, komma's, *en # zijn toegestaan. Een @ voorvoegsel kan ook worden gebruikt. |
| FAILED_FRAUD_CODE_ENTERED | Fraudecode ingevoerd | De gebruiker heeft ervoor gekozen om fraude te melden tijdens het gesprek wat resulteert in een geweigerde verificatie en een geblokkeerd telefoonnummer.| 
| FAILED_SERVER_ERROR | Kan geen oproep plaatsen | De multi-factor verificatieservice kan de oproep niet plaatsen. |
| FAILED_SMS_NOT_SENT | Sms kan niet worden verzonden | Het sms-bericht kan niet worden verzonden. De verificatie wordt geweigerd. |
| FAILED_SMS_OTP_INCORRECT | Sms OTP onjuist | De gebruiker heeft een onjuiste eenmalige toegangscode (OTP) ingevoerd uit het sms-bericht dat hij heeft ontvangen. De verificatie wordt geweigerd. |
| FAILED_SMS_OTP_PIN_INCORRECT | Sms OTP + PINCODE onjuist | De gebruiker heeft een onjuiste one-time toegangscode (OTP) en/of een onjuiste gebruikerspincode ingevoerd. De verificatie wordt geweigerd. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Maximaal sms-pogingen voor sms-berichten overschreden | De gebruiker heeft het maximum aantal one-time passcode (OTP) pogingen overschreden. |
| FAILED_PHONE_APP_DENIED | Mobiele app geweigerd | De gebruiker ontkende de verificatie in de mobiele app door op de knop Weigeren te drukken. |
| FAILED_PHONE_APP_INVALID_PIN | Ongeldige pincode voor mobiele apps | De gebruiker heeft een ongeldige pincode ingevoerd bij het verifiëren in de mobiele app. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Pincode voor mobiele app niet gewijzigd | De gebruiker heeft een vereiste pincodewijziging in de mobiele app niet voltooid. |
| FAILED_FRAUD_REPORTED | Fraude gemeld | De gebruiker meldde fraude in de mobiele app. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobiele app geen reactie | De gebruiker heeft niet gereageerd op het verificatieverzoek voor mobiele apps. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobiele app Alle apparaten geblokkeerd | De mobiele app-apparaten voor deze gebruiker reageren niet meer op meldingen en zijn geblokkeerd. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Melding mobiele app is mislukt | Er is een fout opgetreden bij het verzenden van een melding naar de mobiele app op het apparaat van de gebruiker. |
| FAILED_PHONE_APP_INVALID_RESULT | Ongeldig resultaat mobiele app | De mobiele app heeft een ongeldig resultaat geretourneerd. |
| FAILED_OATH_CODE_INCORRECT | OATH-code onjuist | De gebruiker heeft een onjuiste OATH-code ingevoerd.  De verificatie wordt geweigerd. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-code + pincode onjuist | De gebruiker heeft een onjuiste OATH-code en/of een onjuiste gebruikerspincode ingevoerd.  De verificatie wordt geweigerd. |
| FAILED_OATH_CODE_DUPLICATE | SEED-code dupliceren | De gebruiker heeft een OATH-code ingevoerd die eerder is gebruikt.  De verificatie wordt geweigerd. |
| FAILED_OATH_CODE_OLD | OATH-code verouderd | De gebruiker heeft een OATH-code ingevoerd die voorafgaat aan een OATH-code die eerder is gebruikt.  De verificatie wordt geweigerd. |
| FAILED_OATH_TOKEN_TIMEOUT | Time-out van de resultaattijd van OATH-code | De gebruiker duurde te lang om de OATH-code in te voeren en de poging voor meervoudige verificatie had al een time-out. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Time-out van resultaatresultaten van beveiligingsvragen | De gebruiker duurde te lang om antwoord op beveiligingsvragen in te voeren en de multi-factor authenticatie poging had al een time-out. |
| FAILED_AUTH_RESULT_TIMEOUT | Auth-resultaattime-out | De gebruiker heeft er te lang over gedaan om de multifactorauthenticatie-poging te voltooien. |
| FAILED_AUTHENTICATION_THROTTLED | Verificatie beperkt | De poging voor meervoudige verificatie werd door de service beperkt. |

## <a name="next-steps"></a>Volgende stappen

* [SSPR- en MFA-gebruik en inzichtenrapportage](howto-authentication-methods-usage-insights.md)
* [Voor gebruikers](../user-help/multi-factor-authentication-end-user.md)
* [Waar te implementeren](concept-mfa-whichversion.md)
