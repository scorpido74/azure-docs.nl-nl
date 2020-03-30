---
title: Azure-bronroltoewijzingen vernieuwen in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het verlengen of vernieuwen van Azure-brontoewijzingen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022914"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Azure-brontoewijzingen uitbreiden of vernieuwen in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) biedt besturingselementen voor het beheren van de levenscyclus van toegang en toewijzing voor Azure-resources. Beheerders kunnen rollen toewijzen met behulp van eigenschappen begin- en einddatum. Wanneer de toewijzing nadert, stuurt Privileged Identity Management e-mailmeldingen naar de betrokken gebruikers of groepen. Het stuurt ook e-mailmeldingen naar beheerders van de bron om ervoor te zorgen dat de juiste toegang wordt gehandhaafd. Toewijzingen kunnen worden verlengd en blijven maximaal 30 dagen zichtbaar in een verlopen status, zelfs als de toegang niet wordt verlengd.

## <a name="who-can-extend-and-renew"></a>Wie kan verlengen en verlengen?

Alleen beheerders van de resource kunnen roltoewijzingen verlengen of vernieuwen. De betrokken gebruiker of groep kan vragen om rollen die op het punt staan te verlopen uit te breiden en verzoeken rollen te vernieuwen die al zijn verlopen.

## <a name="when-are-notifications-sent"></a>Wanneer worden meldingen verzonden?

Privileged Identity Management stuurt e-mailmeldingen naar beheerders en getroffen gebruikers of groepen rollen die binnen 14 dagen en één dag voor het verstrijken van de looptijd verlopen. Het stuurt een extra e-mail wanneer een opdracht officieel verloopt.

Beheerders ontvangen meldingen wanneer een gebruiker of groep een verlopen de of verlopen rolaanvragen heeft toegewezen om te verlengen of te verlengen. Wanneer een specifieke beheerder de aanvraag oplost, worden alle andere beheerders op de hoogte gesteld van het afwikkelingsbesluit (goedgekeurd of geweigerd). Vervolgens wordt de verzoekende gebruiker of groep op de hoogte gebracht van de beslissing.

## <a name="extend-role-assignments"></a>Roltoewijzingen uitbreiden

In de volgende stappen wordt het proces beschreven voor het aanvragen, oplossen of beheren van een extensie of vernieuwing van een roltoewijzing.

### <a name="self-extend-expiring-assignments"></a>Aflopende opdrachten zelf verlengen

Gebruikers of groepen die aan een rol zijn toegewezen, kunnen aflopende roltoewijzingen rechtstreeks uitbreiden via het tabblad **In aanmerking komen** of **Actief** op de pagina **Mijn rollen** van een resource en vanaf de pagina Mijn **rollen op** het hoogste niveau van de portal Voor bevoegde identiteitsbeheer. Gebruikers of groepen kunnen aanvragen om in aanmerking komende en actieve (toegewezen) rollen die in de komende 14 dagen verlopen, te verlengen.

![Azure-bronnen - Mijn rollenpagina met in aanmerking komende rollen met een actiekolom](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Wanneer de einddatum van de toewijzing binnen 14 dagen is, wordt de knop **uitbreiden** een actieve koppeling in de gebruikersinterface. In het volgende voorbeeld gaat u ervan uit dat de huidige datum 27 maart is.

![Actiekolom met koppelingen naar Activeren of uitbreiden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Als u een verlenging van deze roltoewijzing wilt aanvragen, selecteert u **Uitlaten** om het aanvraagformulier te openen.

![Taaktoewijzingsvenster uitbreiden met een vak Reden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Als u informatie over de oorspronkelijke toewijzing wilt weergeven, vouwt u **Toewijzingsdetails**uit . Voer een reden in voor de extensieaanvraag en selecteer **Uitbreiden**.

>[!NOTE]
>We raden u aan de details op te nemen waarom de verlenging noodzakelijk is en hoe lang de verlenging moet worden verleend (als u deze informatie hebt).

![Taaktoewijzingsvenster uitbreiden met toewijzingsdetails uitgebreid](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

In een kwestie van momenten ontvangen resourcebeheerders een e-mailmelding met het verzoek om de extensieaanvraag te bekijken. Als er al een aanvraag tot verlenging is ingediend, wordt er een Azure-melding weergegeven in de portal.

![Melding waarin wordt uitgelegd dat er al een bestaande in behandeling zijnde uitbreiding van roltoewijzing bestaat](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Ga naar de pagina **Aanvragen in behandeling** om de status van uw aanvraag te bekijken of te annuleren.

![Azure-bronnen - Pagina in behandeling aangevraagde aanvragen met een in behandeling zijnde aanvraag en een koppeling naar Annuleren](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Beheerde extensie

Wanneer een gebruiker of groep een verzoek indient om een roltoewijzing uit te breiden, ontvangen resourcebeheerders een e-mailmelding met de details van de oorspronkelijke toewijzing en de reden voor de aanvraag. De melding bevat een directe link naar het verzoek voor de beheerder om goed te keuren of te weigeren.

Naast het gebruik van de koppeling via e-mail, kunnen beheerders verzoeken goedkeuren of weigeren door naar de beheerportal voor beheer voor bevoegde identiteit te gaan en aanvragen in het linkerdeelvenster **goedkeuren te** selecteren.

![Azure-bronnen - Aanvragen paginavermeldingaanvragen en koppelingen goedkeuren of weigeren goedkeuren of weigeren](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Wanneer een beheerder **Goedkeuren** of **Weigeren**selecteert, worden de details van de aanvraag weergegeven, samen met een veld om een zakelijke rechtvaardiging voor de controlelogboeken te geven.

![Roltoewijzingsaanvraag goedkeuren met de reden van aanvraag of aanvraag, toewijzingstype, begintijd, eindtijd en reden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Wanneer u een aanvraag goedkeurt om de roltoewijzing uit te breiden, kunnen resourcebeheerders een nieuwe begindatum, einddatum en toewijzingstype kiezen. Het wijzigen van het toewijzingstype kan nodig zijn als de beheerder beperkte toegang wil bieden om een specifieke taak te voltooien (bijvoorbeeld één dag). In dit voorbeeld kan de beheerder de toewijzing wijzigen **van In aanmerking komen** naar **Actief**. Dit betekent dat ze toegang kunnen bieden tot de aanvrager zonder dat ze moeten activeren.

### <a name="admin-initiated-extension"></a>Beheer gestarte extensie

Als een gebruiker die aan een rol is toegewezen, geen extensie aanvraagt voor de roltoewijzing, kan een beheerder een toewijzing namens de gebruiker uitbreiden. Administratieve uitbreidingen van roltoewijzing vereisen geen goedkeuring, maar meldingen worden naar alle andere beheerders verzonden nadat de rol is verlengd.

Als u een roltoewijzing wilt uitbreiden, bladert u naar de resourcerol of toewijzingsweergave in Privileged Identity Management. Zoek de toewijzing waarvoor een extensie nodig is. Selecteer **vervolgens Uitverlengen** in de actiekolom.

![Azure-bronnen - toewijzingen pagina lijst in aanmerking komende rollen met links uit te breiden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Roltoewijzingen vernieuwen

Hoewel conceptueel vergelijkbaar met het proces voor het aanvragen van een extensie, is het proces om een verlopen roltoewijzing te vernieuwen anders. Met behulp van de volgende stappen kunnen toewijzingen en beheerders de toegang tot verlopen rollen verlengen wanneer dat nodig is.

### <a name="self-renew"></a>Zelf vernieuwen

Gebruikers die geen toegang meer hebben tot bronnen, hebben toegang tot maximaal 30 dagen verlopen toewijzingsgeschiedenis. Hiervoor bladeren ze naar **Mijn rollen** in het linkerdeelvenster en selecteren ze het tabblad Verlopen **rollen** in de sectie Azure-bronrollen.

![Pagina Mijn rollen - Tabblad Verlopen rollen](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

De lijst met rollen die standaard worden weergegeven **voor in aanmerking komende rollen**. Gebruik het vervolgkeuzemenu om te schakelen tussen toegewezen rollen in aanmerking komende en actieve rollen.

Als u verlenging wilt aanvragen voor een van de roltoewijzingen in de lijst, selecteert u de actie **Vernieuwen.** Geef dan een reden voor het verzoek. Het is handig om een duur op te geven naast een extra context of een zakelijke rechtvaardiging die de resourcebeheerder kan helpen om te beslissen of te weigeren.

![Venster Roltoewijzing vernieuwen met het vak Reden](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Nadat de aanvraag is ingediend, worden resourcebeheerders op de hoogte gesteld van een in behandeling zijnde aanvraag om een roltoewijzing te vernieuwen.

### <a name="admin-approves"></a>Beheerder keurt

Beheerders van resources hebben toegang tot de verlengingsaanvraag via de koppeling in de e-mailmelding of door toegang te krijgen tot Privileged Identity Management vanuit de Azure-portal en **aanvragen in** het linkerdeelvenster te selecteren.

![Azure-bronnen - Aanvragen paginavermeldingaanvragen en koppelingen goedkeuren of weigeren goedkeuren of weigeren](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Wanneer een beheerder **Goedkeuren** of **Weigeren**selecteert, worden de details van de aanvraag samen met een veld weergegeven om een zakelijke rechtvaardiging voor de controlelogboeken te geven.

![Roltoewijzingsaanvraag goedkeuren met de reden van aanvraag of aanvraag, toewijzingstype, begintijd, eindtijd en reden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Wanneer u een aanvraag goedkeurt om de toewijzing van een rol te vernieuwen, moeten resourcebeheerders een nieuwe begindatum, einddatum en toewijzingstype invoeren.

### <a name="admin-renew"></a>Beheerder vernieuwen

Resourcebeheerders kunnen verlopen roltoewijzingen vernieuwen via het tabblad **Leden** in het linkernavigatiemenu van een resource. Ze kunnen ook verlopen roltoewijzingen vernieuwen vanuit het tabblad **Verlopen** rollen van een resourcerol.

Als u een lijst met alle verlopen roltoewijzingen wilt weergeven, selecteert u in het scherm **Leden** **verlopen rollen**.

![Azure-bronnen - Pagina-pagina-pagina's met verlopen rollen met koppelingen om te vernieuwen](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Volgende stappen

- [Aanvragen voor Azure-bronrollen in Privileged Identity Management goedkeuren of weigeren](pim-resource-roles-approval-workflow.md)
- [Azure-bronrolinstellingen configureren in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
