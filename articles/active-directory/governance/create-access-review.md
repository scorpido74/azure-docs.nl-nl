---
title: Een toegangs beoordeling maken voor groepen & toepassingen-Azure AD
description: Meer informatie over het maken van een toegangs beoordeling van groeps leden of toegang tot toepassingen in Azure Active Directory toegangs Beoordelingen.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/06/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e769df186597a88247601f4fc70c48263edc88b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483904"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangs beoordeling maken voor groepen en toepassingen in azure AD-toegangs beoordelingen

De toegang tot groepen en toepassingen voor werk nemers en gasten verandert in de loop van de tijd. Beheerders kunnen Azure Active Directory (Azure AD) gebruiken voor het maken van toegangs Beoordelingen voor groeps leden of toegang tot toepassingen om het risico te verminderen dat is gekoppeld aan verouderde toegangs toewijzingen. Als u de toegang regel matig wilt controleren, kunt u ook terugkerende toegangs beoordelingen maken. Zie [gebruikers toegang beheren](manage-user-access-with-access-reviews.md) en [gast toegang beheren](manage-guest-access-with-access-reviews.md)voor meer informatie over deze scenario's.

In dit artikel wordt beschreven hoe u een of meer toegangs Beoordelingen voor groeps leden of toegang tot toepassingen maakt.

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder of gebruikers beheerder

Zie [licentie vereisten](access-reviews-overview.md#license-requirements)voor meer informatie.

## <a name="create-one-or-more-access-reviews"></a>Een of meer toegangs beoordelingen maken

1. Meld u aan bij de Azure Portal en open de [pagina voor identiteits](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)beheer.

1. Klik in het menu links op **toegangs beoordelingen**.

1. Klik op **nieuwe toegangs beoordeling** om een nieuwe toegangs beoordeling te maken.

    ![Deel venster toegangs beoordelingen in Identity governance](./media/create-access-review/access-reviews.png)

1. Geef de toegangs beoordeling een naam. U kunt eventueel een beschrijving van de beoordeling opgeven. De naam en beschrijving worden weer gegeven aan de controleurs.

    ![Een toegangs beoordeling maken-naam en beschrijving van de beoordeling](./media/create-access-review/name-description.png)

1. Stel de **begin datum**in. Een toegangs beoordeling vindt standaard plaats, start de tijd die wordt gemaakt en eindigt in één maand. U kunt de begin-en eind datum wijzigen zodat een toegangs beoordeling in de toekomst wordt gestart en het laatste aantal dagen dat u wilt.

    ![Een toegangs beoordeling maken-start-en eind datums](./media/create-access-review/start-end-dates.png)

1. Als u de toegangs beoordeling wilt herhalen, wijzigt u de **frequentie** -instelling van **één keer** in **wekelijks**, **maandelijks**, **per kwar taal**, **half jaar**of **per jaar**. Gebruik de schuif regelaar **duur** of het tekstvak om te bepalen hoeveel dagen elke beoordeling van de terugkerende serie wordt geopend voor de invoer van revisors. De maximale duur die u voor een maandelijkse beoordeling kunt instellen is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voor komen.

1. Gebruik de **eind** instelling om op te geven hoe de terugkerende toegangs beoordelings reeks moet worden beëindigd. De reeks kan op drie manieren eindigen: de serie wordt continu uitgevoerd om te beginnen met beoordelingen, tot een bepaalde datum, of nadat een gedefinieerd aantal exemplaren is voltooid. U, een andere gebruikers beheerder of een andere globale beheerder kunnen de serie na het maken stoppen door de datum in de **instellingen**te wijzigen, zodat deze op die datum eindigt.

1. Geef in de sectie **gebruikers** de gebruikers op waarop de toegangs beoordeling van toepassing is. Toegangs beoordelingen kunnen gelden voor de leden van een groep of voor gebruikers die zijn toegewezen aan een toepassing. U kunt de toegangs beoordeling verder beperken om alleen de gast gebruikers te bekijken die lid zijn van (of toegewezen aan de toepassing), in plaats van alle gebruikers te controleren die lid zijn van de app of toegang hebben tot de toepassing.

    ![Een toegangs beoordeling maken-gebruikers](./media/create-access-review/users.png)

1. Selecteer in de sectie **groep** een of meer groepen waarvan u het lidmaatschap wilt controleren.

    > [!NOTE]
    > Als u meer dan één groep selecteert, worden er meerdere toegangs beoordelingen gemaakt. Als u bijvoorbeeld vijf groepen selecteert, worden er vijf afzonderlijke toegangs beoordelingen gemaakt.
    
    ![Een toegangs beoordeling maken-groep selecteren](./media/create-access-review/select-group.png)

1. Selecteer in de sectie **toepassingen** (als u in stap 8 **toegewezen aan een toepassing** hebt geselecteerd) de toepassingen waarvoor u de toegang wilt controleren.

    > [!NOTE]
    > Als u meer dan één toepassing selecteert, worden er meerdere toegangs beoordelingen gemaakt. Als u bijvoorbeeld vijf toepassingen selecteert, worden er vijf afzonderlijke toegangs beoordelingen gemaakt.
    
    ![Een toegangs beoordeling maken-toepassing selecteren](./media/create-access-review/select-application.png)

1. Selecteer in de sectie **controleurs** een of meer personen om alle gebruikers in het bereik te controleren. Of u kunt ervoor kiezen om de leden hun eigen toegang te laten beoordelen. Als de resource een groep is, kunt u de groeps eigenaren vragen om te controleren. U kunt er ook voor zorgen dat de controleurs een reden opgeven wanneer ze de toegang goed keuren.

    ![Een toegangs beoordeling maken-revisoren](./media/create-access-review/reviewers.png)

1. Selecteer in de sectie **Program ma's** het programma dat u wilt gebruiken. **Standaard programma** is altijd aanwezig.

    ![Een toegangs beoordeling maken-Program Ma's](./media/create-access-review/programs.png)

    U kunt het bijhouden en verzamelen van toegangs Beoordelingen voor verschillende doel einden vereenvoudigen door ze in Program ma's te organiseren. Elke toegangs beoordeling kan worden gekoppeld aan een programma. Wanneer u vervolgens rapporten voorbereidt voor een auditor, kunt u zich richten op de toegangs beoordelingen binnen het bereik van een bepaald initiatief. Program ma's en toegangs beoordelings resultaten zijn zichtbaar voor gebruikers in de rol globale beheerder, gebruikers beheerder, beveiligings beheerder of beveiligings lezer.

    Als u een lijst met Program ma's wilt weer geven, gaat u naar de pagina toegangs beoordelingen en selecteert u **Program ma's**. Als u een globale beheerder of beheerderrol hebt, kunt u extra Program ma's maken. U kunt bijvoorbeeld kiezen voor een programma voor elk nalevings initiatief of zakelijk doel. Als u een programma niet meer nodig hebt en er geen besturings elementen meer aan zijn gekoppeld, kunt u deze verwijderen.

### <a name="upon-completion-settings"></a>Bij voltooiings instellingen

1. Als u wilt opgeven wat er gebeurt nadat een controle is voltooid, vouwt u de sectie **bij voltooiings instellingen** uit.

    ![Instellingen voor een toegangs beoordeling maken-bij voltooiing](./media/create-access-review/upon-completion-settings.png)

1. Als u automatisch de toegang wilt verwijderen voor gebruikers die zijn geweigerd, stelt u **automatisch Toep assen resultaten in op resource** om in te **scha kelen**. Als u de resultaten hand matig wilt Toep assen wanneer de controle is voltooid, stelt u de switch in op **uitschakelen**.

1. Gebruik de lijst als **revisor niet reageert** om op te geven wat er gebeurt voor gebruikers die niet worden gecontroleerd door de revisor binnen de beoordelings periode. Deze instelling heeft geen invloed op gebruikers die hand matig door de controleurs zijn gecontroleerd. Als de laatste beslissing van de revisor weigert, wordt de toegang van de gebruiker verwijderd.

    - **Geen wijziging** -gebruikers toegang ongewijzigd laten
    - **Toegang verwijderen** -de toegang van de gebruiker verwijderen
    - **Toegang goed keuren** : de toegang van de gebruiker goed keuren
    - **Aanbevelingen doen** : de aanbeveling van het systeem voor het weigeren of goed keuren van de permanente toegang van de gebruiker

### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Als u aanvullende instellingen wilt opgeven, vouwt u de sectie **Geavanceerde instellingen** uit.

    ![Een toegangs beoordeling maken-geavanceerde instellingen](./media/create-access-review/advanced-settings.png)

1. Stel **aanbevelingen weer geven** in om de controleurs **weer te geven** op basis van de toegangs gegevens van de gebruiker.

1. Stel een **reden in voor de goed keuring vereisen** om te **zorgen** dat de revisor een reden voor goed keuring moet opgeven.

1. Stel **e-mail meldingen** in om ervoor te **zorgen** dat Azure AD e-mail meldingen naar revisoren verzendt wanneer een toegangs beoordeling wordt gestart en aan beheerders wanneer een controle is voltooid.

1. Stel **herinneringen** in om ervoor te **zorgen** dat Azure AD herinneringen voor toegangs beoordelingen verzendt naar revisoren die hun beoordeling nog niet hebben voltooid.

    Standaard ontvangen de beoordelaars die nog niet hebben gereageerd halverwege de einddatum automatisch een herinnering van Azure AD.

## <a name="start-the-access-review"></a>De toegangs beoordeling starten

Zodra u de instellingen voor een toegangs beoordeling hebt opgegeven, klikt u op **Start**. De toegangs beoordeling wordt weer gegeven in de lijst met een indicator van de status.

![Lijst met toegangs beoordelingen en hun status](./media/create-access-review/access-reviews-list.png)

Standaard verzendt Azure AD een e-mail naar revisoren kort nadat de controle is gestart. Als u ervoor kiest om Azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangs beoordeling wacht totdat deze is voltooid. U kunt de instructies weer geven voor het controleren van de [toegang tot groepen of toepassingen](perform-access-review.md). Als u wilt controleren of gasten hun eigen toegang kunnen bekijken, geeft u de instructies weer voor het [controleren van de toegang tot groepen of toepassingen](review-your-access.md).

Als gasten als revisoren zijn toegewezen en ze de uitnodiging niet hebben geaccepteerd, ontvangen ze geen e-mail berichten van toegangs beoordelingen omdat ze de uitnodiging eerst moeten accepteren voordat ze kunnen beoordelen.

## <a name="access-review-status-table"></a>Status tabel toegangs beoordeling

| Status | Definitie |
|--------|------------|
|NotStarted | De controle is gemaakt, de gebruikers detectie wacht op starten. |
|Initialiseren   | Gebruikers detectie wordt uitgevoerd om alle gebruikers te identificeren die deel uitmaken van de beoordeling. |
|Starten | De beoordeling wordt gestart. Als e-mail meldingen zijn ingeschakeld, worden e-mails naar revisoren verzonden. |
|Wordt uitgevoerd | De beoordeling is gestart. Als e-mail meldingen zijn ingeschakeld, worden e-mails naar revisoren verzonden. Revisoren kunnen beslissingen verzenden tot de verval datum. |
|Invullen | De beoordeling wordt voltooid en e-mail berichten worden verzonden naar de eigenaar van de beoordeling. |
|Autobeoordeling | Beoordeling bevindt zich in een systeem revisie fase. Het systeem is bezig met het vastleggen van beslissingen voor gebruikers die niet zijn gecontroleerd op basis van aanbevelingen of vooraf geconfigureerde beslissingen. |
|Autobeoordeling | Er zijn beslissingen vastgelegd door het systeem voor alle gebruikers die niet zijn gecontroleerd. Beoordeling is gereed om verder te gaan met het **Toep assen van de toepassing** als automatisch Toep assen is ingeschakeld. |
|Aanvragen | Er is geen wijziging in de toegang voor gebruikers die zijn goedgekeurd. |
|Gehanteerd | Geweigerde gebruikers, indien van toepassing, zijn verwijderd uit de resource of map. |

## <a name="create-reviews-via-apis"></a>Beoordelingen maken via Api's

U kunt ook toegangs beoordelingen maken met behulp van Api's. Wat u doet om toegangs beoordelingen van groepen en toepassings gebruikers in het Azure Portal te beheren, kunt u ook uitvoeren met behulp van Microsoft Graph-Api's. Zie voor meer informatie de [Azure AD Access beoordelingen API-referentie](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Voor een code voorbeeld, Zie [voor beeld van het ophalen van Azure AD-toegangs beoordelingen via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot groepen of toepassingen controleren](perform-access-review.md)
- [De toegang tot groepen of toepassingen controleren](review-your-access.md)
- [Een toegangs beoordeling van groepen of toepassingen volt ooien](complete-access-review.md)
