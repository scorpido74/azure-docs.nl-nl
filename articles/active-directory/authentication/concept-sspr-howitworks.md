---
title: Selfservice wachtwoord reset deep dive - Azure Active Directory
description: Hoe werkt het opnieuw instellen van selfservicewachtwoorden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848575"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Hoe het werkt: Azure AD self-service wachtwoord resetten

Hoe werkt self-service password reset (SSPR) ? Wat betekent die optie in de interface? Lees verder voor meer informatie over Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Hoe werkt de wachtwoordresetportal?

Wanneer een gebruiker naar de portal voor het opnieuw instellen van wachtwoorden gaat, wordt een werkstroom afgetrapt om te bepalen:

   * Hoe moet de pagina gelokaliseerd worden?
   * Is het gebruikersaccount geldig?
   * Tot welke organisatie behoort de gebruiker?
   * Waar wordt het wachtwoord van de gebruiker beheerd?
   * Heeft de gebruiker een licentie om de functie te gebruiken?

Lees de volgende stappen voor meer informatie over de logica achter de pagina voor het opnieuw instellen van wachtwoorden:

1. De gebruiker selecteert de **link Kan geen toegang krijgen tot uw account** of gaat rechtstreeks naar [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Op basis van de browserlocale wordt de ervaring in de juiste taal weergegeven. De wachtwoordreset-ervaring is gelokaliseerd in dezelfde talen als die door Office 365 worden ondersteund.
   * Als u de wachtwoordresetportal in een andere gelokaliseerde taal wilt weergeven, wordt "?mkt=" toegevoegd [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)aan het einde van de URL voor het opnieuw instellen van het wachtwoord met het voorbeeld dat volgt op het lokaliseren naar het Spaans .
2. De gebruiker voert een gebruikersnaam in en geeft een captcha door.
3. Azure AD controleert of de gebruiker deze functie kan gebruiken door de volgende controles uit te voeren:
   * Hiermee controleert u of de gebruiker deze functie heeft ingeschakeld en dat een Azure AD-licentie is toegewezen.
     * Als de gebruiker deze functie niet heeft ingeschakeld of een licentie heeft toegewezen, wordt de gebruiker gevraagd contact op te nemen met de beheerder om zijn wachtwoord opnieuw in te stellen.
   * Hiermee controleert u of de gebruiker de juiste verificatiemethoden heeft gedefinieerd in zijn account in overeenstemming met het beheerdersbeleid.
     * Als het beleid slechts één methode vereist, zorgt het ervoor dat de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste één van de verificatiemethoden die door het beheerdersbeleid zijn ingeschakeld.
       * Als de verificatiemethoden niet zijn geconfigureerd, wordt de gebruiker geadviseerd contact op te nemen met de beheerder om zijn wachtwoord opnieuw in te stellen.
     * Als het beleid twee methoden vereist, zorgt het ervoor dat de gebruiker de juiste gegevens heeft gedefinieerd voor ten minste twee van de verificatiemethoden die zijn ingeschakeld door het beheerdersbeleid.
       * Als de verificatiemethoden niet zijn geconfigureerd, wordt de gebruiker geadviseerd contact op te nemen met de beheerder om zijn wachtwoord opnieuw in te stellen.
     * Als een Azure-beheerdersrol aan de gebruiker is toegewezen, wordt het sterke wachtwoordbeleid met twee poortjes afgedwongen. Meer informatie over dit beleid vindt u in de sectie [Beheerdersresetbeleidsverschillen](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Controleert of het wachtwoord van de gebruiker on-premises wordt beheerd (federatieve, doorgeefverificatie of wachtwoordhash gesynchroniseerd).
     * Als writeback is geïmplementeerd en het wachtwoord van de gebruiker on-premises wordt beheerd, mag de gebruiker doorgaan met het verifiëren en opnieuw instellen van zijn wachtwoord.
     * Als writeback niet wordt geïmplementeerd en het wachtwoord van de gebruiker on-premises wordt beheerd, wordt de gebruiker gevraagd contact op te nemen met de beheerder om zijn wachtwoord opnieuw in te stellen.
4. Als wordt vastgesteld dat de gebruiker zijn wachtwoord opnieuw kan instellen, wordt de gebruiker door het resetproces geleid.

## <a name="authentication-methods"></a>Verificatiemethoden

Als SSPR is ingeschakeld, moet u ten minste één van de volgende opties voor de verificatiemethoden selecteren. Soms hoor je deze opties aangeduid als "poorten." We raden u ten zeerste aan **twee of meer verificatiemethoden te kiezen,** zodat uw gebruikers meer flexibiliteit hebben voor het geval ze geen toegang hebben tot een verificatiemethode wanneer ze die nodig hebben. Meer informatie over de onderstaande methoden vindt u in het artikel [Wat zijn authenticatiemethoden?](concept-authentication-methods.md)

* Meldingen via mobiele app
* Code van mobiele app
* Email
* Mobiele telefoon
* Zakelijke telefoon
* Beveiligingsvragen

Gebruikers kunnen hun wachtwoord alleen opnieuw instellen als ze gegevens bevatten in de verificatiemethoden die de beheerder heeft ingeschakeld.

> [!IMPORTANT]
> Vanaf maart 2019 zijn de telefoongespreksopties niet beschikbaar voor MFA- en SSPR-gebruikers in gratis/proefversie van Azure AD-tenants. SMS-berichten worden niet beïnvloed door deze wijziging. Telefoongesprek blijft beschikbaar voor gebruikers in betaalde Azure AD-tenants. Deze wijziging heeft alleen gevolgen voor gratis/trial Azure AD-tenants.

> [!WARNING]
> Accounts die Azure Administrator-rollen toegewezen hebben, zijn vereist om methoden te gebruiken zoals gedefinieerd in de [beleidsverschillen van](concept-sspr-policy.md#administrator-reset-policy-differences)de sectie Administrator reset .

![Verificatiemethoden selecteren in de Azure-portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Aantal vereiste verificatiemethoden

Deze optie bepaalt het minimumaantal beschikbare verificatiemethoden of poorten dat een gebruiker moet doorlopen om zijn wachtwoord opnieuw in te stellen of te ontgrendelen. Het kan worden ingesteld op een of twee.

Gebruikers kunnen ervoor kiezen om meer verificatiemethoden te leveren als de beheerder die verificatiemethode inschakelt.

Als een gebruiker niet de minimaal vereiste methoden heeft geregistreerd, ziet hij of zij een foutpagina die hem opdraagt een beheerder te verzoeken zijn wachtwoord opnieuw in te stellen.

#### <a name="mobile-app-and-sspr"></a>Mobiele app en SSPR

Wanneer u een mobiele app, zoals de Microsoft Authenticator-app, gebruikt als methode voor het opnieuw instellen van wachtwoorden, moet u zich bewust zijn van de volgende kanttekeningen:

* Wanneer beheerders één methode nodig hebben om een wachtwoord opnieuw in te stellen, is verificatiecode de enige beschikbare optie.
* Wanneer beheerders twee methoden nodig hebben om een wachtwoord opnieuw in te stellen, kunnen gebruikers naast andere ingeschakelde methoden **ook** een melding **of** verificatiecode gebruiken.

| Aantal methoden dat nodig is om opnieuw te resetten | Eén | Twee |
| :---: | :---: | :---: |
| Functies voor mobiele apps beschikbaar | Code | Code of kennisgeving |

Gebruikers hebben niet de mogelijkheid om hun mobiele app te [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)registreren bij het registreren voor self-service wachtwoord resetten van . Gebruikers kunnen hun mobiele [https://aka.ms/mfasetup](https://aka.ms/mfasetup)app registreren op , of [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)in de nieuwe security info registratie preview op .

> [!WARNING]
> U moet de [geconvergeerde registratie voor selfservicewachtwoordreset en Azure Multi-Factor Authentication (Public preview)](concept-registration-mfa-sspr-converged.md) inschakelen voordat gebruikers toegang hebben tot de nieuwe ervaring op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> De authenticator-app kan niet worden geselecteerd als de enige verificatiemethode bij het configureren van een 1-gate-beleid. Ook de authenticator-app en slechts één aanvullende methode kunnen niet worden geselecteerd bij het configureren van een beleid met twee poorten.
> Wanneer u vervolgens SSPR-beleid configureert dat de authenticator-app als methode bevat, moet ten minste een extra methode worden geselecteerd bij het configureren van een 1-gate-beleid en moeten ten minste twee extra methoden worden geselecteerd bij het configureren van een beleid met twee poorten.
> De reden voor deze eis is dat de huidige SSPR-registratie-ervaring niet de optie omvat om de authenticator-app te registreren. De optie om de authenticator-app te registreren is inbegrepen bij de nieuwe [geconvergeerde registratie voor het opnieuw instellen van selfservicewachtwoorden en Azure Multi-Factor Authentication (Public preview).](concept-registration-mfa-sspr-converged.md)
> Het toestaan van beleid dat alleen de authenticator-app gebruikt (voor 1-gate-beleid), of de authenticator-app en slechts één aanvullende methode (voor beleid met twee poorten), kan ertoe leiden dat gebruikers zich niet meer registreren voor SSPR totdat ze zijn geconfigureerd om de nieuwe registratie-ervaring.

### <a name="change-authentication-methods"></a>Verificatiemethoden wijzigen

Als u begint met een beleid dat slechts één vereiste verificatiemethode heeft voor het opnieuw instellen of ontgrendelen van geregistreerde methoden en u dat wijzigt in twee methoden, wat gebeurt er dan?

| Aantal geregistreerde methoden | Aantal vereiste methoden | Resultaat |
| :---: | :---: | :---: |
| 1 of meer | 1 | **In staat** om te resetten of te ontgrendelen |
| 1 | 2 | **Kan niet** opnieuw instellen of ontgrendelen |
| 2 of meer | 2 | **In staat** om te resetten of te ontgrendelen |

Als u de typen verificatiemethoden wijzigt die een gebruiker kan gebruiken, u per ongeluk voorkomen dat gebruikers SSPR kunnen gebruiken als ze niet over de minimale hoeveelheid beschikbare gegevens beschikken.

Voorbeeld:
1. Het oorspronkelijke beleid is geconfigureerd met twee vereiste verificatiemethoden. Het gebruikt alleen het telefoonnummer van het kantoor en de beveiligingsvragen. 
2. De beheerder wijzigt het beleid om de beveiligingsvragen niet langer te gebruiken, maar staat het gebruik van een mobiele telefoon en een alternatieve e-mail toe.
3. Gebruikers zonder de mobiele telefoon of alternatieve e-mailvelden kunnen hun wachtwoorden niet opnieuw instellen.

## <a name="registration"></a>Registratie

### <a name="require-users-to-register-when-they-sign-in"></a>Gebruikers verplichten zich te registreren wanneer ze zich aanmelden

Voor het inschakelen van deze optie moet een gebruiker de wachtwoordresetregistratie voltooien als hij zich aanmeldt bij toepassingen met Azure AD. Deze werkstroom bevat de volgende toepassingen:

* Office 365
* Azure Portal
* Toegangsvenster
* Federatieve toepassingen
* Aangepaste toepassingen met Azure AD

Wanneer registratie is uitgeschakeld, kunnen gebruikers zich handmatig registreren. Ze kunnen [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) de koppeling **Registreren voor wachtwoordreset** bezoeken of selecteren onder het tabblad **Profiel** in het toegangspaneel.

> [!NOTE]
> Gebruikers kunnen de registratieportal voor het opnieuw instellen van wachtwoorden verwijderen door **annuleren** te selecteren of door het venster te sluiten. Maar ze worden gevraagd om zich te registreren elke keer dat ze zich aanmelden totdat ze hun registratie te voltooien.
>
> Deze onderbreking verbreekt de verbinding van de gebruiker niet als deze al is aangemeld.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Het aantal dagen instellen voordat gebruikers worden gevraagd hun verificatiegegevens opnieuw te bevestigen

Deze optie bepaalt de periode tussen het instellen en opnieuw bevestigen van verificatiegegevens en is alleen beschikbaar als u de **optie Gebruikers vereisen inschakelt om zich te registreren bij het aanmelden.**

Geldige waarden zijn 0 tot 730 dagen, met "0" wat betekent dat gebruikers nooit worden gevraagd om hun verificatiegegevens opnieuw te bevestigen.

## <a name="notifications"></a>Meldingen

### <a name="notify-users-on-password-resets"></a>Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord

Als deze optie is ingesteld op **Ja,** ontvangen gebruikers die hun wachtwoord opnieuw instellen een e-mail waarin wordt gemeld dat hun wachtwoord is gewijzigd. De e-mail wordt via de SSPR-portal verzonden naar hun primaire en alternatieve e-mailadressen die in Azure AD in het bestand staan. Niemand anders wordt op de hoogte gebracht van de gebeurtenis reset.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Alle beheerders op de hoogte stellen wanneer andere beheerders hun wachtwoorden opnieuw instellen

Als deze optie is ingesteld op **Ja,** ontvangen *alle beheerders* een e-mail naar hun primaire e-mailadres in het bestand in Azure AD. De e-mail waarschuwt hen dat een andere beheerder hun wachtwoord heeft gewijzigd met behulp van SSPR.

Voorbeeld: Er zijn vier beheerders in een omgeving. Beheerder A reset zijn wachtwoord met Behulp van SSPR. Beheerders B, C en D ontvangen een e-mail waarin ze worden gewaarschuwd voor het opnieuw instellen van het wachtwoord.

## <a name="on-premises-integration"></a>On-premises integratie

Als u Azure AD Connect installeert, configureert en inschakelt, hebt u de volgende extra opties voor on-premises integraties. Als deze opties grijs zijn, is terugschrijven niet goed geconfigureerd. Zie [Terugschrijven van wachtwoorden configureren](howto-sspr-writeback.md)voor meer informatie.

![Het valideren van wachtwoordterugschrijfgegevens is ingeschakeld en werkt][Writeback]

Op deze pagina ziet u een snelle status van de on-premises terugschrijfclient, een van de volgende berichten wordt weergegeven op basis van de huidige configuratie:

* Uw on-premises writeback-client is operationeel.
* Azure AD is online en is verbonden met uw on-premises writeback-client. Het lijkt er echter op dat de geïnstalleerde versie van Azure AD Connect verouderd is. Overweeg [Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) te upgraden om ervoor te zorgen dat u beschikt over de nieuwste connectiviteitsfuncties en belangrijke bugfixes.
* Helaas kunnen we de status van uw on-premises terugschrijfclient niet controleren omdat de geïnstalleerde versie van Azure AD Connect verouderd is. [Upgrade Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) om de status van uw verbinding te kunnen controleren.
* Helaas lijkt het erop dat we op dit moment geen verbinding kunnen maken met uw on-premises writeback-client. [Problemen met Azure AD Connect oplossen](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) om de verbinding te herstellen.
* Helaas kunnen we geen verbinding maken met uw on-premises writeback-client omdat wachtwoordwriteback niet goed is geconfigureerd. [Configureer wachtwoordterugschrijfterug](howto-sspr-writeback.md) om de verbinding te herstellen.
* Helaas lijkt het erop dat we op dit moment geen verbinding kunnen maken met uw on-premises writeback-client. Dit kan te wijten zijn aan tijdelijke problemen aan onze kant. Als het probleem zich blijft voordoen, [lost Azure AD Connect op](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) om de verbinding te herstellen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Wachtwoorden terugschrijven naar uw on-premises directory

Dit besturingselement bepaalt of wachtwoordterugschrijven is ingeschakeld voor deze map. Als de terugschrijfback is ingeschakeld, geeft dit de status aan van de on-premises terugschrijfservice. Dit besturingselement is handig als u het terugschrijven van wachtwoorden tijdelijk wilt uitschakelen zonder Azure AD Connect opnieuw te hoeven configureren.

* Als de switch is ingesteld op **Ja,** is writeback ingeschakeld en kunnen federatieve, doorgeefverificatie- of wachtwoordhashgesynchroniseerde gebruikers hun wachtwoorden opnieuw instellen.
* Als de switch is ingesteld op **Nee,** wordt writeback uitgeschakeld en kunnen federatieve, doorgeefverificatie of wachtwoordhash gesynchroniseerde gebruikers hun wachtwoorden niet opnieuw instellen.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Gebruikers toestaan accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen

Dit besturingselement geeft aan of gebruikers die de wachtwoordresetportal bezoeken, de optie moeten krijgen om hun on-premises Active Directory-accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen. Azure AD ontgrendelt standaard accounts wanneer het een wachtwoordreset uitvoert. U gebruikt deze instelling om deze twee bewerkingen te scheiden.

* Als ze zijn ingesteld op **Ja,** krijgen gebruikers de mogelijkheid om hun wachtwoord opnieuw in te stellen en het account te ontgrendelen, of om hun account te ontgrendelen zonder het wachtwoord opnieuw in te stellen.
* Als u bent ingesteld op **Nee,** kunnen gebruikers alleen een gecombineerde wachtwoordreset en accountontgrendelingsbewerking uitvoeren.

### <a name="on-premises-active-directory-password-filters"></a>On-premises Active Directory-wachtwoordfilters

Azure AD self-service password reset voert het equivalent uit van een door de beheerder gestarte wachtwoordreset in Active Directory. Als u een wachtwoordfilter van derden gebruikt om aangepaste wachtwoordregels af te dwingen en u vereist dat dit wachtwoordfilter wordt gecontroleerd tijdens het opnieuw instellen van het wachtwoord van Azure AD, moet u ervoor zorgen dat de wachtwoordfilteroplossing van derden is geconfigureerd om toe te passen in de scenario voor het opnieuw instellen van het beheerderswachtwoord. [Azure AD-wachtwoordbeveiliging voor Active Directory van Windows Server](concept-password-ban-bad-on-premises.md) wordt standaard ondersteund.

## <a name="password-reset-for-b2b-users"></a>Wachtwoord reset voor B2B-gebruikers

Het opnieuw instellen en wijzigen van wachtwoorden wordt volledig ondersteund voor alle B2B-configuraties (business-to-business). B2B-wachtwoordreset wordt in de volgende drie gevallen ondersteund:

* **Gebruikers van een partnerorganisatie met een bestaande Azure AD-tenant:** als de organisatie waarmee u samenwerkt een bestaande Azure AD-tenant heeft, respecteren we *het beleid voor het opnieuw instellen van wachtwoorden op die tenant.* Om het opnieuw instellen van wachtwoorden te laten werken, hoeft de partnerorganisatie alleen maar te zorgen dat Azure AD SSPR is ingeschakeld. Er zijn geen extra kosten verbonden aan Office 365-klanten en dit kan worden ingeschakeld door de stappen in onze handleiding [Voor wachtwoordbeheer](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) te volgen.
* **Gebruikers die zich aanmelden via** selfservice-aanmelding: Als de organisatie waarmee u samenwerkt de [selfservice-aanmeldingsfunctie](../users-groups-roles/directory-self-service-signup.md) heeft gebruikt om bij een tenant te komen, laten we ze het wachtwoord opnieuw instellen met de e-mail die ze hebben geregistreerd.
* **B2B-gebruikers**: Nieuwe B2B-gebruikers die zijn gemaakt met behulp van de nieuwe [Azure AD B2B-mogelijkheden,](../active-directory-b2b-what-is-azure-ad-b2b.md) kunnen hun wachtwoorden ook opnieuw instellen met de e-mail die ze tijdens het uitnodigingsproces hebben geregistreerd.

Als u dit scenario https://passwordreset.microsoftonline.com wilt testen, gaat u naar een van deze partnergebruikers. Als er een alternatieve e-mail of verificatie-e-mail is gedefinieerd, werkt het opnieuw instellen van wachtwoorden zoals verwacht.

> [!NOTE]
> Microsoft-accounts waaraan gasttoegang heeft gekregen tot uw Azure AD-tenant, zoals die van Hotmail.com, Outlook.com of andere persoonlijke e-mailadressen, kunnen Azure AD SSPR niet gebruiken. Ze moeten hun wachtwoord opnieuw instellen met behulp van de informatie in het [artikel Wanneer u zich niet aanmelden bij uw Microsoft-accountartikel.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

## <a name="next-steps"></a>Volgende stappen

De volgende koppelingen bieden aanvullende informatie over wachtwoordherstel met behulp van Azure AD:

* [Hoe kan ik een geslaagde implementatie van SSPR voltooien?](howto-sspr-deployment.md)
* [Uw wachtwoord opnieuw instellen of wijzigen](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registreren voor de selfservice voor het opnieuw instellen van een wachtwoord](../user-help/active-directory-passwords-reset-register.md)
* [Hebt u een vraag over licenties?](concept-sspr-licensing.md)
* [Welke gegevens worden gebruikt door selfservice voor wachtwoordherstel en welke gegevens moet u voor uw gebruikers invullen?](howto-sspr-authenticationdata.md)
* [Welke verificatiemethoden zijn beschikbaar voor gebruikers?](concept-sspr-howitworks.md#authentication-methods)
* [Wat zijn de beleidsopties bij selfservice voor wachtwoordherstel?](concept-sspr-policy.md)
* [Wat is Wachtwoord terugschrijven en waarom is dit van belang?](howto-sspr-writeback.md)
* [Hoe maak ik rapporten van activiteit in selfservice voor wachtwoordherstel?](howto-sspr-reporting.md)
* [Wat zijn alle opties in selfservice voor wachtwoordherstel en wat houden ze in?](concept-sspr-howitworks.md)
* [Ik denk dat er iets kapot is. Hoe los ik SSPR op?](active-directory-passwords-troubleshoot.md)
* [Ik heb een vraag die nog niet is beantwoord](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Azure Active Directory-verificatiemethoden die beschikbaar zijn en hoeveel er vereist zijn"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "On-premises informatie over het terugschrijven van integratiewachtwoorden en probleemoplossing"
