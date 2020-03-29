---
title: 'Azure AD Connect: instanties voor synchronisatieservice | Microsoft Documenten'
description: Op deze pagina worden speciale overwegingen voor Azure AD-exemplaren weergegeven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298820"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: speciale overwegingen voor instanties
Azure AD Connect wordt het meest gebruikt met het wereldwijde exemplaar van Azure AD en Office 365. Maar er zijn ook andere instanties en deze hebben verschillende eisen voor URL's en andere speciale overwegingen.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Duitsland
De [Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) is een soevereine cloud die wordt beheerd door een Duitse datatrustee.

| URL's die moeten worden geopend in proxyserver |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Certificaatintrekkingslijsten |

Wanneer u zich aanmeldt bij uw Azure AD-tenant, moet u een account gebruiken in het onmicrosoft.de-domein.

Functies die momenteel niet aanwezig zijn in de Microsoft Cloud Germany:

* **Wachtwoordterugschrijven** is beschikbaar voor preview met Azure AD Connect-versie 1.1.570.0 en daarna.
* Andere Azure AD Premium-services zijn niet beschikbaar.

## <a name="microsoft-azure-government"></a>Microsoft Azure-overheid
De [Microsoft Azure Government cloud](https://azure.microsoft.com/features/gov/) is een cloud voor de Amerikaanse overheid.

Deze cloud wordt ondersteund door eerdere releases van DirSync. Vanaf build 1.1.180 van Azure AD Connect wordt de volgende generatie van de cloud ondersteund. Deze generatie gebruikt alleen op de VS gebaseerde eindpunten en heeft een andere lijst met URL's om te openen in uw proxyserver.

| URL's die moeten worden geopend in proxyserver |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (vereist voor automatische azure-tenantdetectie) |
| \*.gov.us.microsoftonline.com |
| +Certificaatintrekkingslijsten |

> [!NOTE]
> Vanaf Azure AD Connect versie 1.1.647.0 is het instellen van de AzureInstance-waarde in het register niet langer vereist, mits *.windows.net is geopend op uw proxyserver(s). Voor klanten die echter geen internetverbinding toestaan vanaf hun Azure AD Connect-server(s), kan de volgende handmatige configuratie worden gebruikt.

### <a name="manual-configuration"></a>Handmatige configuratie

De volgende handmatige configuratiestappen worden gebruikt om ervoor te zorgen dat Azure AD Connect azure-synchronisatieeindpunten gebruikt.

1. Start de installatie van Azure AD Connect.
2. Wanneer u de eerste pagina ziet waar u de EULA moet accepteren, gaat u niet verder, maar laat u de installatiewizard draaien.
3. Begin opnieuw gegedit en `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` wijzig `4`de registersleutel in de waarde.
4. Ga terug naar de wizard Installatie van Azure AD Connect, accepteer de EULA en ga verder. Zorg er tijdens de installatie voor dat u het **aangepaste configuratie-installatiepad** (en niet express-installatie) gebruikt en vervolgens de installatie zoals gewoonlijk voortzet.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
