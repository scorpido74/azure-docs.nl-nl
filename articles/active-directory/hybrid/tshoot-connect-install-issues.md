---
title: Problemen met Azure AD Connect installatie oplossen | Microsoft Docs '
description: Dit onderwerp bevat stappen voor het oplossen van problemen met het installeren van Azure AD Connect.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "70211838"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Problemen oplossen: problemen met Azure AD Connect installeren

## <a name="recommended-steps"></a>**Aanbevolen stappen**
Controleer welk [Azure AD Connect installatie type](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) geschikt is voor u. Als u voldoet aan de criteria van snelle installatie, raden we u ten zeerste aan om met de snelle installatie te gaan. De snelle installatie biedt minimale opties die nodig zijn om de installatie te volt ooien, waardoor er minder kans is op problemen. 

Als u echter niet voldoet aan de criteria voor de snelle installatie en u de aangepaste installatie moet uitvoeren, zijn hier enkele aanbevolen procedures die u kunt volgen om veelvoorkomende problemen te voor komen. In het geval van eenvoud worden hier alleen selectieve opties vermeld:

* Zorg ervoor dat u een beheerder bent van de computer waarop u AAD Connect installeert. Meld u aan bij de computer met dezelfde beheerders referenties.

* Laat alle opties standaard op de volgende pagina staan, met uitzonde ring van ' een bestaande SQL Server gebruiken ', als u bestaande SQL Server wilt gebruiken. Hier vindt u [meer informatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) over het gebruik van aangepaste installatie opties. 

    ![Bestaande SQL Server gebruiken](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Kies op de volgende pagina de optie nieuwe AD-account maken om te voor komen dat er machtigings problemen met een bestaand account worden gemaakt.

    ![AD-forest-account](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Veelvoorkomende problemen**

* [Connectiviteits problemen met on-premises Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Connectiviteits problemen met online Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Machtigings problemen met on-premises Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Vereisten voor Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Het installatietype voor Azure AD Connect selecteren](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Aan de slag met Azure AD Connect met expresinstellingen](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Aangepaste installatie van Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: upgraden van een vorige naar de meest recente versie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: wat is een staging-server?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Wat is de PowerShell-module ADConnectivityTool?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md).
- [Wat is hybride identiteit?](whatis-hybrid-identity.md)





