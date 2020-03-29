---
title: Problemen in Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over bekende problemen bij het gebruik van de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js) met Internet Explorer- en Microsoft Edge-browsers.
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
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696092"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Bekende problemen in Internet Explorer- en Microsoft Edge-browsers (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemen als gevolg van beveiligingszones
We hadden meerdere meldingen van problemen met authenticatie in IE en Microsoft Edge (sinds de update van de *Microsoft Edge-browserversie naar 40.15063.0.0).* We volgen deze en hebben het Microsoft Edge-team op de hoogte gebracht. Terwijl Microsoft Edge werkt aan een oplossing, hier is een beschrijving van de vaak voorkomende problemen en de mogelijke oplossingen die kunnen worden geïmplementeerd.

### <a name="cause"></a>Oorzaak
De oorzaak voor de meeste van deze problemen is als volgt. De sessieopslag en lokale opslag worden verdeeld door beveiligingszones in de Microsoft Edge-browser. In deze specifieke versie van Microsoft Edge worden, wanneer de toepassing wordt omgeleid over zones, de sessieopslag en lokale opslag gewist. In het bijzonder wordt de sessieopslag gewist in de reguliere browsernavigatie en worden zowel de sessie als de lokale opslag gewist in de InPrivate-modus van de browser. MSAL.js slaat bepaalde status op in de sessieopslag en vertrouwt op het controleren van deze status tijdens de verificatiestromen. Wanneer de sessieopslag wordt gewist, gaat deze status verloren en resulteert dit in verbroken ervaringen.

### <a name="issues"></a>Problemen

- **Oneindige omleidingslussen en paginaherlaadt tijdens verificatie**. Wanneer gebruikers zich aanmelden bij de toepassing op Microsoft Edge, worden ze omgeleid terug van de AAD-inlogpagina en zitten ze vast in een oneindige omleidingslus, wat resulteert in herhaalde paginaherladen. Dit gaat meestal `invalid_state` gepaard met een fout in de sessieopslag.

- **Infinite acquire token loops and AADSTS50058 error**. Wanneer een toepassing die op Microsoft Edge wordt uitgevoerd, een token voor een resource probeert te verkrijgen, kan de toepassing vast komen te zitten in een oneindige lus van het aanveeltokengesprek, samen met de volgende fout van AAD in uw netwerktracering:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Pop-upvenster wordt niet gesloten of zit vast bij het gebruik van aanmelding via Popup om te verifiëren.** Wanneer u door het pop-upvenster in Microsoft Edge of IE(InPrivate) wordt geauthenticeren, sluit het pop-upvenster na het invoeren van referenties en het aanmelden, als meerdere domeinen in de beveiligingszones betrokken zijn bij de navigatie, het pop-upvenster niet omdat MSAL.js de greep verliest het pop-upvenster.  

### <a name="update-fix-available-in-msaljs-023"></a>Update: Oplossing beschikbaar in MSAL.js 0.2.3
Oplossingen voor de problemen met de verificatieomleidingslus zijn vrijgegeven in [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Schakel de `storeAuthStateInCookie` vlag in de MSAL.js config in om gebruik te maken van deze fix. Standaard is deze vlag ingesteld op false.

Wanneer `storeAuthStateInCookie` de vlag is ingeschakeld, gebruikt MSAL.js de browsercookies om de aanvraagstatus op te slaan die nodig is voor validatie van de auth-stromen.

> [!NOTE]
> Deze fix is nog niet beschikbaar voor de msal-hoekige en msal-angularjs wikkels. Deze oplossing lost het probleem met pop-upvensters niet op.

Gebruik onderstaande oplossingen.

#### <a name="other-workarounds"></a>Andere oplossingen
Zorg ervoor dat u test of uw probleem zich alleen voordoet op de specifieke versie van de Microsoft Edge-browser en werkt op de andere browsers voordat u deze tijdelijke oplossingen aanneemt.  
1. Als eerste stap om deze problemen te omzeilen, moet u ervoor zorgen dat het toepassingsdomein en alle andere sites die betrokken zijn bij de omleidingen van de verificatiestroom worden toegevoegd als vertrouwde sites in de beveiligingsinstellingen van de browser, zodat ze tot dezelfde beveiligingszone behoren.
Hiervoor volgt u de volgende stappen:
    - Open **Internet Explorer** en klik op de **instellingen** (tandwielpictogram) in de rechterbovenhoek
    - **Internetopties selecteren**
    - Het tabblad **Beveiliging** selecteren
    - Klik onder de optie **Vertrouwde sites** op de knop **Sites** en voeg de URL's toe in het dialoogvenster dat wordt geopend.

2. Zoals eerder vermeld, aangezien alleen de sessieopslag wordt gewist tijdens de reguliere navigatie, u MSAL.js configureren om in plaats daarvan de lokale opslag te gebruiken. Dit kan worden `cacheLocation` ingesteld als de config parameter tijdens het initialiseren van MSAL.

Let op, dit lost het probleem voor InPrivate-browsen niet op, omdat zowel sessie als lokale opslag zijn gewist.

## <a name="issues-due-to-popup-blockers"></a>Problemen als gevolg van pop-up blokkers

Er zijn gevallen waarin pop-ups worden geblokkeerd in IE of Microsoft Edge, bijvoorbeeld wanneer een tweede pop-up optreedt tijdens multi-factor authenticatie. U krijgt een waarschuwing in de browser om de pop-up één of altijd toe te staan. Als u ervoor kiest om toe te staan, opent `null` de browser het pop-upvenster automatisch en retourneert u een greep ervoor. Als gevolg hiervan heeft de bibliotheek geen greep voor het venster en is er geen manier om het pop-upvenster te sluiten. Hetzelfde probleem gebeurt niet in Chrome wanneer het u vraagt om pop-ups toe te staan omdat het niet automatisch een pop-upvenster opent.

Als **tijdelijke oplossing**moeten ontwikkelaars pop-ups in IE en Microsoft Edge toestaan voordat ze hun app gaan gebruiken om dit probleem te voorkomen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het gebruik van MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).
