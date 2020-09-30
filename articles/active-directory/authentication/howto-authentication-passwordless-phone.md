---
title: Aanmelding zonder wacht woord met de app-Azure Active Directory Microsoft Authenticator
description: Aanmelding zonder wacht woord inschakelen voor Azure AD met behulp van de Microsoft Authenticator-app (preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b4792e73f6326bb9ac67ce3aabe10b8314bb826
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568200"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aanmelding zonder wacht woord inschakelen met de Microsoft Authenticator-app (preview)

De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder gebruik te maken van een wacht woord. Net als de technologie van [Windows hello voor bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification)gebruikt de Microsoft Authenticator verificatie op basis van een sleutel om een gebruikers referentie in te scha kelen die is gekoppeld aan een apparaat en gebruikmaakt van biometrisch of pincode. Deze verificatie methode kan worden gebruikt op elk platform, met inbegrip van mobiele apparaten, en voor elke app of website die kan worden geïntegreerd met micro soft-verificatie bibliotheken.

![Voor beeld van een browser aanmelding waarin de gebruiker wordt gevraagd om de aanmelding goed te keuren](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

In plaats van een prompt weer te geven voor een wacht woord na het invoeren van een gebruikers naam, ziet u een persoon die zich aanmeldt voor aanmelding bij de app Microsoft Authenticator, een bericht krijgt waarin de gebruiker wordt gevraagd een nummer in hun app te tikken. Om het aanmeldings proces in de app te volt ooien, moet de gebruiker overeenkomen met het aantal, **goed keuren**kiezen en vervolgens hun pincode of biometrische opgeven.

## <a name="prerequisites"></a>Vereisten

Als u aanmelden zonder wacht woord met de app Microsoft Authenticator wilt gebruiken, moet aan de volgende vereisten worden voldaan:

- Azure Multi-Factor Authentication, waarbij push meldingen zijn toegestaan als verificatie methode.
- Nieuwste versie van Microsoft Authenticator geïnstalleerd op apparaten met iOS 8,0 of hoger, of Android 6,0 of hoger.

> [!NOTE]
> Als u Microsoft Authenticator app-aanmeldings voorbeeld met wacht woord voor apps hebt ingeschakeld met behulp van Azure AD Power shell, is deze ingeschakeld voor uw hele directory. Als u het gebruik van deze nieuwe methode inschakelt, wordt het Power shell-beleid vervangen. U wordt aangeraden voor alle gebruikers in uw Tenant in te scha kelen via het menu nieuwe *authenticatie methoden* , anders kunnen gebruikers die geen deel uitmaken van het nieuwe beleid zich niet meer aanmelden zonder een wacht woord.

## <a name="enable-passwordless-authentication-methods"></a>Verificatie methoden met een wacht woord inschakelen

Als u verificatie zonder wacht woord in azure AD wilt gebruiken, moet u eerst de ervaring voor gecombineerde registratie inschakelen en gebruikers inschakelen voor de methode voor wacht woord minder.

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratie functies voor verificatie methoden met een wacht woord zijn afhankelijk van de functie voor gecombineerde registratie. Als u wilt dat gebruikers de gecombineerde registratie zelf kunnen volt ooien, volgt u de stappen voor het inschakelen van de [registratie van gecombineerde beveiligings gegevens](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Verificatie methoden met wacht woord-aanmeld authenticatie inschakelen

Met Azure AD kunt u kiezen welke verificatie methoden tijdens het aanmeldings proces kunnen worden gebruikt. Gebruikers registreren zich vervolgens voor de methoden die ze willen gebruiken.

Voer de volgende stappen uit om de verificatie methode in te scha kelen voor aanmelding zonder wacht woord.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een *Algemeen beheerders* account.
1. Zoek en selecteer *Azure Active Directory*en blader vervolgens naar **beveiligings**  >  **verificatie methoden**  >  **beleid voor verificatie methode (preview)**
1. Onder **aanmelding met een wacht woord voor aanmelden**kiest u de volgende opties:
   1. **Inschakelen** -ja of Nee
   1. **Doel** -alle gebruikers of Selecteer gebruikers
1. Selecteer **Opslaan**om het nieuwe beleid toe te passen.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Gebruikers registratie en het beheer van Microsoft Authenticator-app

Als de verificatie methode zonder wacht woord beschikbaar is voor gebruik in azure AD, moeten gebruikers zich nu registreren voor de verificatie methode met een wacht woord, met behulp van de volgende stappen:

1. Ga naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Meld u aan en voeg de verificator-app toe door **methode toevoegen > verificator-app**te selecteren en vervolgens **toe te voegen**.
1. Volg de instructies voor het installeren en configureren van de Microsoft Authenticator-app op het apparaat.
1. Selecteer **gereed** om de configuratie van de verificator te volt ooien.
1. Kies in **Microsoft Authenticator** -app **Aanmelden via telefoon inschakelen** in de vervolg keuzelijst voor het account dat is geregistreerd.
1. Volg de instructies in de app om de registratie van het account voor wacht woordloze aanmelding via de telefoon te volt ooien.

Organisaties kunnen hun gebruikers om zich aan te [melden met uw telefoon, niet uw wacht woord](../user-help/user-help-auth-app-sign-in.md) voor verdere hulp bij het configureren van de Microsoft Authenticator-app en het inschakelen van de aanmelding via de telefoon.

> [!NOTE]
> Gebruikers die niet zijn toegestaan door het beleid voor het gebruik van aanmelding via de telefoon, kunnen dit niet meer inschakelen binnen de app Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met een wacht woord zonder referenties

Een gebruiker kan aanmelden zonder wacht woord starten als een beheerder de Tenant heeft ingeschakeld en de gebruiker de Microsoft Authenticator-app heeft bijgewerkt om de aanmelding via de telefoon in te scha kelen.

Als u aanmelding met de telefoon wilt starten, moet u, nadat u een gebruikers naam op de aanmeldings pagina hebt getypt en **volgende**selecteren, gebruikers mogelijk **andere manieren selecteren om zich aan te melden**en vervolgens **een aanvraag in mijn Microsoft Authenticator-app goed keuren**. Gebruikers worden vervolgens weer gegeven met een nummer en worden gevraagd om het juiste nummer te selecteren Microsoft Authenticator in plaats van het wacht woord te gebruiken. Zodra gebruikers zijn aangemeld met een wacht woord, wordt ze gevraagd het opnieuw te gebruiken totdat ze een andere methode kiezen.

![Voor beeld van een browser aanmelding met de app Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Bekende problemen

De volgende bekende problemen bevinden zich in de huidige preview-versie.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Optie niet weer geven voor aanmelding met wacht woordloos aanmelden

Als een gebruiker een niet-beantwoorde aanmeldings wachtwoord voor aanmelding in behandeling heeft en zich opnieuw probeert aan te melden, ziet de gebruiker mogelijk alleen een optie om een wacht woord in te voeren. Open de Microsoft Authenticator en reageer op eventuele meldings prompts om de aanmelding zonder wacht woord te blijven gebruiken.

### <a name="federated-accounts"></a>Federatieve accounts

Wanneer een gebruiker een wacht woordloze referentie heeft ingeschakeld, zullen Azure AD-aanmeldingen stoppen met login_hint om de gebruiker te versnellen naar een federatieve aanmeldings locatie. Deze logica voor komt dat gebruikers in een hybride Tenant worden omgeleid naar AD FS voor aanmeldings verificatie zonder dat de gebruiker een extra stap hoeft te doen om op ' uw wacht woord gebruiken ' te klikken.

### <a name="azure-mfa-server"></a>Azure MFA-server

Eind gebruikers die zijn ingeschakeld voor MFA via de on-premises Azure MFA-server van een organisatie, kunnen nog steeds één wacht woord voor eenmalige aanmelding voor een mobiele telefoon maken en gebruiken. Als de gebruiker meerdere installaties (5 +) van de Microsoft Authenticator met de referentie probeert bij te werken, kan deze wijziging een fout veroorzaken.  

### <a name="device-registration"></a>Apparaatregistratie

Een van de vereisten voor het maken van deze nieuwe sterke referentie is dat het apparaat, waar de Microsoft Authenticator-app is geïnstalleerd, ook in de Azure AD-Tenant moet worden geregistreerd voor een afzonderlijke gebruiker. Als gevolg van de huidige beperkingen voor apparaatregistratie, kan een apparaat alleen worden geregistreerd in één Tenant. Deze limiet betekent dat er slechts één werk-of school account in de Microsoft Authenticator-app kan worden ingeschakeld voor aanmelding via de telefoon.

> [!NOTE]
> Apparaatregistratie is niet hetzelfde als Apparaatbeheer of ' MDM. ' Er worden alleen een apparaat-ID en gebruikers-ID gekoppeld in de Azure AD-Directory.  

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure AD-verificatie en wacht woord-methoden:

* [Meer informatie over de werking van verificatie met een wacht woord](concept-authentication-passwordless.md)
* [Meer informatie over apparaatregistratie](../devices/overview.md#getting-devices-in-azure-ad)
* [Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
