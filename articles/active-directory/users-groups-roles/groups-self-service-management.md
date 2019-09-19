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
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097701"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Self-service groeps beheer instellen in Azure Active Directory 

U kunt gebruikers in staat stellen hun eigen beveiligings groepen of Office 365-groepen te maken en beheren in Azure Active Directory (Azure AD). De eigenaar van de groep kan lidmaatschaps aanvragen goed keuren of weigeren en kan het beheer van groepslid maatschap overdragen. Self-service groeps beheer functies zijn niet beschikbaar voor beveiligings groepen met e-mail functionaliteit of distributie lijsten.

## <a name="self-service-group-membership-defaults"></a>Standaard waarden voor groepslid maatschap van self-service

Wanneer beveiligings groepen worden gemaakt in de Azure Portal of Azure AD Power shell gebruiken, kunnen alleen de eigen aren van de groep het lidmaatschap bijwerken. Beveiligings groepen die zijn gemaakt in het [deel venster toegang](https://account.activedirectory.windowsazure.com/r#/joinGroups) en alle Office 365-groepen zijn beschikbaar voor deelname aan alle gebruikers, hetzij door een eigenaar goedgekeurd of automatisch goedgekeurd. In het toegangs venster kunt u de lidmaatschaps opties wijzigen wanneer u de groep maakt.

Groepen die zijn gemaakt in | Standaard gedrag van beveiligings groep | Standaard gedrag van Office 365-groep
------------------ | ------------------------------- | ---------------------------------
[PowerShell voor Azure AD](groups-settings-cmdlets.md) | Alleen eigen aars kunnen leden toevoegen<br>Zichtbaar, maar niet beschikbaar voor deelname in het toegangs venster | Openen om lid te worden van alle gebruikers
[Azure-portal](https://portal.azure.com) | Alleen eigen aars kunnen leden toevoegen<br>Zichtbaar, maar niet beschikbaar voor deelname in het toegangs venster<br>De eigenaar wordt niet automatisch toegewezen bij het maken van een groep | Openen om lid te worden van alle gebruikers
[Toegangs venster](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Openen om lid te worden van alle gebruikers<br>Lidmaatschaps opties kunnen worden gewijzigd wanneer de groep wordt gemaakt | Openen om lid te worden van alle gebruikers<br>Lidmaatschaps opties kunnen worden gewijzigd wanneer de groep wordt gemaakt

## <a name="self-service-group-management-scenarios"></a>Scenario's voor Self-service voor groeps beheer

* **Gedelegeerd groepsbeheer** Een voorbeeld is een beheerder die toegang beheert tot een SaaS-toepassing die het bedrijf gebruikt. Het beheren van deze gebruiksrechten is omslachtig en daarom vraagt de beheerder de eigenaar van het bedrijf om een nieuwe groep te maken. De beheerder wijst de toegang voor de toepassing toe aan de nieuwe groep en voegt alle personen die al toegang hebben tot de toepassing toe aan de groep. De bedrijfseigenaar kan meer gebruikers toevoegen en deze gebruikers worden automatisch ingericht op de toepassing. De bedrijfseigenaar hoeft niet op de beheerder te wachten om de toegang voor gebruikers te beheren. Als de beheerder dezelfde machtiging verleent aan een manager in een andere bedrijfs groep, kan die persoon ook de toegang beheren voor hun eigen groeps leden. De eigenaar van het bedrijf en de Manager kunnen elkaars groepslid maatschappen niet zien of beheren. De beheerder kan nog steeds alle gebruikers die toegang tot de toepassing hebben zien en zonodig de toegangsrechten blokkeren.
* **Self-service voor groepsbeheer** Een voorbeeld van dit scenario zijn twee gebruikers die allebei SharePoint Online-sites hebben die ze onafhankelijk hebben ingesteld. Ze willen elkaars teams toegang geven tot hun sites. Hiervoor kunnen ze in Azure AD een groep maken en in SharePoint selecteert elk van hen de groep waartoe ze op hun sites toegang willen verlenen. Wanneer iemand toegang wil, kan dit worden aangevraagd bij het toegangspaneel en na goedkeuring wordt automatisch toegang verleend tot beide SharePoint Online-sites. Later beslist één van hen dat alle personen die de site openen ook toegang moeten krijgen tot een specifieke SaaS-toepassing. De beheerder van de SaaS-toepassing kan toegangsrechten toevoegen voor de toepassing aan de SharePoint Online-site. Vanaf dan verlenen alle goedgekeurde verzoeken toegang tot de twee SharePoint Online-sites en ook tot deze SaaS-toepassing.

## <a name="make-a-group-available-for-user-self-service"></a>Een groep beschikbaar maken voor self-service door gebruikers

1. Meld u aan bij het [beheercentrum van Azure AD](https://aad.portal.azure.com) met een account met globale beheerdersrechten voor de directory.
2. Selecteer **Gebruikers en groepen** en selecteer vervolgens **Groepsinstellingen**.
3. Stel **Self-service voor groepsbeheer is ingeschakeld** in op **Ja**.
4. Stel **Gebruikers kunnen beveiligingsgroepen maken** of **Gebruikers kunnen Office 365-groepen maken** in op **Ja**.
   * Als deze instellingen zijn ingeschakeld, kunnen alle gebruikers in uw directory nieuwe beveiligingsgroepen maken en leden toevoegen aan deze groepen. Deze nieuwe groepen zullen ook verschijnen in het Toegangsvenster voor alle andere gebruikers. Als de beleidsinstelling van de groep dit toestaat, kunnen andere gebruikers verzoeken maken om lid te worden van deze groepen. 
   * Als deze instellingen zijn uitgeschakeld, kunnen gebruikers geen groepen maken en kunnen ze bestaande groepen waarvan ze een eigenaar zijn niet wijzigen. Ze kunnen echter nog steeds de lidmaatschappen van die groepen beheren en aanvragen van andere gebruikers om lid te worden van hun groep goedkeuren.

U kunt ook **Gebruikers die beveiligingsgroepen kunnen beheren** en **Gebruikers die Office 365-groepen kunnen beheren** gebruiken voor een meer fijnmazig toegangsbeheer over het self-servicegroepsbeheer voor uw gebruikers. Als **Gebruikers kunnen groepen maken** is ingeschakeld, kunnen alle gebruikers in uw tenant nieuwe groepen maken en leden toevoegen aan deze groepen. U kunt geen personen opgeven die hun eigen groepen kunnen maken. U kunt alleen individuen opgeven om een groeps eigenaar te maken van een andere groep.

Door gebruikers in te stellen **die self-service kunnen gebruiken voor beveiligings groepen** en **gebruikers die Office 365-groepen kunnen beheren** op **Ja**, kunt u alle gebruikers in uw Tenant in staat stellen om nieuwe groepen te maken.

U kunt ook **Groep die beveiligingsgroepen kan beheren** of **Groep die Office 365-groepen kan beheren** gebruiken om een enkele groep op te geven waarvan de leden self-service kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over Azure Active Directory.

* [Toegang tot resources beheren met Azure Active Directory-groepen](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory cmdlets for configuring group settings](groups-settings-cmdlets.md) (Azure Active Directory-cmdlets voor het configureren van groepsinstellingen)
* [Application Management in Azure Active Directory](../manage-apps/what-is-application-management.md) (Toepassingsbeheer in Azure Active Directory)
* [Wat is Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
