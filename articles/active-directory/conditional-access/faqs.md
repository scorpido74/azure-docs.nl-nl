---
title: Veelgestelde vragen over voorwaardelijke toegang Azure Active Directory | Microsoft Docs
description: Krijg antwoorden op veelgestelde vragen over voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1704fb50586168be6f960e62b918019cb67ce4c8
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167805"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Veelgestelde vragen over voorwaardelijke toegang Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Welke toepassingen werken met beleid voor voorwaardelijke toegang?

Zie [toepassingen en browsers die gebruikmaken van regels voor voorwaardelijke toegang in azure Active Directory](concept-conditional-access-cloud-apps.md)voor meer informatie over toepassingen die werken met beleid voor voorwaardelijke toegang.

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Zijn beleids regels voor voorwaardelijke toegang afgedwongen voor B2B-samen werking en gast gebruikers?

Beleids regels worden afgedwongen voor samenwerkings gebruikers van Business-to-Business (B2B). In sommige gevallen kan een gebruiker echter mogelijk niet voldoen aan de beleids vereisten. Zo kan de organisatie van een gast gebruiker geen multi-factor Authentication ondersteunen. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>Is er ook een share point online-beleid van toepassing op OneDrive voor bedrijven?

Ja. Een share point online-beleid is ook van toepassing op OneDrive voor bedrijven. Zie het artikel [service afhankelijkheden van voorwaardelijke toegang](service-dependencies.md) voor meer informatie en stel in plaats daarvan het doel beleid toe aan de [Office 365-app](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Waarom kan ik een beleid niet rechtstreeks instellen op client-apps, zoals Word of Outlook?

Een beleid voor voorwaardelijke toegang stelt vereisten in voor het openen van een service. Deze wordt afgedwongen wanneer verificatie voor die service plaatsvindt. Het beleid is niet rechtstreeks ingesteld voor een client toepassing. In plaats daarvan wordt deze toegepast wanneer een-client een service aanroept. Een beleid dat is ingesteld op share point is bijvoorbeeld van toepassing op clients die share point aanroepen. Een beleid dat is ingesteld op Exchange, is van toepassing op Outlook. Zie het artikel [service afhankelijkheden van voorwaardelijke toegang](service-dependencies.md) voor meer informatie en stel in plaats daarvan het doel beleid toe aan de [Office 365-app](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Is er een beleid voor voorwaardelijke toegang van toepassing op service accounts?

Beleid voor voorwaardelijke toegang is van toepassing op alle gebruikers accounts. Dit geldt ook voor gebruikers accounts die worden gebruikt als service accounts. Vaak kan een service account dat zonder toezicht wordt uitgevoerd, niet voldoen aan de vereisten van een beleid voor voorwaardelijke toegang. Multi-factor Authentication kan bijvoorbeeld vereist zijn. Service accounts kunnen worden uitgesloten van een beleid met behulp van een uitsluiting van een [gebruiker of groep](concept-conditional-access-users-groups.md#exclude-users). 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Wat is het standaard uitsluitings beleid voor niet-ondersteunde platformen?

Momenteel worden beleids regels voor voorwaardelijke toegang selectief afgedwongen voor gebruikers van iOS-en Android-apparaten. Toepassingen op andere platformen zijn standaard niet van invloed op het beleid voor voorwaardelijke toegang voor iOS-en Android-apparaten. Een Tenant beheerder kan ervoor kiezen om het globale beleid te negeren om toegang te weigeren aan gebruikers op platforms die niet worden ondersteund.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Hoe werkt het beleid voor voorwaardelijke toegang voor micro soft-teams?

Micro soft teams is sterk afhankelijk van Exchange Online en share point online voor essentiële productiviteits scenario's, zoals vergaderingen, agenda's en het delen van bestanden. Beleid voor voorwaardelijke toegang dat is ingesteld voor deze Cloud-apps, is van toepassing op micro soft-teams wanneer een gebruiker zich rechtstreeks aanmeldt bij micro soft-teams.

Micro soft teams worden ook afzonderlijk ondersteund als een Cloud-app in beleids regels voor voorwaardelijke toegang. Beleid voor voorwaardelijke toegang dat voor een Cloud-app is ingesteld, is van toepassing op micro soft-teams wanneer een gebruiker zich aanmeldt. Maar zonder het juiste beleid voor andere apps, zoals Exchange Online en share point online-gebruikers, kunnen ze nog steeds rechtstreeks toegang hebben tot deze bronnen.

Micro soft teams bureau blad-clients voor Windows en Mac bieden ondersteuning voor moderne verificatie. Moderne verificatie brengt aanmelden op basis van de Azure Active Directory Authentication Library (ADAL) om client toepassingen te Microsoft Office op verschillende platforms.

Zie het artikel [service afhankelijkheden van voorwaardelijke toegang](service-dependencies.md) voor meer informatie en stel in plaats daarvan het doel beleid toe aan de [Office 365-app](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="next-steps"></a>Volgende stappen

- Zie de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md)voor het configureren van beleid voor voorwaardelijke toegang voor uw omgeving. 
