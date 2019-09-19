---
title: Verwijzing naar Azure Active Directory instellingen voor voorwaardelijke toegang | Microsoft Docs
description: Bekijk een overzicht van de ondersteunde instellingen in een Azure Active Directory beleid voor voorwaardelijke toegang.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 216316249197071bdd7a175dfc5339caeddee50a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086829"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Verwijzing naar de Azure Active Directory-instellingen voor voorwaardelijke toegang

U kunt [voorwaardelijke toegang van Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) gebruiken om te bepalen hoe geautoriseerde gebruikers toegang hebben tot uw resources.

In dit artikel vindt u ondersteunings informatie voor de volgende configuratie opties in een beleid voor voorwaardelijke toegang:

- Toewijzingen van Cloud toepassingen
- Toestand van het apparaat platform
- Voor waarde client toepassingen
- Vereiste voor client toepassing goedgekeurd

Als dit niet de informatie is die u zoekt, laat u een opmerking aan het einde van dit artikel.

## <a name="cloud-apps-assignments"></a>Cloud-apps toewijzen

Met beleid voor voorwaardelijke toegang kunt u bepalen hoe uw gebruikers toegang hebben tot uw [Cloud-apps](conditions.md#cloud-apps-and-actions). Wanneer u een beleid voor voorwaardelijke toegang configureert, moet u ten minste één Cloud-app selecteren. 

![De Cloud-apps voor uw beleid selecteren](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Micro soft-Cloud toepassingen

U kunt een beleid voor voorwaardelijke toegang toewijzen aan de volgende Cloud-apps van micro soft:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database en data warehouse: meer [informatie](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Micro soft Application Insights Analytics
- Microsoft Azure Information Protection- [meer informatie](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure-beheer- [meer informatie](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Abonnements beheer Microsoft Azure
- Microsoft Cloud App Security
- Micro soft commerce tools Access Control-Portal
- Micro soft commerce tools-verificatie service
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Microsoft Intune-inschrijving
- Microsoft Planner
- Microsoft PowerApps
- Micro soft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 share point online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI-service
- Project Online
- Skype voor Bedrijven Online
- Virtueel particulier netwerk (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Andere toepassingen

Naast de micro soft-Cloud-apps, kunt u een beleid voor voorwaardelijke toegang toewijzen aan de volgende typen Cloud-apps:

- Toepassingen die zijn verbonden met Azure AD
- Vooraf geïntegreerde SaaS-toepassing (Software as a Service)
- Toepassingen die gebruikmaken van een wacht woord voor eenmalige aanmelding (SSO)
- Line-of-business-toepassingen
- Toepassingen die gebruikmaken van Azure AD-toepassingsproxy

## <a name="device-platform-condition"></a>Toestand van het apparaat platform

In een beleid voor voorwaardelijke toegang kunt u de platform voorwaarde van het apparaat configureren om het beleid te koppelen aan het besturings systeem op een client. Voorwaardelijke toegang van Azure AD biedt ondersteuning voor de volgende platformen:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Het toegangs beleid koppelen aan het client besturingssysteem](./media/technical-reference/41.png)

Als u verouderde verificatie blokkeert met de voor waarde van **andere clients** , kunt u ook de voor waarde voor het apparaat platform instellen.

## <a name="client-apps-condition"></a>Voor waarde voor client-apps

In het beleid voor voorwaardelijke toegang kunt u de voor waarde voor [client-apps](conditions.md#client-apps) configureren om het beleid te koppelen aan de client-app die een toegangs poging heeft gestart. Stel de voor waarde voor client-apps in op het verlenen of blok keren van toegang wanneer een toegangs poging wordt gedaan vanuit de volgende typen client-apps:

- Browser
- Mobiele apps en bureau blad-apps

![Toegang beheren voor client-apps](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Ondersteunde browsers

In het beleid voor voorwaardelijke toegang kunt u **browsers** als client-app selecteren.

![Toegang beheren voor ondersteunde browsers](./media/technical-reference/05.png)

Deze instelling werkt met alle browsers. Om echter te voldoen aan het beleid van een apparaat, zoals een vereiste apparaat vereist, worden de volgende besturings systemen en browsers ondersteund:

| OS                     | Browsers                                        |
| :--                    | :--                                             |
| Windows 10             | Micro soft Edge, Internet Explorer, Chrome       |
| Windows 8/8,1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Micro soft Edge, Intune Managed Browser, Safari  |
| Android                | Micro soft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Micro soft Edge, Internet Explorer               |
| Windows Server 2019    | Micro soft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Waarom wordt er een certificaat prompt weer gegeven in de browser

In Windows 7, iOS, Android en macOS Azure AD identificeert u het apparaat met behulp van een client certificaat dat is ingericht wanneer het apparaat is geregistreerd bij Azure AD.  Wanneer een gebruiker zich voor het eerst aanmeldt via de browser, wordt de gebruiker gevraagd het certificaat te selecteren. De gebruiker moet dit certificaat selecteren voordat u de browser gebruikt.

#### <a name="chrome-support"></a>Chrome-ondersteuning

Voor Chrome-ondersteuning in **Windows 10 Crea tors update (versie 1703)** of nieuwer, installeert u de [extensie Windows 10-accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Deze uitbrei ding is vereist wanneer voor een beleid voor voorwaardelijke toegang apparaatspecifieke Details vereist zijn.

Als u deze uitbrei ding automatisch wilt implementeren in Chrome-browsers, maakt u de volgende register sleutel:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| type | REG_SZ (teken reeks) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.Google.com/service/UPDATE2/CRX |

Voor Chrome-ondersteuning in **Windows 8,1 en 7**maakt u de volgende register sleutel:

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| type | REG_SZ (teken reeks) |
| Data | {"patroon": "https://device.login.microsoftonline.com", "filter": {"uitgever": {"CN": "MS-organisatie-Access"}}} |

Deze browsers ondersteunen de verificatie van apparaten, zodat het apparaat kan worden geïdentificeerd en gevalideerd op basis van een beleid. De controle van het apparaat mislukt als de browser wordt uitgevoerd in de privé modus.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Ondersteunde mobiele toepassingen en desktop-clients

In het beleid voor voorwaardelijke toegang kunt u **mobiele apps en desktop-clients** als client-app selecteren.

![Toegang beheren voor ondersteunde mobiele apps of desktop-clients](./media/technical-reference/06.png)

Deze instelling heeft gevolgen voor toegangs pogingen van de volgende mobiele apps en desktop-clients:

| Client-apps | Doel service | Platform |
| --- | --- | --- |
| Dynamics CRM-app | Dynamics CRM | Windows 10, Windows 8,1, iOS en Android |
| E-mail/agenda/personen-app, Outlook 2016, Outlook 2013 (met moderne verificatie)| Office 365 Exchange Online | Windows 10 |
| MFA en locatie beleid voor apps. Beleids regels op basis van apparaten worden niet ondersteund.| Alle apps app service| Android en iOS |
| Micro soft teams-Services: Hiermee beheert u alle services die micro soft-teams en alle client-apps ondersteunen-Windows Desktop, iOS, Android, WP en web client | Microsoft Teams | Windows 10, Windows 8,1, Windows 7, iOS, Android en macOS |
| Office 2016-apps, Office 2013 (met moderne verificatie), OneDrive Sync-Client (Zie [opmerkingen](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 share point online | Windows 8.1, Windows 7 |
| Office 2016-apps, universele Office-apps, Office 2013 (met moderne verificatie), OneDrive Sync-Client (Zie [opmerkingen](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), ondersteuning van Office-groepen is gepland voor de toekomst. ondersteuning voor share point-apps is gepland voor de toekomst | Office 365 share point online | Windows 10 |
| Office 2016 (alleen Word, Excel, Power Point, OneNote). Ondersteuning voor OneDrive voor bedrijven is gepland voor de toekomst| Office 365 share point online| macOS|
| Office 2019| Office 365 share point online | Windows 10, macOS |
| Mobiele Office-apps | Office 365 share point online | Android, iOS |
| Office Yammer-app | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 share point online | Windows 10, macOS |
| Outlook 2016 (Office voor macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (met moderne verificatie), Skype voor bedrijven (met moderne verificatie) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobiele Outlook-app | Office 365 Exchange Online | Android, iOS |
| App Power BI | Power BI-service | Windows 10, Windows 8,1, Windows 7, Android en iOS |
| Skype voor Bedrijven | Office 365 Exchange Online| Android, IOS |
| App Visual Studio Team Services | Visual Studio-teamservices | Windows 10, Windows 8,1, Windows 7, iOS en Android |

## <a name="support-for-legacy-authentication"></a>Ondersteuning voor verouderde verificatie

Door **andere clients**te selecteren, kunt u een voor waarde opgeven die van invloed is op apps die gebruikmaken van basis verificatie met e-mail protocollen als IMAP-, MAPI-, pop-, SMTP-en oudere Office-apps die geen moderne verificatie gebruiken.  

![Andere clients](./media/technical-reference/11.png)

Zie [client-apps](conditions.md#client-apps)voor meer informatie.

## <a name="approved-client-app-requirement"></a>Vereiste voor client-app vereist

In het beleid voor voorwaardelijke toegang kunt u vereisen dat er een toegangs poging tot de geselecteerde Cloud-apps moet worden uitgevoerd vanuit een goedgekeurde client-app. 

![Toegang beheren voor goedgekeurde client-apps](./media/technical-reference/21.png)

Deze instelling is van toepassing op de volgende client-apps:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Micro soft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Facturering van Microsoft
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype voor Bedrijven
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Micro soft Yammer

**Opmerkingen**

- De goedgekeurde client-apps ondersteunen de functie intune-Mobile Application Management.
- De vereiste **goedgekeurde client-app vereisen** :
   - Biedt alleen ondersteuning voor de voor waarde iOS en Android voor [device platform](#device-platform-condition).

## <a name="app-protection-policy-requirement"></a>Vereiste voor app-beveiligings beleid 

In het beleid voor voorwaardelijke toegang kunt u vereisen dat er een app-beveiligings beleid aanwezig is op de client-app voordat toegang beschikbaar is voor de geselecteerde Cloud-apps. 

![Toegang beheren met beleid voor app-beveiliging](./media/technical-reference/22.png)

Deze instelling is van toepassing op de volgende client-apps:

- Micro soft Cortana
- Microsoft Edge
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Opmerkingen**

- Apps voor het app-beveiligings beleid ondersteunen de functie intune-Mobile Application Management met beleids beveiliging.
- De vereisten voor het **beveiligings beleid voor apps vereisen** :
    - Biedt alleen ondersteuning voor de voor waarde iOS en Android voor [device platform](#device-platform-condition).

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is voorwaardelijke toegang in azure Active Directory?](../active-directory-conditional-access-azure-portal.md) voor een overzicht van voorwaardelijke toegang.
- Als u klaar bent voor het configureren van beleid voor voorwaardelijke toegang in uw omgeving, raadpleegt u de [Aanbevolen procedures voor voorwaardelijke toegang in azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
