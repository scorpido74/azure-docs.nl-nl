---
title: Wat zijn toegangsbeoordelingen? - Azure Active Directory | Microsoft Azure
description: Met Azure Active Directory-toegangs beoordelingen kunt u groepslid maatschap en de toegang tot toepassingen beheren om te voldoen aan governance-, risico beheer en nalevings initiatieven in uw organisatie.
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
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d9922f1c4cbb0afca74c911d9b2bc9f0eab0714
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422786"
---
# <a name="what-are-azure-ad-access-reviews"></a>Wat zijn toegangsbeoordelingen in Azure AD?

Met toegangs beoordelingen van Azure Active Directory (Azure AD) kunnen organisaties op efficiënte wijze groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste personen toegang hebben.

Hier volgt een video met een beknopt overzicht van toegangs beoordelingen:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Waarom zijn toegangs beoordelingen belang rijk?

Met Azure AD kunt u intern samen werken binnen uw organisatie en met gebruikers van externe organisaties, zoals partners. Gebruikers kunnen lid worden van groepen, gasten uitnodigen, verbinding maken met Cloud-apps en op afstand werken vanaf hun werk-of persoonlijke apparaten. Het gebruik van de kracht van self-service heeft ertoe geleid dat er betere toegangs beheer mogelijkheden nodig zijn.

- Hoe zorgt u ervoor dat nieuwe mede werkers de juiste toegang hebben om productief te zijn?
- Hoe zorgt u ervoor dat de oude toegang wordt verwijderd, met name wanneer gasten een gast worden?
- Overmatige toegangs rechten kunnen leiden tot controle van de bevindingen en inbreuken op een gebrek aan controle over toegang.
- U moet proactief samen werken met resource-eigen aren om ervoor te zorgen dat ze regel matig controleren wie toegang heeft tot hun resources.

## <a name="when-to-use-access-reviews"></a>Wanneer moet u toegangs beoordelingen gebruiken?

- **Te veel gebruikers in bevoorrechte rollen:** Het is een goed idee om te controleren hoeveel gebruikers beheerders toegang hebben, hoeveel ze globale beheerders zijn, en of er uitgenodigde gasten of partners zijn die niet zijn verwijderd na de toewijzing van een beheer taak. U kunt de roltoewijzings gebruikers opnieuw certificeren in [Azure AD-rollen](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) zoals globale beheerders of Azure- [resources](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , zoals gebruikers toegangs beheerder in de ervaring [Azure AD privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Wanneer Automation niet haalbaar is:** U kunt regels maken voor een dynamisch lidmaatschap van beveiligings groepen of Office 365-groepen, maar wat als de HR-gegevens zich niet in azure AD bevindt of als gebruikers nog steeds toegang nodig hebben nadat de groep de vervanging heeft verlaten? U kunt vervolgens een beoordeling maken voor die groep om ervoor te zorgen dat gebruikers die nog toegang nodig hebben, de toegang moeten blijven gebruiken.
- **Wanneer een groep wordt gebruikt voor een nieuw doel:** Als u een groep hebt die wordt gesynchroniseerd met Azure AD of als u van plan bent de toepassings Sales Force in te scha kelen voor iedereen in de groep verkoop team, is het handig om de groeps eigenaar te vragen het groepslid maatschap te controleren voordat de groep wordt gebruikt in een andere risico inhoud.
- **Essentiële Bedrijfs gegevens toegang:** voor bepaalde bronnen kan het nodig zijn om gebruikers buiten het systeem te vragen om zich regel matig af te melden en een motivering te geven over de redenen waarom ze toegang nodig hebben voor controle doeleinden.
- **De uitzonderings lijst van een beleid onderhouden:** In een ideale wereld volgen alle gebruikers het toegangs beleid om de toegang tot de resources van uw organisatie te beveiligen. Soms zijn er echter zakelijke cases waarvoor u uitzonde ringen moet maken. Als IT-beheerder kunt u deze taak beheren, het toezicht op beleids uitzonderingen voor komen en Audi tors voorzien van een bewijs dat deze uitzonde ringen regel matig worden gecontroleerd.
- **Groeps eigenaren vragen om te bevestigen dat ze nog gasten in hun groepen nodig hebben:** De toegang van werk nemers kan worden geautomatiseerd met een aantal on-premises IAM, maar niet de uitgenodigd gasten. Als een groep gasten toegang geeft tot zakelijke gevoelige inhoud, is het de verantwoordelijkheid van de groeps eigenaar om te bevestigen dat de gasten nog steeds een legitieme zakelijke nood zaak voor toegang hebben.
- **Regel matig terugkerende beoordelingen:** U kunt terugkerende toegangs beoordelingen van gebruikers instellen op set-frequenties zoals wekelijks, maandelijks, elk kwar taal of jaarlijks en de controleurs worden aan het begin van elke beoordeling op de hoogte gebracht. Revisoren kunnen toegang goed keuren of weigeren met een beschrijvende interface en met behulp van slimme aanbevelingen.

## <a name="where-do-you-create-reviews"></a>Waar maakt u beoordelingen?

Afhankelijk van wat u wilt controleren, maakt u uw toegangs beoordeling in azure AD-toegangs beoordelingen, Azure AD Enter prise-apps (in Preview) of Azure AD PIM.

| Toegangs rechten van gebruikers | Revisoren kunnen worden | Beoordeling gemaakt in | Ervaring voor revisoren |
| --- | --- | --- | --- |
| Leden van beveiligings groep</br>Office-groeps leden | Opgegeven revisoren</br>Groeps eigenaren</br>Zelf beoordeling | Azure AD-toegangsbeoordelingen</br>Azure AD-groepen | Toegangsvenster |
| Toegewezen aan een verbonden app | Opgegeven revisoren</br>Zelf beoordeling | Azure AD-toegangsbeoordelingen</br>Azure AD Enter prise-apps (in preview-versie) | Toegangsvenster |
| Azure AD-rol | Opgegeven revisoren</br>Zelf beoordeling | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-resource functie | Opgegeven revisoren</br>Zelf beoordeling | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |


## <a name="onboard-access-reviews"></a>Toegangs Beoordelingen voor onboarding

Volg deze stappen om toegangs beoordelingen uit te voeren.

1. Ga naar de [Azure Portal](https://portal.azure.com) om toegangs beoordelingen te beheren en u aan te melden als een globale beheerder of gebruikers beheerder.

1. Zoek en selecteer **Azure Active Directory**.

      ![Azure Portal zoeken naar Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Selecteer **Identity governance**.

1. Klik op **toegangs beoordelingen**.
 
    ![Start pagina toegangs beoordelingen](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. Klik op de pagina op de knop **nu Onboarden** .
    
      ![Toegangs Beoordelingen voor onboarding](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>Meer informatie over toegangs beoordelingen

Bekijk deze korte demo voor meer informatie over het maken en uitvoeren van toegangs beoordelingen:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Als u klaar bent voor het implementeren van toegangs beoordelingen in uw organisatie, volgt u deze stappen in de video om de onboarding uit te voeren, uw beheerders te trainen en uw eerste toegangs beoordeling te maken.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>Welke gebruikers moeten licenties hebben?

Elke gebruiker die communiceert met toegangs beoordelingen moet beschikken over een betaalde Azure AD Premium P2-licentie. Voorbeelden:

- Beheerders die een toegangs beoordeling maken
- Groeps eigenaren die een toegangs beoordeling uitvoeren
- Gebruikers die zijn toegewezen als revisoren
- Gebruikers die zelf een beoordeling uitvoeren

U kunt ook gast gebruikers vragen hun eigen toegang te controleren. Voor elke betaalde Azure AD Premium P2-licentie die u toewijst aan een van de gebruikers van uw eigen organisatie, kunt u Azure AD Business-to-Business (B2B) gebruiken om Maxi maal vijf gast gebruikers uit te nodigen onder de limiet voor externe gebruikers. Deze gast gebruikers kunnen ook gebruikmaken van Azure AD Premium P2-functies. Zie [licentie richtlijnen voor Azure AD B2B-samen werking](../b2b/licensing-guidance.md)voor meer informatie.

Hier volgen enkele voorbeeld scenario's die u kunnen helpen bij het bepalen van het aantal licenties dat u nodig hebt.

| Scenario | Berekening | Vereist aantal licenties |
| --- | --- | --- |
| Een beheerder maakt een toegangs beoordeling van groep A met 500 gebruikers. Wijst drie groeps eigenaren als revisoren toe. | 1 licentie voor de beheerder en 3 licenties voor elke groeps eigenaar als controleurs. | 4 |
| Een beheerder maakt een toegangs beoordeling van groep A met 500 gebruikers. Maakt het een zelf beoordeling. | 1 licentie voor de beheerder + 500 licenties voor elke gebruiker als eigen revisors. | 501 |
| Een beheerder maakt een toegangs beoordeling van groep B met vijf gebruikers en 25 gast gebruikers. Maakt het een zelf beoordeling. | 1 licentie voor de beheerder + 5 licenties voor elke gebruiker als eigen revisors.<br/>(gast gebruikers worden gedekt in de vereiste verhouding van 1:5) | 6 |
| Een beheerder maakt een toegangs beoordeling van groep C met 5 gebruikers en 108 gast gebruikers. Maakt het een zelf beoordeling. | 1 licentie voor de beheerder + 5 licenties voor elke gebruiker als eigen revisors + 16 extra licenties voor alle 108 gast gebruikers in de vereiste 1:5 verhouding.<br/>1 + 5 = 6 licenties, die 5\*6 = 30 gast gebruikers bedekking. Voor de resterende (108-5\*6) = 78 gast gebruikers, 78/5 = 16 extra licenties zijn vereist. In totaal moeten 6 + 16 = 22 licenties zijn vereist. | 22 |

Zie [licenties toewijzen of verwijderen met behulp van de Azure Active Directory Portal](../fundamentals/license-users-groups.md)voor informatie over het toewijzen van licenties aan uw gebruik.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)
- [Een toegangsbeoordeling maken van gebruikers met een Azure AD-beheerderrol](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Toegang tot groepen of toepassingen controleren](perform-access-review.md)
- [Een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)
