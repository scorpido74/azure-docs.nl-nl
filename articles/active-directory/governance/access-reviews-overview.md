---
title: Wat zijn toegangsbeoordelingen? - Azure Active Directory | Microsoft Azure
description: Met Azure Active Directory-toegangsbeoordelingen u groepslidmaatschap en toepassingstoegang beheren om te voldoen aan governance-, risicobeheer- en nalevingsinitiatieven in uw organisatie.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262085"
---
# <a name="what-are-azure-ad-access-reviews"></a>Wat zijn toegangsbeoordelingen in Azure AD?

Met Azure Active Directory-toegangsbeoordelingen (Azure AD) kunnen organisaties groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt beheren. De toegang van de gebruiker kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste mensen toegang hebben.

Hier is een video die een snel overzicht geeft van toegangsbeoordelingen:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Waarom zijn toegangsbeoordelingen belangrijk?

Met Azure AD u intern samenwerken binnen uw organisatie en met gebruikers van externe organisaties, zoals partners. Gebruikers kunnen lid worden van groepen, gasten uitnodigen, verbinding maken met cloud-apps en op afstand werken vanaf hun werk of persoonlijke apparaten. Het gemak van het benutten van de kracht van self-service heeft geleid tot een behoefte aan betere mogelijkheden voor toegangsbeheer.

- Hoe zorg je ervoor dat nieuwe medewerkers de juiste toegang hebben om productief te zijn?
- Als mensen teams verplaatsen of het bedrijf verlaten, hoe zorg je ervoor dat hun oude toegang wordt verwijderd, vooral als het gaat om gasten?
- Buitensporige toegangsrechten kunnen leiden tot controlebevindingen en compromissen, omdat deze wijzen op een gebrek aan controle over de toegang.
- Je moet proactief in contact komen met resource-eigenaren om ervoor te zorgen dat ze regelmatig controleren wie toegang heeft tot hun bronnen.

## <a name="when-to-use-access-reviews"></a>Wanneer gebruik je toegangsbeoordelingen?

- **Te veel gebruikers in bevoorrechte rollen:** Het is een goed idee om te controleren hoeveel gebruikers beheerderstoegang hebben, hoeveel van hen globale beheerders zijn en of er genodigden of partners zijn die niet zijn verwijderd nadat ze zijn toegewezen om een administratieve taak uit te voeren. U de gebruikers van de roltoewijzing in [Azure AD-rollen,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) zoals globale beheerders, of [Azure-resourcesrollen](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) zoals User Access Administrator, opnieuw certificeren in de [PIM-ervaring (Azure AD Privileged Identity Management).](../privileged-identity-management/pim-configure.md)
- **Wanneer automatisering onhaalbaar is:** U regels maken voor dynamisch lidmaatschap voor beveiligingsgroepen of Office 365-groepen, maar wat als de HR-gegevens zich niet in Azure AD bevinden of als gebruikers nog steeds toegang nodig hebben nadat ze de groep hebben verlaten om hun vervanging te trainen? U vervolgens een beoordeling voor die groep maken om ervoor te zorgen dat degenen die nog toegang nodig hebben, toegang moeten hebben.
- **Wanneer een groep wordt gebruikt voor een nieuw doel:** Als u een groep hebt die wordt gesynchroniseerd met Azure AD of als u van plan bent de toepassing Salesforce in te schakelen voor iedereen in de teamgroep Verkoop, is het handig om de groepseigenaar te vragen het groepslidmaatschap te controleren voordat de groep wordt gebruikt in een andere risico-inhoud.
- **Toegang tot bedrijfskritieke gegevens:** voor bepaalde bronnen kan het nodig zijn om mensen buiten de IT te vragen zich regelmatig af te melden en een rechtvaardiging te geven waarom ze toegang nodig hebben voor controledoeleinden.
- **Ga als lid van het nieuwe beleid:** In een ideale wereld volgen alle gebruikers het toegangsbeleid om de toegang tot de bronnen van uw organisatie te beveiligen. Soms zijn er echter business cases waarvoor u uitzonderingen moet maken. Als IT-beheerder u deze taak beheren, toezicht op beleidsuitzonderingen vermijden en auditors het bewijs leveren dat deze uitzonderingen regelmatig worden beoordeeld.
- **Vraag groepseigenaren om te bevestigen dat ze nog steeds gasten in hun groepen nodig hebben:** Toegang tot werknemers kan worden geautomatiseerd met sommige op het terrein IAM, maar niet genodigden. Als een groep gasten toegang geeft tot bedrijfsgevoelige inhoud, is het de verantwoordelijkheid van de eigenaar van de groep om te bevestigen dat de gasten nog steeds een legitieme zakelijke behoefte aan toegang hebben.
- **Laat beoordelingen regelmatig terugkeren:** U terugkerende toegangsbeoordelingen van gebruikers instellen op ingestelde frequenties, zoals wekelijks, maandelijks, driemaandelijks of jaarlijks, en de beoordelaars worden aan het begin van elke beoordeling op de hoogte gebracht. Revisoren kunnen toegang goedkeuren of weigeren met behulp van een vriendelijke interface en met behulp van slimme aanbevelingen.

## <a name="where-do-you-create-reviews"></a>Waar maak je reviews?

Afhankelijk van wat u wilt controleren, maakt u uw toegangscontrole in Azure AD-toegangsbeoordelingen, Azure AD Enterprise-apps (in preview) of Azure AD PIM.

| Toegangsrechten van gebruikers | Revisoren kunnen | Beoordeling gemaakt in | Ervaring met reviewers |
| --- | --- | --- | --- |
| Leden van beveiligingsgroepen</br>Leden van de Office-groep | Opgegeven revisoren</br>Groepseigenaren</br>Zelfbeoordeling | Azure AD-toegangsbeoordelingen</br>Azure AD-groepen | Toegangsvenster |
| Toegewezen aan een verbonden app | Opgegeven revisoren</br>Zelfbeoordeling | Azure AD-toegangsbeoordelingen</br>Azure AD enterprise-apps (in preview) | Toegangsvenster |
| Azure AD-rol | Opgegeven revisoren</br>Zelfbeoordeling | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-bronrol | Opgegeven revisoren</br>Zelfbeoordeling | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |


## <a name="create-access-reviews"></a>Toegangsbeoordelingen maken

Voer de volgende stappen uit om een toegangsbeoordeling te maken:

1. Ga naar de [Azure-portal](https://portal.azure.com) om toegangsbeoordelingen te beheren en meld u aan als globale beheerder of gebruikersbeheerder.

1. Zoek naar **Azure Active Directory** en selecteer deze optie.

      ![Azure-portal zoeken naar Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Selecteer **Identiteitsbeheer**.

1. Klik op de pagina Aan de slag op de knop **Een toegangscontrole maken.**

   ![Startpagina voor beoordelingen openen](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Meer informatie over toegangsbeoordelingen

Bekijk deze korte demo voor meer informatie over het maken en uitvoeren van toegangsrecensies:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Als u toegangsbeoordelingen in uw organisatie wilt implementeren, volgt u deze stappen in de video om uw beheerders aan boord te zetten, uw beheerders te trainen en uw eerste toegangsbeoordeling te maken!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hoeveel licenties moet u hebben?

Controleer of uw directory minstens evenveel Azure AD Premium P2-licenties heeft als werknemers die de volgende taken uitvoeren:

- Leden- en gastgebruikers die zijn toegewezen als revisoren
- Leden en gastgebruikers die een zelfbeoordeling uitvoeren
- Groepseigenaren die een toegangscontrole uitvoeren
- Toepassingseigenaren die een toegangscontrole uitvoeren

Azure AD Premium **P2-licenties** zijn niet vereist voor de volgende taken:

- Er zijn geen licenties vereist voor de gebruikers met de rollen Globale beheerder of gebruikersbeheerder die toegangsbeoordelingen instellen, instellingen configureren of de beslissingen uit de beoordelingen toepassen.

Voor elke betaalde Azure AD Premium P2-licentie die u aan een van de gebruikers van uw eigen organisatie toewijst, u B2B (Azure AD Business-to-business) gebruiken om maximaal vijf gastgebruikers uit te nodigen onder de externe gebruikerstoelage. Deze gastgebruikers kunnen ook Azure AD Premium P2-functies gebruiken. Zie [Azure AD B2B-samenwerkingslicentierichtlijnen](../b2b/licensing-guidance.md)voor meer informatie.

Zie [Licenties toewijzen of verwijderen met de Azure Active Directory-portal](../fundamentals/license-users-groups.md)voor meer informatie over licenties.

### <a name="example-license-scenarios"></a>Voorbeeldlicentiescenario's

Hier volgen enkele voorbeeldlicentiescenario's waarmee u bepalen hoeveel licenties u moet hebben.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Een beheerder maakt een toegangsbeoordeling van groep A met 75 gebruikers en 1 groepseigenaar en wijst de eigenaar van de groep toe als de revisor. | 1 licentie voor de groepseigenaar als revisor | 1 |
| Een beheerder maakt een toegangsbeoordeling van groep B met 500 gebruikers en 3 groepseigenaren en wijst de 3 groepseigenaren toe als revisoren. | 3 licenties voor elke groepseigenaar als revisoren | 3 |
| Een beheerder maakt een toegangscontrole van groep B met 500 gebruikers. Maakt het een zelf-review. | 500 licenties voor elke gebruiker als zelfbeoordelaars | 500 |
| Een beheerder maakt een toegangscontrole van groep C met 50 leden gebruikers en 25 gastgebruikers. Maakt het een zelf-review. | 50 licenties voor elke gebruiker als zelfbeoordelaars.<br/>(gastgebruikers zijn gedekt in de vereiste 1:5 verhouding) | 50 |
| Een beheerder maakt een toegangscontrole van groep D met 6 leden gebruikers en 108 gastgebruikers. Maakt het een zelf-review. | 6 licenties voor elke gebruiker als zelfbeoordelaars + 16 extra licenties voor alle 108 gastgebruikers in de vereiste 1:5-verhouding. 6 licenties, die\*betrekking hebben op 6 5 = 30 gastgebruikers. Voor de overige (108-6\*5)=78 gastgebruikers zijn 78/5=16 extra licenties vereist. In totaal zijn dus 6+16=22 licenties vereist. | 22 |

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Een toegangsbeoordeling maken van gebruikers met een Azure AD-beheerderrol](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Toegang tot groepen of toepassingen controleren](perform-access-review.md)
- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
