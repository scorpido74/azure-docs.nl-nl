---
title: Voorwaarden in het beleid voor voorwaardelijke toegang - Azure Active Directory
description: Wat zijn voorwaarden in een Azure AD Conditional Access-beleid
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295339"
---
# <a name="conditional-access-conditions"></a>Voorwaardelijke toegang: voorwaarden

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van signalen van omstandigheden zoals risico, apparaatplatform of locatie om zijn beleidsbeslissingen te verbeteren. 

![Een beleid voor voorwaardelijke toegang definiëren en voorwaarden opgeven](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Meerdere voorwaarden kunnen worden gecombineerd om fijnmazige en specifieke voorwaardelijke toegangsbeleidsregels te maken.

Bij het openen van een gevoelige toepassing kan een beheerder bijvoorbeeld naast andere besturingselementen zoals multi-factor authenticatie ook aanmeldingsrisicogegevens van identiteitsbescherming en locatie in hun toegangsbeslissing verwerken.

## <a name="sign-in-risk"></a>Aanmeldingsrisico

Voor klanten met toegang tot [identiteitsbeveiliging](../identity-protection/overview-identity-protection.md)kan het aanmeldingsrisico worden beoordeeld als onderdeel van een beleid voor voorwaardelijke toegang. Aanmeldingsrisico vertegenwoordigt de kans dat een bepaald verificatieverzoek niet is geautoriseerd door de eigenaar van de identiteit. Meer informatie over aanmeldingsrisico's vindt u in de artikelen, [Wat is risico](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) en [Hoe: Risicobeleid configureren en inschakelen](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Apparaatplatformen

Het apparaatplatform wordt gekenmerkt door het besturingssysteem dat op een apparaat draait. Azure AD identificeert het platform aan de hand van informatie die door het apparaat wordt verstrekt, zoals tekenreeksen van gebruikersagenten. Aangezien tekenreeksen van gebruikersagenten kunnen worden gewijzigd, wordt deze informatie niet geverifieerd. Apparaatplatform moet worden gebruikt in overleg met het nalevingsbeleid van Microsoft Intune-apparaten of als onderdeel van een blokkering. De standaardinstelling is van toepassing op alle apparaatplatforms.

Azure AD Conditional Access ondersteunt de volgende apparaatplatforms:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Als u verouderde verificatie blokkeert met de voorwaarde **Andere clients,** u ook de voorwaarde van het apparaatplatform instellen.

## <a name="locations"></a>Locaties

Bij het configureren van locatie als voorwaarde kunnen organisaties ervoor kiezen om locaties op te nemen of uit te sluiten. Deze genoemde locaties kunnen de openbare IPv4-netwerkinformatie, land of regio of zelfs onbekende gebieden bevatten die niet worden toegewezen aan specifieke landen of regio's. Alleen IP-bereiken kunnen worden gemarkeerd als een vertrouwde locatie.

Wanneer u **een locatie**opneemt, bevat deze optie een IP-adres op het internet, niet alleen geconfigureerde locaties met naam. Bij het selecteren van **een locatie**kunnen beheerders ervoor kiezen om **alle vertrouwde** of geselecteerde **locaties**uit te sluiten.

Sommige organisaties kunnen er bijvoorbeeld voor kiezen om geen meervoudige verificatie te vereisen wanneer hun gebruikers zijn verbonden met het netwerk op een vertrouwde locatie, zoals hun fysieke hoofdkwartier. Beheerders kunnen een beleid maken dat elke locatie bevat, maar de geselecteerde locaties voor hun hoofdkantoornetwerken uitsluit.

Meer informatie over locaties vindt u in het artikel, [Wat is de locatievoorwaarde in Azure Active Directory Conditional Access](location-condition.md).

## <a name="client-apps-preview"></a>Client-apps (voorbeeld)

Het beleid voor voorwaardelijke toegang is standaard van toepassing op browsertoepassingen en toepassingen die moderne verificatieprotocollen gebruiken. Naast deze toepassingen kunnen beheerders ervoor kiezen om Exchange ActiveSync-clients en andere clients op te nemen die oudere protocollen gebruiken.

- Browser
   - Deze omvatten webgebaseerde toepassingen die protocollen gebruiken zoals SAML, WS-Federation, OpenID Connect of services die zijn geregistreerd als een vertrouwelijke OAuth-client.
- Mobiele apps en desktopclients
   - Moderne verificatieclients
      - Deze optie omvat toepassingen zoals het Office-bureaublad en telefoontoepassingen.
   - Exchange ActiveSync-clients
      - Standaard omvat dit al het gebruik van het Exchange ActiveSync (EAS)-protocol. Als u **Beleid alleen toepassen kiest voor ondersteunde platforms,** geldt dit voor ondersteunde platforms zoals iOS, Android en Windows.
      - Wanneer het beleid het gebruik van Exchange ActiveSync blokkeert, ontvangt de betrokken gebruiker één quarantaine-e-mail. Deze e-mail met informatie over waarom ze zijn geblokkeerd en bevatten herstelinstructies indien mogelijk.
   - Andere clients
      - Deze optie omvat clients die basis-/legacy-verificatieprotocollen gebruiken die geen moderne verificatie ondersteunen.
         - Geauthenticeerde SMTP - Gebruikt door POP en IMAP client's om e-mailberichten te verzenden.
         - Automatisch ontdekken - Gebruikt door Outlook- en EAS-clients om postvakken in Exchange Online te vinden en te verbinden.
         - Exchange Online PowerShell - Wordt gebruikt om verbinding te maken met Exchange Online met externe PowerShell. Als u basisverificatie voor Exchange Online PowerShell blokkeert, moet u de Exchange Online PowerShell-module gebruiken om verbinding te maken. Zie Verbinding [maken met Exchange Online PowerShell met behulp van meervoudige verificatie](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)voor instructies.
         - Exchange Web Services (EWS) - een programmeerinterface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.
         - IMAP4 - Gebruikt door IMAP e-mailclients.
         - MAPI over HTTP (MAPI/HTTP) - Gebruikt door Outlook 2010 en hoger.
         - Offline adresboek (OAB) - een kopie van adreslijstverzamelingen die worden gedownload en gebruikt door Outlook.
         - Outlook Anywhere (RPC via HTTP) - Gebruikt door Outlook 2016 en eerder.
         - Outlook-service - Gebruikt door de mail- en agenda-app voor Windows 10.
         - POP3 - Gebruikt door POP e-mailclients.
         - Webservices rapporteren - Wordt gebruikt om rapportgegevens op te halen in Exchange Online.

Deze voorwaarden worden vaak gebruikt wanneer een beheerd apparaat vereist, verouderde verificatie blokkeert en webtoepassingen worden geblokkeerd, maar mobiele of desktop-apps worden toegestaan.

### <a name="supported-browsers"></a>Ondersteunde browsers

Deze instelling werkt met alle browsers. Om te voldoen aan een apparaatbeleid, zoals een compatibele apparaatvereiste, worden de volgende besturingssystemen en browsers echter ondersteund:

| OS | Browsers |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Waarom zie ik een certificaatprompt in de browser

Op Windows 7, iOS, Android en macOS Azure AD identificeert het apparaat met behulp van een clientcertificaat dat is ingericht wanneer het apparaat is geregistreerd bij Azure AD.  Wanneer een gebruiker zich voor het eerst aanmeldt via de browser wordt de gebruiker gevraagd het certificaat te selecteren. De gebruiker moet dit certificaat selecteren voordat hij de browser gebruikt.

#### <a name="chrome-support"></a>Chrome-ondersteuning

Installeer de [windows 10-accountextensie voor Chrome-ondersteuning](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)in **Windows 10 Creators Update (versie 1703)** of hoger. Deze extensie is vereist wanneer een beleid voor voorwaardelijke toegang apparaatspecifieke gegevens vereist.

Als u deze extensie automatisch wilt implementeren in Chrome-browsers, maakt u de volgende registersleutel:

|    |    |
| --- | --- |
| Pad | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (tekenreeks) |
| Gegevens | ppnbnpeolgkicgegkbkbjmhlideopiji;https\:-clients2.google.com/service/update2/crx |

Maak voor **Chrome-ondersteuning in Windows 8.1 en 7**de volgende registersleutel:

|    |    |
| --- | --- |
| Pad | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (tekenreeks) |
| Gegevens | {"patroon":"https://device.login.microsoftonline.com"Filter":"EMITTENT":{"CN":"MS-Organization-Access"}}} |

Deze browsers ondersteunen apparaatverificatie, zodat het apparaat kan worden geïdentificeerd en gevalideerd tegen een beleid. De apparaatcontrole mislukt als de browser in privémodus wordt uitgevoerd.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Ondersteunde mobiele applicaties en desktopclients

Organisaties kunnen **mobiele apps en desktopclients** selecteren als client-app.

Deze instelling heeft een impact op toegangspogingen van de volgende mobiele apps en desktopclients:

| Client-apps | Doelservice | Platform |
| --- | --- | --- |
| Dynamics CRM-app | Dynamics CRM | Windows 10, Windows 8.1, iOS en Android |
| Mail/Agenda/People-app, Outlook 2016, Outlook 2013 (met moderne verificatie)| Office 365 Exchange Online | Windows 10 |
| MFA- en locatiebeleid voor apps. Beleid op basis van apparaten wordt niet ondersteund.| Elke app-service van Mijn Apps | Android en iOS |
| Microsoft Teams Services - hiermee worden alle services aandiestokers uitvoeren voor Microsoft Teams en al zijn client-apps - Windows Desktop, iOS, Android, WP en webclient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android en macOS |
| Office 2016-apps, Office 2013 (met moderne verificatie), [Synchronisatieclient van OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Office 2016-apps, Universal Office-apps, Office 2013 (met moderne verificatie), [Synchronisatieclient van OneDrive](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (alleen Word, Excel, PowerPoint, OneNote). | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Mobiele Office-apps | Office 365 SharePoint Online | Android, iOS |
| Office Yammer-app | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office voor macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (met moderne verificatie), Skype voor Bedrijven (met moderne verificatie) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobiele Outlook-app | Office 365 Exchange Online | Android, iOS |
| Power BI-app | Power BI-service | Windows 10, Windows 8.1, Windows 7, Android en iOS |
| Skype voor Bedrijven | Office 365 Exchange Online| Android, iOS |
| Visual Studio Team Services-app | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS en Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-clients

- Organisaties kunnen Exchange ActiveSync-clients alleen selecteren wanneer ze beleid toewijzen aan gebruikers of groepen. Als **u Alle gebruikers,** **Alle gast- en externe gebruikers**of **Directory-rollen** selecteert, worden alle gebruikers geblokkeerd.
- Wanneer u een beleid maakt dat is toegewezen aan Exchange ActiveSync-clients, moet **Office 365 Exchange Online** de enige cloudtoepassing zijn die aan het beleid is toegewezen. 
- Organisaties kunnen het bereik van dit beleid beperken tot specifieke platforms met behulp van de voorwaarde **Apparaatplatforms.**

Als het toegangsbeheer dat is toegewezen aan het beleid **een goedgekeurde client-app vereist,** wordt de gebruiker doorverwezen om de mobiele Outlook-client te installeren en te gebruiken. In het geval dat **multi-factor authenticatie** vereist is, worden de getroffen gebruikers geblokkeerd, omdat basisverificatie geen ondersteuning biedt voor multi-factor authenticatie.

Raadpleeg voor meer informatie de volgende artikelen:

- [Verouderde verificatie blokkeren met voorwaardelijke toegang](block-legacy-authentication.md)
- [Goedgekeurde client-apps vereisen met voorwaardelijke toegang](app-based-conditional-access.md)

### <a name="other-clients"></a>Andere clients

Door **Andere clients te**selecteren, u een voorwaarde opgeven die van invloed is op apps die basisverificatie gebruiken met e-mailprotocollen zoals IMAP, MAPI, POP, SMTP en oudere Office-apps die geen moderne verificatie gebruiken.

## <a name="device-state-preview"></a>Apparaatstatus (voorbeeld)

De voorwaarde van de apparaatstatus kan worden gebruikt om apparaten die hybride Azure AD zijn, uit te sluiten en/of apparaten die zijn gemarkeerd als compatibel met een Microsoft Intune-nalevingsbeleid, uit te sluiten in het beleid voor voorwaardelijke toegang van een organisatie.

Alle *gebruikers die* toegang hebben tot de Microsoft Azure Management-cloud-app, inclusief **Alle apparaatstatus,** met uitzondering van **Device Hybrid Azure AD, zijn gemarkeerd** als **compatibel** en voor *toegangsbesturingselementen*, **Blokkeren**. *Microsoft Azure Management* 
   - In dit voorbeeld wordt een beleid gemaakt dat alleen toegang tot Microsoft Azure Management toestaat vanaf apparaten die hybride Azure AD hebben samengevoegd en/of apparaten die als compatibel zijn gemarkeerd.

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: subsidie](concept-conditional-access-grant.md)

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
