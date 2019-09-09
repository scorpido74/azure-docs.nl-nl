---
title: Azure-resource roltoewijzingen uitbreiden of vernieuwen in PIM-Azure Active Directory | Microsoft Docs
description: Meer informatie over het uitbreiden of vernieuwen van toewijzingen voor Azure-resource rollen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804133"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Azure-resource roltoewijzingen in PIM uitbreiden of vernieuwen

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) introduceert nieuwe besturings elementen voor het beheren van de levens cyclus van toegang en toewijzing voor Azure-resources. Beheerders kunnen lidmaatschap toewijzen met behulp van de begin-en eind datum-eigenschappen. Wanneer het einde van de toewijzing voldoet, verzendt PIM e-mail meldingen naar de betrokken gebruikers of groepen. Er worden ook e-mail meldingen verzonden naar beheerders van de bron om ervoor te zorgen dat de juiste toegang wordt gehandhaafd. Toewijzingen kunnen worden vernieuwd en blijven tot 30 dagen zichtbaar in de status verlopen, zelfs als de toegang niet is uitgebreid.

## <a name="who-can-extend-and-renew"></a>Wie kan worden uitgebreid en verlengd?

Alleen beheerders van de resource kunnen roltoewijzingen uitbreiden of vernieuwen. Het betrokken lid kan aanvragen om rollen uit te breiden die bijna verlopen en aanvragen voor het vernieuwen van rollen die al verlopen zijn.

## <a name="when-are-notifications-sent"></a>Wanneer worden er meldingen verzonden?

PIM verzendt e-mail meldingen naar beheerders en betrokken leden van rollen die binnen 14 dagen en één dag vóór de verval datum verlopen. Er wordt een extra e-mail verzonden wanneer een toewijzing officieel verloopt. 

Beheerders ontvangen meldingen wanneer een lid van een verlopende of verlopen rol aanvragen uitbreidt of verlengt. Wanneer een specifieke beheerder de aanvraag verhelpt, worden alle andere beheerders op de hoogte gesteld van de oplossings beslissing (goedgekeurd of geweigerd). Vervolgens wordt het aangevraagde lid op de hoogte gesteld van de beslissing. 

## <a name="extend-role-assignments"></a>Roltoewijzingen uitbreiden

De volgende stappen beschrijven het proces voor het aanvragen, oplossen of het beheren van een uitbrei ding of het vernieuwen van een roltoewijzing. 

### <a name="member-extend"></a>Lid uitbreiden

Leden van een roltoewijzing kunnen verlopende roltoewijzingen rechtstreeks vanuit het **in aanmerking komende** of **actieve** tabblad op de pagina **mijn rollen** van een resource en op de pagina mijn **rollen op het hoogste niveau van** de PIM-Portal verlengen. Leden kunnen aanvragen om in aanmerking komende en actieve (toegewezen) rollen uit te breiden die in de komende 14 dagen verlopen.

![Azure-resources: mijn rollen pagina lijst met in aanmerking komende rollen met een actie kolom](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

Wanneer de eind datum van de toewijzing binnen 14 dagen valt, wordt de knop om uit te **breiden** een actieve koppeling in de gebruikers interface. In het volgende voor beeld wordt ervan uitgegaan dat de huidige datum 27 maart is.

![Actie kolom met koppelingen om te activeren of uit te breiden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Als u een uitbrei ding van deze roltoewijzing wilt aanvragen, selecteert u **uitbreiden** om het aanvraag formulier te openen.

![Deel venster roltoewijzing uitbreiden met een reden veld](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Als u informatie over de oorspronkelijke toewijzing wilt weer geven, vouwt u **toewijzings Details**uit. Voer een reden in voor de uitbreidings aanvraag en selecteer vervolgens **uitbreiden**.

>[!Note]
>We raden u aan om de details van de uitbrei ding op te nemen en te bepalen hoe lang de uitbrei ding moet worden verleend (als u deze informatie hebt).

![Deel venster roltoewijzing uitbreiden met toewijzings Details uitgebreid](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

In een paar seconden ontvangen resource beheerders een e-mail melding waarin wordt gevraagd of de aanvraag voor de verlenging wordt gecontroleerd. Als er al een aanvraag om uit te breiden is ingediend, wordt er een pop-upmelding weer gegeven aan de bovenkant van de Azure Portal de fout te verklaren.

![Melding waarin wordt uitgelegd dat er al een bestaande uitbrei ding voor roltoewijzing in behandeling is](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Ga naar de pagina **aanvragen in behandeling** in het linkerdeel venster om de status van uw aanvraag te bekijken of om deze te annuleren.

![Azure-resources-pagina aanvragen in behandeling lijst met aangevraagd en een koppeling om te annuleren](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Beheerder goed keuren

Wanneer een lid een aanvraag indient om een roltoewijzing uit te breiden, ontvangen resource beheerders een e-mail melding die de details van de oorspronkelijke toewijzing en de reden voor de aanvraag bevat. De melding bevat een rechtstreekse koppeling naar de aanvraag die de beheerder kan goed keuren of weigeren. 

Naast het gebruik van de koppeling van e-mail kunnen beheerders aanvragen goed keuren of weigeren door naar de PIM-beheer portal te gaan en **aanvragen goed keuren** te selecteren in het linkerdeel venster.

![Azure-resources-aanvragen goed keuren pagina aanvragen en koppelingen die moeten worden goedgekeurd of geweigerd](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Wanneer een beheerder **goed keuren** of **weigeren**selecteert, worden de details van de aanvraag weer gegeven, samen met een veld om de controle logboeken te verantwoorden.

![Aanvraag voor roltoewijzing goed keuren met de reden van de aanvrager, toewijzings type, begin tijd, eind tijd en reden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Bij het goed keuren van een aanvraag om roltoewijzing uit te breiden, kunnen resource beheerders een nieuwe begin datum, eind datum en toewijzings type kiezen. Het kan nodig zijn om het toewijzings type te wijzigen als de beheerder beperkte toegang wil verlenen om een specifieke taak uit te voeren (bijvoorbeeld één dag). In dit voor beeld kan de beheerder de toewijzing wijzigen van in **aanmerking komend** in **actief**. Dit betekent dat ze toegang kunnen bieden tot de aanvrager zonder dat ze het hoeven te activeren.

### <a name="admin-extend"></a>Beheerdersuitbreiding

Als een lid van de rol een uitbrei ding voor een rollidmaatschap kan of niet kan aanvragen, kan een beheerder namens het lid een toewijzing uitbreiden. Voor beheer uitbreidingen van rollidmaatschap is geen goed keuring vereist, maar er worden meldingen verzonden naar alle andere beheerders nadat de rol is uitgebreid.

Als u een rollidmaatschap wilt uitbreiden, bladert u naar de resource functie of leden weergave in PIM. Zoek het lid waarvoor een extensie is vereist. Selecteer vervolgens **uitbreiden** in de kolom actie.

![Azure-resources: leden pagina lijst in aanmerking komende rollen met koppelingen naar uitbreiden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Roltoewijzingen vernieuwen

Het proces voor het vernieuwen van een uitbrei ding is in het algemeen vergelijkbaar met het proces voor het aanvragen van een verlopen roltoewijzing. Met behulp van de volgende stappen kunnen leden en beheerders de toegang tot verlopen rollen zo nodig vernieuwen.

### <a name="member-renew"></a>Lid vernieuwen

Leden die geen toegang meer hebben tot resources, kunnen Maxi maal 30 dagen aan verlopen toewijzings geschiedenis. Hiertoe gaat u naar **mijn rollen** in het linkerdeel venster en selecteert u vervolgens het tabblad **verlopen rollen** in de sectie Azure-resource rollen.

![Pagina mijn rollen-tabblad verlopen rollen](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

De lijst met rollen wordt standaard weer gegeven naar **in aanmerking komende rollen**. Gebruik de vervolg keuzelijst om te scha kelen tussen de in aanmerking komende en actieve toegewezen rollen.

Als u een verlenging wilt aanvragen voor een van de roltoewijzingen in de lijst, selecteert u de actie **vernieuwen** . Geef vervolgens een reden op voor de aanvraag. Het is handig om een duur te bieden naast eventuele aanvullende context die de resource beheerder helpt om goed te keuren of te weigeren.

![Het deel venster roltoewijzing vernieuwen met het vak reden](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

Nadat de aanvraag is verzonden, worden resource beheerders op de hoogte gesteld van een aanvraag in behandeling voor het vernieuwen van een roltoewijzing.

### <a name="admin-approves"></a>Beheerder keurt

Resource beheerders hebben toegang tot de vernieuwings aanvraag via de koppeling in de e-mail melding of door middel van de Azure Portal en het selecteren van **aanvragen goed keuren** in het linkerdeel venster.

![Azure-resources-aanvragen goed keuren pagina aanvragen en koppelingen die moeten worden goedgekeurd of geweigerd](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

Wanneer een beheerder **goed keuren** of **weigeren**selecteert, worden de details van de aanvraag samen met een veld weer gegeven om een motivering voor de audit logboeken te geven.

![Aanvraag voor roltoewijzing goed keuren met de reden van de aanvrager, toewijzings type, begin tijd, eind tijd en reden](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

Bij het goed keuren van een aanvraag voor het vernieuwen van roltoewijzing moeten resource beheerders een nieuwe begin datum, eind datum en toewijzings type invoeren. 

### <a name="admin-renew"></a>Beheerdersverlenging

Resource beheerders kunnen verlopen roltoewijzingen vernieuwen vanaf het tabblad **leden** in het navigatie menu aan de linkerkant van een resource. Ze kunnen ook verlopen roltoewijzingen vernieuwen vanuit het tabblad **verlopen** rollen van een resource-rol.

Als u een lijst met alle verlopen roltoewijzingen wilt weer geven, selecteert u **verlopen rollen**op het scherm **leden** .

![Azure-resources-leden pagina met een lijst met verlopen rollen met koppelingen naar renew](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Volgende stappen

- [Aanvragen voor Azure-resource rollen in PIM goed keuren of weigeren](pim-resource-roles-approval-workflow.md)
- [Instellingen voor Azure-resource rollen configureren in PIM](pim-resource-roles-configure-role-settings.md)
