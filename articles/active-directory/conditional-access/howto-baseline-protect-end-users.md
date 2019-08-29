---
title: De beveiliging van de baseline-beleids regel voor eind gebruikers (preview)-Azure Active Directory
description: Beleid voor voorwaardelijke toegang om multi-factor Authentication voor gebruikers te vereisen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1a2e0bad39b54edc153416e4120bbc6912578c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125454"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Basislijn beleid: Beveiliging van eind gebruikers (preview-versie)

We denken meestal dat beheerders accounts de enige accounts zijn die moeten worden beveiligd met MFA (multi-factor Authentication). Beheerders hebben brede toegang tot gevoelige informatie en kunnen wijzigingen aanbrengen in instellingen voor het hele abonnement. Ongeldige Actors zijn echter vaak gericht op eind gebruikers. Na het verkrijgen van de toegang kunnen deze beschadigde actors namens de houder van het oorspronkelijke account toegang tot bevoegde informatie vragen of de volledige directory downloaden om een phishing-aanval uit te voeren op uw hele organisatie. Een gemeen schappelijke methode voor het verbeteren van de beveiliging van alle gebruikers is het vereisen van een sterkere vorm van account verificatie, zoals multi-factor Authentication (MFA).

Om een redelijk evenwicht tussen beveiliging en gebruiks vriendelijkheid te krijgen, moeten gebruikers niet elke keer dat ze zich aanmelden, worden gevraagd. Verificatie aanvragen die het normale gebruikers gedrag weer spie gelen, zoals het aanmelden vanaf hetzelfde apparaat vanaf dezelfde locatie, hebben een lage kans op inbreuk. Alleen aanmeldingen die als riskant worden beschouwd en eigenschappen van een ongeldige actor weer geven, moeten worden gevraagd met MFA-uitdagingen.

Beveiliging voor eind gebruikers is een op een risico gebaseerd MFA- [basislijn beleid](concept-baseline-protection.md) dat alle gebruikers in een directory beveiligt, inclusief alle beheerders rollen. Als u dit beleid inschakelt, moeten alle gebruikers zich registreren voor MFA met de verificator-app. Gebruikers kunnen de inschrijvings prompt voor MFA voor 14 dagen negeren, waarna de aanmelding wordt geblokkeerd totdat ze zich registreren voor MFA. Wanneer de registratie voor MFA is geregistreerd, wordt gebruikers alleen om MFA gevraagd tijdens het aanmelden met een Risk ante poging. Verdachte gebruikers accounts worden geblokkeerd totdat het wacht woord opnieuw is ingesteld en risico detecties zijn uitgeschakeld.

> [!NOTE]
> Dit beleid is van toepassing op alle gebruikers, inclusief gast accounts, en wordt geëvalueerd wanneer u zich aanmeldt bij alle toepassingen.

## <a name="recovering-compromised-accounts"></a>Verkraakte accounts herstellen

Ter bescherming van onze klanten vindt de gelekte referentie service van micro soft zoek naar openbaar beschik bare gebruikers naam/wachtwoord paren. Als ze overeenkomen met een van onze gebruikers, helpen we dat account direct te beveiligen. Gebruikers die zijn geïdentificeerd als een gelekte referentie, worden bevestigd. Deze gebruikers kunnen zich niet aanmelden totdat het wacht woord opnieuw is ingesteld.

Gebruikers aan wie een Azure AD Premium-licentie is toegewezen, kunnen de toegang herstellen via selfservice voor wachtwoord herstel (SSPR) als de mogelijkheid is ingeschakeld in hun Directory. Gebruikers zonder een Premium-licentie die wordt geblokkeerd, moeten contact opnemen met een beheerder om een hand matige wachtwoord herstel uit te voeren en de gemarkeerde gebruikers risico detectie te sluiten.

### <a name="steps-to-unblock-a-user"></a>Stappen voor het deblokkeren van een gebruiker

Controleer of de gebruiker is geblokkeerd door het beleid door de aanmeldings logboeken van de gebruiker te controleren.

1. Een beheerder moet zich aanmelden bij de **Azure Portal** en naar **Azure Active Directory** > **gebruikers** navigeren > op de naam van de gebruiker te klikken en naar aanmeldingen te gaan.
1. Om het wacht woord opnieuw instellen voor een geblokkeerde gebruiker te initiëren, moet een beheerder navigeren naar **Azure Active Directory** > gebruikers die zijn**gemarkeerd voor risico**
1. Klik op de gebruiker van wie het account is geblokkeerd voor het weer geven van informatie over de recente aanmeldings activiteit van de gebruiker.
1. Klik op wacht woord opnieuw instellen om een tijdelijk wacht woord toe te wijzen dat bij de volgende aanmelding moet worden gewijzigd.
1. Klik op alle gebeurtenissen negeren om de risico Score van de gebruiker opnieuw in te stellen.

De gebruiker kan zich nu aanmelden, het wacht woord opnieuw instellen en toegang krijgen tot de toepassing.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Omdat het **beveiligings beleid voor eind gebruikers** van toepassing is op alle gebruikers in uw directory, moeten er verschillende overwegingen worden gemaakt om te zorgen voor een soepele implementatie. Deze overwegingen omvatten het identificeren van gebruikers en service principes in azure AD die geen gebruik kunnen maken van MFA en toepassingen en clients die door uw organisatie worden gebruikt en die geen ondersteuning bieden voor moderne verificatie.

### <a name="legacy-protocols"></a>Verouderde protocollen

Verouderde verificatie protocollen (IMAP, SMTP, POP3, enz.) worden door e-mailclients gebruikt om verificatie aanvragen uit te voeren. Deze protocollen bieden geen ondersteuning voor MFA.  De meeste schendingen van het account die door micro soft worden gezien, worden veroorzaakt door ongeldige Actors die aanvallen uitvoeren op verouderde protocollen, waardoor MFA wordt omzeild. Om ervoor te zorgen dat MFA vereist is wanneer u zich aanmeldt bij een account en beschadigde actors niet in staat zijn om MFA over te slaan, blokkeert dit beleid alle verificatie aanvragen voor beheerders accounts van verouderde protocollen.

> [!WARNING]
> Voordat u dit beleid inschakelt, moet u ervoor zorgen dat uw gebruikers geen verouderde verificatie protocollen gebruiken. Zie het artikel [: Blok keer verouderde verificatie voor Azure AD](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) met voorwaardelijke toegang voor meer informatie.

## <a name="enable-the-baseline-policy"></a>Het basislijn beleid inschakelen

Beleid voor **beleids basislijn: De beveiliging van eind gebruikers (** preview) wordt vooraf geconfigureerd en wordt bovenaan weer gegeven wanneer u navigeert naar de Blade voorwaardelijke toegang in azure Portal.

Om dit beleid in te scha kelen en uw gebruikers te beschermen:

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **voorwaardelijke toegang**.
1. Selecteer **basislijn beleid in de lijst met beleids regels: Beveiliging van eind gebruikers (preview**-versie).
1. Stel **beleid inschakelen** om **beleid direct te gebruiken**in.
1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Basis beveiligings beleid voor voorwaardelijke toegang](concept-baseline-protection.md)
* [Vijf stappen voor het beveiligen van uw identiteits infrastructuur](../../security/fundamentals/steps-secure-identity.md)
* [Wat is voorwaardelijke toegang in Azure Active Directory?](overview.md)
