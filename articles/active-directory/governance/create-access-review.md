---
title: Een toegangsbeoordeling maken van groepen & toepassingen - Azure AD
description: Meer informatie over het maken van een toegangscontrole van groepsleden of toepassingstoegang in Azure Active Directory-toegangsbeoordelingen.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b8f2cf7db474f2a67ebaed818a0a440a195326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128849"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Een toegangsbeoordeling van groepen en toepassingen maken in Azure AD-toegangsbeoordelingen

Toegang tot groepen en toepassingen voor medewerkers en gasten verandert in de loop van de tijd. Om het risico te verminderen dat is gekoppeld aan verouderde toegangstoewijzingen, kunnen beheerders Azure Active Directory (Azure AD) gebruiken om toegangsbeoordelingen voor groepsleden of toepassingstoegang te maken. Als u de toegang routinematig moet controleren, u ook terugkerende toegangsbeoordelingen maken. Zie [Gebruikerstoegang beheren](manage-user-access-with-access-reviews.md) en [Gasttoegang beheren](manage-guest-access-with-access-reviews.md)voor meer informatie over deze scenario's.

In dit artikel wordt beschreven hoe u een of meer toegangsbeoordelingen maakt voor groepsleden of toepassingstoegang.

## <a name="prerequisites"></a>Vereisten

- Azure AD Premium P2
- Globale beheerder of gebruikersbeheerder

Zie [Licentievereisten voor](access-reviews-overview.md#license-requirements)meer informatie.

## <a name="create-one-or-more-access-reviews"></a>Een of meer toegangsbeoordelingen maken

1. Meld u aan bij de Azure-portal en open de [pagina Identiteitsbeheer](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klik in het linkermenu op **Toegangsrecensies**.

1. Klik **op Nieuwe toegangscontrole** om een nieuwe toegangsbeoordeling te maken.

    ![Venster Met recensies openen in Identiteitsbeheer](./media/create-access-review/access-reviews.png)

1. Geef de toegangsbeoordeling een naam. Geef eventueel een beschrijving van de beoordeling. De naam en beschrijving worden getoond aan de revisoren.

    ![Een toegangsbeoordeling maken - Naam en beschrijving bekijken](./media/create-access-review/name-description.png)

1. Stel de **begindatum in**. Standaard vindt een toegangscontrole één keer plaats, wordt gestart op hetzelfde moment dat deze wordt gemaakt en eindigt deze in één maand. U de begin- en einddatum wijzigen om in de toekomst een toegangscontrole te laten starten en hoeveel dagen u maar wilt.

    ![Een toegangscontrole maken - Begin- en einddatums](./media/create-access-review/start-end-dates.png)

1. Als u de toegangsbeoordeling wilt herhalen, wijzigt u de **instelling Frequentie** van **Eenmalig** naar **Wekelijks**, **Maandelijks**, **Driemaandelijks**, **Halfjaarlijks**of **Jaarlijks**. Gebruik de schuifregelaar **Duur** of het tekstvak om te bepalen hoeveel dagen elke revisie van de terugkerende reeks wordt geopend voor invoer van revisoren. De maximale duur die u instellen voor een maandelijkse beoordeling is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voorkomen.

1. Gebruik de instelling **Einde** om op te geven hoe u de reeks terugkerende toegangscontrole beëindigen. De serie kan op drie manieren eindigen: het wordt continu uitgevoerd om beoordelingen voor onbepaalde tijd te starten, tot een specifieke datum of nadat een bepaald aantal gebeurtenissen is voltooid. U, een andere gebruiker beheerder, of een andere globale beheerder de reeks na het maken tegenhouden door de datum in **Montages**te veranderen, zodat het op die datum beëindigt.

1. Geef in de sectie **Gebruikers** de gebruikers op waarop de toegangscontrole van toepassing is. Toegangsbeoordelingen kunnen zijn voor de leden van een groep of voor gebruikers die aan een toepassing zijn toegewezen. U de toegangscontrole verder uitzoeken om alleen de gastgebruikers te controleren die lid zijn (of aan de toepassing zijn toegewezen), in plaats van alle gebruikers te controleren die lid zijn of toegang hebben tot de toepassing.

    ![Een toegangscontrole maken - Gebruikers](./media/create-access-review/users.png)

1. Selecteer in de sectie **Groep** een of meer groepen waarvan u het lidmaatschap wilt bekijken.

    > [!NOTE]
    > Als u meer dan één groep selecteert, worden meerdere toegangsbeoordelingen gemaakt. Als u bijvoorbeeld vijf groepen selecteert, worden vijf afzonderlijke toegangsbeoordelingen gemaakt.
    
    ![Een toegangscontrole maken - Groep selecteren](./media/create-access-review/select-group.png)

1. Selecteer in de sectie **Toepassingen** (als u **Toegewezen aan een toepassing** in stap 8 hebt geselecteerd), de toepassingen waartoe u de toegang wilt controleren.

    > [!NOTE]
    > Als u meer dan één toepassing selecteert, worden meerdere toegangsbeoordelingen gemaakt. Als u bijvoorbeeld vijf toepassingen selecteert, worden vijf afzonderlijke toegangsbeoordelingen gemaakt.
    
    ![Een toegangscontrole maken - Toepassing selecteren](./media/create-access-review/select-application.png)

1. Selecteer in de sectie **Revisoren** een of meer personen om alle gebruikers in het bereik te controleren. U er ook voor kiezen dat de leden hun eigen toegang bekijken. Als de bron een groep is, u de groepseigenaren vragen om deze te bekijken. U ook eisen dat de revisoren een reden geven wanneer ze de toegang goedkeuren.

    ![Een toegangsbeoordeling maken - Reviewers](./media/create-access-review/reviewers.png)

1. Selecteer in de sectie **Programma's** het programma dat u wilt gebruiken. **Standaardprogramma** is altijd aanwezig.

    ![Een toegangscontrole maken - Programma's](./media/create-access-review/programs.png)

    U vereenvoudigen hoe u toegangsbeoordelingen voor verschillende doeleinden bijhouden en verzamelen door ze in programma's te ordenen. Elke toegangsbeoordeling kan worden gekoppeld aan een programma. Wanneer u vervolgens rapporten voorbereidt voor een auditor, u zich concentreren op de toegangsbeoordelingen die geschikt zijn voor een bepaald initiatief. Programma's en resultaten van toegangscontrole zijn zichtbaar voor gebruikers in de rol Globale beheerder, Gebruikersbeheerder, Beveiligingsbeheerder of Beveiligingslezer.

    Als u een lijst met programma's wilt bekijken, gaat u naar de pagina Met toegangsrecensies en selecteert u **Programma's**. Als u zich in een globale administrator- of gebruikersbeheerderrol bevindt, u aanvullende programma's maken. U er bijvoorbeeld voor kiezen om één programma te hebben voor elk nalevingsinitiatief of bedrijfsdoel. Als u geen programma meer nodig hebt en er geen besturingselementen aan zijn gekoppeld, u het verwijderen.

### <a name="upon-completion-settings"></a>Na voltooiingsinstellingen

1. Als u wilt opgeven wat er gebeurt nadat een beoordeling is voltooid, vouwt u de sectie **Instellingen voor voltooiing na.**

    ![Een toegangscontrole maken - Na voltooiingsinstellingen](./media/create-access-review/upon-completion-settings.png)

1. Als u automatisch wilt verwijderen, toegang voor gebruikers die zijn geweigerd, stelt u **De resultaten automatisch toe te passen op resource** op **Inschakelen**. Als u de resultaten handmatig wilt toepassen wanneer de beoordeling is voltooid, stelt u de schakelaar **in op Uitschakelen**.

1. Gebruik de **lijst Moet-revisor niet reageren** om op te geven wat er gebeurt voor gebruikers die niet worden beoordeeld door de revisor binnen de beoordelingsperiode. Deze instelling heeft geen invloed op gebruikers die handmatig door de beoordelaars zijn beoordeeld. Als de beslissing van de uiteindelijke beoordelaar Weigeren is, wordt de toegang van de gebruiker verwijderd.

    - **Geen wijziging** - Laat de toegang van de gebruiker ongewijzigd
    - **Toegang verwijderen** - Toegang van de gebruiker verwijderen
    - **Toegang goedkeuren** - Toegang van de gebruiker goedkeuren
    - **Neem aanbevelingen** - Neem de aanbeveling van het systeem over het weigeren of goedkeuren van de voortdurende toegang van de gebruiker

### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Als u extra instellingen wilt opgeven, vouwt u de sectie **Geavanceerde instellingen** uit.

    ![Een toegangscontrole maken - Geavanceerde instellingen](./media/create-access-review/advanced-settings.png)

1. **Stel Aanbevelingen weergeven** **in om** de revisoren de systeemaanbevelingen weer te geven op basis van de toegangsgegevens van de gebruiker.

1. Stel **Reden vereisen bij goedkeuring** in **om** de revisor te verplichten een reden voor goedkeuring te geven.

1. Mailmeldingen instellen **op Inschakelen** om Azure AD e-mailmeldingen te laten verzenden naar revisoren wanneer een toegangscontrole wordt gestart en naar beheerders wanneer een beoordeling is voltooid. **Mail notifications**

1. **Stel Herinneringen** **in om** Azure AD te laten verzenden met herinneringen over toegangsbeoordelingen die worden uitgevoerd naar revisoren die hun beoordeling nog niet hebben voltooid.

    Standaard ontvangen de beoordelaars die nog niet hebben gereageerd halverwege de einddatum automatisch een herinnering van Azure AD.

## <a name="start-the-access-review"></a>De toegangscontrole starten

Nadat u de instellingen voor een toegangscontrole hebt opgegeven, klikt u op **Start**. De toegangscontrole wordt weergegeven in uw lijst met een indicator van de status ervan.

![Lijst met toegangsbeoordelingen en hun status](./media/create-access-review/access-reviews-list.png)

Azure AD stuurt standaard een e-mail naar revisoren kort nadat de beoordeling is gestart. Als u ervoor kiest azure AD de e-mail niet te laten verzenden, moet u de revisoren informeren dat een toegangscontrole wacht voordat ze zijn voltooid. U ze de instructies laten zien voor het [controleren van de toegang tot groepen of toepassingen.](perform-access-review.md) Als uw beoordeling is voor gasten om hun eigen toegang te controleren, laat hen de instructies voor hoe [de toegang voor jezelf te controleren tot groepen of toepassingen.](review-your-access.md)

Als u gasten hebt toegewezen als revisoren en ze de uitnodiging niet hebben geaccepteerd, ontvangen ze geen e-mail van toegangsbeoordelingen omdat ze eerst de uitnodiging moeten accepteren voordat ze worden beoordeeld.

## <a name="access-review-status-table"></a>Statustabel voor toegangscontrole

| Status | Definitie |
|--------|------------|
|Niet gestart | Beoordeling is gemaakt, de ontdekking van de gebruiker wacht om te beginnen. |
|Initialiseren   | De detectie van gebruikers is in volle gang om alle gebruikers te identificeren die deel uitmaken van de beoordeling. |
|Starten | De beoordeling begint. Als e-mailmeldingen zijn ingeschakeld, worden e-mails naar revisoren verzonden. |
|InProgress | De beoordeling is begonnen. Als e-mailmeldingen zijn ingeschakeld, zijn e-mails naar revisoren verzonden. Revisoren kunnen besluiten indienen tot de vervaldatum. |
|Voltooien | Beoordeling wordt voltooid en e-mails worden verzonden naar de eigenaar van de beoordeling. |
|AutoReviewing AutoReviewing AutoReviewing AutoReview | Beoordeling bevindt zich in een systeembeoordelingsfase. Het systeem is het opnemen van beslissingen voor gebruikers die niet zijn beoordeeld op basis van aanbevelingen of vooraf geconfigureerde beslissingen. |
|AutoReviewed | Beslissingen zijn door het systeem vastgelegd voor alle gebruikers die niet zijn beoordeeld. Controle is klaar om over te gaan tot **Solliciteren** als Automatisch toepassen is ingeschakeld. |
|Toepassing | Er zal geen wijziging in de toegang voor gebruikers die zijn goedgekeurd. |
|Toegepast | Geweigerde gebruikers, indien van toepassing, zijn verwijderd uit de bron of directory. |

## <a name="create-reviews-via-apis"></a>Beoordelingen maken via API's

U ook toegangsbeoordelingen maken met API's. Wat u doet om toegangsbeoordelingen van groepen en toepassingsgebruikers in de Azure-portal te beheren, kan ook worden gedaan met Behulp van Microsoft Graph API's. Zie de [API-verwijzing voor Azure AD-toegangsbeoordelingen voor](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta)meer informatie . Zie Voorbeeld van [het ophalen van Azure AD-toegangsbeoordelingen via Microsoft Graph voor](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)een codevoorbeeld.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot groepen of toepassingen controleren](perform-access-review.md)
- [De toegang voor uzelf tot groepen of toepassingen bekijken](review-your-access.md)
- [Een toegangsbeoordeling van groepen of toepassingen voltooien](complete-access-review.md)
