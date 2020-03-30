---
title: Browserextensie voor toepassingstoegangspaneel installeren - Azure AD
description: Verhelpen van veelvoorkomende fouten die zijn opgetreden bij het installeren van de browserextensie van het toegangspaneel.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771ba79f067cbff1ab8bbfece64f4028b4ca50b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275858"
---
# <a name="install-the-access-panel-browser-extension"></a>De browserextensie van het toegangspaneel installeren

Het toegangspaneel is een webportal. Als u een werk- of schoolaccount hebt in Azure Active Directory (Azure AD), u het toegangspaneel gebruiken om cloudgebaseerde toepassingen weer te geven en te starten waartoe een Azure AD-beheerder u toegang heeft verleend. 

Als u Azure AD-edities gebruikt, u ook mogelijkheden voor selfservicegroepen en app-beheer gebruiken via het toegangspaneel. 

Het toegangspaneel staat los van de Azure-portal. U hoeft geen Azure-abonnement te hebben.

## <a name="web-browser-requirements"></a>Vereisten voor webbrowsers

Het toegangspaneel vereist minimaal een browser die JavaScript ondersteunt en CSS heeft ingeschakeld. Als u wilt worden aangemeld bij toepassingen via sso op basis van een wachtwoord in het toegangspaneel, moet u de extensie van het toegangspaneel in uw browser hebben geïnstalleerd. De extensie wordt automatisch gedownload wanneer u een toepassing selecteert die is geconfigureerd voor sso op basis van wachtwoorden.

Voor sso op basis van wachtwoorden u een van de volgende browsers gebruiken:

- **Microsoft Edge:** op Windows 10 Anniversary Edition of hoger. 
- **Chrome:** op Windows 7 of hoger, en op MacOS X of hoger.
- **Firefox 26.0 of hoger:** op Windows XP SP2 of hoger, en op Mac OS X 10.6 of hoger.

## <a name="install-the-access-panel-browser-extension"></a>De browserextensie van het toegangspaneel installeren

Ga als volgt te werk om de browserextensie van het toegangspaneel te installeren:

1.  Open in een van de ondersteunde browsers het [toegangspaneel](https://myapps.microsoft.com)en meld u aan als gebruiker in uw Azure AD-account.

2.  Selecteer een op een wachtwoord gebaseerde SSO-toepassing.

3.  Wanneer u wordt gevraagd, selecteert u **Nu installeren**.  
    U wordt doorverwezen naar de downloadlink voor uw geselecteerde browser. 
    
4.  Selecteer **Toevoegen**.

5.  Als u wordt gevraagd, schakelt **u de** extensie in of staat **deze toe.**

6.  Nadat de installatie is voltooid, start u uw browser opnieuw op.

7.  Meld u aan bij het toegangspaneel en controleer of u uw sso-toepassingen op basis van wachtwoorden starten.

U de extensie voor Chrome en Microsoft Edge ook rechtstreeks downloaden van volgende sites:

- [Chrome-extensie](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge-extensie](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox-extensie](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>De aanmeldingsextensie Mijn apps beveiligen
* Als u een andere URL `https://myapps.microsoft.com`van Mijn apps gebruikt dan , configureert u de standaard-URL als volgt:
   1. Hoewel u *niet* bent aangemeld bij de extensie, klikt u met de rechtermuisknop op het extensiepictogram.
   2. Selecteer in het menu **De URL van Mijn apps**.
   3. Selecteer uw standaard-URL.
   4. Selecteer het extensiepictogram.
   5. Als u zich wilt aanmelden bij de extensie, selecteert u **Aanmelden om aan de slag te gaan.**

* Ga als volgt te werk om u rechtstreeks aan te melden bij een app vanuit de browser:
   1. Nadat u de extensie hebt geïnstalleerd, meldt u zich aan door Aanmelden te selecteren **om aan de slag te gaan.**
   2. Meld u aan bij de app met de aanmeldings-URL.  
       De aanmeldings-URL is meestal de URL van de app die het aanmeldingsformulier weergeeft.
      De extensie moet de status wijzigen en u laten weten dat er een wachtwoord beschikbaar is.
   3. Als u zich wilt aanmelden, selecteert u het extensiepictogram.

* Ga als volgt te werk om een app vanuit de extensie te starten:
   1. Nadat u de extensie hebt geïnstalleerd, meldt u zich aan door Aanmelden te selecteren **om aan de slag te gaan.**
   2. Selecteer het extensiepictogram om het menu te openen.
   3. Zoek naar een app die beschikbaar is in de mijn apps-portal.
   4. Selecteer de app in de lijst met zoekresultaten.  
       De laatste drie apps die u hebt gebruikt, worden weergegeven in de lijst **Met recent gebruikte** sneltoetsen.
       
* Ga als volgt te werk om interne bedrijfs-URL's te gebruiken terwijl u deze afstandsbediening hebt, als volgt:
    1. [Toepassingsproxy configureren](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) op uw tenant
    2. [De toepassing](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) en URL publiceren via Application Proxy
    3. Installeer de extensie en meld u aan door Aanmelden te selecteren om aan de slag te gaan
    4. U nu bladeren naar de interne BEDRIJFS-URL, zelfs als u

> [!NOTE]
> De voorgaande opties zijn alleen beschikbaar voor Microsoft Edge, Chrome en Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Een groepsbeleid instellen voor Internet Explorer

U een groepsbeleid instellen waarmee u op afstand de extensie van het toegangspaneel voor Internet Explorer op de machines van uw gebruikers installeren.

Voordat u een groepsbeleid instelt, moet u ervoor zorgen dat:

-   U hebt [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ingesteld en u hebt de machines van uw gebruikers bij uw domein gevoegd.

-   Als u het groepsbeleidsobject (GPO) wilt bewerken, moet u machtigingen *voor bewerkingsinstellingen* hebben. Deze toestemming wordt standaard verleend aan leden van de volgende beveiligingsgroepen: domeinbeheerders, bedrijfsbeheerders en eigenaren van groepsbeleidseigenaren.

Zie Stap voor stap instructies voor het configureren van het groepsbeleid en het implementeren ervan aan gebruikers [de extensie van het toegangspaneel voor Internet Explorer implementeren met groepsbeleid](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Problemen met de extensie van het toegangspaneel in Internet Explorer oplossen

Zie [Problemen met de extensie van het toegangspaneel voor Internet Explorer voor](manage-access-panel-browser-extension.md)toegang tot een diagnosetool en informatie over het configureren van de extensie voor Internet Explorer.

> [!NOTE]
> Internet Explorer biedt beperkte ondersteuning en ontvangt geen nieuwe software-updates meer. Microsoft Edge is de aanbevolen browser.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Als het probleem niet wordt opgelost in de voorgaande stappen

Open een ondersteuningsticket met de volgende informatie, als deze beschikbaar is:

-   Correlatiefout-id
-   UPN (e-mailadres van de gebruiker)
-   TenantID
-   Browsertype
-   Tijdzone en het tijdstip of het tijdsbestek waarop de fout is opgetreden
-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](what-is-single-sign-on.md)
