---
title: Selfservice voor wachtwoord herstel diep gaande-Azure Active Directory
description: Hoe werkt self-service voor het opnieuw instellen van wacht woorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e88669b5453069d9f6bb64e803adc65baf8afc6b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420679"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Hoe werkt het? self-service voor wachtwoord herstel van Azure AD

Hoe werkt self-service voor wachtwoord herstel (SSPR)? Wat betekent die optie in de interface? Ga verder met lezen om meer te weten te komen over Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Hoe werkt de portal voor het opnieuw instellen van wacht woorden?

Wanneer een gebruiker naar de portal voor het opnieuw instellen van wacht woorden gaat, wordt een werk stroom gestart om het volgende te bepalen:

   * Hoe moet de pagina worden gelokaliseerd?
   * Is het gebruikers account geldig?
   * Tot welke organisatie behoort de gebruiker?
   * Waar wordt het wacht woord van de gebruiker beheerd?
   * Is de gebruiker in licentie gegeven voor het gebruik van de functie?

Lees de volgende stappen voor meer informatie over de logica achter de pagina voor het opnieuw instellen van het wacht woord:

1. De gebruiker selecteert de koppeling **geen toegang tot uw account** of gaat rechtstreeks naar [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Op basis van de land instelling van de browser wordt de ervaring weer gegeven in de juiste taal. De ervaring voor het opnieuw instellen van wacht woorden is gelokaliseerd in dezelfde talen die door Office 365 worden ondersteund.
   * Als u de portal voor het opnieuw instellen van wacht woorden wilt weer geven in een andere gelokaliseerde taal, voegt u '? MKT = ' toe aan het einde van de URL voor het opnieuw instellen van het wacht woord met het voor beeld dat volgt op het [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)van het
2. De gebruiker voert een gebruikers-ID in en geeft een CAPTCHA door.
3. Azure AD controleert of de gebruiker deze functie kan gebruiken door de volgende controles uit te voeren:
   * Hiermee wordt gecontroleerd of de gebruiker deze functie heeft ingeschakeld en een Azure AD-licentie is toegewezen.
     * Als deze functie niet is ingeschakeld of als er een licentie aan de gebruiker is toegewezen, wordt de gebruiker gevraagd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
   * Controleert of de gebruiker beschikt over de juiste verificatie methoden die zijn gedefinieerd op hun account in overeenstemming met het beheerders beleid.
     * Als voor het beleid slechts één methode is vereist, zorgt u ervoor dat de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste een van de verificatie methoden die zijn ingeschakeld door het beheerders beleid.
       * Als de verificatie methoden niet zijn geconfigureerd, wordt de gebruiker geadviseerd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
     * Als het beleid twee methoden vereist, zorgt u ervoor dat de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste twee van de verificatie methoden die zijn ingeschakeld door het beheerders beleid.
       * Als de verificatie methoden niet zijn geconfigureerd, wordt de gebruiker geadviseerd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
     * Als een rol van Azure-beheerder aan de gebruiker is toegewezen, wordt het sterke wachtwoord beleid voor twee poorten afgedwongen. Meer informatie over dit beleid vindt u in de sectie voor het [opnieuw instellen van beleids verschillen](concept-sspr-policy.md#administrator-reset-policy-differences)van de beheerder.
   * Hiermee wordt gecontroleerd of het wacht woord van de gebruiker on-premises (federatieve, Pass-Through-verificatie of wacht woord-hash gesynchroniseerd) wordt beheerd.
     * Als write-back wordt geïmplementeerd en het wacht woord van de gebruiker on-premises wordt beheerd, mag de gebruiker door gaan met het verifiëren en opnieuw instellen van hun wacht woord.
     * Als write-back niet wordt geïmplementeerd en het wacht woord van de gebruiker on-premises wordt beheerd, wordt de gebruiker gevraagd contact op te nemen met de beheerder om hun wacht woord opnieuw in te stellen.
4. Als wordt vastgesteld dat de gebruiker het wacht woord opnieuw kan instellen, wordt de gebruiker begeleid door het reset proces.

## <a name="authentication-methods"></a>Verificatiemethoden

Als SSPR is ingeschakeld, moet u ten minste één van de volgende opties voor de verificatie methoden selecteren. Soms hoort u deze opties als ' Gates ' genoemd. We raden u ten zeerste aan **twee of meer authenticatie methoden te kiezen** zodat uw gebruikers meer flexibiliteit hebben wanneer ze er geen toegang toe hebben. Meer informatie over de hieronder vermelde methoden vindt u in het artikel [Wat zijn verificatie methoden?](concept-authentication-methods.md).

* Meldingen via mobiele app
* Mobiele app-code
* Email
* Mobiele telefoon
* Office Phone
* Beveiligingsvragen

Gebruikers kunnen hun wacht woord alleen opnieuw instellen als ze gegevens bevatten in de verificatie methoden die de beheerder heeft ingeschakeld.

> [!IMPORTANT]
> Vanaf maart 2019 zijn de opties voor telefoon gesprekken niet beschikbaar voor MFA-en SSPR-gebruikers in gratis/proef versie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. De telefoon oproep blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging is alleen van invloed op de Azure AD-tenants gratis en proef versie.

> [!WARNING]
> Accounts waaraan Azure-beheerders rollen zijn toegewezen, moeten gebruikmaken van methoden zoals gedefinieerd in de sectie [beleids verschillen van de beheerder opnieuw instellen](concept-sspr-policy.md#administrator-reset-policy-differences).

![De selectie van verificatie methoden in de Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Aantal vereiste verificatie methoden

Met deze optie bepaalt u het minimum aantal beschik bare verificatie methoden of poorten dat een gebruiker moet door lopen om het wacht woord opnieuw in te stellen of te ontgrendelen. De waarde kan worden ingesteld op een of twee.

Gebruikers kunnen ervoor kiezen om meer verificatie methoden op te geven als de beheerder die verificatie methode inschakelt.

Als een gebruiker niet de mini maal vereiste methoden heeft geregistreerd, wordt er een fout pagina weer geven die hen omleidt om een beheerder te vragen of hun wacht woord opnieuw is ingesteld.

#### <a name="mobile-app-and-sspr"></a>Mobiele app en SSPR

Wanneer u een mobiele app gebruikt, zoals de app Microsoft Authenticator, als methode voor het opnieuw instellen van wacht woorden, moet u rekening houden met het volgende voor behoud:

* Wanneer beheerders een andere methode moeten gebruiken om een wacht woord opnieuw in te stellen, is verificatie code de enige optie die beschikbaar is.
* Wanneer beheerders twee methoden moeten gebruiken om een wacht woord opnieuw in te stellen, kunnen gebruikers naast andere ingeschakelde methoden **ofwel** een melding **of** verificatie code gebruiken.

| Aantal methoden dat is vereist om opnieuw in te stellen | Een' | Twee |
| :---: | :---: | :---: |
| Functies voor mobiele apps beschikbaar | Code | Code of melding |

Gebruikers hebben geen optie om hun mobiele app te registreren wanneer ze zich registreren voor selfservice voor wachtwoord herstel van [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Gebruikers kunnen hun mobiele app registreren bij [https://aka.ms/mfasetup](https://aka.ms/mfasetup)of in de nieuwe preview-versie voor de registratie van beveiligings gegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> U moet de [geconvergeerde registratie voor selfservice voor wachtwoord herstel en Azure multi-factor Authentication (open bare preview)](concept-registration-mfa-sspr-converged.md) inschakelen voordat gebruikers toegang kunnen krijgen tot de nieuwe ervaring op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> De verificator-app kan niet worden geselecteerd als de enige verificatie methode bij het configureren van een 1-poortbeleid. Op dezelfde manier kan de verificator-app en slechts één extra methode worden geselecteerd bij het configureren van een beleid met 2 poorten.
> Bij het configureren van SSPR-beleid dat de verificator-app als methode bevat, moet er ten minste een extra methode worden geselecteerd bij het configureren van een 1-poort beleid, en moeten er ten minste twee extra methoden worden geselecteerd bij het configureren van een beleid met 2 poorten.
> De reden hiervoor is dat de huidige registratie-ervaring voor SSPR niet de optie bevat voor het registreren van de verificator-app. De optie voor het registreren van de verificator-app is opgenomen in de nieuwe [geconvergeerde registratie voor selfservice voor het opnieuw instellen van wacht woorden en Azure multi-factor Authentication (open bare preview)](concept-registration-mfa-sspr-converged.md).
> Het toestaan van beleids regels die alleen de verificator-app (voor 1-poort beleid) of de verificator-app en slechts één extra methode (voor 2-Gates beleid) gebruiken, kunnen ertoe leiden dat gebruikers worden geblokkeerd voor het registreren van SSPR totdat ze zijn geconfigureerd voor het gebruik van de nieuwe registratie-ervaring.

### <a name="change-authentication-methods"></a>Verificatie methoden wijzigen

Wat gebeurt er als u begint met een beleid dat slechts één vereiste authenticatie methode heeft voor opnieuw instellen of ontgrendelen en u wijzigt dat op twee manieren?

| Aantal geregistreerde methoden | Aantal methoden dat is vereist | Resultaat |
| :---: | :---: | :---: |
| 1 of meer | 1 | **Kan** opnieuw instellen of ontgrendelen |
| 1 | 2 | **Kan niet** opnieuw instellen of ontgrendelen |
| 2 of meer | 2 | **Kan** opnieuw instellen of ontgrendelen |

Als u de typen verificatie methoden wijzigt die een gebruiker kan gebruiken, kunt u per ongeluk voor komen dat gebruikers SSPR kunnen gebruiken als ze niet beschikken over de minimale hoeveelheid gegevens die beschikbaar zijn.

Voorbeeld:
1. Het oorspronkelijke beleid is geconfigureerd met twee vereiste verificatie methoden. Alleen het zakelijke telefoon nummer en de beveiligings vragen worden gebruikt. 
2. De beheerder wijzigt het beleid zodat er geen beveiligings vragen meer worden gebruikt, maar maakt het gebruik van een mobiele telefoon en een alternatief e-mail adres mogelijk.
3. Gebruikers zonder de velden mobiel telefoon of alternatief e-mail adres kunnen hun wacht woord niet opnieuw instellen.

## <a name="registration"></a>Registratie

### <a name="require-users-to-register-when-they-sign-in"></a>Vereisen dat gebruikers zich registreren wanneer ze zich aanmelden

Als u deze optie inschakelt, moet de gebruiker de registratie van het wacht woord opnieuw instellen volt ooien als deze zich aanmeldt bij alle toepassingen die gebruikmaken van Azure AD. Deze werk stroom omvat de volgende toepassingen:

* Office 365
* Azure-portal
* Toegangsvenster
* Federatieve toepassingen
* Aangepaste toepassingen met Azure AD

Wanneer registratie is uitgeschakeld, kunnen gebruikers zich hand matig registreren. Ze kunnen [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) bekijken of de koppeling **registreren voor wacht woord opnieuw instellen** selecteren op het tabblad **profiel** in het toegangs venster.

> [!NOTE]
> Gebruikers kunnen de registratie portal voor het opnieuw instellen van het wacht woord sluiten door **Annuleren** te selecteren of door het venster te sluiten. Maar ze moeten zich registreren elke keer dat ze zich aanmelden totdat ze hun registratie hebben voltooid.
>
> Met deze interrupt wordt de verbinding van de gebruiker niet verbroken als deze al zijn aangemeld.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Het aantal dagen instellen waarna gebruikers wordt gevraagd om de verificatie gegevens opnieuw te bevestigen

Met deze optie bepaalt u de tijds periode tussen het instellen en opnieuw bevestigen van verificatie gegevens en is alleen beschikbaar als u de optie **vereisen dat gebruikers zich registreren wanneer ze zich aanmelden** inschakelen.

Geldige waarden zijn 0 tot 730 dagen, met ' 0 ', wat betekent dat gebruikers nooit worden gevraagd om hun verificatie gegevens opnieuw te bevestigen.

## <a name="notifications"></a>Meldingen

### <a name="notify-users-on-password-resets"></a>Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord

Als deze optie is ingesteld op **Ja**, moeten gebruikers hun wacht woord opnieuw instellen een e-mail bericht ontvangen dat het wacht woord is gewijzigd. Het e-mail bericht wordt via de SSPR-portal verzonden naar de primaire en alternatieve e-mail adressen die zich in azure AD bevinden. Niemand anders wordt op de hoogte gesteld van de gebeurtenis opnieuw instellen.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Alle beheerders waarschuwen wanneer andere beheerders hun wacht woord opnieuw instellen

Als deze optie is ingesteld op **Ja**, worden *alle beheerders* een e-mail ontvangen op het primaire e-mail adres van het bestand in azure AD. Het e-mail bericht geeft aan dat een andere beheerder zijn of haar wacht woord heeft gewijzigd met behulp van SSPR.

Voor beeld: er zijn vier beheerders in een omgeving. Beheerder een wacht woord opnieuw instellen met behulp van SSPR. Beheerders B, C en D ontvangen een e-mail bericht met een waarschuwing voor het opnieuw instellen van het wacht woord.

## <a name="on-premises-integration"></a>On-premises integratie

Als u Azure AD Connect installeert, configureert en inschakelt, hebt u de volgende aanvullende opties voor on-premises integraties. Als deze opties grijs worden weer gegeven, is terugschrijven niet goed geconfigureerd. Zie [Configuring Password writeing](howto-sspr-writeback.md)(Engelstalig) voor meer informatie.

![Valideren van wacht woord terugschrijven is ingeschakeld en werkt][Writeback]

Op deze pagina vindt u een snelle status van de on-premises terugschrijf client, een van de volgende berichten wordt weer gegeven op basis van de huidige configuratie:

* Uw on-premises terugschrijf client is actief.
* Azure AD is online en is verbonden met uw on-premises terugschrijf client. Het lijkt echter alsof de geïnstalleerde versie van Azure AD Connect verouderd is. Overweeg het [upgraden van Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) om ervoor te zorgen dat u beschikt over de nieuwste connectiviteits functies en belang rijke problemen met oplossingen.
* Helaas kan de status van uw on-premises terugschrijf client niet worden gecontroleerd, omdat de geïnstalleerde versie van Azure AD Connect verouderd is. [Upgrade Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) zodat u de verbindings status kunt controleren.
* Helaas lijkt het alsof er geen verbinding kan worden gemaakt met uw on-premises back-upclient. [Problemen met Azure AD Connect oplossen](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) om de verbinding te herstellen.
* Er kan geen verbinding worden gemaakt met uw on-premises terugschrijf client omdat het terugschrijven van wacht woorden niet correct is geconfigureerd. [Configureer write-back van wacht woord](howto-sspr-writeback.md) om de verbinding te herstellen.
* Helaas lijkt het alsof er geen verbinding kan worden gemaakt met uw on-premises back-upclient. Dit kan worden veroorzaakt door tijdelijke problemen aan onze kant. Als het probleem zich blijft voordoen, [lost u Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) op om de verbinding te herstellen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Wacht woorden terugschrijven naar uw on-premises Directory

Dit besturings element bepaalt of het terugschrijven van wacht woorden is ingeschakeld voor deze map. Als write-back is ingeschakeld, wordt de status van de on-premises write-service weer gegeven. Dit besturings element is handig als u het terugschrijven van wacht woorden tijdelijk wilt uitschakelen zonder Azure AD Connect opnieuw te moeten configureren.

* Als de schakel optie is ingesteld op **Ja**, dan write-back is ingeschakeld, en federatieve, Pass-Through-verificatie of met wacht woord-hash gesynchroniseerde gebruikers kunnen hun wacht woord opnieuw instellen.
* Als de schakel optie is ingesteld op **Nee**, dan write-back is uitgeschakeld en federatieve, Pass-Through-verificatie of met wacht woord-hash gesynchroniseerde gebruikers kunnen hun wacht woorden niet opnieuw instellen.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Gebruikers toestaan om accounts te ontgrendelen zonder hun wacht woord opnieuw in te stellen

Dit besturings element bepaalt of gebruikers die de portal voor het opnieuw instellen van wacht woorden bezoeken, de optie moeten krijgen om hun on-premises Active Directory accounts te ontgrendelen zonder hun wacht woord opnieuw in te stellen. Standaard worden accounts door Azure AD ontgrendeld wanneer het wacht woord opnieuw wordt ingesteld. U gebruikt deze instelling om deze twee bewerkingen van elkaar te scheiden.

* Als deze optie is ingesteld op **Ja**, krijgen gebruikers de mogelijkheid om hun wacht woord opnieuw in te stellen en het account te ontgrendelen, of om hun account te ontgrendelen zonder het wacht woord opnieuw in te stellen.
* Als deze instelling is ingesteld op **Nee**, kunnen gebruikers alleen een gecombineerde wachtwoord herstel-en account vergrendelings bewerking uitvoeren.

### <a name="on-premises-active-directory-password-filters"></a>On-premises Active Directory wachtwoord filters

Azure AD self-service voor wachtwoord herstel voert het equivalent van een door de beheerder geïnitieerd wacht woord opnieuw instellen in Active Directory. Als u een wachtwoord filter van een derde partij gebruikt om aangepaste wachtwoord regels af te dwingen en u wilt dat dit wachtwoord filter wordt gecontroleerd tijdens de selfservice voor wachtwoord herstel van Azure AD, moet u ervoor zorgen dat de oplossing voor wachtwoord filter van derden is geconfigureerd om toe te passen in de het scenario voor het opnieuw instellen van het beheerders wachtwoord. [Azure AD-wachtwoord beveiliging voor Windows Server Active Directory](concept-password-ban-bad-on-premises.md) wordt standaard ondersteund.

## <a name="password-reset-for-b2b-users"></a>Wacht woord opnieuw instellen voor B2B-gebruikers

Wacht woord opnieuw instellen en wijzigen worden volledig ondersteund voor alle Business-to-Business (B2B)-configuraties. Het opnieuw instellen van B2B-gebruikers wachtwoorden wordt in de volgende drie gevallen ondersteund:

* **Gebruikers van een partner organisatie met een bestaande Azure AD-Tenant**: als de organisatie waarmee u een partner bent, een bestaande Azure AD-Tenant heeft, respecteren we dat het beleid voor het *opnieuw instellen van het wacht woord is ingeschakeld voor die Tenant*. Om het wacht woord opnieuw in te stellen, moet de partner organisatie alleen ervoor zorgen dat Azure AD SSPR is ingeschakeld. Er worden geen extra kosten in rekening gebracht voor Office 365-klanten en dit kan worden ingeschakeld door de stappen in de hand leiding aan de slag [met wachtwoord beheer](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) te volgen.
* **Gebruikers die zich aanmelden via** Self-Service-aanmelding: als de organisatie waarmee u bent gelieerd, gebruikmaakt van de [self-service registratie](../users-groups-roles/directory-self-service-signup.md) functie om een Tenant op te halen, kunnen we het wacht woord opnieuw instellen met het e-mail bericht dat ze hebben geregistreerd.
* **B2B-gebruikers**: alle nieuwe B2B-gebruikers die zijn gemaakt met de nieuwe [mogelijkheden van Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) , kunnen hun wacht woord ook opnieuw instellen met het e-mail adres dat is geregistreerd tijdens het INVITE-proces.

Als u dit scenario wilt testen, gaat u naar https://passwordreset.microsoftonline.com met een van deze partner-gebruikers. Als er een alternatieve e-mail of verificatie-e-mail is gedefinieerd, werkt wacht woord opnieuw instellen zoals verwacht.

> [!NOTE]
> Micro soft-accounts waaraan gast toegang is verleend voor uw Azure AD-Tenant, zoals die van Hotmail.com, Outlook.com of andere persoonlijke e-mail adressen, kunnen Azure AD SSPR niet gebruiken. Ze moeten hun wacht woord opnieuw instellen met behulp van de informatie in de [Als u zich niet kunt aanmelden bij uw Microsoft-account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) -artikel.

## <a name="next-steps"></a>Volgende stappen

De volgende koppelingen bieden aanvullende informatie over wachtwoordherstel met behulp van Azure AD:

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor wachtwoordherstel](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets is verbroken. Hoe kan ik problemen met SSPR oplossen?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure Active Directory-verificatiemethoden die beschikbaar zijn en hoeveel er vereist zijn"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Configuratie van on-premises integratie wacht woord terugschrijven en informatie over probleem oplossing"
