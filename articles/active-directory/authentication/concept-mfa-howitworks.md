---
title: Hoe werkt Azure MFA? Azure Active Directory
description: Multi-Factor Authentication van Azure helpt bij het bewaken van de toegang tot uw gegevens en toepassingen en komt tegemoet aan de wensen van gebruikers met een eenvoudige aanmeldprocedure.
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
ms.openlocfilehash: 0982f6fb70cd6866af48feab640d5dc36bcb6b28
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848677"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Hoe werkt het? Azure Multi-Factor Authentication

De beveiliging van verificatie in twee stappen is afhankelijk van de gelaagde benadering. Het in gevaar brengen van meerdere verificatie factoren vormt een aanzienlijke uitdaging voor aanvallers. Zelfs als een aanvaller het wacht woord van de gebruiker wil weten, is dit overbodig zonder dat ook de extra verificatie methode is. Het werkt met twee of meer van de volgende verificatie methoden:

* Iets dat u kent (doorgaans een wacht woord)
* Iets dat u hebt (een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon)
* Iets dat u bent (biometrie)

<center>

installatie kopie van ![conceptuele verificatie methoden](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) helpt bij het beveiligen van de toegang tot gegevens en toepassingen en voor gebruikers eenvoudiger te beheren. Het biedt extra beveiliging door een tweede vorm van verificatie te vereisen en sterke verificatie te bieden met behulp van een bereik aan gebruiks vriendelijke [verificatie methoden](concept-authentication-methods.md). Gebruikers kunnen of kunnen niet worden aangevraagd voor MFA op basis van configuratie beslissingen die een beheerder doet.

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication aanschaffen

Multi-Factor Authentication wordt geleverd als onderdeel van de volgende aanbiedingen:

* **Azure Active Directory Premium** of **Microsoft 365 Business** -volledig aanbevolen gebruik van Azure multi-factor Authentication met behulp van beleid voor voorwaardelijke toegang om multi-factor Authentication te vereisen.

* **Azure ad free** of zelfstandige **Office 365** -licenties: gebruik vooraf gemaakte [basis beveiligings beleidsregels voor voorwaardelijke toegang](../conditional-access/concept-baseline-protection.md) om multi-factor Authentication voor uw gebruikers en beheerders te vereisen.

* **Azure Active Directory globale beheerders**: een subset mogelijkheden voor Azure Multi-Factor Authentication is beschikbaar als een middel om globale beheerdersaccounts te beschermen.

> [!NOTE]
> Nieuwe klanten kunnen Azure Multi-Factor Authentication niet meer kopen als zelfstandige aanbieding die vanaf 1 september 2018. Multi-factor Authentication blijft een beschik bare functie in Azure AD Premium-licenties.

## <a name="supportability"></a>Ondersteuning

Aangezien de meeste gebruikers gewend zijn om alleen wacht woorden te gebruiken voor verificatie, is het belang rijk dat uw organisatie communiceert met alle gebruikers met betrekking tot dit proces. Met bewustzijn kan de kans worden beperkt dat gebruikers uw Help Desk bellen voor kleine problemen die betrekking hebben op MFA. Er zijn echter enkele scenario's waarin het tijdelijk uitschakelen van MFA nood zakelijk is. Gebruik de volgende richt lijnen om te begrijpen hoe u deze scenario's kunt afhandelen:

* Train uw ondersteunings medewerkers voor het afhandelen van scenario's waarbij de gebruiker zich niet kan aanmelden omdat ze geen toegang hebben tot hun verificatie methoden of ze niet goed werken.
   * Met het beleid voor voorwaardelijke toegang voor de Azure MFA-service kunnen uw ondersteunings medewerkers een gebruiker toevoegen aan een groep die is uitgesloten van een beleid waarvoor MFA is vereist.
* Overweeg het gebruik van voorwaardelijke toegang met de naam locaties als een manier om verificatie vragen in twee stappen te minimaliseren. Met deze functionaliteit kunnen beheerders verificatie in twee stappen overs laan voor gebruikers die zich aanmelden vanaf een beveiligde vertrouwde netwerk locatie, zoals een netwerk segment dat wordt gebruikt voor het onboarden van nieuwe gebruikers.
* Implementeer [Azure AD Identity Protection](../active-directory-identityprotection.md) en activeer verificatie in twee stappen op basis van risico detecties.

## <a name="next-steps"></a>Volgende stappen

- [Step-by-Step Azure Multi-Factor Authentication-implementatie](howto-mfa-getstarted.md)
