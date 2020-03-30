---
title: Azure AD-verificatie & nationale clouds | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over app-registratie- en verificatieeindpunten voor nationale clouds.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262995"
---
# <a name="national-clouds"></a>Nationale wolken

Nationale clouds zijn fysiek geïsoleerde exemplaren van Azure. Deze regio's van Azure zijn ontworpen om ervoor te zorgen dat vereisten voor gegevensresidentie, soevereiniteit en naleving binnen geografische grenzen worden nageleefd.

Azure Active Directory (Azure AD) wordt met inbegrip van de globale cloud geïmplementeerd in de volgende nationale clouds:  

- Azure Government
- Azure Duitsland
- Azure China 21Vianet

Nationale clouds zijn uniek en een aparte omgeving van Azure global. Het is belangrijk om bewust te zijn van de belangrijkste verschillen tijdens het ontwikkelen van uw toepassing voor deze omgevingen. Verschillen zijn onder andere het registreren van applicaties, het verwerven van tokens en het configureren van eindpunten.

## <a name="app-registration-endpoints"></a>Eindpunten voor app-registratie

Er is een aparte Azure-portal voor elk van de nationale clouds. Als u toepassingen wilt integreren met het Microsoft-identiteitsplatform in een nationale cloud, moet u uw toepassing afzonderlijk registreren in elke Azure-portal die specifiek is voor de omgeving.

In de volgende tabel worden de basis-URL's weergegeven voor de Azure AD-eindpunten die worden gebruikt om een toepassing voor elke nationale cloud te registreren.

| Nationale wolk | Eindpunt van Azure AD-portal |
|----------------|--------------------------|
| Azure AD voor Amerikaanse overheid | `https://portal.azure.us` |
| Azure AD Duitsland | `https://portal.microsoftazure.de` |
| Azure AD China beheerd door 21Vianet | `https://portal.azure.cn` |
| Azure AD (wereldwijde service) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Eindpunten azure AD-verificatie

Alle nationale clouds verifiëren gebruikers afzonderlijk in elke omgeving en hebben afzonderlijke verificatieeindpunten.

In de volgende tabel worden de basis-URL's weergegeven voor de Azure AD-eindpunten die worden gebruikt om tokens voor elke nationale cloud te verkrijgen.

| Nationale wolk | Eindpunt azure AD-verificatie |
|----------------|-------------------------|
| Azure AD voor Amerikaanse overheid | `https://login.microsoftonline.us` |
| Azure AD Duitsland| `https://login.microsoftonline.de` |
| Azure AD China beheerd door 21Vianet | `https://login.chinacloudapi.cn` |
| Azure AD (wereldwijde service)| `https://login.microsoftonline.com` |

U aanvragen voor de Azure AD-autorisatie of tokeneindpunten aanvragen met behulp van de juiste regiospecifieke basis-URL. Bijvoorbeeld voor Azure Germany:

  - Het algemene eindpunt `https://login.microsoftonline.de/common/oauth2/authorize`van autorisatie is .
  - Token gemeenschappelijk `https://login.microsoftonline.de/common/oauth2/token`eindpunt is .

Voor toepassingen met één tenant vervangt u 'gewoon' in de vorige URL's door uw tenant-id of naam. Een voorbeeld is `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Ga naar [Microsoft Graph in nationale cloudimplementaties](https://developer.microsoft.com/graph/docs/concepts/deployments)voor meer informatie over het aanroepen van de API's van Microsoft Graph in een nationale cloudomgeving.

> [!IMPORTANT]
> Bepaalde diensten en functies die zich in specifieke regio's van de wereldwijde service bevinden, zijn mogelijk niet in alle nationale clouds beschikbaar. Ga naar [Producten die beschikbaar](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)zijn per regio om erachter te komen welke services beschikbaar zijn.

Als u wilt weten hoe u een toepassing maken met behulp van het Microsoft-identiteitsplatform, volgt u de zelfstudie van de [Microsoft Authentication Library (MSAL).](msal-national-cloud.md) In het bijzonder zal deze app zich aanmelden bij een gebruiker en een toegangstoken krijgen om de Microsoft Graph API aan te roepen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Duitsland](https://docs.microsoft.com/azure/germany/)
- [Basisbeginselen azure AD-verificatie](authentication-scenarios.md)
