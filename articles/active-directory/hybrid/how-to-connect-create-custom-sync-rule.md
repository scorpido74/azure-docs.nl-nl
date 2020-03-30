---
title: Een synchronisatieregel aanpassen in Azure AD Connect | Microsoft Docs'
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
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60351041"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Een synchronisatieregel aanpassen

## <a name="recommended-steps"></a>**Aanbevolen stappen**

U de synchronisatieregeleditor gebruiken om een nieuwe synchronisatieregel te bewerken of te maken. U moet een gevorderde gebruiker zijn om wijzigingen aan te brengen in synchronisatieregels. Eventuele verkeerde wijzigingen kunnen leiden tot verwijdering van objecten uit uw doelmap. Lees [Aanbevolen documenten](#recommended-documents) om expertise op te doen op het gebied van synchronisatieregels. Als u een synchronisatieregel wilt wijzigen, gaat u door de volgende stappen:

* Start de synchronisatie-editor vanuit het toepassingsmenu op het bureaublad zoals hieronder wordt weergegeven:

    ![Editormenu synchronisatieregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Als u een standaardsynchronisatieregel wilt aanpassen, kloont u de bestaande regel door op de knop Bewerken te klikken op de editor synchronisatieregels, waarmee u een kopie van de standaardstandaardregel maakt en deze uitschakelt. Sla de gekloonde regel op met een voorrangsprioriteit van minder dan 100.  Voorrang bepaalt welke regel wint (lagere numerieke waarde) een conflictoplossing als er een kenmerkstroomconflict is.

    ![Synchronisatieregeleditor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Bij het wijzigen van een specifiek kenmerk moet u idealiter alleen het wijzigende kenmerk in de gekloonde regel houden.  Schakel vervolgens de standaardregel in, zodat gewijzigd kenmerk afkomstig is van de gekloonde regel en andere kenmerken worden gekozen uit de standaardregel. 

* Houd er rekening mee dat in het geval dat in het geval dat de berekende waarde van het gewijzigde kenmerk NULL is in uw gekloonde regel en dan niet NULL is in de standaardregel, de niet NULL-waarde wint en de NULL-waarde vervangt. Als u niet wilt dat een NULL-waarde wordt vervangen door een niet NULL-waarde, wijst u AuthoritativeNull toe in uw gekloonde regel.

* Als u een **uitgaande** regel wilt wijzigen, wijzigt u het filter uit de synchronisatieregeleditor.

## <a name="recommended-documents"></a>**Aanbevolen documenten**
* [Azure AD Connect-synchronisatie: technische concepten](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect-synchronisatie: de architectuur begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect-synchronisatie: declaratieve inrichting begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect-synchronisatie: declaratieve inrichtingsexpressies begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-synchronisatie: inzicht in de standaardconfiguratie](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronisatie van Azure AD Connect: gebruikers, groepen en contactpersonen begrijpen](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect-synchronisatie: schaduwkenmerken](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Volgende stappen
- [Synchronisatie van Azure AD Connect](how-to-connect-sync-whatis.md).
- [Wat is hybride identiteit?](whatis-hybrid-identity.md)
