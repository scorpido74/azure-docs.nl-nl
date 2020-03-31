---
title: Overzicht van Azure Active Directory voor ontwikkelaars (v1.0)
description: In dit artikel vindt u een overzicht van het ondertekenen in Microsoft-werk- en schoolaccounts met behulp van het Azure Active Directory v1.0-eindpunt en -platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ade350c91ebd2f3a68b52011e598f739a14c220f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154488"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Overzicht van Azure Active Directory voor ontwikkelaars (v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) is een identiteitsservice in de cloud waarmee ontwikkelaars apps kunnen maken die ervoor zorgen dat elke gebruiker met een Microsoft-werkaccount of -schoolaccount zich veilig kan aanmelden. Azure AD biedt ondersteuning voor ontwikkelaars die Line-Of-Business-apps met één tenant maken en voor ontwikkelaars die apps met meerdere tenants willen ontwikkelen. Naast eenvoudige aanmelding maakt Azure AD ook mogelijk dat apps Microsoft-API's zoals [Microsoft Graph](https://docs.microsoft.com/graph/overview) en aangepaste API's die zijn gemaakt op het Azure AD-platform aanroepen. In deze documentatie wordt beschreven hoe u Azure AD-ondersteuning aan uw app kunt toevoegen met standaardprotocollen zoals OAuth 2.0 en OpenID Connect.

> [!NOTE]
> Het grootste deel van de inhoud op deze pagina richt zich op het v1.0-eindpunt en -platform, dat alleen Microsoft-werk- of schoolaccounts ondersteunt. Als u zich wilt aanmelden voor consumenten- of persoonlijke Microsoft-accounts, raadpleegt u de informatie op het [v2.0-eindpunt en -platform](../develop/v2-overview.md). Het v2.0-eindpunt biedt een uniforme ontwikkelaarservaring voor apps die zich willen aanmelden bij alle Microsoft-identiteiten.

| | |
| --- | --- |
|[Basisbeginselen van verificatie](v1-authentication-scenarios.md) | Een inleiding tot verificatie met Azure AD. |
|[Soorten toepassingen](app-types.md) | Een overzicht van de verificatiescenario's die worden ondersteund door Azure AD. |
| | |

## <a name="get-started"></a>Aan de slag

De v1.0 quickstarts en tutorials leiden u door het bouwen van een app op uw voorkeursplatform met behulp van de Azure AD Authentication Library (ADAL) SDK. Bekijk de **v1.0 Quickstarts** en **v1.0 Tutorials** in [Microsoft Identity Platform (Azure Active Directory for developers)](index.yml) om aan de slag te gaan.

## <a name="how-to-guides"></a>Handleidingen

Zie de **handleidingen v1.0 How-to** voor gedetailleerde informatie en walkthroughs van de meest voorkomende taken in Azure AD.

## <a name="reference-topics"></a>Onderwerpen met naslaginformatie

De volgende artikelen bieden gedetailleerde informatie over API's, protocolberichten en termen die worden gebruikt in Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Verificatiebibliotheken (ADAL)](active-directory-authentication-libraries.md)   | Een overzicht van de bibliotheken en SDK's die worden geleverd door Azure AD. |
| [Codevoorbeelden](sample-v1-code.md)                                  | Een lijst met alle Azure AD-codevoorbeelden. |
| [Woordenlijst](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | Termen en definities van woorden die in deze documenten worden gebruikt. |
|  |  |

## <a name="videos"></a>Video's

Zie [Azure Active Directory-ontwikkelaarsplatformvideo's](videos.md) voor hulp bij het migreren naar het nieuwe Microsoft-identiteitsplatform.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
