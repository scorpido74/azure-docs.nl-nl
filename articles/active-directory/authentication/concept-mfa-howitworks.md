---
title: Overzicht van Azure Multi-Factor Authentication
description: Meer informatie over hoe Azure Multi-Factor Authentication u helpt de toegang tot gegevens en toepassingen te beschermen terwijl u aan de vraag van de gebruiker voor een eenvoudig aanmeldings proces voldoet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5f8d08f0814ec69719c002ea9efd39bb38b7d16
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718013"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Hoe werkt het - Azure Multi-Factor Authentication

Meervoudige verificatie is een proces waarbij gebruikers tijdens het aanmeldproces om een aanvullende vorm van identificatie wordt gevraagd, zoals het invoeren van een code op hun mobiele telefoon of het uitvoeren van een vingerafdrukscan.

Als u alleen een wachtwoord gebruikt om gebruikers te verifiëren, is dit een onveilige aanvalsvector. Als het wachtwoord zwak is of elders is weergegeven, is het dan wel echt de gebruiker die zich met de gebruikersnaam en het wachtwoord aanmeldt, of is het een aanvaller? Wanneer u een tweede vorm van verificatie vereist, neemt de beveiliging toe omdat deze aanvullende factor niet eenvoudig door een aanvaller kan worden verkregen of gedupliceerd.

![Conceptafbeelding van de verschillende vormen van meervoudige verificatie](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication werkt door twee van de volgende verificatiemethoden te vereisen:

* Iets dat u weet, zoals een wachtwoord.
* Iets dat u hebt, zoals een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon of hardwaresleutel.
* Iets dat u bent: biometrische gegevens zoals een vingerafdruk of gezichtsscan.

Gebruikers kunnen zichzelf registreren voor zowel de self-service voor wachtwoordherstel als Azure Multi-Factor Authentication. Dit gebeurt in één stap, voor een eenvoudigere onboardingervaring. Beheerders kunnen definiëren welke vormen van secundaire verificatie kunnen worden gebruikt. Azure Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een self-service voor wachtwoordherstel uitvoeren om dat proces nog verder te beveiligen.

![Gebruikte verificatiemethoden op het aanmeldscherm](media/concept-authentication-methods/overview-login.png)

Azure Multi-Factor Authentication helpt bij het beschermen van de toegang tot gegevens en toepassingen, terwijl u eenvoudiger bent voor gebruikers. Het biedt extra beveiliging door een tweede vorm van verificatie te vereisen en sterke verificatie te bieden met behulp van een bereik aan gebruiks vriendelijke [verificatie methoden](concept-authentication-methods.md). Gebruikers kunnen of kunnen niet worden aangevraagd voor MFA op basis van configuratie beslissingen die een beheerder doet.

Uw toepassingen of services hoeven geen wijzigingen aan te brengen om Azure Multi-Factor Authentication te gebruiken. De verificatie prompts maken deel uit van de Azure AD-aanmeldings gebeurtenis, die de MFA-Challenge automatisch aanvraagt en verwerkt wanneer dat nodig is.

## <a name="available-verification-methods"></a>Beschik bare verificatie methoden

Wanneer een gebruiker zich aanmeldt bij een toepassing of service en een MFA-prompt ontvangt, kan hij of zij kiezen uit een van de geregistreerde formulieren van aanvullende verificatie. Een beheerder kan registratie van deze Azure Multi-Factor Authentication-verificatie methoden vereisen of de gebruiker kan toegang krijgen tot hun eigen [profiel](https://myprofile.microsoft.com) om verificatie methoden te bewerken of toe te voegen.

De volgende aanvullende vormen van verificatie kunnen worden gebruikt met Azure Multi-Factor Authentication:

* Microsoft Authenticator-app
* OATH-hardware-token
* Sms
* Spraakoproep

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication inschakelen en gebruiken

Gebruikers en groepen kunnen worden ingeschakeld voor Azure Multi-Factor Authentication om te vragen om aanvullende verificatie tijdens de aanmeldings gebeurtenis. De [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md) zijn beschikbaar voor alle Azure AD-tenants om het gebruik van de Microsoft Authenticator-app snel in te scha kelen voor alle gebruikers.

Voor uitgebreidere besturings elementen kunnen beleids regels voor [voorwaardelijke toegang](../conditional-access/overview.md) worden gebruikt voor het definiëren van gebeurtenissen of toepassingen waarvoor MFA is vereist. Met deze beleids regels kunt u regel matige aanmeldings gebeurtenissen toestaan wanneer de gebruiker zich in het bedrijfs netwerk of een geregistreerd apparaat bevindt, maar u wordt gevraagd om aanvullende verificatie factoren wanneer het externe apparaat of op een persoon is.

![Overzichtsdiagram van de werking van voorwaardelijke toegang om het aanmeldingsproces te beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over licenties de [functies en licenties voor Azure multi-factor Authentication](concept-mfa-licensing.md).

Als u MFA in actie wilt zien, schakelt u Azure Multi-Factor Authentication in voor een set test gebruikers in de volgende zelf studie:

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication inschakelen](./tutorial-enable-azure-mfa.md)