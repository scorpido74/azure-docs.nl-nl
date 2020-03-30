---
title: 'Azure AD Connect: naadloze aanmelding | Microsoft Documenten'
description: In dit onderwerp wordt beschreven hoe Azure Active Directory (Azure AD) Seamless Single Sign-On is en hoe u hiermee echte eenmalige aanmelding bieden voor zakelijke desktopgebruikers in uw bedrijfsnetwerk.
services: active-directory
keywords: wat is Azure AD Connect, Active Directory installeren, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77483751"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Seamless Single Sign-On

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Wat is Azure Active Directory Seamless Single Sign-On?

Met Naadloze eenmalige aanmelding van Azure Active Directory (Naadloze SSO van Azure AD) worden gebruikers aangemeld als hun bedrijfsapparaten zijn verbonden met net bedrijfsnetwerk. Wanneer dit is ingeschakeld, hoeven gebruikers hun wachtwoorden niet in te typen om zich aan te melden bij Azure AD en typen ze meestal zelfs hun gebruikersnamen in. Deze functie biedt een gebruiker eenvoudig toegang tot cloudtoepassingen zonder dat er aanvullende on-premises onderdelen nodig zijn.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Naadloze SSO kan worden gecombineerd met de [aanmeldingsmethoden voor wachtwoordhashsynchronisatie](how-to-connect-password-hash-synchronization.md) of [pass-through-verificatie.](how-to-connect-pta.md) Naadloze SSO is _niet_ van toepassing op Active Directory Federation Services (ADFS).

![Naadloze aanmelding](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Naadloze SSO heeft het apparaat van de gebruiker nodig om alleen lid te zijn van het **domein,** maar het wordt niet gebruikt op [Azure AD Joined-](../devices/concept-azure-ad-join.md) of [Hybrid Azure AD-apparaten.](../devices/concept-azure-ad-join-hybrid.md) SSO op Azure AD is lid geworden en Hybride Azure AD-werken op basis van het [primaire vernieuwingstoken](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Belangrijkste voordelen

- *Goede gebruikerservaring*
  - Gebruikers worden automatisch aangemeld bij zowel on-premises als cloudtoepassingen.
  - Gebruikers hoeven hun wachtwoord niet herhaaldelijk in te voeren.
- *Eenvoudig te implementeren & beheren*
  - Er zijn geen extra onderdelen nodig die on-premises nodig zijn om dit te laten werken.
  - Werkt met elke methode van cloudverificatie - [Wachtwoordhashsynchronisatie](how-to-connect-password-hash-synchronization.md) of [Pass-through-verificatie](how-to-connect-pta.md).
  - Kan worden uitgerold naar sommige of al uw gebruikers met behulp van groepsbeleid.
  - Registreer niet-Windows 10-apparaten met Azure AD zonder dat er een AD FS-infrastructuur nodig is. Deze mogelijkheid heeft u nodig om versie 2.1 of hoger van de client die lid is van de [werkplek lid te maken.](https://www.microsoft.com/download/details.aspx?id=53554)

## <a name="feature-highlights"></a>Functiehoogtepunten

- Aanmeldingsgebruikersnaam kan de on-premises standaardgebruikersnaam ()`userPrincipalName`of een ander`Alternate ID`kenmerk zijn dat is geconfigureerd in Azure AD Connect ( ). Beide use cases werken omdat `securityIdentifier` Seamless SSO de claim in het Kerberos-ticket gebruikt om het bijbehorende gebruikersobject in Azure AD op te zoeken.
- Naadloze SSO is een opportunistische functie. Als het om welke reden dan ook mislukt, gaat de gebruikersaanmeldingservaring terug naar zijn normale gedrag - d.w.z. dat de gebruiker zijn wachtwoord moet invoeren op de aanmeldingspagina.
- Als een toepassing (bijvoorbeeld `https://myapps.microsoft.com/contoso.com`) `domain_hint` een (OpenID `whr` Connect) of (SAML)-parameter `login_hint` doorstuurt - het identificeren van uw tenant of parameter - die de gebruiker identificeert, in de Azure AD-aanmeldingsaanvraag, worden gebruikers automatisch aangemeld zonder dat ze gebruikersnamen of wachtwoorden invoeren.
- Gebruikers krijgen ook een stille aanmeldingservaring als `https://contoso.sharepoint.com`een toepassing (bijvoorbeeld) aanmeldingsaanvragen verzendt naar de eindpunten `https://login.microsoftonline.com/<tenant_ID>/<..>` van Azure AD die zijn ingesteld als `https://login.microsoftonline.com/common/<...>`tenants - dat wil zeggen, `https://login.microsoftonline.com/contoso.com/<..>` of - in plaats van het gemeenschappelijke eindpunt van Azure AD - dat wil zeggen .
- Afmelden wordt ondersteund. Hierdoor kunnen gebruikers een ander Azure AD-account kiezen om zich mee aan te melden, in plaats van automatisch automatisch te worden aangemeld met Seamless SSO.
- Office 365 Win32-clients (Outlook, Word, Excel en anderen) met versies 16.0.8730.xxxx en hoger worden ondersteund met behulp van een niet-interactieve stroom. Voor OneDrive moet u de [OneDrive-functie voor stille config](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) activeren voor een stille aanmeldingservaring.
- Het kan worden ingeschakeld via Azure AD Connect.
- Het is een gratis functie en u hebt geen betaalde edities van Azure AD nodig om het te gebruiken.
- Het wordt ondersteund op webbrowser-gebaseerde clients en Office-clients die [moderne verificatie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) ondersteunen op platforms en browsers die geschikt zijn voor Kerberos-verificatie:

| BE\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ja\*|Ja|Ja|Ja\*\*\*|N.v.t.
|Windows 8.1|Ja\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Windows 8|Ja\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Windows 7|Ja\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Windows Server 2012 R2 of hoger|Ja\*\*|N.v.t.|Ja|Ja\*\*\*|N.v.t.
|Mac OS X|N.v.t.|N.v.t.|Ja\*\*\*|Ja\*\*\*|Ja\*\*\*


\*Vereist Internet Explorer-versies 10 of hoger

\*\*Hiervoor zijn Internet Explorer-versies 10 of hoger vereist. Verbeterde beveiligde modus uitschakelen

\*\*\*Vereist [extra configuratie](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Voor Windows 10 is de aanbeveling om [Azure AD Join](../devices/concept-azure-ad-join.md) te gebruiken voor de optimale single sign-on ervaring met Azure AD.

## <a name="next-steps"></a>Volgende stappen

- [**Snel aan de slag**](how-to-connect-sso-quick-start.md) : ga aan de slag met Azure AD Seamless SSO.
- [**Implementatieplan**](https://aka.ms/deploymentplans/sso) - Stapsgewijs implementatieplan.
- [**Technical Deep Dive**](how-to-connect-sso-how-it-works.md) - Begrijp hoe deze functie werkt.
- [**Veelgestelde vragen**](how-to-connect-sso-faq.md) - Antwoorden op veelgestelde vragen.
- [**Problemen oplossen**](tshoot-connect-sso.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Voor het indienen van nieuwe functieaanvragen.

