---
title: Problemen met installatieproblemen met Azure AD Connect oplossen | Microsoft Docs'
description: In dit onderwerp vindt u stappen voor het oplossen van problemen met het installeren van Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70211838"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Problemen met problemen met de installatie problemen met Azure AD Connect oplossen

## <a name="recommended-steps"></a>**Aanbevolen stappen**
Controleer welk [Azure AD Connect-installatietype](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) geschikt is voor u. Als u voldoet aan de criteria van express installatie, dan raden wij u ten zeerste aan om te gaan met de express installatie. De express installatie geeft u minimale opties die nodig zijn om de installatie te voltooien, daarom is er minder kans op problemen. 

Als u echter niet voldoet aan de uitdrukkelijke installatiecriteria en de aangepaste installatie moet doen, zijn hier enkele aanbevolen procedures die u volgen om veelvoorkomende problemen te voorkomen. Omwille van de eenvoud worden hier alleen selectieve opties genoemd:

* Zorg ervoor dat u een beheerder bent op de machine waarop u AAD Connect installeert. Meld u aan bij de machine met dezelfde beheerdersreferenties.

* Laat alle opties standaard op de volgende pagina staan, behalve 'Gebruik een bestaande SQL Server', als u bestaande SQL Server wilt gebruiken. Hier vindt [u meer informatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) over het gebruik van aangepaste installatieopties. 

    ![Bestaande SQL Server gebruiken](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Kies op de volgende pagina de optie 'Nieuw AD-account maken' om eventuele problemen met de toestemming met het bestaande account te voorkomen.

    ![AD-forestaccount](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Algemene problemen**

* [Verbindingsproblemen met on-premises Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Verbindingsproblemen met online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Machtigingsproblemen met on-premises Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Vereisten voor Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Het installatietype voor Azure AD Connect selecteren](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Aan de slag met Azure AD Connect met expresinstellingen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Aangepaste installatie van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: upgraden van een vorige naar de meest recente versie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: wat is staging server?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Wat is de PowerShell-module ADConnectivityTool?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Volgende stappen
- [Synchronisatie van Azure AD Connect](how-to-connect-sync-whatis.md).
- [Wat is hybride identiteit?](whatis-hybrid-identity.md)





