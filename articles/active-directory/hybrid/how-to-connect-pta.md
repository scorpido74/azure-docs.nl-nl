---
title: 'Azure AD Connect: verificatie doorgegeven | Microsoft Documenten'
description: In dit artikel wordt beschreven azure active directory (Azure AD) Pass-through Authentication en hoe azure AD-aanmeldingen hierdoor worden ingeschakeld door wachtwoorden van gebruikers te valideren tegen on-premises Active Directory.
services: active-directory
keywords: wat is Azure AD Connect Pass-Through Authentication, Active Directory installeren, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185507"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Wat is Azure Active Directory Pass-through-verificatie?

Azure Active Directory (Azure AD) Pass-through Authentication stelt uw gebruikers in staat om zich aan te melden bij zowel on-premises als cloudtoepassingen met dezelfde wachtwoorden. Deze functie biedt een betere gebruikerservaring (een wachtwoord minder te onthouden) en het vermindert de kosten voor de IT-helpdesk omdat gebruikers minder vaak een wachtwoord vergeten. Wanneer gebruikers zich aanmelden met Azure AD, valideert deze functie de wachtwoorden van gebruikers rechtstreeks tegen uw on-premises Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Deze functie is een alternatief voor [Azure AD Password Hash Synchronization](how-to-connect-password-hash-synchronization.md), dat hetzelfde voordeel biedt van cloudverificatie voor organisaties. Bepaalde organisaties die hun on-premises Active Directory-beveiligings- en wachtwoordbeleid willen afdwingen, kunnen er echter voor kiezen om pass-through-verificatie te gebruiken. Bekijk [deze handleiding](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) voor een vergelijking van de verschillende Azure AD-aanmeldingsmethoden en hoe u de juiste aanmeldingsmethode voor uw organisatie kiezen.

![Azure AD-pass-through-verificatie](./media/how-to-connect-pta/pta1.png)

U Pass-through Authentication combineren met de [functie Naadloze aanmelding.](how-to-connect-sso.md) Op deze manier hoeven uw gebruikers, wanneer ze toegang hebben tot applicaties op hun bedrijfsmachines binnen uw bedrijfsnetwerk, hun wachtwoorden niet in te typen om zich aan te melden.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Belangrijkste voordelen van het gebruik van Azure AD Pass-through-verificatie

- *Goede gebruikerservaring*
  - Gebruikers gebruiken dezelfde wachtwoorden om zich aan te melden bij zowel on-premises als cloudtoepassingen.
  - Gebruikers besteden minder tijd aan het praten met de IT-helpdesk bij het oplossen van wachtwoordgerelateerde problemen.
  - Gebruikers kunnen [selfservicewachtwoordbeheertaken](../authentication/active-directory-passwords-overview.md) in de cloud uitvoeren.
- *Eenvoudig te implementeren & beheren*
  - Er is geen behoefte aan complexe on-premises implementaties of netwerkconfiguratie.
  - Heeft slechts een lichtgewicht agent nodig om on-premises te worden geïnstalleerd.
  - Geen beheer overhead. De agent ontvangt automatisch verbeteringen en bugfixes.
- *Beveiligen*
  - On-premises wachtwoorden worden nooit in welke vorm dan ook in de cloud opgeslagen.
  - Beschermt uw gebruikersaccounts door naadloos te werken met [azure AD Conditional Access-beleidsregels](../active-directory-conditional-access-azure-portal.md), waaronder Multi-Factor Authentication (MFA), [het blokkeren van verouderde verificatie](../conditional-access/concept-conditional-access-conditions.md) en door [wachtwoordaanvallen van brute force uit te filteren.](../authentication/howto-password-smart-lockout.md)
  - De agent maakt alleen uitgaande verbindingen vanuit uw netwerk. Daarom is er geen verplichting om de agent te installeren in een perimeter netwerk, ook wel bekend als een DMZ.
  - De communicatie tussen een agent en Azure AD wordt beveiligd met verificatie op basis van certificaten. Deze certificaten worden om de paar maanden automatisch vernieuwd door Azure AD.
- *Zeer beschikbaar*
  - Extra agents kunnen op meerdere on-premises servers worden geïnstalleerd om een hoge beschikbaarheid van aanmeldingsverzoeken te bieden.

## <a name="feature-highlights"></a>Functiehoogtepunten

- Ondersteunt gebruikersaanmelding bij alle op webbrowsers gebaseerde toepassingen en in Microsoft Office-clienttoepassingen die [moderne verificatie](https://aka.ms/modernauthga)gebruiken.
- Aanmeldingsgebruikersnamen kunnen de on-premises standaardgebruikersnaam ()`userPrincipalName`of een ander kenmerk `Alternate ID`zijn dat is geconfigureerd in Azure AD Connect (bekend).
- De functie werkt naadloos samen met [voorwaardelijke toegangsfuncties](../active-directory-conditional-access-azure-portal.md) zoals Multi-Factor Authentication (MFA) om uw gebruikers te beveiligen.
- Geïntegreerd met cloudgebaseerd [selfservicewachtwoordbeheer,](../authentication/active-directory-passwords-overview.md)inclusief wachtwoordterugschrijven naar on-premises Active Directory en wachtwoordbeveiliging door veelgebruikte wachtwoorden te verbieden.
- Omgevingen met meerdere foresten worden ondersteund als er forestvertrouwensrelaties zijn tussen uw AD-forests en als de routeringsplaats voor naamachtervoegsel correct is geconfigureerd.
- Het is een gratis functie en u hebt geen betaalde edities van Azure AD nodig om het te gebruiken.
- Het kan worden ingeschakeld via [Azure AD Connect](whatis-hybrid-identity.md).
- Het maakt gebruik van een lichtgewicht on-premises agent die luistert naar en reageert op wachtwoord validatie verzoeken.
- Het installeren van meerdere agents biedt een hoge beschikbaarheid van aanmeldingsverzoeken.
- Het [beschermt](../authentication/howto-password-smart-lockout.md) uw on-premises accounts tegen brute force wachtwoordaanvallen in de cloud.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart](how-to-connect-pta-quick-start.md) - Azure AD Pass-through-verificatie starten.
- [Migreren van AD FS naar Pass-through Authentication](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - Een gedetailleerde handleiding om te migreren van AD FS (of andere federatietechnologieën) naar Pass-through Authentication.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md) - Configureer smart lockout-mogelijkheden op uw tenant om gebruikersaccounts te beschermen.
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md) - Leer welke scenario's worden ondersteund en welke niet.
- [Technical Deep Dive](how-to-connect-pta-how-it-works.md) - Begrijp hoe deze functie werkt.
- [Veelgestelde vragen](how-to-connect-pta-faq.md) - Antwoorden op veelgestelde vragen.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
- [Security Deep Dive](how-to-connect-pta-security-deep-dive.md) - Aanvullende diepgaande technische informatie over de functie.
- [Azure AD Seamless SSO](how-to-connect-sso.md) - Meer informatie over deze aanvullende functie.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Voor het indienen van nieuwe functieaanvragen.
