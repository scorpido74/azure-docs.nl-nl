---
title: Overzicht van Azure Multi-Factor Authentication
description: Meer informatie over hoe Azure Multi-Factor Authentication u helpt de toegang tot gegevens en toepassingen te beschermen terwijl u aan de vraag van de gebruiker voor een eenvoudig aanmeldings proces voldoet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80667365"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Hoe werkt het - Azure Multi-Factor Authentication

Multi-factor Authentication is een proces waarbij een gebruiker wordt gevraagd tijdens het aanmeldings proces om een extra vorm van identificatie, zoals het invoeren van een code op hun cellphone of voor het bieden van een vingerafdruk scan.

Als u alleen een wacht woord gebruikt om een gebruiker te verifiëren, blijft er een onveilige vector voor aanvallen. Als het wacht woord zwak is of elders is weer gegeven, is het echt de gebruiker die zich aanmeldt met de gebruikers naam en het wacht woord of is het een aanvaller? Wanneer u een tweede vorm van verificatie nodig hebt, wordt de beveiliging verhoogd omdat deze extra factor niet duidelijk is voor een aanvaller om te voor komen dat deze wordt opgehaald of gedupliceerd.

![Conceptuele afbeelding van de verschillende vormen van multi-factor Authentication](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication werkt door twee of meer van de volgende verificatie methoden te vereisen:

* Iets wat u kent, doorgaans een wacht woord.
* Iets wat u hebt, zoals een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon-of hardware-sleutel.
* Iets wat u wilt: biometrie, zoals een vinger afdruk of een gezichts scan.

Gebruikers kunnen zich registreren voor selfservice voor wachtwoord herstel en Azure Multi-Factor Authentication in één stap om de on-board ervaring te vereenvoudigen. Beheerders kunnen definiëren welke vormen van secundaire authenticatie kunnen worden gebruikt. Azure Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een self-service voor wachtwoord herstel moeten uitvoeren om dat proces verder te beveiligen.

![Verificatie methoden die in gebruik zijn op het aanmeldings scherm](media/concept-authentication-methods/overview-login.png)

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

![Overzichts diagram van hoe voorwaardelijke toegang het aanmeldings proces kan beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over licenties de [functies en licenties voor Azure multi-factor Authentication](concept-mfa-licensing.md).

Als u MFA in actie wilt zien, schakelt u Azure Multi-Factor Authentication in voor een set test gebruikers in de volgende zelf studie:

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication inschakelen](tutorial-mfa-applications.md)
