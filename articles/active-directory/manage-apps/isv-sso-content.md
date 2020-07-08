---
title: SSO inschakelen voor uw toepassing met meerdere tenants
description: Richt lijnen voor onafhankelijke software leveranciers over de integratie met Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26fa2b82b89c7b9ce1e05062718f2c4d0c93197c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763275"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Eenmalige aanmelding inschakelen voor uw toepassing met meerdere tenants  

Wanneer u uw toepassing aanbiedt voor gebruik door andere bedrijven via een aankoop of abonnement, maakt u uw toepassing beschikbaar voor klanten binnen hun eigen Azure-tenants. Dit staat bekend als het maken van een toepassing met meerdere tenants. Zie voor meer informatie over dit concept [multi tenant-toepassingen in azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) en [pacht in azure Active Directory](../develop/single-and-multi-tenant-apps.md).

## <a name="what-is-single-sign-on"></a>Wat is eenmalige aanmelding?

Eenmalige aanmelding (SSO) voegt beveiligings-en gebruiks gemak toe wanneer gebruikers zich aanmelden bij toepassingen met behulp van Azure Active Directory en andere identiteiten. Wanneer een toepassing SSO is ingeschakeld, hoeven gebruikers geen afzonderlijke referenties in te voeren voor toegang tot de toepassing. Voor een volledige uitleg van eenmalige aanmelding. [Zie eenmalige aanmelding bij toepassingen in azure Active Directory](what-is-single-sign-on.md).

## <a name="why-enable-single-sign-on-in-your-application"></a>Hoe kan ik eenmalige aanmelding in uw toepassing inschakelen?

Er zijn veel voor delen om SSO in te scha kelen in uw toepassing met meerdere tenants. Wanneer u SSO inschakelt voor uw toepassing:

* Uw toepassing kan worden vermeld op de Azure Marketplace, waar uw app kan worden gedetecteerd door miljoenen organisaties die gebruikmaken van Azure Active Directory.
  * Hiermee kunnen klanten de toepassing snel configureren met Azure AD.

* Uw toepassing kan worden gedetecteerd in de Office 365-app-galerie, het start programma voor Office 365-apps en in micro soft Search op Office.com

* Uw toepassing kan de Microsoft Graph REST API gebruiken om toegang te krijgen tot de gegevens die de gebruikers productiviteit beschikt die beschikbaar is via de Microsoft Graph.

* U verlaagt de ondersteunings kosten door het gemakkelijker te maken voor uw klanten.
  * Toepassingsspecifieke documentatie die is geproduceerd met het Azure AD-team voor onze wederzijdse klanten, vereenvoudigt de acceptatie.
  * Als eenmalige aanmelding is ingeschakeld, hebben de IT-beheerders van uw klanten geen informatie over het configureren van uw toepassing voor gebruik in hun organisatie.

* U geeft uw klanten de mogelijkheid om de verificatie en autorisatie van hun werk nemers en gast identiteiten volledig te beheren.

  * Het plaatsen van alle account beheer en nalevings verantwoordelijkheid met de klant eigenaar van deze identiteiten.

  * Biedt de mogelijkheid om eenmalige aanmelding in of uit te scha kelen voor specifieke id-providers, groepen of gebruikers om te voldoen aan de behoeften van hun bedrijf.

* U kunt uw markt en goed keuring verg Roten. Veel grote organisaties vereisen dat (of streven ernaar) hun werk nemers naadloze SSO-ervaringen hebben in alle toepassingen. Het is belang rijk om SSO eenvoudig te maken.

* U vermindert de wrijving van de eind gebruiker, waardoor het gebruik van de eind gebruiker kan toenemen en uw omzet kan worden verhoogd.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Eenmalige aanmelding inschakelen in uw gepubliceerde toepassing

1. [Kies het juiste Federatie protocol voor uw multi tenant-toepassing](isv-choose-multi-tenant-federation.md).
1. SSO in uw toepassing implementeren
   - Zie de [richt lijnen voor verificatie patronen](../develop/v2-app-types.md)
   - Zie [code voorbeelden van Azure Active Directory](../develop/sample-v2-code.md) voor OIDC en OAuth-protocollen
1. [Uw Azure-Tenant maken](isv-tenant-multi-tenant-app.md) en uw toepassing testen
1. [Maak en PUBLICEER SSO-documentatie op uw site](isv-create-sso-documentation.md).
1. [Dien de aanbieding](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) en partner van uw toepassing in bij micro soft om documentatie te maken op de site van micro soft.
1. [Word lid van de Microsoft Partner Network (gratis) en maak uw Go to Market-abonnement](https://partner.microsoft.com/en-us/explore/commercial#gtm).
