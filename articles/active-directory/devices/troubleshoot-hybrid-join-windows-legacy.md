---
title: Problemen met verouderde hybride Azure Active Directory-samengevoegde apparaten oplossen
description: Het oplossen van problemen met hybride Azure Active Directory is samengevoegd op apparaten op downniveau.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379108"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Problemen met hybride Azure Active Directory mogelijk maken van apparaten op downniveau 

Dit artikel is alleen van toepassing op de volgende apparaten: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Zie Het oplossen van hybride Azure Active Directory voor Windows 10- [of Windows Server 2016.](troubleshoot-hybrid-join-windows-current.md)

In dit artikel wordt ervan uitgegaan dat u [hybride Azure Active Directory-samengevoegde apparaten](hybrid-azuread-join-plan.md) hebt geconfigureerd om de volgende scenario's te ondersteunen:

- Voorwaardelijke toegang op basis van het apparaat

In dit artikel vindt u richtlijnen voor het oplossen van problemen voor het oplossen van mogelijke problemen.  

**Wat je moet weten:** 

- Hybride Azure AD join voor downlevel Windows-apparaten werkt iets anders dan in Windows 10. Veel klanten realiseren zich niet dat ze AD FS (voor federatieve domeinen) of Seamless SSO geconfigureerd (voor beheerde domeinen) nodig hebben.
- Voor klanten met federatieve domeinen, als het Service Connection Point (SCP) zodanig is geconfigureerd dat het naar de beheerde domeinnaam verwijst (bijvoorbeeld contoso.onmicrosoft.com in plaats van contoso.com), dan wordt Hybride Azure AD Join voor downlevel Windows-apparaten niet werken.
- Het maximum aantal apparaten per gebruiker is momenteel ook van toepassing op hybride Azure AD-apparaten met downlevel. 
- Hetzelfde fysieke apparaat wordt meerdere keren weergegeven in Azure AD wanneer meerdere domeingebruikers zich aanmelden voor de hybride Azure AD-apparaten met downlevel.  Als *jdoe* en *jharnett* zich bijvoorbeeld aanmelden bij een apparaat, wordt voor elk van hen een aparte registratie (DeviceID) gemaakt op het tabblad **GEBRUIKERSgegevens.** 
- U ook meerdere vermeldingen voor een apparaat op het tabblad gebruikersgegevens krijgen vanwege een herinstallatie van het besturingssysteem of een handmatige herregistratie.
- De eerste registratie / join van apparaten is geconfigureerd om een poging uit te voeren om aan te melden of te vergrendelen / ontgrendelen. Er kan 5 minuten vertraging worden veroorzaakt door een taakplannertaak. 
- Zorg ervoor dat [KB4284842](https://support.microsoft.com/help/4284842) is geïnstalleerd, in het geval van Windows 7 SP1 of Windows Server 2008 R2 SP1. Deze update voorkomt toekomstige verificatiefouten als gevolg van het toegangsverlies van de klant tot beveiligde sleutels na het wijzigen van het wachtwoord.

## <a name="step-1-retrieve-the-registration-status"></a>Stap 1: De registratiestatus ophalen 

**Ga als volgt te werk om de registratiestatus te verifiëren:**  

1. Meld u aan met het gebruikersaccount waarmee een hybride Azure AD-lid is uitgevoerd.
1. De opdrachtprompt openen 
1. Type `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Met deze opdracht wordt een dialoogvenster weergegeven waarin u meer informatie krijgt over de joinstatus.

![Workplace Join voor Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Stap 2: De hybride Azure AD-joinstatus evalueren 

Als het apparaat niet hybride is Azure AD, u proberen om hybride Azure AD-lid te maken door op de knop Deelnemen te klikken. Als de poging om hybride Azure AD-join te doen mislukt, worden de details over de fout weergegeven.

**De meest voorkomende problemen zijn:**

- Een verkeerd geconfigureerde AD FS- of Azure AD- of Netwerkproblemen

    ![Workplace Join voor Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Autoworkplace.exe kan niet stilletjes verifiëren met Azure AD of AD FS. Dit kan worden veroorzaakt door ontbrekende of verkeerd geconfigureerde AD FS (voor federatieve domeinen) of ontbrekende of verkeerd geconfigureerde Azure AD Seamless Single Sign-On (voor beheerde domeinen) of netwerkproblemen. 
   - Het kan zijn dat multi-factor authenticatie (MFA) is ingeschakeld/geconfigureerd voor de gebruiker en WIAORMULTIAUTHN is niet geconfigureerd op de AD FS-server. 
   - Een andere mogelijkheid is dat de HRD-pagina (Home Realm Discovery) wacht op interactie van de gebruiker, waardoor **autoworkplace.exe** niet stilletjes een token kan aanvragen.
   - Het kan zijn dat AD FS- en Azure AD-URL's ontbreken in de intranetzone van IE op de client.
   - Problemen met de netwerkconnectiviteit kunnen voorkomen dat **autoworkplace.exe** AD FS of de AZURE AD-URL's bereikt. 
   - **Autoworkplace.exe** vereist dat de client een directe zichtlijn heeft van de client naar de on-premises AD-domeincontroller van de organisatie, wat betekent dat hybride Azure AD-join alleen slaagt wanneer de client is verbonden met het intranet van de organisatie.
   - Uw organisatie gebruikt Azure AD Seamless `https://autologon.microsoftazuread-sso.com` `https://aadg.windows.net.nsatc.net` Single Sign-On of is niet aanwezig op de IE-intranetinstellingen van het apparaat en **Updates toestaan voor de statusbalk via het script** is niet ingeschakeld voor de intranetzone.
- U bent niet aangemeld als domeingebruiker

   ![Workplace Join voor Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Er zijn een paar verschillende redenen waarom dit kan gebeuren:

   - De aangemelde gebruiker is geen domeingebruiker (bijvoorbeeld een lokale gebruiker). Hybride Azure AD-lid worden op apparaten op downniveau wordt alleen ondersteund voor domeingebruikers.
   - De client kan geen verbinding maken met een domeincontroller.    
- Er is een quotum bereikt

    ![Workplace Join voor Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- De service reageert niet 

    ![Workplace Join voor Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

U de statusgegevens ook vinden in het gebeurtenislogboek onder: **Logboek toepassingen en services\Microsoft-Workplace Join**
  
**De meest voorkomende oorzaken voor een mislukte hybride Azure AD join zijn:** 

- Uw computer is niet verbonden met het interne netwerk van uw organisatie of met een VPN met een verbinding met uw on-premises AD-domeincontroller.
- U bent aangemeld bij uw computer met een lokaal computeraccount. 
- Problemen met de serviceconfiguratie: 
   - De AD FS-server is niet geconfigureerd om **WIAORMULTIAUTHN**te ondersteunen. 
   - Het forest van uw computer heeft geen object Service Connection Point dat verwijst naar uw geverifieerde domeinnaam in Azure AD 
   - Of als uw domein wordt beheerd, is Seamless SSO niet geconfigureerd of werkt deze niet.
   - Een gebruiker heeft de limiet van apparaten bereikt. 

## <a name="next-steps"></a>Volgende stappen

Zie de [veelgestelde vragen over apparaatbeheer](faq.md) voor vragen  
