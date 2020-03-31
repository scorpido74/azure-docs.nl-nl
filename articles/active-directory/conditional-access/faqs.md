---
title: Veelgestelde vragen over azure Active Directory voor voorwaardelijke toegang | Microsoft Documenten
description: Antwoorden op veelgestelde vragen over voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6842338bd27e4bea3436f0b249380ab773d60de6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368085"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Veelgestelde vragen over azure Active Directory voorwaardelijke toegang

## <a name="which-applications-work-with-conditional-access-policies"></a>Welke toepassingen werken met beleid voor voorwaardelijke toegang?

Zie Toepassingen en browsers die voorwaardelijke [toegangsregels gebruiken in Azure Active Directory](concept-conditional-access-cloud-apps.md)voor informatie over toepassingen die werken met beleid voor voorwaardelijke toegang.

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Wordt het beleid voor voorwaardelijke toegang afgedwongen voor B2B-samenwerking en gastgebruikers?

Beleid wordt afgedwongen voor b2b-samenwerkingsgebruikers (business-to-business). In sommige gevallen kan een gebruiker echter niet voldoen aan de beleidsvereisten. De organisatie van een gastgebruiker ondersteunt bijvoorbeeld geen multifactorauthenticatie. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Is een SharePoint Online-beleid ook van toepassing op OneDrive voor Bedrijven?

Ja. Een SharePoint Online-beleid is ook van toepassing op OneDrive voor Bedrijven.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Waarom kan ik geen beleid rechtstreeks instellen voor clientapps, zoals Word of Outlook?

Een beleid voor voorwaardelijke toegang stelt vereisten in voor toegang tot een service. Dit wordt afgedwongen wanneer verificatie voor die service plaatsvindt. Het beleid is niet direct ingesteld op een clienttoepassing. In plaats daarvan wordt het toegepast wanneer een client een service aanroept. Een beleidsset op SharePoint is bijvoorbeeld van toepassing op clients die SharePoint aanroepen. Er is een beleid ingesteld op Exchange dat van toepassing is op Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Is een beleid voor voorwaardelijke toegang van toepassing op serviceaccounts?

Beleid voor voorwaardelijke toegang is van toepassing op alle gebruikersaccounts. Dit geldt ook voor gebruikersaccounts die worden gebruikt als serviceaccounts. Een serviceaccount dat zonder toezicht wordt uitgevoerd, voldoet vaak niet aan de vereisten van een beleid voor voorwaardelijke toegang. Multifactorauthenticatie is bijvoorbeeld vereist. Serviceaccounts kunnen worden uitgesloten van een beleid door gebruik te maken van instellingen voor beleidbeheer voor voorwaardelijke toegang. 

## <a name="are-microsoft-graph-apis-available-for-configuring-conditional-access-policies"></a>Zijn Microsoft Graph API's beschikbaar voor het configureren van beleid voor voorwaardelijke toegang?

Op dit moment, nee. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Wat is het standaarduitsluitingsbeleid voor niet-ondersteunde apparaatplatforms?

Momenteel wordt het beleid voor voorwaardelijke toegang selectief toegepast op gebruikers van iOS- en Android-apparaten. Toepassingen op andere apparaatplatforms worden standaard niet beïnvloed door het beleid voor voorwaardelijke toegang voor iOS- en Android-apparaten. Een tenantbeheerder kan ervoor kiezen het algemene beleid te overschrijven om toegang tot gebruikers op platforms die niet worden ondersteund, te weigeren.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hoe werkt het beleid voor voorwaardelijke toegang voor Microsoft Teams?

Microsoft Teams is sterk afhankelijk van Exchange Online en SharePoint Online voor kernproductiviteitsscenario's, zoals vergaderingen, agenda's en het delen van bestanden. Het beleid voor voorwaardelijke toegang dat is ingesteld voor deze cloud-apps, is van toepassing op Microsoft Teams wanneer een gebruiker zich rechtstreeks aanmeldt bij Microsoft Teams.

Microsoft Teams wordt ook afzonderlijk ondersteund als een cloud-app in azure Active Directory Conditional Access-beleidsregels. Het beleid voor voorwaardelijke toegang dat is ingesteld voor een cloud-app, is van toepassing op Microsoft Teams wanneer een gebruiker zich aanmeldt. Zonder het juiste beleid voor andere apps zoals Exchange Online en SharePoint Online hebben gebruikers mogelijk nog steeds rechtstreeks toegang tot deze bronnen.

Microsoft Teams-bureaubladclients voor Windows en Mac ondersteunen moderne verificatie. Moderne verificatie brengt aanmelding op basis van de Azure Active Directory Authentication Library (ADAL) naar Microsoft Office-clienttoepassingen op verschillende platforms.

## <a name="next-steps"></a>Volgende stappen

- Zie de [aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](best-practices.md)om beleid voor voorwaardelijke toegang voor uw omgeving te configureren. 
