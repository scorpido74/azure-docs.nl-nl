---
title: Browser extensie voor het toegangs venster voor toepassingen installeren-Azure AD
description: Veelvoorkomende fouten oplossen die zijn opgetreden tijdens de installatie van de browser uitbreiding van het toegangs venster.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/04/2018
ms.author: kenwith
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2046c24220079a604792d07f3ebc3f6ef11e9c8a
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761082"
---
# <a name="install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Het toegangs venster is een portal op het web. Als u een werk-of school account in Azure Active Directory (Azure AD) hebt, kunt u het toegangs venster gebruiken om Cloud toepassingen te bekijken en te starten waartoe een Azure AD-beheerder u toegang heeft verleend. 

Als u Azure AD-edities gebruikt, kunt u ook selfservice-en app-beheer mogelijkheden gebruiken via het toegangs venster. 

Het toegangs venster is gescheiden van de Azure Portal. U hoeft geen Azure-abonnement te hebben.

## <a name="web-browser-requirements"></a>Vereisten voor webbrowsers

Het toegangs paneel vereist mini maal een browser die Java script ondersteunt en waarvoor CSS is ingeschakeld. Als u zich wilt aanmelden bij toepassingen via SSO op basis van een wacht woord in het toegangs venster, moet u de extensie van het toegangs venster in uw browser hebben geïnstalleerd. De uitbrei ding wordt automatisch gedownload wanneer u een toepassing selecteert die is geconfigureerd voor SSO op basis van een wacht woord.

Voor SSO op basis van een wacht woord kunt u een van de volgende browsers gebruiken:

- **Micro soft Edge**: op Windows 10 jubileum Edition of hoger. 
- **Chrome**: op Windows 7 of hoger en op MacOS X of hoger.
- **Firefox 26,0 of hoger**: op Windows XP SP2 of hoger, en op Mac OS X 10,6 of hoger.

## <a name="install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Ga als volgt te werk om de browser uitbreiding van het toegangs venster te installeren:

1.  Open in een van de ondersteunde browsers het [toegangs venster](https://myapps.microsoft.com)en meld u vervolgens aan als een gebruiker in uw Azure ad-account.

2.  Selecteer een SSO-toepassing op basis van een wacht woord.

3.  Wanneer u hierom wordt gevraagd, selecteert u **nu installeren**.  
    U wordt omgeleid naar de download koppeling voor de geselecteerde browser. 
    
4.  Selecteer **Toevoegen**.

5.  Schakel de extensie **in** of **toestaan** als u hierom wordt gevraagd.

6.  Nadat de installatie is voltooid, start u de browser opnieuw.

7.  Meld u aan bij het toegangs venster en controleer of u uw SSO-toepassingen op basis van wacht woorden kunt starten.

U kunt de uitbrei ding voor Chrome en micro soft Edge ook rechtstreeks downloaden vanaf de volgende sites:

- [Chrome-extensie](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Micro soft Edge-extensie](https://www.microsoft.com/en-us/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)
- [Firefox-extensie](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>De beveiligde aanmeldings extensie voor mijn apps gebruiken
* Als u een andere URL gebruikt dan `https://myapps.microsoft.com` , moet u uw standaard-URL configureren door het volgende te doen:
   1. Terwijl u *niet* bent aangemeld bij de extensie, klikt u met de rechter muisknop op het pictogram extensie.
   2. Selecteer in het menu **mijn apps-URL**.
   3. Selecteer uw standaard-URL.
   4. Selecteer het pictogram extensie.
   5. Als u zich wilt aanmelden bij de extensie, selecteert **u aanmelden om**aan de slag te gaan.

* Ga als volgt te werk als u zich vanuit de browser rechtstreeks wilt aanmelden bij een app:
   1. Nadat u de uitbrei ding hebt geïnstalleerd, kunt u zich aanmelden door **Aanmelden te selecteren om**aan de slag te gaan.
   2. Meld u aan bij de app met de aanmeldings-URL.  
       De aanmeldings-URL is doorgaans de URL van de app die het aanmeldings formulier weergeeft.
      De uitbrei ding moet de status wijzigen en u laten weten dat er een wacht woord beschikbaar is.
   3. Als u zich wilt aanmelden, selecteert u het pictogram extensie.

* Ga als volgt te werk om een app te starten vanuit de uitbrei ding:
   1. Nadat u de uitbrei ding hebt geïnstalleerd, kunt u zich aanmelden door **Aanmelden te selecteren om**aan de slag te gaan.
   2. Selecteer het pictogram extensie om het menu te openen.
   3. Zoek naar een app die beschikbaar is in de portal mijn apps.
   4. Selecteer de app in de lijst met zoek resultaten.  
       De laatste drie apps die u hebt gebruikt, worden weer gegeven in de lijst met **recent gebruikte** toepassingen.
       
* Ga als volgt te werk als u interne bedrijfs-Url's wilt gebruiken terwijl u op afstand werkt:
    1. [Toepassings proxy configureren](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) voor uw Tenant
    2. [De toepassing](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) en URL publiceren via een toepassings proxy
    3. Installeer de extensie en meld u aan met de optie aanmelden om aan de slag te gaan
    4. U kunt nu naar de interne bedrijfs-URL bladeren, zelfs wanneer deze extern is

> [!NOTE]
> De voor gaande opties zijn alleen beschikbaar voor micro soft Edge, Chrome en Firefox.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Een groeps beleid instellen voor Internet Explorer

U kunt een groeps beleid instellen waarmee u de uitbrei ding van het toegangs paneel voor Internet Explorer op afstand kunt installeren op de computers van uw gebruikers.

Voordat u een groeps beleid instelt, moet u ervoor zorgen dat:

-   U hebt [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ingesteld en u hebt de computers van uw gebruikers toegevoegd aan uw domein.

-   Als u het groepsbeleid object (GPO) wilt bewerken, moet u de machtigingen *Instellingen bewerken* hebben. Deze machtiging wordt standaard verleend aan leden van de volgende beveiligings groepen: domein beheerders, ondernemings Administrators en maker eigenaar groeps beleid.

Voor stapsgewijze instructies over het configureren van groeps beleid en het implementeren ervan voor gebruikers raadpleegt [u de uitbrei ding van het toegangs paneel voor Internet Explorer implementeren met behulp van groeps beleid](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Problemen met de uitbrei ding van het toegangs paneel in Internet Explorer oplossen

Zie [problemen met de uitbrei ding van het toegangs paneel voor Internet Explorer oplossen](manage-access-panel-browser-extension.md)voor meer informatie over het configureren van de uitbrei ding voor Internet Explorer.

> [!NOTE]
> Internet Explorer heeft beperkte ondersteuning en ontvangt geen nieuwe software-updates meer. Micro soft Edge is de aanbevolen browser.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Als de voor gaande stappen het probleem niet oplossen

Open een ondersteunings ticket met de volgende informatie als dit beschikbaar is:

-   ID correlatie fout
-   UPN (e-mail adres van gebruiker)
-   TenantID
-   Browsertype
-   Tijd zone en de tijd of periode waarop de fout is opgetreden
-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[What is application access and single sign-on with Azure Active Directory?](what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
