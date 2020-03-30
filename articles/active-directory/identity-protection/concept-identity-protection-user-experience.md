---
title: Gebruikerservaringen met Azure AD-identiteitsbeveiliging
description: Gebruikerservaring van Azure AD-identiteitsbeveiliging
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886998"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Gebruikerservaringen met Azure AD-identiteitsbeveiliging

Met Azure Active Directory Identity Protection u het als nog niet doen:

* Gebruikers verplichten zich te registreren voor Azure Multi-Factor Authentication (MFA)
* Herstel van risicovolle aanmeldingen en gecompromitteerde gebruikers automatiseren

Alle beleidsregels voor identiteitsbescherming hebben een impact op de aanmeldingservaring voor gebruikers. Door gebruikers toe te staan zich te registreren voor en hulpprogramma's zoals Azure MFA en selfservicewachtwoordreset te gebruiken, kan de impact worden verminderd. Deze tools samen met de juiste beleidskeuzes geven gebruikers een zelfhersteloptie wanneer ze die nodig hebben.

## <a name="multi-factor-authentication-registration"></a>Registratie van meervoudige verificatie

Als u het beleid voor identiteitsbescherming inschakelt waarbij registratie van meervoudige verificatie moet worden geregistreerd en al uw gebruikers zijn gericht, wordt ervoor gezorgd dat ze azure MFA in de toekomst zelf kunnen herstellen. Het configureren van dit beleid geeft uw gebruikers een periode van 14 dagen waarin ze kunnen kiezen om zich te registreren en aan het einde gedwongen worden zich te registreren. De ervaring voor gebruikers wordt hieronder beschreven. Meer informatie is te vinden in de documentatie van de eindgebruiker in het artikel, [Overzicht voor tweefactorverificatie en uw werk- of schoolaccount.](../user-help/user-help-two-step-verification-overview.md)

### <a name="registration-interrupt"></a>Registratieonderbreking

1. Bij aanmelding bij een azure AD-geïntegreerde toepassing krijgt de gebruiker een melding over de vereiste om het account in te stellen voor multi-factor authenticatie. Dit beleid wordt ook geactiveerd in de Windows 10 Out of Box Experience voor nieuwe gebruikers met een nieuw apparaat.
   
    ![Meer informatie vereist](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Voer de begeleide stappen uit om u te registreren voor Azure Multi-Factor Authentication en uw aanmelding te voltooien.

## <a name="risky-sign-in-remediation"></a>Risicovolle aanmeldingsherstel

Wanneer een beheerder een beleid voor aanmeldingsrisico's heeft geconfigureerd, worden de betrokken gebruikers op de hoogte gebracht wanneer ze zich proberen aan te melden en het beleidsrisiconiveau activeren. 

### <a name="risky-sign-in-self-remediation"></a>Riskante zelfherstel

1. De gebruiker wordt geïnformeerd dat er iets ongewoons is gedetecteerd over zijn aanmelding, zoals het aanmelden vanaf een nieuwe locatie, apparaat of app.
   
    ![Iets ongewoons prompt](./media/concept-identity-protection-user-experience/120.png)

1. De gebruiker is verplicht om zijn identiteit te bewijzen door Azure MFA in te vullen met een van hun eerder geregistreerde methoden. 

### <a name="risky-sign-in-administrator-unblock"></a>Deblokkering van riskante aanmeldingsbeheerder deblokkering en blokkering

Beheerders kunnen ervoor kiezen om gebruikers te blokkeren bij het aanmelden, afhankelijk van hun risiconiveau. Om te worden gedeblokkeerd, moeten eindgebruikers contact opnemen met hun IT-personeel of ze kunnen proberen zich aan te melden vanaf een vertrouwde locatie of apparaat. Zelfherstel door het uitvoeren van multi-factor authenticatie is in dit geval geen optie.

![Geblokkeerd door aanmeldingsrisicobeleid](./media/concept-identity-protection-user-experience/200.png)

IT-medewerkers kunnen de instructies in de sectie [Deblokkeren van gebruikers](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) opvolgen zodat gebruikers zich weer kunnen aanmelden.

## <a name="risky-user-remediation"></a>Riskante gebruikersherstel

Wanneer een gebruikersrisicobeleid is geconfigureerd, moeten gebruikers die voldoen aan de kans op een compromis van de gebruiker de herstelstroom van het gebruikerscompromis doorlopen voordat ze zich kunnen aanmelden. 

### <a name="risky-user-self-remediation"></a>Riskante gebruiker zelfherstel

1. De gebruiker wordt geïnformeerd dat hun accountbeveiliging in gevaar is als gevolg van verdachte activiteiten of gelekte referenties.
   
    ![Herstel](./media/concept-identity-protection-user-experience/101.png)

1. De gebruiker is verplicht om zijn identiteit te bewijzen door Azure MFA in te vullen met een van hun eerder geregistreerde methoden. 
1. Ten slotte wordt de gebruiker gedwongen om zijn wachtwoord te wijzigen met behulp van self-service wachtwoord resetten, omdat iemand anders kan hebben toegang gehad tot hun account.

## <a name="risky-sign-in-administrator-unblock"></a>Deblokkering van riskante aanmeldingsbeheerder deblokkering en blokkering

Beheerders kunnen ervoor kiezen om gebruikers te blokkeren bij het aanmelden, afhankelijk van hun risiconiveau. Om te worden gedeblokkeerd, moeten eindgebruikers contact opnemen met hun IT-personeel. Zelfherstel door het uitvoeren van multi-factor authenticatie en self-service wachtwoord reset is geen optie in dit geval.

![Geblokkeerd door gebruikersrisicobeleid](./media/concept-identity-protection-user-experience/104.png)

IT-medewerkers kunnen de instructies in de sectie [Deblokkeren van gebruikers](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) opvolgen zodat gebruikers zich weer kunnen aanmelden.

## <a name="see-also"></a>Zie ook

- [Risico's herstellen en gebruikers deblokkeren](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory-identiteitsbeveiliging](../active-directory-identityprotection.md) 
