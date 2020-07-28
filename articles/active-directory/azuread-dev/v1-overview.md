---
title: Overzicht van Azure Active Directory voor ontwikkel aars (v 1.0)
description: Dit artikel bevat een overzicht van het aanmelden bij micro soft-werk-en school accounts met behulp van het eind punt en platform van Azure Active Directory v 1.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: db158a50cfc164a0fc839e858cc6e7c46558eb77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551585"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Overzicht van Azure Active Directory voor ontwikkel aars (v 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) is een identiteitsservice in de cloud waarmee ontwikkelaars apps kunnen maken die ervoor zorgen dat elke gebruiker met een Microsoft-werkaccount of -schoolaccount zich veilig kan aanmelden. Azure AD biedt ondersteuning voor ontwikkelaars die Line-Of-Business-apps met één tenant maken en voor ontwikkelaars die apps met meerdere tenants willen ontwikkelen. Naast eenvoudige aanmelding maakt Azure AD ook mogelijk dat apps Microsoft-API's zoals [Microsoft Graph](https://docs.microsoft.com/graph/overview) en aangepaste API's die zijn gemaakt op het Azure AD-platform aanroepen. In deze documentatie wordt beschreven hoe u Azure AD-ondersteuning aan uw app kunt toevoegen met standaardprotocollen zoals OAuth 2.0 en OpenID Connect.

> [!NOTE]
> De meeste inhoud op deze pagina is gericht op het v 1.0-eind punt en het platform, dat alleen micro soft-werk-of school accounts ondersteunt. Als u zich wilt aanmelden bij consumenten-of persoonlijke micro soft-accounts, raadpleegt u de informatie op het [v 2.0-eind punt en-platform](../develop/v2-overview.md). Het v 2.0-eind punt biedt een uniforme ontwikkelaars ervaring voor apps die zich willen aanmelden bij alle micro soft-identiteiten.

- [Basis beginselen van verificatie](v1-authentication-scenarios.md) Een inleiding tot verificatie met Azure AD.
- [Typen toepassingen](app-types.md) Een overzicht van de verificatie scenario's die worden ondersteund door Azure AD.

## <a name="get-started"></a>Aan de slag

De Snelstartgids en zelf studies voor v 1.0 begeleiden u bij het bouwen van een app op uw favoriete platform met behulp van de Azure AD Authentication Library (ADAL) SDK. Raadpleeg de zelf studies **v 1.0 quicks** en **v 1.0** in [micro soft Identity platform (Azure Active Directory voor ontwikkel aars)](index.yml) om aan de slag te gaan.

## <a name="how-to-guides"></a>Instructiegidsen

Raadpleeg de **hand leidingen voor v 1.0** voor gedetailleerde informatie en instructies van de meest voorkomende taken in azure AD.

## <a name="reference-topics"></a>Onderwerpen met naslaginformatie

De volgende artikelen bieden gedetailleerde informatie over API's, protocolberichten en termen die worden gebruikt in Azure AD.

- [Verificatie bibliotheken (ADAL)](active-directory-authentication-libraries.md) Een overzicht van de bibliotheken en Sdk's die worden geleverd door Azure AD.
- [Code voorbeelden](sample-v1-code.md) Een lijst met alle Azure AD-code voorbeelden.
- [Woorden lijst](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Terminologie en definities van woorden die in deze documentatie worden gebruikt.

## <a name="videos"></a>Video's

Zie [Azure Active Directory ontwikkelaars platform Video's](videos.md) voor meer informatie over het migreren naar het nieuwe micro soft-identiteits platform.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
