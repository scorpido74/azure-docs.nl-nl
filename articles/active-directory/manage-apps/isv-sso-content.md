---
title: SSO inschakelen voor uw toepassing met meerdere tenants
description: Richtlijnen voor onafhankelijke softwareleveranciers bij de integratie met Azure active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795186"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Eén aanmelding inschakelen voor uw multi-tenant toepassing  

Wanneer u uw toepassing aanbiedt voor gebruik door andere bedrijven via een aankoop of abonnement, stelt u uw toepassing beschikbaar voor klanten binnen hun eigen Azure-tenants. Dit staat bekend als het maken van een multi-tenant applicatie. Zie [Multitenant-toepassingen in Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) en [Tenancy in Azure Active Directory](../develop/single-and-multi-tenant-apps.md)voor een overzicht van dit concept.

## <a name="what-is-single-sign-on"></a>Wat is Single Sign-On

Single sign-on (SSO) voegt beveiliging en gemak toe wanneer gebruikers zich aanmelden bij toepassingen met Azure Active Directory en andere identiteiten. Wanneer een toepassing is ingeschakeld, hoeven gebruikers geen afzonderlijke referenties in te voeren om toegang te krijgen tot die toepassing. Voor een volledige uitleg van Single sign-on. [Zie Enkele aanmelding voor toepassingen in Azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Waarom Eenmalige aanmelding inschakelen in uw toepassing?

Er zijn vele voordelen aan het inschakelen van SSO in uw multi-tenant applicatie. Wanneer u SSO inschakelt voor uw toepassing:

* Uw toepassing kan worden weergegeven in de Azure Marketplace, waar uw app door miljoenen organisaties kan worden ontdekt met Azure Active Directory.
  * Hiermee kunnen klanten de toepassing snel configureren met Azure AD.

* Uw toepassing kan worden vindbaar in de Office 365 App Gallery, het Office 365 App Launcher en in Microsoft Search op Office.com

* Uw toepassing kan de Microsoft Graph REST API gebruiken om toegang te krijgen tot de gegevens die de productiviteit van gebruikers stimuleren die beschikbaar is in de Microsoft Graph.

* U verlaagt de ondersteuningskosten door het uw klanten gemakkelijker te maken.
  * Toepassingsspecifieke documentatie die is in samenwerking met het Azure AD-team voor onze wederzijdse klanten, vergemakkelijkt de acceptatie.
  * Als SSO met één klik is ingeschakeld, hoeven de IT-beheerders van uw klanten niet te leren hoe ze uw toepassing kunnen configureren voor gebruik in hun organisatie.

* U biedt uw klanten de mogelijkheid om de authenticatie en autorisatie van hun werknemers- en gastidentiteiten volledig te beheren.

  * Het plaatsen van alle verantwoordelijkheid voor accountbeheer en compliance bij de klanteigenaar van die identiteiten.

  * De mogelijkheid bieden om SSO in of uit te schakelen voor specifieke identiteitsproviders, groepen of gebruikers om aan hun bedrijfsbehoeften te voldoen.

* U verhoogt uw verhandelbaarheid en adopteerbaarheid. Veel grote organisaties eisen dat (of streven naar) hun medewerkers hebben naadloze SSO ervaringen in alle toepassingen. Het is belangrijk om SSO gemakkelijk te maken.

* U vermindert de frictie voor eindgebruikers, waardoor het gebruik van eindgebruikers kan toenemen en uw inkomsten kan toenemen.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Aanmelden voor eenmalig aanmelden in uw gepubliceerde toepassing inschakelen

1. [Kies het juiste federatieprotocol voor uw multi-tenant toepassing.](isv-choose-multi-tenant-federation.md)
1. SSO implementeren in uw applicatie
   - Zie [richtlijnen voor verificatiepatronen](../develop/v2-app-types.md)
   - Zie [Azure active Directory code samples](../develop/sample-v2-code.md) voor OIDC- en OAuth-protocollen
1. [Uw Azure-tenant maken](isv-tenant-multi-tenant-app.md) en uw toepassing testen
1. [SSO-documentatie maken en publiceren op uw site.](isv-create-sso-documentation.md)
1. [Verzend uw toepassingsvermelding](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) en werk samen met Microsoft om documentatie op de site van Microsoft te maken.
1. [Word lid van het Microsoft Partner Network (gratis) en maak uw go to-market-abonnement](https://partner.microsoft.com/en-us/explore/commercial#gtm).
