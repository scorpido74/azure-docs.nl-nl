---
title: Afhankelijke afhankelijkheden van de Voorwaardelijke toegang-service - Azure Active Directory
description: Meer informatie over hoe voorwaarden worden gebruikt in voorwaardelijke toegang tot Azure Active Directory om een beleid te activeren.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39238575c05d35a2d87999e08c49c0c77e99bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74380017"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Wat zijn serviceafhankelijkheden in Voorwaardelijke toegang van Azure Active Directory? 

Met beleid voor voorwaardelijke toegang u toegangsvereisten voor websites en services opgeven. Uw toegangsvereisten kunnen bijvoorbeeld bestaan uit het vereisen van multi-factor authenticatie (MFA) of [beheerde apparaten.](require-managed-devices.md) 

Wanneer u rechtstreeks toegang krijgt tot een site of service, is de impact van een gerelateerd beleid doorgaans eenvoudig te beoordelen. Als u bijvoorbeeld een beleid hebt waarvoor MFA voor SharePoint Online is geconfigureerd, wordt MFA afgedwongen voor elke aanmelding bij de SharePoint-webportal. Het is echter niet altijd eenvoudig om de impact van een beleid te beoordelen, omdat er cloud-apps zijn met afhankelijkheden ten opzichte van andere cloud-apps. Microsoft Teams kan bijvoorbeeld toegang bieden tot bronnen in SharePoint Online. Wanneer u dus microsoft teams in ons huidige scenario bevindt, bent u ook onderworpen aan het SharePoint MFA-beleid.   

## <a name="policy-enforcement"></a>Beleidsafdwinging 

Als u een serviceafhankelijkheid hebt geconfigureerd, kan het beleid worden toegepast met behulp van vroegtijdige of late handhaving. 

- **Vroegtijdige handhaving van het beleid** betekent dat een gebruiker moet voldoen aan het beleid van de afhankelijke service voordat hij toegang krijgt tot de bel-app. Een gebruiker moet bijvoorbeeld voldoen aan het SharePoint-beleid voordat hij zich aanmeldt bij MS Teams. 
- **Late beleidshandhaving** vindt plaats nadat de gebruiker zich heeft aangegeven bij de oproep-app. Handhaving wordt uitgesteld tot bij het aanroepen van app-verzoeken, een token voor de downstream-service. Voorbeelden hiervan zijn MS Teams die toegang hebben tot Planner en Office.com toegang tot SharePoint. 

Het onderstaande diagram illustreert ms teams service afhankelijkheden. Vaste pijlen geven vroege handhaving aan de gestippelde pijl voor Planner geeft te late handhaving aan. 

![MS Teams-serviceafhankelijkheden](./media/service-dependencies/01.png)

Als aanbevolen praktijk moet u waar mogelijk gemeenschappelijke beleidsregels instellen voor gerelateerde apps en services. Het hebben van een consistente beveiligingshouding biedt u de beste gebruikerservaring. Als u bijvoorbeeld een gemeenschappelijk beleid in Exchange Online, SharePoint Online, Microsoft Teams en Skype voor Bedrijven instelt, worden onverwachte aanwijzingen die kunnen voortvloeien uit verschillende beleidsregels die op downstreamservices worden toegepast, aanzienlijk verminderd. 

In de onderstaande tabel worden aanvullende serviceafhankelijkheden weergegeven, waar de client-apps aan moeten voldoen  

| Client-apps         | Downstream-service                          | Handhaving |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal en API) | Vroeg gebonden |
| Microsoft Classroom | Exchange                                    | Vroeg gebonden |
|                     | SharePoint                                  | Vroeg gebonden |
| Microsoft Teams     | Exchange                                    | Vroeg gebonden |
|                     | MS Planner                                  | Laatgebonden  |
|                     | SharePoint                                  | Vroeg gebonden |
|                     | Skype voor Bedrijven Online                   | Vroeg gebonden |
| Office-portal       | Exchange                                    | Laatgebonden  |
|                     | SharePoint                                  | Laatgebonden  |
| Outlook-groepen      | Exchange                                    | Vroeg gebonden |
|                     | SharePoint                                  | Vroeg gebonden |
| PowerApps           | Microsoft Azure Management (portal en API) | Vroeg gebonden |
|                     | Windows Azure Active Directory              | Vroeg gebonden |
| Project             | Dynamics CRM                                | Vroeg gebonden |
| Skype voor Bedrijven  | Exchange                                    | Vroeg gebonden |
| Visual Studio       | Microsoft Azure Management (portal en API) | Vroeg gebonden |
| Microsoft Forms     | Exchange                                    | Vroeg gebonden |
|                     | SharePoint                                  | Vroeg gebonden |
| Microsoft To-Do     | Exchange                                    | Vroeg gebonden |

## <a name="next-steps"></a>Volgende stappen

Zie [Uw voorwaardelijke toegangsimplementatie](plan-conditional-access.md)plannen in Azure Active Directory voor meer informatie over het implementeren van voorwaardelijke toegang in uw omgeving.
