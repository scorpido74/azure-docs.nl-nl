---
title: Overzicht van Azure Active Directory-verificatie
description: Meer informatie over de verschillende verificatiemethoden en beveiligingsfuncties voor aanmeldingen van gebruikers met Azure Active Directory.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261259"
---
# <a name="what-is-azure-active-directory-authentication"></a>Wat is Azure Active Directory-verificatie?

Een van de belangrijkste kenmerken van een identiteitsplatform is het verifiëren of *verifiëren*van referenties wanneer een gebruiker zich aanmeldt bij een apparaat, toepassing of service. In Azure Active Directory (Azure AD) omvat verificatie meer dan alleen de verificatie van een gebruikersnaam en wachtwoord. Om de beveiliging te verbeteren en de behoefte aan helpdeskondersteuning te verminderen, bevat Azure AD-verificatie de volgende onderdelen:

* Self-service voor wachtwoord opnieuw instellen
* Azure Multi-Factor Authentication
* Hybride integratie om wachtwoordwijzigingen terug te schrijven naar on-premises omgeving
* Hybride integratie om beleid voor wachtwoordbeveiliging af te dwingen voor een on-premises omgeving
* Verificatie zonder wachtwoord

## <a name="improve-the-end-user-experience"></a>De ervaring van de eindgebruiker verbeteren

Azure AD helpt de identiteit van een gebruiker te beschermen en hun aanmeldingservaring te vereenvoudigen. Met functies zoals selfservice wachtwoordreset kunnen gebruikers hun wachtwoorden bijwerken of wijzigen via een webbrowser vanaf elk apparaat. Deze functie is vooral handig wanneer de gebruiker zijn wachtwoord is vergeten of zijn/haar account is vergrendeld. Zonder te wachten tot een helpdesk of beheerder ondersteuning biedt, kan een gebruiker zichzelf deblokkeren en blijven werken.

Azure Multi-Factor Authentication stelt gebruikers in staat om een extra vorm van verificatie te kiezen tijdens het aanmelden, zoals een telefoongesprek of een melding van een mobiele app. Deze mogelijkheid vermindert de vereiste voor een enkele, vaste vorm van secundaire verificatie zoals een hardwaretoken. Als de gebruiker momenteel niet over één vorm van aanvullende verificatie beschikt, kan hij een andere methode kiezen en blijven werken.

![Verificatiemethoden die worden gebruikt op het aanmeldingsscherm](media/concept-authentication-methods/overview-login.png)

Wachtwoordloze verificatie verwijdert de noodzaak voor de gebruiker om een veilig wachtwoord te maken en te onthouden. Met mogelijkheden zoals Windows Hello for Business of FIDO2-beveiligingssleutels kunnen gebruikers zich zonder wachtwoord aanmelden bij een apparaat of toepassing. Deze mogelijkheid kan de complexiteit van het beheren van wachtwoorden in verschillende omgevingen verminderen.

## <a name="self-service-password-reset"></a>Self-service voor wachtwoord opnieuw instellen

Self-service wachtwoord reset geeft gebruikers de mogelijkheid om hun wachtwoord te wijzigen of opnieuw in te stellen, zonder tussenkomst van de beheerder of helpdesk. Als het account van een gebruiker is vergrendeld of als hij zijn wachtwoord vergeet, kunnen ze aanwijzingen volgen om zichzelf te deblokkeren en weer aan het werk te gaan. Deze mogelijkheid vermindert helpdeskoproepen en productiviteitsverlies wanneer een gebruiker zich niet kan aanmelden bij zijn apparaat of toepassing.

Selfservice wachtwoord reset werkt in de volgende scenario's:

* **Wachtwoord wijzigen -** wanneer een gebruiker weet dat hun wachtwoord, maar wil veranderen in iets nieuws.
* **Wachtwoord opnieuw instellen -** wanneer een gebruiker zich niet kan aanmelden, bijvoorbeeld wanneer hij het wachtwoord is vergeten en zijn wachtwoord opnieuw wil instellen.
* **Accountontgrendelen -** wanneer een gebruiker zich niet kan aanmelden omdat zijn account is vergrendeld en zijn account wil ontgrendelen.

Wanneer een gebruiker zijn wachtwoord bijwerkt of opnieuw instelt met behulp van selfservicewachtwoordopnieuw instellen, kan dat wachtwoord ook worden teruggeschreven naar een on-premises Active Directory-omgeving. Wachtwoord writeback zorgt ervoor dat een gebruiker onmiddellijk gebruik kan maken van hun bijgewerkte referenties met on-premises apparaten en toepassingen.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Meervoudige verificatie is een proces waarbij een gebruiker tijdens het aanmeldingsproces wordt gevraagd om een extra vorm van identificatie, zoals het invoeren van een code op zijn mobiele telefoon of het verstrekken van een vingerafdrukscan.

Als u alleen een wachtwoord gebruikt om een gebruiker te verifiëren, blijft er een onveilige vector over voor aanvallen. Als het wachtwoord zwak is of elders is blootgesteld, is het dan echt de gebruiker die zich aanmeldt met de gebruikersnaam en het wachtwoord, of is het een aanvaller? Wanneer u een tweede vorm van verificatie nodig hebt, wordt de beveiliging verhoogd omdat deze extra factor niet gemakkelijk is voor een aanvaller om te verkrijgen of te dupliceren.

![Conceptueel beeld van de verschillende vormen van multi-factor authenticatie](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication werkt door twee of meer van de volgende verificatiemethoden te vereisen:

* Iets wat je weet, typisch een wachtwoord.
* Iets wat je hebt, zoals een vertrouwd apparaat dat niet gemakkelijk wordt gedupliceerd, zoals een telefoon of hardwaresleutel.
* Iets wat je bent - biometrie zoals een vingerafdruk of gezichtsscan.

Gebruikers kunnen zich in één stap registreren voor zowel selfservice wachtwoordreset als Azure Multi-Factor Authentication om de on-boarding-ervaring te vereenvoudigen. Beheerders kunnen bepalen welke vormen van secundaire verificatie kunnen worden gebruikt. Azure Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een selfservice wachtwoordreset uitvoeren om dat proces verder te beveiligen.

## <a name="password-protection"></a>Wachtwoordbeveiliging

Azure AD blokkeert standaard zwakke wachtwoorden zoals *Password1.* Een wereldwijde lijst met verboden wachtwoorden wordt automatisch bijgewerkt en afgedwongen met bekende zwakke wachtwoorden. Als een Azure AD-gebruiker probeert zijn wachtwoord in te stellen op een van deze zwakke wachtwoorden, ontvangt hij of zij een melding om een veiliger wachtwoord te kiezen.

Als u de beveiliging wilt verhogen, u aangepaste beleidsregels voor wachtwoordbeveiliging definiëren. Met dit beleid kunnen filters worden gebruikt om elke wijziging van een wachtwoord met een naam zoals *Contoso* of een locatie als *Londen*te blokkeren, bijvoorbeeld.

Voor hybride beveiliging u Azure AD-wachtwoordbeveiliging integreren met een on-premises Active Directory-omgeving. Een component die is geïnstalleerd in de on-prem-omgeving ontvangt de algemene lijst met verboden wachtwoorden en aangepaste beleidsregels voor wachtwoordbeveiliging van Azure AD en domeincontrollers gebruiken deze om gebeurtenissen voor wachtwoordwijzigingen te verwerken. Deze hybride aanpak zorgt ervoor dat het gebruik van sterke wachtwoorden ongeacht het gebruik van een gebruiker, ongeacht hoe of wanneer een gebruiker zijn of haar referenties wijzigt.

## <a name="passwordless-authentication"></a>Verificatie zonder wachtwoord

Het einddoel voor veel omgevingen is het verwijderen van het gebruik van wachtwoorden als onderdeel van aanmeldingsgebeurtenissen. Functies zoals Azure-wachtwoordbeveiliging of Azure Multi-Factor Authentication helpen de beveiliging te verbeteren, maar een gebruikersnaam en wachtwoord blijven een zwakke vorm van verificatie die kan worden blootgesteld of brute-force aangevallen.

![Beveiliging versus gemak met het verificatieproces dat leidt tot wachtwoordloos](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Wanneer u zich aanmeldt met een wachtwoordloze methode, worden referenties verstrekt via het gebruik van methoden zoals biometrie met Windows Hello for Business of een FIDO2-beveiligingssleutel. Deze verificatiemethoden kunnen niet gemakkelijk worden gedupliceerd door een aanvaller.

Azure AD biedt manieren om native te verifiëren met behulp van wachtwoordloze methoden om de aanmeldingservaring voor gebruikers te vereenvoudigen en het risico op aanvallen te verminderen.

## <a name="next-steps"></a>Volgende stappen

Zie de [snelstart voor selfservicewachtwoordreset][quickstart-sspr] en [azure multi-factor authentication-zelfstudie][tutorial-mfa-applications]om aan de slag te gaan.

Zie [Hoe azure AD selfservice wachtwoordreset werkt][concept-sspr]voor meer informatie over concepten voor het opnieuw instellen van selfservicewachtwoorden.

Zie [Hoe Azure Multi-Factor Authentication werkt][concept-mfa]voor meer informatie over multi-factor authenticatieconcepten.

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
