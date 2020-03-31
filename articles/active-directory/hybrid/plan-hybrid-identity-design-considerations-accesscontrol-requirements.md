---
title: Vereisten voor toegangsbeheer voor hybride identiteitontwerp Azure | Microsoft Documenten
description: Omvat de pijlers van identiteit en het identificeren van toegangsvereisten voor resources voor gebruikers in een hybride omgeving.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60295140"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Toegangscontrolevereisten voor uw hybride identiteitsoplossing bepalen
Wanneer een organisatie hun hybride identiteitsoplossing ontwerpt, kunnen ze deze mogelijkheid ook gebruiken om de toegangsvereisten te bekijken voor de resources die ze van plan zijn om deze beschikbaar te maken voor gebruikers. De toegang tot gegevens kruist alle vier de pijlers van de identiteit, die zijn:

* Beheer
* Authentication
* Autorisatie
* Controleren

De volgende secties hebben betrekking op verificatie en autorisatie in meer details, beheer en controle maken deel uit van de levenscyclus van hybride identiteiten. Lees [Hybride identiteitsbeheertaken bepalen](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) voor meer informatie over deze mogelijkheden.

> [!NOTE]
> Lees [The Four Pillars of Identity - Identity Management in the Age of Hybrid IT](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) voor meer informatie over elk van deze pijlers.
> 
> 

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie
Er zijn verschillende scenario's voor authenticatie en autorisatie, deze scenario's hebben specifieke vereisten waaraan moet worden voldaan door de hybride identiteitsoplossing die het bedrijf gaat gebruiken. Scenario's met betrekking tot Business to Business (B2B) communicatie kan een extra uitdaging voor IT-beheerders, omdat ze nodig hebben om ervoor te zorgen dat de authenticatie en autorisatie methode die wordt gebruikt door de organisatie kan communiceren met hun zakelijke partners. Controleer tijdens het ontwerpproces voor verificatie- en autorisatievereisten of de volgende vragen worden beantwoord:

* Zal uw organisatie alleen gebruikers verifiëren en autoriseren die zich op hun identiteitsbeheersysteem bevinden?
  * Zijn er plannen voor B2B-scenario's?
  * Zo ja, weet u al welke protocollen (SAML, OAuth, Kerberos of Certificaten) zullen worden gebruikt om beide bedrijven met elkaar te verbinden?
* Ondersteunt de hybride identiteitsoplossing die u gaat aannemen deze protocollen?

Een ander belangrijk punt om te overwegen is waar de verificatierepository die door gebruikers en partners zal worden gebruikt, zich zal bevinden en het te gebruiken administratieve model. Overweeg de volgende twee kernopties:

* Gecentraliseerd: in dit model kunnen de referenties, het beleid en het beheer van de gebruiker on-premises of in de cloud worden gecentraliseerd.
* Hybride: in dit model worden de referenties, het beleid en het beheer van de gebruiker on-premises gecentraliseerd en gerepliceerd in de cloud.

Welk model uw organisatie zal aannemen, is afhankelijk van hun zakelijke vereisten, u wilt de volgende vragen beantwoorden om te bepalen waar het identiteitsbeheersysteem zich zal bevinden en de administratieve modus die u moet gebruiken:

* Heeft uw organisatie momenteel een on-premises identiteitsbeheer?
  * Zo ja, zijn ze van plan om het te houden?
  * Zijn er regelgevings- of nalevingsvereisten waaraan uw organisatie moet voldoen die bepaalt waar het identiteitsbeheersysteem zich moet bevinden?
* Gebruikt uw organisatie eenmalige aanmelding voor apps die zich on-premises of in de cloud bevinden?
  * Zo ja, heeft de goedkeuring van een hybride identiteitsmodel invloed op dit proces?

## <a name="access-control"></a>Toegangsbeheer
Hoewel verificatie en autorisatie kernelementen zijn om toegang tot bedrijfsgegevens mogelijk te maken via de validatie van de gebruiker, is het ook belangrijk om het toegangsniveau te bepalen dat deze gebruikers zullen hebben en het niveau van toegangsbeheerders over de resources die ze beheren. Uw hybride identiteitsoplossing moet gedetailleerde toegang kunnen bieden tot bronnen, delegatie en toegangscontrole voor de basisvan rollen. Controleer of de volgende vraag wordt beantwoord met betrekking tot toegangscontrole:

* Heeft uw bedrijf meer dan één gebruiker met een verhoogd voorrecht om uw identiteitssysteem te beheren?
  * Zo ja, heeft elke gebruiker hetzelfde toegangsniveau nodig?
* Moet uw bedrijf de toegang tot gebruikers delegeren om specifieke bronnen te beheren?
  * Zo ja, hoe vaak gebeurt dit?
* Moet uw bedrijf toegangsbeheermogelijkheden integreren tussen on-premises en cloudresources?
* Moet uw bedrijf de toegang tot resources beperken volgens bepaalde voorwaarden?
* Zou uw bedrijf een toepassing hebben die aangepaste controletoegang tot sommige bronnen nodig heeft?
  * Zo ja, waar bevinden die apps zich (on-premises of in de cloud)?
  * Zo ja, waar bevinden die doelbronnen zich (on-premises of in de cloud)?

> [!NOTE]
> Zorg ervoor dat u elk antwoord noteert de logica achter het antwoord begrijpt. [Define Data Protection Strategy](plan-hybrid-identity-design-considerations-data-protection-strategy.md) zal gaan over de beschikbare opties en voordelen / nadelen van elke optie.  Door deze vragen te beantwoorden selecteert u welke optie het beste bij uw bedrijfsbehoeften past.
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor reageren op incidenten vaststellen](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

