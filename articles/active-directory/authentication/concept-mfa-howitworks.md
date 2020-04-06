---
title: Overzicht van Azure Multi-Factor Authentication
description: Ontdek hoe Azure Multi-Factor Authentication de toegang tot gegevens en toepassingen helpt te waarborgen en tegelijkertijd aan de vraag van gebruikers naar een eenvoudig aanmeldingsproces te voldoen.
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
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667365"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Hoe werkt het - Azure Multi-Factor Authentication

Meervoudige verificatie is een proces waarbij een gebruiker tijdens het aanmeldingsproces wordt gevraagd om een extra vorm van identificatie, zoals het invoeren van een code op zijn mobiele telefoon of het verstrekken van een vingerafdrukscan.

Als u alleen een wachtwoord gebruikt om een gebruiker te verifiëren, blijft er een onveilige vector over voor aanvallen. Als het wachtwoord zwak is of elders is blootgesteld, is het dan echt de gebruiker die zich aanmeldt met de gebruikersnaam en het wachtwoord, of is het een aanvaller? Wanneer u een tweede vorm van verificatie nodig hebt, wordt de beveiliging verhoogd omdat deze extra factor niet gemakkelijk is voor een aanvaller om te verkrijgen of te dupliceren.

![Conceptueel beeld van de verschillende vormen van multi-factor authenticatie](./media/concept-mfa-howitworks/methods.png)

Azure Multi-Factor Authentication werkt door twee of meer van de volgende verificatiemethoden te vereisen:

* Iets wat je weet, typisch een wachtwoord.
* Iets wat je hebt, zoals een vertrouwd apparaat dat niet gemakkelijk wordt gedupliceerd, zoals een telefoon of hardwaresleutel.
* Iets wat je bent - biometrie zoals een vingerafdruk of gezichtsscan.

Gebruikers kunnen zich in één stap registreren voor zowel selfservice wachtwoordreset als Azure Multi-Factor Authentication om de on-boarding-ervaring te vereenvoudigen. Beheerders kunnen bepalen welke vormen van secundaire verificatie kunnen worden gebruikt. Azure Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een selfservice wachtwoordreset uitvoeren om dat proces verder te beveiligen.

![Verificatiemethoden die worden gebruikt op het aanmeldingsscherm](media/concept-authentication-methods/overview-login.png)

Azure Multi-Factor Authentication helpt de toegang tot gegevens en toepassingen te waarborgen en tegelijkertijd de eenvoud voor gebruikers te behouden. Het biedt extra beveiliging door een tweede vorm van authenticatie te vereisen en levert sterke authenticatie via een reeks [gebruiksvriendelijke authenticatiemethoden.](concept-authentication-methods.md) Gebruikers kunnen al dan niet worden uitgedaagd voor MFA op basis van configuratiebeslissingen die een beheerder maakt.

Uw toepassingen of services hoeven geen wijzigingen aan te brengen om Azure Multi-Factor Authentication te gebruiken. De verificatieprompts maken deel uit van de aanmeldingsgebeurtenis Azure AD, die de MFA-uitdaging automatisch opvraagt en verwerkt wanneer dat nodig is.

## <a name="available-verification-methods"></a>Beschikbare verificatiemethoden

Wanneer een gebruiker zich aanmeldt bij een toepassing of service en een MFA-prompt ontvangt, kan hij of zij kiezen uit een van hun geregistreerde vormen van aanvullende verificatie. Een beheerder kan registratie van deze Azure Multi-Factor Verification verification methoden vereisen, of de gebruiker heeft toegang tot zijn eigen [Mijn profiel](https://myprofile.microsoft.com) om verificatiemethoden te bewerken of toe te voegen.

De volgende aanvullende verificatievormen kunnen worden gebruikt met Azure Multi-Factor Authentication:

* Microsoft Authenticator-app
* OATH-hardwaretoken
* Sms
* Spraakoproep

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication inschakelen en gebruiken

Gebruikers en groepen kunnen worden ingeschakeld voor Azure Multi-Factor Authentication om extra verificatie te vragen tijdens de aanmeldingsgebeurtenis. [Beveiligingsstandaarden](../fundamentals/concept-fundamentals-security-defaults.md) zijn beschikbaar voor alle Azure AD-tenants om snel het gebruik van de Microsoft Authenticator-app voor alle gebruikers in te schakelen.

Voor gedetailleerdere besturingselementen kan [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) worden gebruikt om gebeurtenissen of toepassingen te definiëren waarvoor MFA vereist is. Met dit beleid kunnen regelmatig aanmeldingsgebeurtenissen worden toegestaan wanneer de gebruiker zich op het bedrijfsnetwerk of een geregistreerd apparaat bevindt, maar wordt gevraagd om aanvullende verificatiefactoren wanneer deze op afstand of op een persoonlijk apparaat zijn.

![Overzichtsdiagram over hoe voorwaardelijke toegang werkt om het aanmeldingsproces te beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Volgende stappen

Zie Functies en [licenties voor Azure Multi-Factor Authentication voor](concept-mfa-licensing.md)meer informatie over licenties.

Als u MFA in actie wilt zien, schakelt u Azure Multi-Factor Authentication in voor een set testgebruikers in de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Azure Multi-Factor Authentication inschakelen](tutorial-mfa-applications.md)
