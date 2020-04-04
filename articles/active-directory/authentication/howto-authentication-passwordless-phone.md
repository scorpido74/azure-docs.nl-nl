---
title: Aanmelden zonder wachtwoord met de Microsoft Authenticator-app - Azure Active Directory
description: Aanmelden zonder wachtwoord bij Azure AD inschakelen met de Microsoft Authenticator-app (voorbeeld)
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
ms.openlocfilehash: 853e7143834a340b870b71ef1a287dab136e2783
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654054"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Aanmelden zonder wachtwoord inschakelen met de Microsoft Authenticator-app (voorbeeld)

De Microsoft Authenticator-app kan worden gebruikt om u aan te melden bij een Azure AD-account zonder een wachtwoord te gebruiken. Net als bij de technologie van [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)gebruikt de Microsoft Authenticator verificatie op basis van sleutels om een gebruikersreferentie in te schakelen die is gekoppeld aan een apparaat en een biometrische of pincode gebruikt. Deze verificatiemethode kan worden gebruikt op elk apparaatplatform, inclusief mobiel, en met elke app of website die integreert met Microsoft-verificatiebibliotheken. 

![Voorbeeld van een browseraanmelding waarin de gebruiker wordt gevraagd de aanmelding goed te keuren](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

In plaats van een prompt voor een wachtwoord te zien nadat hij een gebruikersnaam heeft ingevoerd, ziet een persoon die zich heeft aangemeld voor de telefoon vanuit de Microsoft Authenticator-app een bericht waarin staat dat hij of zij een nummer in zijn app moet tikken. In de app moet de gebruiker overeenkomen met het nummer, goedkeuren kiezen en vervolgens zijn pincode of biometrische gegevens opgeven en vervolgens de verificatie voltooien.

> [!NOTE]
> Deze mogelijkheid staat sinds maart 2017 in de Microsoft Authenticator-app, dus er is een mogelijkheid dat wanneer het beleid is ingeschakeld voor een directory, gebruikers deze stroom onmiddellijk kunnen tegenkomen en een foutmelding kunnen zien als ze niet zijn ingeschakeld door het beleid. Wees je ervan bewust en bereid je gebruikers voor op deze wijziging.

## <a name="prerequisites"></a>Vereisten

- Azure Multi-Factor Authentication, met pushmeldingen toegestaan als verificatiemethode 
- Nieuwste versie van Microsoft Authenticator geïnstalleerd op apparaten met iOS 8.0 of hoger, of Android 6.0 of hoger.

> [!NOTE]
> Als u de vorige inlogvoorbeeld microsoft Authenticator-app met azure AD PowerShell hebt ingeschakeld, is deze ingeschakeld voor uw hele directory. Als u deze nieuwe methode inschakelt, wordt het PowerShell-beleid vervangen. We raden u aan alle gebruikers in uw tenant in te schakelen via de nieuwe verificatiemethoden, anders kunnen gebruikers die niet in het nieuwe beleid staan niet langer zonder wachtwoord kunnen inloggen. 

## <a name="enable-passwordless-authentication-methods"></a>Verificatiemethoden zonder wachtwoord inschakelen

### <a name="enable-the-combined-registration-experience"></a>De gecombineerde registratie-ervaring inschakelen

Registratiefuncties voor wachtwoordloze verificatiemethoden zijn afhankelijk van de gecombineerde registratievoorbeeld. Volg de stappen in het artikel [Gecombineerde beveiliginggegevensregistratie inschakelen (voorbeeld)](howto-registration-mfa-sspr-combined.md)om het gecombineerde registratievoorbeeld in te schakelen.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Verificatiemethoden voor aanmelding via wachtwoordloze telefoon inschakelen

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Zoek naar *Azure Active Directory* en selecteer deze optie.  > **Beveiligingsverificatiemethoden** >  **Security****verificatiemethodebeleid selecteren (voorbeeld)**
1. Kies onder **Aanmelden voor een wachtwoordloze telefoon**de volgende opties
   1. **Inschakelen** - Ja of Nee
   1. **Doel** - Alle gebruikers of Selectie-gebruikers
1. **Opslaan** om het nieuwe beleid in te stellen

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Gebruikersregistratie en -beheer van de Microsoft Authenticator-app

1. Blader naar[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Aanmelden als u dit nog niet hebt gedaan
1. Een authenticator-app toevoegen door op **Methode toevoegen**te klikken, **authenticator-app**te kiezen en op **Toevoegen** te klikken
1. Volg de instructies voor het installeren en configureren van de Microsoft Authenticator-app op uw apparaat
1. Klik **op Gereed** om de installatiestroom van de Authenticator MFA-app te voltooien. 
1. Kies in **Microsoft Authenticator**de optie **Aanmelding voor telefoon inschakelen** in het vervolgkeuzemenu account
1. Volg de instructies in de app om de registratie voor het aanmelden van een wachtwoordloze telefoon te voltooien. 

Organisaties kunnen hun gebruikers wijzen op het artikel [Aanmelden met uw telefoon, niet uw wachtwoord](../user-help/microsoft-authenticator-app-phone-signin-faq.md) voor verdere hulp bij het instellen in de Microsoft Authenticator-app en het inschakelen van aanmelding via de telefoon. Om deze instellingen toe te passen, moet u zich mogelijk afmelden en weer inloggen bij de tenant. 

## <a name="sign-in-with-passwordless-credential"></a>Aanmelden met wachtwoordloze referenties

Voor openbare preview is er geen manier om gebruikers af te dwingen deze nieuwe referentie te maken of te gebruiken. Een gebruiker zal alleen meldingsloos aanmelden zonder wachtwoord tegenkomen zodra een beheerder zijn tenant heeft ingeschakeld **en** de gebruiker zijn Microsoft Authenticator-app heeft bijgewerkt om zich aan te melden bij de telefoon.

Na het typen van uw gebruikersnaam op het web en het selecteren van **Volgende,** gebruikers worden gepresenteerd met een nummer en wordt gevraagd in hun Microsoft Authenticator app om het juiste nummer te verifiëren in plaats van het gebruik van hun wachtwoord te selecteren. 

![Voorbeeld van een browseraanmelding met de Microsoft Authenticator-app](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Bekende problemen

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Gebruiker is niet ingeschakeld door het beleid, maar heeft nog steeds wachtwoordloze telefoon aanmeldingsmethode in Microsoft Authenticator

Het is mogelijk dat een gebruiker op een gegeven moment een wachtwoordloze aanmeldingsreferentie voor een wachtwoordloze telefoon heeft gemaakt in hun huidige Microsoft Authenticator-app of op een eerder apparaat. Zodra een beheerder het verificatiemethodebeleid voor wachtwoordloze telefoonaanmelding inschakelt, begint elke gebruiker met een geregistreerde referentie de nieuwe aanmeldingsprompt te ervaren, ongeacht of deze is ingeschakeld om het beleid al dan niet te gebruiken. Als de gebruiker de referentie niet heeft mogen gebruiken volgens het beleid, ziet hij of zij een fout na het voltooien van de verificatiestroom. 

De beheerder kan ervoor kiezen om de gebruiker in staat te stellen zich aan te melden voor een wachtwoordloze telefoon of de gebruiker moet de methode verwijderen. Als de gebruiker het geregistreerde apparaat niet [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) meer heeft, kan hij het apparaat verwijderen. Als ze de Authenticator voor MFA nog steeds gebruiken, kunnen ze kiezen **voor Telefoonaanmelding uitschakelen** vanuit de Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>AD FS-integratie

Wanneer een gebruiker de microsoft authenticator-wachtwoordloze referentie heeft ingeschakeld, wordt de verificatie voor die gebruiker altijd standaard verzonden naar het verzenden van een melding ter goedkeuring. Deze logica voorkomt dat gebruikers in een hybride tenant worden doorverwezen naar ADFS voor aanmeldingsverificatie zonder dat de gebruiker een extra stap neemt om te klikken op 'In plaats daarvan uw wachtwoord gebruiken'. Dit proces omzeilt ook on-premises beleid voor voorwaardelijke toegang en verificatiestromen doorstaan. 

Als een gebruiker een niet-beantwoorde wachtwoordloze verificatie van de telefoon in behandeling heeft en probeert zich opnieuw aan te melden, kan de gebruiker naar ADFS worden geleid om in plaats daarvan een wachtwoord in te voeren.  

### <a name="azure-mfa-server"></a>Azure MFA-server

Eindgebruikers die zijn ingeschakeld voor MFA via de on-premises Azure MFA-server van een organisatie, kunnen nog steeds één wachtwoordloze aanmeldingsreferentie voor telefoon maken en gebruiken. Als de gebruiker meerdere installaties (5+) van de Microsoft Authenticator probeert te upgraden met de referentie, kan deze wijziging leiden tot een fout.  

### <a name="device-registration"></a>Apparaatregistratie

Een van de vereisten om deze nieuwe sterke referentie te maken, is dat het apparaat, waar de Microsoft Authenticator-app is geïnstalleerd, ook moet worden geregistreerd binnen de Azure AD-tenant aan een individuele gebruiker. Vanwege de huidige beperkingen voor apparaatregistratie kan een apparaat alleen in één tenant worden geregistreerd. Deze limiet betekent dat slechts één werk- of schoolaccount in de Microsoft Authenticator-app kan worden ingeschakeld voor telefonischaanmelden.

### <a name="intune-mobile-application-management"></a>Intune Mobile Application Management 

Eindgebruikers die onderworpen zijn aan een beleid waarvoor mam (Mobile Application Management) vereist, kunnen de wachtwoordloze referentie niet registreren in de Microsoft Authenticator-app. 

> [!NOTE]
> Apparaatregistratie is niet hetzelfde als apparaatbeheer of 'MDM'. Het koppelt alleen een apparaat-id en een gebruikersnaam samen in de Azure AD-map.  

## <a name="next-steps"></a>Volgende stappen

[Wat betekent zonder wachtwoord?](concept-authentication-passwordless.md)

[Meer informatie over apparaatregistratie](../devices/overview.md#getting-devices-in-azure-ad)

[Meer informatie over Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
