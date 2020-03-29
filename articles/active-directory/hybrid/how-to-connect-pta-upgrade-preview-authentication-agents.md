---
title: Azure AD Connect - Verificatie doordedoorgevening - Auth-agents upgraden | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u uw Azure Active Directory (Azure AD) Pass-through Authentication-configuratie upgraden.
services: active-directory
keywords: Azure AD Connect Pass-Through Authentication, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 494ccc3b90b8c249ee935087dcf0f0b5264b02ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386744"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory Pass-Through-verificatie: verificatieagents voor upgradevoorbeelden

## <a name="overview"></a>Overzicht

Dit artikel is voor klanten die Azure AD Pass-through Authentication gebruiken via preview. We hebben onlangs een upgrade (en omgedoopt) de Authentication Agent software. U moet _voorafs_ voorafverificatieagents handmatig upgraden die op uw on-premises servers zijn geïnstalleerd. Deze handmatige upgrade is slechts een eenmalige actie. Alle toekomstige updates voor verificatieagents worden automatisch uitgevoerd. De redenen om te upgraden zijn als volgt:

- De preview-versies van verificatieagents ontvangen geen verdere beveiligings- of bugfixes.
-   De preview-versies van verificatieagents kunnen niet worden geïnstalleerd op extra servers, voor hoge beschikbaarheid.

## <a name="check-versions-of-your-authentication-agents"></a>Versies van uw verificatieagents controleren

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Stap 1: Controleren waar uw verificatieagents zijn geïnstalleerd

Volg de volgende stappen om te controleren waar uw verificatieagents zijn geïnstalleerd:

1. Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met de globale beheerdersreferenties voor uw tenant.
2. Selecteer **Azure Active Directory** op de linkernavigatie.
3. Selecteer **Azure AD Connect**. 
4. Selecteer **Doorgeefverificatie**. Dit blad geeft een overzicht van de servers waar uw verificatieagents zijn geïnstalleerd.

![Azure Active Directory-beheercentrum - Verificatieblad voor doorgeefverificatie](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Stap 2: De versies van uw verificatieagents controleren

Volg de volgende instructies om de versies van uw verificatiemedewerkers op elke server die in de vorige stap is geïdentificeerd, te controleren:

1. Ga naar **Het Configuratiescherm -> programma's -> programma's en functies** op de on-premises server.
2. Als er een vermelding is voor "**Microsoft Azure AD Connect Authentication Agent**", hoeft u geen actie te ondernemen op deze server.
3. Als er een vermelding is voor "**Microsoft Azure AD Application Proxy Connector**", moet u handmatig upgraden op deze server.

![Voorbeeldversie van verificatieagent](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Aanbevolen procedures om te volgen voordat u met de upgrade begint

Voordat u een upgrade uitvoert, moet u ervoor zorgen dat u de volgende items hebt:

1. **Cloud-only Global Administrator-account maken:** upgrade niet zonder dat u een Global Administrator-account in de cloud hebt om te gebruiken in noodsituaties waarin uw pass-through-verificatieagents niet goed werken. Meer informatie over [het toevoegen van een global administrator-account met alleen cloud](../active-directory-users-create-azure-portal.md). Het doen van deze stap is van cruciaal belang en zorgt ervoor dat u niet buitengesloten van uw huurder.
2.  **Zorg voor hoge beschikbaarheid:** Als deze niet eerder is voltooid, installeer dan een tweede zelfstandige verificatieagent om met deze [instructies](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)een hoge beschikbaarheid voor aanmeldingsverzoeken te bieden.

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>De verificatieagent upgraden op uw Azure AD Connect-server

U moet Azure AD Connect upgraden voordat u de verificatieagent op dezelfde server upgradet. Volg de volgende stappen op zowel uw primaire als tijdelijke Azure AD Connect-servers:

1. **Azure AD Connect upgraden:** volg dit [artikel](how-to-upgrade-previous-version.md) en upgrade naar de nieuwste Azure AD Connect-versie.
2. **Verwijder de preview-versie van de verificatieagent:** download [dit PowerShell-script](https://aka.ms/rmpreviewagent) en voer deze uit als beheerder op de server.
3. **Download de nieuwste versie van de verificatieagent (versies 1.5.389.0 of hoger):** Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met de globale beheerdersreferenties van uw tenant. Selecteer **Azure Active Directory -> Azure AD Connect -> Pass-through Authentication -> Download agent**. Accepteer de [servicevoorwaarden](https://aka.ms/authagenteula) en download de nieuwste versie van de verificatieagent. U de verificatieagent ook [vanaf hier](https://aka.ms/getauthagent)downloaden.
4. **Installeer de nieuwste versie van de verificatieagent:** Voer de uitvoerbare downloadde uit in stap 3 uit. Geef de globale beheerdersreferenties van uw tenant op wanneer daarom wordt gevraagd.
5. **Controleer of de nieuwste versie is geïnstalleerd:** Zoals eerder is weergegeven, gaat u naar **Het Configuratiescherm -> Programma's -> programma's en onderdelen** en controleer of er een vermelding is voor " Microsoft Azure AD Connect Authentication**Agent**".

>[!NOTE]
>Als u het verificatieblad pass-through controleert in het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) nadat u de voorgaande stappen hebt voltooid, ziet u twee verificatieagentvermeldingen per server : één vermelding met de verificatieagent als **actief** en het andere als **Inactief**. Dit wordt _verwacht_. De **inactieve** vermelding wordt na een paar dagen automatisch verwijderd.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>De verificatieagent upgraden op andere servers

Volg deze stappen om verificatieagents te upgraden op andere servers (waar Azure AD Connect niet is geïnstalleerd):

1. **Verwijder de preview-versie van de verificatieagent:** download [dit PowerShell-script](https://aka.ms/rmpreviewagent) en voer deze uit als beheerder op de server.
2. **Download de nieuwste versie van de verificatieagent (versies 1.5.389.0 of hoger):** Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met de globale beheerdersreferenties van uw tenant. Selecteer **Azure Active Directory -> Azure AD Connect -> Pass-through Authentication -> Download agent**. Accepteer de servicevoorwaarden en download de nieuwste versie.
3. **Installeer de nieuwste versie van de verificatieagent:** Voer de uitvoerbare downloadde uit in stap 2. Geef de globale beheerdersreferenties van uw tenant op wanneer daarom wordt gevraagd.
4. **Controleer of de nieuwste versie is geïnstalleerd:** Zoals eerder is weergegeven, gaat u naar **configuratiescherm -> Programma's -> programma's en onderdelen** en controleer of er een vermelding is met de naam **Microsoft Azure AD Connect Authentication Agent**.

>[!NOTE]
>Als u het verificatieblad pass-through controleert in het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) nadat u de voorgaande stappen hebt voltooid, ziet u twee verificatieagentvermeldingen per server : één vermelding met de verificatieagent als **actief** en het andere als **Inactief**. Dit wordt _verwacht_. De **inactieve** vermelding wordt na een paar dagen automatisch verwijderd.

## <a name="next-steps"></a>Volgende stappen
- [**Problemen oplossen**](tshoot-connect-pass-through-authentication.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
