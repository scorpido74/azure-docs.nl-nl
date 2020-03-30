---
title: Hoe het werkt Azure MFA - Azure Active Directory
description: Azure Multi-Factor Authentication helpt de toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd te voldoen aan de vraag van gebruikers naar een eenvoudig aanmeldingsproces.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484057"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Hoe werkt het - Azure Multi-Factor Authentication

De beveiliging van tweestapsverificatie ligt in de gelaagde aanpak. Het compromitteren van meerdere authenticatiefactoren vormt een aanzienlijke uitdaging voor aanvallers. Zelfs als een aanvaller erin slaagt om het wachtwoord van de gebruiker te leren, is het nutteloos zonder ook het bezit van de extra authenticatie methode. Het werkt door twee of meer van de volgende verificatiemethoden te vereisen:

* Iets wat u weet (meestal een wachtwoord)
* Iets wat je hebt (een vertrouwd apparaat dat niet gemakkelijk wordt gedupliceerd, zoals een telefoon)
* Iets wat je bent (biometrie)

<center>

![Afbeelding van conceptverificatiemethoden](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) helpt de toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd de eenvoud voor gebruikers te behouden. Het biedt extra beveiliging door een tweede vorm van authenticatie te vereisen en levert sterke authenticatie via een reeks [gebruiksvriendelijke authenticatiemethoden.](concept-authentication-methods.md) Gebruikers kunnen al dan niet worden uitgedaagd voor MFA op basis van configuratiebeslissingen die een beheerder maakt.

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication aanschaffen

Multi-Factor Authentication wordt geleverd als onderdeel van de volgende aanbiedingen:

* **Azure Active Directory Premium** of **Microsoft 365 Business** - Volledig gebruik van Azure Multi-Factor Authentication met behulp van voorwaardelijke toegangsbeleidsregels om meervoudige verificatie te vereisen.

* **Azure AD-vrije** of zelfstandige **Office 365-licenties** - Gebruik [Beveiligingsstandaardaat](../fundamentals/concept-fundamentals-security-defaults.md) om multi-factor authenticatie voor uw gebruikers en beheerders te vereisen.

* **Azure Active Directory globale beheerders**: een subset mogelijkheden voor Azure Multi-Factor Authentication is beschikbaar als een middel om globale beheerdersaccounts te beschermen.

> [!NOTE]
> Nieuwe klanten kunnen azure multi-factorauthenticatie niet langer kopen als een zelfstandige aanbieding vanaf 1 september 2018. Meervoudige verificatie blijft een beschikbare functie in Azure AD Premium-licenties.

## <a name="supportability"></a>Ondersteuning

Aangezien de meeste gebruikers gewend zijn om alleen wachtwoorden te gebruiken om te verifiëren, is het belangrijk dat uw organisatie communiceert met alle gebruikers met betrekking tot dit proces. Bewustwording kan de kans verkleinen dat gebruikers uw helpdesk bellen voor kleine problemen met betrekking tot MFA. Er zijn echter enkele scenario's waarin het tijdelijk uitschakelen van MFA noodzakelijk is. Gebruik de volgende richtlijnen om te begrijpen hoe u met deze scenario's omgaat:

* Train uw ondersteuningsmedewerkers om scenario's af te handelen waarin de gebruiker zich niet kan aanmelden omdat ze geen toegang hebben tot hun verificatiemethoden of omdat ze niet correct werken.
   * Met behulp van beleid voor voorwaardelijke toegang voor Azure MFA-service kunnen uw ondersteuningsmedewerkers een gebruiker toevoegen aan een groep die is uitgesloten van een beleid waarvoor MFA vereist is.
* Overweeg locaties met voorwaardelijke toegang te gebruiken als een manier om verificatieprompts in twee stappen te minimaliseren. Met deze functionaliteit kunnen beheerders verificatie in twee stappen omzeilen voor gebruikers die zich aanmelden vanaf een beveiligde vertrouwde netwerklocatie, zoals een netwerksegment dat wordt gebruikt voor nieuwe onboarding van gebruikers.
* Implementeer [Azure AD-identiteitsbeveiliging](../active-directory-identityprotection.md) en activeer verificatie in twee stappen op basis van risicodetecties.

## <a name="next-steps"></a>Volgende stappen

- [Stapsgewijze Azure Multi-Factor Authentication-implementatie](howto-mfa-getstarted.md)
