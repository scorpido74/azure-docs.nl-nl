---
title: Overzicht van Azure Active Directory voor ontwikkel aars (v 1.0)
description: Dit artikel bevat een overzicht van het aanmelden bij micro soft-werk-en school accounts met behulp van het eind punt en platform van Azure Active Directory v 1.0.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e790728cbc7415532754d10773d466e9a0280422
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373908"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Overzicht van Azure Active Directory voor ontwikkel aars (v 1.0)

Azure Active Directory (Azure AD) is een identiteitsservice in de cloud waarmee ontwikkelaars apps kunnen maken die ervoor zorgen dat elke gebruiker met een Microsoft-werkaccount of -schoolaccount zich veilig kan aanmelden. Azure AD biedt ondersteuning voor ontwikkelaars die Line-Of-Business-apps met één tenant maken en voor ontwikkelaars die apps met meerdere tenants willen ontwikkelen. Naast eenvoudige aanmelding maakt Azure AD ook mogelijk dat apps Microsoft-API's zoals [Microsoft Graph](https://docs.microsoft.com/graph/overview) en aangepaste API's die zijn gemaakt op het Azure AD-platform aanroepen. In deze documentatie wordt beschreven hoe u Azure AD-ondersteuning aan uw app kunt toevoegen met standaardprotocollen zoals OAuth 2.0 en OpenID Connect.

> [!NOTE]
> De meeste inhoud op deze pagina is gericht op het v 1.0-eind punt en het platform, dat alleen micro soft-werk-of school accounts ondersteunt. Als u zich wilt aanmelden bij consumenten-of persoonlijke micro soft-accounts, raadpleegt u de informatie op het [v 2.0-eind punt en-platform](v2-overview.md). Het v 2.0-eind punt biedt een uniforme ontwikkelaars ervaring voor apps die zich willen aanmelden bij alle micro soft-identiteiten.

| | |
| --- | --- |
|[De basisbeginselen van verificatie](v1-authentication-scenarios.md) | Een inleiding tot verificatie met Azure AD. |
|[Soorten toepassingen](app-types.md) | Een overzicht van de verificatiescenario's die worden ondersteund door Azure AD. |
| | |

## <a name="get-started"></a>Aan de slag

De Snelstartgids en zelf studies voor v 1.0 begeleiden u bij het bouwen van een app op uw favoriete platform met behulp van de Azure AD Authentication Library (ADAL) SDK. Raadpleeg de zelf studies **v 1.0 quicks** en **v 1.0** in [micro soft Identity platform (Azure Active Directory voor ontwikkel aars)](index.yml) om aan de slag te gaan.

## <a name="how-to-guides"></a>Handleidingen

Raadpleeg de **hand leidingen voor v 1.0** voor gedetailleerde informatie en instructies van de meest voorkomende taken in azure AD.

## <a name="reference-topics"></a>Onderwerpen met naslaginformatie

De volgende artikelen bieden gedetailleerde informatie over API's, protocolberichten en termen die worden gebruikt in Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Verificatiebibliotheken (ADAL)](active-directory-authentication-libraries.md)   | Een overzicht van de bibliotheken en SDK's die worden geleverd door Azure AD. |
| [Codevoorbeelden](sample-v1-code.md)                                  | Een lijst met alle Azure AD-codevoorbeelden. |
| [Woordenlijst](developer-glossary.md)                                      | Termen en definities van woorden die in deze documenten worden gebruikt. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
