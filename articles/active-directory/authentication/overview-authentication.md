---
title: Overzicht van Azure Active Directory verificatie
description: Meer informatie over de verschillende verificatie methoden en beveiligings functies voor gebruikers aanmeldingen met Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261259"
---
# <a name="what-is-azure-active-directory-authentication"></a>Wat is Azure Active Directory verificatie?

Een van de belangrijkste functies van een identiteits platform is het verifiëren of *verifiëren*van referenties wanneer een gebruiker zich aanmeldt bij een apparaat, toepassing of service. In Azure Active Directory (Azure AD) omvat de verificatie meer dan alleen de verificatie van een gebruikers naam en wacht woord. Azure AD-verificatie omvat de volgende onderdelen om de beveiliging te verbeteren en de nood zaak van helpdesk ondersteuning te verminderen:

* Self-service voor wachtwoord opnieuw instellen
* Verificatie op basis van meerdere factoren van Azure
* Hybride integratie om wachtwoord wijzigingen terug te schrijven naar de on-premises omgeving
* Hybride integratie voor het afdwingen van wachtwoord beveiligings beleid voor een on-premises omgeving
* Verificatie zonder wachtwoord

## <a name="improve-the-end-user-experience"></a>De ervaring van de eind gebruiker verbeteren

Azure AD helpt bij het beschermen van de identiteit van een gebruiker en het vereenvoudigen van de aanmeldings ervaring. Met functies als selfservice voor wachtwoord herstel kunnen gebruikers hun wacht woorden bijwerken of wijzigen met behulp van een webbrowser op elk apparaat. Deze functie is vooral nuttig wanneer de gebruiker het wacht woord vergeet of het account is vergrendeld. Zonder te wachten tot een Help Desk of beheerder ondersteuning biedt, kan een gebruiker zichzelf deblokkeren en blijven werken.

Met Azure Multi-Factor Authentication kunnen gebruikers een extra verificatie vorm kiezen tijdens het aanmelden, zoals een telefoon gesprek of een melding voor mobiele apps. Deze mogelijkheid vermindert de vereiste voor één vaste vorm van secundaire authenticatie, zoals een hardware-token. Als de gebruiker momenteel niet beschikt over één vorm van aanvullende verificatie, kan deze een andere methode kiezen en blijven werken.

![Verificatie methoden die in gebruik zijn op het aanmeldings scherm](media/concept-authentication-methods/overview-login.png)

Met een wacht woord-authenticatie wordt de gebruiker niet meer nodig om een veilig wacht woord te maken en te onthouden. Met mogelijkheden als Windows hello voor bedrijven-of FIDO2-beveiligings sleutels kunnen gebruikers zich zonder wacht woord aanmelden bij een apparaat of toepassing. Deze mogelijkheid kan de complexiteit van het beheer van wacht woorden in verschillende omgevingen verminderen.

## <a name="self-service-password-reset"></a>Self-service voor wachtwoord opnieuw instellen

Selfservice voor wachtwoord herstel biedt gebruikers de mogelijkheid om hun wacht woord te wijzigen of opnieuw in te stellen, zonder de rol van beheerder of Help Desk. Als het account van een gebruiker is vergrendeld of als het wacht woord is verg eten, kunnen ze de aanwijzingen volgen om zichzelf te deblokkeren en terug te gaan naar het werk. Deze mogelijkheid vermindert helpdesk oproepen en productiviteits verlies wanneer een gebruiker zich niet kan aanmelden bij hun apparaat of een toepassing.

Self-service voor wacht woord opnieuw instellen werkt in de volgende scenario's:

* **Wachtwoord wijziging:** wanneer een gebruiker het wacht woord kent, maar wil dit wijzigen in een nieuwe waarde.
* **Wacht woord opnieuw instellen:** wanneer een gebruiker zich niet kan aanmelden, bijvoorbeeld wanneer het wacht woord is verg eten en het wacht woord opnieuw wilt instellen.
* **Account ontgrendelen:** wanneer een gebruiker zich niet kan aanmelden omdat het account is vergrendeld en u het account wilt ontgrendelen.

Wanneer een gebruiker het wacht woord bijwerkt of opnieuw instelt met behulp van selfservice voor wachtwoord herstel, kan dat wacht woord ook worden teruggeschreven naar een on-premises Active Directory omgeving. Met wacht woord terugschrijven zorgt u ervoor dat een gebruiker de bijgewerkte referenties direct kan gebruiken met on-premises apparaten en toepassingen.

## <a name="azure-multi-factor-authentication"></a>Verificatie op basis van meerdere factoren van Azure

Multi-factor Authentication is een proces waarbij een gebruiker wordt gevraagd tijdens het aanmeldings proces om een extra vorm van identificatie, zoals het invoeren van een code op hun cellphone of voor het bieden van een vingerafdruk scan.

Als u alleen een wacht woord gebruikt om een gebruiker te verifiëren, blijft er een onveilige vector voor aanvallen. Als het wacht woord zwak is of elders is weer gegeven, is het echt de gebruiker die zich aanmeldt met de gebruikers naam en het wacht woord of is het een aanvaller? Wanneer u een tweede vorm van verificatie nodig hebt, wordt de beveiliging verhoogd omdat deze extra factor niet duidelijk is voor een aanvaller om te voor komen dat deze wordt opgehaald of gedupliceerd.

![Conceptuele afbeelding van de verschillende vormen van multi-factor Authentication](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication werkt door twee of meer van de volgende verificatie methoden te vereisen:

* Iets wat u kent, doorgaans een wacht woord.
* Iets wat u hebt, zoals een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon-of hardware-sleutel.
* Iets wat u wilt: biometrie, zoals een vinger afdruk of een gezichts scan.

Gebruikers kunnen zich registreren voor selfservice voor wachtwoord herstel en Azure Multi-Factor Authentication in één stap om de on-board ervaring te vereenvoudigen. Beheerders kunnen definiëren welke vormen van secundaire authenticatie kunnen worden gebruikt. Azure Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een self-service voor wachtwoord herstel moeten uitvoeren om dat proces verder te beveiligen.

## <a name="password-protection"></a>Wachtwoord beveiliging

Azure AD blokkeert standaard zwakke wacht woorden, zoals *Wachtwoord1*. Er wordt automatisch een lijst met verboden wacht woorden bijgewerkt en afgedwongen met bekende zwakke wachtwoord typen. Als een Azure AD-gebruiker het wacht woord probeert in te stellen op een van deze zwakke wacht woorden, wordt een melding weer gegeven om een veiligere wacht woord te kiezen.

U kunt de beveiliging verbeteren door aangepaste beleids regels voor wachtwoord beveiliging te definiëren. Deze beleids regels kunnen filters gebruiken om de variatie te blok keren van een wacht woord dat een naam bevat zoals *Contoso* of een locatie zoals *Londen*.

Voor hybride beveiliging kunt u Azure AD-wachtwoord beveiliging integreren met een on-premises Active Directory omgeving. Een onderdeel dat in de on-premises omgeving is geïnstalleerd, ontvangt de globale verboden wachtwoord lijst en aangepaste beleids regels voor wachtwoord beveiliging van Azure AD, en domein controllers gebruiken ze om wachtwoord wijzigings gebeurtenissen te verwerken. Deze hybride aanpak zorgt ervoor dat het gebruik van sterke wacht woorden afdwingt, ongeacht hoe of waar een gebruiker hun referenties wijzigt.

## <a name="passwordless-authentication"></a>Verificatie zonder wachtwoord

Het eind doel voor veel omgevingen is het gebruik van wacht woorden te verwijderen als onderdeel van aanmeldings gebeurtenissen. Functies zoals Azure-wachtwoord beveiliging of Azure Multi-Factor Authentication helpen de beveiliging te verbeteren, maar een gebruikers naam en wacht woord blijven een zwakke vorm van verificatie die beschikbaar kan worden gesteld of kunnen worden aangetast.

![Beveiliging versus gebruiks gemak met het verificatie proces dat is gekoppeld aan een wacht woord](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Wanneer u zich aanmeldt met een methode zonder wacht woord, worden er referenties gegeven via het gebruik van methoden als biometrie met Windows hello voor bedrijven of een FIDO2-beveiligings sleutel. Deze verificatie methoden kunnen niet eenvoudig worden gedupliceerd door een aanvaller.

Azure AD biedt manieren om op eigen wijze te verifiëren met behulp van methoden met een wacht woord om de aanmeldings ervaring voor gebruikers te vereenvoudigen en het risico op aanvallen te verminderen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de [Quick start voor selfservice voor wachtwoord herstel][quickstart-sspr] en [Azure multi-factor Authentication zelf studie][tutorial-mfa-applications].

Zie [hoe Azure AD self-service password reset werkt][concept-sspr]voor meer informatie over de concepten van self-service voor het opnieuw instellen van wacht woorden.

Zie [hoe Azure multi-factor Authentication werkt][concept-mfa]voor meer informatie over multi-factor Authentication-concepten.

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
