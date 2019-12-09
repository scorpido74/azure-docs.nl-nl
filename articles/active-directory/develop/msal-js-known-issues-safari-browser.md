---
title: Bekende problemen met de Safari-browser (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over problemen met het gebruik van de micro soft-verificatie bibliotheek voor Java script (MSAL. js) met de Safari-browser.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1fda1471d629ab8d84d06a3cc5bcfae0a6d9ec5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916329"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Bekende problemen in de Safari-browser met MSAL. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Vernieuwing van het Silent-token in Safari 12 en ITP 2,0

Apple iOS 12-en MacOS 10,14-besturings systemen bevatten een release van de [Safari 12-browser](https://developer.apple.com/safari/whats-new/). Voor de beveiliging en privacy bevat Safari 12 het [intelligent volgen van 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Hierdoor worden cookies van derden die worden ingesteld, weggehaald. ITP 2,0 behandelt ook de cookies die door id-providers zijn ingesteld als cookies van derden.

### <a name="impact-on-msaljs"></a>Impact op MSAL. js

MSAL. js maakt gebruik van een verborgen iframe voor het uitvoeren van een Silent token Acquisition en vernieuwing als onderdeel van de `acquireTokenSilent`-aanroepen. De aanvragen voor het Silent-token zijn afhankelijk van de iframe die toegang heeft tot de geverifieerde gebruikers sessie, aangeduid door de cookies die door Azure AD zijn ingesteld. Met ITP 2,0 kan geen toegang tot deze cookies worden geblokkeerd, kan MSAL. js geen tokens op de achtergrond verkrijgen en vernieuwen. Dit leidt tot `acquireTokenSilent` fouten.

Er is op dit moment geen oplossing voor dit probleem en we evalueren de opties in de Community Standards.

### <a name="work-around"></a>Tijdelijke oplossing

De ITP-instelling is standaard ingeschakeld in de Safari-browser. U kunt deze instelling uitschakelen door te navigeren naar de **voor keuren** -> **Privacy** en de optie **voor het bijhouden van cross-site voor komen** uit te scha kelen.

![Safari-instelling](./media/msal-js-known-issue-safari-browser/safari.png)

U moet de `acquireTokenSilent` storingen met een interactieve aanvraag voor het ophalen van tokens afhandelen, waarbij de gebruiker wordt gevraagd zich aan te melden.
Om herhaalde aanmeldingen te voor komen, kunt u het beste de `acquireTokenSilent`-fout afhandelen en de gebruiker een optie geven om de instelling ITP in Safari uit te scha kelen voordat u verdergaat met de interactieve oproep. Zodra de instelling is uitgeschakeld, moeten volgende Silent-token vernieuwingen slagen.
