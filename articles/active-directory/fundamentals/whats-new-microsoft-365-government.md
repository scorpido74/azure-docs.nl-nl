---
title: Wat is er nieuw voor Azure AD in Microsoft 365-overheid? | Microsoft Docs
description: Meer informatie over enkele wijzigingen in Azure Active Directory (Azure AD) in het cloudexemplaar van Microsoft 365 Government, die van invloed kunnen zijn op u.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48a7e5e810e509a97b6010218532f6e7dff0b73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425541"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>Nieuwe nieuwe auto's voor Azure Active Directory in Microsoft 365-overheid

We hebben een aantal wijzigingen aangebracht in Azure Active Directory (Azure AD) in het cloudexemplaar van Microsoft 365 Government, dat van toepassing is op klanten die de volgende services gebruiken:

- Microsoft Azure-overheid

- Microsoft 365 Overheid – GCC Hoog

- Microsoft 365 Overheid – DoD

Dit artikel is niet van toepassing op Microsoft 365 Government - GCC-klanten.

## <a name="changes-to-the-initial-domain-name"></a>Wijzigingen in de oorspronkelijke domeinnaam

Tijdens de eerste aanmelding van uw organisatie voor een online service van microsoft 365 `<your-domain-name>.onmicrosoft.com`Government werd u gevraagd om de domeinnaam van uw organisatie te kiezen. Als u al een domeinnaam met het achtervoegsel .com hebt, verandert er niets.

Als u zich echter aanmeldt voor een nieuwe Microsoft 365-overheidsdienst, wordt u gevraagd `.us` een domeinnaam te kiezen met behulp van het achtervoegsel. Dus, het `<your-domain-name>.onmicrosoft.us`zal.

>[!Note]
>Deze wijziging is niet van toepassing op klanten die worden beheerd door cloudserviceproviders (CSP's).

## <a name="changes-to-portal-access"></a>Wijzigingen in portaltoegang

We hebben de portaleindpunten voor Microsoft Azure Government, Microsoft 365 Government – GCC High en Microsoft 365 Government – DoD bijgewerkt, zoals weergegeven in de [Endpoint-toewijzingstabel.](#endpoint-mapping)

Voorheen konden klanten zich aanmelden via de wereldwijde Azure -portals (portal.azure.com) en Office 365 (portal.office.com). Met deze update moeten klanten zich nu aanmelden met de specifieke Microsoft Azure Government, Microsoft 365 Government - GCC High en Microsoft 365 Government - DoD-portals.

## <a name="endpoint-mapping"></a>Eindpunttoewijzing

In de volgende tabel worden de eindpunten voor alle klanten weergegeven:

| Name | Eindpuntdetails |
|------|------------------|
| Portals |Microsoft Azure-overheid:https://portal.azure.us<p>Microsoft 365 Overheid – GCC Hoog:https://portal.office365.us<p>Microsoft 365 Overheid – DoD:https://portal.apps.mil |
| Eindpunt azure Active Directory Authority | https://login.microsoftonline.us |
| Microsoft Graph API voor Microsoft 365 Government - GCC High | https://graph.microsoft.us |
| Microsoft Graph API voor Microsoft 365 Government - DoD | https://dod-graph.microsoft.us |
| Eindpunten azure-overheidsdiensten | Zie Azure [Government-ontwikkelaarshandleiding](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide) voor meer informatie |
| Microsoft 365-overheid - Hoge eindpunten van GCC | Zie [GCC High Endpoints van de Amerikaanse overheid](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) voor meer informatie |
| Microsoft 365-regering - DoD | Zie [Dod-eindpunten van de Amerikaanse overheid](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) voor meer informatie |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen:

- [Wat is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)

- [Azure Government AAD Authority Endpoint Update](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Microsoft Graph-eindpunten in de cloud van de Amerikaanse overheid](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 GCC High en DoD van de Amerikaanse overheid](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)
