---
title: Bekende Safari browser problemen (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over bekende problemen bij het gebruik van de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js) met de Safari-browser.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696109"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Bekende problemen op Safari browser met MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Stille tokenverlenging op Safari 12 en ITP 2.0

Apple iOS 12 en MacOS 10.14 besturingssystemen opgenomen een release van de [Safari 12 browser](https://developer.apple.com/safari/whats-new/). Voor de veiligheid en privacy bevat Safari 12 de [Intelligent Tracking Prevention 2.0.](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) Dit zorgt er in wezen voor dat de browser cookies van derden laat vallen die worden ingesteld. ITP 2.0 behandelt ook de cookies die door identiteitsproviders zijn geplaatst als cookies van derden.

### <a name="impact-on-msaljs"></a>Impact op MSAL.js

MSAL.js gebruikt een verborgen Iframe om stille token `acquireTokenSilent` acquisitie en vernieuwing uit te voeren als onderdeel van de gesprekken. De stille tokenaanvragen zijn afhankelijk van het Iframe dat toegang heeft tot de geverifieerde gebruikerssessie die wordt weergegeven door de cookies die zijn ingesteld door Azure AD. Met ITP 2.0 die de toegang tot deze cookies verhindert, slaagt MSAL.js er niet in om tokens in stilte te verwerven en te vernieuwen en dit resulteert in `acquireTokenSilent` fouten.

Er is geen oplossing voor dit probleem op dit punt en we zijn de evaluatie van opties met de normen gemeenschap.

### <a name="work-around"></a>Werken rond

Standaard is de ITP-instelling ingeschakeld in de Safari-browser. U deze instelling uitschakelen door te navigeren naar **Voorkeuren** -> **Privacy** en de controle uit de optie **Cross-site tracking voorkomen.**

![safari-instelling](./media/msal-js-known-issue-safari-browser/safari.png)

U moet de `acquireTokenSilent` fouten afhandelen met een interactief tokengesprek voor aanschaffen, waardoor de gebruiker wordt gevraagd zich aan te melden.
Om herhaalde aanmeldingen te voorkomen, u de `acquireTokenSilent` fout aanpakken en de gebruiker de optie bieden om de ITP-instelling in Safari uit te schakelen voordat u verdergaat met het interactieve gesprek. Zodra de instelling is uitgeschakeld, moeten latere stille tokenverlengingen slagen.
