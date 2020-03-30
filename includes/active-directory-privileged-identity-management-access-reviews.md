---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476259"
---
## <a name="create-one-or-more-access-reviews"></a>Een of meer toegangsbeoordelingen maken

1. Klik **op Nieuw** om een nieuwe toegangsbeoordeling te maken.

1. Geef de toegangsbeoordeling een naam. Geef eventueel een beschrijving van de beoordeling. De naam en beschrijving worden getoond aan de revisoren.

    ![Een toegangsbeoordeling maken - Naam en beschrijving bekijken](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Stel de **begindatum in**. Standaard vindt een toegangscontrole één keer plaats, wordt gestart op hetzelfde moment dat deze wordt gemaakt en eindigt deze in één maand. U de begin- en einddatum wijzigen om in de toekomst een toegangscontrole te laten starten en hoeveel dagen u maar wilt.

    ![Begindatum, frequentie, duur, einde, aantal keren en einddatum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Als u de toegangscontrole wilt herhalen, wijzigt u de **instelling Frequentie** van **Eenmalig** naar **Wekelijks**, **Maandelijks**, **Kwartaal ,** **Jaarlijks**of **Halfjaarlijks**. Gebruik de schuifregelaar **Duur** of het tekstvak om te bepalen hoeveel dagen elke revisie van de terugkerende reeks wordt geopend voor invoer van revisoren. De maximale duur die u instellen voor een maandelijkse beoordeling is bijvoorbeeld 27 dagen, om overlappende beoordelingen te voorkomen.

1. Gebruik de instelling **Einde** om op te geven hoe u de reeks terugkerende toegangscontrole beëindigen. De serie kan op drie manieren eindigen: het wordt continu uitgevoerd om beoordelingen voor onbepaalde tijd te starten, tot een specifieke datum of nadat een bepaald aantal gebeurtenissen is voltooid. U, een andere gebruiker beheerder, of een andere globale beheerder de reeks na het maken tegenhouden door de datum in **Montages**te veranderen, zodat het op die datum beëindigt.

1. Selecteer in de sectie **Gebruikers** een of meer rollen waarvan u het lidmaatschap wilt bekijken.

    ![Gebruikers scope om rol lidmaatschap van](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Als u meer dan één rol selecteert, worden meerdere toegangsbeoordelingen gemaakt. Als u bijvoorbeeld vijf rollen selecteert, worden vijf afzonderlijke toegangsbeoordelingen gemaakt.

    Als u een toegangscontrole van Azure AD-rollen maakt, wordt in het volgende een voorbeeld van de lijst met lidmaatschapsoverzicht en beoordeling weergegeven.

    ![Overzicht van het lidmaatschapsvenster met Azure AD-rollen die u selecteren](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Als u een toegangscontrole van Azure-bronrollen maakt, wordt in het volgende een voorbeeld van de lijst met lidmaatschapsoverzicht controleren weergegeven.

    ![Overzicht van het lidmaatschapsvenster met Azure-bronrollen die u selecteren](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Selecteer in de sectie **Revisoren** een of meer personen om alle gebruikers te controleren. U er ook voor kiezen dat de leden hun eigen toegang bekijken.

    ![Lijst met revisoren met geselecteerde gebruikers of leden (zelf)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Geselecteerde gebruikers** - Gebruik deze optie als u niet weet wie toegang nodig heeft. Met deze optie u de beoordeling toewijzen aan een resourceeigenaar of groepsmanager om te voltooien.
    - **Leden (zelf)** - Gebruik deze optie om de gebruikers hun eigen roltoewijzingen te laten bekijken.

### <a name="upon-completion-settings"></a>Na voltooiingsinstellingen

1. Als u wilt opgeven wat er gebeurt nadat een beoordeling is voltooid, vouwt u de sectie **Instellingen voor voltooiing na.**

    ![Na voltooiing instellingen om automatisch toe te passen en moet controleren niet reageren](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Als u de toegang voor gebruikers die zijn geweigerd automatisch wilt verwijderen, stelt u **De resultaten automatisch toe te passen op resource** op **Inschakelen**. Als u de resultaten handmatig wilt toepassen wanneer de beoordeling is voltooid, stelt u de schakelaar **in op Uitschakelen**.

1. Gebruik de **lijst Moet-revisor niet reageren** om op te geven wat er gebeurt voor gebruikers die niet worden beoordeeld door de revisor binnen de beoordelingsperiode. Deze instelling heeft geen invloed op gebruikers die handmatig door de beoordelaars zijn beoordeeld. Als de beslissing van de uiteindelijke beoordelaar Weigeren is, wordt de toegang van de gebruiker verwijderd.

    - **Geen wijziging** - Laat de toegang van de gebruiker ongewijzigd
    - **Toegang verwijderen** - Toegang van de gebruiker verwijderen
    - **Toegang goedkeuren** - Toegang van de gebruiker goedkeuren
    - **Neem aanbevelingen** - Neem de aanbeveling van het systeem over het weigeren of goedkeuren van de voortdurende toegang van de gebruiker

### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Als u extra instellingen wilt opgeven, vouwt u de sectie **Geavanceerde instellingen** uit.

    ![Geavanceerde instellingen voor aanbevelingen weergeven, reden vereisen voor goedkeuring, e-mailmeldingen en herinneringen](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. **Stel Aanbevelingen weergeven** **in om** de revisoren de systeemaanbevelingen weer te geven op basis van de toegangsgegevens van de gebruiker.

1. Stel **Reden vereisen bij goedkeuring** in **om** de revisor te verplichten een reden voor goedkeuring te geven.

1. Mailmeldingen instellen **op Inschakelen** om Azure AD e-mailmeldingen te laten verzenden naar revisoren wanneer een toegangscontrole wordt gestart en naar beheerders wanneer een beoordeling is voltooid. **Mail notifications**

1. **Stel Herinneringen** **in om** Azure AD te laten verzenden met herinneringen over toegangsbeoordelingen die worden uitgevoerd naar revisoren die hun beoordeling nog niet hebben voltooid.
