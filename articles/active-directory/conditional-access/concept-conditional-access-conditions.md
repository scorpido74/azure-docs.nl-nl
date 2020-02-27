---
title: Voor waarden in het beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wat zijn voor waarden in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: dff80d849268c770e4227ff8c99b8f4d133c4d78
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620735"
---
# <a name="conditional-access-conditions"></a>Voorwaardelijke toegang: voor waarden

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van signalen van voor waarden zoals risico, platform of locatie om hun beleids beslissingen te verbeteren. 

![Een beleid voor voorwaardelijke toegang definiëren en voor waarden opgeven](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Er kunnen meerdere voor waarden worden gecombineerd om een nauw keurig en specifiek beleid voor voorwaardelijke toegang te maken.

Als er bijvoorbeeld een gevoelige toepassing wordt geopend, kan een beheerder de informatie over aanmeldings Risico's van identiteits beveiliging en locatie in hun toegangs beslissing naast andere besturings elementen zoals multi-factor Authentication.

## <a name="sign-in-risk"></a>Aanmeldings risico

Voor klanten met toegang tot [identiteits beveiliging](../identity-protection/overview-identity-protection.md)kunnen aanmeldings Risico's worden geëvalueerd als onderdeel van een beleid voor voorwaardelijke toegang. Aanmeld risico geeft aan dat de kans dat een bepaalde verificatie aanvraag niet is geautoriseerd door de eigenaar van de identiteit. Meer informatie over aanmeldings Risico's vindt u in de artikelen, [Wat is Risico's](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) en [procedure: risico beleid configureren en inschakelen](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Apparaat platforms

Het platform van het apparaat wordt gekenmerkt door het besturings systeem dat op een apparaat wordt uitgevoerd. Azure AD identificeert het platform met behulp van informatie die wordt geleverd door het apparaat, zoals teken reeksen van de gebruikers agent. Omdat teken reeksen van de gebruikers agent kunnen worden gewijzigd, wordt deze informatie niet geverifieerd. Het platform van het apparaat moet worden gebruikt in combi natie met Microsoft Intune nalevings beleid voor apparaten of als onderdeel van een blok instructie. De standaard instelling is van toepassing op alle platformen.

Voorwaardelijke toegang van Azure AD biedt ondersteuning voor de volgende platformen:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Als u verouderde verificatie blokkeert met de voor waarde van **andere clients** , kunt u ook de voor waarde voor het apparaat platform instellen.

## <a name="locations"></a>Locaties

Bij het configureren van een locatie als voor waarde kunnen organisaties ervoor kiezen om locaties op te nemen of uit te sluiten. Deze benoemde locaties kunnen de open bare IPv4-netwerk gegevens, het land of de regio of zelfs onbekende gebieden bevatten die niet aan specifieke landen of regio's zijn toegewezen. Alleen IP-bereiken kunnen als een vertrouwde locatie worden gemarkeerd.

Bij het opnemen van **een locatie**bevat deze optie een IP-adres op Internet die niet alleen de benoemde locaties heeft geconfigureerd. Als u **een wille keurige locatie**selecteert, kunnen beheerders ervoor kiezen **alle vertrouwde** of **geselecteerde locaties**uit te sluiten.

Sommige organisaties kunnen er bijvoorbeeld voor kiezen om geen multi-factor Authentication te vereisen wanneer hun gebruikers zijn verbonden met het netwerk op een vertrouwde locatie, zoals hun fysieke hoofd kantoor. Beheerders kunnen een beleid maken dat een wille keurige locatie bevat, maar sluit de geselecteerde locaties voor hun hoofd netwerken uit.

Meer informatie over locaties vindt u in het artikel, [Wat is de voor waarde voor de locatie in azure Active Directory voorwaardelijke toegang](location-condition.md).

## <a name="client-apps-preview"></a>Client-apps (preview-versie)

Beleid voor voorwaardelijke toegang is standaard van toepassing op browser toepassingen en toepassingen die gebruikmaken van moderne verificatie protocollen. Naast deze toepassingen kunnen beheerders kiezen voor Exchange ActiveSync-clients en andere clients die gebruikmaken van verouderde protocollen.

- Browser
   - Dit zijn webtoepassingen die gebruikmaken van protocollen zoals SAML, WS-Federation, OpenID Connect Connect of services die zijn geregistreerd als een OAuth-vertrouwelijke client.
- Mobiele apps en bureaubladclients
   - Moderne verificatie-clients
      - Deze optie omvat toepassingen zoals het Office-bureau blad en de telefoon toepassingen.
   - Exchange ActiveSync-clients
      - Dit omvat standaard alle gebruik van het Exchange ActiveSync-protocol (EAS). Het kiezen van **beleid alleen Toep assen op ondersteunde platforms** wordt beperkt tot ondersteunde platforms zoals IOS, Android en Windows.
      - Wanneer het beleid het gebruik van Exchange ActiveSync blokkeert, ontvangt de betrokken gebruiker één quarantaine-e-mail. Dit e-mail bericht bevat informatie over waarom ze zijn geblokkeerd en bevatten herstel instructies als dit mogelijk is.
   - Andere clients
      - Deze optie omvat clients die gebruikmaken van basis/verouderde verificatie protocollen die geen ondersteuning bieden voor moderne verificatie.
         - Geverifieerde SMTP: wordt gebruikt door de POP-en IMAP-client om e-mail berichten te verzenden.
         - Automatische detectie: wordt door Outlook-en EAS-clients gebruikt om post vakken in Exchange Online te vinden en er verbinding mee te maken.
         - Exchange Online Power shell: wordt gebruikt om verbinding te maken met Exchange Online met externe Power shell. Als u basis verificatie voor Exchange Online Power shell blokkeert, moet u de Exchange Online Power shell-module gebruiken om verbinding te maken. Zie [verbinding maken met Exchange Online Power shell met multi-factor Authentication](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)voor instructies.
         - Exchange Web Services (EWS): een programmeer interface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.
         - IMAP4: wordt gebruikt door IMAP-e-mailclients.
         - MAPI via HTTP (MAPI/HTTP): wordt gebruikt door Outlook 2010 en hoger.
         - Offline adresboek (OAB): een kopie van de adres lijst verzamelingen die worden gedownload en gebruikt door Outlook.
         - Outlook Anywhere (RPC via HTTP): wordt gebruikt door Outlook 2016 en eerder.
         - Outlook-service: wordt gebruikt door de mail-en agenda-app voor Windows 10.
         - POP3: wordt gebruikt door POP-e-mailclients.
         - Reporting Web Services: wordt gebruikt voor het ophalen van rapport gegevens in Exchange Online.

Deze voor waarden worden meestal gebruikt voor het vereisen van een beheerd apparaat, het blok keren van verouderde verificatie en het blok keren van webtoepassingen, maar het toestaan van mobiele of desktop-apps

### <a name="supported-browsers"></a>Ondersteunde browsers

Deze instelling werkt met alle browsers. Om echter te voldoen aan het beleid van een apparaat, zoals een vereiste apparaat vereist, worden de volgende besturings systemen en browsers ondersteund:

| OS | Browsers |
| :-- | :-- |
| Windows 10 | Micro soft Edge, Internet Explorer, Chrome |
| Windows 8/8,1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Micro soft Edge, Intune Managed Browser, Safari |
| Android | Micro soft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Micro soft Edge, Internet Explorer |
| Windows Server 2019 | Micro soft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Waarom wordt er een certificaat prompt weer gegeven in de browser

In Windows 7, iOS, Android en macOS Azure AD identificeert u het apparaat met behulp van een client certificaat dat is ingericht wanneer het apparaat is geregistreerd bij Azure AD.  Wanneer een gebruiker zich voor het eerst aanmeldt via de browser, wordt de gebruiker gevraagd het certificaat te selecteren. De gebruiker moet dit certificaat selecteren voordat u de browser gebruikt.

#### <a name="chrome-support"></a>Chrome-ondersteuning

Voor Chrome-ondersteuning in **Windows 10 Crea tors update (versie 1703)** of nieuwer, installeert u de [extensie Windows 10-accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Deze uitbrei ding is vereist wanneer voor een beleid voor voorwaardelijke toegang apparaatspecifieke Details vereist zijn.

Als u deze uitbrei ding automatisch wilt implementeren in Chrome-browsers, maakt u de volgende register sleutel:

|    |    |
| --- | --- |
| Pad | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (teken reeks) |
| Gegevens | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

Voor Chrome-ondersteuning in **Windows 8,1 en 7**maakt u de volgende register sleutel:

|    |    |
| --- | --- |
| Pad | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (teken reeks) |
| Gegevens | {"patroon": "https://device.login.microsoftonline.com", "filter": {"uitgever": {"CN": "MS-organisatie-Access"}}} |

Deze browsers ondersteunen de verificatie van apparaten, zodat het apparaat kan worden geïdentificeerd en gevalideerd op basis van een beleid. De controle van het apparaat mislukt als de browser wordt uitgevoerd in de privé modus.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Ondersteunde mobiele toepassingen en desktop-clients

Organisaties kunnen **mobiele apps en desktop-clients** als client-app selecteren.

Deze instelling heeft gevolgen voor toegangs pogingen van de volgende mobiele apps en desktop-clients:

| Client-apps | Doel service | Platform |
| --- | --- | --- |
| Dynamics CRM-app | Dynamics CRM | Windows 10, Windows 8,1, iOS en Android |
| E-mail/agenda/personen-app, Outlook 2016, Outlook 2013 (met moderne verificatie)| Office 365 Exchange Online | Windows 10 |
| MFA en locatie beleid voor apps. Beleids regels op basis van apparaten worden niet ondersteund.| Alle apps app service | Android en iOS |
| Micro soft teams-Services: Hiermee beheert u alle services die micro soft-teams en alle client-apps ondersteunen-Windows Desktop, iOS, Android, WP en web client | Microsoft Teams | Windows 10, Windows 8,1, Windows 7, iOS, Android en macOS |
| Office 2016-apps, Office 2013 (met moderne verificatie), [OneDrive Sync-Client](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 share point online | Windows 8.1, Windows 7 |
| Office 2016-apps, universele Office-apps, Office 2013 (met moderne verificatie), [OneDrive Sync-Client](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 share point online | Windows 10 |
| Office 2016 (alleen Word, Excel, Power Point, OneNote). | Office 365 share point online | macOS |
| Office 2019| Office 365 share point online | Windows 10, macOS |
| Mobiele Office-apps | Office 365 share point online | Android, iOS |
| Office Yammer-app | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 share point online | Windows 10, macOS |
| Outlook 2016 (Office voor macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (met moderne verificatie), Skype voor bedrijven (met moderne verificatie) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobiele Outlook-app | Office 365 Exchange Online | Android, iOS |
| App Power BI | Power BI-service | Windows 10, Windows 8,1, Windows 7, Android en iOS |
| Skype voor Bedrijven | Office 365 Exchange Online| Android, iOS |
| App Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8,1, Windows 7, iOS en Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-clients

- Organisaties kunnen alleen Exchange ActiveSync-clients selecteren wanneer ze beleid toewijzen aan gebruikers of groepen. Als u **alle gebruikers**selecteert, worden alle **gast-en externe gebruikers**of **Directory-rollen** door alle gebruikers geblokkeerd.
- Bij het maken van een beleid dat is toegewezen aan Exchange ActiveSync-clients, moet **Office 365 Exchange Online** de enige Cloud toepassing zijn die aan het beleid is toegewezen. 
- Organisaties kunnen het bereik van dit beleid beperken tot specifieke platformen met behulp van de voor waarde **apparaat platforms** .

Als voor het toegangs beheer dat aan het beleid **is toegewezen, goedgekeurde client-app is vereist**, wordt de gebruiker omgeleid om de Outlook Mobile client te installeren en te gebruiken. Als **multi-factor Authentication** is vereist, worden betrokken gebruikers geblokkeerd omdat basis verificatie geen ondersteuning biedt voor multi-factor Authentication.

Raadpleeg voor meer informatie de volgende artikelen:

- [Verouderde verificatie met voorwaardelijke toegang blok keren](block-legacy-authentication.md)
- [Goedgekeurde client-apps waarvoor voorwaardelijke toegang is vereist](app-based-conditional-access.md)

### <a name="other-clients"></a>Andere clients

Door **andere clients**te selecteren, kunt u een voor waarde opgeven die van invloed is op apps die gebruikmaken van basis verificatie met e-mail protocollen als IMAP-, MAPI-, pop-, SMTP-en oudere Office-apps die geen moderne verificatie gebruiken.

## <a name="device-state-preview"></a>Apparaatstatus (preview-versie)

De toestand Apparaatstatus kan worden gebruikt om apparaten uit te sluiten die zijn toegevoegd aan hybride Azure AD en/of apparaten die zijn gemarkeerd als compatibel met een Microsoft Intune nalevings beleid van het beleid voor voorwaardelijke toegang van een organisatie.

Bijvoorbeeld *alle gebruikers* die toegang hebben tot de Cloud-app voor het *Microsoft Azure beheer* , inclusief **alle Apparaatstatus** , met uitzonde ring van **hybride Azure AD gekoppelde** apparaten en **apparaten die als compatibel zijn gemarkeerd** en voor *Access controls*, **blok keren**. 
   - In dit voor beeld wordt een beleid gemaakt dat alleen toegang verleent tot Microsoft Azure beheer van apparaten die zijn toegevoegd aan hybride Azure AD en/of apparaten die als compatibel zijn gemarkeerd.

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: verlenen](concept-conditional-access-grant.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
