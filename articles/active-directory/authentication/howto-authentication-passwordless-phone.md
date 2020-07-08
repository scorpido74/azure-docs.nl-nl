---
title: Aanmelding zonder wacht woord met de app-Azure Active Directory Microsoft Authenticator
description: Aanmelding zonder wacht woord inschakelen voor Azure AD met behulp van de Microsoft Authenticator-app (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5dbf26bc636107576af15a0217eb16302f63f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85601629"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aanmelding zonder wacht woord inschakelen met de Microsoft Authenticator-app (preview)

De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder gebruik te maken van een wacht woord. Net als de technologie van [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification)gebruikt de Microsoft Authenticator verificatie op basis van een sleutel om een gebruikers referentie in te scha kelen die is gekoppeld aan een apparaat en gebruikmaakt van biometrisch of pincode. Deze verificatie methode kan worden gebruikt op elk platform, met inbegrip van mobiele apparaten, en voor elke app of website die kan worden geïntegreerd met micro soft-verificatie bibliotheken. 

![Voor beeld van een browser aanmelding waarin de gebruiker wordt gevraagd om de aanmelding goed te keuren](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

In plaats van een prompt weer te geven voor een wacht woord na Microsoft Authenticator het invoeren van een gebruikers naam, wordt een persoon die zich heeft aangemeld bij de app voor het inschakelen van de telefoon, een bericht te zien waarin wordt aangegeven dat ze een nummer in hun app willen tikken. In de app moet de gebruiker overeenkomen met het nummer. Kies goed keuren en geef vervolgens hun pincode of biometrische op. vervolgens wordt de verificatie voltooid.

> [!NOTE]
> Deze functie bevindt zich sinds 2017 maart in de app Microsoft Authenticator. als het beleid is ingeschakeld voor een directory, kunnen gebruikers deze stroom onmiddellijk tegen komen en een fout bericht zien als ze niet zijn ingeschakeld door het beleid. Stel uw gebruikers op de hoogte en bereid ze voor op deze wijziging.

## <a name="prerequisites"></a>Vereisten

- Azure Multi-Factor Authentication, waarbij push meldingen zijn toegestaan als verificatie methode 
- Nieuwste versie van Microsoft Authenticator geïnstalleerd op apparaten met iOS 8,0 of hoger, of Android 6,0 of hoger.

> [!NOTE]
> Als u de vorige Microsoft Authenticator app-aanmeldings voorbeeld voor apps hebt ingeschakeld met behulp van Azure AD Power shell, is deze ingeschakeld voor uw hele directory. Als u het gebruik van deze nieuwe methode inschakelt, wordt het Power shell-beleid vervangen. We raden u aan alle gebruikers in uw Tenant in te scha kelen via de nieuwe authenticatie methoden, anders kunnen gebruikers die geen deel uitmaken van het nieuwe beleid, zich niet meer aanmelden bij passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Verificatie methoden met een wacht woord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratie functies voor verificatie methoden met een wacht woord zijn afhankelijk van de functie voor gecombineerde registratie. Volg de stappen in het artikel [registratie van gecombineerde beveiligings gegevens inschakelen](howto-registration-mfa-sspr-combined.md)om gecombineerde registratie in te scha kelen.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Verificatie methoden met wacht woord-aanmeld authenticatie inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com)
1. Zoek naar *Azure Active Directory* en selecteer deze optie. **Beveiligings**  >  **verificatie methoden**selecteren  >  **beleid voor verificatie methode (preview-versie)**
1. Onder **Aanmelden met een wacht woord voor eenmalige aanmelding**kiest u de volgende opties
   1. **Inschakelen** -ja of Nee
   1. **Doel** -alle gebruikers of Selecteer gebruikers
1. **Opslaan** om het nieuwe beleid in te stellen

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Gebruikers registratie en het beheer van Microsoft Authenticator-app

1. Bladeren naar[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Meld u aan als dat nog niet is gebeurd
1. Voeg een verificator-app toe door op **methode toevoegen**te klikken, **verificator-app**te kiezen en op **toevoegen** te klikken.
1. Volg de instructies voor het installeren en configureren van de Microsoft Authenticator-app op uw apparaat
1. Klik op **gereed** om de installatie stroom van de verificator-app te volt ooien. 
1. In **Microsoft Authenticator**kiest u **Aanmelden via telefoon inschakelen** in de vervolg keuzelijst account
1. Volg de instructies in de app om de registratie te volt ooien voor aanmelden met een wacht woord. 

Organisaties kunnen hun gebruikers naar het artikel [Aanmelden met uw telefoon, niet uw wacht woord](../user-help/microsoft-authenticator-app-phone-signin-faq.md) voor meer hulp bij het instellen van de app Microsoft Authenticator en het inschakelen van de aanmelding via de telefoon. Als u deze instellingen wilt Toep assen, moet u zich mogelijk afmelden en weer aanmelden bij de Tenant. 

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met een wacht woord zonder referenties

Voor een open bare preview is het niet mogelijk om gebruikers af te dwingen om deze nieuwe referentie te maken of te gebruiken. Een gebruiker kan zich alleen aanmelden zonder wacht woord wanneer een beheerder hun Tenant heeft ingeschakeld **en** de gebruiker de Microsoft Authenticator-app heeft bijgewerkt om het aanmelden via de telefoon in te scha kelen.

Nadat u uw gebruikers naam op het web hebt getypt en **vervolgens volgende**selecteert, worden gebruikers weer gegeven met een nummer en wordt u gevraagd om het juiste aantal te verifiëren in de Microsoft Authenticator-app, in plaats van het wacht woord te gebruiken. 

![Voor beeld van een browser aanmelding met de app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Bekende problemen

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>De gebruiker is niet ingeschakeld door het beleid, maar heeft nog steeds een wacht woordloze aanmeldings methode in Microsoft Authenticator

Het is mogelijk dat een gebruiker op een bepaald moment een aanmeld referentie voor een wacht woord heeft gemaakt in de huidige Microsoft Authenticator-app of op een eerder apparaat. Zodra een beheerder het beleid voor de authenticatie methode voor een aanmelding met een wacht woord is ingeschakeld, wordt een gebruiker met een geregistreerde referentie gestart met de nieuwe aanmeldings prompt, ongeacht of deze zijn ingeschakeld voor het gebruik van het beleid of niet. Als de gebruiker niet gemachtigd is om de referentie per beleid te gebruiken, wordt er een fout melding weer gegeven na het volt ooien van de verificatie stroom. 

De beheerder kan ervoor kiezen om de gebruiker in staat te stellen om aanmelding zonder wacht woord te gebruiken, of de gebruiker moet de methode verwijderen. Als de gebruiker het geregistreerde apparaat niet meer heeft, kan hij of zij het [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) verwijderen. Als ze nog steeds de verificator voor MFA gebruiken, kunnen ze de optie **voor het uitschakelen** van de telefoon selecteren in het Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integratie van AD FS

Wanneer een gebruiker de Microsoft Authenticator wacht woordloze referentie heeft ingeschakeld, zal verificatie voor die gebruiker altijd standaard een melding verzenden voor goed keuring. Deze logica voor komt dat gebruikers in een hybride Tenant worden omgeleid naar AD FS voor aanmeldings verificatie zonder dat de gebruiker een extra stap hoeft te doen om op ' uw wacht woord gebruiken ' te klikken. Met dit proces worden ook alle on-premises beleids regels voor voorwaardelijke toegang en Pass Through-verificatie stromen genegeerd. 

Als een gebruiker een niet-beantwoorde aanmeldings wachtwoord voor aanmelding in behandeling heeft en zich opnieuw probeert aan te melden, wordt de gebruiker mogelijk geAD FS om in plaats daarvan een wacht woord in te voeren.  

### <a name="azure-mfa-server"></a>Azure MFA-server

Eind gebruikers die zijn ingeschakeld voor MFA via de on-premises Azure MFA-server van een organisatie, kunnen nog steeds één wacht woord voor eenmalige aanmelding op hetzelfde telefoon nummer maken en gebruiken. Als de gebruiker meerdere installaties (5 +) van de Microsoft Authenticator met de referentie probeert bij te werken, kan deze wijziging een fout veroorzaken.  

### <a name="device-registration"></a>Apparaatregistratie

Een van de vereisten voor het maken van deze nieuwe sterke referentie is dat het apparaat, waar de Microsoft Authenticator-app is geïnstalleerd, ook in de Azure AD-Tenant moet worden geregistreerd voor een afzonderlijke gebruiker. Als gevolg van de huidige beperkingen voor apparaatregistratie, kan een apparaat alleen worden geregistreerd in één Tenant. Deze limiet betekent dat er slechts één werk-of school account in de Microsoft Authenticator-app kan worden ingeschakeld voor aanmelding via de telefoon.

### <a name="intune-mobile-application-management"></a>Intune Mobile Application Management 

Eind gebruikers die onderhevig zijn aan een beleid waarvoor Mobile Application Management (MAM) is vereist, kunnen de wacht woordloze referenties in de Microsoft Authenticator-app niet registreren. 

> [!NOTE]
> Apparaatregistratie is niet hetzelfde als Apparaatbeheer of ' MDM. ' Er worden alleen een apparaat-ID en gebruikers-ID gekoppeld in de Azure AD-Directory.  

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de werking van verificatie met een wacht woord](concept-authentication-passwordless.md)

[Meer informatie over apparaatregistratie](../devices/overview.md#getting-devices-in-azure-ad)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
