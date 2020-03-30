---
title: Selfservice voor groepsbeheer instellen - Azure Active Directory | Microsoft Docs
description: Beveiligingsgroepen of Office 365-groepen maken en beheren in Azure Active Directory en lidmaatschap van een beveiligingsgroep of Office 365-groep aanvragen
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298000"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Zelfservicegroepsbeheer instellen in Azure Active Directory 

U gebruikers inschakelen hun eigen beveiligingsgroepen of Office 365-groepen te maken en te beheren in Azure Active Directory (Azure AD). De eigenaar van de groep kan lidmaatschapsaanvragen goedkeuren of weigeren en kan het beheer van het groepslidmaatschap delegeren. Functies voor zelfservicegroepenbeheer zijn niet beschikbaar voor beveiligingsgroepen of distributielijsten met e-mail.

## <a name="self-service-group-membership-defaults"></a>Zelfbedieningsgroeplidmaatschap standaard

Wanneer beveiligingsgroepen worden gemaakt in de Azure-portal of met Azure AD PowerShell, kunnen alleen de eigenaren van de groep het lidmaatschap bijwerken. Beveiligingsgroepen die door selfservice zijn gemaakt in het [Access-paneel](https://account.activedirectory.windowsazure.com/r#/joinGroups) en alle Office 365-groepen zijn beschikbaar om lid te worden voor alle gebruikers, of het nu door de eigenaar is goedgekeurd of automatisch is goedgekeurd. In het deelvenster Access u de lidmaatschapsopties wijzigen wanneer u de groep maakt.

Groepen die zijn gemaakt in | Standaardgedrag beveiligingsgroep | Standaardgedrag van Office 365-groeps
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Alleen eigenaren kunnen leden toevoegen<br>Zichtbaar, maar niet beschikbaar om deel te nemen in het deelvenster Access | Open om lid te worden voor alle gebruikers
[Azure-portal](https://portal.azure.com) | Alleen eigenaren kunnen leden toevoegen<br>Zichtbaar, maar niet beschikbaar om deel te nemen in het deelvenster Access<br>Eigenaar wordt niet automatisch toegewezen bij het maken van groepen | Open om lid te worden voor alle gebruikers
[Deelvenster Access](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Open om lid te worden voor alle gebruikers<br>Lidmaatschapsopties kunnen worden gewijzigd wanneer de groep wordt gemaakt | Open om lid te worden voor alle gebruikers<br>Lidmaatschapsopties kunnen worden gewijzigd wanneer de groep wordt gemaakt

## <a name="self-service-group-management-scenarios"></a>Scenario's voor zelfservicegroepsbeheer

* **Gedelegeerd groepsbeheer** Een voorbeeld is een beheerder die toegang beheert tot een SaaS-toepassing die het bedrijf gebruikt. Het beheren van deze gebruiksrechten is omslachtig en daarom vraagt de beheerder de eigenaar van het bedrijf om een nieuwe groep te maken. De beheerder wijst toegang voor de toepassing toe aan de nieuwe groep en voegt aan de groep alle personen toe die al toegang hebben tot de toepassing. De bedrijfseigenaar kan meer gebruikers toevoegen en deze gebruikers worden automatisch ingericht op de toepassing. De bedrijfseigenaar hoeft niet op de beheerder te wachten om de toegang voor gebruikers te beheren. Als de beheerder dezelfde toestemming verleent aan een manager in een andere bedrijfsgroep, kan die persoon ook de toegang voor zijn eigen groepsleden beheren. Noch de bedrijfseigenaar, noch de manager kan elkaars groepslidmaatschappen bekijken of beheren. De beheerder kan nog steeds alle gebruikers die toegang tot de toepassing hebben zien en zonodig de toegangsrechten blokkeren.
* **Self-service voor groepsbeheer** Een voorbeeld van dit scenario zijn twee gebruikers die allebei SharePoint Online-sites hebben die ze onafhankelijk hebben ingesteld. Ze willen elkaars teams toegang geven tot hun sites. Hiervoor kunnen ze in Azure AD een groep maken en in SharePoint selecteert elk van hen de groep waartoe ze op hun sites toegang willen verlenen. Wanneer iemand toegang wil, kan dit worden aangevraagd bij het toegangspaneel en na goedkeuring wordt automatisch toegang verleend tot beide SharePoint Online-sites. Later beslist één van hen dat alle personen die de site openen ook toegang moeten krijgen tot een specifieke SaaS-toepassing. De beheerder van de SaaS-toepassing kan toegangsrechten toevoegen voor de toepassing aan de SharePoint Online-site. Vanaf dan verlenen alle goedgekeurde verzoeken toegang tot de twee SharePoint Online-sites en ook tot deze SaaS-toepassing.

## <a name="make-a-group-available-for-user-self-service"></a>Een groep beschikbaar maken voor self-service door gebruikers

1. Meld u aan bij het [beheercentrum van Azure AD](https://aad.portal.azure.com) met een account met globale beheerdersrechten voor de directory.
1. Selecteer **Groepen**en selecteer **Vervolgens Algemene** instellingen.
1. **Eigenaren instellen kunnen groepslidmaatschapaanvragen beheren in het toegangspaneel** naar **Ja**.
1. **Toegang tot groepen in het toegangspaneel beperken** tot **nee**instellen.
1. Als u **gebruikers instelt, beveiligingsgroepen kunnen maken in Azure-portals** of **gebruikers Office 365-groepen kunnen maken in Azure-portalen**

    - **Ja:** alle gebruikers in uw Azure AD-organisatie mogen nieuwe beveiligingsgroepen maken en leden toevoegen aan deze groepen. Deze nieuwe groepen zullen ook verschijnen in het Toegangsvenster voor alle andere gebruikers. Als de beleidsinstelling voor de groep dit toestaat, kunnen andere gebruikers aanvragen maken om lid te worden van deze groepen
    - **Nee:** gebruikers kunnen geen groepen maken en kunnen bestaande groepen waarvoor ze eigenaar zijn, niet wijzigen. Ze kunnen echter nog steeds de lidmaatschappen van die groepen beheren en aanvragen van andere gebruikers om lid te worden van hun groep goedkeuren.

U eigenaren ook gebruiken **die leden kunnen toewijzen als groepseigenaren in Azure-portals** en **-eigenaren die leden kunnen toewijzen als groepseigenaren in Azure-portals** om meer gedetailleerde toegangscontrole te bereiken voor zelfservicegroepsbeheer voor uw gebruikers.

Wanneer gebruikers groepen kunnen maken, mogen alle gebruikers in uw organisatie nieuwe groepen maken en kunnen ze als standaardeigenaar leden aan deze groepen toevoegen. U geen personen opgeven die hun eigen groepen kunnen maken. U alleen personen opgeven om van een ander groepslid een groepseigenaar te maken.

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Toegang tot resources beheren met Azure Active Directory-groepen](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](groups-settings-cmdlets.md)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Wat is Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
