---
title: Gebruikers beheren die zijn uitgesloten van beleid voor voorwaardelijke toegang - Azure AD
description: Meer informatie over het gebruik van Azure Active Directory-toegangsbeoordelingen (Azure ACTIVE Directory) om gebruikers te beheren die zijn uitgesloten van beleid voor voorwaardelijke toegang
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
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422715"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Azure AD-toegangsbeoordelingen gebruiken om gebruikers te beheren die zijn uitgesloten van het beleid voor voorwaardelijke toegang

In een ideale wereld volgen alle gebruikers de toegangspolitie om toegang te krijgen tot de bronnen van uw organisatie. Soms zijn er echter business cases waarvoor u uitzonderingen moet maken. In dit artikel worden enkele voorbeelden beschreven waarin uitsluitingen vereist kunnen zijn en hoe u als IT-beheerder deze taak beheren, toezicht op beleidsuitzonderingen vermijden en auditors voorzien van het bewijs dat deze uitzonderingen regelmatig worden beoordeeld met Azure Active Directory (Azure AD) toegang tot beoordelingen.

> [!NOTE]
> Een geldige Azure AD Premium P2, Enterprise Mobility + Security E5 betaald of proeflicentie is vereist om Azure AD Access-toegangsbeoordelingen te gebruiken. Zie [Azure Active Directory-edities](../fundamentals/active-directory-whatis.md)voor meer informatie.

## <a name="why-would-you-exclude-users-from-policies"></a>Waarom zou u gebruikers uitsluiten van beleid?

Als IT-beheerder u [voorwaardelijke toegang tot Azure AD](../conditional-access/overview.md) gebruiken om gebruikers te verplichten zich te verifiëren met behulp van multi-factor authenticatie (MFA) of zich aan te melden vanaf een vertrouwd netwerk of apparaat. Tijdens de implementatieplanning komt u erachter dat niet aan sommige van deze vereisten kan worden voldaan door alle gebruikers. Er zijn bijvoorbeeld gebruikers die vanuit een extern kantoor werken dat geen deel uitmaakt van uw interne netwerk of er is een leidinggevende die een oude telefoon gebruikt die niet wordt ondersteund. Het bedrijf vereist dat deze gebruikers zich mogen aanmelden en hun werk mogen doen, daarom zijn ze uitgesloten van het beleid voor voorwaardelijke toegang.

Als een ander voorbeeld u [benoemde locaties](../conditional-access/location-condition.md) gebruiken in Voorwaardelijke toegang om een reeks provincies en regio's te configureren waarvan u gebruikers geen toegang wilt geven tot hun tenant.

![Benoemde locaties in Voorwaardelijke toegang](./media/conditional-access-exclusion/named-locations.png)

In sommige gevallen kunnen gebruikers echter een legitieme reden hebben om zich aan te melden vanuit deze geblokkeerde landen/regio's. Gebruikers kunnen bijvoorbeeld reizen om werk of persoonlijke redenen. In dit voorbeeld kan het beleid voor voorwaardelijke toegang om deze landen/regio's te blokkeren een speciale cloudbeveiligingsgroep hebben voor de gebruikers die zijn uitgesloten van het beleid. Gebruikers die toegang nodig hebben tijdens het reizen, kunnen zichzelf toevoegen aan de groep met [Azure AD selfservice group management](../users-groups-roles/groups-self-service-management.md).

Een ander voorbeeld is dat u een beleid voor voorwaardelijke toegang hebt dat [verouderde verificatie blokkeert voor de overgrote meerderheid van uw gebruikers.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) Microsoft raadt u ten zeerste aan het gebruik van verouderde protocollen in uw tenant te blokkeren om uw beveiligingshouding te verbeteren. Als u echter een aantal gebruikers hebt die absoluut verouderde verificatiemethoden moeten gebruiken om toegang te krijgen tot uw bronnen via Office 2010- of IMAP/SMTP/POP-gebaseerde clients, u deze gebruikers uitsluiten van het beleid dat verouderde verificatiemethoden blokkeert.

## <a name="why-are-exclusions-challenging"></a>Waarom zijn uitsluitingen uitdagend?

In Azure AD u een beleid voor voorwaardelijke toegang instellen op een set gebruikers. U sommige van deze gebruikers ook uitsluiten door Azure AD-rollen, individuele gebruikers of gasten van gebruikers te selecteren. Het is belangrijk om te onthouden dat wanneer deze uitsluitingen zijn geconfigureerd, de beleidsintentie niet kan worden afgedwongen voor die gebruikers. Als deze uitsluitingen zijn geconfigureerd als een lijst van individuele gebruikers of via een verouderde on-premises beveiligingsgroep, beperkt dit de zichtbaarheid van deze uitsluitingslijst (gebruikers weten mogelijk niet van het bestaan ervan) en de controle van de IT-beheerder erover (gebruikers kunnen lid worden van de beveiligingsgroep om het beleid te omzeilen). Bovendien hebben gebruikers die in één keer voor de uitsluiting in aanmerking kwamen, deze mogelijk niet meer nodig of komen ze daarvoor in aanmerking.

Aan het begin van een uitsluiting is er een korte lijst van gebruikers die het beleid omzeilen. Na verloop van tijd worden steeds meer gebruikers uitgesloten en groeit de lijst. Op een gegeven moment is het nodig om de lijst te bekijken en te bevestigen dat elk van deze gebruikers nog steeds moet worden uitgesloten. Het beheren van de lijst vanuit een technisch oogpunt, kan relatief eenvoudig zijn, maar wie maakt de zakelijke beslissingen en hoe zorg je ervoor dat het allemaal controleerbaar is?

Als u de uitsluiting echter configureert met het beleid voorwaardelijke toegang met een Azure AD-groep, u toegangsbeoordelingen gebruiken als compensatiebesturingselement, om de zichtbaarheid te vergroten en het aantal gebruikers met een uitzondering te verminderen.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Een uitsluitingsgroep maken in een beleid voor voorwaardelijke toegang

Volg deze stappen om een nieuwe Azure AD-groep en een beleid voor voorwaardelijke toegang te maken dat niet van toepassing is op die groep.

### <a name="create-an-exclusion-group"></a>Een uitsluitingsgroep maken

1. Meld u aan bij Azure Portal.

1. Klik in de linkernavigatie op **Azure Active Directory** en klik vervolgens op **Groepen**.

1. Klik in het bovenste menu op **Nieuwe groep** om het groepsvenster te openen.

1. Selecteer **Beveiliging**in de lijst **Groeptype** . Geef een naam en beschrijving op.

1. Zorg ervoor dat u het **type Lidmaatschap** instelt **op Toegewezen**.

1. Selecteer de gebruikers die deel moeten uitmaken van deze uitsluitingsgroep en klik op **Maken**.

    ![Nieuw groepsvenster in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Een beleid voor voorwaardelijke toegang maken dat de groep uitsluit

U nu een beleid voor voorwaardelijke toegang maken dat deze uitsluitingsgroep gebruikt.

1. Klik in de linkernavigatie op **Azure Active Directory** en klik vervolgens op Voorwaardelijke **toegang** om het **blad Beleid** te openen.

1. Klik **op Nieuw beleid** om het deelvenster **Nieuw** te openen.

1. Geef een naam op.

1. Klik onder Toewijzingen op **Gebruikers en groepen**.

1. Selecteer alle **gebruikers**op het tabblad **Opnemen** .

1. Voeg op het tabblad **Uitsluiten** een vinkje toe aan **gebruikers en groepen** en klik op Uitgesloten gebruikers **selecteren**.

1. Selecteer de uitsluitingsgroep die u hebt gemaakt.

    > [!NOTE]
    > Als aanbevolen aanbevolen aanbevolen om ten minste één beheerdersaccount uit te sluiten van het beleid bij het testen om ervoor te zorgen dat u niet buitengesloten bent van uw tenant.

1. Ga verder met het instellen van het beleid voor voorwaardelijke toegang op basis van uw organisatorische vereisten.

    ![Deelvenster Uitgesloten gebruikers selecteren in Voorwaardelijke toegang](./media/conditional-access-exclusion/select-excluded-users.png)

Laten we twee voorbeelden behandelen waarin u toegangsbeoordelingen gebruiken om uitsluitingen in beleid voor voorwaardelijke toegang te beheren.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Voorbeeld 1: Toegangscontrole voor gebruikers die toegang hebben vanuit geblokkeerde landen/regio's

Stel dat u een beleid voor voorwaardelijke toegang hebt dat de toegang van bepaalde landen/regio's blokkeert. Het omvat een groep die is uitgesloten van het beleid. Hier is een aanbevolen toegangscontrole waarbij leden van de groep worden beoordeeld.

> [!NOTE]
> Er is een globale beheerder- of gebruikersbeheerderrol vereist om toegangsbeoordelingen te maken.

1. De beoordeling zal elke week opnieuw plaatsvinden.

2. Zal nooit eindigen om ervoor te zorgen dat u deze uitsluitingsgroep het meest up-to-date houdt.

3. Alle leden van deze groep zullen in het kader van de herziening.

4. Elke gebruiker zal zelf moeten bevestigen dat hij nog steeds toegang moet hebben vanuit deze geblokkeerde landen/regio's, daarom moet hij nog steeds lid zijn van de groep.

5. Als de gebruiker niet reageert op het beoordelingsverzoek, wordt deze automatisch uit de groep verwijderd en heeft hij daarom geen toegang meer tot de tenant tijdens het reizen naar deze landen/regio's.

6. E-mailmeldingen inschakelen zodat gebruikers op de hoogte worden gesteld van het begin en de voltooiing van de toegangscontrole.

    ![Een toegangscontrolevenster maken, bijvoorbeeld 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Voorbeeld 2: Toegangscontrole voor gebruikers die toegang hebben tot verouderde verificatie

Stel dat u een beleid voor voorwaardelijke toegang hebt dat de toegang blokkeert voor gebruikers met oudere verificatie en oudere clientversies. Het omvat een groep die is uitgesloten van het beleid. Hier is een aanbevolen toegangscontrole waarbij leden van de groep worden beoordeeld.

1. Deze beoordeling zou een terugkerende beoordeling moeten zijn.

2. Iedereen in de groep zou moeten worden herzien.

3. Het kan worden geconfigureerd om de eigenaren van de business unit als de geselecteerde revisoren te vermelden.

4. Pas de resultaten automatisch toe en verwijder gebruikers die niet zijn goedgekeurd om oudere verificatiemethoden te blijven gebruiken.

5. Het kan nuttig zijn om aanbevelingen in te schakelen, zodat beoordelaars van grote groepen gemakkelijk hun beslissingen kunnen nemen.

6. E-mailmeldingen inschakelen zodat gebruikers op de hoogte worden gesteld van het begin en de voltooiing van de toegangscontrole.

    ![Een toegangscontrolevenster maken, bijvoorbeeld 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro-tip:** Als u veel uitsluitingsgroepen hebt en daarom meerdere toegangsbeoordelingen moet maken, hebben we nu een API in het bètaeindpunt van Microsoft Graph waarmee u ze programmatisch maken en beheren. Zie de [API-referentie voor Azure AD-toegangsbeoordelingen](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) en [Voorbeeld van het ophalen van Azure AD-toegangsbeoordelingen via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultaten van access review en controlelogboeken

Nu u alles op zijn plaats hebt, het beleid voor voorwaardelijke toegang en toegangsbeoordelingen hebt, is het tijd om de resultaten van deze beoordelingen te controleren en bij te houden.

1. Open in de Azure-portal het **access-recensiesblad.**

1. Open het besturingselement en het programma dat u hebt gemaakt voor het beheren van de uitsluitingsgroep.

1. Klik **op Resultaten** om te zien wie is goedgekeurd om op de lijst te blijven en wie is verwijderd.

    ![Resultaten van toegangsbeoordelingen geven aan wie is goedgekeurd](./media/conditional-access-exclusion/access-reviews-results.png)

1. Klik vervolgens op **Controlelogboeken** om de acties te zien die tijdens deze beoordeling zijn uitgevoerd.

    ![Acties met controlelogboeken voor access-beoordelingen](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Als IT-beheerder weet u dat het beheren van uitsluitingsgroepen naar uw beleid soms onvermijdelijk is. Het onderhouden van deze groepen, het regelmatig bekijken ervan door de bedrijfseigenaar of de gebruikers zelf, en het controleren van deze wijzigingen kan echter eenvoudiger worden met Azure AD-toegangsbeoordelingen.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangsbeoordeling van groepen of toepassingen maken](create-access-review.md)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
