---
title: Verouderde verificatie blok keren voor Azure Active Directory (Azure AD) met voorwaardelijke toegang | Microsoft Docs
description: Meer informatie over het verbeteren van uw beveiligings postuur door verouderde verificatie te blok keren met voorwaardelijke toegang van Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e4dc33d670c5f6c5ebefa21ccf1a1ff941e913
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024576"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedure: Verouderde verificatie naar Azure AD blok keren met voorwaardelijke toegang   

Om uw gebruikers eenvoudig toegang te geven tot uw Cloud-apps, ondersteunt Azure Active Directory (Azure AD) een breed scala aan verificatie protocollen, waaronder verouderde verificatie. Verouderde protocollen bieden echter geen ondersteuning voor multi-factor Authentication (MFA). MFA is in veel omgevingen een algemene vereiste om identiteits diefstal te verhelpen. 

Als uw omgeving klaar is om verouderde verificatie te blok keren om de beveiliging van uw Tenant te verbeteren, kunt u dit doel bereiken met voorwaardelijke toegang. In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang kunt configureren waarmee verouderde verificatie voor uw Tenant wordt geblokkeerd.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met: 

- De [basis concepten](overview.md) van voorwaardelijke toegang tot Azure AD 
- De [Aanbevolen procedures](best-practices.md) voor het configureren van beleid voor voorwaardelijke toegang in de Azure Portal

## <a name="scenario-description"></a>Scenariobeschrijving

Azure AD biedt ondersteuning voor een aantal van de meest gebruikte verificatie-en autorisatie protocollen, inclusief verouderde verificatie. Verouderde verificatie verwijst naar protocollen die gebruikmaken van basis verificatie. Normaal gesp roken kunnen deze protocollen geen type verificatie van de tweede factor afdwingen. Voor beelden voor apps die zijn gebaseerd op verouderde verificatie zijn:

- Oudere Microsoft Office-apps
- Apps die e-mail protocollen zoals POP, IMAP en SMTP gebruiken

Verificatie met één factor (bijvoorbeeld gebruikers naam en wacht woord) is niet voldoende dagen. Wacht woorden zijn ongeldig omdat ze gemakkelijk te raden zijn en wij (mens) zijn slecht bij het kiezen van goede wacht woorden. Wacht woorden zijn ook kwetsbaar voor diverse aanvallen zoals phishing en wachtwoord spray. Een van de eenvoudigste dingen die u kunt doen om te beschermen tegen wachtwoord dreigingen is het implementeren van MFA. Met MFA, zelfs als een aanvaller in bezit is van het wacht woord van een gebruiker, is het wacht woord alleen niet voldoende om te verifiëren en toegang te krijgen tot de gegevens.

Hoe kunt u voor komen dat apps die verouderde verificatie gebruiken, toegang krijgen tot de resources van uw Tenant? De aanbeveling is om ze alleen te blok keren met een beleid voor voorwaardelijke toegang. Als dat nodig is, kunt u alleen bepaalde gebruikers en specifieke netwerk locaties gebruiken voor het gebruik van apps die zijn gebaseerd op verouderde verificatie.

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de eerste-factor Authentication is voltooid. Daarom is voorwaardelijke toegang niet bedoeld als een eerste verdedigings linie voor scenario's als denial-of-service-aanvallen, maar kunnen ook signalen van deze gebeurtenissen (bijvoorbeeld het risico niveau van de aanmelding, de locatie van de aanvraag enzovoort) worden gebruikt om de toegang te bepalen.

## <a name="implementation"></a>Implementatie

In deze sectie wordt uitgelegd hoe u een beleid voor voorwaardelijke toegang configureert om verouderde verificatie te blok keren. 

### <a name="identify-legacy-authentication-use"></a>Gebruik van verouderde authenticatie identificeren

Voordat u verouderde verificatie in uw Directory kunt blok keren, moet u eerst begrijpen of uw gebruikers apps hebben die gebruikmaken van verouderde verificatie en hoe dit van invloed is op uw algemene Directory. Aanmeld logboeken van Azure AD kunnen worden gebruikt om te begrijpen of u gebruikmaakt van verouderde verificatie.

1. Navigeer naar het **Azure Portal** > -**Azure Active Directory** > -**aanmeldingen**.
1. Voeg de kolom client-app toe als deze niet wordt weer gegeven door te klikken op de **kolommen** > **client-app**.
1. **Filters toevoegen** > **Client-App** > Selecteer alle opties voor **andere clients** en klik op **Toep assen**.

Bij filteren worden alleen de aanmeldings pogingen weer gegeven die zijn gemaakt door verouderde verificatie protocollen. Als u op elke afzonderlijke aanmeldings poging klikt, wordt er meer informatie weer gegeven. In het veld **client-app** onder het tabblad **basis informatie** wordt aangegeven welk verouderde verificatie protocol is gebruikt.

In deze logboeken wordt aangegeven welke gebruikers nog steeds afhankelijk zijn van verouderde verificatie en welke toepassingen verouderde protocollen gebruiken om verificatie aanvragen uit te voeren. Voor gebruikers die niet in deze logboeken worden weer gegeven en worden bevestigd dat ze geen verouderde authenticatie gebruiken, implementeert alleen het beleid voor voorwaardelijke toegang voor deze gebruikers.

### <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren 

In een beleid voor voorwaardelijke toegang kunt u een voor waarde instellen die is gekoppeld aan de client-apps die worden gebruikt voor toegang tot uw resources. Met de voor waarde voor client-apps kunt u het bereik beperken tot apps met behulp van verouderde verificatie door **andere clients** voor **mobiele apps en desktop-clients**te selecteren.

![Andere clients](./media/block-legacy-authentication/01.png)

Als u de toegang tot deze apps wilt blok keren, moet u **toegang blok keren**selecteren.

![Toegang blok keren](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Gebruikers en Cloud-apps selecteren

Als u verouderde verificatie voor uw organisatie wilt blok keren, denkt u waarschijnlijk dat u dit kunt doen door het volgende te selecteren:

- Alle gebruikers
- Alle Cloud-apps
- Toegang blok keren

![Toewijzingen](./media/block-legacy-authentication/03.png)

Azure heeft een veiligheids functie waarmee wordt voor komen dat u een beleid zoals dit kunt maken, omdat deze configuratie de [Aanbevolen procedures](best-practices.md) voor beleids regels voor voorwaardelijke toegang schendt.
 
![Beleids configuratie wordt niet ondersteund](./media/block-legacy-authentication/04.png)

De veiligheids functie is nood zakelijk omdat het *blok keren van alle gebruikers en alle Cloud-apps* de mogelijkheid heeft om uw hele organisatie te blok keren om zich aan te melden bij uw Tenant. U moet ten minste één gebruiker uitsluiten om te voldoen aan de minimale best practice vereiste. U kunt ook een directory-rol uitsluiten.

![Beleids configuratie wordt niet ondersteund](./media/block-legacy-authentication/05.png)

U kunt aan deze veiligheids functie voldoen door één gebruiker uit uw beleid uit te sluiten. In het ideale geval moet u enkele [beheerders accounts voor nood gevallen in azure AD](../users-groups-roles/directory-emergency-access.md) definiëren en deze uitsluiten van uw beleid.

## <a name="policy-deployment"></a>Implementatie van beleid

Voordat u uw beleid in productie neemt, moet u het volgende doen:
 
- **Service accounts** : Identificeer gebruikers accounts die worden gebruikt als service accounts of op apparaten, zoals telefoons in een Vergader Zaal. Zorg ervoor dat deze accounts sterke wacht woorden hebben en voeg ze toe aan een uitgesloten groep.
- **Aanmeldings rapporten** : Bekijk het aanmeldings rapport en zoek naar **ander client** verkeer. Identificeer het hoogste gebruik en onderzoek waarom het in gebruik is. Normaal gesp roken wordt het verkeer gegenereerd door oudere Office-clients die geen gebruik maken van moderne verificatie of door bepaalde e-mail-apps van derden. Maak een plan om het gebruik van deze apps te verplaatsen of als de impact laag is, Informeer uw gebruikers dat ze deze apps niet meer kunnen gebruiken.
 
Zie [Hoe moet u een nieuw beleid implementeren?](best-practices.md#how-should-you-deploy-a-new-policy)voor meer informatie.

## <a name="what-you-should-know"></a>Wat u moet weten

Het blok keren van toegang met **andere clients** blokkeert ook Exchange Online Power shell en Dynamics 365 met behulp van basis verificatie.

Het configureren van een beleid voor **andere clients** blokkeert de hele organisatie van bepaalde clients, zoals SPConnect. Dit blok treedt op omdat oudere clients op onverwachte wijze worden geverifieerd. Het probleem is niet van toepassing op grote Office-toepassingen zoals de oudere Office-clients.

Het kan tot 24 uur duren voordat het beleid van kracht wordt.

U kunt alle beschik bare granting-besturings elementen voor de andere voor waarden van de **clients** selecteren. de ervaring van de eind gebruiker is echter altijd dezelfde toegang die wordt geblokkeerd.

Als u verouderde verificatie blokkeert met de voor waarde **andere clients** , kunt u ook het platform en de locatie voorwaarde van het apparaat instellen. Als u bijvoorbeeld alleen verouderde verificatie voor mobiele apparaten wilt blok keren, stelt u de voor waarde **apparaat platformen** in door het volgende te selecteren:

- Android
- iOS
- Windows Phone

![Beleids configuratie wordt niet ondersteund](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Volgende stappen

- Als u nog niet bekend bent met het configureren van beleid voor voorwaardelijke toegang, raadpleegt u [MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory](app-based-mfa.md) voor een voor beeld.
- Zie voor meer informatie over ondersteuning voor moderne authenticatie [hoe moderne verificatie werkt voor office 2013-en office 2016-client-apps](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
