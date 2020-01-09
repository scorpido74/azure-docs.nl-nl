---
title: Gebruikers beheren die zijn uitgesloten van beleid voor voorwaardelijke toegang-Azure AD
description: Meer informatie over het gebruik van Azure Active Directory (Azure AD) toegangs Beoordelingen voor het beheren van gebruikers die zijn uitgesloten van het beleid voor voorwaardelijke toegang
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422715"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Azure AD-toegangs beoordelingen gebruiken voor het beheren van gebruikers die zijn uitgesloten van het beleid voor voorwaardelijke toegang

In een ideale wereld volgen alle gebruikers het toegangs beleid om de toegang tot de resources van uw organisatie te beveiligen. Soms zijn er echter zakelijke cases waarvoor u uitzonde ringen moet maken. In dit artikel worden enkele voor beelden beschreven waarin uitsluitingen mogelijk vereist zijn en hoe u, als IT-beheerder, deze taak kan beheren, het toezicht op beleids uitzonderingen te voor komen en Audi tors te voorzien van een bewijs dat deze uitzonde ringen regel matig worden gecontroleerd met behulp van Azure Toegangs beoordelingen van Active Directory (Azure AD).

> [!NOTE]
> Een geldige Azure AD Premium P2, Enterprise Mobility + Security E5 betaalde of een proef licentie is vereist voor het gebruik van Azure AD-toegangs Beoordelingen. Zie [Azure Active Directory editions](../fundamentals/active-directory-whatis.md) (Engelstalig) voor meer informatie.

## <a name="why-would-you-exclude-users-from-policies"></a>Waarom zou u gebruikers uitsluiten van beleid?

Als IT-beheerder kunt u [voorwaardelijke toegang van Azure AD](../conditional-access/overview.md) gebruiken om gebruikers te verplichten te verifiëren met multi-factor Authentication (MFA) of zich aan te melden vanaf een vertrouwd netwerk of apparaat. Tijdens de implementatie planning vindt u dat aan sommige van deze vereisten niet kan worden voldaan door alle gebruikers. Er zijn bijvoorbeeld gebruikers die werken vanaf een extern kantoor dat geen deel uitmaakt van uw interne netwerk of er is een Executive die gebruikmaakt van een oude telefoon die niet wordt ondersteund. Het bedrijf vereist dat deze gebruikers zich kunnen aanmelden en hun taak kunnen uitvoeren, daarom worden ze uitgesloten van het beleid voor voorwaardelijke toegang.

Een ander voor beeld is het mogelijk om [benoemde locaties](../conditional-access/location-condition.md) in voorwaardelijke toegang te gebruiken om een set met tellingen en regio's te configureren waarvan u niet wilt dat gebruikers toegang hebben tot hun Tenant.

![Benoemde locaties in voorwaardelijke toegang](./media/conditional-access-exclusion/named-locations.png)

In sommige gevallen kunnen gebruikers echter een rechtmatige reden hebben om zich aan te melden bij deze geblokkeerde landen/regio's. Gebruikers kunnen bijvoorbeeld op reis zijn voor werk of persoonlijke redenen. In dit voor beeld kan het beleid voor voorwaardelijke toegang om deze landen/regio's te blok keren, een specifieke Cloud beveiligings groep hebben voor de gebruikers die zijn uitgesloten van het beleid. Gebruikers die toegang nodig hebben tijdens het reizen, kunnen zichzelf toevoegen aan de groep met [Azure AD self-service groeps beheer](../users-groups-roles/groups-self-service-management.md).

Een ander voor beeld is mogelijk dat u een beleid voor voorwaardelijke toegang hebt waarmee [verouderde verificatie voor de meeste gebruikers wordt geblokkeerd](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Micro soft raadt u ten zeerste aan het gebruik van verouderde protocollen in uw Tenant te blok keren om uw beveiligings postuur te verbeteren. Als u echter een aantal gebruikers hebt die absoluut gebruikmaken van verouderde verificatie methoden om toegang te krijgen tot uw resources via Office 2010-of IMAP/SMTP/POP-clients, kunt u deze gebruikers uitsluiten van het beleid waarmee verouderde verificatie methoden worden geblokkeerd.

## <a name="why-are-exclusions-challenging"></a>Waarom zijn uitsluitingen lastig?

In azure AD kunt u een beleid voor voorwaardelijke toegang bereiken voor een groep gebruikers. U kunt ook een aantal van deze gebruikers uitsluiten door Azure AD-rollen, afzonderlijke gebruikers of gasten van gebruikers te selecteren. Het is belang rijk te weten dat wanneer deze uitsluitingen zijn geconfigureerd, de beleids intentie niet kan worden afgedwongen voor deze gebruikers. Als deze uitzonde ringen zijn geconfigureerd als een lijst met afzonderlijke gebruikers of via een verouderde on-premises beveiligings groep, wordt de zicht baarheid van deze uitsluitings lijst beperkt (gebruikers kunnen niet weten dat ze het bestaan) en de controle van de IT-beheerder. gebruikers kunnen lid worden van de beveiligings groep om het beleid door te geven). Daarnaast is het mogelijk dat gebruikers die de uitsluiting in één keer hebben gekwalificeerd, deze niet meer nodig hebben of niet langer in aanmerking komen.

Aan het begin van een uitsluiting bevindt zich een korte lijst met gebruikers die het beleid niet gebruiken. In de loop van de tijd worden er meer gebruikers uitgesloten en wordt de lijst verg root. Op een bepaald moment moet u de lijst controleren en bevestigen dat elk van deze gebruikers nog steeds moet worden uitgesloten. Het beheren van de lijst vanuit een technisch oogpunt kan relatief eenvoudig zijn, maar wie neemt de zakelijke beslissingen en hoe weet u zeker dat u deze allemaal kunt controleren?

Als u de uitsluiting echter configureert voor het beleid voor voorwaardelijke toegang met behulp van een Azure AD-groep, kunt u toegangs beoordelingen gebruiken als compenserende controle, de zicht baarheid van het apparaat beperken en het aantal gebruikers verminderen dat een uitzonde ring heeft.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Een uitsluitings groep maken in een beleid voor voorwaardelijke toegang

Volg deze stappen om een nieuwe Azure AD-groep en een beleid voor voorwaardelijke toegang te maken dat niet van toepassing is op die groep.

### <a name="create-an-exclusion-group"></a>Een uitsluitings groep maken

1. Meld u aan bij Azure Portal.

1. Klik in de linkernavigatiebalk op **Azure Active Directory** en klik vervolgens op **groepen**.

1. Klik in het bovenste menu op **nieuwe groep** om het deel venster groep te openen.

1. Selecteer in de lijst **groeps type** de optie **beveiliging**. Geef een naam en beschrijving op.

1. Zorg ervoor dat u het **lidmaatschaps** type instelt op **toegewezen**.

1. Selecteer de gebruikers die deel moeten uitmaken van deze uitsluitings groep en klik vervolgens op **maken**.

    ![Het deel venster nieuwe groep in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Een beleid voor voorwaardelijke toegang maken waarmee de groep wordt uitgesloten

U kunt nu een beleid voor voorwaardelijke toegang maken waarin deze uitsluitings groep wordt gebruikt.

1. Klik in de linkernavigatiebalk op **Azure Active Directory** en klik vervolgens op **voorwaardelijke toegang** om de Blade **beleid** te openen.

1. Klik op **Nieuw beleid** om het **nieuwe** deel venster te openen.

1. Geef een naam op.

1. Klik onder toewijzingen op **gebruikers en groepen**.

1. Selecteer **alle gebruikers**op het tabblad **includes** .

1. Voeg op het tabblad **uitsluiten** een vinkje toe aan **gebruikers en groepen** en klik vervolgens op **uitgesloten gebruikers selecteren**.

1. Selecteer de uitsluitings groep die u hebt gemaakt.

    > [!NOTE]
    > Als best practice wordt u aangeraden ten minste één beheerders account uit te sluiten van het beleid bij het testen om er zeker van te zijn dat u niet bent uitgesloten van uw Tenant.

1. Ga verder met het instellen van het beleid voor voorwaardelijke toegang op basis van de vereisten van uw organisatie.

    ![Deel venster uitgesloten gebruikers selecteren in voorwaardelijke toegang](./media/conditional-access-exclusion/select-excluded-users.png)

Laten we twee voor beelden gebruiken waarmee u uitsluitingen kunt beheren in beleids regels voor voorwaardelijke toegang.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Voor beeld 1: toegangs beoordeling voor gebruikers die toegang hebben tot geblokkeerde landen/regio's

Stel dat u een beleid voor voorwaardelijke toegang hebt waarmee de toegang vanuit bepaalde landen/regio's wordt geblokkeerd. Het bevat een groep die niet is uitgesloten van het beleid. Hier volgt een aanbevolen toegangs beoordeling waar leden van de groep worden gecontroleerd.

> [!NOTE]
> Er is een globale beheerder of beheerderrol vereist voor het maken van toegangs Beoordelingen.

1. De beoordeling wordt elke week opnieuw uitgevoerd.

2. Eindigt nooit om ervoor te zorgen dat u deze uitsluitings groep het meest up-to-date blijft.

3. Alle leden van deze groep worden binnen het bereik van de beoordeling weer geven.

4. Elke gebruiker moet zelf bevestigen dat ze nog steeds toegang moeten hebben tot deze geblokkeerde landen/regio's. Daarom moeten ze nog steeds lid zijn van de groep.

5. Als de gebruiker niet reageert op de controle aanvraag, wordt deze automatisch uit de groep verwijderd en kan daarom geen toegang meer krijgen tot de Tenant tijdens het reizen naar deze landen/regio's.

6. Schakel e-mail meldingen in zodat gebruikers op de hoogte worden gebracht van het starten en volt ooien van de toegangs beoordeling.

    ![Een toegangs beoordelings venster maken bijvoorbeeld 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Voor beeld 2: toegangs beoordeling voor gebruikers die toegang hebben tot verouderde verificatie

Stel dat u een beleid voor voorwaardelijke toegang hebt waarmee de toegang wordt geblokkeerd voor gebruikers die gebruikmaken van verouderde verificatie en oudere client versies. Het bevat een groep die niet is uitgesloten van het beleid. Hier volgt een aanbevolen toegangs beoordeling waar leden van de groep worden gecontroleerd.

1. Deze beoordeling moet een terugkerende beoordeling zijn.

2. Iedereen in de groep moet worden gecontroleerd.

3. Het kan worden geconfigureerd om de eigen aren van Business Units als de geselecteerde revisoren weer te geven.

4. De resultaten automatisch Toep assen en gebruikers verwijderen die niet zijn goedgekeurd om door te gaan met het gebruik van verouderde verificatie methoden.

5. Het kan nuttig zijn om aanbevelingen in te scha kelen zodat revisoren van grote groepen hun beslissingen eenvoudig kunnen maken.

6. Schakel e-mail meldingen in zodat gebruikers op de hoogte worden gebracht van het starten en volt ooien van de toegangs beoordeling.

    ![Een toegangs beoordelings venster maken bijvoorbeeld 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro-Tip**: als u veel uitsluitings groepen hebt en daarom meerdere toegangs beoordelingen moet maken, hebben we nu een API in het Microsoft Graph bèta-eind punt waarmee u ze programmatisch kunt maken en beheren. Als u aan de slag wilt gaan, raadpleegt u de [Azure AD Access beoordelingen API-referentie](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) en [voor beeld van het ophalen van Azure AD-toegangs beoordelingen via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultaten en audit logboeken voor toegang controleren

Nu u alles hebt geïmplementeerd, groep, voorwaardelijk toegangs beleid en toegangs beoordelingen, is het tijd om de resultaten van deze beoordelingen te controleren en bij te houden.

1. Open de Blade **toegangs beoordelingen** In het Azure Portal.

1. Open het besturings element en programma dat u hebt gemaakt voor het beheren van de uitsluitings groep.

1. Klik op **resultaten** om te zien wie is goedgekeurd om in de lijst te blijven en die is verwijderd.

    ![Resultaten van toegangs beoordelingen geven aan wie is goedgekeurd](./media/conditional-access-exclusion/access-reviews-results.png)

1. Klik vervolgens op **audit logboeken** om de acties weer te geven die zijn uitgevoerd tijdens deze beoordeling.

    ![Toegangs beoordelingen controle logboeken acties weer geven](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Als IT-beheerder weet u dat het beheren van uitsluitings groepen in uw beleid soms onvermijdelijk is. Door deze groepen echter te onderhouden, worden ze regel matig door de eigenaar van het bedrijf of gebruikers zelf bekeken en kunnen deze wijzigingen gemakkelijker worden gemaakt met Azure AD-toegangs Beoordelingen.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling van groepen of toepassingen maken](create-access-review.md)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md)
