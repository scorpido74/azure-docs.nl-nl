---
title: Authenticating and securing users - Azure Active Directory
description: Hoe kan ik als Azure AD-beheerder gebruikersverificatie beschermen en tegelijkertijd ervoor zorgen dat dit minder gevolgen heeft voor eindgebruikers?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccbb754f03f77ce0571912a5443dc9b18c66b463
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381108"
---
# <a name="what-methods-are-available-for-authentication"></a>Welke verificatiemethoden zijn er beschikbaar?

Je leest in het nieuws steeds dat wachtwoorden worden gestolen en dat er met identiteiten wordt geknoeid. Door een tweede verificatiefactor naast een wachtwoord te gebruiken, wordt uw organisatie meteen een stuk beter beveiligd. Microsoft Azure Active Directory (Azure AD) bevat functies, zoals Azure Multi-Factor Authentication (Azure MFA) en de selfservice voor wachtwoordherstel (SSPR) van Azure AD, die beheerders helpen om hun organisaties en gebruikers in staat te stellen zich beter te beveiligen met behulp van aanvullende verificatiemethoden.

Er zijn veel scenario's waarin het volgende gebeurt: aanmelden bij een toepassing, het wachtwoord opnieuw instellen, Windows Hello instellen en meer. Mogelijk wordt uw gebruikers gevraagd om op aanvullende manieren te verifiëren dat ze zijn wie ze beweren te zijn.

Aanvullende verificatie kan de vorm hebben van verificatiemethoden zoals:

* Een code die wordt verstrekt in een e-mail of sms-bericht
* Een telefoontje
* Een melding of de code op de telefoon
* Het beantwoorden van beveiligingsvragen

![Een voorbeeld is de aanmeldingspagina login.microsoftonline.com in Chrome](media/overview-authentication/overview-login.png)

Met Azure MFA en de selfservice voor het opnieuw instellen van wachtwoorden van Azure AD, hebben beheerders een betere controle over de configuratie, het beleid, bewaking en rapportagemogelijkheden om hun organisatie te beveiligen via Azure AD en Azure Portal.

## <a name="self-service-password-reset"></a>Self-service voor wachtwoord opnieuw instellen

De selfservice voor het opnieuw instellen van wachtwoorden biedt uw gebruikers de mogelijkheid om waar en wanneer ze dat willen hun wachtwoord opnieuw in te stellen zonder dat er een beheerder bij te pas komt.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

De selfservice voor het opnieuw instellen van wachtwoorden omvat onder andere:

* **Wachtwoord wijzigen:** ik weet mijn wachtwoord maar wil het wijzigen.
* **Wachtwoord opnieuw instellen:** ik kan me niet aanmelden en wil mijn wachtwoord met behulp van een of meer goedgekeurde verificatiemethoden opnieuw instellen.
* **Account ontgrendelen:** ik kan me niet aanmelden omdat mijn account is vergrendeld en ik wil dit ontgrendelen met behulp van een of meer goedgekeurde verificatiemethoden.

## <a name="multi-factor-authentication"></a>Meervoudige verificatie

Azure Multi-Factor Authentication (MFA) is een Microsoft-oplossing voor verificatie in twee stappen. Met behulp van door een beheerder goedgekeurde verificatiemethode, helpt Azure MFA met het beveiligen van gegevens en toepassingen, terwijl tegelijkertijd aan de behoefte aan een eenvoudige aanmeldingsprocedure wordt voldaan.

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

De volgende stap is dieper in te gaan op de selfservice voor het opnieuw instellen van wachtwoorden en op Azure Multi-Factor Authentication en hoe deze geconfigureerd kunnen worden.

Lees het [snelstart-artikel Selfservice voor het opnieuw instellen van wachtwoorden inschakelen](quickstart-sspr.md) als u aan de slag wilt met de functie voor het opnieuw instellen van wachtwoorden .

U vindt meer informatie over de selfservice voor het opnieuw instellen van wachtwoorden in het artikel [How it works: Azure AD self-service password reset](concept-sspr-howitworks.md) (Hoe het werkt: De selfservice voor het opnieuw instellen van wachtwoorden in Azure AD).

Lees het artikel [How it works: Azure Multi-Factor Authentication](concept-mfa-howitworks.md) (Hoe het werkt: Azure Multi-Factor Authentication) als u meer wilt weten over Azure Multi-Factor Authentication.
